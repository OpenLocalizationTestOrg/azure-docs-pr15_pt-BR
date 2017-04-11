<properties
    pageTitle="Habilitar sincronização offline para seu aplicativo do Azure Mobile (Xamarin Android)"
    description="Saiba como usar o aplicativo de serviço móvel aplicativo cache e sincronização de dados offline em seu aplicativo Xamarin Android"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Habilitar a sincronização offline para seu aplicativo móvel do Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Visão geral

Este tutorial apresenta o recurso de sincronização offline do Azure Mobile aplicativos para Xamarin.Android. Sincronização offline permite que os usuários finais interagir com um aplicativo móvel – exibir, adicionar ou modificar dados, mesmo quando não há nenhuma conexão de rede. As alterações são armazenadas em um banco de dados local.
Depois que o dispositivo estiver online novamente, essas alterações são sincronizadas com o serviço remoto.

Neste tutorial, você atualizar o projeto de cliente na tutorial [criar um aplicativo do Xamarin Android] para oferecer suporte os recursos offline dos aplicativos do Azure Mobile. Se você não usa o projeto de servidor de início rápido baixado, você deve adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para obter mais informações sobre os pacotes de extensão do servidor, consulte [trabalhar com o servidor de back-end do .NET SDK para aplicativos do Azure móvel](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, consulte o tópico [Sincronização de dados Offline em aplicativos móveis do Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Atualizar o aplicativo cliente para oferecer suporte a recursos offline

Azure recursos offline do aplicativo Mobile permitem que você interaja com um banco de dados local quando você estiver em um cenário offline. Para usar esses recursos em seu aplicativo, você inicializar um [SyncContext] para um armazenamento local. Fazer referência a sua tabela por meio da interface de [IMobileServiceSyncTable] [IMobileServiceSyncTable]. SQLite é usado como o armazenamento local no dispositivo.

1. No Visual Studio, abra o Gerenciador de pacote do NuGet no projeto que você concluiu o tutorial de [criar um aplicativo de Xamarin Android] .  Pesquise e instale o pacote de NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** .

2. Abra o arquivo ToDoActivity.cs e Tire comentários a `#define OFFLINE_SYNC_ENABLED` definição.

3. No Visual Studio, pressione a tecla **F5** para recriar e executar o aplicativo de cliente. O aplicativo funciona da mesma maneira como fazia antes de você habilitou a sincronização offline. No entanto, o banco de dados local agora é preenchido com os dados que podem ser usados em um cenário offline.

## <a name="update-sync"></a>Atualizar o aplicativo desconectar do back-end

Nesta seção, você pode interromper a conexão para seu back-end do aplicativo Mobile para simular uma situação offline. Quando você adiciona itens de dados, o manipulador de exceção informa que o aplicativo está no modo offline. Nesse estado, novos itens adicionados no armazenamento local em são sincronizados com o back-end do aplicativo móvel quando um envio é executado em um estado conectado.

1. Edite ToDoActivity.cs no projeto compartilhado. Altere o **applicationURL** para apontar para um URL inválido:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Você também pode demonstrar comportamento offline desativando wifi e redes de celular no dispositivo ou usando o modo de avião.

2. Pressione **F5** para criar e executar o aplicativo. Observe a sincronização falhou ao atualizar quando o aplicativo é iniciado.

3. Insira novos itens e observe que push falha com um status de [CancelledByNetworkError] cada vez que você clique em **Salvar**. No entanto, os novos itens de todo existirem no armazenamento local até que ele podem ser enviados no back-end do aplicativo móvel.  Em um aplicativo de produção, se você suprimir essas exceções o aplicativo cliente se comporta como se ele ainda está conectado ao aplicativo móvel back-end.

4. Feche o aplicativo e reinicie-o para verificar que os novos itens criados por você são mantidos no repositório local.

5. (Opcional) No Visual Studio, abra o **Explorador de servidor**. Navegue até o banco de dados no **Azure**->**Bancos de dados SQL**. Clique com botão direito seu banco de dados e selecione **Abrir no Pesquisador de objetos do SQL Server**. Agora você pode navegar para a sua tabela de banco de dados SQL e seu conteúdo. Verifique se os dados no banco de dados back-end não foi alterado.

6. (Opcional) Use uma ferramenta de restante como Fiddler ou carteiro para consultar seu back-end móvel, usando uma consulta GET no formulário `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualizar o aplicativo para se reconectar seu back-end do aplicativo móvel

Nesta seção, reconecte o aplicativo no back-end do aplicativo móvel. Quando você executa o aplicativo, o `OnCreate` chamadas de manipulador de eventos `OnRefreshItemsSelected`. Este método chama `SyncAsync` para sincronizar seu armazenamento local com o banco de dados back-end.

1. Abra ToDoActivity.cs no projeto compartilhado e reverter a alteração da propriedade **applicationURL** .

2. Pressione a tecla **F5** para recriar e executar o aplicativo. O aplicativo sincroniza as alterações locais com o aplicativo do Azure Mobile back-end usando operações de envio e recebimento quando o `OnRefreshItemsSelected` método é executado.

3. (Opcional) Exiba os dados atualizados usando o SQL Server Object Explorer ou uma ferramenta de restante como Fiddler. Observe os dados tenha sido sincronizado entre o banco de dados de back-end do aplicativo do Azure Mobile e o armazenamento local.

4. No aplicativo, clique na caixa de seleção ao lado de alguns itens para concluí-las no armazenamento local.

  `CheckItem`chamadas `SyncAsync` item de sincronização cada concluída com o back-end do aplicativo Mobile. `SyncAsync`chama push e pull. **Sempre que você executar uma recepção em relação a uma tabela que o cliente fez alterações, um envio sempre é executado automaticamente**. Isso garante que todas as tabelas no armazenamento local junto com relações permanecem consistentes. Esse comportamento pode resultar em um envio inesperado. Para obter mais informações sobre esse comportamento, consulte [Sincronização de dados Offline em aplicativos móveis do Azure].

## <a name="review-the-client-sync-code"></a>Revisar o código de sincronização do cliente

O projeto de cliente de Xamarin que você baixou quando você concluiu o tutorial [criar um aplicativo do Xamarin Android] já contém código de suporte sincronização offline usando um banco de dados local do SQLite. Aqui está uma visão geral dos que já está incluído no tutorial ode c. Para obter uma visão conceitual do recurso, consulte [Sincronização de dados Offline em aplicativos móveis do Azure].

* Antes de quaisquer operações de tabela podem ser executadas, o armazenamento local deve ser inicializado. O banco de dados do armazenamento local é inicializado quando `ToDoActivity.OnCreate()` executa `ToDoActivity.InitLocalStoreAsync()`. Este método cria um banco de dados SQLite local usando o `MobileServiceSQLiteStore` classe fornecida pelo cliente aplicativos do Azure Mobile SDK.

    O `DefineTable` método cria uma tabela no armazenamento local que corresponda aos campos na fornecido, digite `ToDoItem` nesse caso. O tipo não tem que incluir todas as colunas que estão no banco de dados remoto. É possível armazenar apenas um subconjunto de colunas.

        // ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
                .GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
            // For more details, see http://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }


* O `toDoTable` membro do `ToDoActivity` é do `IMobileServiceSyncTable` digite em vez de `IMobileServiceTable`. O IMobileServiceSyncTable direciona todas criar, ler, atualizar e excluir operações de tabela (CRUD) para o banco de dados de armazenamento local.

    Decidir quando alterações são enviadas no back-end do aplicativo do Azure Mobile chamando `IMobileServiceSyncContext.PushAsync()`. O contexto de sincronização ajuda a preservar relações de tabela rastreando e enviar as alterações em todas as tabelas de um aplicativo cliente modificou quando `PushAsync` é chamado.

    As chamadas de código fornecido `ToDoActivity.SyncAsync()` para sincronização sempre que a lista de todoitem é atualizada ou um todoitem é adicionado ou concluída. Sincroniza o código depois de cada alteração local.

    No código fornecido, todos os registros em remoto `TodoItem` tabela são consultados, mas também é possível filtrar registros, passando uma id de consulta e consulta para `PushAsync`. Para obter mais informações, consulte a seção de *Sincronização Incremental* na [Sincronização de dados Offline em aplicativos móveis do Azure].

        // ToDoActivity.cs
        private async Task SyncAsync()
        {
            try {
                await client.SyncContext.PushAsync();
                await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
            } catch (Java.Net.MalformedURLException) {
                CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
            } catch (Exception e) {
                CreateAndShowDialog (e, "Error");
            }
        }

## <a name="additional-resources"></a>Recursos adicionais

* [Sincronização de dados offline em aplicativos móveis do Azure]
* [Aplicativos móveis Azure .NET SDK HOWTO][8]

<!-- URLs. -->
[Criar um aplicativo do Xamarin Android]: ../app-service-mobile-xamarin-android-get-started.md
[Sincronização de dados offline em aplicativos móveis do Azure]: ../app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Criar um aplicativo do Xamarin Android]: app-service-mobile-xamarin-android-get-started.md
[Sincronização de dados offline em aplicativos móveis do Azure]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
