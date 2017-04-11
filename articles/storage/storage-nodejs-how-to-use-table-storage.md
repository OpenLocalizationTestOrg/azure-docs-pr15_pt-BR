<properties
    pageTitle="Como usar o armazenamento de tabela do Azure do Node | Microsoft Azure"
    description="Armazene dados estruturados na nuvem usando o armazenamento de tabela do Azure, um armazenamento de dados NoSQL."
    services="storage"
    documentationCenter="nodejs"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>


# <a name="how-to-use-azure-table-storage-from-nodejs"></a>Como usar o armazenamento de tabela do Azure do Node

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Visão geral

Este tópico mostra como executar cenários comuns usando o serviço de tabela do Azure em um aplicativo de Node.

Os exemplos de código neste tópico pressupõem que você já tiver um aplicativo Node. Para obter informações sobre como criar um aplicativo de Node no Azure, consulte qualquer um destes tópicos:

- [Criar um aplicativo da web de Node no serviço de aplicativo do Azure](../app-service-web/web-sites-nodejs-develop-deploy-mac.md)
- [Construir e implantar um aplicativo de web Node para Azure usando o WebMatrix](../app-service-web/web-sites-nodejs-use-webmatrix.md)
- [Criar e implantar um aplicativo Node para um serviço de nuvem do Azure](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (usando o Windows PowerShell)


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## <a name="configure-your-application-to-access-azure-storage"></a>Configurar seu aplicativo para acessar o armazenamento do Azure

Para usar o armazenamento do Azure, é necessário o SDK de armazenamento do Azure para Node. js, que inclui um conjunto de bibliotecas de conveniência que se comunicar com os serviços de armazenamento restante.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Use o Gerenciador de pacote de nó (NPM) para instalar o pacote

1.  Use uma interface de linha como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix) e navegue até a pasta onde você criou seu aplicativo.

2.  Na janela de comando, digite **npm instalar o armazenamento do azure** . Saída do comando é semelhante ao seguinte exemplo.

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  Você pode executar manualmente o comando **ls** para verificar se um **nó\_módulos** pasta foi criada. Dentro dessa pasta, você encontrará o pacote de **armazenamento do azure** , que contém as bibliotecas que você precisa para acessar o armazenamento.

### <a name="import-the-package"></a>Importar o pacote

Adicione o seguinte código na parte superior do arquivo **Server. js** em seu aplicativo:

    var azure = require('azure-storage');

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma conexão de armazenamento do Azure

O módulo azure lerá as variáveis de ambiente AZURE\_armazenamento\_conta e AZURE\_armazenamento\_acesso\_chave ou AZURE\_armazenamento\_conexão\_cadeia de caracteres para informações necessárias para conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, especifique as informações da conta ao chamar **TableService**.

Para obter um exemplo de definir as variáveis de ambiente no [Portal do Azure](https://portal.azure.com) para um site do Azure, consulte [Node web app usando o serviço de tabela do Azure].

## <a name="create-a-table"></a>Criar uma tabela

O código a seguir cria um objeto de **TableService** e usa para criar uma nova tabela. Adicione o seguinte na parte superior da **Server. js**.

    var tableSvc = azure.createTableService();

A chamada para **createTableIfNotExists** criará uma nova tabela com o nome especificado, se ele ainda não exista. O exemplo a seguir cria uma nova tabela Minha_tabela '' se ele ainda não existir:

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
      if(!error){
        // Table exists or created
      }
    });

O `result.created` será `true` se uma nova tabela é criada, e `false` se a tabela já existe. O `response` conterá informações sobre a solicitação.

### <a name="filters"></a>Filtros

Operações de filtragem opcionais podem ser aplicadas a operações executadas usando **TableService**. Operações de filtragem pode incluir log automaticamente repetindo, etc. Filtros são objetos que implementam um método com a assinatura:

    function handle (requestOptions, next)

Depois de fazer seu pré-processamento na lista Opções de solicitação, o método precisa chamar "next", passando um retorno de chamada com a assinatura a seguir:

    function (returnObject, finalCallback, next)

Esse retorno de chamada e, depois de processar a returnObject (a resposta da solicitação de servidor), o retorno de chamada precisa para invocar Avançar se existir para continuar processando outros filtros ou simplesmente invocar finalCallback caso contrário, para encerrar a chamada de serviço.

Dois filtros que implementam lógica de repetição são fornecidos com o SDK do Azure para Node, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. A seguir cria um objeto de **TableService** que usa o **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var tableSvc = azure.createTableService().withFilter(retryOperations);

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade, primeiro crie um objeto que define as propriedades de entidade. Todas as entidades devem conter um **PartitionKey** e **RowKey**, que são identificadores exclusivos para a entidade.

* **PartitionKey** - determina a partição a entidade armazenado em

* **RowKey** - identifica exclusivamente a entidade dentro da partição

**PartitionKey** e **RowKey** devem ser valores de cadeia de caracteres. Para obter mais informações, consulte [Noções básicas sobre o modelo de dados do serviço de tabela](http://msdn.microsoft.com/library/azure/dd179338.aspx).

A seguir é um exemplo de definir uma entidade. Observe que **exemplo** é definida como um tipo de **Edm.DateTime**. Especificar o tipo é opcional, e tipos vai ser inferidos se não for especificados.

    var task = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
    };

> [AZURE.NOTE] Também há um campo de **carimbo de hora** para cada registro, que é definido por Azure quando uma entidade é inserida ou atualizada.

Você também pode usar o **entityGenerator** para criar entidades. O exemplo a seguir cria a mesma entidade de tarefas usando o **entityGenerator**.

    var entGen = azure.TableUtilities.entityGenerator;
    var task = {
      PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

Para adicionar uma entidade à sua tabela, passe o objeto de entidade para o método **insertEntity** .

    tableSvc.insertEntity('mytable',task, function (error, result, response) {
      if(!error){
        // Entity inserted
      }
    });

Se a operação for bem-sucedida, `result` conterá a [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) do registro inserido e `response` conterá informações sobre a operação.

Resposta de exemplo:

    { '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }

> [AZURE.NOTE] Por padrão, **insertEntity** não retornar a entidade inserida como parte do `response` informações. Se você planeja executar outras operações nesta entidade ou deseja as informações em cache, podem ser úteis para que ele retornado como parte do `result`. Você pode fazer isso habilitando **echoContent** da seguinte maneira:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-an-entity"></a>Atualizar uma entidade

Há vários métodos disponíveis para atualizar uma entidade existente:

* **replaceEntity** - atualiza uma entidade existente, substituindo-

* **mergeEntity** - atualiza uma entidade existente mesclando novos valores de propriedade para a entidade existente

* **insertOrReplaceEntity** - atualiza uma entidade existente, substituindo. Se nenhuma entidade existir, um novo será inserido

* **insertOrMergeEntity** - atualiza uma entidade existente mesclando novos valores de propriedade em existente. Se nenhuma entidade existir, um novo será inserido

O exemplo a seguir demonstra atualizando uma entidade usando **replaceEntity**:

    tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [AZURE.NOTE] Por padrão, atualizar uma entidade não verificar se os dados sendo atualizados anteriormente foi modificados por outro processo. Para dar suporte a atualizações simultâneas:
>
> 1. Obtenha a ETag do objeto sendo atualizado. Isso é retornado como parte do `response` para qualquer entidade relacionados à operação e podem ser recuperada por meio de `response['.metadata'].etag`.
>
> 2. Ao executar uma operação de atualização em uma entidade, adicione as informações de ETag recuperadas anteriormente para a nova entidade. Por exemplo:
>
>     `entity2['.metadata'].etag = currentEtag;`
>
> 3. Execute a operação de atualização. Se a entidade foi modificada desde que você recuperou o valor de ETag, como outra instância do seu aplicativo, uma `error` será retornado informando que a condição de atualização especificada na solicitação não foi atendida.

Com **replaceEntity** e **mergeEntity**, se a entidade que está sendo atualizada não existir, em seguida, a operação de atualização falhará. Portanto, se quiser armazenar uma entidade independente de se ele já existe, use **insertOrReplaceEntity** ou **insertOrMergeEntity**.

O `result` para atualização bem-sucedida operações conterá a **Etag** da entidade atualizada.

## <a name="work-with-groups-of-entities"></a>Trabalhar com grupos de entidades

Às vezes, faz sentido para submeter várias operações juntos em um lote para garantir atômica processamento pelo servidor. Para fazer isso, use a classe **TableBatch** para criar um lote e, em seguida, use o método de **executeBatch** do **TableService** para executar as operações em lote.

 O exemplo a seguir demonstra duas entidades em um lote de envio:

    var task1 = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'Take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };
    var task2 = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '2'},
      description: {'_':'Wash the dishes'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };

    var batch = new azure.TableBatch();

    batch.insertEntity(task1, {echoContent: true});
    batch.insertEntity(task2, {echoContent: true});

    tableSvc.executeBatch('mytable', batch, function (error, result, response) {
      if(!error) {
        // Batch completed
      }
    });

Para operações de lote bem-sucedido, `result` conterá informações para cada operação no lote.

### <a name="work-with-batched-operations"></a>Trabalhar com operações em lote

Adicionado a um lote de operações podem inspecionar exibindo o `operations` propriedade. Você também pode usar os seguintes métodos para trabalhar com operações:

* **desmarque** - limpa todas as operações de um lote

* **getOperations** - obtém uma operação do lote

* **hasOperations** - retorna verdadeiro se o lote contém operações

* **removeOperations** - remove uma operação

* **tamanho** - retorna o número de operações em lote

## <a name="retrieve-an-entity-by-key"></a>Recuperar uma entidade pela chave

Para retornar uma entidade específica com base na **PartitionKey** e **RowKey**, use o método **retrieveEntity** .

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
      if(!error){
        // result contains the entity
      }
    });

Quando essa operação for concluída, `result` conterá a entidade.

## <a name="query-a-set-of-entities"></a>Consulta de um conjunto de entidades

Para consultar uma tabela, use o objeto **TableQuery** para criar uma expressão de consulta usando as seguintes cláusulas:

* **Selecione** - os campos a ser retornado da consulta

* **onde** - where cláusula

    * **e** - um `and` condição onde

    * **ou** - um `or` condição onde

* **início** - o número de itens para buscar


O exemplo a seguir cria uma consulta que retornará os primeiros cinco itens com um PartitionKey de 'hometasks'.

    var query = new azure.TableQuery()
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

Como **Selecionar** não é usado, todos os campos serão retornados. Para executar a consulta em uma tabela, use **queryEntities**. O exemplo a seguir usa essa consulta para retornar entidades de 'mytable'.

    tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
      if(!error) {
        // query was successful
      }
    });

Se for bem-sucedido, `result.entries` conterá uma matriz de entidades que correspondem à consulta. Se a consulta não conseguiu retornar todas as entidades, `result.continuationToken` será não -*Nulo* e podem ser usados como o terceiro parâmetro de **queryEntities** para recuperar mais resultados. Para a consulta inicial, use *null* para o terceiro parâmetro.

### <a name="query-a-subset-of-entity-properties"></a>Um subconjunto das propriedades de entidade da consulta

Uma consulta a uma tabela pode recuperar apenas alguns campos de uma entidade.
Isso reduz a largura de banda e pode melhorar o desempenho de consulta, especialmente para entidades grandes. Use a cláusula **select** e passar os nomes dos campos a ser retornado. Por exemplo, a seguinte consulta retornará somente os campos de **exemplo** e **Descrição** .

    var query = new azure.TableQuery()
      .select(['description', 'dueDate'])
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

## <a name="delete-an-entity"></a>Excluir uma entidade

Você pode excluir uma entidade usando suas chaves de partição e linha. Neste exemplo, o objeto de **task1** contém os valores **RowKey** e **PartitionKey** da entidade a ser excluída. Em seguida, o objeto é passado para o método **deleteEntity** .

    var task = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'}
    };

    tableSvc.deleteEntity('mytable', task, function(error, response){
      if(!error) {
        // Entity deleted
      }
    });

> [AZURE.NOTE] Considere usar ETags ao excluir itens, para garantir que o item não tenha sido modificado por outro processo. Consulte [atualizar uma entidade](#update-an-entity) para obter informações sobre como usar ETags.

## <a name="delete-a-table"></a>Excluir uma tabela

O código a seguir exclui uma tabela de uma conta de armazenamento.

    tableSvc.deleteTable('mytable', function(error, response){
        if(!error){
            // Table deleted
        }
    });

Se você não tiver certeza de que se existe a tabela, use **deleteTableIfExists**.

## <a name="use-continuation-tokens"></a>Use tokens de continuação

Quando você estiver consultando tabelas para grandes quantidades de resultados, procure por tokens de continuação. Pode haver grandes quantidades de dados disponíveis para sua consulta que você não pode perceber se você não criar reconhecer quando houver um token de continuação.

Os resultados do objeto retornado durante consultando conjuntos de entidades um `continuationToken` propriedade quando esse token está presente. Você pode usar esta ao executar uma consulta para continuar para mover entre as entidades partição e tabela.

Ao consultar, um parâmetro de continuationToken pode ser fornecido entre a instância do objeto de consulta e a função de retorno de chamada:

```
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Se você inspecionar o `continuationToken` objeto, você encontrará propriedades como `nextPartitionKey`, `nextRowKey` e `targetLocation`, que pode ser usado para percorrer todos os resultados.

Também há uma amostra de continuação dentro do repo Node de armazenamento do Azure no GitHub. Procure por `examples/samples/continuationsample.js`.

## <a name="work-with-shared-access-signatures"></a>Trabalhar com assinaturas de acesso compartilhado

Assinaturas de acesso compartilhado (SAS) são uma maneira segura para fornecer acesso granular às tabelas sem fornecer o seu nome de conta de armazenamento ou teclas. SAS muitas vezes são usados para fornecer acesso limitado aos seus dados, como permitir que um aplicativo móvel aos registros de consulta.

Um aplicativo confiável como um serviço baseado em nuvem gera uma SAS usando o **generateSharedAccessSignature** da **TableService**e fornece-o a um aplicativo de confiança parcial ou não confiável como um aplicativo móvel. As associações de segurança é gerada usando uma política, que descreve as datas de início e término durante o qual as associações de segurança é válida, bem como o nível de acesso concedido ao proprietário SAS.

O exemplo a seguir gera uma nova política de acesso compartilhado que permitirá que o proprietário SAS ('r') a tabela de consulta e expira 100 minutos após o horário em que ele é criado.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
    var host = tableSvc.host;

Observe que as informações do host devem ser fornecidas também, pois é necessária quando o proprietário SAS tenta acessar a tabela.

O aplicativo cliente usa as associações de segurança com **TableServiceWithSAS** para executar operações em relação a tabela. O exemplo a seguir se conecta à tabela e executa uma consulta.

    var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
    var query = azure.TableQuery()
      .where('PartitionKey eq ?', 'hometasks');

    sharedTableService.queryEntities(query, null, function(error, result, response) {
      if(!error) {
        // result contains the entities
      }
    });

Desde que as associações de segurança foi gerada com apenas acesso de consulta, se foi feita uma tentativa de inserir, atualizar ou excluir entidades, um erro será retornado.

### <a name="access-control-lists"></a>Listas de controle de acesso

Você também pode usar uma lista de controle de acesso (ACL) para definir a política de acesso uma Associações de. Isso é útil se você quiser permitir que vários clientes acessar a tabela, mas oferece políticas de acesso diferente para cada cliente.

Uma ACL é implementada usando uma matriz de políticas de acesso, com uma identificação associada a cada diretiva. O exemplo a seguir define duas diretivas, uma para 'Usuário1' e outro para 'Usuário2':

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

O exemplo a seguir obtém a ACL atual para a tabela de **hometasks** e adiciona as novas diretivas usando **setTableAcl**. Essa abordagem permite:

    var extend = require('extend');
    tableSvc.getTableAcl('hometasks', function(error, result, response) {
    if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Depois que a ACL tiver sido definida, você poderá criar uma SAS com base na identificação de uma política. O exemplo a seguir cria uma novas associações de segurança para 'Usuário2':

    tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes recursos.

-   [Blog da equipe de armazenamento do azure][].
-   Repositório de [SDK de armazenamento do Azure para nó][] no GitHub.
-   [Central de desenvolvedores do Node](/develop/nodejs/)

  [Armazenamento do Azure SDK para nó]: https://github.com/Azure/azure-storage-node
  [OData.org]: http://www.odata.org/
  [Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: portal.azure.com

  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
  [Blog da equipe de armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Website with WebMatrix]: ../web-sites-nodejs-use-webmatrix.md
  [Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
  [Node web app usando o serviço de tabela do Azure]: ../storage-nodejs-use-table-storage-web-site.md
  [Create and deploy a Node.js application to an Azure website]: ../web-sites-nodejs-develop-deploy-mac.md
