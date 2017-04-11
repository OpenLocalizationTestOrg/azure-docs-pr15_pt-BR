<properties
 pageTitle="Executar ESTRELA-CCM + com HPC Pack no Linux VMs | Microsoft Azure"
 description="Implantar um cluster Microsoft HPC Pack no Azure e executar uma ESTRELA-CCM + trabalho no Linux vários nós de computadores em uma rede RDMA."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="xpillons"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/13/2016"
 ms.author="xpillons"/>

# <a name="run-star-ccm-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Executar ESTRELA-CCM + com Microsoft HPC Pack em um RDMA Linux cluster no Azure
Este artigo mostra como implantar um cluster Microsoft HPC Pack no Azure e execute uma [ESTRELA de CD-adapco-CCM +](http://www.cd-adapco.com/products/star-ccm%C2%AE) trabalho em vários nós de computação Linux interconectados com InfiniBand.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC Pack fornece recursos para executar uma variedade de grande escala HPC e aplicativos em paralelo, inclusive aplicativos MPI, em clusters de máquinas virtuais do Microsoft Azure. HPC Pack também suporta aplicativos Linux HPC em execução no Linux nó de computação VMs que são implantadas em um cluster de HPC Pack. Para uma introdução ao uso Linux calcular nós com HPC Pack, consulte [Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="set-up-an-hpc-pack-cluster"></a>Configurar um cluster de HPC Pack
Baixe os scripts de implantação de HPC Pack IaaS no [Centro de Download](https://www.microsoft.com/en-us/download/details.aspx?id=44949) e extraia-las localmente.

PowerShell Azure é um pré-requisito. Se PowerShell não estiver configurado no seu computador local, leia o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

No momento da redação deste artigo, as imagens de Linux do Azure Marketplace (que contém os drivers de InfiniBand Azure) são para SLES 12, CentOS 6.5 e CentOS 7.1. Este artigo baseia-se no uso do SLES 12. Para recuperar o nome de todas as imagens de Linux que suportam HPC no mercado, você pode executar o seguinte comando do PowerShell:

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

A saída lista o local em que essas imagens estão disponíveis e o nome da imagem (**ImageName**) a serem usados no modelo de implantação posteriormente.

Antes de implantar o cluster, você precisa criar um arquivo de modelo de implantação de HPC Pack. Porque estamos visando um pequeno cluster, o nó principal será o controlador de domínio e hospedar um banco de dados local do SQL.

O modelo a seguir serão implantar tal um nó principal, crie um arquivo XML chamado **MyCluster.xml**e substituir os valores da **SubscriptionId**, **StorageAccount**, **local**, **VMName**e **ServiceName** com as suas.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

Inicie a criação de cabeça nós executando o comando do PowerShell no prompt de comando:

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

Depois de 20 a 30 minutos, o nó principal deve estar pronto. Você pode se conectar a ele a partir do portal Azure clicando no ícone de **conexão** da máquina virtual.

Eventualmente, você talvez precise corrigir os encaminhadores DNS. Para fazer isso, inicie o Gerenciador de DNS.

1.  Clique com botão direito no nome do servidor no Gerenciador DNS, selecione **Propriedades**e, em seguida, clique na guia **encaminhadores** .

2.  Clique no botão **Editar** para remover qualquer encaminhadores e clique em **Okey**.

3.  Verifique se a caixa de seleção **usar dicas de raiz se não há encaminhadores disponíveis** está marcada e clique em **Okey**.

## <a name="set-up-linux-compute-nodes"></a>Configurar nós de computação Linux
Você pode implantar os nós de computação Linux usando o mesmo modelo de implantação que você usou para criar o nó principal.

Copie o arquivo **MyCluster.xml** do computador local para o nó principal e atualizar a marca de **NodeCount** com o número de nós que você deseja implantar (< = 20). Tenha cuidado para ter suficiente cores disponíveis em sua cota de Azure, porque cada instância A9 irá consumir 16 cores em sua assinatura. Você pode usar instâncias de A8 (8 cores) em vez de A9 se você quiser usar mais VMs no mesmo orçamento.

No nó principal, copie os scripts de implantação de HPC Pack IaaS.

Execute os seguintes comandos do PowerShell do Azure em um prompt de comando:

1.  Execute **Add-AzureAccount** para se conectar à sua assinatura do Azure.

2.  Se você tiver várias assinaturas, execute **Get-AzureSubscription** para listá-los.

3.  Definir uma assinatura padrão, executando o **Selecione AzureSubscription - nome de inscrição xxxx-padrão** comando.

4.  Executar **.\New-HPCIaaSCluster.ps1 - ConfigFile MyCluster.xml** para iniciar a implantação Linux nós de computadores.

    ![Implantação de cabeça-node em ação][hndeploy]

Abra a ferramenta de HPC Pack Cluster Manager. Após alguns minutos, nós de computação Linux regularmente aparecerão na lista de nós de computação de cluster. Com o modo clássico de implantação, IaaS VMs são criadas sequencialmente. Portanto se o número de nós for importante, obtendo-los todos implantado pode levar a um período de tempo significativo.

![Nós Linux no Gerenciador de Cluster HPC Pack][clustermanager]

Agora que todos os nós estão funcionando no cluster, existem configurações de infraestrutura adicional para fazer.

## <a name="set-up-an-azure-file-share-for-windows-and-linux-nodes"></a>Configurar um compartilhamento de arquivo do Azure para Windows e Linux nós
Você pode usar o serviço de arquivo do Azure para armazenar scripts, pacotes de aplicativos e arquivos de dados. Arquivo Azure fornece recursos CIFS na parte superior de armazenamento de Blob do Azure como um armazenamento persistente. Lembre-se de que essa não é a solução mais flexível, mas ele é o mais simples e não requer VMs dedicadas.

Crie um compartilhamento de arquivo do Azure seguindo as instruções no artigo [Introdução ao armazenamento de arquivos do Azure no Windows](..\storage\storage-dotnet-how-to-use-files.md).

Manter o nome da sua conta de armazenamento como **saname**, o nome de compartilhamento de arquivo como **compartilhamento**e a chave da conta de armazenamento como **sakey**.

### <a name="mount-the-azure-file-share-on-the-head-node"></a>Montar o compartilhamento de arquivos do Azure no nó principal
Abra um prompt de comando e execute o seguinte comando para armazenar as credenciais no cofre de máquina local:

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

Em seguida, para montar o compartilhamento de arquivos do Azure, execute:

```
    net use Z: \\<saname>.file.core.windows.net\<sharename> /persistent:yes
```

### <a name="mount-the-azure-file-share-on-linux-compute-nodes"></a>Montar o compartilhamento de arquivos do Azure em nós de computação Linux
Uma ferramenta útil que acompanha HPC Pack é a ferramenta clusrun. Você pode usar esta ferramenta de linha de comando para executar o mesmo comando simultaneamente em um conjunto de nós de computação. Em nosso caso, ele é usado para montar o compartilhamento de arquivos do Azure e persistir sobreviver reinicialização.
Em um prompt de comando elevado no nó principal, execute os seguintes comandos.

Para criar o diretório de montagem:

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Montar o compartilhamento de arquivos do Azure:

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

Para manter o compartilhamento de montagem:

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## <a name="install-star-ccm"></a>Instalar ESTRELA-CCM +
Instâncias de máquina virtual Azure A8 e A9 oferecem suporte de InfiniBand e recursos RDMA. Os drivers de núcleo que permitem que esses recursos estão disponíveis para o Windows Server 2012 R2, SUSE 12, CentOS 6.5 e imagens de CentOS 7.1 do Azure Marketplace. Microsoft MPI e MPI Intel (versão 5. x) são as duas bibliotecas MPI que oferecem suporte a esses drivers no Azure.

ESTRELA de CD-adapco-CCM + versão 11. x e posterior é fornecida com versão de MPI Intel 5. x, portanto InfiniBand suporte para Azure está incluído.

Obtenha o Linux64 ESTRELA-CCM + pacote a partir do [portal de CD-adapco](https://steve.cd-adapco.com). Em nosso caso, usamos versão 11.02.010 precisão misto.

No nó principal, no compartilhamento de arquivo do Azure **/hpcdata** , crie um script de shell chamado **setupstarccm.sh** com o seguinte conteúdo. Esse script será executado em cada nó de computação configurar ESTRELA-CCM + localmente.

#### <a name="sample-setupstarcmsh-script"></a>Exemplo de script de setupstarcm.sh
```
    #!/bin/bash
    # setupstarcm.sh to set up STAR-CCM+ locally

    # Create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # Copy the STAR-CCM package from the file share to the local directory
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # Extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # Start a silent installation of STAR-CCM without the FLEXlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # Update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Agora, configurar ESTRELA-CCM + em todos os seu Linux nós de computadores, abra um prompt de comando e execute o seguinte comando:

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

Enquanto o comando é executado, você pode monitorar o uso da CPU usando o mapa de calor do Gerenciador de Cluster. Após alguns minutos, todos os nós devem ser configurados corretamente.

## <a name="run-star-ccm-jobs"></a>Executar ESTRELA-CCM + trabalhos
HPC Pack é usado para seus recursos do Agendador de trabalho para executar ESTRELA-CCM + trabalhos. Para fazer isso, precisamos o suporte de alguns scripts que são usados para iniciar o trabalho e executar ESTRELA-CCM +. Os dados de entrada são mantidos no compartilhamento de arquivos do Azure primeiro para simplificar.

O seguinte script do PowerShell é usado para fila uma ESTRELA-CCM + trabalho. Ele utiliza três argumentos:

*  O nome do modelo

*  O número de nós a ser usado

*  O número de cores em cada nó a ser usado

Porque ESTRELA-CCM + pode preencher a largura de banda de memória, é geralmente melhor usar cores menos por nós de computação e adicionar novos nós. O número exato de cores por nó dependerá da família de processador e a velocidade de interconexão.

Os nós estão alocados exclusivamente para o trabalho e não podem ser compartilhados com outros trabalhos. O trabalho não é iniciado como um trabalho MPI diretamente. O script do shell **runstarccm.sh** iniciará o inicializador de MPI.

Modelo de entrada e o script **runstarccm.sh** são armazenados no compartilhamento **/hpcdata** que foi montado anteriormente.

Arquivos de log são nomeados com a ID de trabalho e são armazenados em **/hpcdata compartilhar**, juntamente com a ESTRELA-CCM + arquivos de saída.


#### <a name="sample-submitstarccmjobps1-script"></a>Exemplo de script de SubmitStarccmJob.ps1
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # Create a new job; this will give us the job ID that's used to identify the name of the uploaded package in Azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # Submit the job    
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Substitua **runner.java** por sua ESTRELA preferencial-iniciador de modelo CCM + Java e o código de log.

#### <a name="sample-runstarccmsh-script"></a>Exemplo de script de runstarccm.sh
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set the mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
        let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run STAR-CCM with the hostfile argument
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
        -power -podkey "<yourkey>" -rsh ssh \
        -mpi intel -fabric UDAPL -cpubind bandwidth,v \
        -mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
        -batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

Em nosso teste, usamos um token de licença do Power sob demanda. Para esse token, você precisa definir a variável de ambiente **$CDLMD_LICENSE_FILE** para **1999@flex.cd-adapco.com** e a chave na opção **- podkey** da linha de comando.

Após alguns inicialização, o script extrai – das **$CCP_NODES_CORES** variáveis de ambiente HPC Pack defina - a lista de nós para criar um arquivo de host que usa o inicializador de MPI. Este arquivo de host conterá a lista de nomes de nó de computação que são usados para o trabalho, um nome por linha.

O formato de **$CCP_NODES_CORES** segue este padrão:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

Onde:

* `<Number of nodes>`é o número de nós alocados para este trabalho.

* `<Name of node_n_...>`é o nome de cada nó alocado para este trabalho.

* `<Cores of node_n_...>`é o número de cores no nó alocados para este trabalho.

O número de cores (**$NBCORES**) também é calculado com base no número de nós (**$NBNODES**) e o número de cores por nó (fornecido como parâmetro **$NBCORESPERNODE**).

Para as opções de MPI, aqueles que são usados com Intel MPI no Azure são:

*   `-mpi intel`para especificar Intel MPI.

*   `-fabric UDAPL`Para usar verbos InfiniBand do Azure.

*   `-cpubind bandwidth,v`para otimizar a largura de banda para MPI com ESTRELA-CCM +.

*   `-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0"`Para fazer com que o Intel MPI funcionar com o Azure InfiniBand e definir o número necessário de cores por nó.

*   `-batch`Para começar a ESTRELA-CCM + no modo em lotes com nenhuma interface do usuário.


Finalmente, para iniciar um trabalho, certifique-se de que os nós estão funcionando e estão online no Gerenciador de Cluster. Em um prompt de comando do PowerShell, execute a isto:

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## <a name="stop-nodes"></a>Parar de nós
No futuro, depois que terminar com os testes, você pode usar os seguintes comandos de HPC Pack PowerShell parar e iniciar nós:

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## <a name="next-steps"></a>Próximas etapas
Tente executar outras cargas de trabalho de Linux. Por exemplo, consulte:

* [Executar NAMD com Microsoft HPC Pack em nós de computação Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Executar OpenFOAM com o Microsoft HPC Pack em um cluster de Linux RDMA no Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)


<!--Image references-->
[hndeploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/hndeploy.png
[clustermanager]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/ClusterManager.png
