<properties
   pageTitle="Introdução ao Azure lote PowerShell | Microsoft Azure"
   description="Obtenha uma rápida introdução às cmdlets do PowerShell do Azure que você pode usar para gerenciar o serviço de lote do Azure"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="10/20/2016"
   ms.author="marsma"/>

# <a name="get-started-with-azure-batch-powershell-cmdlets"></a>Começar a usar cmdlets do PowerShell do Azure em lotes
Com os cmdlets do PowerShell do Azure lote, você pode executar e script muitas das mesmas tarefas que executar com as APIs do lote, o portal do Azure e a Interface de linha de comando (CLI) do Azure. Esta é uma rápida introdução aos cmdlets que você pode usar para gerenciar suas contas de lote e trabalhar com seus recursos de lote como pools, trabalhos e tarefas.

Para obter uma lista completa de cmdlets do lote e sintaxe de cmdlet detalhadas, consulte a [referência de cmdlets do lote do Azure](https://msdn.microsoft.com/library/azure/mt125957.aspx).

Este artigo se baseia em cmdlets do PowerShell do Azure versão 3.0.0. Recomendamos que você atualize seu PowerShell Azure com frequência para tirar proveito das atualizações do serviço e aprimoramentos.

## <a name="prerequisites"></a>Pré-requisitos

Execute as seguintes operações para usar o Azure PowerShell para gerenciar os recursos de lote.

* [Instalar e configurar o PowerShell do Azure](../powershell-install-configure.md)

* Execute o cmdlet **Login AzureRmAccount** para se conectar à sua assinatura (a remessa de cmdlets do Azure lote no módulo Gerenciador de recursos do Azure):

    `Login-AzureRmAccount`

* **Registre-se com o espaço para nome do lote provedor**. Essa operação só precisa ser executada **uma vez por assinatura**.

    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>Gerenciar contas de lote e chaves

### <a name="create-a-batch-account"></a>Criar uma conta de lote

**Novo AzureRmBatchAccount** cria uma conta de lote em um grupo de recursos especificado. Se você ainda não tiver um grupo de recursos, crie um executando o cmdlet [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx) . Especifique uma das regiões Azure no parâmetro **local** , como "Central EUA". Por exemplo:

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

Em seguida, crie uma conta de lote no grupo de recursos, especificando um nome para a conta no <*nome_da_conta*> e o local e o nome do seu grupo de recursos. Criar a conta de lote pode levar algum tempo para ser concluída. Por exemplo:

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [AZURE.NOTE] O nome de conta do lote deve ser exclusivo à região do Azure para o grupo de recursos, conter entre 3 e 24 caracteres e use letras minúsculas e números somente.

### <a name="get-account-access-keys"></a>Obtenha as teclas de acesso de conta
**Get-AzureRmBatchAccountKeys** mostra as teclas de acesso associadas a uma conta de lote do Azure. Por exemplo, execute o seguinte para obter as chaves primárias e secundárias da conta que você criou.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>Gerar uma nova chave de acesso
**New-AzureRmBatchAccountKey** gera uma nova chave primária ou secundária conta para uma conta do Azure lote. Por exemplo, para gerar uma nova chave primária para a sua conta do lote, digite:

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [AZURE.NOTE] Para gerar uma nova chave secundária, especifique "Secundário" para o parâmetro **KeyType** . Você precisa gerar as chaves primárias e secundárias separadamente.

### <a name="delete-a-batch-account"></a>Excluir uma conta de lote
**Remover AzureRmBatchAccount** exclui uma conta de lote. Por exemplo:

    Remove-AzureRmBatchAccount -AccountName <account_name>

Quando solicitado, confirme que você deseja remover a conta. Remoção de conta pode levar algum tempo para ser concluída.

## <a name="create-a-batchaccountcontext-object"></a>Criar um objeto de BatchAccountContext

Para autenticar usando os cmdlets do PowerShell do lote quando você criar e gerenciar pools lote, trabalhos, tarefas e outros recursos, primeiro crie um objeto de BatchAccountContext para armazenar seu nome de conta e chaves:

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Você passa o objeto BatchAccountContext para cmdlets que usam o parâmetro **BatchContext** .

> [AZURE.NOTE] Por padrão, a chave primária da conta é usado para autenticação, mas você pode selecionar explicitamente a chave para usar alterando **KeyInUse** propriedade seu BatchAccountContext objeto: `$context.KeyInUse = "Secondary"`.

## <a name="create-and-modify-batch-resources"></a>Criar e modificar recursos de lote
Use cmdlets como **New-AzureBatchPool**, **New-AzureBatchJob**e **AzureBatchTask de novo** para criar recursos em uma conta de lote. Há são correspondentes **Get -** e **Set -** cmdlets para atualizar as propriedades dos recursos existentes e **Remover -** cmdlets para remover recursos em uma conta de lote.

Ao usar muitos desses cmdlets, além de passar um objeto de BatchContext, você precisa criar ou passar objetos que contêm as configurações do recurso detalhadas, conforme mostrado no exemplo a seguir. Consulte a ajuda detalhada de cada cmdlet para obter exemplos adicionais.

### <a name="create-a-batch-pool"></a>Criar um pool de lote

Ao criar ou atualizar um pool de lote, selecione uma configuração de serviço de nuvem ou uma configuração de máquina virtual para o sistema operacional em nós de computação (consulte [Visão geral do recurso de lote](batch-api-basics.md#pool)). Sua escolha determina se os nós de computação são espelhados com um dos [lançamentos de sistema operacional do Azure convidado](../cloud-services/cloud-services-guestos-update-matrix.md#releases) ou com uma das imagens de Linux ou máquina virtual do Windows com suporte do Azure Marketplace.

Quando você executa **New-AzureBatchPool**, passe as configurações do sistema operacional em um objeto PSCloudServiceConfiguration ou PSVirtualMachineConfiguration. Por exemplo, o cmdlet a seguir cria um novo pool de lote conosco de computação pequena tamanho na configuração de serviço de nuvem, imagem criada com a versão mais recente do sistema operacional da família 3 (Windows Server 2012). Aqui, o parâmetro **CloudServiceConfiguration** Especifica a variável de *$configuration* como o objeto PSCloudServiceConfiguration. O parâmetro **BatchContext** Especifica uma variável definido anteriormente *$context* como o objeto BatchAccountContext.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

O número de destino de nós de computação no novo pool é determinado por uma fórmula de autoscaling. Nesse caso, a fórmula é simplesmente **$TargetDedicated = 4**, indicando o número de nós de computação no pool é 4 no máximo.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Consulta para pools, trabalhos, tarefas e outros detalhes

Use cmdlets como **Get-AzureBatchPool**, **Get-AzureBatchJob**e **Get-AzureBatchTask** a consulta para entidades criadas em uma conta de lote.

### <a name="query-for-data"></a>Consulta de dados

Como exemplo, use **Get-AzureBatchPools** para localizar os pools. Por padrão esse consultas para todos os pools em sua conta, supondo que você já armazenadas no objeto BatchAccountContext em *$context*:

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>Usar um filtro de OData

Você pode fornecer um filtro de OData usando o parâmetro de **filtro** para localizar apenas os objetos que você está interessado. Por exemplo, você pode encontrar todos os pools com ids começando com "myPool":

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

Este método não é tão flexível quanto usando "Where-Object" em um pipeline de local. No entanto, a consulta é enviada para o serviço de lote diretamente para que todos os filtros acontece no lado do servidor, salvando a largura de banda de Internet.

### <a name="use-the-id-parameter"></a>Use o parâmetro de Id

Uma alternativa para um filtro de OData é usar o parâmetro de **Id** . Para consultar um pool específico com id "myPool":

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

O parâmetro **Id** suporta somente pesquisa de id de total, não curingas ou filtros de estilo OData.

### <a name="use-the-maxcount-parameter"></a>Use o parâmetro MaxCount

Por padrão, cada cmdlet retorna um máximo de 1000 objetos. Se você atingir esse limite, ou refinar seu filtro para trazer volta menos objetos ou definir explicitamente máximo usando o parâmetro **MaxCount** . Por exemplo:

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Para remover o limite superior, defina **MaxCount** como 0 ou menos.

### <a name="use-the-powershell-pipeline"></a>Usar o pipeline do PowerShell

Cmdlets de lote pode aproveitar o pipeline de PowerShell para enviar dados entre cmdlets. Isso tem o mesmo efeito que especifica um parâmetro, mas facilita o trabalho com várias entidades.

Por exemplo, localizar e exibir todas as tarefas em sua conta:

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

Reiniciar (reinicializar) cada nó de computação em um pool:

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>Gerenciamento de pacote de aplicativos

Pacotes de aplicativos oferecem uma maneira simplificada para implantar os nós de computação em seus pools de aplicativos. Com os cmdlets do PowerShell do lote, você pode carregar e gerenciar pacotes de aplicativo na sua conta de lote e implantar versões de pacote para nós de computadores.

**Criar** um aplicativo:

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

**Adicionar** um pacote de aplicativos:

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

Defina a **versão padrão** do aplicativo:

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

**Lista de** pacotes de um aplicativo

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

**Excluir** um pacote de aplicativos

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

**Excluir** um aplicativo

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

>[AZURE.NOTE] Você deve excluir todas as versões de pacote de aplicativos de um aplicativo antes de excluir o aplicativo. Você receberá um erro de 'Conflito' se você tentar excluir um aplicativo que atualmente tem pacotes de aplicativos.

### <a name="deploy-an-application-package"></a>Implantar um pacote de aplicativo

Quando você cria um pool, você pode especificar um ou mais pacotes de aplicativo para implantação. Quando você especifica um pacote no momento da criação de pool, ele é implantado em cada nó como o pool de junções de nó. Pacotes também são implantados quando um nó é reiniciado ou recriar a imagem.

Especificar o `-ApplicationPackageReference` opção ao criar um pool para implantar um pacote de aplicativos para nós do pool ao entrarem o pool. Primeiro, crie um objeto de **PSApplicationPackageReference** e configurá-lo com a versão de pacote e Id do aplicativo que você deseja implantar para nós de computação do pool:

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

Agora, criar o pool e especifique o objeto de referência de pacote como o argumento para a `ApplicationPackageReferences` opção:

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

Você pode encontrar mais informações sobre os pacotes de aplicativo na [implantação do aplicativo com pacotes de aplicativos do Azure lote](batch-application-packages.md).

>[AZURE.IMPORTANT] Você deve [vincular uma conta de armazenamento do Azure](#linked-storage-account-autostorage) à sua conta de lote para usar os pacotes do aplicativo.

### <a name="update-a-pools-application-packages"></a>Pacotes de aplicativo de um pool de atualização

Para atualizar os aplicativos atribuídos a um pool existente, primeiro crie um objeto de PSApplicationPackageReference com as propriedades desejadas (versão de identificação e pacote de aplicativo):

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

Em seguida, obtenha o pool do lote, limpar quaisquer pacotes existentes, adicionar nossa nova referência de pacote e atualizar o serviço de lote com as novas configurações do pool:

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

Agora você atualizou propriedades do pool no serviço em lotes. Para realmente implantar o novo pacote de aplicativo para calcular nós no pool, no entanto, você deve reiniciar ou imagem em nós. Você pode reiniciar cada nó em um pool com este comando:

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

>[AZURE.TIP] Você pode implantar vários pacotes de aplicativos para os nós de computação em um pool. Se você quiser *Adicionar* um pacote de aplicativos em vez de substituir os pacotes atualmente implantados, omita a `$pool.ApplicationPackageReferences.Clear()` linha acima.

## <a name="next-steps"></a>Próximas etapas

* Para sintaxe de cmdlet detalhadas e exemplos, consulte [referência de cmdlets do lote do Azure](https://msdn.microsoft.com/library/azure/mt125957.aspx).

* Para obter mais informações sobre aplicativos e pacotes de aplicativos em lote, consulte [implantação do aplicativo com pacotes de aplicativos do Azure lote](batch-application-packages.md).
