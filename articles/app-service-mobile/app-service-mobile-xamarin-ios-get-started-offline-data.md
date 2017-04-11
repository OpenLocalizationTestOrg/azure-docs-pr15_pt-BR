<properties
    pageTitle="Habilitar sincronização offline para seu aplicativo do Azure Mobile (Xamarin iOS)"
    description="Saiba como usar o aplicativo de serviço móvel aplicativo cache e sincronização de dados offline em seu aplicativo de iOS Xamarin"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Habilitar a sincronização offline para seu aplicativo móvel do Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Visão geral

Este tutorial apresenta o recurso de sincronização offline do Azure Mobile aplicativos para Xamarin.iOS. Sincronização offline permite que os usuários finais interagir com um aplicativo móvel – exibir, adicionar ou modificar dados, mesmo quando não há nenhuma conexão de rede. As alterações são armazenadas em um banco de dados local. Depois que o dispositivo estiver online novamente, essas alterações são sincronizadas com o serviço remoto.

Neste tutorial, atualize o projeto de aplicativo Xamarin.iOS em [criar um aplicativo do iOS Xamarin] para oferecer suporte os recursos offline dos aplicativos do Azure Mobile. Se você não usa o projeto de servidor de início rápido baixado, você deve adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para obter mais informações sobre os pacotes de extensão do servidor, consulte [trabalhar com o servidor de back-end do .NET SDK para aplicativos do Azure móvel](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, consulte o tópico [Sincronização de dados Offline em aplicativos móveis do Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Atualizar o aplicativo cliente para oferecer suporte a recursos offline

Azure recursos offline do aplicativo Mobile permitem que você interaja com um banco de dados local quando você estiver em um cenário offline. Para usar esses recursos em seu aplicativo, inicialize um [SyncContext] para um armazenamento local. Fazer referência a sua tabela por meio da interface de [IMobileServiceSyncTable]. SQLite é usado como o armazenamento local no dispositivo.

1. Abrir o Gerenciador de pacote do NuGet no projeto que você concluiu o tutorial de [criar um aplicativo do iOS Xamarin] , e em seguida, pesquise e instale o pacote de **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet.

2. Abra o arquivo QSTodoService.cs e Tire comentários a `#define OFFLINE_SYNC_ENABLED` definição.

3. Recriar e executar o aplicativo de cliente. O aplicativo funciona da mesma maneira como fazia antes de você habilitou a sincronização offline. No entanto, o banco de dados local agora é preenchido com os dados que podem ser usados em um cenário offline.

## <a name="update-sync"></a>Atualizar o aplicativo desconectar do back-end

Nesta seção, você pode quebrar a conexão para seu back-end do aplicativo Mobile para simular uma situação offline. Quando você adiciona itens de dados, o manipulador de exceção informa que o aplicativo está no modo offline. Nesse estado, novos itens adicionados no armazenamento local em serão sincronizados com o back-end do aplicativo móvel quando push próximo é executado em um estado conectado.

1. Edite QSToDoService.cs no projeto compartilhado. Altere o **applicationURL** para apontar para um URL inválido:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Você também pode demonstrar comportamento offline desativando wifi e redes de celular no dispositivo ou usando o modo de avião.

2. Criar e executar o aplicativo. Observe a sincronização falhou ao atualizar quando o aplicativo é iniciado.

3. Insira novos itens e observe que push falha com um status de [CancelledByNetworkError] cada vez que você clique em **Salvar**. No entanto, os novos itens de todo existirem no armazenamento local até que ele podem ser enviados no back-end do aplicativo móvel.  Em um aplicativo de produção, se você suprimir essas exceções o aplicativo cliente se comporta como se ele ainda está conectado ao aplicativo móvel back-end.

4. Feche o aplicativo e reinicie-o para verificar que os novos itens criados por você são mantidos no repositório local.

5. (Opcional) Se você tiver o Visual Studio instalado em um PC, abra o **Explorador de servidor**. Navegue até o banco de dados no **Azure**-> **Bancos de dados SQL**. Clique com botão direito seu banco de dados e selecione **Abrir no Pesquisador de objetos do SQL Server**. Agora você pode navegar para a sua tabela de banco de dados SQL e seu conteúdo. Verifique se os dados no banco de dados back-end não foi alterado.

6. (Opcional) Use uma ferramenta de restante como Fiddler ou carteiro para consultar seu back-end móvel, usando uma consulta GET no formulário `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualizar o aplicativo para se reconectar seu back-end do aplicativo móvel

Nesta seção, reconecte o aplicativo no back-end do aplicativo móvel. Isso simula o aplicativo passando do estado offline para um estado online com o back-end do aplicativo móvel.   Se você simulada a quebra de rede desativando a conectividade de rede, nenhuma alteração de código é necessários.
Ative a rede novamente.  Quando você executa o aplicativo, o `RefreshDataAsync` método é chamado. Isso por sua vez chama `SyncAsync` para sincronizar seu armazenamento local com o banco de dados back-end.

1. Abra QSToDoService.cs no projeto compartilhado e reverter a alteração da propriedade **applicationURL** .

2. Recriar e executar o aplicativo. O aplicativo sincroniza as alterações locais com o aplicativo do Azure Mobile back-end usando operações de envio e recebimento quando o `OnRefreshItemsSelected` método é executado.

3. (Opcional) Exiba os dados atualizados usando o SQL Server Object Explorer ou uma ferramenta de restante como Fiddler. Observe os dados tenha sido sincronizado entre o banco de dados de back-end do aplicativo do Azure Mobile e o armazenamento local.

4. No aplicativo, clique na caixa de seleção ao lado de alguns itens para concluí-las no armazenamento local.

  `CompleteItemAsync`chamadas `SyncAsync` item de sincronização cada concluída com o back-end do aplicativo Mobile. `SyncAsync`chama push e pull.
  **Sempre que você executar uma recepção em relação a uma tabela que o cliente fez alterações, um envio no contexto de sincronização do cliente é sempre executado primeiro automaticamente**. O envio implícito garante que todas as tabelas no armazenamento local junto com relações permaneçam consistentes. Para obter mais informações sobre esse comportamento, consulte [Sincronização de dados Offline em aplicativos móveis do Azure].

## <a name="review-the-client-sync-code"></a>Revisar o código de sincronização do cliente

O projeto de cliente de Xamarin que você baixou quando você concluiu o tutorial [criar um aplicativo do iOS Xamarin] já contém código de suporte sincronização offline usando um banco de dados local do SQLite. Aqui está uma breve visão geral do que já está incluído no código de tutorial. Para obter uma visão conceitual do recurso, consulte [Sincronização de dados Offline em aplicativos móveis do Azure].

* Antes de quaisquer operações de tabela podem ser executadas, o armazenamento local deve ser inicializado. O banco de dados do armazenamento local é inicializado quando `QSTodoListViewController.ViewDidLoad()` executa `QSTodoService.InitializeStoreAsync()`. Este método cria um novo local SQLite banco de dados usando o `MobileServiceSQLiteStore` classe fornecida pelo cliente do aplicativo do Azure Mobile SDK.

    O `DefineTable` método cria uma tabela no armazenamento local que corresponda aos campos na fornecido, digite `ToDoItem` nesse caso. O tipo não tem que incluir todas as colunas que estão no banco de dados remoto. É possível armazenar apenas um subconjunto de colunas.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }


* O `todoTable` membro do `QSTodoService` é do `IMobileServiceSyncTable` digite em vez de `IMobileServiceTable`. O IMobileServiceSyncTable direciona todas criar, ler, atualizar e excluir operações de tabela (CRUD) para o banco de dados de armazenamento local.

    Decidir quando essas alterações são enviadas no back-end do aplicativo do Azure Mobile chamando `IMobileServiceSyncContext.PushAsync()`. O contexto de sincronização ajuda a preservar relações de tabela rastreando e enviar as alterações em todas as tabelas de um aplicativo cliente modificou quando `PushAsync` é chamado.

    As chamadas de código fornecido `QSTodoService.SyncAsync()` para sincronização sempre que a lista de todoitem é atualizada ou um todoitem é adicionado ou concluída. Sincroniza o aplicativo depois de cada alteração local. Se uma recepção for executada em uma tabela que tem as atualizações locais pendentes controladas pelo contexto, essa operação de recepção automaticamente irá disparar um envio de contexto primeiro.

    No código fornecido, todos os registros em remoto `TodoItem` tabela são consultados, mas também é possível filtrar registros, passando uma id de consulta e consulta para `PushAsync`. Para obter mais informações, consulte a seção de *Sincronização Incremental* na [Sincronização de dados Offline em aplicativos móveis do Azure].

        // QSTodoService.cs
        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }


## <a name="additional-resources"></a>Recursos adicionais

* [Sincronização de dados offline em aplicativos móveis do Azure]
* [Aplicativos móveis Azure .NET SDK HOWTO][8]

<!-- Images -->

<!-- URLs. -->
[Criar um aplicativo do iOS Xamarin]: app-service-mobile-xamarin-ios-get-started.md
[Sincronização de dados offline em aplicativos móveis do Azure]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md