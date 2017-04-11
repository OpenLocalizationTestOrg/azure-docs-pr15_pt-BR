<properties
    pageTitle="Habilitar sincronização offline para seu aplicativo do Azure Mobile (Xamarin.Forms) | Microsoft Azure"
    description="Saiba como usar o aplicativo de serviço móvel aplicativo cache e sincronização de dados offline em seu aplicativo de Xamarin.Forms"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="yochayk"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Habilitar a sincronização offline para seu aplicativo móvel do Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Visão geral
Este tutorial apresenta o recurso de sincronização offline do Azure Mobile aplicativos para Xamarin.Forms. Sincronização offline permite que os usuários finais interagir com um aplicativo móvel – exibir, adicionar ou modificar dados, mesmo quando não há nenhuma conexão de rede. As alterações são armazenadas em um banco de dados local. Depois que o dispositivo estiver online novamente, essas alterações são sincronizadas com o serviço remoto.

Este tutorial baseia-se na solução Xamarin.Forms início rápido para aplicativos de móvel que você cria quando você concluir o tutorial [criar um aplicativo do iOS Xamarin]. A solução de início rápido para Xamarin.Forms contém o código para oferecer suporte à sincronização offline, que só precisa ser habilitada. Neste tutorial, você atualiza a solução de início rápido para ativar os recursos offline do Azure Mobile aplicativos. Nós também realçar o código offline específico no aplicativo. Se você não usar a solução de início rápido baixado, você deve adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para obter mais informações sobre os pacotes de extensão do servidor, consulte [trabalhar com o servidor de back-end do .NET SDK para aplicativos do Azure móvel][1].

Para saber mais sobre o recurso de sincronização offline, consulte o tópico da [Sincronização de dados Offline em aplicativos móveis do Azure][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Habilitar a funcionalidade de sincronização offline na solução início rápido

O código de sincronização offline está incluído no projeto usando diretivas de pré-processador c#. Quando o **OFFLINE\_sincronização\_ativado** símbolo é definido, estes caminhos de código serão incluídos na compilação. Para aplicativos do Windows, você também deve instalar a plataforma SQLite.

1. No Visual Studio, clique com botão direito a solução > **Gerenciar pacotes NuGet para solução …**, e em seguida, procurar e instalar o pacote de NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** para todos os projetos na solução.

2. No Solution Explorer, abra o arquivo TodoItemManager.cs do projeto com **portátil** no nome, que é o projeto de biblioteca de classes portátil, então não comentar a seguinte diretiva de pré-processador:

        #define OFFLINE_SYNC_ENABLED

4. (Opcional) Para oferecer suporte a dispositivos Windows, instale um dos seguintes SQLite runtime pacotes:

    * **Tempo de execução do Windows 8.1:** Instalar [SQLite para o Windows 8.1][3].
    * **Windows Phone 8.1:** Instalar [SQLite para Windows Phone 8.1][4].
    * **Plataforma Windows universal** Instalar [SQLite universal do Windows Universal][5].

    Embora o início rápido não contiver um projeto Windows Universal, a plataforma Windows Universal é compatível com formulários de Xamarin.

5. (Opcional) Em cada projeto de aplicativo do Windows, clique com botão direito **referências** > **Adicionar referência …**, expanda a pasta do **Windows** > **extensões**.
    Habilite o apropriado **SQLite para Windows** SDK junto com o **Visual C++ 2013 Runtime para o Windows** SDK.
    Os nomes de SQLite SDK variam um pouco com cada plataforma Windows.

## <a name="review-the-client-sync-code"></a>Revisar o código de sincronização do cliente

Aqui está uma breve visão geral do que já está incluído no código de tutorial dentro do `#if OFFLINE_SYNC_ENABLED` diretivas. A funcionalidade de sincronização offline está no arquivo de projeto TodoItemManager.cs do projeto de biblioteca de classes portátil. Para obter uma visão conceitual do recurso, consulte [Sincronização de dados Offline em aplicativos móveis do Azure][2].

* Antes de quaisquer operações de tabela podem ser executadas, o armazenamento local deve ser inicializado. O banco de dados do armazenamento local é inicializado no construtor da classe **TodoItemManager** usando o seguinte código:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Esse código cria um novo local SQLite banco de dados usando a classe **MobileServiceSQLiteStore** .

    O método **DefineTable** cria uma tabela no armazenamento local que corresponda aos campos no tipo fornecido.  O tipo não tem que incluir todas as colunas que estão no banco de dados remoto. É possível armazenar um subconjunto de colunas.

* O campo **todoTable** em **TodoItemManager** é um tipo de **IMobileServiceSyncTable** em vez de **IMobileServiceTable**. Esta usa classe banco de dados local para todas as criar, ler, atualizar e excluir operações de tabela (CRUD). Você decide quando essas alterações são enviadas no back-end do aplicativo Mobile chamando **PushAsync** na **IMobileServiceSyncContext**. O contexto de sincronização ajuda a preservar relações de tabela rastreando e enviar as alterações em um aplicativo cliente modificou quando **PushAsync** é chamado de todas as tabelas.

    O seguinte método **SyncAsync** é chamado para sincronização com o back-end do aplicativo Mobile:

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
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

            // Simple error/conflict handling.
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

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    Este exemplo utiliza simples tratamento de erros com o manipulador de sincronização padrão. Um aplicativo real seria manipular os diversos erros como conflitos de servidor e condições da rede usando uma implementação **IMobileServiceSyncHandler** personalizada.

## <a name="offline-sync-considerations"></a>Considerações de sincronização offline

No exemplo, o método **SyncAsync** é chamado somente na inicialização e quando é solicitada uma sincronização.  Para iniciar uma sincronização em um aplicativo do Android ou iOS, puxe para baixo na lista de itens; para Windows, use o botão de **sincronização** . Em um aplicativo do mundo real, você também pode fazer o disparador de sincronização quando o estado de rede muda.

Quando a extração é executada em uma tabela que tenha pendente atualizações locais acompanhada pelo contexto, que retirar a operação automaticamente disparadores um envio de contexto anterior. Ao atualizar, adicionar e Concluindo itens neste exemplo, você pode omitir a chamada **PushAsync** explícita.

No código fornecido, todos os registros na tabela TodoItem remota são consultados, mas também é possível filtrar registros, passando uma id de consulta e uma consulta para **PushAsync**. Para obter mais informações, consulte a seção de *Sincronização Incremental* na [Sincronização de dados Offline em aplicativos móveis do Azure][2].

## <a name="run-the-client-app"></a>Executar o aplicativo de cliente

Com a sincronização offline agora ativada, execute o aplicativo cliente pelo menos uma vez em cada plataforma para preencher o banco de dados de armazenamento local. Posteriormente, simular um cenário offline e modificar os dados no armazenamento local enquanto o aplicativo está offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Atualizar o comportamento de sincronização do aplicativo cliente

Nesta seção, modifique o projeto de cliente para simular um cenário offline usando uma URL de aplicativo inválidas do seu back-end. Como alternativa, você pode desativar conexões de rede movendo o seu dispositivo para "Modo de avião".  Quando você adicionar ou altera itens de dados, essas alterações são mantidas no armazenamento local, mas não sincronizadas com o armazenamento de dados back-end até que a conexão for estabelecida novamente.

1. No Solution Explorer, abra o arquivo de projeto Constants.cs do projeto **portátil** e altere o valor de `ApplicationURL` para apontar para um URL inválido:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";

2. Abra o arquivo TodoItemManager.cs do projeto **portátil** e adicionar **capturar** para a classe de **exceção** base para o bloco **try... variável** em **SyncAsync**. Este bloco de **capturar** grava a mensagem de exceção no console, da seguinte maneira:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }

3. Criar e executar o aplicativo cliente.  Adicione alguns novos itens. Observe que uma exceção está registrada no console para cada tentativa de sincronizar com o back-end. Esses novos itens existem somente no armazenamento local até que ele podem ser enviados no back-end móvel. O aplicativo cliente se comporta como se ele está conectado ao back-end, suporte que todos criar, ler, atualizar, operações de exclusão (CRUD).

4. Feche o aplicativo e reinicie-o para verificar que os novos itens criados por você são mantidos no repositório local.

5. (Opcional) Use o Visual Studio para exibir sua tabela de banco de dados do Azure SQL para ver se os dados no banco de dados back-end não foi alterado.

    No Visual Studio, abra o **Explorador de servidor**. Navegue até o banco de dados no **Azure**->**Bancos de dados SQL**. Clique com botão direito seu banco de dados e selecione **Abrir no Pesquisador de objetos do SQL Server**. Agora você pode navegar para a sua tabela de banco de dados SQL e seu conteúdo.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Atualizar o aplicativo cliente para se reconectar seu back-end móvel

Nesta seção, reconecte o aplicativo móvel back-end, que simula o aplicativo volte ao estado online. Quando você executa o gesto de atualização, dados são sincronizados com seu celular back-end.

1. Reabra Constants.cs. Corrigir o `applicationURL` para apontar para a URL correta.

2. Recriar e executar o aplicativo de cliente. O aplicativo tenta sincronizar com o back-end do aplicativo móvel após iniciar. Verifique se que sem exceções efetuou no console de depuração.

3. (Opcional) Exibir os dados atualizados usando o SQL Server Object Explorer ou uma ferramenta de restante como Fiddler ou [carteiro][6]. Observe que os dados foram sincronizados entre o banco de dados back-end e o armazenamento local.

    Observe que os dados tiverem sido sincronizados entre o banco de dados e o armazenamento local e contém os itens que você adicionou enquanto seu aplicativo estava desconectado.

## <a name="additional-resources"></a>Recursos adicionais

* [Sincronização de dados offline em aplicativos móveis do Azure][2]
* [Aplicativos móveis Azure .NET SDK HOWTO][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: http://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: http://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: http://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: http://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md