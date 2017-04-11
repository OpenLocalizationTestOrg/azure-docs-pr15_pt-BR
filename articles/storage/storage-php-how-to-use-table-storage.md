<properties
    pageTitle="Como usar o armazenamento de tabela do PHP | Microsoft Azure"
    description="Saiba como usar o serviço de tabela do PHP para criar e excluir uma tabela e inserir, excluir e consultar a tabela."
    services="storage"
    documentationCenter="php"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="php"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-php"></a>Como usar o armazenamento de tabela do PHP

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Visão geral

Este guia mostra como executar cenários comuns usando o serviço de tabela do Azure. Os exemplos são gravados no PHP e usar o [SDK do Azure para PHP][download]. Os cenários cobertos incluem **Criando e excluindo uma tabela e inserir, excluir e consultar entidades em uma tabela**. Para obter mais informações sobre o serviço de tabela do Azure, consulte a seção [próximas etapas](#next-steps) .

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Criar um aplicativo PHP

O único requisito para criar um aplicativo PHP que acessa o serviço de tabela do Azure é de referência de classes no SDK do Azure para PHP de dentro do seu código. Você pode usar as ferramentas de desenvolvimento para criar seu aplicativo, incluindo o bloco de notas.

Este guia, use os recursos de serviço de tabela que podem ser chamados de dentro de um aplicativo PHP localmente ou em código em execução dentro de uma função web Azure, uma função de trabalho ou um site.

## <a name="get-the-azure-client-libraries"></a>Obtenha as bibliotecas de cliente Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-table-service"></a>Configurar seu aplicativo para acessar o serviço de tabela

Para usar o serviço de tabela do Azure APIs, você precisa:

1. Fazer referência ao arquivo de carregador usando o [require_once] [ require_once] instrução, e
2. Fazer referência a quaisquer classes que você pode usar.

O exemplo a seguir mostra como incluir o arquivo de carregador e fazer referência à classe **ServicesBuilder** .

> [AZURE.NOTE] Este exemplo (e outros exemplos neste artigo) pressupõem que você instalou as bibliotecas de cliente do PHP para Azure via compositor. Se você instalou as bibliotecas manualmente, você precisará fazer referência a <code>WindowsAzure.php</code> arquivo de carregador automático.

    require_once 'vendor/autoload.php';
    use WindowsAzure\Common\ServicesBuilder;


Nos exemplos a seguir, o `require_once` instrução sempre é mostrada, mas somente as classes necessárias para executar o exemplo referenciadas.

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma conexão de armazenamento do Azure

Para criar uma instância de um cliente do serviço de tabela do Azure, primeiro você deve ter uma cadeia de conexão válida. O formato para a cadeia de conexão de serviço de tabela é:

Para acessar um serviço live:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Para acessar o armazenamento de emulador:

    UseDevelopmentStorage=true


Para criar qualquer cliente de serviço Azure, você precisa usar a classe **ServicesBuilder** . É possível:

* passar a cadeia de conexão diretamente para ele ou
* Use o **CloudConfigurationManager (CCM)** para verificar várias origens externas para a cadeia de conexão:
    * Por padrão, ele vem com suporte para uma fonte externa - variáveis de ambiente
    * Você pode adicionar novas fontes estendendo a classe de **ConnectionStringSource**

Para os exemplos descritos aqui, a cadeia de conexão será passada diretamente.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);


## <a name="create-a-table"></a>Criar uma tabela

Um objeto de **TableRestProxy** permite que você crie uma tabela com o método **createTable** . Ao criar uma tabela, você pode definir o tempo de limite de serviço de tabela. (Para obter mais informações sobre o tempo de limite de serviço de tabela, consulte [Tempos limite de configuração para operações de serviço de tabela][table-service-timeouts].)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Create table.
        $tableRestProxy->createTable("mytable");
    }
    catch(ServiceException $e){
        $code = $e->getCode();
        $error_message = $e->getMessage();
        // Handle exception based on error codes and messages.
        // Error codes and messages can be found here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
    }

Para obter informações sobre restrições em nomes de tabela, consulte [Noções básicas sobre o modelo de dados do serviço de tabela][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade a uma tabela, crie um novo objeto de **entidade** e passe para **TableRestProxy -> insertEntity**. Observe que quando você cria uma entidade, você deverá especificar um `PartitionKey` e `RowKey`. Estes são os identificadores exclusivos para uma entidade e valores que podem ser consultados mais rápidas que outras propriedades de entidade. O sistema usa `PartitionKey` para distribuir automaticamente entidades da tabela sobre muitos de nós de armazenamento. Entidades com a mesma `PartitionKey` são armazenados no mesmo nó. (Executam operações em várias entidades armazenadas no mesmo nó melhor do que em entidades armazenadas em diferentes nós.) O `RowKey` é a identificação exclusiva de uma entidade dentro de uma partição.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $entity = new Entity();
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate",
                         EdmType::DATETIME,
                         new DateTime("2012-11-05T08:15:00-08:00"));
    $entity->addProperty("Location", EdmType::STRING, "Home");

    try{
        $tableRestProxy->insertEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
    }

Para obter informações sobre tipos e as propriedades da tabela, consulte [Noções básicas sobre o modelo de dados do serviço de tabela][table-data-model].

A classe **TableRestProxy** oferece dois métodos alternativos para inserir entidades: **insertOrMergeEntity** e **insertOrReplaceEntity**. Para usar esses métodos, criar uma nova **entidade** e passe-lo como um parâmetro para qualquer um dos métodos. Cada método irá inserir a entidade se ele não existir. Se a entidade já existir, **insertOrMergeEntity** atualiza os valores de propriedade se as propriedades já existirem e adiciona novas propriedades se elas não existirem, enquanto **insertOrReplaceEntity** substitui completamente uma entidade existente. O exemplo a seguir mostra como usar o **insertOrMergeEntity**. Se a entidade com `PartitionKey` "tasksSeattle" e `RowKey` "1" ainda não existir, ele será inserido. No entanto, se ele anteriormente foi inserido (como mostrado no exemplo acima), o `DueDate` propriedade será atualizada e o `Status` propriedade será adicionada. O `Description` e `Location` propriedades também são atualizadas, mas com valores que efetivamente deixar inalterado. Se essas duas últimas propriedades não foram adicionadas conforme mostrado no exemplo, mas existe na entidade de destino, seus valores existentes deve permanecer inalterados.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    //Create new entity.
    $entity = new Entity();

    // PartitionKey and RowKey are required.
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");

    // If entity exists, existing properties are updated with new values and
    // new properties are added. Missing properties are unchanged.
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
    $entity->addProperty("Location", EdmType::STRING, "Home");
    $entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

    try {
        // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
        // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
        $tableRestProxy->insertOrMergeEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }


## <a name="retrieve-a-single-entity"></a>Recuperar uma única entidade

O método **TableRestProxy -> getEntity** permite que você recupere uma única entidade consultando seu `PartitionKey` e `RowKey`. No exemplo abaixo, a chave da partição `tasksSeattle` e linha chave `1` são passados para o método **getEntity** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entity = $result->getEntity();

    echo $entity->getPartitionKey().":".$entity->getRowKey();

## <a name="retrieve-all-entities-in-a-partition"></a>Recuperar todas as entidades em uma partição

Consultas de entidade são criadas usando filtros (para obter mais informações, consulte [tabelas de consulta e entidades][filters]). Para recuperar todas as entidades na partição, use o filtro "PartitionKey eq *nome da partição*". O exemplo a seguir mostra como recuperar todas as entidades no `tasksSeattle` partição passando um filtro para o método **queryEntities** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "PartitionKey eq 'tasksSeattle'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Recuperar um subconjunto de entidades em uma partição

O mesmo padrão usado no exemplo anterior pode ser usado para recuperar qualquer subconjunto de entidades em uma partição. Subconjunto de entidades recuperar são determinadas pelo filtro que você use (para obter mais informações, consulte [tabelas de consulta e entidades][filters]). O exemplo a seguir mostra como usar um filtro para recuperar todas as entidades com uma determinada `Location` e um `DueDate` menor do que uma data especificada.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <a name="retrieve-a-subset-of-entity-properties"></a>Recuperar um subconjunto das propriedades de entidade

Uma consulta pode recuperar um subconjunto de propriedades de entidade. Essa técnica, chamada *projeção*, reduz a largura de banda e pode melhorar o desempenho de consulta, especialmente para entidades grandes. Para especificar uma propriedade a serem recuperados, passe o nome da propriedade para o método de **consulta -> addSelectField** . Você pode chamar esse método várias vezes para adicionar mais propriedades. Depois de executar **TableRestProxy -> queryEntities**, as entidades retornadas só terá as propriedades selecionadas. (O se quiser retornar um subconjunto de entidades de tabela, use um filtro conforme mostrado nas consultas acima.)

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $options = new QueryEntitiesOptions();
    $options->addSelectField("Description");

    try {
        $result = $tableRestProxy->queryEntities("mytable", $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    // All entities in the table are returned, regardless of whether
    // they have the Description field.
    // To limit the results returned, use a filter.
    $entities = $result->getEntities();

    foreach($entities as $entity){
        $description = $entity->getProperty("Description")->getValue();
        echo $description."<br />";
    }

## <a name="update-an-entity"></a>Atualizar uma entidade

Uma entidade existente pode ser atualizada usando os métodos de **entidade -> setProperty** e **entidade -> addProperty** na entidade e então chamar **TableRestProxy -> updateEntity**. O exemplo a seguir recupera uma entidade, modifica uma propriedade, remove outra propriedade e adiciona uma nova propriedade. Observe que você pode remover uma propriedade, definindo seu valor como **Nulo**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);

    $entity = $result->getEntity();

    $entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.

    $entity->setPropertyValue("Location", null); //Removed Location.

    $entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

    try {
        $tableRestProxy->updateEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="delete-an-entity"></a>Excluir uma entidade

Para excluir uma entidade, passe o nome da tabela e a entidade `PartitionKey` e `RowKey` para o método **TableRestProxy -> deleteEntity** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete entity.
        $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Observe que, para verificações de concorrência, você pode definir a Etag para uma entidade sejam excluídos usando o método **DeleteEntityOptions -> setEtag** e passando o objeto **DeleteEntityOptions** para **deleteEntity** como um quarto parâmetro.

## <a name="batch-table-operations"></a>Operações de tabela em lotes

O método **TableRestProxy -> lote** permite executar várias operações em uma única solicitação. O padrão aqui envolve adicionando operações ao objeto **BatchRequest** e, em seguida, passando o objeto **BatchRequest** para o método **TableRestProxy -> lote** . Para adicionar uma operação a um objeto de **BatchRequest** , você pode chamar qualquer um dos seguintes métodos várias vezes:

* **addInsertEntity** (adiciona uma operação de insertEntity)
* **addUpdateEntity** (adiciona uma operação de updateEntity)
* **addMergeEntity** (adiciona uma operação de mergeEntity)
* **addInsertOrReplaceEntity** (adiciona uma operação de insertOrReplaceEntity)
* **addInsertOrMergeEntity** (adiciona uma operação de insertOrMergeEntity)
* **addDeleteEntity** (adiciona uma operação de deleteEntity)

O exemplo a seguir mostra como executar operações de **insertEntity** e **deleteEntity** em uma única solicitação:

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;
    use MicrosoftAzure\Storage\Table\Models\BatchOperations;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    // Create list of batch operation.
    $operations = new BatchOperations();

    $entity1 = new Entity();
    $entity1->setPartitionKey("tasksSeattle");
    $entity1->setRowKey("2");
    $entity1->addProperty("Description", null, "Clean roof gutters.");
    $entity1->addProperty("DueDate",
                          EdmType::DATETIME,
                          new DateTime("2012-11-05T08:15:00-08:00"));
    $entity1->addProperty("Location", EdmType::STRING, "Home");

    // Add operation to list of batch operations.
    $operations->addInsertEntity("mytable", $entity1);

    // Add operation to list of batch operations.
    $operations->addDeleteEntity("mytable", "tasksSeattle", "1");

    try {
        $tableRestProxy->batch($operations);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Para obter mais informações sobre lotes operações de tabela, consulte [Executando transações de grupo de entidade][entity-group-transactions].

## <a name="delete-a-table"></a>Excluir uma tabela

Finalmente, para excluir uma tabela, passe o nome da tabela para o método **TableRestProxy -> tabela** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete table.
        $tableRestProxy->deleteTable("mytable");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu as Noções básicas para o serviço de tabela do Azure, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

- Visite o [blog da equipe de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)

Para obter mais informações, consulte também o [PHP Developer Center](/develop/php/).

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/azure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/azure/dd179338.aspx
[filters]: http://msdn.microsoft.com/library/azure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/azure/dd894038.aspx
