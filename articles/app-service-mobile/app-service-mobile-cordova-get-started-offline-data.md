<properties
    pageTitle="Habilitar sincronização offline para seu aplicativo do Azure Mobile (Cordova) | Microsoft Azure"
    description="Saiba como usar o aplicativo de serviço móvel aplicativo cache e sincronização de dados offline em seu aplicativo de Cordova"
    documentationCenter="cordova"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-cordova-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Habilitar a sincronização offline para seu aplicativo móvel do Cordova

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Este tutorial apresenta o recurso de sincronização offline do Azure Mobile aplicativos para Cordova. Sincronização offline permite que os usuários finais interagir com um aplicativo móvel&mdash;exibir, adicionar ou modificar dados&mdash;mesmo quando não há nenhuma conexão de rede. As alterações são armazenadas em um banco de dados local; Depois que o dispositivo estiver online novamente, essas alterações são sincronizadas com o serviço remoto.

Este tutorial baseia-se na solução Cordova início rápido para aplicativos de móvel que você cria quando você concluir o tutorial [Apache Cordova rápido iniciar]. Neste tutorial, você irá atualizar a solução de início rápido para adicionar recursos offline dos aplicativos do Azure Mobile. Nós também realçará o código offline específico no aplicativo.

Para saber mais sobre o recurso de sincronização offline, consulte o tópico [Sincronização de dados Offline em aplicativos móveis do Azure]. Para obter detalhes de uso de APIs, consulte o arquivo [Leiame] o plug-in.

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Adicionar sincronização offline para a solução de início rápido

O código de sincronização offline deve ser adicionado ao aplicativo. Sincronização offline requer o plug-in cordova-sqlite-armazenamento, que é adicionado automaticamente para o seu aplicativo quando o plug-in de aplicativos do Azure Mobile está incluído no projeto. O projeto de início rápido inclui ambas esses plug-ins.

1. No Explorador de solução do Visual Studio, abra js e substitua o seguinte código

        var client,            // Connection to the Azure Mobile App backend
          todoItemTable;      // Reference to a table endpoint on backend

    com este código:

        var client,             // Connection to the Azure Mobile App backend
          todoItemTable, syncContext; // Reference to table and sync context

2. Em seguida, substitua o seguinte código:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    com este código:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

        // Note: Requires at least version 2.0.0-beta6 of the Azure Mobile Apps plugin
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              deleted: 'boolean',
              complete: 'boolean'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    As adições de código anterior inicializar o armazenamento local e definir uma tabela local que coincida com os valores de coluna usados no seu Azure back-end. (Você não precisa incluir todos os valores de coluna nesse código.)

    Você obtenha uma referência para o contexto de sincronização chamando **getSyncContext**. O contexto de sincronização ajuda a preservar relações de tabela rastreando e enviar as alterações em um aplicativo cliente modificou quando **envio** é chamado de todas as tabelas.

3. Atualize a URL do aplicativo para o URL de aplicativo do aplicativo Mobile.

4. Em seguida, substitua este código:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    com este código:

        // todoItemTable = client.getTable('todoitem');

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (serverRecord, clientRecord, pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    O código anterior inicializa o contexto de sincronização e, em seguida, chama getSyncTable (em vez de pode ser obtida) para obter uma referência à tabela de dados local.

    Esta usa código banco de dados local para todas as criar, ler, atualizar e excluir operações de tabela (CRUD).

    Este exemplo executa erro simples manipulação de conflitos de sincronização. Um aplicativo real usaria os diversos erros como condições da rede, conflitos de servidor e outras pessoas. Para obter exemplos de código, consulte a [amostra de sincronização offline].

5. Em seguida, adicione esta função para executar a sincronização real.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Você decide quando push alterações no back-end do aplicativo Mobile chamando **push** no objeto **syncContext** usado pelo cliente. Por exemplo, você pode adicionar uma chamada para **syncBackend** a um botão de manipulador de eventos no aplicativo como um novo botão de sincronização, ou você pode adicionar a chamada para a função **addItemHandler** sincronizar sempre que um novo item é adicionado.

##<a name="offline-sync-considerations"></a>Considerações de sincronização offline

No exemplo, o método de **envio** do **syncContext** apenas é chamado na inicialização do aplicativo na função de retorno de chamada para fazer login.  Em um aplicativo do mundo real, você também pode fazer essa funcionalidade de sincronização acionada manualmente ou quando altera o estado da rede.

Quando a extração é executada em relação a uma tabela que tem as atualizações locais pendentes controladas pelo contexto, essa operação de recepção acionará automaticamente um envio de contexto anterior. Ao atualizar, adicionando e Concluindo itens neste exemplo, você pode omitir a chamada explícita **push** , pois ela pode ser redundante (primeira verificação o [Leiame] para o status atual de recurso).

No código fornecido, todos os registros na tabela todoItem remoto são consultados, mas também é possível filtrar registros, passando uma id de consulta e uma consulta para **envio**. Para obter mais informações, consulte a seção de *Sincronização Incremental* na [Sincronização de dados Offline em aplicativos móveis do Azure].

## <a name="optional-disable-authentication"></a>(Opcional) Desabilitar a autenticação

Se você não configurou já autenticação e não quer configurar a autenticação antes de teste sincronizar offline, comente a função de retorno de chamada para fazer login, mas deixe o código dentro a função de retorno de chamada são removidos.

O código deve ter esta aparência após comentar as linhas de login.

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Agora, o aplicativo será sincronizada com o Azure back-end quando você executar o aplicativo em vez de quando você fizer logon.

## <a name="run-the-client-app"></a>Executar o aplicativo de cliente

Com a sincronização offline agora ativada, agora você pode executar o aplicativo cliente pelo menos uma vez em cada plataforma para preencher o banco de dados de armazenamento local. Posteriormente, você irá simular um cenário offline e modificar os dados no armazenamento local enquanto o aplicativo está offline.

## <a name="optional-test-the-sync-behavior"></a>(Opcional) Teste o comportamento de sincronização

Nesta seção, você irá modificar o projeto de cliente para simular um cenário offline usando uma URL de aplicativo inválidas do seu back-end. Quando você adicionar ou altera itens de dados, essas alterações serão mantidas no armazenamento local, mas não sincronizadas com o armazenamento de dados back-end até que a conexão for estabelecida novamente.

1. No Solution Explorer, abra o arquivo de projeto js e altere a URL do aplicativo para apontar para um URL inválido, como o seguinte:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. No index, atualize o CSP `<meta>` elemento com a mesma URL inválido.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Criar e executar o aplicativo cliente e observe que uma exceção está registrada no console quando o aplicativo tenta sincronizar com o back-end depois de login. Qualquer novos itens que você adicionar serão existe somente no armazenamento local até que ele podem ser enviados no back-end móvel. O aplicativo cliente se comporta como se está conectado ao back-end, suporte que todos criar, ler, atualizar, operações de exclusão (CRUD).

4. Feche o aplicativo e reinicie-o para verificar que os novos itens criados por você são mantidos no repositório local.

5. (Opcional) Use o Visual Studio para exibir sua tabela de banco de dados do Azure SQL para ver se os dados no banco de dados back-end não foi alterado.

    No Visual Studio, abra o **Explorador de servidor**. Navegue até o banco de dados no **Azure**->**Bancos de dados SQL**. Clique com botão direito seu banco de dados e selecione **Abrir no Pesquisador de objetos do SQL Server**. Agora você pode navegar para a sua tabela de banco de dados SQL e seu conteúdo.


## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Opcional) Testar o reconectado à sua móvel back-end

Nesta seção, você irá se reconectar o aplicativo móvel back-end, que simula o aplicativo volte ao estado online. Quando você fizer logon, dados serão sincronizados com seu celular back-end.

1. Reabrir js e corrija a URL do aplicativo para apontar para a URL correta.

2. Reabrir Index e corrigir a URL do aplicativo no CSP `<meta>` elemento.

3. Recriar e executar o aplicativo de cliente. O aplicativo tenta sincronizar com o back-end do aplicativo móvel depois de login. Verifique se que sem exceções efetuou no console de depuração.

4. (Opcional) Exiba os dados atualizados usando o SQL Server Object Explorer ou uma ferramenta de restante como Fiddler. Observe que os dados foram sincronizados entre o banco de dados back-end e o armazenamento local.

    Observe que os dados tiverem sido sincronizados entre o banco de dados e o armazenamento local e contém os itens que você adicionou enquanto seu aplicativo estava desconectado.

## <a name="additional-resources"></a>Recursos adicionais

* [Sincronização de dados offline em aplicativos móveis do Azure]

* [Nuvem capa: sincronização Offline no Azure serviços móveis] \(Observação: o vídeo for em serviços de celular, mas sincronização offline funciona de maneira semelhante em aplicativos móveis do Azure\)

* [Ferramentas do Visual Studio para Apache Cordova]

## <a name="next-steps"></a>Próximas etapas

* Examine os recursos mais avançados de sincronização offline como resolução de conflitos na [amostra de sincronização offline]
* Examine a sincronização offline referência da API do [Leiame]

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Início rápido do Apache Cordova]: app-service-mobile-cordova-get-started.md
[LEIAME]: https://github.com/Azure/azure-mobile-apps-js-client#offline-data-sync-preview
[amostra de sincronização offline]: https://github.com/shrishrirang/azure-mobile-apps-quickstarts/tree/samples/client/cordova/ZUMOAPPNAME
[Sincronização de dados offline em aplicativos móveis do Azure]: app-service-mobile-offline-data-sync.md
[Folha de rosto de nuvem: Sincronização Offline no Azure serviços móveis]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Ferramentas do Visual Studio para Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
