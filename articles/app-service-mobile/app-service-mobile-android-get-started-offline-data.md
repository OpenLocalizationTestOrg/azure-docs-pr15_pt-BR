<properties
    pageTitle="Habilitar sincronização offline para seu aplicativo do Azure Mobile (Android)"
    description="Saiba como usar o aplicativo de serviço móvel aplicativos para cache e sincronização de dados offline em seu aplicativo Android"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Habilitar a sincronização offline para seu aplicativo móvel Android

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Visão geral

Este tutorial apresenta o recurso de sincronização offline de aplicativos do Azure Mobile para Android. Sincronização offline permite aos usuários finais interagir com um aplicativo móvel&mdash;exibir, adicionar ou modificar dados&mdash;mesmo quando não há nenhuma conexão de rede. As alterações são armazenadas em um banco de dados local. Depois que o dispositivo estiver online novamente, essas alterações são sincronizadas com o back-end remoto.

Se esta for sua primeira experiência com aplicativos do Azure Mobile, primeiro você deve concluir o tutorial [criar um aplicativo do Android]. Se você não usa o projeto de servidor de início rápido baixado, você deve adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para obter mais informações sobre os pacotes de extensão do servidor, consulte [trabalhar com o servidor de back-end do .NET SDK para aplicativos do Azure móvel](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, consulte o tópico [Sincronização de dados Offline em aplicativos móveis do Azure].

## <a name="update-the-app-to-support-offline-sync"></a>Atualizar o aplicativo para oferecer suporte à sincronização offline

Com a sincronização offline, você ler e gravar a partir de uma *tabela de sincronização* (usando a interface *IMobileServiceSyncTable* ), que é parte de um banco de dados **SQLite** em seu dispositivo.

Para envio e recebimento alterações entre o dispositivo e os serviços do Azure Mobile, você usa um *contexto de sincronização* (*MobileServiceClient.SyncContext*), que você inicializa com o banco de dados local para armazenar dados localmente.

1. Em `TodoActivity.java`, comente a definição existente de `mToDoTable` e Tire comentários a versão de tabela de sincronização:

        private MobileServiceSyncTable<ToDoItem> mToDoTable;

2. No `onCreate` método, comente a inicialização existente do `mToDoTable` e Tire comentários essa definição:

        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);

3. No `refreshItemsFromTable` comente a definição de `results` e Tire comentários essa definição:

        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();

4. Comente a definição de `refreshItemsFromMobileServiceTable`.

5. Tire comentários a definição de `refreshItemsFromMobileServiceTableSyncTable`:

        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }

6. Tire comentários a definição de `sync`:

        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>Testar o aplicativo

Nesta seção, você testar o comportamento com WiFi na e desative WiFi para criar um cenário offline.

Quando você adiciona itens de dados, elas são mantidas no armazenamento SQLite local, mas não sincronizadas com o serviço móvel até que você pressione o botão de **atualização** . Outros aplicativos podem ter requisitos diferentes sobre quando dados precisam ser sincronizado, mas para fins de demonstração este tutorial tenha o usuário explicitamente solicitá-los.

Quando você pressiona esse botão, inicia uma nova tarefa de plano de fundo. Primeiro, ele envia todas as alterações feitas com o armazenamento local usando o contexto de sincronização, em seguida, alterado recebe todos os dados do Azure à tabela de dados local.

### <a name="offline-testing"></a>Teste off-line

1. Coloque o dispositivo ou simulator no *Modo de avião*. Isso cria um cenário offline.

2. Adicione alguns itens *ToDo* ou marcar alguns itens como concluída. Feche o dispositivo ou simulator (ou obrigatoriamente o aplicativo) e reiniciar. Confirmar que as alterações têm foi persistente no dispositivo porque eles são mantidos no repositório do SQLite local.

3. Exiba o conteúdo da tabela Azure *TodoItem* com um SQL ferramenta como *SQL Server Management Studio*ou um cliente restante como *Fiddler* ou *carteiro*. Verifique os novos itens _não_ foram sincronizadas com o servidor

    + Para um back-end Node, vá para o [portal do Azure](https://portal.azure.com/)e em seu aplicativo de celular back-end clique **Tabelas fácil** > **TodoItem** para exibir o conteúdo do `TodoItem` tabela.
    + Para um back-end do .NET, exiba os conteúdos da tabela com uma ferramenta SQL como o *SQL Server Management Studio*ou um cliente restante como *Fiddler* ou *carteiro*.

4. Ative WiFi no dispositivo ou simulator. Em seguida, pressione o botão **Atualizar** .

5. Exiba os dados de TodoItem novamente no portal do Azure. O TodoItems novas e alteradas deverá aparecer agora.

## <a name="additional-resources"></a>Recursos adicionais

* [Sincronização de dados offline em aplicativos móveis do Azure]

* [Nuvem capa: sincronização Offline no Azure serviços móveis] \(Observação: o vídeo for em serviços de celular, mas sincronização offline funciona de maneira semelhante em aplicativos móveis do Azure\)


<!-- URLs. -->

[Sincronização de dados offline em aplicativos móveis do Azure]: app-service-mobile-offline-data-sync.md

[Criar um aplicativo do Android]: app-service-mobile-android-get-started.md

[Folha de rosto de nuvem: Sincronização Offline no Azure serviços móveis]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

