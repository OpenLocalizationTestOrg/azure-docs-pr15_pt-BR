<properties
    pageTitle="Trabalhando com a biblioteca de cliente gerenciado de aplicativos do Mobile de serviço de aplicativo (Windows | Xamarin) | Microsoft Azure"
    description="Saiba como usar um cliente .NET para aplicativos do Azure aplicativo de serviço móvel com aplicativos do Windows e Xamarin."
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Como usar o cliente gerenciado para aplicativos do Azure móvel

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##<a name="overview"></a>Visão geral

Este guia mostra como executar cenários comuns usando a biblioteca de cliente gerenciado para Azure aplicativo de serviço móvel aplicativos para Windows e aplicativos de Xamarin. Se você estiver começando a usar os aplicativos Mobile, você deve considerar primeiro Concluindo o [início rápido de aplicativos do Azure Mobile] [ 1] tutorial. Neste guia, podemos focalizar o SDK gerenciado do lado do cliente. Para saber mais sobre o SDK do lado do servidor para aplicativos Mobile, consulte a documentação para o [SDK do .NET Server] [ 2] ou o [SDK do Node Server][3].

## <a name="reference-documentation"></a>Documentação de referência

A documentação de referência para o SDK cliente está localizada aqui: [referência de cliente do Azure Mobile aplicativos .NET][4].
Você também pode encontrar vários exemplos de cliente no [repositório do Azure amostras GitHub][5].

## <a name="supported-platforms"></a>Plataformas com suporte

A plataforma .NET suporta as seguintes plataformas:

* Versões de Xamarin Android para API 19 por meio de 24 (KitKat por meio de Nougat)
* Versões do iOS de Xamarin para iOS versões 8.0 e posteriores
* Plataforma Windows universal
* Windows Phone 8.1
* Windows Phone 8.0, exceto para os aplicativos do Silverlight

A autenticação de "server-fluxo" usa uma exibição da Web para a interface do usuário apresentada.  Se o dispositivo não for capaz de apresentar uma UI WebView, outros métodos de autenticação são necessários.  Este SDK, portanto, não é adequado para o tipo de inspeção ou da mesma forma dispositivos restritos.

##<a name="setup"></a>Pré-requisitos e configuração

Vamos supor que você já tiver criado e publicado seu projeto de back-end do aplicativo móvel, que inclua pelo menos uma tabela.  No código usado neste tópico, a tabela é denominada `TodoItem` e tem as seguintes colunas: `Id`, `Text`, e `Complete`. Esta tabela é a mesma tabela criada quando você concluir o [início rápido a aplicativos do Azure Mobile].

O tipo de cliente digitado correspondente em c# é a seguinte classe:

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

O [JsonPropertyAttribute] [ 6] é usado para definir o mapeamento de *PropertyName* entre o tipo de cliente e a tabela.

Para aprender a criar tabelas no seu back-end aplicativos Mobile, consulte as informações no [tópico SDK do servidor .NET] [ 7] ou o [tópico Node Server SDK][8]. Se você criou seu back-end do aplicativo Mobile no portal do Azure usando o início rápido, você também pode usar a configuração de **tabelas fácil** no [portal do Azure].

###<a name="how-to-install-the-managed-client-sdk-package"></a>Como: instalar o pacote SDK do cliente gerenciado

Use um dos seguintes métodos para instalar o pacote SDK do cliente gerenciado para os aplicativos móveis do [NuGet][9]:

+ **O Visual Studio** Seu projeto de atalho, clique em **Gerenciar pacotes NuGet**, procure o `Microsoft.Azure.Mobile.Client` empacotar, em seguida, clique em **instalar**.

+ **Xamarin Studio** Atalho de seu projeto, clique em **Adicionar** > **Adicionar pacotes NuGet**, procure o `Microsoft.Azure.Mobile.Client `empacotar e, em seguida, clique em **Add Package**.

No seu arquivo de atividade principal, lembre-se de adicionar a seguinte instrução **usando** :

    using Microsoft.WindowsAzure.MobileServices;

###<a name="symbolsource"></a>Como: trabalhar com símbolos de depuração no Visual Studio

Os símbolos do namespace Microsoft.Azure.Mobile estão disponíveis em [SymbolSource][10].  Consulte as [instruções de SymbolSource] [ 11] integrar SymbolSource com o Visual Studio.

##<a name="create-client"></a>Crie o cliente de aplicativos Mobile

O código a seguir cria o [MobileServiceClient] [ 12] objeto que é usado para acessar seu back-end do aplicativo Mobile.

    var client = new MobileServiceClient("MOBILE_APP_URL");

No código anterior, substitua `MOBILE_APP_URL` com a URL do aplicativo Mobile back-end, que é encontrado na lâmina para seu back-end do aplicativo Mobile no [portal do Azure]. O objeto MobileServiceClient deve ser um único.

## <a name="work-with-tables"></a>Trabalhar com tabelas

A seção a seguir detalha como pesquisar e recuperar registros e modificar os dados dentro da tabela.  Os tópicos a seguir são abordados:

* [Criar uma referência de tabela](#instantiating)
* [Dados da consulta](#querying)
* [Filtrar os dados retornados](#filtering)
* [Classificar dados retornado](#sorting)
* [Retornar dados nas páginas](#paging)
* [Selecionar colunas específicas](#selecting)
* [Pesquisar um registro por Id](#lookingup)
* [Lidando com consultas sem tipo](#untypedqueries)
* [Inserindo dados](#inserting)
* [Atualizando dados](#updating)
* [Exclusão de dados](#deleting)
* [Resolução de conflitos e concorrência otimista](#optimisticconcurrency)
* [Associação a uma Interface de usuário do Windows](#binding)
* [Alterar o tamanho de página](#pagesize)

###<a name="instantiating"></a>Como: criar uma referência de tabela

Todo o código que acessa ou modifica dados em uma tabela de back-end chamadas de funções na `MobileServiceTable` objeto. Obtenha uma referência para a tabela chamando o método [GetTable] , da seguinte maneira:

    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();

O objeto retornado usa o modelo de serialização digitado. Também é compatível com um modelo de serialização não digitado. O seguinte exemplo [cria uma referência a uma tabela sem tipo]:

    // Get an untyped table reference
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

Em consultas sem tipo, especifique a cadeia de caracteres de consulta OData subjacente.

###<a name="querying"></a>Como: consultar dados de seu aplicativo móvel

Esta seção descreve como emitir consultas no aplicativo móvel back-end, que inclui as seguintes funcionalidades:

- [Filtrar os dados retornados](#filtering)
- [Classificar dados retornado](#sorting)
- [Retornar dados nas páginas](#paging)
- [Selecionar colunas específicas](#selecting)
- [Procurar dados por ID](#lookingup)

>[AZURE.NOTE]Um tamanho de página controlado pelo servidor é aplicado para impedir que todas as linhas sendo retornados.  Paginação mantém solicitações de padrão para grandes conjuntos de dados de impacto negativo no serviço.  Para retornar mais de 50 linhas, use o `Skip` e `Take` método, conforme descrito em [retornar dados nas páginas].

###<a name="filtering"></a>Como: filtrar dados retornados

O código a seguir ilustra como filtrar dados, incluindo um `Where` cláusula em uma consulta. Ela retorna todos os itens do `todoTable` cuja `Complete` propriedade é igual a `false`. A função [onde] aplica uma linha filtragem predicado à consulta com base na tabela.

    // This query filters out completed TodoItems and items without a timestamp.
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .ToListAsync();

Você pode exibir o URI da solicitação enviado ao back-end usando software de inspeção de mensagem, como ferramentas de desenvolvedor do navegador ou [Fiddler]. Se você olhar URI da solicitação, observe que a cadeia de caracteres de consulta é modificada:

    GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Esta solicitação de OData é convertida em uma consulta SQL pelo SDK do servidor:

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0

A função que é passada para o `Where` método pode ter um número aleatório de condições.

    // This query filters out completed TodoItems where Text isn't null
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
       .ToListAsync();

Este exemplo deve ser convertido em uma consulta SQL pelo SDK do servidor:

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0

Essa consulta também pode ser dividida em várias cláusulas:

    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .Where(todoItem => todoItem.Text != null)
       .ToListAsync();

Os dois métodos são equivalentes e podem ser usados alternadamente.  A opção anterior&mdash;de concatenar vários predicados em uma consulta&mdash;é mais compacta e recomendado.

O `Where` cláusula dá suporte a operações que ser traduzida para o subconjunto de OData. Operações incluem:

* Operadores relacionais (= =,! =, <, < =, >, > =),
* Operadores aritméticos (+, -, /, *, %),
* Número de precisão (Math.Floor, Math.Ceiling)
* Funções de cadeia de caracteres (comprimento, subcadeia, substituir, IndexOf, StartsWith, EndsWith)
* Propriedades de data (ano, mês, dia, hora, minuto, segundo)
* Acessar as propriedades de um objeto, e
* Expressões combinando qualquer uma dessas operações.

Ao considerar o que suporta o SDK do servidor, você pode considerar a [documentação do OData v3].

###<a name="sorting"></a>Como: classificar dados retornados

O código a seguir ilustra como classificar dados, incluindo uma função [OrderBy] ou [OrderByDescending] na consulta. Retorna itens de `todoTable` classificado em ordem crescente pela `Text` campo.

    // Sort items in ascending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

    // Sort items in descending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

###<a name="paging"></a>Como: retornar dados em páginas

Por padrão, o back-end retorna somente as primeiras 50 linhas. Você pode aumentar o número de linhas retornadas chamando o método [levar] . Use `Take` juntamente com o método de [Ignorar] para solicitar uma determinada "página" do total dataset retornada pela consulta. A seguinte consulta, quando executado, retorna os itens de três principais na tabela.

    // Define a filtered query that returns the top 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

A seguinte consulta revisada ignora os três primeiros resultados e retorna os próximos três resultados. Essa consulta produzirá a segunda "página" de dados, onde o tamanho da página é três itens.

    // Define a filtered query that skips the top 3 items and returns the next 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Skip(3)
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

O método de [IncludeTotalCount] solicita a contagem total para _todos_ os registros que poderia ter sido retornados, ignorando qualquer cláusula de paginação/limite especificada:

    query = query.IncludeTotalCount();

Em um aplicativo do mundo real, você pode usar consultas semelhantes ao exemplo anterior com um controle de pager ou UI comparável para navegar entre páginas.

>[AZURE.NOTE]Para substituir o limite de linha de 50 em um back-end do aplicativo móvel, você também deve aplicar o [EnableQueryAttribute] para o método GET público e especificar o comportamento de paginação. Quando aplicado ao método, o seguinte define o máximo retornado linhas a 1000:
>
>    [EnableQuery(MaxTop=1000)]

### <a name="selecting"></a>Como: selecionar colunas específicas

Você pode especificar qual conjunto de propriedades para incluir os resultados, adicionando uma cláusula [Select] à sua consulta. Por exemplo, o código a seguir mostra como selecionar apenas um campo e também como selecionar e formatar vários campos:

    // Select one field -- just the Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => todoItem.Text);
    List<string> items = await query.ToListAsync();

    // Select multiple fields -- both Complete and Text info
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => string.Format("{0} -- {1}",
                        todoItem.Text.PadRight(30), todoItem.Complete ?
                        "Now complete!" : "Incomplete!"));
    List<string> items = await query.ToListAsync();

Todas as funções descritas até agora são aditivos, portanto estamos pode manter encadeamento-los. Cada chamada encadeada afeta mais da consulta. Mais um exemplo:

    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Where(todoItem => todoItem.Complete == false)
                    .Select(todoItem => todoItem.Text)
                    .Skip(3).
                    .Take(3);
    List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>Como: procurar dados por ID

A função [LookupAsync] pode ser usada para procurar objetos do banco de dados com uma ID de determinado.

    // This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
    TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="untypedqueries"></a>Como: executar consultas sem tipo

Ao executar uma consulta usando um objeto de tabela sem tipo, você deve especificar explicitamente a cadeia de caracteres de consulta OData chamando [ReadAsync], como no exemplo seguinte:

    // Lookup untyped data using OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Você voltar valores JSON que você pode usar como um conjunto de propriedades. Para obter mais informações sobre JToken e Newtonsoft Json.NET, consulte o site [Json.NET] .

### <a name="inserting"></a>Como: inserir dados em um back-end do aplicativo móvel

Todos os tipos de cliente devem conter um membro chamado **Id**, que é por padrão uma cadeia de caracteres. Essa **Id** é necessário para executar operações CRUD e para sincronização offline. O código a seguir ilustra como usar o método [InsertAsync] para inserir novas linhas em uma tabela. O parâmetro contém os dados a ser inserido como um objeto .NET.

    await todoTable.InsertAsync(todoItem);

Se um valor de identificação exclusivo personalizado não está incluído no `todoItem` durante uma inserção, um GUID é gerado pelo servidor.
Você pode recuperar a identificação gerada por meio da inspeção o objeto depois retorna a chamada.

Para inserir dados sem tipo, você pode aproveitar Json.NET:

    JObject jo = new JObject();
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

Aqui está um exemplo usando um endereço de email como um id de cadeia de caracteres exclusivos:

    JObject jo = new JObject();
    jo.Add("id", "myemail@emaildomain.com");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

### <a name="working-with-id-values"></a>Trabalhando com valores de ID

Aplicativos Mobile oferece suporte a valores de cadeia de caracteres personalizada exclusivo para a coluna da tabela **id** . Um valor de cadeia de caracteres permite que os aplicativos usar valores personalizados como endereços de email ou nomes de usuário para a identificação.  IDs de cadeia de caracteres fornecem os seguintes benefícios:

* IDs são gerados sem fazer uma viagem ao banco de dados.
* Registros são mais fáceis de mesclar a partir de diferentes tabelas ou bancos de dados.
* Valores de iDs podem integrar melhor com lógica de um aplicativo.

Quando um valor de cadeia de caracteres de identificação não estiver definido em um registro de inserido, o back-end do aplicativo Mobile gera um valor exclusivo para a identificação. Você pode usar o método [Guid.NewGuid] para gerar seus próprios valores de ID, no cliente ou no back-end.

    JObject jo = new JObject();
    jo.Add("id", Guid.NewGuid().ToString("N"));

###<a name="modifying"></a>Como: modificar dados em um back-end do aplicativo móvel

O código a seguir ilustra como usar o método [UpdateAsync] para atualizar um registro existente com o mesmo ID com novas informações. O parâmetro contém os dados que serão atualizadas como um objeto .NET.

    await todoTable.UpdateAsync(todoItem);

Para atualizar dados não digitados, você pode aproveitar [Json.NET] da seguinte maneira:

    JObject jo = new JObject();
    jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);

Um `id` campo deve ser especificado quando você faz uma atualização. O back-end usa o `id` campo para identificar qual linha Atualizar. O `id` campo pode ser obtido o resultado do `InsertAsync` chamar. Um `ArgumentException` é gerado se você tentar atualizar um item sem fornecer a `id` valor.

###<a name="deleting"></a>Como: excluir dados em um back-end do aplicativo móvel

O código a seguir ilustra como usar o método [DeleteAsync] para excluir uma instância existente. A instância é identificada pela `id` conjunto de campo na `todoItem`.

    await todoTable.DeleteAsync(todoItem);

Para excluir dados sem tipo, você pode aproveitar Json.NET da seguinte maneira:

    JObject jo = new JObject();
    jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    await table.DeleteAsync(jo);

Quando você faz uma solicitação de exclusão, uma ID deve ser especificada. Outras propriedades não são passadas para o serviço ou são ignoradas no serviço. O resultado de um `DeleteAsync` chamada é geralmente `null`. A ID de passar pode ser obtida o resultado do `InsertAsync` chamar. A `MobileServiceInvalidOperationException` é lançada quando você tenta excluir um item sem especificar o `id` campo.

###<a name="optimisticconcurrency"></a>Como: usar concorrência otimista para resolução de conflitos

Dois ou mais clientes podem gravar alterações ao mesmo item ao mesmo tempo. Sem detecção de conflito, a última gravação seria substituir todas as atualizações anteriores. **Controle de concorrência otimista** pressupõe que cada transação pode confirmar e, portanto, não use qualquer bloqueio de recurso.  Antes de confirmar uma transação, o controle de concorrência otimista verifica que nenhuma outra transação modificou os dados. Se os dados foram modificados, a transação comprometimento é revertida.

Aplicativos Mobile oferece suporte ao controle de concorrência otimista por controle de alterações em cada item usando a `version` coluna de propriedade de sistema que é definida para cada tabela em seu back-end do aplicativo Mobile. Sempre que um registro é atualizado, aplicativos Mobile define o `version` propriedade para esse registro para um novo valor. Durante cada solicitação de atualização, o `version` propriedade do registro incluído na solicitação é comparada à mesma propriedade para o registro no servidor. Se a versão passado com a solicitação não coincidir com o back-end, a biblioteca de cliente gera uma `MobileServicePreconditionFailedException<T>` exceção. O tipo incluído com a exceção é o registro de back-end que contém a versão de servidores do registro. O aplicativo pode usar essas informações para decidir se deseja executar a solicitação de atualização com o nome correto `version` valor de back-end para confirmar as alterações.

Definir uma coluna na classe de tabela para o `version` propriedade do sistema para habilitar concorrência otimista. Por exemplo:

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

        // *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "version")]
        public string Version { set; get; }
    }


Aplicativos usando tabelas sem ativar concorrência otimista definindo a `Version` sinalize no `SystemProperties` da tabela da seguinte maneira.

    //Enable optimistic concurrency by retrieving version
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

Além de habilitar concorrência otimista, você também deve capturar a `MobileServicePreconditionFailedException<T>` exceção em seu código ao chamar [UpdateAsync].  Resolver o conflito aplicando correta `version` para o registro atualizado e chamada [UpdateAsync] com o registro resolvido. O código a seguir mostra como resolver um conflito de gravação uma vez detectado:

    private async void UpdateToDoItem(TodoItem item)
    {
        MobileServicePreconditionFailedException<TodoItem> exception = null;

        try
        {
            //update at the remote table
            await todoTable.UpdateAsync(item);
        }
        catch (MobileServicePreconditionFailedException<TodoItem> writeException)
        {
            exception = writeException;
        }

        if (exception != null)
        {
            // Conflict detected, the item has changed since the last query
            // Resolve the conflict between the local and server item
            await ResolveConflict(item, exception.Item);
        }
    }


    private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
    {
        //Ask user to choose the resoltion between versions
        MessageDialog msgDialog = new MessageDialog(
            String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
            serverItem.Text, localItem.Text),
            "CONFLICT DETECTED - Select a resolution:");

        UICommand localBtn = new UICommand("Commit Local Text");
        UICommand ServerBtn = new UICommand("Leave Server Text");
        msgDialog.Commands.Add(localBtn);
        msgDialog.Commands.Add(ServerBtn);

        localBtn.Invoked = async (IUICommand command) =>
        {
            // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
            // catching a MobileServicePreConditionFailedException.
            localItem.Version = serverItem.Version;

            // Updating recursively here just in case another change happened while the user was making a decision
            UpdateToDoItem(localItem);
        };

        ServerBtn.Invoked = async (IUICommand command) =>
        {
            RefreshTodoItems();
        };

        await msgDialog.ShowAsync();
    }

Para obter mais informações, consulte o tópico de [Sincronização de dados Offline em aplicativos móveis do Azure] .

###<a name="binding"></a>Como: dados de aplicativos do Mobile vincular a uma interface de usuário do Windows

Esta seção mostra como exibir objetos de dados retornados usando elementos de interface do usuário em um aplicativo do Windows.  O código de exemplo a seguir vincula à fonte da lista com uma consulta para itens incompletos. O [MobileServiceCollection] cria uma coleção de vinculação aplicativos móveis-ciente.

    // This query filters out completed TodoItems.
    MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
        .Where(todoItem => todoItem.Complete == false)
        .ToCollectionAsync();

    // itemsControl is an IEnumerable that could be bound to a UI list control
    IEnumerable itemsControl  = items;

    // Bind this to a ListBox
    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Alguns controles no runtime gerenciado suportam uma interface chamada [ISupportIncrementalLoading]. Essa interface permite controles solicitar dados extras quando o usuário rola. Não há suporte interno para essa interface para aplicativos do Windows universais via [MobileServiceIncrementalLoadingCollection], que controla automaticamente as chamadas dos controles. Use `MobileServiceIncrementalLoadingCollection` nos aplicativos do Windows da seguinte forma:

    MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
    items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Para usar o novo conjunto em aplicativos do Windows Phone 8 e "Silverlight", use o `ToCollection` métodos de extensão em `IMobileServiceTableQuery<T>` e `IMobileServiceTable<T>`. Para carregar dados, chamar `LoadMoreItemsAsync()`.

    MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
    await items.LoadMoreItemsAsync();

Quando você usa a coleção criada chamando `ToCollectionAsync` ou `ToCollection`, você recebe um conjunto que pode ser vinculado a controles de interface do usuário.  Essa coleção é ciente de paginação.  Desde que a coleção está carregando dados da rede, carregando às vezes falhará. Para lidar com essas falhas, substituir o `OnException` método em `MobileServiceIncrementalLoadingCollection` para manipular exceções resultantes de chamadas para `LoadMoreItemsAsync`.

Considere se sua tabela tiver muitos campos, mas só você deseja exibir algumas no seu controle. Você pode usar a orientação na seção anterior "[Selecionar colunas específicas](#selecting)" para selecionar colunas específicas para exibir a IU.

###<a name="pagesize"></a>Alterar o tamanho da página

Os aplicativos do Azure Mobile retorna um máximo de 50 itens por solicitação por padrão.  Você pode alterar o tamanho de paginação aumentando o tamanho máximo de página no cliente e no servidor.  Para aumentar o tamanho de página solicitada, especifique `PullOptions` ao usar `PullAsync()`:

    PullOptions pullOptions = new PullOptions
        {
            MaxPageSize = 100
        };

Presumindo que você fez o `PageSize` igual a ou maior que 100 dentro do servidor, uma solicitação retornará até 100 itens.

##<a name="#offlinesync"></a>Trabalhar com tabelas Offline

Tabelas offline usam um SQLite armazenar para armazenamento de dados locais para uso quando estiver offline.  Tabela de todas as operações são feitas contra local SQLite armazena em vez de armazenamento do servidor remoto.  Para criar uma tabela offline, primeiro prepare seu projeto:

1. No Visual Studio, clique com botão direito a solução > **Gerenciar pacotes NuGet para solução …**, e em seguida, procurar e instalar o pacote de NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** para todos os projetos na solução.

2. (Opcional) Para oferecer suporte a dispositivos Windows, instale um dos seguintes SQLite runtime pacotes:

    * **Tempo de execução do Windows 8.1:** Instalar [SQLite para o Windows 8.1][3].
    * **Windows Phone 8.1:** Instalar [SQLite para Windows Phone 8.1][4].
    * **Plataforma Windows universal** Instalar [SQLite universal do Windows Universal][5].

3. (Opcional). Para dispositivos do Windows, clique em **referências** > **Adicionar referência …**, expanda a pasta do **Windows** > **extensões**, em seguida, habilitar o apropriado **SQLite para Windows** SDK junto com o **Visual C++ 2013 Runtime para o Windows** SDK.
    Os nomes de SQLite SDK variam um pouco com cada plataforma Windows.

Antes de uma referência de tabela pode ser criada, o armazenamento local deve estar preparado:

    var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
    store.DefineTable<TodoItem>();

    //Initializes the SyncContext using the default IMobileServiceSyncHandler.
    await this.client.SyncContext.InitializeAsync(store);

Inicialização de armazenamento normalmente é feita imediatamente após o cliente é criado.  O **OfflineDbPath** deve ser um nome de arquivo adequado para uso em todas as plataformas compatíveis com você.  Se o caminho é um caminho totalmente qualificado (ou seja, ele começa com uma barra), e em seguida, ele será usado.  Se o caminho não é totalmente qualificado, o arquivo é colocado em um local específico da plataforma.

* Para dispositivos iOS e Android, o caminho padrão é a pasta "Arquivos pessoais".
* Para dispositivos Windows, o caminho padrão é a pasta de "AppData" específicos do aplicativo.

Uma referência de tabela pode ser obtida usando o `GetSyncTable<>` método:

    var table = client.GetSyncTable<TodoItem>();

Você não precisa se autenticar para usar uma tabela offline.  Você só precisa autenticar quando você está se comunicando com o serviço de back-end.

###<a name="syncoffline"></a>Sincronização de uma tabela Offline

Tabelas offline não são sincronizadas com o back-end por padrão.  Sincronização é dividida em duas partes.  Você pode enviar alterações separadamente baixem novos itens.  Aqui está um método de sincronização típica:

    public async Task SyncAsync()
    {
        ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

        try
        {
            await this.client.SyncContext.PushAsync();

            await this.todoTable.PullAsync(
                //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
                //Use a different query name for each unique query in your program
                "allTodoItems",
                this.todoTable.CreateQuery());
        }
        catch (MobileServicePushFailedException exc)
        {
            if (exc.PushResult != null)
            {
                syncErrors = exc.PushResult.Errors;
            }
        }

        // Simple error/conflict handling. A real application would handle the various errors like network conditions,
        // server conflicts and others via the IMobileServiceSyncHandler.
        if (syncErrors != null)
        {
            foreach (var error in syncErrors)
            {
                if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                {
                    //Update failed, reverting to server's copy.
                    await error.CancelAndUpdateItemAsync(error.Result);
                }
                else
                {
                    // Discard local change.
                    await error.CancelAndDiscardItemAsync();
                }

                Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
            }
        }
    }

Se o primeiro argumento para `PullAsync` for nulo, a sincronização incremental não é usado.  Cada operação de sincronização recupera todos os registros.

O SDK executa um implícito `PushAsync()` antes retirando registros.

Manipulação de conflito ocorre em uma `PullAsync()` método.  Você pode lidar com conflitos da mesma maneira como tabelas online.  O conflito é produzido quando `PullAsync()` é chamado em vez de durante as insert, update ou delete. Caso vários conflitos, eles estão agrupados em um único MobileServicePushFailedException.  Lidar com cada falha separadamente.

##<a name="#customapi"></a>Trabalhar com uma API personalizada

Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapear para uma inserção, atualizar, excluir ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definindo cabeçalhos de mensagem HTTP e definir um formato de corpo da mensagem diferente JSON.

Você chama uma API personalizada chamando um dos métodos [InvokeApiAsync] no cliente. Por exemplo, a seguinte linha de código envia uma solicitação POST para o **completeAll** API no back-end:

    var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);

Este formulário é uma chamada de método digitado e requer que o tipo de retorno de **MarkAllResult** é definido. Métodos digitados e sem tipo são suportados.

##<a name="authentication"></a>Autenticar usuários

Aplicativos Mobile dá suporte a autenticação e a autorização de usuários de aplicativo usando vários provedores de identidade externa: Facebook, Google, Account da Microsoft, Twitter e Azure Active Directory. Você pode definir permissões em tabelas para restringir o acesso para operações específicas somente para usuários autenticados. Você também pode usar a identidade dos usuários autenticados implementar regras de autorização em scripts de servidor. Para obter mais informações, consulte o tutorial [autenticação de adicionar ao seu aplicativo].

Dois fluxos de autenticação com suporte: fluxo _gerenciadas pelo cliente_ e _servidor gerenciado_ . O fluxo de servidor gerenciado fornece a experiência de autenticação mais simples, pois depende de interface de autenticação do provedor da web. O fluxo de cliente gerenciado possibilita maior integração com recursos específicos para dispositivos pois depende de SDKs de dispositivo específico específicas do provedor.

>[AZURE.NOTE] É recomendável usar um fluxo de cliente gerenciado em seus aplicativos de produção.

Para configurar a autenticação, você deve registrar seu aplicativo com um ou mais provedores de identidade.  O provedor de identidade gera uma ID de cliente e um segredo de cliente para seu aplicativo.  Depois, esses valores são definidos no seu back-end para habilitar o serviço de aplicativo do Azure autenticação/autorização.  Para obter mais informações, siga as instruções detalhadas no tutorial [autenticação de adicionar ao seu aplicativo].

Os tópicos a seguir são descritos nesta seção:

+ [Autenticação de cliente gerenciado](#clientflow)
+ [Autenticação de servidor gerenciado](#serverflow)
+ [O símbolo de autenticação de cache](#caching)

###<a name="clientflow"></a>Autenticação de cliente gerenciado

Seu aplicativo pode independentemente entre em contato com o provedor de identidade e fornecer o token retornado durante o logon com seu back-end. Este fluxo de cliente permite que você forneça uma experiência de logon único para usuários ou para recuperar dados de usuário adicionais do provedor de identidade. Autenticação de fluxo de cliente é preferida para usar um fluxo de servidor como o provedor de identidade SDK fornece uma aparência mais nativa do UX e permite a personalização adicional.

Exemplos são fornecidos para os seguintes padrões de autenticação de fluxo de cliente:

+ [Biblioteca de autenticação do Active Directory](#adal)
+ [Facebook ou do Google](#client-facebook)
+ [SDK do Live](#client-livesdk)

#### <a name="adal"></a>Autenticar usuários com a biblioteca de autenticação do Active Directory

Você pode usar a biblioteca de autenticação do Active Directory (ADAL) para autenticação do usuário iniciação do cliente usando a autenticação do Active Directory do Azure.

1. Configure seu back-end do aplicativo móvel para logon AAD seguindo o tutorial de [como configurar o serviço de aplicativo para o login no Active Directory] . Certifique-se concluir a etapa opcional de registro de um aplicativo cliente nativo.
2. No Visual Studio ou Xamarin Studio, abra seu projeto e adicione uma referência para o `Microsoft.IdentityModel.CLients.ActiveDirectory` pacote NuGet. Ao pesquisar, inclua versões de pré-lançamento.
3. Adicione o seguinte código ao seu aplicativo, de acordo com a plataforma que você está usando. Em cada um, verifique as seguintes substituições:

    * Substitua o nome do locatário na qual você provisionado seu aplicativo **Inserir-autoridade-HERE** . O formato deve ser https://login.windows.net/contoso.onmicrosoft.com. Este valor pode ser copiado a partir da guia de domínio no seu Azure Active Directory do [Azure portal clássico].
    * Substitua **Inserir-recurso-ID-HERE** com a ID do cliente para seu back-end do aplicativo móvel. Você pode obter a ID do cliente da guia **Avançado** em **Configurações do Azure Active Directory** no portal.
    * Substitua **Inserir-cliente-ID-HERE** com a ID do cliente que você copiou do aplicativo cliente nativo.
    * Substitua **Inserir-REDIRECT-URI-HERE** com ponto de extremidade de _/.auth/login/done_ do seu site, usando o esquema HTTPS. Este valor deve ser semelhante ao _https://contoso.azurewebsites.net/.auth/login/done_.

    O código necessário para cada plataforma segue:

    **Windows:**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            while (user == null)
            {
                string message;
                try
                {
                    AuthenticationContext ac = new AuthenticationContext(authority);
                    AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                        new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                    JObject payload = new JObject();
                    payload["access_token"] = ar.AccessToken;
                    user = await App.MobileService.LoginAsync(
                        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    **Xamarin.iOS**

        private MobileServiceUser user;
        private async Task AuthenticateAsync(UIViewController view)
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(view));
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await client.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    **Xamarin.Android**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(this));
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await client.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
            }
            catch (Exception ex)
            {
                AlertDialog.Builder builder = new AlertDialog.Builder(this);
                builder.SetMessage(ex.Message);
                builder.SetTitle("You must log in. Login Required");
                builder.Create().Show();
            }
        }
        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);
            AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
        }

####<a name="client-facebook"></a>Logon único usando um token do Facebook ou do Google

Você pode usar o fluxo de cliente, conforme mostrado neste trecho de Facebook ou Google.

    var token = new JObject();
    // Replace access_token_value with actual value of your access token obtained
    // using the Facebook or Google SDK.
    token.Add("access_token", "access_token_value");

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        while (user == null)
        {
            string message;
            try
            {
                // Change MobileServiceAuthenticationProvider.Facebook
                // to MobileServiceAuthenticationProvider.Google if using Google auth.
                user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

####<a name="client-livesdk"></a>Único entrar usando Account da Microsoft com o SDK do Live

Para autenticar usuários, você deve registrar seu aplicativo na conta Microsoft Developer Center. Configure os detalhes do registro em seu back-end do aplicativo Mobile. Para criar um registro de conta da Microsoft e conecte-o ao seu back-end do aplicativo móvel, conclua as etapas em [registrar seu aplicativo para usar um logon de conta da Microsoft]. Se você tiver as versões de 8/Silverlight da Windows Store e do Windows Phone do aplicativo, registre a versão do Windows Store primeiro.

O seguinte código autentica usando o SDK do Live e usa o token retornado para entrar no seu back-end do aplicativo Mobile.

    private LiveConnectSession session;
    //private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the Mobile App backend.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
            // The wl.basic scope should always be requested.  Other scopes can be added
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to App Service.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }

Para obter mais informações, consulte a documentação do [SDK do Windows Live] .

###<a name="serverflow"></a>Autenticação de servidor gerenciado

Depois que você registrou seu provedor de identidade, chame o método de [LoginAsync] em [MobileServiceClient] com o valor de [MobileServiceAuthenticationProvider] do seu provedor. Por exemplo, o seguinte código inicia um servidor fluxo-entrar usando o Facebook.

    private MobileServiceUser user;
    private async System.Threading.Tasks.Task Authenticate()
    {
        while (user == null)
        {
            string message;
            try
            {
                user = await client
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

Se você estiver usando um provedor de identidade diferente de Facebook, altere o valor de [MobileServiceAuthenticationProvider] para o valor do seu provedor.

Em um fluxo de servidor, o serviço de aplicativo do Azure gerencia o fluxo de autenticação OAuth exibindo a página de entrada do provedor selecionado.  Depois que os retornos de provedor de identidade, o serviço de aplicativo do Azure gera um token de autenticação do serviço de aplicativo. O método [LoginAsync] retorna um [MobileServiceUser], que fornece a [ID de usuário] do usuário autenticado tanto o [MobileServiceAuthenticationToken], como um símbolo de web JSON (JWT). Esse token pode ser armazenados em cache e reutilizado até que ela expire. Para obter mais informações, consulte [cache o token de autenticação](#caching).

###<a name="caching"></a>O símbolo de autenticação de cache

Em alguns casos, a chamada para o método login pode ser evitada após a primeira autenticação bem-sucedida armazenando o token de autenticação do provedor.  Aplicativos da Windows Store e UWP podem usar [PasswordVault] em cache o token de autenticação atual após um bem-sucedido entrar, da seguinte maneira:

    await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

    PasswordVault vault = new PasswordVault();
    vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
        client.currentUser.MobileServiceAuthenticationToken));

O valor de ID de usuário é armazenado como o nome de usuário da credencial e o token é armazenado como a senha. Em subsequentes iniciante, você pode verificar a **PasswordVault** para credenciais armazenadas em cache. O exemplo a seguir usa credenciais armazenadas em cache quando eles forem encontrados e caso contrário tenta autenticar novamente com o back-end:

    // Try to retrieve stored credentials.
    var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
    if (creds != null)
    {
        // Create the current user from the stored credentials.
        client.currentUser = new MobileServiceUser(creds.UserName);
        client.currentUser.MobileServiceAuthenticationToken =
            vault.Retrieve("Facebook", creds.UserName).Password;
    }
    else
    {
        // Regular login flow and cache the token as shown above.
    }

Quando você sair de um usuário, você deve remover também a credencial armazenada, da seguinte maneira:

    client.Logout();
    vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));

Aplicativos Xamarin usam [Xamarin.Auth] APIs para armazenar as credenciais com segurança em um objeto de **conta** . Para obter um exemplo de como usar essas APIs, consulte o arquivo de código de [AuthStore.cs] na [amostra de compartilhamento de fotos de ContosoMoments].

Quando você usa autenticação de cliente gerenciado, você pode também armazenar em cache o token de acesso obtido de seu provedor como Facebook ou Twitter. Esse token pode ser fornecido para solicitar um novo token de autenticação de back-end, da seguinte maneira:

    var token = new JObject();
    // Replace <your_access_token_value> with actual value of your access token
    token.Add("access_token", "<your_access_token_value>");

    // Authenticate using the access token.
    await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);

##<a name="pushnotifications"></a>Notificações por push

Os tópicos a seguir abordam notificações Push:

* [Registre-se para notificações por Push](#register-for-push)
* [Obter um pacote do Windows Store SID](#package-sid)
* [Registre-se com os modelos de várias plataformas](#register-xplat)

###<a name="register-for-push"></a>Como: registrar para notificações por Push

O cliente de aplicativos Mobile permite que você registrar para notificações de envio com Hubs de notificação do Azure. Ao registrar, obtém uma alça que você obtém do serviço de notificação por Push a específico da plataforma (PNS). Você fornece este valor juntamente com quaisquer marcas quando você cria o registro. O código a seguir registra seu aplicativo do Windows para notificações por push com o serviço de notificação do Windows (WNS):

    private async void InitNotificationsAsync()
    {
        // Request a push notification channel.
        var channel =await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // Register for notifications using the new channel.
        await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
    }

Se você está enviando WNS, você deve [obter um pacote do Windows Store SID](#package-sid).  Para obter mais informações sobre os aplicativos do Windows, incluindo como registrar para registros de modelo, consulte [Adicionar as notificações por push para o seu aplicativo].

Solicitar marcas do cliente não é suportada.  Solicitações de marca são descartadas silenciosamente do registro.
Se você deseja registrar seu dispositivo com marcas, crie uma API personalizada que usa a API de Hubs de notificação para executar o registro em seu nome.  [Chamar a API personalizada](#customapi) em vez da `RegisterNativeAsync()` método.

###<a name="package-sid"></a>Como: obter um pacote do Windows Store SID

Um pacote SID é necessária para ativar as notificações por push nos aplicativos da Windows Store.  Para receber um pacote SID, registre seu aplicativo Windows Store.

Para obter este valor:

1. No Visual Studio Solution Explorer, clique com botão direito do projeto de aplicativo da Windows Store, clique em **repositório** > **Associar o aplicativo com o armazenamento de...**.
2. No assistente, clique em **Avançar**, entrar com sua conta da Microsoft, digite um nome para seu aplicativo em **reserva um novo nome de aplicativo**e clique em **reserva**.
3. Depois que o registro de aplicativo é criado com êxito, selecione o nome do aplicativo, clique em **Avançar**e, em seguida, clique em **associar**.
4. Faça logon no [Centro de desenvolvimento do Windows] usando seu Account da Microsoft. Em **Meus aplicativos**, clique no registro de aplicativo que você criou.
5. Clique em **gerenciamento de aplicativo** > **identidade de aplicativo**e, em seguida, role para baixo até encontrar seu **Pacote SID**.

Muitos usos do pacote SID tratam como um URI, caso em que você precisa usar _ms-app: / /_ do esquema. Anote a versão do seu pacote SID formado concatenando esse valor como um prefixo.

Aplicativos de Xamarin requerem algum código adicional sejam capazes de registrar um aplicativo em execução no iOS ou Android plataformas. Para obter mais informações, consulte o tópico para sua plataforma:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push)

###<a name="register-xplat"></a>Como: registrar modelos de envio enviar notificações de várias plataformas

Para registrar modelos, use o `RegisterAsync()` método com os modelos, da seguinte forma:

        JObject templates = myTemplates();
        MobileService.GetPush().RegisterAsync(channel.Uri, templates);

Seus modelos devem ser `JObject` tipos e podem conter vários modelos no seguinte formato JSON:

        public JObject myTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

            var templates = new JObject
            {
                ["generic-message"] = new JObject
                {
                    ["body"] = template,
                    ["headers"] = new JObject
                    {
                        ["X-WNS-Type"] = "wns/toast"
                    },
                    ["tags"] = new JArray()
                },
                ["more-templates"] = new JObject {...}
            };
            return templates;
        }

O método **RegisterAsync()** também aceita blocos secundário:

        MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);

Todas as marcas são removidas ausente durante o registro de segurança. Para adicionar marcas a instalações ou modelos dentro de instalações, consulte [trabalhar com o servidor de back-end do .NET SDK para aplicativos do Azure móvel].

Para enviar notificações utilizando esses modelos registrados, consulte as [APIs de Hubs de notificação].

##<a name="misc"></a>Tópicos de diversos

###<a name="errors"></a>Como: tratar erros

Quando ocorre um erro no back-end, o cliente SDK gera uma `MobileServiceInvalidOperationException`.  O exemplo a seguir mostra como lidar com uma exceção retornado pelo back-end:

    private async void InsertTodoItem(TodoItem todoItem)
    {
        // This code inserts a new TodoItem into the database. When the operation completes
        // and App Service has assigned an Id, the item is added to the CollectionView
        try
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);
        }
        catch (MobileServiceInvalidOperationException e)
        {
            // Handle error
        }
    }

Outro exemplo de lidar com condições de erro pode ser encontrado na [Amostra de arquivos de aplicativos móveis]. O exemplo [LoggingHandler] fornece um manipulador de representante de log (seguinte) para registrar as solicitações feitas no back-end.

###<a name="headers"></a>Como: personalizar solicitações de cabeçalhos

Para dar suporte ao seu cenário de aplicativo específico, talvez seja necessário personalizar a comunicação com o back-end do aplicativo Mobile. Por exemplo, talvez você queira adicionar um cabeçalho personalizado para cada solicitação de saída ou até mesmo alterar códigos de status de respostas. Você pode usar um personalizado [DelegatingHandler], como no exemplo seguinte:

    public async Task CallClientWithHandler()
    {
        HttpResponseMessage[]
        MobileServiceClient client = new MobileServiceClient("AppUrl",
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await todoTable.InsertAsync(newItem);
    }

    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage>
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Change the request-side here based on the HttpRequestMessage
            request.Headers.Add("x-my-header", "my value");

            // Do the request
            var response = await base.SendAsync(request, cancellationToken);

            // Change the response-side here based on the HttpResponseMessage

            // Return the modified response
            return response;
        }
    }


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/en-us/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: http://www.symbolsource.org/
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Adicione autenticação ao seu aplicativo]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Sincronização de dados offline em aplicativos móveis do Azure]: app-service-mobile-offline-data-sync.md
[Adicionar notificações por push para o seu aplicativo]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Registrar seu aplicativo para usar um logon de conta da Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Como configurar o serviço de aplicativo de logon do Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/en-us/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/en-us/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[Pode ser obtida]: https://msdn.microsoft.com/en-us/library/azure/jj554275(v=azure.10).aspx
[cria uma referência a uma tabela sem tipo]: https://msdn.microsoft.com/en-us/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/en-us/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/en-us/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/en-us/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/en-us/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/en-us/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/en-us/library/azure/mt691741(v=azure.10).aspx
[Leve]: https://msdn.microsoft.com/en-us/library/azure/dn250574(v=azure.10).aspx
[Selecione]: https://msdn.microsoft.com/en-us/library/azure/dn250569(v=azure.10).aspx
[Ignorar]: https://msdn.microsoft.com/en-us/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/en-us/library/azure/dn250536.(v=azure.10)aspx
[ID de usuário]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Onde]: https://msdn.microsoft.com/en-us/library/azure/dn250579(v=azure.10).aspx
[Portal do Azure]: https://portal.azure.com/
[Azure portal clássico]: https://manage.windowsazure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/en-us/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Centro de desenvolvimento do Windows]: https://dev.windows.com/en-us/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[SDK do Windows Live]: https://msdn.microsoft.com/en-us/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[APIs de Hubs de notificação]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Exemplo de arquivos de aplicativos móveis]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[Documentação do OData v3]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: (https://github.com/azure-appservice-samples/ContosoMoments/blob/dev/src/Mobile/ContosoMoments/Helpers/AuthStore.cs)
[Exemplo de compartilhamento de fotos de ContosoMoments]: https://github.com/azure-appservice-samples/ContosoMoments