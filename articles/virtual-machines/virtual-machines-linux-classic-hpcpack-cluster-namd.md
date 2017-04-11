<properties
 pageTitle="NAMD com Microsoft HPC Pack em Linux VMs | Microsoft Azure"
 description="Implantar um cluster Microsoft HPC Pack no Azure e executar uma simulação de NAMD com charmrun em vários nós de computação Linux"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="10/13/2016"
 ms.author="danlep"/>

# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Executar NAMD com Microsoft HPC Pack em nós de computação Linux no Azure

Este artigo mostra a você uma maneira de executar uma carga de trabalho de computação de alto desempenho (HPC) Linux no Azure máquinas virtuais. Aqui, você configurar um cluster de [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) no Azure conosco de computação Linux e executar uma simulação de [NAMD](http://www.ks.uiuc.edu/Research/namd/) para calcular e visualizar a estrutura de um sistema de biomolecular grandes.  

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD** (para o programa do Dynamics moleculares Nanoscale) é um pacote de paralelas dynamics moleculares projetado para simulação de alto desempenho de sistemas de biomolecular grandes contendo milhões de átomos. Vírus, estruturas de célula e grandes proteins são exemplos desses sistemas. NAMD escalas para centenas de cores para simulações típicas e mais de 500.000 cores para as simulações maior.

* **Microsoft HPC Pack** fornece recursos para executar o HPC grande escala e aplicativos paralelos em clusters de computadores locais ou Azure máquinas virtuais. Originalmente desenvolvido como uma solução para cargas de trabalho do Windows HPC, HPC Pack agora suporta executando Linux HPC aplicativos no Linux calcular VMs nó implantadas em um cluster de HPC Pack. Para obter uma introdução, consulte [Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md) .

Para obter outras opções executar Linux HPC cargas de trabalho no Azure, consulte [recursos técnicos para o lote e de alto desempenho computação](../batch/batch-hpc-solutions.md).




## <a name="prerequisites"></a>Pré-requisitos

* **Nós de computadores de cluster HPC Pack com Linux** - implantar um cluster de HPC Pack conosco de computação Linux no Azure usando um [modelo do Gerenciador de recursos do Azure](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) ou um [script do PowerShell do Azure](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Consulte [Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md) para os pré-requisitos e as etapas para qualquer opção. Se você escolher a opção de implantação de script do PowerShell, consulte o arquivo de configuração de amostra nos arquivos de exemplo no final deste artigo. Este arquivo configura um cluster de com base no Azure HPC Pack consiste de um nó principal do Windows Server 2012 R2 e quatro nós de computação de grandes 6.6 CentOS de tamanho. Personalize este arquivo conforme necessário para seu ambiente.


* **Arquivos de software e tutorial NAMD** - software de NAMD de Download para Linux do site do [NAMD](http://www.ks.uiuc.edu/Research/namd/) (registro necessário). Este artigo se baseia NAMD versão 2.10 e usa o arquivamento [Linux-x86_64 (64 bits Intel/AMD com Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310) . Também Baixe os [arquivos do tutorial NAMD](http://www.ks.uiuc.edu/Training/Tutorials/#namd). Os downloads são arquivos. tar, e você precisa de uma ferramenta do Windows para extrair os arquivos no nó principal cluster. Para extrair os arquivos, siga as instruções neste artigo. 

* **VMD** (opcional) - para ver os resultados do seu trabalho NAMD, baixe e instale o programa de visualização moleculares [VMD](http://www.ks.uiuc.edu/Research/vmd/) em um computador de sua escolha. A versão atual é 1.9.2. Consulte o VMD baixar o site para começar.  


## <a name="set-up-mutual-trust-between-compute-nodes"></a>Configurar a confiança comum entre nós de computação
Executar um trabalho entre os nós em vários nós Linux requer os nós confiar em si (por **rsh** ou **ssh**). Quando você cria o cluster HPC Pack com o script de implantação do Microsoft HPC Pack IaaS, o script configura automaticamente permanente confiança comum para a conta de administrador que você especificar. Para usuários não-administrador que você cria no domínio do cluster, você precisa configurar temporária confiança comum entre os nós quando um trabalho é alocado a elas. Em seguida, destroy relação após concluir o trabalho. Para fazer isso para cada usuário, forneça um par de chaves RSA ao cluster que HPC Pack usa para estabelecer a relação de confiança. Siga as instruções.

### <a name="generate-an-rsa-key-pair"></a>Gerar um par de chaves RSA
É fácil gerar um par de chaves RSA, que contém uma chave pública e uma chave privada, executando o comando de **ssh keygen** Linux.

1.  Faça logon um computador Linux.

2.  Execute o seguinte comando:

    ```bash
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] Pressione **Enter** para usar as configurações padrão até que o comando seja concluído. Não digite uma senha aqui; Quando for solicitada uma senha, pressione **Enter**.

    ![Gerar um par de chaves RSA][keygen]

3.  Altere o diretório para o ~/.ssh. A chave particular é armazenada em id_rsa e a chave pública no id_rsa.pub.

    ![Chaves pública e privadas][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Adicionar o par de chaves ao cluster HPC Pack
1.  [Conectar-se pela área de trabalho remota](virtual-machines-windows-connect-logon.md) para o nó principal máquina virtual usando o domínio credenciais é fornecida quando você implantado cluster (por exemplo, hpc\clusteradmin). Gerenciar o cluster de nó principal.

2. Use os procedimentos padrão do Windows Server para criar uma conta de usuário de domínio no domínio do Active Directory do cluster. Por exemplo, use a ferramenta de usuário do Active Directory e computadores no nó principal. Os exemplos deste artigo presumem que você cria um usuário de domínio chamado hpcuser no domínio hpclab (hpclab\hpcuser).

3. Adicione o usuário de domínio ao cluster HPC Pack como um usuário de cluster. Para obter instruções, consulte [Adicionar ou remover usuários de cluster](https://technet.microsoft.com/library/ff919330.aspx).

2.  Crie um arquivo denominado C:\cred.xml e copie os dados da chave RSA nele. Você pode encontrar um exemplo os arquivos de exemplo no final deste artigo.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

3.  Abra um Prompt de comando e digite o seguinte comando para definir os dados de credenciais para a conta de hpclab\hpcuser. Use o parâmetro **extendeddata** para passar o nome do arquivo C:\cred.xml que você criou para os principais dados.

    ```command
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Este comando concluído com êxito sem saída. Depois de definir as credenciais para as contas de usuário que você precisa executar trabalhos, armazenar o arquivo cred.xml em um local seguro ou excluí-la.

5.  Se você gerou o par de chaves RSA em um dos seus nós Linux, lembre-se excluir as chaves após terminar de usá-los. HPC Pack não configurar confiança comum se encontrar um arquivo de id_rsa existente ou um arquivo de id_rsa.pub.

>[AZURE.IMPORTANT] Não recomendamos executar um trabalho de Linux como um administrador de cluster em um cluster compartilhado, como um trabalho enviado por um administrador é executado sob a conta raiz em nós Linux. Um trabalho enviado por um usuário não administrador executa em uma conta de usuário Linux local com o mesmo nome que o usuário de trabalho. Nesse caso, HPC Pack configura confiança comum para esse usuário Linux em todos os nós alocado para o trabalho. Você pode configurar o usuário Linux manualmente em nós Linux antes de executar o trabalho ou HPC Pack cria o usuário automaticamente quando o trabalho é enviado. Se o pacote de HPC cria o usuário, HPC Pack excluirá após concluir o trabalho. Para reduzir ameaças de segurança, as teclas são removidas após concluir o trabalho em nós.

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configurar um compartilhamento de arquivo para nós Linux

Agora, configurar um compartilhamento de arquivo SMB e montar a pasta compartilhada em todos os nós Linux para permitir que os nós Linux para acessar arquivos NAMD com um caminho comum. A seguir está as etapas para montar uma pasta compartilhada no nó principal. Um compartilhamento é recomendado para distribuições como CentOS 6.6 que atualmente não suportam o serviço de arquivo do Azure. Se seus nós Linux oferecer suporte a um compartilhamento de arquivo do Azure, veja [como usar o armazenamento de arquivos do Azure com Linux](../storage/storage-how-to-use-files-linux.md). Para opções de compartilhamento com HPC Pack de arquivo adicional, consulte [Introdução a nós de computação Linux em um Cluster de pacote HPC no Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

1.  Crie uma pasta no nó principal e compartilhá-la para todos, definindo privilégios de leitura/gravação. Neste exemplo, \\ \\CentOS66HN\Namd é o nome da pasta, onde CentOS66HN é o nome de host do nó principal.

2. Crie uma subpasta chamada namd2 na pasta compartilhada. No namd2, crie outra subpasta nomeada namdsample.

3. Extrai os arquivos NAMD na pasta usando uma versão do Windows do **tar** ou outro utilitário de Windows que operam em arquivos mortos tar. 
    * Extraia o arquivo de tar NAMD para \\ \\CentOS66HN\Namd\namd2.
    
    * Extrair os arquivos do tutorial em \\ \\CentOS66HN\Namd\namd2\namdsample.

4. Abra uma janela do Windows PowerShell e execute os seguintes comandos para montar a pasta compartilhada em nós Linux.

    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2

    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

O primeiro comando cria uma pasta chamada /namd2 em todos os nós no grupo LinuxNodes. O segundo comando montagens //CentOS66HN/Namd/namd2 a pasta compartilhada para a pasta com dir_mode e file_mode bits definidos como 777. O *nome de usuário* e a *senha* no comando devem ser as credenciais de um usuário no nó principal.

>[AZURE.NOTE]O "\`" símbolo no segundo comando é um símbolo de escape para PowerShell. "\`," significa "," (caractere de vírgula) é uma parte do comando.


## <a name="create-a-bash-script-to-run-a-namd-job"></a>Criar um script de Bash para executar um trabalho NAMD

Seu trabalho NAMD precisa de um arquivo de *nodelist* para **charmrun** determinar o número de nós para usar ao iniciar processos NAMD. Você usa um script de Bash que gera o arquivo de nodelist e executa **charmrun** com este arquivo nodelist. Em seguida, você pode enviar um trabalho NAMD no HPC Cluster Manager que chama esse script.

Usando um editor de texto de sua escolha, crie um script Bash na pasta /namd2 que contém os arquivos de programa NAMD e nomeie-hpccharmrun.sh. Para uma rápida prova de conceito, copie o script de hpccharmrun.sh de exemplo fornecido no final deste artigo e vá para [Enviar um trabalho NAMD](#submit-a-namd-job).

>[AZURE.TIP] Salvar seu script como um arquivo de texto com Linux finais (somente alimentação de linha, não CR LF) de linha. Isso garante que ele seja executado corretamente em nós Linux.

A seguir é detalhes sobre o que significa esse script bash. 

1.  Defina algumas variáveis.

    ```bash
    #!/bin/bash

    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    # Charmrun command
    CHARMRUN=${SCRIPT_PATH}/charmrun
    # Argument of ++nodelist
    NODELIST_OPT="++nodelist"
    # Argument of ++p
    NUMPROCESS="+p"
    ```

2.  Obtenha informações sobre o nó das variáveis de ambiente. $NODESCORES armazena uma lista de palavras de divisão de $CCP_NODES_CORES. $COUNT é o tamanho da $NODESCORES.
    ```
    # Get node information from the environment variables
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    ```    
    
    O formato para a variável CCP_NODES_CORES $ é da seguinte maneira:

    ```
    <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
    ```

    Essa variável lista o número total de nós, nomes de nós e número de cores em cada nó que estão alocados para o trabalho. Por exemplo, se o trabalho precisa 10 cores para executar, o valor de $CCP_NODES_CORES é semelhante a:

    ```
    3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
    ```
        
3.  Se a variável CCP_NODES_CORES $ não estiver definida, comece **charmrun** diretamente. (Isso só ocorrer quando você executa esse script diretamente no seu nós Linux.)

    ```
    if [ ${COUNT} -eq 0 ]
    then
        # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
        #echo ${CHARMRUN} $*
        ${CHARMRUN} $*
    ```

4.  Ou crie um arquivo de nodelist para **charmrun**.

    ```
    else
        # Create the nodelist file
        NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

        # Write the head line
        echo "group main" > ${NODELIST_PATH}

        # Get every node name and number of cores and write into the nodelist file
        I=1
        while [ ${I} -lt ${COUNT} ]
        do
            echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
            let "I=${I}+2"
        done
```
5.  Executar **charmrun** com o arquivo de nodelist, obter seu status de retorno e remova o arquivo nodelist no final.

    ${CCP_NUMCPUS} é outra variável de ambiente definida pelo nó principal HPC Pack. Ele armazena o número de cores totais alocados para este trabalho. Podemos usá-lo para especificar o número de processos para charmrun.

    ```
    # Run charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
    fi

    ```
6.  Saia com o status de retorno de **charmrun** .

    ```
    exit ${RTNSTS}
    ```



A seguir está as informações no arquivo nodelist, que gera o script:

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Por exemplo:

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>Enviar um trabalho NAMD

Agora você está pronto para enviar um trabalho NAMD no HPC Cluster Manager.

1.  Conectar ao seu nó principal do cluster e comece a HPC Cluster Manager.

2.  Em **Gerenciamento de recursos**, certifique-se de que os nós de computação Linux estão no estado **Online** . Se não estiverem, selecione-os e clique em **Colocar on-line**.

2.  Em **Gerenciamento de trabalho**, clique em **Nova tarefa**.

3.  Insira um nome para o trabalho como *hpccharmrun*.

    ![Novo trabalho HPC][namd_job]

4.  Na página **Detalhes do trabalho** , em **Recursos de trabalho**, selecione o tipo de recurso como **nó** e defina o **mínimo** para 3. , podemos executar o trabalho em três nós Linux e cada nó tem quatro cores.

    ![Recursos de trabalho][job_resources]

5. Clique em **Editar tarefas** no painel esquerdo e, em seguida, clique em **Adicionar** para adicionar uma tarefa para o trabalho.    


6. Na página **detalhes da tarefa e redirecionamento de i/o** , defina os seguintes valores:

    * **Linha de comando** -
`/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`

        >[AZURE.TIP] Linha de comando anterior é um único comando sem quebras de linha. Ele é quebrado para que apareçam em várias linhas em **linha de comando**.

    * **Diretório de trabalho** - /namd2

    * **Mínimo** - 3

    ![Detalhes da tarefa][task_details]

    >[AZURE.NOTE] Você definir a pasta de trabalho aqui porque **charmrun** tenta navegar para a mesma pasta de trabalho em cada nó. Se a pasta de trabalho não estiver definida, o HPC Pack inicia o comando em uma pasta aleatoriamente nomeada criada em um de nós Linux. Isso faz com que o seguinte erro nos outros nós: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` para evitar esse problema, especifique um caminho de pasta que possa ser acessado por todos os nós como a pasta de trabalho.

5.  Clique em **Okey** e clique em **Enviar** para executar esta tarefa.

    Por padrão, o HPC Pack envia o trabalho como sua conta de logon do usuário atual. Uma caixa de diálogo pode solicitar que você digite o nome de usuário e senha após você clicar em **Enviar**.

    ![Credenciais de trabalho][creds]

    Sob algumas condições, HPC Pack lembra as informações do usuário antes de entrada e não mostrar esta caixa de diálogo. Para fazer com que o pacote de HPC mostrá-la novamente, digite o seguinte comando no Prompt de comando e, em seguida, envie o trabalho.

    ```command
    hpccred delcreds
    ```

6.  O trabalho leva vários minutos para concluir.

7.  Localizar o log de trabalho em \\ <headnodeName>\Namd\namd2\namd2_hpccharmrun.log e os arquivos de saída em \\ <headnodeName>\Namd\namd2\namdsample\1-2-sphere\.

8.  Opcionalmente, inicie o VMD para exibir seus resultados de trabalho. As etapas para visualizar o NAMD saída arquivos (neste caso, uma ubiquitin proteína molécula em um círculo de água) estão além do escopo deste artigo. Consulte o [Tutorial de NAMD](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) para obter detalhes.

    ![Resultados de trabalho][vmd_view]

## <a name="sample-files"></a>Arquivos de exemplo

### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Arquivo de configuração de XML de amostra para implantação de cluster por script do PowerShell

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
      <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS66HN</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
    <ServiceName>MyLnxCNService</ServiceName>
     <VMSize>Large</VMSize>
     <NodeCount>4</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>    
```

### <a name="sample-credxml-file"></a>Exemplo de arquivo de cred.xml

```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```

### <a name="sample-hpccharmrunsh-script"></a>Exemplo de script de hpccharmrun.sh

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/keygen.png
[keys]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/keys.png
[namd_job]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/namd_job.png
[job_resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/job_resources.png
[creds]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/creds.png
[task_details]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/task_details.png
[vmd_view]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/vmd_view.png
