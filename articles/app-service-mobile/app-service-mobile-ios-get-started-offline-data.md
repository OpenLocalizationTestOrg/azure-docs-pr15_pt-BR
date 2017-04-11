<properties
    pageTitle="Habilitar sincronização offline para seu aplicativo do Azure Mobile (iOS)"
    description="Saiba como usar o aplicativo de serviço móvel aplicativos para cache e sincronização de dados offline em seu aplicativo iOS"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="enable-offline-sync-for-your-ios-mobile-app"></a>Habilitar a sincronização offline para seu aplicativo móvel do iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Visão geral

Este tutorial apresenta o recurso de sincronização offline do Azure Mobile aplicativos para iOS. Sincronização offline permite que os usuários finais interagir com um aplicativo móvel&mdash;exibir, adicionar ou modificar dados&mdash;mesmo quando não há nenhuma conexão de rede. As alterações são armazenadas em um banco de dados local; Depois que o dispositivo estiver online novamente, essas alterações são sincronizadas com o back-end remoto.

Se esta for sua primeira experiência com aplicativos do Azure Mobile, primeiro você deve concluir o tutorial [criar um aplicativo do iOS]. Se você não usa o projeto de servidor de início rápido baixado, você deve adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para obter mais informações sobre os pacotes de extensão do servidor, consulte [trabalhar com o servidor de back-end do .NET SDK para aplicativos do Azure móvel](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, consulte o tópico [Sincronização de dados Offline em aplicativos móveis do Azure].

## <a name="review-sync"></a>Revisar o código de sincronização do cliente

O projeto de cliente que você baixou para o tutorial [criar um aplicativo iOS] já contém código de suporte sincronização offline usando um banco de dados local com base em dados Core. Esta seção é um resumo dos quais já está incluído no código de tutorial. Para obter uma visão conceitual do recurso, consulte [Sincronização de dados Offline em aplicativos móveis do Azure].

O recurso de sincronização de sincronização de dados offline dos aplicativos do Azure Mobile permite que os usuários finais interagir com um banco de dados local quando a rede não está acessível. Para usar esses recursos em seu aplicativo, você inicializar o contexto de sincronização de `MSClient` e um armazenamento local de referência. Fazer referência a sua tabela através do `MSSyncTable` interface.

1. No **QSTodoService.m** (objetivo-C) ou **ToDoTableViewController.swift** (Swift), observe o tipo do membro `syncTable` é `MSSyncTable`. Sincronização offline usa esta interface de tabela de sincronização, em vez de `MSTable`. Quando uma tabela de sincronização é usada, todas as operações de ir para o armazenamento local e somente são sincronizadas com o back-end remoto com operações de envio e recebimento explícitas.

    Para obter uma referência a uma tabela de sincronização, use o método `syncTableWithName` na `MSClient`. Para remover a funcionalidade de sincronização offline, use `tableWithName` em vez disso.

2. Antes de quaisquer operações de tabela podem ser executadas, o armazenamento local deve ser inicializado. Veja aqui o código relevante. 
    
    **Objetivo-C**:
    
    No `QSTodoService.init` método:
    
    
            MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
            self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
    
    
    **Rápido**:
    
    No `ToDoTableViewController.viewDidLoad` método:
    
    
            let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
            let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
            self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
            client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
    

    Isso cria um armazenamento local usando a interface `MSCoreDataStore`, que é fornecido no SDK de aplicativos do Mobile. Você pode, em vez de um fornecer um armazenamento local diferente Implementando o `MSSyncContextDataSource` protocolo. 
    
    Além disso, o primeiro parâmetro de `MSSyncContext` é usado para especificar um manipulador de conflito. Como podemos passaram `nil`, chegaremos manipulador de conflito de padrão, que falha em qualquer conflito.
    
3. Agora, vamos executar a operação de sincronização real e obter dados de back-end remoto.

    **Objetivo-C**:
    
    `syncData`primeiro envia novas alterações, em seguida, chama `pullData` para obter dados de back-end remoto. Por sua vez, o método `pullData` obtém novos dados que coincida com uma consulta:
    
    
            -(void)syncData:(QSCompletionBlock)completion
            {
                // push all changes in the sync context, then pull new data
                [self.client.syncContext pushWithCompletion:^(NSError *error) {
                    [self logErrorIfNotNil:error];
                    [self pullData:completion];
                }];
            }
    
            -(void)pullData:(QSCompletionBlock)completion
            {
                MSQuery *query = [self.syncTable query];
    
                // Pulls data from the remote server into the local table.
                // We're pulling all items and filtering in the view
                // query ID is used for incremental sync
                [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
                    [self logErrorIfNotNil:error];
    
                    // Let the caller know that we have finished
                    if (completion != nil) {
                        dispatch_async(dispatch_get_main_queue(), completion);
                    }
                }];
            }
        
        
      **Rápido**:
        
        
        func onRefresh(sender: UIRefreshControl!) {
            UIApplication.sharedApplication().networkActivityIndicatorVisible = true
            
            self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
                (error) -> Void in
                
                UIApplication.sharedApplication().networkActivityIndicatorVisible = false
                
                if error != nil {
                    // A real application would handle various errors like network conditions,
                    // server conflicts, etc via the MSSyncContextDelegate
                    print("Error: \(error!.description)")
                    
                    // We will just discard our changes and keep the servers copy for simplicity
                    if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                        for opError in opErrors {
                            print("Attempted operation to item \(opError.itemId)")
                            if (opError.operation == .Insert || opError.operation == .Delete) {
                                print("Insert/Delete, failed discarding changes")
                                opError.cancelOperationAndDiscardItemWithCompletion(nil)
                            } else {
                                print("Update failed, reverting to server's copy")
                                opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                            }
                        }
                    }
                }
                self.refreshControl?.endRefreshing()
            }
        } 
    
    
    Na versão objetivo-C, em `syncData`, primeiro chamamos `pushWithCompletion` no contexto de sincronização. Este método é um membro da `MSSyncContext` (em vez da sincronização da tabela em si) porque ele enviará as alterações em todas as tabelas. Somente os registros que foram modificados de alguma maneira localmente (por meio de operações CUD) serão enviados para o servidor. Em seguida, o auxiliar `pullData` é chamado, quais chamadas `MSSyncTable.pullWithQuery` para recuperar dados remotos e armazenar no banco de dados local.
    
    Na versão rápida, não há nenhuma chamada para `pushWithCompletion`. Isso ocorre porque a operação de envio não era rigorosamente necessária. Se houver quaisquer alterações pendentes no contexto de sincronização para a tabela que está fazendo uma operação de envio, recepção sempre emite um envio pela primeira vez. No entanto, se você tiver mais de uma tabela de sincronização, é melhor chamar explicitamente push para garantir que tudo seja consistente em tabelas relacionadas.
    
    Em versões de objetivo-C tanto o rápido, o método `pullWithQuery` permite especificar uma consulta para filtrar os registros que você deseja recuperar. Neste exemplo, a consulta recupera apenas todos os registros no controle remoto `TodoItem` tabela.
    
    O segundo parâmetro para `pullWithQuery` é uma ID de consulta que é usada para *sincronização incremental*. Sincronização incremental recupera somente aqueles registros modificados desde a última sincronização, usando o registro `UpdatedAt` carimbo de hora (chamado `updatedAt` no armazenamento local.) O ID de consulta deve ser uma cadeia de caracteres descritiva exclusiva para cada consulta lógica em seu aplicativo. Para desativar essa opção-out de sincronização incremental, passe `nil` como a ID de consulta. Observe que isso pode ser potencialmente ineficaz, desde que ele irá recuperar todos os registros em cada operação de recepção.

5. O objetivo-C aplicativo for sincronizado quando podemos modificar ou adicionar dados, um usuário executa o gesto de atualização e na inicialização. O aplicativo rápido sincroniza quando um usuário executa o gesto de atualização e no lançamento. 

Porque sincroniza o aplicativo sempre dados forem modificado (objetivo-C) ou sempre que o aplicativo é iniciado (objetivo-C & rápido), o aplicativo pressupõe que o usuário está online. Outra seção, podemos irá atualizar o aplicativo para que os usuários podem editar mesmo quando estiverem offline.

## <a name="review-core-data"></a>Revisar o modelo de dados principais

Ao usar o armazenamento de dados Core offline, você precisa definir determinadas tabelas e campos no seu modelo de dados. O aplicativo de amostra já inclui um modelo de dados com o formato correto. Nesta seção, vamos percorrer essas tabelas e como elas são usadas.

- Abra **QSDataModel.xcdatamodeld**. Há quatro tabelas definidas - três que são usadas pelo SDK, e os itens de uma tabela para o todo próprios:     *MS_TableOperations: para controlar os itens que precisam ser sincronizado com o servidor    * MS_TableOperationErrors: para controlar todos os erros que ocorrem durante a sincronização offline     *MS_TableConfig: para controlar a última atualização tempo para a última operação de sincronização para todas as operações de recepção    * TodoItem : Para armazenar os itens de todo. O sistema colunas **criadona**, **updatedAt**e **versão** são propriedades de sistema opcional.

>[AZURE.NOTE] O SDK de aplicativos do Azure Mobile reserva nomes de coluna sendo com "**``**". Você não deve usar esse prefixo em qualquer coisa diferente de colunas do sistema, caso contrário, os nomes de coluna serão modificados ao usar o remote back-end.

- Ao usar o recurso de sincronização offline, você deve definir as tabelas do sistema, conforme mostrado abaixo.

    ### <a name="system-tables"></a>Tabelas do sistema

    **MS_TableOperations**

    ![][defining-core-data-tableoperations-entity]

  	| Atributo  |    Tipo     |
  	|----------- |   ------    |
  	| ID         | Número inteiro de 64  |
  	| itemId     | Cadeia de caracteres      |
  	| Propriedades | Dados binários |
  	| tabela      | Cadeia de caracteres      |
  	| tableKind  | Número inteiro de 16  |

    <br>**MS_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

  	| Atributo  |    Tipo     |
  	|----------- |   ------    |
  	| ID         | Cadeia de caracteres      |
  	| operationId | Número inteiro de 64 |
  	| Propriedades | Dados binários |
  	| tableKind  | Número inteiro de 16  |

    <br>**MS_TableConfig**

    ![][defining-core-data-tableconfig-entity]

  	| Atributo  |    Tipo     |
  	|----------- |   ------    |
  	| ID         | Cadeia de caracteres      |
  	| chave        | Cadeia de caracteres      |
  	| keyType    | Número inteiro de 64  |
  	| tabela      | Cadeia de caracteres      |
  	| valor      | Cadeia de caracteres      |

    ### <a name="data-table"></a>Tabela de dados

    **TodoItem**

  	| Atributo    |  Tipo   | Observação                                                   |
  	|-----------   |  ------ | -------------------------------------------------------|
  	| ID           | Cadeia de caracteres, marcadas como obrigatórias  | chave primária no repositório remoto                            |
  	| Concluir     | Booliano | todo o campo do item                                        |
  	| texto         | Cadeia de caracteres  | todo o campo do item                                        |
  	| criadona | Data    | mapas (opcionais) para a propriedade do sistema de criadona         |
  	| updatedAt | Data    | mapas (opcionais) para a propriedade do sistema de updatedAt         |
  	| Versão   | Cadeia de caracteres  | (opcional) usado para detectar conflitos, mapas à versão |


## <a name="setup-sync"></a>Alterar o comportamento de sincronização do aplicativo

Nesta seção, você irá modificar o aplicativo para que ele não sincronizar no início do aplicativo ou ao inserir e atualizar itens, mas somente quando o botão Atualizar do gesto é executado.

**Objetivo-C**:

1. No **QSTodoListViewController.m**, alterar o método de **viewDidLoad** para remover a chamada para `[self refresh]` no final do método. Agora, os dados não serão sincronizados com o servidor em Iniciar aplicativo, mas em vez disso, será o conteúdo de armazenamento local.

2. Em **QSTodoService.m**, modificar a definição de `addItem` para que ele não sincroniza após o item é inserido. Remover o `self syncData` bloquear e substitua a seguir:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. Modificar a definição de `completeItem` acima; remover o bloco para `self syncData` e substitua a seguir:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

**Rápido**:

1. Em `viewDidLoad` em **ToDoTableViewController.swift**, comente essas duas linhas, para interromper a sincronização em Iniciar aplicativo. No momento da escrita deste artigo, o aplicativo Todo rápido não atualizar o serviço quando alguém adiciona ou conclui um item, apenas no início do aplicativo.

        self.refreshControl?.beginRefreshing()
        self.onRefresh(self.refreshControl)


## <a name="test-app"></a>Testar o aplicativo

Nesta seção, você se conecta a um URL inválido para simular um cenário offline. Quando você adiciona itens de dados, eles serão mantidos no repositório de dados Core local, mas não sincronizados com o back-end móvel.

1. Alterar a URL do aplicativo móvel em **QSTodoService.m** para um URL inválido e execute o aplicativo novamente:

    **Objetivo-C** na QSTodoService.m:
    
            self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
    
    **Rápido** no ToDoTableViewController.swift:

        let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")

2. Adicione alguns itens de todo. Feche o simulator (ou obrigatoriamente o aplicativo) e reiniciar. Verifique se que têm foi persistente suas alterações.

3. Exiba o conteúdo da tabela de TodoItem remoto:

    + Para um back-end Node, vá para o [portal do Azure](https://portal.azure.com/)e em seu aplicativo de celular back-end clique **Tabelas fácil** > **TodoItem** para exibir o conteúdo do `TodoItem` tabela.
    + Para um back-end do .NET, exiba os conteúdos da tabela com uma ferramenta SQL como o SQL Server Management Studio ou um cliente restante como Fiddler ou carteiro.

    Verifique os novos itens *não* foram sincronizadas com o servidor:

4. Alterar a URL para o correto em **QSTodoService.m** e execute novamente o aplicativo. Execute o gesto de atualização, puxe para baixo na lista de itens. Você verá um girar de andamento.

5. Exiba os dados de TodoItem novamente. O TodoItems novas e alteradas deverá aparecer agora.

## <a name="summary"></a>Resumo

Para suporte ao recurso de sincronização offline, usamos o `MSSyncTable` interface e inicializado `MSClient.syncContext` com um armazenamento local. Nesse caso, o armazenamento local foi um banco de dados com base em dados Core.

Ao usar um armazenamento de dados Core local, você deve definir várias tabelas com as [Propriedades do sistema correta](#review-core-data).

As operações de CRUD normais para os aplicativos do Azure Mobile trabalhem como se o aplicativo ainda está conectado, mas todas as operações ocorrem em relação ao armazenamento local.

Quando queremos sincronizar o armazenamento local com o servidor, usamos o `MSSyncTable.pullWithQuery`método.


## <a name="additional-resources"></a>Recursos adicionais

* [Sincronização de dados offline em aplicativos móveis do Azure]

* [Nuvem capa: sincronização Offline no Azure serviços móveis] \(Observação: o vídeo for em serviços de celular, mas sincronização offline funciona de maneira semelhante em aplicativos móveis do Azure\)

<!-- URLs. -->


[Criar um aplicativo do iOS]: app-service-mobile-ios-get-started.md
[Sincronização de dados offline em aplicativos móveis do Azure]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Folha de rosto de nuvem: Sincronização Offline no Azure serviços móveis]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
