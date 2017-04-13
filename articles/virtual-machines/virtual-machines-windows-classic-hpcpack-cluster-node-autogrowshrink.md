<properties
 pageTitle="Nós de cluster de escala automática HPC Pack | Microsoft Azure"
 description="Aumentar e reduzir o número de nós de computação de cluster HPC Pack no Azure automaticamente"
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
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>Aumentar e reduzir os recursos de cluster HPC Pack no Azure acordo com a carga de trabalho de cluster automaticamente




Se você implantar nós do Azure "intermitente" no seu cluster HPC Pack ou se você criar um cluster de HPC Pack no Azure VMs, você pode querer uma maneira automaticamente aumente ou diminua o número de recursos de computação Azure como nós ou cores de acordo com a carga de trabalho atual no cluster. Isso permite que você usar os recursos do Azure com mais eficiência e controlar os custos.
Para fazer isso, configure a propriedade de cluster HPC Pack **AutoGrowShrink**. Como alternativa, execute o script do PowerShell de HPC **AzureAutoGrowShrink.ps1** instalado com HPC Pack.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Além disso, atualmente pode automaticamente apenas aumentar e diminuir nós de computação HPC Pack que executam o sistema operacional Windows Server.

## <a name="set-the-autogrowshrink-cluster-property"></a>Defina a propriedade de cluster AutoGrowShrink

### <a name="prerequisites"></a>Pré-requisitos

* **HPC Pack 2012 R2 atualização 2 ou posterior cluster** - o nó principal do cluster pode ser implantado locais ou em uma máquina virtual do Azure. Consulte [Configurar um cluster híbrida com HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) para começar a usar um nó de cabeça local e nós do Azure "intermitente". Consulte o [script de implantação de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) implantar rapidamente um cluster de HPC Pack em VMs do Azure.


* **Para um cluster com um nó principal no Azure** - se você usar o script de implantação de HPC Pack IaaS para criar o cluster, habilite a propriedade de cluster **AutoGrowShrink** , definindo a opção AutoGrowShrink no arquivo de configuração de cluster. Para obter detalhes, consulte a documentação que acompanha o [download de script](https://www.microsoft.com/download/details.aspx?id=44949). 

    Como alternativa, habilite a propriedade de cluster **AutoGrowShrink** após implantar o cluster usando os comandos de HPC PowerShell descritos na seção a seguir. Para se preparar para isso, primeiro conclua as etapas a seguir:
    1. Configure um certificado de gerenciamento Azure no nó principal e na assinatura do Azure. Para uma implantação de teste que você pode usar o certificado auto-assinado padrão Microsoft HPC Azure HPC Pack instala no nó principal e simplesmente carregar certificado à sua assinatura do Azure. Para obter opções e etapas, consulte a [orientação de biblioteca do TechNet](https://technet.microsoft.com/library/gg481759.aspx).
    2. Execute **regedit** no nó principal, vá para HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo e adicione um novo valor de cadeia de caracteres. Defina o nome do valor para "Impressão digital" e o valor de dados para a impressão digital do certificado na etapa 1.


### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>Comandos do PowerShell do HPC para definir a propriedade AutoGrowShrink

Comandos do PowerShell do HPC para definir **AutoGrowShrink** e ajustar seu comportamento com parâmetros adicionais são exemplo a seguir. Consulte [parâmetros de AutoGrowShrink](#AutoGrowShrink-parameters) posteriormente neste artigo para obter uma lista completa das configurações. 

Para executar esses comandos, inicie HPC PowerShell no nó principal cluster como administrador.

**Habilitar a propriedade de AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 1

**Para desabilitar a propriedade AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 0

**Para alterar o intervalo de crescer em minutos**

    Set-HpcClusterProperty –GrowInterval <interval>

**Para alterar o intervalo de reduzir em minutos**

    Set-HpcClusterProperty –ShrinkInterval <interval>

**Para exibir a configuração atual do AutoGrowShrink**

    Get-HpcClusterProperty –AutoGrowShrink

### <a name="autogrowshrink-parameters"></a>Parâmetros de AutoGrowShrink

A seguir estão AutoGrowShrink parâmetros que você pode modificar usando o comando **Set-HpcClusterProperty** .

* **EnableGrowShrink** - alternar para habilitar ou desabilitar a propriedade **AutoGrowShrink** .
* **ParamSweepTasksPerCore** - número de tarefas de limpar paramétrica crescer um principal. O padrão é a crescer um núcleo por tarefa. 
 
    >[AZURE.NOTE] HPC Pack QFE KB3134307 altera **ParamSweepTasksPerCore** para **TasksPerResourceUnit**. Ele se baseia no tipo de recurso de trabalho e pode ser nó, soquete ou core.
    
* **GrowThreshold** - limite de tarefas na fila disparar aumento automático. O padrão é 1, o que significa que se houver 1 ou mais tarefas no estado enfileirado, aumentar automaticamente nós.
* **GrowInterval** - intervalo em minutos para acionar aumento automático. O intervalo padrão é 5 minutos.
* **ShrinkInterval** - intervalo em minutos para acionar a redução automática. O intervalo padrão é 5 minutos. |
* **ShrinkIdleTimes** - número de cheques contínuos a reduzir para indicar que os nós são ociosos. O padrão é 3 vezes. Por exemplo, se o **ShrinkInterval** é de 5 minutos, HPC Pack verifica se o nó é ocioso cada 5 minutos. Se os nós estão no estado ocioso após 3 contínuo verifica (15 minutos), o HPC Pack reduz o nó.
* **ExtraNodesGrowRatio** - adicional porcentagem de nós crescer para trabalhos de Interface de transmissão de mensagens (MPI). O valor padrão é 1, o que significa que HPC Pack crescer nós 1% para trabalhos MPI. 
* **GrowByMin** - alternar para indicar se a política de crescimento automático é baseada nos recursos mínimos necessários para o trabalho. O padrão é falso, o que significa que o pacote de HPC crescer nós para trabalhos com base no máximos recursos necessários para os trabalhos.
* **SoaJobGrowThreshold** - limite SOA de solicitações de entrada para acionar o automático crescer processo. O valor padrão é 50000.  
    
    >[AZURE.NOTE] Este parâmetro é suportado iniciando no HPC Pack 2012 R2 atualização 3.
    
* **SoaRequestsPerCore** -número da entrada SOA solicitações para expandir um principal. O valor padrão é 20.000.  

    >[AZURE.NOTE] Este parâmetro é suportado iniciando no HPC Pack 2012 R2 atualização 3.

### <a name="mpi-example"></a>Exemplo MPI

Por padrão HPC Pack crescer 1% nós extras para trabalhos MPI (**ExtraNodesGrowRatio** está definido como 1). O motivo é que MPI pode exigir vários nós e o trabalho pode ser executado somente quando todos os nós estiver prontos. Quando o Azure inicia nós, ocasionalmente um nó talvez seja necessário mais tempo para iniciar de outras pessoas, causando outros nós ficar ocioso enquanto aguarda nó preparar. Por aumentando nós extras, HPC Pack reduz o tempo de espera este recurso e potencialmente economiza custos. Para aumentar a porcentagem de nós extras para trabalhos MPI (por exemplo, para 10%), executar um comando semelhante a

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>Exemplo SOA

Por padrão, **SoaJobGrowThreshold** está definido como 50000 e **SoaRequestsPerCore** está definida como 200000. Se você enviar um trabalho SOA com 70000 solicitações, haverá uma tarefa na fila e solicitações de entrada são 70000. Neste caso crescer HPC Pack 1 núcleo da tarefa na fila e solicitações de entrada, crescerá (70000-50000) / core 20000 = 1, portanto total crescerá 2 cores para este trabalho SOA.

## <a name="run-the-azureautogrowshrinkps1-script"></a>Execute o script AzureAutoGrowShrink.ps1

### <a name="prerequisites"></a>Pré-requisitos

* **HPC Pack 2012 R2 Update 1 ou posterior cluster** - o script **AzureAutoGrowShrink.ps1** está instalado na pasta bin % CCP_HOME %. O nó principal do cluster pode ser implantado locais ou em uma máquina virtual do Azure. Consulte [Configurar um cluster híbrida com HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) para começar a usar um nó de cabeça local e nós do Azure "intermitente". Consulte o [script de implantação de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) implantar rapidamente um cluster de HPC Pack no Azure VMs ou usar um [modelo de início rápido Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).

* **Azure PowerShell 0.8.12** - o script atualmente depende essa versão específica do PowerShell do Azure. Se você estiver executando uma versão posterior no nó principal, você talvez precise fazer downgrade do PowerShell do Azure para [versão 0.8.12](http://az412849.vo.msecnd.net/downloads03/azure-powershell.0.8.12.msi) para executar o script. 

* **Para um cluster conosco intermitente Azure** - execute o script em um computador cliente onde HPC Pack está instalado, ou no nó principal. Se executando em um computador cliente, certifique-se de que você definir a variável $env: CCP_SCHEDULER corretamente para apontar para o nó principal. Nós do Azure "intermitente" já devem ser adicionados ao cluster, mas eles podem ser no estado implantado não.


* **Para um cluster implantado em VMs Azure** - execute o script no nó principal máquina virtual, porque ela depende do **HpcIaaSNode.ps1 de início** e **Parada HpcIaaSNode.ps1** scripts que são instalados lá. Além disso, esses scripts exigem um certificado de gerenciamento Azure ou publicar o arquivo de configurações (consulte [Gerenciar calcular nós em um cluster de HPC Pack no Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)). Verifique se todos o nó de computação VMs necessárias já são adicionados ao cluster. Eles podem ser no estado interrompido.

### <a name="syntax"></a>Sintaxe

```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
### <a name="parameters"></a>Parâmetros

 * **NodeTemplates** - nomes dos modelos de nó para definir o escopo para os nós ampliar ou reduzir. Se não especificado (o valor padrão é @()), todos os nós no grupo de nó **AzureNodes** estão no escopo quando **NodeType** tem um valor de AzureNodes e todos os nós no grupo de nó **ComputeNodes** estão no escopo quando **NodeType** tem um valor de ComputeNodes.

 * **JobTemplates** - nomes dos modelos de trabalho para definir o escopo para os nós a crescer.

 * **NodeType** - o tipo de nó para ampliar ou reduzir. Valores com suporte são:

     * **AzureNodes** – para nós do Azure PaaS (intermitente) em um local ou Azure IaaS cluster.

     * **ComputeNodes** - para nó de computação VMs apenas em um cluster de IaaS do Azure.

* **NumOfQueuedJobsPerNodeToGrow** - número de trabalhos enfileirados necessários para expandir um nó.

* **NumOfQueuedJobsToGrowThreshold** - o número de limite de trabalhos enfileirados para iniciar o processo de crescer.

* **NumOfActiveQueuedTasksPerNodeToGrow** - o número de tarefas ativas de enfileirados necessários para expandir um nó. Se **NumOfQueuedJobsPerNodeToGrow** for especificado com um valor maior do que 0, esse parâmetro será ignorado.

* **NumOfActiveQueuedTasksToGrowThreshold** - o número de limite de tarefas ativas na fila para iniciar o processo de crescer.

* **NumOfInitialNodesToGrow** - o número mínimo inicial de nós crescer se todos os nós no escopo for **Implantado não** ou **parado (Deallocated)**.

* **GrowCheckIntervalMins** - o intervalo em minutos entre as verificações para crescer.

* **ShrinkCheckIntervalMins** - o intervalo em minutos entre as verificações para reduzir.

* **ShrinkCheckIdleTimes** - o número de contínua reduzir verificações (separadas por **ShrinkCheckIntervalMins**) para indicar os nós são ociosos.

* **UseLastConfigurations** - as configurações anteriores salvas no arquivo argumento.

* **ArgFile**- o nome do arquivo do argumento usado para salvar e atualizar as configurações para executar o script.

* **LogFilePrefix** - o nome do prefixo do arquivo de log. Você pode especificar um caminho. Por padrão, o log é gravado para a pasta de trabalho atual.

### <a name="example-1"></a>Exemplo 1

O exemplo a seguir configura os nós intermitente Azure implantados com o modelo de AzureNode padrão para ampliar ou reduzir automaticamente. Se todos os nós são inicialmente no estado **Não implantado** , pelo menos 3 nós serão iniciadas. Se o número de trabalhos enfileirados exceder 8, o script começa nós até seu número excede a proporção da trabalhos enfileirados para **NumOfQueuedJobsPerNodeToGrow**. Se um nó for encontrado ficar ocioso no 3 vezes ociosos consecutivas, ela é interrompida.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>Exemplo 2

O exemplo a seguir configura o nó de computação Azure VMs implantadas com o modelo de ComputeNode padrão para ampliar ou reduzir automaticamente.
Os trabalhos configurados pelo modelo de trabalho padrão definem o escopo da carga de trabalho no cluster. Se todos os nós são inicialmente interrompidos, pelo menos 5 nós são iniciadas. Se o número de tarefas na fila ativas exceder 15, o script começa nós até seu número excede a proporção de tarefas ativas na fila para **NumOfActiveQueuedTasksPerNodeToGrow**. Se um nó for encontrado ficar ocioso em 10 vezes ociosos consecutivas, ela é interrompida.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```
