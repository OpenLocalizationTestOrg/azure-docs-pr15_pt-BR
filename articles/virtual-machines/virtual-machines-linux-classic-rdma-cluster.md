<properties
 pageTitle="Cluster Linux RDMA para executar aplicativos MPI | Microsoft Azure"
 description="Criar um cluster de Linux de tamanho H16r, H16mr, A8 ou A9 VMs usar a rede RDMA do Azure para executar aplicativos MPI"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Configurar um cluster Linux RDMA para executar aplicativos MPI


Saiba como configurar um cluster Linux RDMA no Azure com [VMs de série de uma série de H ou pesados](virtual-machines-linux-a8-a9-a10-a11-specs.md) para executar aplicativos de Interface de transmissão de mensagens (MPI) paralelos. Este artigo fornece etapas para preparar uma imagem de Linux HPC para executar o Intel MPI em um cluster. Em seguida, você implantar um cluster de VMs usando essa imagem e um dos tamanhos de máquina virtual de Azure habilitados para RDMA (atualmente H16r, H16mr, A8 ou A9). Usar cluster executar aplicativos MPI que se comunicar com eficiência através de uma baixa latência, rede de alta produtividade com base em remoto acesso direto à memória tecnologia (RDMA).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="cluster-deployment-options"></a>Opções de implantação de cluster

A seguir é métodos que você pode usar para criar um cluster de Linux RDMA com ou sem um agendador de trabalho.


* **Scripts de CLI azure** - conforme mostrado posteriormente neste artigo, use a [Interface de linha de comando do Azure](../xplat-cli-install.md) (CLI) para a implantação de um cluster de VMs RDMA capaz de script. A CLI no modo de gerenciamento de serviço cria nós de cluster em série no modelo clássico de implantação, portanto implantar muitos de nós de computação pode demorar vários minutos. Para habilitar a conexão de rede RDMA quando você usa o modelo clássico de implantação, implante VMs no serviço de nuvem do mesmo.

* **Modelos do Gerenciador de recursos do azure** - você também pode usar o modelo de implantação do Gerenciador de recursos para implantar um cluster de VMs capaz de RDMA que se conecta à rede RDMA. Você pode [criar seu próprio modelo](../resource-group-authoring-templates.md), ou verifique os [modelos de início rápido Azure](https://azure.microsoft.com/documentation/templates/) por modelos contribuído pela Microsoft ou a comunidade para implantar a solução desejada. Modelos do gerente de recursos podem fornecer uma maneira rápida e confiável para implantar um cluster de Linux. Para habilitar a conexão de rede RDMA quando você usa o modelo de implantação do Gerenciador de recursos, implante VMs no mesmo conjunto de disponibilidade.

* **HPC Pack** - crie um cluster Microsoft HPC Pack no Azure e adicione habilitados para RDMA nós de computação que executam uma distribuição Linux suportada para acessar a rede RDMA. Consulte [Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-classic-model"></a>Etapas de implantação de amostra no modelo clássico

As etapas a seguir mostram como usar a CLI do Azure para implantar uma máquina virtual a HPC SUSE Linux Enterprise Server (SLES) 12 SP1 do Azure Marketplace, personalizá-la e criar uma imagem de máquina virtual personalizada. Em seguida, use a imagem para a implantação de um cluster de VMs RDMA capaz de script. 

>[AZURE.TIP]Use etapas semelhantes para implantar um cluster de VMs habilitados para RDMA com base em outras imagens HPC com suporte do Azure Marketplace. Algumas etapas podem variar um pouco, conforme indicado. Por exemplo, Intel MPI é incluída e configurado em apenas algumas dessas imagens. E se você implantar uma máquina virtual a HPC SLES 12 em vez de uma máquina virtual HPC do SLES 12 SP1, você precisa atualizar os drivers RDMA. Para obter detalhes, consulte [sobre o A8, A9, A10 e A11 instâncias de pesados](virtual-machines-linux-a8-a9-a10-a11-specs.md#rdma-driver-updates-for-sles-12).


### <a name="prerequisites"></a>Pré-requisitos

*   **Computador cliente** - você precisa de um cliente baseado no Windows, Linux ou Mac computador para se comunicar com o Azure. Estas etapas pressupõem que você estiver usando um cliente Linux.

*   **Assinatura do azure** - se você não tiver uma assinatura, você pode criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos. Clusters maiores, considere uma assinatura flexível ou outras opções de compra. 

*   **Disponibilidade de tamanho de máquina virtual** - atualmente os seguintes tamanhos de instância são capazes de RDMA: H16r, H16mr, A8 e A9. Verificar disponibilidade no Azure regiões [produtos disponíveis por região](https://azure.microsoft.com/regions/services/) . 

*   **Cota de cores** - talvez você precise aumentar a cota de cores para implantar um cluster de VMs pesados. Por exemplo, é necessário pelo menos 128 cores se desejar implantar 8 VMs A9, conforme mostrado neste artigo. Sua assinatura também pode limitar o número de cores que você pode implantar em certas famílias de tamanho de máquina virtual, inclusive a série de H. Para solicitar uma cota aumentar, [Abra uma solicitação de suporte online do cliente](../azure-supportability/how-to-create-azure-support-request.md) sem nenhum custo. 

*   **CLI Azure** - [instalar](../xplat-cli-install.md) o CLI do Azure e [se conectar à sua assinatura do Azure](../xplat-cli-connect.md) do computador cliente.


### <a name="step-1-provision-a-sles-12-sp1-hpc-vm"></a>Etapa 1. Provisionar uma máquina virtual HPC do SLES SP1 12

Após efetuar login no Azure com a CLI do Azure, execute `azure config list` para confirmar que a saída mostra o modo de gerenciamento de serviço. Se não estiver, defina o modo executando este comando:


    azure config mode asm


Digite o seguinte para listar todas as assinaturas que você está autorizado a usar:


    azure account list

A assinatura ativa atual é identificada com `Current` definida como `true`. Se esta assinatura não seja aquele que você deseja usar para criar o cluster, defina a Id de inscrição apropriado como a assinatura ativa:

    azure account set <subscription-Id>

Para ver as imagens de SLES 12 SP1 HPC publicamente disponíveis no Azure, executar um comando semelhante à seguinte, considerando que o shell ambiente suporta **grep**:


    azure vm image list | grep "suse.*hpc"

Agora Provisione uma máquina virtual capaz de RDMA com uma imagem de SLES 12 SP1 HPC executando um comando semelhante ao seguinte:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

onde

* O tamanho (A9 neste exemplo) é um dos tamanhos de máquina virtual habilitados para RDMA.

* O número da porta SSH externo (22 neste exemplo, o que é o padrão SSH) é qualquer número de porta válido. O número da porta SSH interno está definido como 22.

* Um novo serviço de nuvem é criado na região Azure especificado pelo local. Especifique um local no qual o tamanho de máquina virtual que você escolha está disponível.

* O nome de imagem SLES 12 SP1 atualmente pode ser `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824` ou `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824` para suporte de prioridade SUSE (serão cobrados encargos adicionais).

### <a name="step-2-customize-the-vm"></a>Etapa 2. Personalizar a máquina virtual

Após a máquina virtual conclui provisionamento, SSH para a máquina virtual usando o endereço IP externo a máquina virtual (ou nome DNS) e a porta externa número você configurou e personalizá-lo. Para obter detalhes de conexão, veja [como fazer logon em uma máquina Virtual executando Linux](virtual-machines-linux-mac-create-ssh-keys.md). Execute comandos como o usuário que você configurou na máquina virtual, a menos que o acesso à raiz é necessário para concluir uma etapa.

>[AZURE.IMPORTANT]Microsoft Azure não fornece acesso à raiz para VMs Linux. Para obter acesso administrativo quando conectado como um usuário para a máquina virtual, execute comandos usando `sudo`.

* **Atualizações** - instalar atualizações usando **zypper**. Talvez você também queira instalar utilitários NFS. 

    >[AZURE.IMPORTANT]Na máquina virtual HPC SLES 12 SP1, recomendamos que você não aplicar atualizações de núcleo, que podem causar problemas com o RDMA Linux drivers.

* **Intel MPI** - concluir a instalação do Intel MPI na VM SLES 12 SP1 HPC executando o seguinte comando:

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm

* **Bloquear memória** - códigos MPI para bloquear a memória disponível para RDMA, adicionar ou alterar as configurações a seguir no arquivo /etc/security/limits.conf. (Você precisa de acesso raiz para editar o arquivo.) 

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

    >[AZURE.NOTE]Para fins de teste, você também pode definir memlock para ilimitado. Por exemplo: `<User or group name>    hard    memlock unlimited`. Para obter mais informações, consulte [Métodos de melhor conhecidos para configuração de tamanho de memória bloqueada](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size).

* **Teclas SSH para SLES VMs** - gerar SSH chaves para estabelecer a confiança de sua conta de usuário entre os nós de computação no cluster SLES durante a execução de trabalhos MPI. (Se você implantou um baseado em CentOS HPC VM, não siga esta etapa. Consulte instruções posteriormente neste artigo para configurar confiança SSH passwordless entre os nós de cluster depois de capturar a imagem e implantar o cluster.) 

    Execute o seguinte comando para criar chaves SSH. Quando você for solicitado a entrada, pressione Enter para gerar chaves no local padrão sem definir uma senha.

        ssh-keygen

    Acrescente a chave pública para o arquivo authorized_keys para chaves públicas conhecidas.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    No diretório ~/.ssh, editar ou criar o arquivo "config". Forneça o intervalo de endereços IP da rede privada que você planeja usar no Azure (10.32.0.0/16 neste exemplo):

        host 10.32.0.*
        StrictHostKeyChecking no

    Como alternativa, liste o endereço IP de rede privada de cada máquina virtual no seu cluster da seguinte maneira:

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]Configurando `StrictHostKeyChecking no` pode criar um risco de segurança quando um endereço IP específico ou o intervalo não for especificado.

* **Aplicativos** - instale os aplicativos que você precisa sobre esta máquina virtual ou realizar outras personalizações antes de capturar a imagem.

### <a name="step-3-capture-the-image"></a>Etapa 3. Capturar uma imagem

Para capturar a imagem, execute o seguinte comando na VM Linux. Esse comando deprovisions a máquina virtual, mas mantém contas de usuário e chaves SSH que você configurar.

```
sudo waagent -deprovision
```

Em seguida, em seu computador cliente, execute os seguintes comandos de CLI do Azure para capturar uma imagem. Veja [como um computador de virtual Linux clássico como uma imagem de captura](virtual-machines-linux-classic-capture-image.md) para obter detalhes.  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Após executar esses comandos, a imagem de máquina virtual for capturada para seu uso e a máquina virtual é excluída. Agora você tem a imagem personalizada pronta para implantar um cluster.

### <a name="step-4-deploy-a-cluster-with-the-image"></a>Etapa 4. Implantar um cluster com a imagem

Modifique o seguinte script Bash com valores apropriados para seu ambiente e executá-lo do computador cliente. Porque Azure implanta VMs em série no modelo clássico de implantação, ele leva alguns minutos para implantar 8 VMs A9 sugeridos neste script.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>Considerações para um cluster HPC CentOS

Se desejar configurar um cluster com base em uma das imagens baseado em CentOS HPC no mercado Azure em vez de 12 SLES para HPC, siga as etapas gerais na seção anterior. Observe as seguintes diferenças ao provisionar e configurar a máquina virtual:

1. Intel MPI já está instalado em uma máquina virtual provisionada a partir de uma imagem baseada em CentOS HPC. 

2. Configurações de bloqueio de memória já são adicionadas no arquivo de /etc/security/limits.conf da VM.

2. Não gere chaves SSH na VM você provisionar para captura. Em vez disso, é recomendável configurar a autenticação baseada no usuário após implantar o cluser. Consulte a seção a seguir.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>Configurar passwordless confiança SSH no cluster

Em um cluster HPC baseado em CentOS, há dois métodos para estabelecer confiança entre os nós de computação: autenticação baseada em host e a autenticação baseada no usuário. Autenticação baseada em host estiver fora do escopo deste artigo e geralmente deve ser feita por meio de um script de extensão durante a implantação. Autenticação baseada no usuário é conveniente para estabelecer confiança após a implantação e requer a geração e o compartilhamento de chaves SSH entre os nós de computação no cluster. Este método é conhecido como passwordless login SSH e é necessário durante a execução de trabalhos MPI. 

Um exemplo de script contribuído da comunidade está disponível no [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) para habilitar a autenticação de usuário fácil em um cluster HPC baseado em CentOS. Baixar e usar este script usando as etapas a seguir. Você também pode modificar o script ou usar qualquer outro método para estabelecer passwordless autenticação de SSH entre os nós de computação de cluster.

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh
    
Para executar o script, você precisa saber o prefixo para seus endereços IP de sub-rede. Obtenha o prefixo executando o seguinte comando em um de nós do cluster. A saída deve parecer com 10.1.3.5 e o prefixo é o 10.1.3 parte.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Agora execute o script usando três parâmetros: o nome de usuário comuns em nós de computação, a senha comuns para esse usuário nos nós de computação e o prefixo de sub-rede que foi retornado no comando anterior.


    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Esse script faz o seguinte:

* Cria um diretório no nó host chamado .ssh, que é necessário para passwordless login. 
* Cria um arquivo de configuração no diretório .ssh que instrui login passwordless para permitir o logon de qualquer nó no cluster. 
* Cria arquivos contendo os nomes de nó e endereços IP de nó para todos os nós no cluster. Esses arquivos são deixados após o script é executado para referência futura. 
* Cria um par de chaves público e privado para cada nó de cluster, inclusive o nó de host e entradas no arquivo authorized_keys.

>[AZURE.WARNING]Executar este script pode criar um risco de segurança. Certifique-se de que as informações da chave públicas em ~/.ssh não são distribuídas.


## <a name="configure-intel-mpi"></a>Configurar Intel MPI

Para executar aplicativos MPI no Azure Linux RDMA, você precisa configurar determinadas variáveis de ambiente específicos para Intel MPI. Aqui está um exemplo de script Bash de configurar as variáveis necessárias para executar um aplicativo. Altere o caminho para mpivars.sh conforme necessário para sua instalação do Intel MPI.

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

O formato do arquivo de host é da seguinte maneira. Adicione uma linha para cada nó no cluster. Especifique os endereços IP particulares a VNet definido anteriormente, não os nomes DNS. Por exemplo, para que dois hosts com endereços IP 10.32.0.1 e 10.32.0.2 o arquivo contém o seguinte:

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Executar MPI em um cluster de dois nós básico

Se você ainda não fez isso, primeiro configure o ambiente para Intel MPI. 

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-a-simple-mpi-command"></a>Executar um comando MPI simples

Execute um comando MPI simples em um de nós de computação para mostrar que MPI está instalada corretamente e pode comunicar-se pelo menos que dois nós de computadores. O seguinte comando **mpirun** executa o comando **hostname** em dois nós.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
A saída deve listar os nomes de todos os nós passado como entrada para `-hosts`. Por exemplo, um comando **mpirun** com dois nós retorna resultado similar ao seguinte:

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Executar uma avaliação de desempenho MPI

O seguinte comando Intel MPI executa uma referência de pingpong para verificar a configuração de cluster e conexão com a rede RDMA.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

Em um cluster de trabalho com dois nós, você deve ver saída semelhante à seguinte. Na rede do Azure RDMA, espere latência abaixo 3 microssegundos mensagem ou tamanhos até 512 bytes.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>Próximas etapas

* Tente implantar e executar sua MPI Linux aplicativos em seu cluster Linux.

* Consulte a [documentação da biblioteca de MPI Intel](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) para obter orientações sobre Intel MPI.

* Tente um [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) para criar um cluster de Intel Lustre usando uma imagem baseada em CentOS HPC. Para obter detalhes, consulte esta [postagem de blog](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/).
