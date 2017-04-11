<properties
 pageTitle="Executar OpenFOAM com HPC Pack no Linux VMs | Microsoft Azure"
 description="Implantar um cluster Microsoft HPC Pack no Azure e execute um trabalho OpenFOAM em vários nós de computação Linux através de uma rede RDMA."
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
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Executar OpenFoam com o Microsoft HPC Pack em um cluster de Linux RDMA no Azure

Este artigo mostra a você uma maneira de executar OpenFoam no Azure máquinas virtuais. Aqui, você implantar um cluster de Microsoft HPC Pack conosco de computação Linux no Azure e execute um [OpenFoam](http://openfoam.com/) trabalho com Intel MPI. Você pode usar VMs habilitados para RDMA do Azure para os nós de computação, para que os nós de computação se comunicar através da rede RDMA do Azure. Outras opções para executar OpenFoam no Azure incluem totalmente configuradas comerciais imagens disponíveis no mercado, como o [OpenFoam 2.3 no CentOS 6 do UberCloud](https://azure.microsoft.com/marketplace/partners/ubercloud/openfoam-v2dot3-centos-v6/)e executando no [Lote do Azure](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/). 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

OpenFOAM (para abrir a operação de campo e manipulação) é um pacote de software de código-fonte aberto fluidos computacional (CFD) que é usado amplamente em engenharia e ciência, em organizações comerciais e acadêmicas. Ele inclui ferramentas para meshing, especialmente snappyHexMesh, um mesher paralelizado para geometrias CAD complexas e para processamento prévio e posterior. Quase todos os processos executados em paralelo, permitindo que os usuários tirar proveito do hardware do computador à sua disposição.  

Microsoft HPC Pack fornece recursos para executar o HPC grande escala e aplicativos em paralelo, inclusive aplicativos MPI, em clusters de máquinas virtuais do Microsoft Azure. HPC Pack também oferece suporte a execução HPC Linux nó VMs implantadas em um cluster de HPC Pack de computação de aplicativos no Linux. Para obter uma introdução ao uso de nós de computação Linux com HPC Pack, consulte [Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md) .

>[AZURE.NOTE] Este artigo ilustra como executar uma carga de trabalho Linux MPI com HPC Pack. Ele pressupõe que você tenha alguma familiaridade com administração do sistema Linux e executando cargas de trabalho MPI em clusters Linux. Se você usar versões de MPI e OpenFOAM diferente daqueles mostrados neste artigo, talvez seja necessário modificar algumas etapas de instalação e configuração. 

## <a name="prerequisites"></a>Pré-requisitos

*   **Nós de computadores de cluster HPC Pack com Linux habilitados para RDMA** - implantar um cluster de HPC Pack com tamanho A8, A9, H16r ou Linux H16rm nós de computação usando um [modelo do Gerenciador de recursos do Azure](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) ou um [script do PowerShell do Azure](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Consulte [Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md) para os pré-requisitos e as etapas para qualquer opção. Se você escolher a opção de implantação de script do PowerShell, consulte o arquivo de configuração de amostra nos arquivos de exemplo no final deste artigo. Use esta configuração para implantar um cluster com base no Azure HPC Pack consiste de um nó de cabeça de A8 Windows Server 2012 R2 de tamanho e 2 nós de computação de A8 SUSE Linux Enterprise Server 12 de tamanho. Substitua os valores adequados para seus nomes de assinatura e de serviço. 

    **Outras questões saber**

    *   Para pré-requisitos de rede Linux RDMA no Azure, consulte [sobre H série e pesados VMs A série](virtual-machines-windows-a8-a9-a10-a11-specs.md).

    *   Se você usar a opção de implantação de script do Powershell, implante todos os nós de computação Linux dentro de serviço de uma nuvem para usar a conexão de rede RDMA.

    *   Depois de implantar os nós Linux, conecte SSH para executar tarefas administrativas adicionais. Encontre os detalhes de conexão SSH para cada VM Linux no portal do Azure.  
        
*   **Intel MPI** - executem OpenFOAM em SLES 12 HPC calcular nós no Azure, você precisa instalar o tempo de execução Intel MPI biblioteca 5 do [site Intel.com](https://software.intel.com/en-us/intel-mpi-library/). (Intel MPI 5 vem pré-instalado em imagens com base em CentOS HPC.)  Em uma etapa posterior, se necessário, instale MPI Intel em seus nós de computação Linux. Para se preparar para esta etapa, depois que você registrar com Intel, siga o link no email de confirmação à página da web relacionados. Em seguida, copie o link de download para o arquivo. tgz para a versão apropriada do Intel MPI. Este artigo se baseia em Intel MPI versão 5.0.3.048.

*   **Pacote de origem OpenFOAM** - baixar o software de pacote de origem OpenFOAM para Linux do [site OpenFOAM Foundation](http://openfoam.org/download/2-3-1-source/). Este artigo se baseia na versão do pacote de origem 2.3.1, disponível para download como OpenFOAM-2.3.1.tgz. Siga as instruções neste artigo para descompactar e compilar OpenFOAM em nós de computação Linux.

*   **EnSight** (opcional) - para ver os resultados da simulação OpenFOAM, baixe e instale o programa de visualização e análise de [EnSight](https://www.ceisoftware.com/download/) . Informações de licenciamento e download estiver no site EnSight.


## <a name="set-up-mutual-trust-between-compute-nodes"></a>Configurar a confiança comum entre nós de computação

Executar um trabalho entre os nós em vários nós Linux requer os nós confiar em si (por **rsh** ou **ssh**). Quando você cria o cluster HPC Pack com o script de implantação do Microsoft HPC Pack IaaS, o script configura automaticamente permanente confiança comum para a conta de administrador que você especificar. Para usuários não-administrador que você cria no domínio do cluster, você precisa configurar temporária confiança comum entre os nós quando um trabalho é alocado a eles e destroy relação após concluir o trabalho. Para estabelecer a relação de confiança para cada usuário, forneça um par de chaves RSA para cluster que HPC Pack usa a relação de confiança.

### <a name="generate-an-rsa-key-pair"></a>Gerar um par de chaves RSA

É fácil gerar um par de chaves RSA, que contém uma chave pública e uma chave privada, executando o comando de **ssh keygen** Linux.

1.  Faça logon um computador Linux.

2.  Execute o seguinte comando:

    ```
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] Pressione **Enter** para usar as configurações padrão até que o comando seja concluído. Não digite uma senha aqui; Quando for solicitada uma senha, pressione **Enter**.

    ![Gerar um par de chaves RSA][keygen]

3.  Altere o diretório para o ~/.ssh. A chave privada é armazenada em id_rsa e a chave pública no id_rsa.pub.

    ![Chaves pública e privadas][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Adicionar o par de chaves ao cluster HPC Pack
1.  Fazer uma conexão de área de trabalho remota para seu nó principal com sua conta de administrador de HPC Pack (a conta de administrador configurar quando você executou o script de implantação).

2. Use os procedimentos padrão do Windows Server para criar uma conta de usuário de domínio no domínio do Active Directory do cluster. Por exemplo, use a ferramenta de usuário do Active Directory e computadores no nó principal. Os exemplos deste artigo presumem que você cria um usuário de domínio chamado hpclab\hpcuser.

3.  Crie um arquivo denominado C:\cred.xml e copie os dados da chave RSA nele. Um exemplo de arquivo de cred.xml está no final deste artigo.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

4.  Abra um Prompt de comando e digite o seguinte comando para definir os dados de credenciais para a conta de hpclab\hpcuser. Use o parâmetro **extendeddata** para passar o nome de arquivo de C:\cred.xml que você criou para os principais dados.

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Este comando concluído com êxito sem saída. Depois de definir as credenciais para as contas de usuário que você precisa executar trabalhos, armazenar o arquivo cred.xml em um local seguro ou excluí-la.

5.  Se você gerou o par de chaves RSA em um dos seus nós Linux, lembre-se excluir as chaves após terminar de usá-los. Se HPC Pack encontra um arquivo de id_rsa existente ou um arquivo de id_rsa.pub, ele não configurar confiança comum.

>[AZURE.IMPORTANT] Não recomendamos executar um trabalho de Linux como um administrador de cluster em um cluster compartilhado, como um trabalho enviado por um administrador é executado sob a conta raiz em nós Linux. No entanto, um trabalho enviado por um usuário não-administrador executa em uma conta de usuário Linux local com o mesmo nome que o usuário de trabalho. Nesse caso, HPC Pack configura confiança comum para esse usuário Linux em nós alocado para o trabalho. Você pode configurar o usuário Linux manualmente em nós Linux antes de executar o trabalho ou HPC Pack cria o usuário automaticamente quando o trabalho é enviado. Se o pacote de HPC cria o usuário, HPC Pack excluirá após concluir o trabalho. Para reduzir ameaças de segurança, HPC Pack remove as chaves após a conclusão do trabalho.

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configurar um compartilhamento de arquivo para nós Linux

Agora, configure um compartilhamento SMB padrão em uma pasta no nó principal. Para permitir que os nós Linux para acessar arquivos de aplicativo com um caminho comum, monte a pasta compartilhada em nós Linux. Se desejar, você pode usar outra opção, como um compartilhamento de arquivos do Azure - recomendado para muitos cenários - ou um compartilhamento NFS de compartilhamento de arquivos. Consulte os etapas e informações detalhadas em [Introdução a nós de computação Linux em um Cluster de pacote HPC no Azure](virtual-machines-linux-classic-hpcpack-cluster.md)de compartilhamento de arquivos.

1.  Crie uma pasta no nó principal e compartilhá-la para todos, definindo privilégios de leitura/gravação. Por exemplo, compartilhar C:\OpenFOAM no nó principal como \\ \\SUSE12RDMA HN\OpenFOAM. Aqui, *SUSE12RDMA-HN* é o nome do host do nó principal.

2.  Abra uma janela do Windows PowerShell e execute os seguintes comandos:

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

    clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

O primeiro comando cria uma pasta chamada /openfoam em todos os nós no grupo LinuxNodes. O segundo comando montagens //SUSE12RDMA-HN/OpenFOAM a pasta compartilhada em nós Linux com dir_mode e file_mode bits definidos como 777. O *nome de usuário* e a *senha* no comando devem ser as credenciais de um usuário no nó principal.

>[AZURE.NOTE]O "\`" símbolo no segundo comando é um símbolo de escape para PowerShell. "\`," significa "," (caractere de vírgula) é uma parte do comando.

## <a name="install-mpi-and-openfoam"></a>Instalar MPI e OpenFOAM

Para executar OpenFOAM como um trabalho MPI na rede RDMA, você precisa compilar OpenFOAM com as bibliotecas Intel MPI. 

Primeiro, execute vários comandos de **clusrun** instalar bibliotecas MPI Intel (se ainda não estiver instalado) e OpenFOAM em seus nós Linux. Use o compartilhamento de nó principal configurado anteriormente para compartilhar os arquivos de instalação entre os nós Linux.

>[AZURE.IMPORTANT]Estas instalação e etapas compilação são exemplos. É necessário algum conhecimento Linux de administração do sistema para garantir que bibliotecas e compiladores dependentes estão instaladas corretamente. Você talvez precise modificar determinadas variáveis de ambiente ou outras configurações para suas versões do Intel MPI e OpenFOAM. Para obter detalhes, consulte [A biblioteca de MPI Intel para o guia de instalação do Linux](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) e [Instalação de pacote de origem OpenFOAM](http://openfoam.org/download/2-3-1-source/) para seu ambiente.


### <a name="install-intel-mpi"></a>Instalar o Intel MPI

Salve o pacote de instalação baixado para MPI Intel (l_mpi_p_5.0.3.048.tgz neste exemplo) no C:\OpenFoam no nó principal para que os nós Linux podem acessar esse arquivo de /openfoam. Execute **clusrun** para instalar a biblioteca Intel MPI em todos os nós Linux.

1.  Os seguintes comandos Copiar o pacote de instalação e extraia-o para /opt/intel em cada nó.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel

    clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
    ```

2.  Para instalar a biblioteca de MPI Intel silenciosamente, use um arquivo de silent.cfg. Você pode encontrar um exemplo os arquivos de exemplo no final deste artigo. Coloque este arquivo em /openfoam a pasta compartilhada. Para obter detalhes sobre o arquivo de silent.cfg, consulte [A biblioteca de MPI Intel para o guia de instalação do Linux – silencioso de instalação](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall).

    >[AZURE.TIP]Certifique-se de que você salvar seu arquivo de silent.cfg como um arquivo de texto com Linux finais (somente alimentação de linha, não CR LF) de linha. Esta etapa garante que ele seja executado corretamente em nós Linux.

3.  Instale a biblioteca de MPI Intel no modo silencioso.
 
    ```
    clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
    ```
    
### <a name="configure-mpi"></a>Configurar MPI

Para testar, adicione as seguintes linhas para os /etc/security/limits.conf em cada um de nós Linux:


    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


Reinicie os nós Linux após atualizar o arquivo limits.conf. Por exemplo, use o seguinte comando **clusrun** :

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

Depois de reiniciar, certifique-se de que a pasta compartilhada é montada como /openfoam.

### <a name="compile-and-install-openfoam"></a>Compilar e instalar OpenFOAM

Salve o pacote de instalação baixado para o pacote de fonte de OpenFOAM (OpenFOAM-2.3.1.tgz neste exemplo) para C:\OpenFoam no nó principal para que os nós Linux podem acessar esse arquivo de /openfoam. Execute comandos **clusrun** compilar OpenFOAM em todos os nós Linux.


1.  Criar uma /opt/OpenFOAM de pasta em cada nó Linux, copie o pacote de origem para esta pasta e extraia-o de lá.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM

    clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
    
    clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
    ```

2.  Para compilar OpenFOAM com a biblioteca de MPI Intel, primeiro configure algumas variáveis de ambiente para Intel MPI e OpenFOAM. Use um script de bash chamado settings.sh para definir as variáveis. Você pode encontrar um exemplo os arquivos de exemplo no final deste artigo. Coloque este arquivo (salvo com finais de linha Linux) em /openfoam a pasta compartilhada. Este arquivo também contém configurações para os tempos de execução MPI e OpenFOAM que você usar posteriormente para executar um trabalho de OpenFOAM.

3. Instale pacotes dependentes necessários para compilar OpenFOAM. Dependendo da distribuição do Linux, você precisará primeiro adicionar um repositório. Execute comandos **clusrun** similares ao seguinte:

    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
    
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
    
    Se necessário, SSH para cada nó Linux para executar os comandos para confirmar que ele é executado corretamente.

4.  Execute o seguinte comando para compilar OpenFOAM. O processo de compilação leva algum tempo para ser concluída e gera uma grande quantidade de informações de log para saída padrão; portanto, use a opção **/ AVI** para exibir a saída AVI.

    ```
    clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
    ```
    
    >[AZURE.NOTE]O "\`" símbolo no comando é um símbolo de escape para PowerShell. "\`&" significa que a "&" é uma parte do comando.

## <a name="prepare-to-run-an-openfoam-job"></a>Preparar para executar um trabalho de OpenFOAM

Prepare-se agora executar um trabalho MPI chamado sloshingTank3D, que é um dos exemplos de OpenFoam, em dois nós Linux. 

### <a name="set-up-the-runtime-environment"></a>Configurar o ambiente de tempo de execução

Para configurar os ambientes de tempo de execução para MPI e OpenFOAM em nós Linux, execute o seguinte comando em uma janela do Windows PowerShell no nó principal. (Este comando é válido para SUSE Linux somente.)

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>Preparar dados de exemplo

Use o compartilhamento de nó principal que você tenha configurado anteriormente para compartilhar arquivos entre os nós Linux (montados como /openfoam).

1.  Nós de computação SSH para uma das sua Linux.

2.  Execute o seguinte comando para configurar o ambiente de tempo de execução de OpenFOAM, se ainda não fez isso.

    ```
    $ source /openfoam/settings.sh
    ```
    
3.  Copie o exemplo sloshingTank3D para a pasta compartilhada e navegue até ele.

    ```
    $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/

    $ cd /openfoam/sloshingTank3D
    ```

4.  Quando você usa os parâmetros padrão deste exemplo, pode levar dezenas de minutos para ser executado, para que você talvez queira modificar alguns parâmetros para que ele seja executado com mais rapidez. Uma opção simple é modificar o tempo Etapa variáveis deltaT e writeInterval no arquivo de sistema/controlDict. Este arquivo armazena todos os dados de entrada relacionados para o controle do tempo e ler e gravar dados de solução. Por exemplo, você pode alterar o valor de deltaT de 0,05 0,5 e o valor de writeInterval de 0,05 0,5.

    ![Modificar variáveis de etapa][step_variables]

5.  Especifique os valores desejados para as variáveis no arquivo de sistema/decomposeParDict. Este exemplo usa duas nós Linux cada com 8 cores, então defina numberOfSubdomains para 16 e n de hierarchicalCoeffs para (1 1 16), que significa executar OpenFOAM em paralelo com processos de 16. Para obter mais informações, consulte [OpenFOAM guia do usuário: 3,4 aplicativos em execução em paralelo](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).

    ![Decompor processos][decompose]

6.  Execute os seguintes comandos do diretório sloshingTank3D para preparar os dados de exemplo.

    ```
    $ . $WM_PROJECT_DIR/bin/tools/RunFunctions

    $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict

    $ runApplication blockMesh

    $ cp 0/alpha.water.org 0/alpha.water

    $ runApplication setFields  
    ```
    
7.  No nó principal, você deve ver que os arquivos de dados de exemplo são copiados em C:\OpenFoam\sloshingTank3D. (C:\OpenFoam é a pasta compartilhada no nó principal.)

    ![Arquivos de dados no nó principal][data_files]

### <a name="host-file-for-mpirun"></a>Arquivo de host para mpirun

Nesta etapa, você cria um arquivo de host (uma lista de nós de computação) que usa o comando **mpirun** .

1.  Em um de nós Linux, crie um arquivo denominado arquivo de host em /openfoam, para que esse arquivo possa ser encontrado /openfoam/hostfile em todos os nós Linux.

2.  Escreva seus nomes de nó Linux no arquivo. Neste exemplo, o arquivo contém os nomes a seguir:
    
    ```       
    SUSE12RDMA-LN1
    SUSE12RDMA-LN2
    ```
    
    >[AZURE.TIP]Você também pode criar esse arquivo em C:\OpenFoam\hostfile no nó principal. Se você escolher essa opção, salvá-lo como um arquivo de texto com Linux finais de linha (somente alimentação de linha, não CR LF). Isso garante que ele seja executado corretamente em nós Linux.

    **Embalagem de script bash**

    Se você tiver muitos de nós Linux e você deseja que o trabalho seja executado em apenas algumas delas, ele não é uma boa ideia usar um arquivo de host fixo, porque você não sabe quais nós serão alocados ao seu trabalho. Nesse caso, escreva uma embalagem de script bash para **mpirun** criar o arquivo de host automaticamente. Você pode encontrar uma embalagem de script do exemplo bash chamada hpcimpirun.sh no final deste artigo e salvá-lo como /openfoam/hpcimpirun.sh. Este script de exemplo faz o seguinte:

    1.  Configura as variáveis de ambiente para **mpirun**e alguns parâmetros de comando de adição para executar o trabalho MPI através da rede RDMA. Nesse caso, ele define as variáveis a seguir:

        *   I_MPI_FABRICS = shm:dapl
        *   I_MPI_DAPL_PROVIDER = deuma-v2-ib0
        *   I_MPI_DYNAMIC_CONNECTION = 0

    2.  Cria um arquivo de host acordo com o ambiente variável $CCP_NODES_CORES, que é definido pelo nó cabeça HPC quando o trabalho é ativado.

        O formato de $CCP_NODES_CORES segue este padrão:

        ```
        <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
        ```

        onde

        * `<Number of nodes>`-o número de nós alocados para este trabalho.  
        
        * `<Name of node_n_...>`-o nome de cada nó alocado para este trabalho.
        
        * `<Cores of node_n_...>`-o número de cores no nó alocados para este trabalho.

        Por exemplo, se o trabalho precisa dois nós para executar, $CCP_NODES_CORES é semelhante a
        
        ```
        2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
        ```
        
    3.  Chama o comando **mpirun** e acrescenta dois parâmetros à linha de comando.

        * `--hostfile <hostfilepath>: <hostfilepath>`-o caminho do arquivo host o script cria

        * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}`-uma variável de ambiente definida pelo nó de cabeça HPC Pack, que armazena o número de cores totais alocados para este trabalho. Nesse caso, ele especifica o número de processos para **mpirun**.


## <a name="submit-an-openfoam-job"></a>Enviar um trabalho de OpenFOAM

Agora você pode enviar um trabalho no HPC Cluster Manager. Você precisa passar o script hpcimpirun.sh nas linhas de comando para algumas das tarefas de trabalho.

1. Conectar ao seu nó principal do cluster e comece a HPC Cluster Manager.

2. **Em gerenciamento de recursos**, certifique-se de que os nós de computação Linux estão no estado **Online** . Se não estiverem, selecione-os e clique em **Colocar on-line**.

3.  Em **Gerenciamento de trabalho**, clique em **Nova tarefa**.

4.  Insira um nome para o trabalho como _sloshingTank3D_.

    ![Detalhes do trabalho][job_details]

5.  Em **recursos de trabalho**, escolha o tipo de recurso como "Nó" e defina o mínimo para 2. Essa configuração executa o trabalho em dois nós Linux, cada um com oito cores neste exemplo.

    ![Recursos de trabalho][job_resources]

6. Clique em **Editar tarefas** no painel esquerdo e, em seguida, clique em **Adicionar** para adicionar uma tarefa para o trabalho. Adicione quatro tarefas para o trabalho com os seguintes linhas de comando e configurações.

    >[AZURE.NOTE]Executando `source /openfoam/settings.sh` configura os ambientes de tempo de execução OpenFOAM e MPI, portanto, cada uma das seguintes tarefas chama-antes do comando OpenFOAM.

    *   **Tarefa 1**. Execute o **decomposePar** para gerar arquivos de dados para executar **interDyMFoam** em paralelo.
    
        *   Atribuir um nó à tarefa

        *   **Linha de comando** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
    
        *   **Diretório de trabalho** - / openfoam/sloshingTank3D
        
        Consulte a figura a seguir. Você pode configurar as tarefas restantes da mesma forma.

        ![Detalhes da tarefa 1][task_details1]

    *   **Tarefa 2**. Execute **interDyMFoam** em paralelo para calcular a amostra.

        *   Atribuir dois nós à tarefa

        *   **Linha de comando** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`

        *   **Diretório de trabalho** - / openfoam/sloshingTank3D

    *   **Tarefa 3**. Execute o **reconstructPar** para mesclar os conjuntos de diretórios de tempo de cada diretório de processor_N_ em um único conjunto.

        *   Atribuir um nó à tarefa

        *   **Linha de comando** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`

        *   **Diretório de trabalho** - / openfoam/sloshingTank3D

    *   **Tarefa 4**. Execute **foamToEnsight** em paralelo para converter os arquivos de resultado de OpenFOAM no formato de EnSight e coloque os arquivos de EnSight em um diretório chamado Ensight no diretório de maiusculas.

        *   Atribuir dois nós à tarefa

        *   **Linha de comando** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`

        *   **Diretório de trabalho** - / openfoam/sloshingTank3D

6.  Adicione dependências a essas tarefas na tarefa ordem crescente.

    ![Dependências entre tarefas][task_dependencies]

7.  Clique em **Enviar** para executar esta tarefa.

    Por padrão, o HPC Pack envia o trabalho como sua conta de logon do usuário atual. Após você clicar em **Enviar**, você poderá ver uma caixa de diálogo solicitando que você digite o nome de usuário e senha.

    ![Credenciais de trabalho][creds]

    Sob algumas condições, HPC Pack lembra as informações do usuário antes de entrada e não mostrar esta caixa de diálogo. Para fazer com que o pacote de HPC mostrá-la novamente, digite o seguinte comando no prompt de comando e, em seguida, envie o trabalho.

    ```
    hpccred delcreds
    ```

8.  O trabalho demora de dezenas de minutos a várias horas de acordo com os parâmetros que você definiu para a amostra. O mapa de calor, você verá o trabalho em execução em nós Linux. 

    ![Mapa de calor][heat_map]

    Em cada nó, oito processos são iniciados.

    ![Processos de Linux][linux_processes]

9.  Quando o trabalho for concluído, localize os resultados de trabalho em pastas em C:\OpenFoam\sloshingTank3D e os arquivos de log em C:\OpenFoam.


## <a name="view-results-in-ensight"></a>Exibir os resultados em EnSight

Opcionalmente, use [EnSight](https://www.ceisoftware.com/) para visualizar e analisar os resultados do trabalho OpenFOAM. Para obter mais informações sobre a visualização e animação em EnSight, consulte este [guia de vídeo](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html).

1.  Depois de instalar EnSight no nó principal, inicie-o.

2.  Abra C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case.

    Você verá um tanque no visualizador.

    ![Tanque em EnSight][tank]

3.  Criar um **Isosurface** de **internalMesh**e, em seguida, escolha a variável **alpha_water**.

    ![Criar um isosurface][isosurface]

4.  Defina a cor para **Isosurface_part** criado na etapa anterior. Por exemplo, defina-água azul.

    ![Editar cor isosurface][isosurface_color]

5.  Crie um **volume Iso** de **paredes** selecionando **paredes** no painel **Parts** e clique no botão **Isosurfaces** na barra de ferramentas.

6.  Na caixa de diálogo, selecione **tipo** como **Isovolume** e defina o mínimo do **intervalo de Isovolume** 0,5. Para criar a isovolume, clique em **criar com partes selecionadas**.

7.  Defina a cor para **Iso_volume_part** criado na etapa anterior. Por exemplo, defina-água profunda azul.

8.  Defina a cor para **as paredes**. Por exemplo, defina-o para branco transparente.

9. Agora, clique em **Reproduzir** para ver os resultados da simulação.

    ![Resultado de combustível][tank_result]

## <a name="sample-files"></a>Arquivos de exemplo

### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Arquivo de configuração de XML de amostra para implantação de cluster por script do PowerShell

 ```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>suse12rdmavnet</VNetName>
    <SubnetName>SUSE12RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>SUSE12RDMA-HN</VMName>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <NodeCount>2</NodeCount>
      <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
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
### <a name="sample-silentcfg-file-to-install-mpi"></a>Arquivo de silent.cfg de exemplo para instalar MPI

```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no


```

### <a name="sample-settingssh-script"></a>Exemplo de script de settings.sh

```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```


###<a name="sample-hpcimpirunsh-script"></a>Exemplo de script de hpcimpirun.sh

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
    ${MPIRUN} $*
else
    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

    # Get every node name and write into the hostfile file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the mpirun with hostfile arg
    ${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/keygen.png
[keys]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/keys.png
[step_variables]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/step_variables.png
[data_files]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/data_files.png
[decompose]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/decompose.png
[job_details]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/job_details.png
[job_resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/job_resources.png
[task_details1]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/task_details1.png
[task_dependencies]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/task_dependencies.png
[creds]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/creds.png
[heat_map]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/heat_map.png
[tank]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/tank.png
[tank_result]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/tank_result.png
[isosurface]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/isosurface.png
[isosurface_color]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/isosurface_color.png
[linux_processes]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/linux_processes.png
