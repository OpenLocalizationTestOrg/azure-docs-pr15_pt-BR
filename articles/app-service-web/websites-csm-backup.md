<properties
    pageTitle="Usar restante para fazer backup e restaurar aplicativos de serviço de aplicativo"
    description="Saiba como usar chamadas API RESTful para fazer backup e restaurar um aplicativo no serviço de aplicativo do Azure"
    services="app-service"
    documentationCenter=""
    authors="NKing92"
    manager="wpickett"
    editor="" />

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="nicking"/>
# <a name="use-rest-to-back-up-and-restore-app-service-apps"></a>Usar restante para fazer backup e restaurar aplicativos de serviço de aplicativo

> [AZURE.SELECTOR]
- [PowerShell](../app-service/app-service-powershell-backup.md)
- [API REST](websites-csm-backup.md)

[Aplicativos de serviço de aplicativo](https://azure.microsoft.com/services/app-service/web/) pode ser feito backup como blobs no armazenamento do Azure. O backup também pode conter bancos de dados do aplicativo. Se o aplicativo for excluído nunca acidentalmente ou se o aplicativo deve ser revertido para uma versão anterior, ele poderá ser restaurado a partir de qualquer backup anterior. Backups podem ser feitos a qualquer momento sob demanda ou backups podem ser agendados em intervalos adequados.

Este artigo explica como fazer backup e restaurar um aplicativo com solicitações de API RESTful. Se você gostaria de criar e gerenciar backups de aplicativo graficamente através do portal Azure, consulte [fazer backup de um aplicativo web no serviço de aplicativo do Azure](web-sites-backup.md)

<a name="gettingstarted"></a>
## <a name="getting-started"></a>Guia de Introdução
Para enviar solicitações REST, você precisa saber o **nome**do seu aplicativo, **grupo de recursos**e **id da assinatura**. Essas informações podem ser encontradas clicando em seu aplicativo na lâmina de **Serviço de aplicativo** do [portal do Azure](https://portal.azure.com). Para os exemplos deste artigo, estamos configurando o site **backuprestoreapiexamples.azurewebsites.net**. Ele está armazenado no grupo de recursos padrão-Web-WestUS e está sendo executado em uma assinatura com a ID 00001111-2222-3333-4444-555566667777.

![Informações do site de amostra][SampleWebsiteInformation]

<a name="backup-restore-rest-api"></a>
## <a name="backup-and-restore-rest-api"></a>Backup e restauração API REST
Agora, vamos abordar vários exemplos de como usar a API REST para fazer backup e restaurar um aplicativo. Cada exemplo inclui uma URL e HTTP no corpo da solicitação. Exemplo de URL contém espaços reservados empacotados em chaves, como {id da assinatura}. Substitua os espaços reservados com as informações correspondentes para o aplicativo. Para referência, aqui está uma explicação de cada espaço reservado que aparece nas URLs de exemplo.

* id da assinatura – ID da assinatura do Azure que contém o aplicativo
* recurso nome do grupo – nome do grupo de recursos que contém o aplicativo
* nome – nome do aplicativo
* id da backup – ID do backup app

Para a documentação completa da API, incluindo vários parâmetros opcionais que podem ser incluídos na solicitação de HTTP, consulte o [Azure Resource Explorer](https://resources.azure.com/).

<a name="backup-on-demand"></a>
## <a name="backup-an-app-on-demand"></a>Faça o backup de um aplicativo sob demanda
Para fazer backup de um aplicativo imediatamente, envie uma solicitação de **POSTAGEM** para **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backup/**.

Veja aqui o URL aparência usando nosso site de exemplo. **https://Management.Azure.com/Subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/backup/**

Fornece um objeto JSON no corpo da sua solicitação para especificar qual conta de armazenamento para usar para armazenar o backup. O objeto JSON deve ter uma propriedade chamada **storageAccountUrl**, que contém uma [URL de SAS](../storage/storage-dotnet-shared-access-signature-part-1.md) concedendo acesso de gravação para o contêiner de armazenamento do Azure que detém o blob backup. Se você quiser fazer backup de seus bancos de dados, você também deve fornecer uma lista que contém os nomes, tipos e cadeias de caracteres de conexão de bancos de dados de backup.

```
{
    "properties":
    {
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        “databases”: [
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”,
                "connectionString": "<your database connection string>"
            }
        ]
    }
}
```

Um backup do aplicativo começa imediatamente quando a solicitação for recebida. O processo de backup pode levar muito tempo para ser concluída. A resposta HTTP contém uma identificação que você pode usar em outra solicitação para ver o status do backup. Aqui está um exemplo do corpo da resposta HTTP à nossa solicitação de backup.

```
{
    "id": "/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples",
    "name": " backuprestoreapiexamples ",
    "type": "Microsoft.Web/sites",
    "location": "WestUS",
    "properties":    {
        "id": 1,
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        "blobName": “backup_201509291825.zip”,
        "name": "backup_201509291825",
        "status": 4,
        "sizeInBytes": 0,
        "created": "2015-09-29T18:25:26.3992707Z",
        "log": null,
        "databases": [
            {
                "databaseType": "SqlAzure",
                "name": "MyDatabase1",
                "connectionString": "<your database connection string>"
            }
        ],
        "scheduled": false,
        "correlationId": "ea730f4d-dd06-4f7f-a090-9aa09k662h36",
    }
}
```

>[AZURE.NOTE] Mensagens de erro podem ser encontradas na propriedade log de resposta HTTP.

<a name="schedule-automatic-backups"></a>
## <a name="schedule-automatic-backups"></a>Agendar backups automáticos
Além de fazer backup de um aplicativo sob demanda, você também pode agendar um backup para ocorrer automaticamente.

### <a name="set-up-a-new-automatic-backup-schedule"></a>Configurar uma nova programação de backup automática
Para configurar um agendamento de backup, envie uma solicitação de **colocar** para **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup**.

Veja como fica o URL para nosso site de exemplo. **https://Management.Azure.com/Subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup**

O corpo da solicitação deve ter um objeto JSON que especifica a configuração de backup. Aqui está um exemplo com todos os parâmetros necessários.

```
{
    "location": "WestUS",
    "properties": // Represents an app restore request
    {
        "backupSchedule": { // Required for automatically scheduled backups
            "frequencyInterval": "7",
            "frequencyUnit": "Day",
            "keepAtLeastOneBackup": "True",
            "retentionPeriodInDays": "10",
        },
        "enabled": "True", // Must be set to true to enable automatic backups
        "name": “mysitebackup”,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

Este exemplo configura o aplicativo de backup automático a cada sete dias. Os parâmetros **frequencyInterval** e **frequencyUnit** juntos determinam quantas vezes os backups acontecem. Valores válidos para **frequencyUnit** são **hora** e **dia**. Por exemplo, para fazer backup de um aplicativo cada 12 horas, defina frequencyInterval como 12 e frequencyUnit a hora.

Backups antigos são automaticamente removidos da conta de armazenamento. Você pode controlar como antigo os backups podem ser definindo o parâmetro **retentionPeriodInDays** . Se você quiser sempre ter pelo menos um backup salvo, independentemente de como antigo está, definido **keepAtLeastOneBackup** para true.

### <a name="get-the-automatic-backup-schedule"></a>Obtenha o agendamento de backup automático
Para obter a configuração de backup de um aplicativo, enviar uma solicitação **POST** para a URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup/list**.

A URL do site nosso exemplo é **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list**.

<a name="get-backup-status"></a>
## <a name="get-the-status-of-a-backup"></a>Obter o status de um backup
Dependendo de como grande é o aplicativo, um backup pode demorar um pouco para ser concluída. Backups podem também falhar, hora de saída, ou parcialmente bem-sucedida. Para ver o status dos backups do todo um aplicativo, envie uma solicitação **GET** para a URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups**.

Para ver o status de um backup específico, envie uma solicitação GET para a URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**.

Veja como fica o URL para nosso site de exemplo. **https://Management.Azure.com/Subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1**

Corpo da resposta contém um objeto JSON semelhante a este exemplo.

```
{
    "properties":  {
        "id": 1,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl",
        "blobName": "backup_201509291734.zip",
        "name": "backup_201509291734",
        "status": 2,
        "sizeInBytes": 151973,
        "created": "2015-09-29T17:34:57.2091605",
        "scheduled": false,
        "lastRestoreTimeStamp": null,
        "finishedTimeStamp": "2015-09-29T17:35:11.3293602",
        "correlationId": "2379fc13-418a-4b9c-920d-d06e73c5028d",
        "websiteSizeInBytes": 209920
    }
}
```

O status de um backup é um tipo enumerado. Aqui está a cada estado possível.

* 0 – InProgress: O backup foi iniciado, mas ainda não concluiu.
* 1 – falha: O backup foi bem-sucedido.
* 2 – êxito: O backup foi concluído com êxito.
* 3 – TimedOut: O backup não foi concluída no tempo e foi cancelado.
* 4 – criadas: A solicitação de backup está na fila, mas não foi iniciada.
* 5 – ignorada: O backup não prosseguir devido a um cronograma de disparo excesso de backups.
* 6 – PartiallySucceeded: Que o backup foi bem-sucedida, mas alguns arquivos não foram copiados porque não pôde ser lido. Normalmente, isso acontece porque um bloqueio exclusivo foi colocado em arquivos.
* 7 – DeleteInProgress: O backup foi solicitado a ser excluído, mas ainda não foi excluído.
* 8 – DeleteFailed: O backup não pôde ser excluído. Isso pode acontecer porque a URL de SAS que foi usado para criar o backup expirou.
* 9 – excluídos: O backup foi excluído com êxito.

<a name="restore-app"></a>
## <a name="restore-an-app-from-a-backup"></a>Restaurar um aplicativo de um backup
Se seu aplicativo tiver sido excluído ou se você quiser reverter seu aplicativo para uma versão anterior, você pode restaurar o aplicativo de um backup. Para chamar uma restauração, enviar uma solicitação **POST** para a URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/restore**.

Veja como fica o URL para nosso site de exemplo. **https://Management.Azure.com/Subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/Restore**

No corpo da solicitação, envie um objeto JSON que contém as propriedades da operação de restauração. Aqui está um exemplo que contém todas as propriedades necessárias:

```
{
    "location": "WestUS",
    "properties":
    {
        "blobName": "backup_201509280431.zip",
        "databases": [ // Not required unless you’re restoring databases
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”
        }],
        "overwrite": "true",
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl" // SAS URL to storage container containing your website backup
    }
}
```

### <a name="restore-to-a-new-app"></a>Restaurar para um novo aplicativo
Às vezes, talvez você queira criar um novo aplicativo ao restaurar um backup, em vez de substituir um aplicativo já existente. Para fazer isso, altere a URL de solicitação para apontar para o novo aplicativo que você deseja criar e alterar a propriedade **Substituir** no JSON para **false**.

<a name="delete-app-backup"></a>
## <a name="delete-an-app-backup"></a>Excluir um backup de aplicativo
Se você quiser excluir um backup, envie uma solicitação de **Excluir** para o URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**.

Veja como fica o URL para nosso site de exemplo. **https://Management.Azure.com/Subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1**

<a name="manage-sas-url"></a>
## <a name="manage-a-backups-sas-url"></a>Gerenciar SAS URL de um backup
Serviço de aplicativo do Azure tentará excluir o backup de armazenamento do Azure usando a URL de SAS que foi fornecida quando o backup foi criado. Se esta URL SAS não é mais válida, o backup não pode ser excluído por meio da API REST. No entanto, você pode atualizar a URL de SAS associada a um backup enviando uma solicitação **POST** para a URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/list**.

Veja como fica o URL para nosso site de exemplo. **https://Management.Azure.com/Subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/List**

No corpo da solicitação, envie um objeto JSON que contém a nova URL de SAS. Aqui está um exemplo.

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

>[AZURE.NOTE] Por razões de segurança, a URL de SAS associada a um backup não é retornada ao enviar uma solicitação GET para um backup específico. Se você quiser exibir a URL de SAS associada a um backup, envie uma solicitação de POSTAGEM para a mesma URL acima. Inclua um objeto JSON vazio no corpo da solicitação. A resposta do servidor contém todas as informações do backup, incluindo sua URL SAS.

<!-- IMAGES -->
[SampleWebsiteInformation]: ./media/websites-csm-backup/01siteconfig.png
