<properties
    pageTitle="Habilitar a sincronização offline para seu aplicativo de plataforma de Windows Universal (UWP) aos aplicativos Mobile | Serviço de aplicativo do Azure"
    description="Saiba como usar o aplicativo móvel do Azure a cache e sincronização de dados offline em seu aplicativo de plataforma de Windows Universal (UWP)."
    documentationCenter="windows"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-windows-app"></a>Habilitar a sincronização offline para seu aplicativo do Windows

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Visão geral

Este tutorial mostra como adicionar suporte offline um aplicativo de plataforma de Windows Universal (UWP) usando um back-end do aplicativo do Azure Mobile. Sincronização offline permite que os usuários finais interagir com um aplicativo móvel – exibir, adicionar ou modificar dados - mesmo quando não há nenhuma conexão de rede. As alterações são armazenadas em um banco de dados local. Depois que o dispositivo estiver online novamente, essas alterações são sincronizadas com o back-end remoto.

Neste tutorial, você atualizar o projeto de aplicativo UWP na tutorial [criar um aplicativo do Windows] para oferecer suporte os recursos offline dos aplicativos do Azure Mobile. Se você não usa o projeto de servidor de início rápido baixado, você deve adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para obter mais informações sobre os pacotes de extensão do servidor, consulte [trabalhar com o servidor de back-end do .NET SDK para aplicativos do Azure móvel](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, consulte o tópico [Sincronização de dados Offline em aplicativos móveis do Azure].

## <a name="requirements"></a>Requisitos

Este tutorial requer os seguintes pré-requisitos:

* 2013 do Visual Studio em execução no Windows 8.1 ou posterior.
* Conclusão de [criar um aplicativo Windows][criar um aplicativo do windows].
* [Azure Mobile serviços SQLite Store][sqlite store nuget]
* [SQLite para desenvolvimento de plataforma Universal do Windows](http://www.sqlite.org/downloads)

## <a name="update-the-client-app-to-support-offline-features"></a>Atualizar o aplicativo cliente para oferecer suporte a recursos offline

Azure recursos offline do aplicativo Mobile permitem que você interaja com um banco de dados local quando você estiver em um cenário offline. Para usar esses recursos em seu aplicativo, você inicializar um [SyncContext] [ synccontext] para um armazenamento local. Fazer referência a sua tabela por meio da interface de[IMobileServiceSyncTable] [IMobileServiceSyncTable]. SQLite é usado como o armazenamento local no dispositivo.

1. Instale o [tempo de execução de SQLite Universal na plataforma Windows](http://sqlite.org/2016/sqlite-uwp-3120200.vsix).

2. No Visual Studio, abra o Gerenciador de pacote do NuGet para o projeto de aplicativo UWP que você concluiu o tutorial de [criar um aplicativo do Windows] .
    Pesquise e instale o pacote de NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** .

4. No Solution Explorer, clique com botão direito **referências** > **Adicionar referência...**  >  **Windows Universal** > **extensões**, em seguida, habilitar **SQLite para Universal plataforma Windows** e **Visual C++ 2015 Runtime para os aplicativos da plataforma Universal do Windows**.

    ![Adicionar referência SQLite UWP][1]

5. Abra o arquivo MainPage.xaml.cs e Tire comentários a `#define OFFLINE_SYNC_ENABLED` definição.

6. No Visual Studio, pressione a tecla **F5** para recriar e executar o aplicativo de cliente. O aplicativo funciona da mesma maneira como fazia antes de você habilitou a sincronização offline. No entanto, o banco de dados local agora é preenchido com os dados que podem ser usados em um cenário offline.

## <a name="update-sync"></a>Atualizar o aplicativo desconectar do back-end

Nesta seção, você pode quebrar a conexão para seu back-end do aplicativo Mobile para simular uma situação offline. Quando você adiciona itens de dados, o manipulador de exceção informa que o aplicativo está no modo offline. Nesse estado, novos itens adicionados no armazenamento local em serão sincronizados com o back-end do aplicativo móvel quando push próximo é executado em um estado conectado.

1. Edite App.xaml.cs no projeto compartilhado. Comentar a inicialização da **MobileServiceClient** e adicione a seguinte linha, que usa uma URL do aplicativo móvel inválido:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Você também pode demonstrar comportamento offline desativando wifi e redes de celular no dispositivo ou use o modo de avião.

2. Pressione **F5** para criar e executar o aplicativo. Observe a sincronização falhou ao atualizar quando o aplicativo é iniciado.

3. Insira novos itens e observe que push falha com um status de [CancelledByNetworkError] cada vez que você clique em **Salvar**. No entanto, os novos itens de todo existirem no armazenamento local até que ele podem ser enviados no back-end do aplicativo móvel.  Em um aplicativo de produção, se você suprimir essas exceções o aplicativo cliente se comporta como se ele ainda está conectado ao aplicativo móvel back-end.

4. Feche o aplicativo e reinicie-o para verificar que os novos itens criados por você são mantidos no repositório local.

5. (Opcional) No Visual Studio, abra o **Explorador de servidor**. Navegue até o banco de dados no **Azure**->**Bancos de dados SQL**. Clique com botão direito seu banco de dados e selecione **Abrir no Pesquisador de objetos do SQL Server**. Agora você pode navegar para a sua tabela de banco de dados SQL e seu conteúdo. Verifique se os dados no banco de dados back-end não foi alterado.

6. (Opcional) Use uma ferramenta de restante como Fiddler ou carteiro para consultar seu back-end móvel, usando uma consulta GET no formulário `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualizar o aplicativo para se reconectar seu back-end do aplicativo móvel

Nesta seção, você se reconectar o aplicativo no back-end do aplicativo móvel. Essas alterações simular uma reconexão de rede o aplicativo.

Quando você executa o aplicativo, o `OnNavigatedTo` chamadas de manipulador de eventos `InitLocalStoreAsync`. Este método chama `SyncAsync` para sincronizar seu armazenamento local com o banco de dados back-end. O aplicativo tenta sincronizar na inicialização.

1. Abra App.xaml.cs no projeto compartilhado e Tire comentários sua inicialização anterior do `MobileServiceClient` usar correta a URL do aplicativo móvel.

2. Pressione a tecla **F5** para recriar e executar o aplicativo. O aplicativo sincroniza as alterações locais com o aplicativo do Azure Mobile back-end usando operações de envio e recebimento quando o `OnNavigatedTo` executa de manipulador de eventos.

3. (Opcional) Exiba os dados atualizados usando o SQL Server Object Explorer ou uma ferramenta de restante como Fiddler. Observe os dados tenha sido sincronizado entre o banco de dados de back-end do aplicativo do Azure Mobile e o armazenamento local.

4. No aplicativo, clique na caixa de seleção ao lado de alguns itens para concluí-las no armazenamento local.

  `UpdateCheckedTodoItem`chamadas `SyncAsync` item de sincronização cada concluída com o back-end do aplicativo Mobile. `SyncAsync`chama push e pull. No entanto, **sempre que você executar uma recepção em relação a uma tabela que o cliente fez alterações, um envio sempre é executado automaticamente**. Esse comportamento garante que todas as tabelas no armazenamento local junto com relações permanecem consistentes. Esse comportamento pode resultar em um envio inesperado.  Para obter mais informações sobre esse comportamento, consulte [Sincronização de dados Offline em aplicativos móveis do Azure].


##<a name="api-summary"></a>Resumo de API

Para oferecer suporte os recursos offline dos serviços móveis, podemos usado a interface [IMobileServiceSyncTable] e inicializado [MobileServiceClient.SyncContext] [ synccontext] com um banco de dados local do SQLite. Quando estiver offline, as operações de CRUD normais para aplicativos móvel trabalhem como se o aplicativo ainda está conectado enquanto as operações ocorrem em relação ao armazenamento local. Os seguintes métodos são usados para sincronizar o armazenamento local com o servidor:

*  **[PushAsync]** Como esse método é um membro do [IMobileServicesSyncContext], alterações em todas as tabelas são enviadas no back-end. Somente os registros com as alterações locais são enviados ao servidor.

* **[PullAsync] ** 
   uma recepção é iniciada a partir de um [IMobileServiceSyncTable]. Quando houver alterações controladas na tabela, um envio implícito é executado para garantir que todas as tabelas no armazenamento local junto com relações permaneçam consistentes. O parâmetro *pushOtherTables* controla se outras tabelas no contexto são enviadas em um envio implícito. O parâmetro de *consulta* leva uma [IMobileServiceTableQuery<T> ] [ IMobileServiceTableQuery] 
   ou OData cadeia de caracteres de consulta para filtrar os dados retornados. O parâmetro *queryId* é usado para definir a sincronização incremental. Para obter mais informações, consulte  [Sincronização de dados Offline em aplicativos móveis do Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

* **[PurgeAsync]** Seu aplicativo periodicamente deve chamar este método para limpar dados obsoletos do armazenamento local. Use o parâmetro *Forçar* quando você precisar limpar todas as alterações que ainda não foram sincronizadas.

Para obter mais informações sobre esses conceitos, consulte [Sincronização de dados Offline em aplicativos móveis do Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Mais informações

Os tópicos a seguir fornecem informações adicionais sobre o recurso de sincronização offline de aplicativos móveis:

* [Sincronização de dados offline em aplicativos móveis do Azure]
* [Aplicativos móveis Azure .NET SDK HOWTO][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Sincronização de dados offline em aplicativos móveis do Azure]: app-service-mobile-offline-data-sync.md
[criar um aplicativo do windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
