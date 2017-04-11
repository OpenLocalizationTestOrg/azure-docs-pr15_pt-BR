<properties
    pageTitle="Como usar o armazenamento de tabela do Python | Microsoft Azure"
    description="Armazene dados estruturados na nuvem usando o armazenamento de tabela do Azure, um armazenamento de dados NoSQL."
    services="storage"
    documentationCenter="python"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-python"></a>Como usar o armazenamento de tabela do Python

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Visão geral

Este guia mostra como executar cenários comuns usando o serviço de armazenamento de tabela do Azure. Os exemplos são gravados em Python e usam o [Microsoft Azure armazenamento SDK para Python]. Os cenários cobertos incluem criando e excluindo uma tabela, além de inserir e consultando entidades em uma tabela.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-table"></a>Criar uma tabela

O objeto **TableService** permite trabalhar com os serviços de tabela. O código a seguir cria um objeto **TableService** . Adicione o código próximo à parte superior de qualquer arquivo Python no qual você deseja acessar programaticamente o armazenamento do Azure:

    from azure.storage.table import TableService, Entity

O código a seguir cria um objeto **TableService** usando a tecla de nome e uma conta de conta do armazenamento.  Substitua 'Minha conta' e 'mykey' com seu nome de conta e chave.

    table_service = TableService(account_name='myaccount', account_key='mykey')

    table_service.create_table('tasktable')

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade, primeiro crie um dicionário ou entidade que define os valores e nomes de propriedade de entidade. Observe que, para cada entidade, você deverá especificar **PartitionKey** e **RowKey**. Estes são os identificadores exclusivos de suas entidades. Você pode consultar usando esses valores muito mais rápido do que você pode consultar suas outras propriedades. O sistema usa **PartitionKey** para distribuir automaticamente as entidades tabela sobre muitos de nós de armazenamento.
Entidades que têm o mesmo **PartitionKey** são armazenadas no mesmo nó. **RowKey** é a ID exclusiva da entidade dentro da partição qual ele pertence.

Para adicionar uma entidade à sua tabela, passe um objeto de dicionário para o **Inserir\_entidade** método.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
    table_service.insert_entity('tasktable', task)

Você também pode passar uma instância da classe **entidade** para o **Inserir\_entidade** método.

    task = Entity()
    task.PartitionKey = 'tasksSeattle'
    task.RowKey = '2'
    task.description = 'Wash the car'
    task.priority = 100
    table_service.insert_entity('tasktable', task)

## <a name="update-an-entity"></a>Atualizar uma entidade

Este código mostra como substituir a versão antiga de uma entidade existente por uma versão atualizada.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage', 'priority' : 250}
    table_service.update_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

Se a entidade que está sendo atualizada não existir, a operação de atualização falhará. Se você quiser armazenar uma entidade, independentemente se ele era antes, use **Inserir\_ou\_replace_entity**.
No exemplo a seguir, a primeira chamada substituirá a entidade existente. A segunda chamada para inserir uma nova entidade, desde nenhuma entidade com o especificado **PartitionKey** e **RowKey** existe na tabela.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage again', 'priority' : 250}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '3', 'description' : 'Buy detergent', 'priority' : 300}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

## <a name="change-a-group-of-entities"></a>Alterar um grupo de entidades

Às vezes, faz sentido para submeter várias operações juntos em um lote para garantir atômica processamento pelo servidor. Para fazer isso, use a classe **TableBatch** . Quando você deseja enviar o lote, chame **confirmação\_lote**. Observe que todas as entidades devem estar na mesma partição para ser alterado como um lote. O exemplo a seguir adiciona duas entidades juntos em um lote.

    from azure.storage.table import TableBatch
    batch = TableBatch()
    task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
    task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
    batch.insert_entity(task10)
    batch.insert_entity(task11)
    table_service.commit_batch('tasktable', batch)

Lotes também podem ser usados com a sintaxe de Gerenciador de contexto:

    task12 = {'PartitionKey': 'tasksSeattle', 'RowKey': '12', 'description' : 'Go grocery shopping', 'priority' : 400}
    task13 = {'PartitionKey': 'tasksSeattle', 'RowKey': '13', 'description' : 'Clean the bathroom', 'priority' : 100}

    with table_service.batch('tasktable') as batch:
        batch.insert_entity(task12)
        batch.insert_entity(task13)


## <a name="query-for-an-entity"></a>Consulta para uma entidade

Para consultar uma entidade em uma tabela, use o **obter\_entidade** método passando **PartitionKey** e **RowKey**.

    task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
    print(task.description)
    print(task.priority)

## <a name="query-a-set-of-entities"></a>Consulta de um conjunto de entidades

Este exemplo localiza que todas as tarefas em Seattle com base em **PartitionKey**.

    tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
    for task in tasks:
        print(task.description)
        print(task.priority)

## <a name="query-a-subset-of-entity-properties"></a>Um subconjunto das propriedades de entidade da consulta

Uma consulta a uma tabela pode recuperar apenas algumas propriedades de uma entidade.
Essa técnica, chamada *projeção*, reduz a largura de banda e pode melhorar o desempenho de consulta, especialmente para entidades grandes. Use o parâmetro **Selecione** e passar os nomes das propriedades que você deseja recuperar dados para o cliente.

A consulta no código a seguir retorna apenas as descrições de entidades na tabela.

[AZURE.NOTE] O trecho a seguir funciona apenas com o serviço de armazenamento em nuvem. Isso não é suportado pelo emulador armazenamento.

    tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
    for task in tasks:
        print(task.description)

## <a name="delete-an-entity"></a>Excluir uma entidade

Você pode excluir uma entidade usando sua chave de partição e linha.

    table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## <a name="delete-a-table"></a>Excluir uma tabela

O código a seguir exclui uma tabela de uma conta de armazenamento.

    table_service.delete_table('tasktable')

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu as Noções básicas do armazenamento de tabela, siga estes links para saber mais.

- [Central de desenvolvedores do Python](/develop/python/)
- [Serviços de armazenamento do Azure API REST](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog da equipe de armazenamento do Azure]
- [Armazenamento do Microsoft Azure SDK para Python]

[Blog da equipe de armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Armazenamento do Microsoft Azure SDK para Python]: https://github.com/Azure/azure-storage-python
