<properties
    pageTitle="Usando o PowerShell Azure com o armazenamento do Azure | Microsoft Azure"
    description="Saiba como usar os cmdlets do PowerShell do Azure para o armazenamento do Azure para criar e gerenciar contas de armazenamento; trabalhar com tabelas, blobs, filas e arquivos; Configurar a análise de armazenamento de consulta e criar assinaturas de acesso compartilhado."
    services="storage"
    documentationCenter="na"
    authors="robinsh"
    manager="carmonm"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="using-azure-powershell-with-azure-storage"></a>Usando o PowerShell Azure com o armazenamento do Azure

## <a name="overview"></a>Visão geral

PowerShell Azure é um módulo que fornece cmdlets para gerenciar Azure através do Windows PowerShell. É um shell de linha de comando baseado em tarefas e a linguagem de script projetado especialmente para administração do sistema. Com o PowerShell, você pode facilmente controlar e automatizar a administração de serviços do Azure e aplicativos. Por exemplo, você pode usar os cmdlets para realizar as mesmas tarefas que você pode executar por meio do [Portal do Azure](https://portal.azure.com).

Neste guia, vamos explorar como usar os [Cmdlets de armazenamento do Azure](https://msdn.microsoft.com/library/azure/mt269418.aspx) para realizar uma variedade de tarefas de desenvolvimento e administração com o armazenamento do Azure.

Este guia pressupõe que você tenha experiência anterior usando o [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/) e o [Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx). Guia fornece um número de scripts para demonstrar o uso do PowerShell com o armazenamento do Azure. Você deve atualizar as variáveis de script com base em sua configuração antes de executar cada script.

A primeira seção deste guia fornece uma olhada rápida em armazenamento do Azure e PowerShell. Para obter informações detalhadas e instruções, inicie dos [pré-requisitos para usar o PowerShell Azure com o armazenamento do Azure](#prerequisites-for-using-azure-powershell-with-azure-storage).


## <a name="getting-started-with-azure-storage-and-powershell-in-5-minutes"></a>Introdução ao armazenamento do Azure e PowerShell em 5 minutos

Esta seção mostra como acessar o armazenamento do Azure por meio do PowerShell em 5 minutos.

**Experiência com o Azure:** Obtenha uma assinatura do Microsoft Azure e uma conta da Microsoft associada a essa assinatura. Para obter informações sobre as opções de compra do Azure, consulte [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/), [Opções de compra](https://azure.microsoft.com/pricing/purchase-options/)e [Membro oferece](https://azure.microsoft.com/pricing/member-offers/) (para membros do MSDN, Microsoft Partner Network, BizSpark e outros programas da Microsoft).

Consulte [Atribuindo funções de administrador do Azure Active Directory (AD Azure)](https://msdn.microsoft.com/library/azure/hh531793.aspx) para obter mais informações sobre as assinaturas do Azure.

**Depois de criar uma assinatura do Microsoft Azure e conta:**

1.  Baixe e instale [O PowerShell do Azure](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
2.  Iniciar Windows PowerShell script ambiente integrado (ISE): No seu computador local, vá para o menu **Iniciar** . Digite **Ferramentas administrativas** e clique em para executá-la. Na janela **Ferramentas administrativas** , **O Windows PowerShell ISE**de atalho, clique em **Executar como administrador**.
3.  No **Windows PowerShell ISE**, clique em **arquivo** > **novo** para criar um novo arquivo de script.
4.  Agora, você terá um script simples que mostra os comandos básicos do PowerShell para acessar o armazenamento do Azure. O script perguntará primeiro o Azure credenciais de conta para adicionar seu Azure conta para o ambiente do PowerShell local. Em seguida, o script será definir o padrão de assinatura do Azure e criar uma nova conta de armazenamento no Azure. Em seguida, o script irá criar um novo recipiente nessa nova conta de armazenamento e carregar um arquivo de imagem existente (blob) no contêiner. Após o script todos os blobs no contêiner de listas, ele irá criar uma nova pasta de destino no computador local e baixar o arquivo de imagem.
5.  Na seção de código a seguir, selecione o script entre os comentários **#begin** e **#end**. Pressione CTRL + C para copiá-lo para a área de transferência.

        #begin
        # Update with the name of your subscription.
        $SubscriptionName = "YourSubscriptionName"

        # Give a name to your new storage account. It must be lowercase!
        $StorageAccountName = "yourstorageaccountname"

        # Choose "West US" as an example.
        $Location = "West US"

        # Give a name to your new container.
        $ContainerName = "imagecontainer"

        # Have an image file and a source directory in your local computer.
        $ImageToUpload = "C:\Images\HelloWorld.png"

        # A destination directory in your local computer.
        $DestinationFolder = "C:\DownloadImages"

        # Add your Azure account to the local PowerShell environment.
        Add-AzureAccount

        # Set a default Azure subscription.
        Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

        # Create a new storage account.
        New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $Location

        # Set a default storage account.
        Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

        # Create a new container.
        New-AzureStorageContainer -Name $ContainerName -Permission Off

        # Upload a blob into a container.
        Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

        # List all blobs in a container.
        Get-AzureStorageBlob -Container $ContainerName

        # Download blobs from the container:
        # Get a reference to a list of all blobs in a container.
        $blobs = Get-AzureStorageBlob -Container $ContainerName

        # Create the destination directory.
        New-Item -Path $DestinationFolder -ItemType Directory -Force  

        # Download blobs into the local destination directory.
        $blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder
        #end

5.  No **Windows PowerShell ISE**, pressione CTRL + V para copiar o script. Clique em **arquivo** > **Salvar**. Na janela de diálogo **Salvar como** , digite o nome do arquivo de script, como "mystoragescript". Clique em **Salvar**.

6.  Agora, você precisa atualizar as variáveis de script com base em suas configurações. Você deve atualizar a variável **$SubscriptionName** com sua própria assinatura. Você pode manter outras variáveis conforme especificado no script ou atualizá-los conforme desejar.

    - **$SubscriptionName:** Você deve atualizar esta variável com seu próprio nome de assinatura. Siga um destes três maneiras para localizar o nome da sua assinatura:

        a. No **Windows PowerShell ISE**, clique em **arquivo** > **novo** para criar um novo arquivo de script. Copie o seguinte script para o novo arquivo de script e clique em **Depurar** > **Executar**. O seguinte script perguntará primeiro o Azure credenciais de conta para adicionar seu Azure conta para o ambiente do PowerShell local e, em seguida, Mostrar todas as assinaturas que estão conectadas à sessão do PowerShell local. Anote o nome da assinatura que você deseja usar ao seguindo este tutorial:

            Add-AzureAccount
                Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName

        b. Para localizar e copiar seu nome de assinatura no [Portal do Azure](https://portal.azure.com), no menu Hub à esquerda, clique em **assinaturas**. Copie o nome da assinatura que você deseja usar ao executar os scripts neste guia.

        ![Portal do Azure][Image2]

        c. Para localizar e copiar seu nome de assinatura no [Portal de clássico do Azure](https://manage.windowsazure.com/), role para baixo e clique em **configurações** no lado esquerdo do portal. Clique em **assinaturas** para ver uma lista das suas assinaturas. Copie o nome da assinatura que você deseja usar ao executar os scripts fornecidos neste guia.

        ![Azure Portal clássico][Image1]

    - **$StorageAccountName:** Use o nome fornecido no script ou insira um novo nome para a sua conta de armazenamento. **Importante:** O nome da conta de armazenamento deve ser exclusivo no Azure. Ele deve ser minúsculo, demais!

    - **$Location:** Usar determinado "Oeste EUA" no script ou escolha outros locais Azure, como EUA Leste, Norte da Europa e assim por diante.

    - **$ContainerName:** Use o nome fornecido no script ou digite um novo nome para seu contêiner.

    - **$ImageToUpload:** Digite um caminho para uma imagem em seu computador local, como: "C:\Images\HelloWorld.png".

    - **$DestinationFolder:** Digite um caminho para um diretório local para armazenar os arquivos baixados do armazenamento do Azure, como: "C:\DownloadImages".

7.  Depois de atualizar as variáveis de script no arquivo "mystoragescript.ps1", clique em **arquivo** > **Salvar**. Clique em **Depurar** > **Executar** ou pressione **F5** para executar o script.  

Após o script ser executado, você deve ter uma pasta de destino local que inclui o arquivo de imagem baixado. A captura de tela a seguir mostra um exemplo de saída:

![Baixar Blobs][Image3]


> [AZURE.NOTE] A seção "Introdução ao armazenamento do Azure e PowerShell em 5 minutos" fornecido uma rápida introdução sobre como usar o PowerShell do Azure com o armazenamento do Azure. Para obter informações detalhadas e instruções, recomendamos que você leia as seções a seguir.

## <a name="prerequisites-for-using-azure-powershell-with-azure-storage"></a>Pré-requisitos para usar o PowerShell do Azure com armazenamento do Azure
Você precisará de uma assinatura do Azure e conta para executar cmdlets do PowerShell fornecido neste guia, conforme descrito acima.

PowerShell Azure é um módulo que fornece cmdlets para gerenciar Azure através do Windows PowerShell. Para obter informações sobre como instalar e configurar o Azure PowerShell, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Recomendamos que você baixar e instala ou atualizar para o módulo do PowerShell do Azure mais recente antes de usar este guia.

Você pode executar os cmdlets no console do Windows PowerShell padrão ou o Windows PowerShell Integrated Scripting ambiente (ISE). Por exemplo, para abrir **O Windows PowerShell ISE**, vá para o menu Iniciar, digite ferramentas administrativas e clique em para executá-la. Na janela Ferramentas administrativas, o Windows PowerShell ISE de atalho, clique em Executar como administrador.

## <a name="how-to-manage-storage-accounts-in-azure"></a>Como gerenciar contas de armazenamento no Azure

### <a name="how-to-set-a-default-azure-subscription"></a>Como definir um padrão de assinatura do Azure
Para gerenciar o armazenamento do Azure usando o PowerShell do Azure, você precisará autenticar seu ambiente do cliente com o Azure via autenticação do Azure Active Directory ou a autenticação baseada em certificado. Para obter informações detalhadas, consulte [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) tutorial. Este guia usa a autenticação do Active Directory do Azure.

1.  No Windows PowerShell ISE, digite o seguinte comando para adicionar o Azure conta para o ambiente local do PowerShell:

    `Add-AzureAccount`

2.  Na janela "Sinal ao Microsoft Azure", digite o endereço de email e a senha associada a sua conta. Azure autentica salva as informações de credencial e fecha a janela.

3.  Em seguida, execute o seguinte comando para exibir as contas Azure no seu ambiente local do PowerShell e verifique se a sua conta está listada:

    `Get-AzureAccount`

4.  Em seguida, execute o seguinte cmdlet para exibir todas as assinaturas que estão conectadas à sessão do PowerShell local e verifique se a sua assinatura está listada:

    `Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`

5.  Para definir um padrão de assinatura do Azure, execute o cmdlet Select-AzureSubscription:

        $SubscriptionName = 'Your subscription Name'
        Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

6.  Verifique se o nome da assinatura padrão executando o cmdlet Get-AzureSubscription:

    `Get-AzureSubscription -Default`

7.  Para ver todos os cmdlets do PowerShell disponíveis para o armazenamento do Azure, execute:

    `Get-Command -Module Azure -Noun *Storage*`

### <a name="how-to-create-a-new-azure-storage-account"></a>Como criar uma nova conta de armazenamento do Azure
Para usar o armazenamento do Azure, você precisará de uma conta de armazenamento. Você pode criar uma nova conta de armazenamento do Azure após você ter configurado seu computador para se conectar à sua assinatura.

1.  Execute o cmdlet Get-AzureLocation para encontrar todos os locais de análise disponíveis:

    `Get-AzureLocation | Format-Table -Property Name, AvailableServices, StorageAccountTypes`

2.  Em seguida, execute o cmdlet New-AzureStorageAccount para criar uma nova conta de armazenamento. O exemplo a seguir cria uma nova conta de armazenamento no data center "Oeste EUA".

        $location = "West US"
        $StorageAccountName = "yourstorageaccount"
        New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $location

> [AZURE.IMPORTANT] O nome da sua conta de armazenamento deve ser exclusivo dentro do Azure e deve ser minúsculo. Para convenções de nomenclatura e restrições, consulte [Sobre contas de armazenamento do Azure](storage-create-storage-account.md) e [nomeação e referenciando contêineres, Blobs e metadados](http://msdn.microsoft.com/library/azure/dd135715.aspx).

### <a name="how-to-set-a-default-azure-storage-account"></a>Como configurar uma conta de armazenamento do Azure padrão
Você pode ter várias contas de armazenamento em sua assinatura. Você pode escolher um deles e defini-lo como a conta de armazenamento padrão para todos os comandos de armazenamento na mesma sessão do PowerShell. Isso permite executar os comandos de armazenamento do Azure PowerShell sem especificar o contexto de armazenamento explicitamente.

1.  Para definir uma conta de armazenamento padrão para sua assinatura, você pode executar o cmdlet Set-AzureSubscription.

        $SubscriptionName = "Your subscription name"
        $StorageAccountName = "yourstorageaccount"  
        Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

2.  Em seguida, execute o cmdlet Get-AzureSubscription para verificar se a conta de armazenamento está associada a sua conta de assinatura padrão. Este comando retorna as propriedades de inscrição na assinatura atual, incluindo sua conta de armazenamento atual.

        Get-AzureSubscription –Current

### <a name="how-to-list-all-azure-storage-accounts-in-a-subscription"></a>Como listar todas as contas de armazenamento do Azure em uma assinatura
Cada assinatura Azure pode ter até 100 contas de armazenamento. Para obter as informações mais atualizadas sobre os limites, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).

Execute o seguinte cmdlet para descobrir o nome e o status das contas de armazenamento na assinatura atual:

    Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus

### <a name="how-to-create-an-azure-storage-context"></a>Como criar um contexto de armazenamento do Azure
Contexto de armazenamento do Azure é um objeto do PowerShell para encapsular as credenciais de armazenamento. Usando um contexto de armazenamento enquanto executando qualquer cmdlet subsequente permite autenticar sua solicitação sem especificar a conta de armazenamento e sua chave de acesso explicitamente. Você pode criar um contexto de armazenamento de várias maneiras, como usando chave de acesso e nome da conta de armazenamento, o token de assinatura (SAS) de acesso compartilhado, a cadeia de conexão, ou anônima. Para obter mais informações, consulte [AzureStorageContext de novo](http://msdn.microsoft.com/library/azure/dn806380.aspx).  

Use uma das seguintes três maneiras para criar um contexto de armazenamento:

- Execute o cmdlet [Get-AzureStorageKey](http://msdn.microsoft.com/library/azure/dn495235.aspx) para descobrir a tecla de acesso do armazenamento principal para a sua conta de armazenamento do Azure. Em seguida, chame o cmdlet [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) para criar um contexto de armazenamento:

        $StorageAccountName = "yourstorageaccount"
        $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
        $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary


- Gerar um token de assinatura de acesso compartilhado para um contêiner de armazenamento do Azure e usá-lo para criar um contexto de armazenamento:

        $sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
        $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken

    Para obter mais informações, consulte [ [New-AzureStorageContainerSASToken](http://msdn.microsoft.com/library/azure/dn806416.aspx) usando compartilhados acesso assinaturas (Associações](storage-dotnet-shared-access-signature-part-1.md)e).

- Em alguns casos, você talvez queira especificar os pontos de extremidade do serviço quando você cria um novo contexto de armazenamento. Isso pode ser necessário quando você ter registrado um nome de domínio personalizado para sua conta de armazenamento com o serviço de Blob ou que você deseja usar uma assinatura de acesso compartilhado para acessar os recursos de armazenamento. Defina os pontos de extremidade do serviço em uma cadeia de conexão e usá-lo para criar um novo contexto de armazenamento, conforme mostrado abaixo:

        $ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
        $Ctx = New-AzureStorageContext -ConnectionString $ConnectionString

Para obter mais informações sobre como configurar uma cadeia de conexão de armazenamento, consulte [Configurando cadeias de caracteres de Conexão](storage-configure-connection-string.md).

Agora que você tiver configurado seu computador e aprendeu gerenciar contas de armazenamento usando o PowerShell do Azure e assinaturas, vá para a próxima seção para saber como gerenciar blobs Azure e blob instantâneos.

### <a name="how-to-retrieve-and-regenerate-azure-storage-keys"></a>Como recuperar e gerar chaves de armazenamento do Azure

Uma conta de armazenamento do Azure vem com duas chaves de conta. Você pode usar o cmdlet a seguir para recuperar suas chaves.

    Get-AzureStorageKey -StorageAccountName "yourstorageaccount"

Use o cmdlet a seguir para recuperar uma chave específica. Valores válidos são primário e secundário.  

    (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary

    (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Secondary

Se você gostaria de gerar suas chaves, use o cmdlet a seguir. Valores válidos para - KeyType são "Principal" e "Secundário"

    New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Primary”

    New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Secondary”

## <a name="how-to-manage-azure-blobs"></a>Como gerenciar blobs Azure
Armazenamento de Blob do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, como dados binários ou texto, que podem ser acessados a partir de qualquer lugar do mundo via HTTP ou HTTPS. Esta seção pressupõe que você já esteja familiarizado com os conceitos de serviço de armazenamento de Blob do Azure. Para obter informações detalhadas, consulte [Introdução ao armazenamento de Blob usando .NET](storage-dotnet-how-to-use-blobs.md) e [Conceitos de serviços de Blob](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="how-to-create-a-container"></a>Como criar um contêiner
Cada blob no armazenamento do Azure deve estar em um contêiner. Você pode criar um contêiner particular usando o cmdlet New-AzureStorageContainer:

    $StorageContainerName = "yourcontainername"
    New-AzureStorageContainer -Name $StorageContainerName -Permission Off

> [AZURE.NOTE] Há três níveis de acesso de leitura anônimo: **Desativar** **Blob**e **contêiner**. Para impedir o acesso anônimo em blobs, defina o parâmetro de permissão para **desativado**. Por padrão, o novo contêiner é particular e pode ser acessado somente pelo proprietário da conta. Para permitir acesso de leitura público anônimo blob recursos, mas não para metadados de contêiner ou lista de blobs no contêiner, defina o parâmetro de permissão ao **Blob**. Para permitir o acesso de leitura público completa para recursos, metadados de contêiner e a lista de blobs no contêiner de blob, defina o parâmetro de permissão ao **contêiner**. Para obter mais informações, consulte [Gerenciar acesso anônimo de leitura para contêineres e blobs](storage-manage-access-to-resources.md).

### <a name="how-to-upload-a-blob-into-a-container"></a>Como carregar um blob em um contêiner
Armazenamento de Blob do Azure suporta blobs de bloco e blobs de página. Para obter mais informações, consulte [Noções básicas sobre bloco Blobs, BLobs acrescentar e Blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Para carregar blobs em um contêiner, você pode usar o cmdlet [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) . Por padrão, este comando carrega os arquivos locais para um blob de bloco. Para especificar o tipo de blob, você pode usar o parâmetro - BlobType.

O exemplo a seguir executa o cmdlet [Get-ChildItem](http://technet.microsoft.com/library/hh849800.aspx) para acessar todos os arquivos na pasta especificada e passa-os para o próximo cmdlet usando o operador de pipeline. O cmdlet [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) carrega os arquivos locais para o recipiente:

    Get-ChildItem –Path C:\Images\* | Set-AzureStorageBlobContent -Container "yourcontainername"

### <a name="how-to-download-blobs-from-a-container"></a>Como baixar blobs de um contêiner
O exemplo a seguir demonstra como baixar blobs de um contêiner. O exemplo primeiro estabelece uma conexão para o armazenamento do Azure usando o contexto de conta de armazenamento, que inclui o nome da conta de armazenamento e sua chave primária de acesso. Em seguida, o exemplo recupera uma referência de blob usando o cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) . Em seguida, o exemplo usa o cmdlet [Get-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806418.aspx) para baixar blobs na pasta de destino local.

    #Define the variables.
    $ContainerName = "yourcontainername"
    $DestinationFolder = "C:\DownloadImages"

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #List all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Download blobs from a container.
    New-Item -Path $DestinationFolder -ItemType Directory -Force
    $blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx

### <a name="how-to-copy-blobs-from-one-storage-container-to-another"></a>Como copiar blobs de um contêiner de armazenamento para outro
Você pode copiar blobs assíncrona entre regiões e contas de armazenamento. O exemplo a seguir demonstra como copiar blobs de um contêiner de armazenamento para outro em duas contas de armazenamento diferentes. O exemplo primeiro define variáveis para contas de armazenamento de origem e destino e, em seguida, cria um contexto de armazenamento para cada conta. Em seguida, o exemplo copia blobs do contêiner de origem ao contêiner de destino usando o cmdlet [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) . O exemplo supõe que as contas de armazenamento de origem e destino e contêineres já existem.

    #Define the source storage account and context.
    $SourceStorageAccountName = "yoursourcestorageaccount"
    $SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
    $SrcContainerName = "yoursrccontainername"
    $SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey

    #Define the destination storage account and context.
    $DestStorageAccountName = "yourdeststorageaccount"
    $DestStorageAccountKey = "Storage key for yourdeststorageaccount"
    $DestContainerName = "destcontainername"
    $DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey

    #Get a reference to blobs in the source container.
    $blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext

    #Copy blobs from one container to another.
    $blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext

Observe que este exemplo executa uma cópia assíncrona. Você pode monitorar o status de cada cópia executando o cmdlet [Get-AzureStorageBlobCopyState](http://msdn.microsoft.com/library/azure/dn806406.aspx) .

### <a name="how-to-copy-blobs-from-a-secondary-location"></a>Como copiar blobs de um local secundário
Você pode copiar blobs do local secundário de uma conta de ar GRS-habilitados.

    #define secondary storage context using a connection string constructed from secondary endpoints.
    $SrcContext = New-AzureStorageContext -ConnectionString "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***;BlobEndpoint=http://***-secondary.blob.core.windows.net;FileEndpoint=http://***-secondary.file.core.windows.net;QueueEndpoint=http://***-secondary.queue.core.windows.net; TableEndpoint=http://***-secondary.table.core.windows.net;"
    Start-AzureStorageBlobCopy –Container *** -Blob *** -Context $SrcContext –DestContainer *** -DestBlob *** -DestContext $DestContext

### <a name="how-to-delete-a-blob"></a>Como excluir um blob
Para excluir um blob, primeiro obter uma referência de blob e, em seguida, chamar o cmdlet remover AzureStorageBlob nele. O exemplo a seguir exclui todos os blobs em um determinado contêiner. O exemplo primeiro define variáveis para uma conta de armazenamento e, em seguida, cria um contexto de armazenamento. Em seguida, o exemplo recupera uma referência de blob usando o cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) e executa o cmdlet [AzureStorageBlob remover](http://msdn.microsoft.com/library/azure/dn806399.aspx) para remover blobs de um contêiner no armazenamento do Azure.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "containername"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to all the blobs in the container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Delete blobs in a specified container.
    $blobs| Remove-AzureStorageBlob

## <a name="how-to-manage-azure-blob-snapshots"></a>Como gerenciar instantâneos de blob do Microsoft Azure
Azure permite que você crie um instantâneo de um blob. Um instantâneo é uma versão somente leitura de um blob executada em um ponto no tempo. Depois que um instantâneo tiver sido criado, ele pode ser ler, copiado, ou excluído, mas não modificado. Instantâneos oferecem uma maneira de fazer backup de um blob como aparece em um momento específico. Para obter mais informações, consulte [Criando um instantâneo de um Blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).

### <a name="how-to-create-a-blob-snapshot"></a>Como criar um instantâneo de blob
Para criar um instantâneo de um blob, primeiro obter uma referência de blob e ligue o `ICloudBlob.CreateSnapshot` método nele. O exemplo a seguir primeiro define variáveis para uma conta de armazenamento e, em seguida, cria um contexto de armazenamento. Em seguida, o exemplo recupera uma referência de blob usando o cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) e executa o método [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) para criar um instantâneo.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "yourcontainername"
    $BlobName = "yourblobname"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName

    #Create a snapshot of the blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

### <a name="how-to-list-a-blobs-snapshots"></a>Como listar instantâneos de um blob
Você pode criar quantas instantâneos como você deseja para um blob. Você pode listar os instantâneos associados ao seu blob para controlar sua instantâneos atuais. O exemplo a seguir usa um blob predefinido e chama o cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) para listar os instantâneos dos blob.  

    #Define the blob name.
    $BlobName = "yourblobname"

    #List the snapshots of a blob.
    Get-AzureStorageBlob –Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }

### <a name="how-to-copy-a-snapshot-of-a-blob"></a>Como copiar um instantâneo de um blob
Você pode copiar um instantâneo de um blob para restaurar o instantâneo. Para obter informações detalhadas e restrições, consulte [Criando um instantâneo de um Blob](http://msdn.microsoft.com/library/azure/hh488361.aspx). O exemplo a seguir primeiro define variáveis para uma conta de armazenamento e, em seguida, cria um contexto de armazenamento. Em seguida, o exemplo define as variáveis de nome contêiner e blob. O exemplo recupera uma referência de blob usando o cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) e executa o método [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) para criar um instantâneo. Em seguida, o exemplo executa o cmdlet [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) para copiar o instantâneo de um blob usando o objeto ICloudBlob para o blob de origem. Certifique-se de atualizar as variáveis com base em sua configuração antes de executar o exemplo. Observe que o exemplo a seguir pressupõe que a fonte e contêineres de destino e o blob de origem já existem.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Define the variables.
    $SrcContainerName = "yoursourcecontainername"
    $DestContainerName = "yourdestcontainername"
    $SrcBlobName = "yourblobname"
    $DestBlobName = "CopyBlobName"

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName

    #Create a snapshot of a blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

    #Copy the snapshot to another container.
    Start-AzureStorageBlobCopy –Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName

Agora que você aprendeu como gerenciar blobs Azure e blob instantâneos com PowerShell do Azure, vá para a próxima seção para saber como gerenciar arquivos, filas e tabelas.

## <a name="how-to-manage-azure-tables-and-table-entities"></a>Como gerenciar tabelas do Azure e entidades de tabela
Serviço de armazenamento de tabela Azure é um armazenamento de dados NoSQL, que você pode usar para armazenar e grandes conjuntos de dados estruturados e não-relacionais de consulta. Os componentes principais do serviço são tabelas, entidades e propriedades. Uma tabela é uma coleção de entidades. Uma entidade é um conjunto de propriedades. Cada entidade pode ter até 252 propriedades, que são todos os pares de valor de nome. Esta seção pressupõe que você já esteja familiarizado com os conceitos de serviço de armazenamento de tabela do Azure. Para obter informações detalhadas, consulte [Noções básicas sobre o modelo de dados do serviço de tabela](http://msdn.microsoft.com/library/azure/dd179338.aspx) e [começar a usar o armazenamento de tabela do Azure usando .NET](storage-dotnet-how-to-use-tables.md).

Nas subseções a seguir, você aprenderá como gerenciar o serviço de armazenamento de tabela do Azure usando o PowerShell do Azure. Os cenários cobertos incluem **criação**, **exclusão**e **recuperação de** **tabelas**, bem como **adição**, **consultando**e **Excluindo entidades de tabela**.

### <a name="how-to-create-a-table"></a>Como criar uma tabela
Cada tabela deve residem em uma conta de armazenamento do Azure. O exemplo a seguir demonstra como criar uma tabela no armazenamento do Azure. O exemplo primeiro estabelece uma conexão para o armazenamento do Azure usando o contexto de conta de armazenamento, que inclui o nome da conta de armazenamento e sua chave de acesso. Em seguida, ele usa o cmdlet [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) para criar uma tabela no armazenamento do Azure.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Create a new table.
    $tabName = "yourtablename"
    New-AzureStorageTable –Name $tabName –Context $Ctx

### <a name="how-to-retrieve-a-table"></a>Como recuperar uma tabela
Você pode consultar e recuperar uma ou todas as tabelas em uma conta de armazenamento. O exemplo a seguir demonstra como recuperar uma determinada tabela usando o cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) .

    #Retrieve a table.
    $tabName = "yourtablename"
    Get-AzureStorageTable –Name $tabName –Context $Ctx

Se você chamar o cmdlet Get-AzureStorageTable sem parâmetros, ele obtém todas as tabelas de armazenamento para uma conta de armazenamento.

### <a name="how-to-delete-a-table"></a>Como excluir uma tabela
Você pode excluir uma tabela de uma conta de armazenamento usando o cmdlet [Remove-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806393.aspx) .  

    #Delete a table.
    $tabName = "yourtablename"
    Remove-AzureStorageTable –Name $tabName –Context $Ctx

### <a name="how-to-manage-table-entities"></a>Como gerenciar entidades de tabela
Atualmente, o Azure PowerShell não fornece cmdlets para gerenciar entidades tabela diretamente. Para executar operações em entidades de tabela, você pode usar as classes fornecidas na [Biblioteca de cliente de armazenamento do Azure para .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).

#### <a name="how-to-add-table-entities"></a>Como adicionar entidades de tabela
Para adicionar uma entidade a uma tabela, primeiro crie um objeto que define as propriedades de entidade. Uma entidade pode ter até 255 propriedades, incluindo 3 Propriedades do sistema: **PartitionKey**, **RowKey**e **carimbo de hora**. Você é responsável por inserir e atualizar os valores de **PartitionKey** e **RowKey**. O servidor gerencia o valor de **carimbo de hora**, que não pode ser modificada. Juntos a **PartitionKey** e **RowKey** identificam exclusivamente cada entidade dentro de uma tabela.

-   **PartitionKey**: determina a partição que a entidade está armazenada em.
-   **RowKey**: identifica exclusivamente a entidade dentro da partição.

Você pode definir até 252 propriedades personalizadas para uma entidade. Para obter mais informações, consulte [Noções básicas sobre o modelo de dados do serviço de tabela](http://msdn.microsoft.com/library/azure/dd179338.aspx).

O exemplo a seguir demonstra como adicionar entidades a uma tabela. O exemplo mostra como recuperar a tabela de funcionários e adicionar várias entidades nela. Primeiro, ele estabelece uma conexão para o armazenamento do Azure usando o contexto de conta de armazenamento, que inclui o nome da conta de armazenamento e sua chave de acesso. Em seguida, ele recupera a determinada tabela usando o cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) . Se a tabela não existir, o cmdlet [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) é usado para criar uma tabela no armazenamento do Azure. Em seguida, o exemplo define uma função personalizada entidade Adicionar para adicionar entidades à tabela especificando partição de cada entidade e a chave de linha. A função de adicionar entidade chama o cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) na classe [Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) para criar um objeto de entidade. Posteriormente, o exemplo chama o método [Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx) nesse objeto de entidade para adicioná-la a uma tabela.

    #Function Add-Entity: Adds an employee entity to a table.
    function Add-Entity() {
        [CmdletBinding()]
        param(
           $table,
           [String]$partitionKey,
           [String]$rowKey,
           [String]$name,
           [Int]$id
        )

      $entity = New-Object -TypeName Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity -ArgumentList $partitionKey, $rowKey
      $entity.Properties.Add("Name", $name)
      $entity.Properties.Add("ID", $id)

      $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
    }

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Retrieve the table if it already exists.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx -ErrorAction Ignore

    #Create a new table if it does not exist.
    if ($table -eq $null)
    {
       $table = New-AzureStorageTable –Name $TableName -Context $Ctx
    }

    #Add multiple entities to a table.
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row1 -Name Chris -Id 1
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row2 -Name Jessie -Id 2
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row1 -Name Christine -Id 3
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row2 -Name Steven -Id 4

#### <a name="how-to-query-table-entities"></a>Como consultar entidades de tabela
Para consultar uma tabela, use a classe [Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx) . O exemplo a seguir supõe que você já tiver executado o script fornecido como adicionar seção entidades deste guia. O exemplo primeiro estabelece uma conexão para o armazenamento do Azure usando o contexto de armazenamento, que inclui o nome da conta de armazenamento e sua chave de acesso. Em seguida, ele tentará recuperar a tabela "Funcionários" criada anteriormente usando o cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) . Chamar o cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) na classe Microsoft.WindowsAzure.Storage.Table.TableQuery cria um novo objeto de consulta. O exemplo procura as entidades que tem uma coluna de 'ID' cujo valor é 1 conforme especificado em um filtro de cadeia de caracteres. Para obter informações detalhadas, consulte [tabelas de consulta e entidades](http://msdn.microsoft.com/library/azure/dd894031.aspx). Quando você executa essa consulta, ela retorna todas as entidades que correspondem aos critérios de filtro.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Get a reference to a table.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx

    #Create a table query.
    $query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery

    #Define columns to select.
    $list = New-Object System.Collections.Generic.List[string]
    $list.Add("RowKey")
    $list.Add("ID")
    $list.Add("Name")

    #Set query details.
    $query.FilterString = "ID gt 0"
    $query.SelectColumns = $list
    $query.TakeCount = 20

    #Execute the query.
    $entities = $table.CloudTable.ExecuteQuery($query)

    #Display entity properties with the table format.
    $entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties["ID"].Int32Value}} -AutoSize

#### <a name="how-to-delete-table-entities"></a>Como excluir entidades de tabela
Você pode excluir uma entidade usando suas chaves de partição e linha. O exemplo a seguir supõe que você já tiver executado o script fornecido como adicionar seção entidades deste guia. O exemplo primeiro estabelece uma conexão para o armazenamento do Azure usando o contexto de armazenamento, que inclui o nome da conta de armazenamento e sua chave de acesso. Em seguida, ele tentará recuperar a tabela "Funcionários" criada anteriormente usando o cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) . Se a tabela existir, o exemplo chama o método [Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) para recuperar uma entidade com base em seus valores-chave partição e linha. Em seguida, passe a entidade para o método [Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx) para excluir.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the table.
    $TableName = "Employees"
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

    #If the table exists, start deleting its entities.
    if ($table -ne $null) {
       #Together the PartitionKey and RowKey uniquely identify every  
       #entity within a table.
       $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve("Partition2", "Row1"))
       $entity = $tableResult.Result
    if ($entity -ne $null)
    {
       #Delete the entity.
       $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
    }

## <a name="how-to-manage-azure-queues-and-queue-messages"></a>Como gerenciar filas Azure e mensagens de fila
Armazenamento de fila Azure é um serviço para armazenar grandes números de mensagens que podem ser acessadas a partir de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Esta seção pressupõe que você já esteja familiarizado com os conceitos de serviço de armazenamento de fila do Azure. Para obter informações detalhadas, consulte [Introdução ao armazenamento de fila do Azure usando .NET](storage-dotnet-how-to-use-queues.md).

Esta seção mostrará como gerenciar o serviço de armazenamento de fila do Azure usando o PowerShell do Azure. Os cenários cobertos incluem **inserindo** e **Excluindo** mensagens de fila, bem como **criação**, **exclusão**e **recuperação filas**.

### <a name="how-to-create-a-queue"></a>Como criar uma fila
O exemplo a seguir primeiro estabelece uma conexão para o armazenamento do Azure usando o contexto de conta de armazenamento, que inclui o nome da conta de armazenamento e sua chave de acesso. Em seguida, ele chama o cmdlet [New-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806382.aspx) para criar uma fila denominada 'NomeDaFila'.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $QueueName = "queuename"
    $Queue = New-AzureStorageQueue –Name $QueueName -Context $Ctx

Para obter informações sobre convenções de nomenclatura para o serviço de fila do Azure, consulte [filas de nomenclatura e metadados](http://msdn.microsoft.com/library/azure/dd179349.aspx).

### <a name="how-to-retrieve-a-queue"></a>Como recuperar uma fila
Você pode consultar e recuperar uma fila específica ou uma lista de todas as filas de uma conta de armazenamento. O exemplo a seguir demonstra como recuperar uma fila especificada usando o cmdlet [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) .

    #Retrieve a queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue –Name $QueueName –Context $Ctx

Se você chamar o cmdlet [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) sem parâmetros, ele obtém uma lista de todas as filas.

### <a name="how-to-delete-a-queue"></a>Como excluir uma fila
Para excluir uma fila e todas as mensagens contidas nele, ligue para o cmdlet Remove-AzureStorageQueue. O exemplo a seguir mostra como excluir uma fila especificada usando o cmdlet Remove-AzureStorageQueue.

    #Delete a queue.
    $QueueName = "yourqueuename"
    Remove-AzureStorageQueue –Name $QueueName –Context $Ctx

#### <a name="how-to-insert-a-message-into-a-queue"></a>Como inserir uma mensagem em uma fila
Para inserir uma mensagem em uma fila existente, primeiro crie uma nova instância da classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) . Em seguida, chame o método [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) . Um CloudQueueMessage pode ser criada de uma cadeia de caracteres (no formato UTF-8) ou uma matriz de bytes.

O exemplo a seguir demonstra como adicionar mensagem para uma fila. O exemplo primeiro estabelece uma conexão para o armazenamento do Azure usando o contexto de conta de armazenamento, que inclui o nome da conta de armazenamento e sua chave de acesso. Em seguida, ele recupera fila especificada usando o cmdlet [Get-AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx) . Se a fila existir, o cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) é usado para criar uma instância da classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) . Posteriormente, o exemplo chama o método [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) nesse objeto de mensagem para adicioná-la a uma fila. Aqui está o código que recupera uma fila e insere a mensagem 'MessageInfo':

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    #If the queue exists, add a new message.
    if ($Queue -ne $null) {
       # Create a new message using a constructor of the CloudQueueMessage class.
       $QueueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage -ArgumentList MessageInfo

       #Add a new message to the queue.
       $Queue.CloudQueue.AddMessage($QueueMessage)
    }


#### <a name="how-to-de-queue-at-the-next-message"></a>Como eliminação fila a próxima mensagem
O código do seu filas eliminação de uma mensagem de uma fila em duas etapas. Quando você chama o método [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) , você recebe a próxima mensagem na fila. Uma mensagem retornada de **GetMessage** fica invisível para qualquer outro código ler mensagens dessa fila. Para concluir a remoção a mensagem da fila, você também deve chamar o método [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) . Esse processo de duas etapas de remoção de uma mensagem garante que, se seu código falha processar uma mensagem devido a falha de hardware ou software, outra instância do seu código pode receberá a mesma mensagem e tente novamente. Seu código chama **DeleteMessage** direita após a mensagem foi processada.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    $InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)

    #Get the message object from the queue.
    $QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
    #Delete the message.
    $Queue.CloudQueue.DeleteMessage($QueueMessage)

## <a name="how-to-manage-azure-file-shares-and-files"></a>Como gerenciar arquivos e compartilhamentos de arquivos Azure
Armazenamento de arquivos Azure oferece armazenamento compartilhado para aplicativos que usam o protocolo SMB padrão. Máquinas virtuais do Microsoft Azure e os serviços de nuvem podem compartilhar dados de arquivos em componentes de aplicativo por meio de compartilhamentos montados e aplicativos de locais podem acessar dados de arquivo em um compartilhamento através do API de armazenamento de arquivos ou Azure PowerShell.

Para obter mais informações sobre o armazenamento de arquivos do Azure, consulte [Introdução ao armazenamento de arquivos do Azure no Windows](storage-dotnet-how-to-use-files.md) e [API do resto de serviço de arquivos](http://msdn.microsoft.com/library/azure/dn167006.aspx).

## <a name="how-to-set-and-query-storage-analytics"></a>Como definir e análise de armazenamento de consulta
Você pode usar [A análise de armazenamento do Azure](storage-analytics.md) para coletar métricas para suas contas de armazenamento do Azure e dados de log sobre solicitações enviados à sua conta de armazenamento. Você pode usar métricas de armazenamento para monitorar a integridade de uma conta de armazenamento e armazenamento registro em log para diagnosticar e solucionar problemas com sua conta de armazenamento.
Por padrão, métricas de armazenamento não está habilitada para os serviços de armazenamento. Você pode habilitar o monitoramento usando o Portal do Azure ou o Windows PowerShell, ou programaticamente usando a biblioteca de cliente de armazenamento. Log de armazenamento acontece servidor e permite que você detalhes de registro de solicitações bem-sucedidas e em sua conta de armazenamento. Esses registros permitem que você ver detalhes de ler, gravar e excluir operações em relação a suas tabelas, filas e blobs bem como as razões para solicitações com falha.

Para saber como ativar e exibir dados de métricas de armazenamento usando o PowerShell, consulte [como habilitar métricas de armazenamento usando o PowerShell](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell).

Para saber como ativar e recuperar dados de log de armazenamento usando o PowerShell, consulte [como habilitar o log de armazenamento usando o PowerShell](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell) e [localizar seus dados de log log de armazenamento](http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata).
Para obter informações detalhadas sobre como usar métricas de armazenamento e armazenamento de registro em log para solucionar problemas de armazenamento, consulte [Monitorando, diagnosticando e solução de problemas de armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="how-to-manage-shared-access-signature-sas-and-stored-access-policy"></a>Como gerenciar a assinatura de acesso compartilhado (SAS) e armazenados política de acesso
Assinaturas de acesso compartilhado são uma parte importante do modelo de segurança para qualquer aplicativo usando o armazenamento do Azure. Eles são úteis para fornecer permissões limitadas à sua conta de armazenamento para clientes que não devem ter a chave da conta. Por padrão, somente o proprietário da conta de armazenamento pode acessar blobs, tabelas e filas dentro dessa conta. Se seu aplicativo ou serviço precisa disponibilizar esses recursos para outros clientes sem compartilhar sua chave de acesso, você tem três opções:

- Defina permissões de um contêiner para permitir o acesso anônimo de leitura para o contêiner e seus blobs. Isso não é permitido para tabelas ou filas.
- Use uma assinatura de acesso compartilhado que concede direitos de acesso para contêineres, blobs, filas e tabelas restrito por um intervalo de tempo específico.
- Use uma política de acesso armazenadas para obter um nível adicional de controle sobre assinaturas de acesso compartilhado para um contêiner ou seus blobs, para uma fila ou para uma tabela. A política de acesso armazenadas permite que você altere a hora de início, hora de expiração ou permissões para uma assinatura ou para revogá-lo depois que ele foi emitido.

Uma assinatura de acesso compartilhado pode estar em uma das duas formas:

- **Ad-hoc SAS**: quando você cria um SAS ad-hoc, a hora de início, a hora de vencimento, e as permissões para as associações de segurança são especificadas no URI SAS. Esse tipo de Associações de segurança pode ser criado em um contêiner, blob, tabela ou fila e é não revokable.
- **SAS com política de acesso armazenadas**: uma política de acesso armazenadas está definida em um contêiner de recurso um contêiner de blob, tabela ou fila - e você pode usá-lo para gerenciar as restrições para uma ou mais assinaturas de acesso compartilhado. Quando você associa uma SAS uma política de acesso armazenadas, as associações de segurança herda as restrições - a hora de início, hora de vencimento e permissões - definidas para a política de acesso armazenadas. Esse tipo de Associações de é revokable.

Para obter mais informações, consulte [Usando compartilhados acesso assinaturas (SAS)](storage-dotnet-shared-access-signature-part-1.md) e [Gerenciar acesso anônimo de leitura para contêineres e blobs](storage-manage-access-to-resources.md).

Nas próximas seções, você aprenderá a criar uma política de acesso de token e armazenados de assinatura de acesso compartilhado para tabelas do Azure. Azure PowerShell fornece cmdlets semelhante para contêineres, blobs e filas também. Para executar os scripts nesta seção, baixe o [Azure PowerShell versão 0.8.14](http://go.microsoft.com/?linkid=9811175&clcid=0x409) ou posterior.

### <a name="how-to-create-a-policy-based-shared-access-signature-token"></a>Como criar um token de assinatura de acesso compartilhado baseadas em políticas
Use o cmdlet New-AzureStorageTableStoredAccessPolicy para criar uma nova política de acesso armazenadas. Em seguida, ligue o cmdlet [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) para criar um novo token de assinatura de acesso compartilhado baseadas em política para uma tabela de armazenamento do Azure.

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
    New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx

### <a name="how-to-create-an-ad-hoc-non-revokable-shared-access-signature-token"></a>Como criar um token de assinatura de acesso compartilhado (não revokable) ad-hoc
Use o cmdlet [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) para criar um novo ad-hoc (não revokable) assinatura de acesso compartilhado token de uma tabela de armazenamento do Azure:

    New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx

### <a name="how-to-create-a-stored-access-policy"></a>Como criar uma política de acesso armazenadas
Use o cmdlet New-AzureStorageTableStoredAccessPolicy para criar uma nova política de acesso armazenadas para uma tabela de armazenamento do Azure:

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx

### <a name="how-to-update-a-stored-access-policy"></a>Como atualizar uma política de acesso armazenadas
Use o cmdlet Set-AzureStorageTableStoredAccessPolicy para atualizar uma política de acesso armazenadas existente para uma tabela de armazenamento do Azure:

    Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx

### <a name="how-to-delete-a-stored-access-policy"></a>Como excluir uma política de acesso armazenadas
Use o cmdlet AzureStorageTableStoredAccessPolicy remover para excluir uma política de acesso armazenadas em uma tabela de armazenamento do Azure:

    Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx


## <a name="how-to-use-azure-storage-for-us-government-and-azure-china"></a>Como usar o armazenamento do Azure para governo dos EUA e China do Azure
Um ambiente Azure é uma implantação independente do Microsoft Azure, como [Azure governo do governo dos EUA](https://azure.microsoft.com/features/gov/), [AzureCloud Azure global](https://portal.azure.com)e [AzureChinaCloud para Azure operado pela 21Vianet na China](http://www.windowsazure.cn/). Você pode implantar novos ambientes Azure para EUA governo e Azure China.

Para usar o armazenamento do Azure com AzureChinaCloud, você precisa criar um contexto de armazenamento que está associado a AzureChinaCloud. Siga estas etapas para iniciá-lo:

1.  Execute o cmdlet [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) para ver os ambientes Azure disponíveis:

    `Get-AzureEnvironment`

2.  Adicione uma conta do Azure China no Windows PowerShell:

    `Add-AzureAccount –Environment AzureChinaCloud`

3.  Crie um contexto de armazenamento para uma conta de AzureChinaCloud:

        $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud

Para usar o armazenamento do Azure com [Azure governamentais](https://azure.microsoft.com/features/gov/), você deve definir um novo ambiente e, em seguida, criar um novo contexto de armazenamento com esse ambiente:

1.  Execute o cmdlet [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) para ver os ambientes Azure disponíveis:

    `Get-AzureEnvironment`

2.  Adicione uma conta do governo do Azure no Windows PowerShell:

    `Add-AzureAccount –Environment AzureUSGovernment`

3.  Crie um contexto de armazenamento para uma conta de AzureUSGovernment:

        $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureUSGovernment

Para obter mais informações, consulte:

- [Guia de desenvolvedor do Microsoft Azure governamentais](../azure-government-developer-guide.md).
- [Visão geral das diferenças ao criar um aplicativo no serviço China](https://msdn.microsoft.com/library/azure/dn578439.aspx)

## <a name="next-steps"></a>Próximas etapas
Neste guia, você aprendeu como gerenciar o armazenamento do Azure com o PowerShell do Azure. Aqui estão alguns artigos relacionados e recursos para aprender mais sobre eles.

- [Documentação de armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Cmdlets do PowerShell de armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn806401.aspx)
- [Referência do Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)

[Image1]: ./media/storage-powershell-guide-full/Subscription_currentportal.png
[Image2]: ./media/storage-powershell-guide-full/Subscription_Previewportal.png
[Image3]: ./media/storage-powershell-guide-full/Blobdownload.png


[Getting started with Azure Storage and PowerShell in 5 minutes]: #getstart
[Prerequisites for using Azure PowerShell with Azure Storage]: #pre
[How to manage storage accounts in Azure]: #manageaccount
[How to set a default Azure subscription]: #setdefsub
[How to create a new Azure storage account]: #createaccount
[How to set a default Azure storage account]: #defaultaccount
[How to list all Azure storage accounts in a subscription]: #listaccounts
[How to create an Azure storage context]: #createctx
[How to manage Azure blobs and blob snapshots]: #manageblobs
[How to create a container]: #container
[How to upload a blob into a container]: #uploadblob
[How to download blobs from a container]: #downblob
[How to copy blobs from one storage container to another]: #copyblob
[How to delete a blob]: #deleteblob
[How to manage Azure blob snapshots]: #manageshots
[How to create a blob snapshot]: #createshot
[How to list snapshots of a blob]: #listshot
[How to copy a snapshot of a blob]: #copyshot
[How to manage Azure tables and table entities]: #managetables
[How to create a table]: #createtable
[How to retrieve a table]: #gettable
[How to delete a table]: #remtable
[How to manage table entities]: #mngentity
[How to add table entities]: #addentity
[How to query table entities]: #queryentity
[How to delete table entities]: #deleteentity
[How to manage Azure queues and queue messages]: #managequeues
[How to create a queue]: #createqueue
[How to retrieve a queue]: #getqueue
[How to delete a queue]: #remqueue
[How to manage queue messages]: #mngqueuemsg
[How to insert a message into a queue]: #addqueuemsg
[How to de-queue at the next message]: #dequeuemsg
[How to manage Azure file shares and files]: #files
[How to set and query storage analytics]: #stganalytics
[How to manage Shared Access Signature (SAS) and Stored Access Policy]: #sas
[How to use Azure Storage for U.S. government and Azure China]: #gov
[Next Steps]: #next
