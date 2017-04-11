<properties 
    pageTitle="Usar o emulador de armazenamento do Azure para desenvolvimento e teste | Microsoft Azure" 
    description="O emulador de armazenamento do Azure fornece um ambiente de desenvolvimento local gratuita para desenvolver e testar o armazenamento do Azure. Saiba mais sobre o emulador de armazenamento, inclusive como solicitações são autenticadas, como conectar o emulador de seu aplicativo e como usar a ferramenta de linha de comando." 
    services="storage" 
    documentationCenter="" 
    authors="tamram" 
    manager="carmonm" 
    editor="tysonn"/>
<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/07/2016" 
    ms.author="tamram"/>

# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Usar o emulador de armazenamento do Azure para desenvolvimento e teste

## <a name="overview"></a>Visão geral

O emulador de armazenamento do Microsoft Azure fornece um ambiente local que emula os serviços do Azure Blob, fila e tabela para fins de desenvolvimento. Usar o emulador de armazenamento, você pode testar seu aplicativo contra os serviços de armazenamento localmente, sem a criação de uma assinatura do Azure ou gerar os custos. Quando estiver satisfeito com como o seu aplicativo está trabalhando no emulador, você pode alternar usando uma conta de armazenamento do Azure na nuvem.

> [AZURE.NOTE] Emulador de armazenamento está disponível como parte do [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). Você também pode instalar o emulador de armazenamento usando o [instalador autônomo](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409). Para configurar o emulador de armazenamento, você deve ter privilégios administrativos no computador.
> 
> Emulador de armazenamento no momento é executada somente no Windows.
>  
> Observe que dados criados em uma versão do emulador de armazenamento não são garantias estejam acessíveis ao usar uma versão diferente. Se você precisar manter seus dados a longo prazo, é recomendável que você armazene dados em uma conta de armazenamento do Azure, em vez de no emulador de armazenamento.

## <a name="how-the-storage-emulator-works"></a>Como funciona o emulador de armazenamento
 
Emulador de armazenamento usa uma instância do Microsoft SQL Server local e o sistema de arquivo local para emular os serviços de armazenamento do Azure. Por padrão, o emulador de armazenamento usa um banco de dados do Microsoft SQL Server 2012 Express LocalDB.  Você pode optar por configurar o emulador de armazenamento para acessar uma instância local do SQL Server em vez da instância LocalDB. Para obter mais informações, consulte [Iniciar e inicializar o emulador de armazenamento](#start-and-initialize-the-storage-emulator) abaixo.

Você pode instalar o SQL Server Management Studio Express para gerenciar sua instalação LocalDB. Emulador de armazenamento se conecta ao SQL Server ou LocalDB usando autenticação do Windows. 

Existem algumas diferenças na funcionalidade entre o emulador de armazenamento e serviços de armazenamento do Azure. Para obter mais informações sobre essas diferenças, consulte [diferenças entre o emulador de armazenamento e o armazenamento do Azure](#differences-between-the-storage-emulator-and-azure-storage).

## <a name="authenticating-requests-against-the-storage-emulator"></a>Solicitações de autenticação contra emulador de armazenamento

Assim como com armazenamento do Azure na nuvem, cada solicitação que você faça contra emulador de armazenamento deve ser autenticada, a menos que ele seja uma solicitação anônima. Você pode autenticar solicitações contra o emulador de armazenamento usando autenticação de chave compartilhada ou com uma assinatura de acesso compartilhado (SAS).

### <a name="authentication-with-shared-key-credentials"></a>Autenticação com credenciais de chave compartilhada

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Para obter mais detalhes sobre cadeias de caracteres de conexão, consulte [Configurar cadeias de Conexão de armazenamento do Azure](storage-configure-connection-string.md). 

### <a name="authentication-with-a-shared-access-signature"></a>Autenticação com uma assinatura de acesso compartilhado 

Algumas bibliotecas de cliente de armazenamento do Azure, como a biblioteca de Xamarin, só oferece suporte a autenticação com um token de assinatura (SAS) acesso compartilhado. Você precisará criar esse token SAS usando uma ferramenta ou aplicativo que oferece suporte à autenticação de chave compartilhada. É uma maneira fácil de gerar o token SAS por meio do Azure PowerShell:

1. Instale o Azure PowerShell se você ainda não fez isso. É recomendável usar a versão mais recente do cmdlets do PowerShell do Azure. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md#Install) para instruções de instalação.

2. Abra o PowerShell do Azure e execute os seguintes comandos. Lembre-se de substituir *nome_da_conta* e *ACCOUNT_KEY = =* com suas próprias credenciais. Substitua *CONTAINER_NAME* por um nome de sua escolha.

        $context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
        
        New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
        
        $now = Get-Date 
        
        New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

A assinatura de acesso compartilhado resultante URI para o novo contêiner deve ser semelhante ao seguinte:

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

A assinatura de acesso compartilhado criada com este exemplo é válida para um dia. A assinatura concede acesso completo (isto é ler, gravar, excluir, lista) para blobs dentro do contêiner.

Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [Assinaturas usando de acesso compartilhado (SAS)](storage-dotnet-shared-access-signature-part-1.md).


## <a name="start-and-initialize-the-storage-emulator"></a>Iniciar e inicializar o emulador de armazenamento

Para iniciar o emulador de armazenamento do Azure, selecione o botão Iniciar ou pressione a tecla do Windows. Comece a digitar **Emulador de armazenamento do Azure**e selecione o emulador na lista de aplicativos. 

Quando o emulador está em execução, você verá um ícone na área de notificação da barra de tarefas do Windows.

Quando o emulador de armazenamento for iniciado, aparecerá uma janela de linha de comando. Você pode usar esta janela de linha de comando para iniciar e parar o emulador de armazenamento, bem como limpar dados, obtenha o status atual e inicializar o emulador. Para obter mais informações, consulte [Referência de ferramenta do armazenamento emulador de linha de comando](#storage-emulator-command-line-tool-reference).

Quando a janela de linha de comando é fechada, o emulador de armazenamento continua a executar. Para exibir a linha de comando novamente, siga as etapas acima, como se Iniciando emulador de armazenamento.

Na primeira vez que você executa o emulador de armazenamento, o ambiente de armazenamento local é inicializado para você. O processo de inicialização cria um banco de dados no LocalDB e reserva portas HTTP para cada serviço de armazenamento local. 

Emulador de armazenamento é instalado por padrão ao diretório C:\Program Files (x86) \Microsoft SDKs\Azure\Storage emulador. 

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inicializar o emulador de armazenamento para usar outro banco de dados SQL

Você pode usar a ferramenta de linha de comando de emulador de armazenamento para inicializar o emulador de armazenamento para apontar para uma instância de banco de dados do SQL diferente a instância de LocalDB padrão. Observe que você deve estar executando a ferramenta de linha de comando com privilégios administrativos ao inicializar o banco de dados back-end para o emulador de armazenamento:

1. Clique no botão **Iniciar** ou pressione a tecla do **Windows** . Comece a digitar `Azure Storage Emulator` e selecione-a quando for exibido abrir a ferramenta de linha de comando de emulador de armazenamento.
2. Na janela do prompt de comando, digite o seguinte comando, onde `<SQLServerInstance>` é o nome da instância do SQL Server. Para usar LocalDb, especifique `(localdb)\v11.0` como a instância do SQL Server.

        AzureStorageEmulator init /server <SQLServerInstance> 
    
    Você também pode usar o comando a seguir, que direciona o emulador a usar a instância do SQL Server padrão:

        AzureStorageEmulator init /server .\\ 

    Ou, você pode usar o comando a seguir, que inicializa novamente o banco de dados à instância LocalDB padrão:

        AzureStorageEmulator init /forceCreate 

Para obter mais informações sobre esses comandos, consulte [Referência de ferramenta do armazenamento emulador de linha de comando](#storage-emulator-command-line-tool-reference).

## <a name="addressing-resources-in-the-storage-emulator"></a>Recursos de endereçamento em emulador de armazenamento

Os pontos de extremidade do serviço para o emulador de armazenamento são diferentes daqueles de uma conta de armazenamento do Azure. A diferença é devido ao fato de que o computador local não executará resolução de nome de domínio, para que os pontos de extremidade de emulador de armazenamento exigem um endereço local em vez de um nome de domínio.

Ao endereço de um recurso em uma conta de armazenamento do Azure, você usa o seguinte esquema, onde o nome da conta é parte do nome de host de URI e o recurso sendo resolvido faz parte do caminho do URI:

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>

Por exemplo, o seguinte URI é um endereço válido para um blob em uma conta de armazenamento do Azure:

    https://myaccount.blob.core.windows.net/mycontainer/myblob.txt

No emulador armazenamento, como o computador local não executará a resolução de nome de domínio, o nome da conta é parte do caminho do URI em vez do nome de host. Você pode usar o seguinte esquema para um recurso em execução no emulador de armazenamento:

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

Por exemplo, o seguinte endereço pode ser usado para acessar um blob no emulador armazenamento:

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

Os pontos de extremidade do serviço para o emulador de armazenamento são:

    Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
    Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
    Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

### <a name="addressing-the-account-secondary-with-ra-grs"></a>A conta secundária com ar GRS de endereçamento

Começando com a versão 3.1, a conta de emulador de armazenamento oferece suporte para replicação de localização geográfica redundantes de acesso de leitura (ar-GRS). Para recursos de armazenamento na nuvem e no emulador local, você pode acessar o local secundário por anexando - secundário para o nome da conta. Por exemplo, o seguinte endereço pode ser usado para acessar um blob usando o secundário somente leitura no emulador de armazenamento:

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] Para obter acesso por programação para o secundário com o emulador de armazenamento, use a biblioteca de cliente de armazenamento para .NET versão 3,2 ou posterior. Consulte a [Biblioteca do cliente de armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) para obter detalhes.

## <a name="storage-emulator-command-line-tool-reference"></a>Referência de ferramenta de linha de comando do emulador de armazenamento

Iniciando na versão 3.0, quando você inicia o emulador de armazenamento, você verá uma linha de comando janela pop-up. Use a janela de linha de comando para iniciar e parar o emulador, bem como para consultar status e realizar outras operações.

> [AZURE.NOTE] Se você tiver o Microsoft Azure calcular emulador instalado, um ícone de bandeja do sistema aparecerá quando você inicia o emulador de armazenamento. Clique com botão direito no ícone para revelar um menu, que fornece uma maneira gráfica de iniciar e parar o emulador de armazenamento.

### <a name="command-line-syntax"></a>Sintaxe de linha de comando

    AzureStorageEmulator [start] [stop] [status] [clear] [init] [help]

### <a name="options"></a>Opções

Para exibir a lista de opções, digite `/help` no prompt de comando.

| Opção | Descrição                                                    | Comando                                                                                                 | Argumentos                                                                                                         |
|--------|----------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| **Iniciar**  | Inicia o emulador de armazenamento.                                | `AzureStorageEmulator start [-inprocess]`                                                                    | *-de*: iniciar o emulador no processo atual em vez de criar um novo processo.                          |
| **Parar**   | Interrompe o emulador de armazenamento.                                    | `AzureStorageEmulator stop`                                                                                  |                                                                                                                   |
| **Status** | Imprime o status de emulador de armazenamento.                     | `AzureStorageEmulator status`                                                                                |                                                                                                                   |
| **Limpar**  | Limpa os dados em todos os serviços especificados na linha de comando. | `AzureStorageEmulator clear [blob] [table] [queue] [all]                                                    `| *blob*: limpa blob dados. <br/>*fila*: limpa fila dados. <br/>*tabela*: limpa dados de tabela. <br/>*todos os*: limpa todos os dados em todos os serviços. |
| **Inicialização**   | Executa a inicialização única para configurar o emulador.       | `AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate] [-inprocess]` | *-servidor NomedoServidor \ NomedaInstância*: Especifica o servidor que hospeda a instância do SQL. <br/>*-sqlinstance instanceName*: Especifica o nome da instância do SQL a ser usado na instância padrão do servidor. <br/>*-forcecreate*: força a criação do banco de dados SQL, mesmo se ele já existir. <br/>*-de*: executa a inicialização do processo atual em vez de disparo de um processo de novo. Você deve iniciar o processo atual com permissões elevados para realizar inicialização.          |
                                                                                                                  
## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Diferenças entre o emulador de armazenamento e o armazenamento do Azure

Como o emulador de armazenamento é um ambiente emulada executado em uma instância local do SQL, há algumas diferenças na funcionalidade entre o emulador e uma conta de armazenamento do Azure na nuvem:

- Emulador de armazenamento oferece suporte apenas uma única conta fixa e uma chave de autenticação conhecidos.

- Emulador de armazenamento não é um serviço de armazenamento escaláveis e não oferecerá suporte a um grande número de clientes simultâneos.

- Conforme descrito em [recursos de endereçamento no emulador armazenamento](#addressing-resources-in-the-storage-emulator), recursos apresentados diferente emulador de armazenamento versus uma conta de armazenamento do Azure. Essa diferença é devido ao fato de que resolução de nome de domínio está disponível na nuvem, mas não no computador local.

- Começando com a versão 3.1, a conta de emulador de armazenamento oferece suporte para replicação de localização geográfica redundantes de acesso de leitura (ar-GRS). No emulador, todas as contas têm ar-GRS habilitado e nunca há qualquer tempo de latência entre as réplicas primárias e secundárias. As operações de obter estatísticas de serviço Blob, obter estatísticas de serviço de fila e obter estatísticas do serviço de tabela são suportadas na conta secundária e sempre retornará o valor da `LastSyncTime` elemento de resposta como a hora atual de acordo com o banco de dados do SQL subjacente.

    Para obter acesso por programação para o secundário com o emulador de armazenamento, use a biblioteca de cliente de armazenamento para .NET versão 3,2 ou posterior. Consulte a [Biblioteca do cliente de armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) para obter detalhes.

- O serviço de arquivo e pontos de extremidade de serviço de protocolo SMB não são suportados no momento no emulador de armazenamento.

- Emulador de armazenamento retornará um erro de VersionNotSupportedByEmulator (código de status HTTP 400 - Solicitação inválida) se você usar uma versão dos serviços de armazenamento que ainda não é suportada pela versão do emulador que você está usando.

### <a name="differences-for-blob-storage"></a>Diferenças para armazenamento de Blob 

As seguintes diferenças aplicam-se ao armazenamento de Blob no emulador:

- O armazenamento emulador somente suporta blob tamanhos até 2 GB.

- Uma operação de colocar Blob pode êxito contra um blob que existe no emulador de armazenamento e tem uma concessão ativa, mesmo se a ID de concessão não foi especificada como parte da solicitação. 

- Acrescente Blob operações não são suportadas pelo emulador. Tentando uma operação em um blob de acréscimo retornará um erro de FeatureNotSupportedByEmulator (código de status HTTP 400 - Solicitação inválida).

### <a name="differences-for-table-storage"></a>Diferenças para armazenamento de tabela 

As seguintes diferenças aplicam-se ao armazenamento de tabela no emulador:

- Propriedades de data no serviço de tabela no emulador de armazenamento suportam somente o intervalo compatível com SQL Server 2005 (*ou seja*, eles são necessários para ser posterior 1 de janeiro de 1753). Todas as datas anteriores a 1 de janeiro de 1753 são alteradas para esse valor. A precisão de datas é limitada para a precisão do SQL Server 2005, indicando que as datas são precisas a 1/300th de segundo.

- O emulador de armazenamento oferece suporte a valores de propriedade de chave chave e linha de partição menor que 512 bytes cada. Além disso, o tamanho total do nome de conta, nome da tabela e nomes de propriedade de chave juntos não pode exceder 900 bytes.

- O tamanho total de uma linha em uma tabela no emulador de armazenamento está limitado aos menor que 1 MB.

- No emulador armazenamento, digite propriedades dos dados `Edm.Guid` ou `Edm.Binary` suporte somente a `Equal (eq)` e `NotEqual (ne)` cadeias de caracteres de filtro de operadores de comparação na consulta.

### <a name="differences-for-queue-storage"></a>Diferenças para armazenamento de fila

Não existem diferenças específicas ao armazenamento de fila no emulador.

## <a name="storage-emulator-release-notes"></a>Notas de versão do emulador de armazenamento

### <a name="version-45"></a>Versão 4,5

- Corrigido um bug que causou a instalação do emulador armazenamento falha quando o banco de dados de backup foi renomeado e inicialização.

### <a name="version-44"></a>Versão 4.4

- Emulador de armazenamento agora dá suporte a versão 2015-12-11 dos serviços de armazenamento em Blob, fila e tabela pontos de extremidade do serviço.

- Coleta de lixo do emulador de armazenamento dos dados blob agora é mais eficiente ao lidar com grandes quantidades de blobs.

- Corrigido um bug que causou contêiner ACL XML para ser validados ligeiramente diferente de como o serviço de armazenamento faz isso.

- Corrigido um bug que às vezes causou max e min valores de DateTime para ser informado no fuso horário incorreto.

### <a name="version-43"></a>Versão 4.3

- Emulador de armazenamento agora dá suporte a versão 2015-07-08 dos serviços de armazenamento em Blob, fila e tabela pontos de extremidade do serviço.

### <a name="version-42"></a>Versão 4.2

- Emulador de armazenamento agora dá suporte a versão 2015-04-05 dos serviços de armazenamento em Blob, fila e tabela pontos de extremidade do serviço.

### <a name="version-41"></a>Versão 4.1

- Emulador de armazenamento agora dá suporte a versão 2015-02-21 dos serviços de armazenamento em Blob, fila e tabela serviço pontos de extremidade, excepto os novos recursos de Blob acrescentar. 

- Emulador de armazenamento agora retorna uma mensagem de erro significativo se você usar uma versão dos serviços de armazenamento que ainda não é suportada por essa versão do emulador. É recomendável usar a versão mais recente do emulador. Se você encontrar um erro de VersionNotSupportedByEmulator (código de status HTTP 400 - Solicitação inválida), baixe a versão mais recente do emulador armazenamento.

- Corrigido um bug na qual um corrida condição causado tabela entidade de dados para estar incorretos durante as operações de mesclagem simultâneas.

### <a name="version-40"></a>Versão 4.0

- O emulador de armazenamento executável foi renomeado para *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Versão 3,2

- Emulador de armazenamento agora dá suporte a versão de 2014-02-14 dos serviços de armazenamento em Blob, fila e tabela pontos de extremidade do serviço. Observe que os pontos de extremidade de serviço de arquivo não são aceitos atualmente no emulador armazenamento. Consulte o [controle de versão para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd894041.aspx) para obter detalhes sobre a versão de 2014-02-14.

### <a name="version-31"></a>Versão 3.1

- Acesso de leitura geográfica-redundantes (ar-GRS) agora é suportado no emulador de armazenamento. A obter estatísticas de serviço Blob, obter estatísticas de serviço de fila e obter APIs de estatísticas de serviço de tabela têm suporte para a conta secundária e sempre retornará o valor do elemento de resposta LastSyncTime como a hora atual de acordo com o banco de dados do SQL subjacente. Para obter acesso por programação para o secundário com o emulador de armazenamento, use a biblioteca de cliente de armazenamento para .NET versão 3,2 ou posterior. Consulte a biblioteca de cliente de armazenamento do Microsoft Azure para referência .NET para obter detalhes.

### <a name="version-30"></a>Versão 3.0

- O emulador de armazenamento do Azure já não é fornecido no mesmo pacote como a computação.

- A interface de usuário gráfica de emulador de armazenamento é substituída uma interface de linha de comando programável. Para obter detalhes sobre a interface de linha de comando, consulte Referência de ferramenta do armazenamento emulador de linha de comando. A interface gráfica continuará a ser presentes na versão 3.0, mas só pode ser acessada quando o emulador de computação está instalado clicando no ícone de bandeja do sistema e selecionando Mostrar UI de emulador de armazenamento.

- Versão 2013-08-15 dos serviços de armazenamento do Azure agora é totalmente suportada. (Esta versão foi apenas suportada anteriormente pelo emulador de armazenamento versão 2.2.1 Preview.)
