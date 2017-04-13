<properties
 pageTitle="Configurar um cluster de RDMA do Windows para executar aplicativos MPI | Microsoft Azure"
 description="Aprenda a criar um cluster de Windows HPC Pack com tamanho H16r, H16mr, A8 ou A9 VMs usar a rede RDMA do Azure para executar aplicativos MPI."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/20/2016"
 ms.author="danlep"/>

# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Configurar um cluster de Windows RDMA com HPC Pack para executar aplicativos MPI

Configure um cluster de RDMA do Windows Azure com [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) e [série H ou pesados instâncias de uma série](virtual-machines-windows-a8-a9-a10-a11-specs.md) para executar aplicativos de Interface de transmissão de mensagens (MPI) paralelos. Quando você configura habilitados para RDMA baseados no Windows Server, nós em um cluster de HPC Pack, aplicativos MPI se comunicar com eficiência através de uma baixa latência, a rede de alta taxa de transferência no Azure com base em tecnologia de acesso (RDMA) de memória direta remoto.

Se você quiser executar cargas de trabalho MPI em VMs Linux que acessar a rede RDMA do Azure, consulte [Configurar um cluster Linux RDMA para executar aplicativos MPI](virtual-machines-linux-classic-rdma-cluster.md).


## <a name="hpc-pack-cluster-deployment-options"></a>Opções de implantação de cluster HPC Pack
Microsoft HPC Pack é uma ferramenta fornecidos sem nenhum custo adicional para criar HPC clusters local ou no Azure para executar o Windows ou Linux HPC aplicativos. HPC pacote inclui um ambiente de tempo de execução para a implementação da Microsoft do mensagem passando Interface para Windows (MS-MPI). Quando usada com habilitados para RDMA instâncias com sistema operacional Windows Server compatível, HPC Pack oferece uma opção eficiente executar aplicativos MPI do Windows que acessam a rede RDMA do Azure. 

Este artigo apresenta dois cenários e links para orientações detalhadas para configurar um cluster de Winodws RDMA com Microsoft HPC Pack. 

* Cenário 1. Implantar instâncias de função pesados trabalhador (PaaS)

* Cenário 2. Implantar nós de computação em VMs pesados (IaaS)

Para pré-requisitos gerais usar pesados instâncias com o Windows, consulte [sobre H série e pesados VMs A série](virtual-machines-windows-a8-a9-a10-a11-specs.md) .



## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Cenário 1. Implantar instâncias de função pesados trabalhador (PaaS)

A partir de um cluster de HPC Pack existente, adicione recursos de computação extra nas instâncias de função do Azure trabalhador (nós Azure) em execução em um serviço de nuvem (PaaS). Esse recurso, também chamado de "intermitente no Azure" do HPC Pack, oferece suporte a uma variedade de tamanhos para as instâncias de função de trabalho. Ao adicionar nós do Azure, basta Especifica um dos tamanhos habilitados para RDMA.

A seguir é considerações e etapas para intermitente para capaz de RDMA Azure instâncias de uma já existente (normalmente locais) cluster. Use procedimentos semelhantes para adicionar instâncias de função de trabalho a um nó de cabeça HPC Pack que é implantado em uma máquina virtual do Azure.

>[AZURE.NOTE] Para um tutorial para intermitente no Azure com HPC Pack, consulte [Configurar um cluster híbrida com HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Observe as considerações nas etapas abaixo que se aplicam especificamente a habilitados para RDMA nós Azure.

![Intermitente no Azure][burst]

### <a name="steps"></a>Etapas

4. **Implantar e configurar um nó principal HPC Pack 2012 R2**

    Baixe o pacote de instalação mais recente do HPC Pack no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=49922). Para requisitos e instruções para se preparar para uma implantação do Azure intermitente, consulte [HPC Pack guia de Introdução](https://technet.microsoft.com/library/jj884144.aspx) e [intermitente para instâncias de trabalho do Azure com Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

5. **Configurar um certificado de gerenciamento na assinatura do Azure**

    Configure um certificado para proteger a conexão entre o nó principal e o Azure. Para obter opções e procedimentos, consulte [cenários para configurar o certificado de gerenciamento do Azure para HPC Pack](http://technet.microsoft.com/library/gg481759.aspx). Para implantações de teste, HPC Pack instala um padrão Microsoft HPC Azure gerenciamento certificado rapidamente, você pode carregar à sua assinatura do Azure.

6. **Criar um novo serviço de nuvem e uma conta de armazenamento**

    Use o portal de clássico Azure para criar um serviço na nuvem e uma conta de armazenamento para a implantação em uma região onde as instâncias capaz de RDMA estão disponíveis.

7. **Criar um modelo de nó Azure**

    Use o nó Assistente para criar modelo no HPC Cluster Manager. Para obter etapas, consulte [criar um modelo de nó Azure](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) "Etapas para implantar o Azure nós com Microsoft HPC Pack".

    Para testes iniciais, sugerimos Configurando uma política de disponibilidade manual no modelo.

8. **Adicionar nós ao cluster**

    Use o Assistente para adicionar nó no Gerenciador de Cluster de HPC. Para obter mais informações, consulte [Adicionar nós Azure ao Windows HPC Cluster](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).

    Ao especificar o tamanho de nós, selecione um dos tamanhos da instância habilitados para RDMA.
    
    >[AZURE.NOTE]Em cada intermitente para implantação do Azure com as instâncias de pesados, HPC Pack implanta automaticamente um mínimo de 2 instâncias capaz de RDMA (como A8) como nós de proxy, além das instâncias de função Azure trabalhador especificado por você. Os nós de proxy usarem cores que estão alocados para a assinatura e provocam encargos juntamente com as instâncias de função trabalhador Azure.

9. **Iniciar (Configurar) os nós e exibi-las online para executar trabalhos**

    Selecione os nós e usar a ação **Iniciar** no HPC Cluster Manager. Quando provisionamento for concluído, selecione os nós e usar a ação de **Colocar on-line** no HPC Cluster Manager. Os nós estão prontos para executar trabalhos.

10. **Enviar trabalhos ao cluster**

    Use ferramentas de envio de trabalho HPC Pack para executar trabalhos de cluster. Consulte [Microsoft HPC Pack: gerenciamento de trabalho](http://technet.microsoft.com/library/jj899585.aspx).

11. **Parar (deprovision) os nós**

    Quando terminar de trabalhos em execução, colocar os nós offline e usar a ação **Parar** no HPC Cluster Manager.





## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Cenário 2. Implantar nós de computação em VMs pesados (IaaS)

Neste cenário, você implantar o nó principal HPC Pack e cluster calcular nós em VMs associados a um domínio do Active Directory em uma rede virtual Azure. HPC Pack fornece várias [Opções de implantação do Azure VMs](virtual-machines-linux-hpcpack-cluster-options.md), incluindo scripts de implantação automatizados e modelos de início rápido Azure. Como exemplo, as considerações e etapas abaixo orientação-lo para usar o [script de implantação de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para automatizar a maioria desse processo.

![Cluster em VMs Azure][iaas]



### <a name="steps"></a>Etapas

1. **Criar um nó principal do cluster e calcular nó VMs executando o script de implantação de HPC Pack IaaS em um computador cliente**

    Baixe o pacote de Script de implantação do HPC Pack IaaS no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=49922).

    Para preparar o computador cliente, criar o arquivo de configuração de script e execute o script, consulte [criar um Cluster de HPC com o script de implantação de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md). 
    
    Para implantar habilitados para RDMA nós de computação, observe as seguintes considerações adicionais:
    
    * **Rede Virtual** - especificar uma nova rede virtual em uma região em que o tamanho da instância capaz de RDMA que você deseja usar está disponível.

    * **Sistema operacional Windows Server** - para oferecer suporte à conectividade RDMA, especificar um sistema operacional Windows Server 2012 R2 ou Windows Server 2012 para o nó de computação VMs.

    * **Serviços de nuvem** - recomendamos implantando seu nó principal no serviço de uma nuvem e seus nós de computação em um serviço de nuvem diferentes.

    * **Tamanho de nó de cabeça** - para esse cenário, considere um tamanho de pelo menos A4 (Extra grande) para o nó principal.

    * **Extensão de HpcVmDrivers** - o script de implantação instala o agente de máquina virtual do Azure e a extensão de HpcVmDrivers automaticamente quando você implanta o tamanho A8 ou A9 calcular nós com um sistema operacional Windows Server. HpcVmDrivers instala drivers no nó computação VMs para que eles possam se conectar à rede RDMA.

    * **Configuração de rede de cluster** - o script de implantação configurará automaticamente o cluster HPC Pack na topologia 5 (todos os nós na rede empresarial). Essa topologia é necessária para todas as implantações de cluster HPC Pack em VMs. Não altere a topologia de rede de cluster mais tarde.

2. **Trazer os nós de computação online para executar trabalhos**

    Selecione os nós e usar a ação de **Colocar on-line** no HPC Cluster Manager. Os nós estão prontos para executar trabalhos.

3. **Enviar trabalhos ao cluster**

    Conectar-se para o nó principal para enviar trabalhos, ou configurar um computador local para fazer isso. Para obter informações, consulte [Enviar trabalhos a um cluster HPC no Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).

4. **Colocar os nós offline e parar (desalocar)-los**

    Quando terminar de trabalhos em execução, leve os nós offline no HPC Cluster Manager. Em seguida, use ferramentas de gerenciamento Azure para fechá-los.



## <a name="run-mpi-applications-on-the-cluster"></a>Executar aplicativos MPI no cluster

### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Exemplo: Executar mpipingpong em um cluster de HPC Pack

Para verificar uma implantação HPC Pack das instâncias habilitados para RDMA, execute o comando de **mpipingpong** HPC Pack no cluster. **MPIPingPong** envia pacotes de dados entre pares nós repetidamente para calcular a latência e medidas de produtividade e as estatísticas de rede do aplicativo RDMA habilitado. Este exemplo mostra um padrão típico para executar um trabalho MPI (nesse caso, **mpipingpong**) usando o comando de **mpiexec** cluster.

Este exemplo pressupõe que você adicionou Azure nós em uma configuração de "intermitente no Azure" ([cenário 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-(PaaS) in this article). Se você implantou HPC Pack em um cluster de VMs do Azure, você precisará modificar a sintaxe de comando para especificar um grupo de nó diferente e definir variáveis de ambiente adicionais para direcionar o tráfego de rede à rede RDMA.


Para executar mpipingpong no cluster:


1. No nó principal ou em um computador cliente configurado corretamente, abra um Prompt de comando.

2. Para estimar a latência entre pares de nós em uma implantação do Azure intermitente de 4 nós, digite o seguinte comando para enviar um trabalho para executar mpipingpong com um tamanho pequeno de pacote e um grande número de iterações:

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```

    O comando retorna a identificação do trabalho que foi enviado.

    Se você implantou o cluster HPC Pack implantado em VMs do Azure, especifique um grupo de nó que contém calcular nó VMs implantadas em um serviço de nuvem único e modificar o comando **mpiexec** da seguinte maneira:

    ```
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```

3. Quando o trabalho for concluído, para exibir a saída (nesse caso, o resultado da tarefa 1 do trabalho), digite o seguinte

    ```
    task view <JobID>.1
    ```

    onde &lt; *JobID* &gt; é a ID do trabalho que foi enviado.

    A saída incluirá resultados de latência semelhantes à seguinte.

    ![Latência de pong ping][pingpong1]

4. Para estimar a produtividade entre pares de nós do Azure intermitente, digite o seguinte comando para enviar um trabalho para executar **mpipingpong** com um tamanho de pacote grande e um pequeno número de iterações:

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```

    O comando retorna a identificação do trabalho que foi enviado.

    Em um cluster de HPC Pack implantado em VMs do Azure, modifique o comando conforme indicado na etapa 2.

5. Quando o trabalho for concluído, para exibir a saída (nesse caso, o resultado da tarefa 1 do trabalho), digite o seguinte:

    ```
    task view <JobID>.1
    ```

  A saída incluirá resultados de produtividade similares ao seguinte.

  ![Taxa de transferência do ping pong][pingpong2]


### <a name="mpi-application-considerations"></a>Considerações de aplicativo MPI


A seguir estão as considerações para executar aplicativos MPI com HPC Pack no Azure. Alguns se aplicam apenas às implantações de nós Azure (instâncias de função trabalhador adicionadas em uma configuração de "intermitente no Azure").

* Instâncias de função de trabalho em um serviço de nuvem são periodicamente provisionado novamente sem aviso prévio, Azure (por exemplo, para a manutenção do sistema, ou em caso de que falha de uma instância). Se uma instância está provisionado novamente enquanto executa um trabalho MPI, a instância perde seus dados e retorna ao estado quando ele foi implantado pela primeira vez, que pode causar a falha do trabalho MPI. Os nós mais que você usa para um único trabalho MPI e mais o trabalho é executado, mais provável que uma das instâncias será ser provisionado novamente durante um trabalho está em execução. Você também deve considerar isso se você designar um único nó na implantação como um servidor de arquivos.


* Para executar trabalhos MPI no Azure, você não tem as instâncias RDMA capaz de usar. Você pode usar qualquer tamanho de instância que é suportado pelo HPC Pack. No entanto, as instâncias habilitados para RDMA são recomendadas para executar trabalhos MPI relativamente grande escala que são sensíveis a latência e a largura de banda da rede que conecta os nós. Se você usar outros tamanhos para executar e largura de banda-sensíveis à latência MPI trabalhos, é recomendável executar trabalhos pequenos, na qual uma única tarefa é executado em apenas alguns nós.

* Aplicativos implantados em instâncias do Azure estão sujeitos os termos de licenciamento associados com o aplicativo. Verifique com o fornecedor de qualquer aplicativo comercial para licenciamento ou outras restrições para execução na nuvem. Nem todos os fornecedores oferecem licenciamento flexível.


* Instâncias Azure necessário outra configuração nós do local de acesso, compartilhamentos e servidores de licenças. Por exemplo, para permitir que os nós Azure acessar um servidor de licenças de locais, você pode configurar uma rede virtual Azure de-to-site.


* Para executar aplicativos MPI em instâncias Azure, registre cada aplicativo MPI Firewall do Windows em instâncias executando o comando **hpcfwutil** . Isso permite que as comunicações MPI ocorram em uma porta atribuída dinamicamente pelo firewall.

    >[AZURE.NOTE] Para intermitente para implantações Azure, você também pode configurar um comando de exceção de firewall para executar automaticamente em todos os novos nós Azure que são adicionados ao seu cluster. Depois de executar o comando **hpcfwutil** e verifique se o seu aplicativo funciona, adicione o comando a um script de inicialização para nós do Azure. Para obter mais informações, consulte [usar um Script de inicialização para nós do Azure](https://technet.microsoft.com/library/jj899632.aspx).



* HPC Pack usa a variável de ambiente de cluster CCP_MPI_NETMASK para especificar um intervalo de endereços aceitáveis para comunicação MPI. Iniciando no HPC Pack 2012 R2, a variável de ambiente de cluster CCP_MPI_NETMASK afetará somente a MPI comunicação entre nós de computação de domínio cluster (locais ou em VMs Azure). A variável será ignorada por nós adicionado em um intermitente à configuração Azure.


* Não é possível executar trabalhos MPI nas instâncias Azure que são implantadas em serviços de nuvem diferente (por exemplo, em intermitente para implantações Azure com modelos de nó diferente ou nós de computação de máquina virtual do Azure implantados em vários serviços de nuvem). Se você tiver várias implantações de nó Azure que são iniciadas com modelos de nó diferente, o trabalho MPI deve executar em apenas um conjunto de nós do Azure.


* Quando você adicionar nós Azure a seu cluster e colocá-los online, o serviço de Agendador de trabalho HPC imediatamente tenta iniciar trabalhos em nós. Se apenas uma parte da sua carga de trabalho pode ser executados no Azure, certifique-se de que você atualize ou crie modelos de trabalho para definir quais tipos de trabalho podem ser executados no Azure. Por exemplo, para garantir que trabalhos enviados com um modelo de trabalho é executado somente em nós Azure, adicione a propriedade de grupos de nó ao modelo de trabalho e selecione AzureNodes como o valor necessário. Para criar grupos personalizados para nós do Azure, use o cmdlet do PowerShell do HPC Add-HpcGroup.


## <a name="next-steps"></a>Próximas etapas

* Como uma alternativa para usar o pacote de HPC, desenvolva com o serviço de lote do Azure para executar aplicativos MPI em gerenciado pools de nós de computação no Azure. Consulte [tarefas de várias instâncias de uso para executar aplicativos de Interface de transmissão de mensagens (MPI) em lote do Azure](../batch/batch-mpi.md).

* Se você quiser executar Linux MPI aplicativos que acessam a rede RDMA do Azure, consulte [Configurar um cluster Linux RDMA para executar aplicativos MPI](virtual-machines-linux-classic-rdma-cluster.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/burst.png
[iaas]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/iaas.png
[pingpong1]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong1.png
[pingpong2]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong2.png