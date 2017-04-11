<properties 
   pageTitle="Começar a usar a análise de Lucerne dados usando API REST | Microsoft Azure" 
   description="Use WebHDFS restante APIs para executar operações em Lucerne a análise de dados" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/19/2016"
   ms.author="jgao"/>

# <a name="get-started-with-azure-data-lake-analytics-using-rest-apis"></a>Introdução ao Azure dados Lucerne análise usando APIs REST

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como usar WebHDFS restante APIs e dados Lucerne Analytics restante APIs para gerenciar contas, trabalhos e catálogo de dados Lucerne Analytics. 

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).

- **Criar um aplicativo do Active Directory do Azure**. Use o aplicativo do Azure AD para autenticar o aplicativo de análise de Lucerne de dados com o Azure AD. Há diferentes abordagens para autenticar com Azure AD, que são a **autenticação do usuário final** ou **ao serviço**. Para obter instruções e mais informações sobre como se autenticar, consulte [autenticar com dados Lucerne análise usando o Active Directory do Azure](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

- [cURL](http://curl.haxx.se/). Este artigo usa ondulação para demonstrar como fazer chamadas de API REST em relação a uma conta de dados Lucerne Analytics.

## <a name="authenticate-with-azure-active-directory"></a>Autenticar com o Active Directory do Azure

Há dois métodos para autenticar com o Active Directory do Azure.

### <a name="end-user-authentication-interactive"></a>Autenticação de usuário final (interativa)

Usando esse método, aplicativo solicita que o usuário para efetuar login e todas as operações são executadas no contexto do usuário. 

Siga estas etapas para autenticação interativa:

1. Por meio do aplicativo, redirecione o usuário para a URL a seguir:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

    >[AZURE.NOTE] \<REDIRECIONAR-URI > precisam ser codificados para uso em uma URL. Portanto, para https://localhost, use `https%3A%2F%2Flocalhost`)

    Efeitos neste tutorial, você pode substituir os valores de espaço reservado na URL acima e colá-lo na barra de endereços do navegador da web. Você será redirecionado para autenticar usando seu logon Azure. Depois que você com êxito efetuar login, a resposta é exibida na barra de endereços do navegador. A resposta será no seguinte formato:
        
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Capture o código de autorização da resposta. Para este tutorial, você pode copiar o código de autorização na barra de endereços do navegador da web e passar na solicitação de POSTAGEM no ponto de extremidade token, conforme mostrado abaixo:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

    >[AZURE.NOTE] Nesse caso, o \<URI de REDIRECIONAMENTO > não precisa ser codificada.

3. A resposta é um objeto JSON que contém um token de acesso (por exemplo, `"access_token": "<ACCESS_TOKEN>"`) e um token de atualização (por exemplo, `"refresh_token": "<REFRESH_TOKEN>"`). Seu aplicativo usa o token de acesso ao acessar o repositório de Lucerne de dados do Azure e o token de atualização para obter outro token de acesso quando um token de acesso expira.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before": "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  Quando o token de acesso expira, você pode solicitar um novo token de acesso usando o token de atualização, conforme mostrado abaixo:

         curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
            -F grant_type=refresh_token \
            -F resource=https://management.core.windows.net/ \
            -F client_id=<CLIENT-ID> \
            -F refresh_token=<REFRESH-TOKEN>
 
Para obter mais informações sobre autenticação de usuário interativo, consulte o [código de autorização conceder fluxo](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### <a name="service-to-service-authentication-non-interactive"></a>Serviço de autenticação (não-interativo)

Usando esse método, o aplicativo fornece suas própria credenciais para executar as operações. Para isso, você deve emitir uma solicitação de POSTAGEM como a mostrada abaixo: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

A saída dessa solicitação incluirá um símbolo de autorização (indicado por `access-token` na saída abaixo) que você subsequentemente passará com suas chamadas de API REST. Salvar este token de autenticação em um arquivo de texto; Você precisará isso neste artigo.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Este artigo usa o **não interativo** abordagem. Para obter mais informações sobre não interativo (chamadas de serviço para), consulte [serviço para chamadas de serviço usando credenciais](https://msdn.microsoft.com/library/azure/dn645543.aspx).
## <a name="create-a-data-lake-analytics-account"></a>Criar uma conta de dados Lucerne Analytics

Você deve criar um grupo de recursos do Azure e uma conta de armazenamento de Lucerne de dados antes de criar uma conta de dados Lucerne Analytics.  Consulte [criar uma conta de armazenamento de Lucerne de dados](../data-lake-store/data-lake-store-get-started-rest-api.md#create-a-data-lake-store-account).

O comando de rotação a seguir mostra como criar uma conta:

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<NewAzureDataLakeAnalyticsAccountName>?api-version=2016-11-01 -d@"C:\tutorials\adla\CreateDataLakeAnalyticsAccountRequest.json"

Substituir \< `REDACTED` \> com o token de autorização, \< `AzureSubscriptionID` \> com sua ID de assinatura, \< `AzureResourceGroupName` \> com um nome de grupo de recursos do Azure existente, e \< `NewAzureDataLakeAnalyticsAccountName` \> com um novo nome de conta de análise de Lucerne de dados. A carga de solicitação para este comando está contida no arquivo **CreateDatalakeAnalyticsAccountRequest.json** que é fornecido para a `-d` parâmetro acima. O conteúdo do arquivo input.json semelhante ao seguinte:

    {  
        "location": "East US 2",  
        "name": "myadla1004",  
        "tags": {},  
        "properties": {  
            "defaultDataLakeStoreAccount": "my1004store",  
            "dataLakeStoreAccounts": [  
                {  
                    "name": "my1004store"  
                }     
            ]
        }  
    }  


## <a name="list-data-lake-analytics-accounts-in-a-subscription"></a>Contas de análise de Lucerne de dados de lista em uma assinatura

O comando de rotação a seguir mostra como contas em uma assinatura de lista:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/providers/Microsoft.DataLakeAnalytics/Accounts?api-version=2016-11-01

Substituir \< `REDACTED` \> com o token de autorização, \< `AzureSubscriptionID` \> com sua ID de assinatura. A saída é semelhante a:

    {
        "value": [
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla0831.azuredatalakeanalytics.net",
                "accountId": "21e74660-0941-4880-ae72-b143c2615ea9",
                "creationTime": "2016-09-01T12:49:12.7451428Z",
                "lastModifiedTime": "2016-09-01T12:49:12.7451428Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla0831rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla0831",
            "name": "myadla0831",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            },
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla1004.azuredatalakeanalytics.net",
                "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
                "creationTime": "2016-10-04T20:46:42.287147Z",
                "lastModifiedTime": "2016-10-04T20:46:42.287147Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
            "name": "myadla1004",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            }
        ]
    }

## <a name="get-information-about-a-data-lake-analytics-account"></a>Obter informações sobre uma conta de dados Lucerne Analytics

O comando de ondulação a seguir mostra como obter as informações da conta:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>?api-version=2015-11-01

Substituir \< `REDACTED` \> com o token de autorização, \< `AzureSubscriptionID` \> com sua ID de assinatura, \< `AzureResourceGroupName` \> com um nome de grupo de recursos do Azure existente, e \< `DataLakeAnalyticsAccountName` \> com o nome de uma conta de análise de Lucerne de dados existente. A saída é semelhante a:

    {
        "properties": {
            "defaultDataLakeStoreAccount": "my1004store",
            "dataLakeStoreAccounts": [
            {
                "properties": {
                "suffix": "azuredatalakestore.net"
                },
                "name": "my1004store"
            }
            ],
            "provisioningState": "Creating",
            "state": null,
            "endpoint": null,
            "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
            "creationTime": null,
            "lastModifiedTime": null
        },
        "location": "East US 2",
        "tags": {},
        "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
        "name": "myadla1004",
        "type": "Microsoft.DataLakeAnalytics/accounts"
    }

## <a name="list-data-lake-stores-of-a-data-lake-analytics-account"></a>Lista de dados Lucerne lojas de uma conta de dados Lucerne Analytics

O comando de rotação a seguir mostra como armazenamentos de Lucerne de dados de uma conta de lista:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>/DataLakeStoreAccounts/?api-version=2016-11-01

Substituir \< `REDACTED` \> com o token de autorização, \< `AzureSubscriptionID` \> com sua ID de assinatura, \< `AzureResourceGroupName` \> com um nome de grupo de recursos do Azure existente, e \< `DataLakeAnalyticsAccountName` \> com o nome de uma conta de análise de Lucerne de dados existente. A saída é semelhante a:

    {
        "value": [
            {
            "properties": {
                "suffix": "azuredatalakestore.net"
            },
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004/dataLakeStoreAccounts/my1004store",
            "name": "my1004store",
            "type": "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts"
            }
        ]
    }

## <a name="submit-u-sql-jobs"></a>Enviar trabalhos de U-SQL

O comando de rotação a seguir mostra como enviar um trabalho de U-SQL:

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs/<NewGUID>?api-version=2016-03-20-preview -d@"C:\tutorials\adla\SubmitADLAJob.json"

Substituir \< `REDACTED` \> com o token de autorização, \< `DataLakeAnalyticsAccountName` \> com o nome de uma conta de análise de Lucerne de dados existente. A carga de solicitação para este comando está contida no arquivo **SubmitADLAJob.json** que é fornecido para a `-d` parâmetro acima. O conteúdo do arquivo input.json semelhante ao seguinte:

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "properties": {
            "type": "USql",
            "script": "@searchlog =\n    EXTRACT UserId          int,\n            Start           DateTime,\n            Region          string,\n            Query          
        string,\n            Duration        int?,\n            Urls            string,\n            ClickedUrls     string\n    FROM \"/Samples/Data/SearchLog.tsv\"\n    US
        ING Extractors.Tsv();\n\nOUTPUT @searchlog   \n    TO \"/Output/SearchLog-from-Data-Lake.csv\"\nUSING Outputters.Csv();"
        }
    }

A saída é semelhante a:

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "myadl@SPI",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "2016-10-05T13:54:59.9871859+00:00",
        "state": "Compiling",
        "result": "Succeeded",
        "stateAuditRecords": [
            {
            "newState": "New",
            "timeStamp": "2016-10-05T13:54:59.9871859+00:00",
            "details": "userName:myadl@SPI;submitMachine:N/A"
            }
        ],
        "properties": {
            "owner": "myadl@SPI",
            "resources": [],
            "runtimeVersion": "default",
            "rootProcessNodeId": "00000000-0000-0000-0000-000000000000",
            "algebraFilePath": "adl://myadls0831.azuredatalakestore.net/system/jobservice/jobs/Usql/2016/10/05/13/54/8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a/algebra.xml",
            "compileMode": "Semantic",
            "errorSource": "Unknown",
            "totalCompilationTime": "PT0S",
            "totalPausedTime": "PT0S",
            "totalQueuedTime": "PT0S",
            "totalRunningTime": "PT0S",
            "type": "USql"
        }
    }


## <a name="list-u-sql-jobs"></a>Lista trabalhos de U-SQL

O comando de rotação a seguir mostra como listar trabalhos de U-SQL:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs?api-version=2016-11-01 

Substituir \< `REDACTED` \> com o token de autorização, e \< `DataLakeAnalyticsAccountName` \> com o nome de uma conta de análise de Lucerne de dados existente. 


A saída é semelhante a:

    {
    "value": [
        {
        "jobId": "65cf1691-9dbe-43cd-90ed-1cafbfb406fb",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someone@microsoft.com",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:46:53 GMT",
        "startTime": "Wed, 05 Oct 2016 13:47:33 GMT",
        "endTime": "Wed, 05 Oct 2016 13:48:07 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        },
        {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someoneadl@SPI",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:54:59 GMT",
        "startTime": "Wed, 05 Oct 2016 13:55:43 GMT",
        "endTime": "Wed, 05 Oct 2016 13:56:11 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        }
    ],
    "nextLink": null,
    "count": null
    }


## <a name="get-catalog-items"></a>Obter itens de catálogo

O comando de rotação a seguir mostra como obter os bancos de dados do catálogo:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/catalog/usql/databases?api-version=2016-11-01

A saída é semelhante a:

    {
    "@odata.context":"https://myadla0831.azuredatalakeanalytics.net/sqlip/$metadata#databases","value":[
        {
        "computeAccountName":"myadla0831","databaseName":"mytest","version":"f6956327-90b8-4648-ad8b-de3ff09274ea"
        },{
        "computeAccountName":"myadla0831","databaseName":"master","version":"e8bca908-cc73-41a3-9564-e9bcfaa21f4e"
        }
    ]
    }

## <a name="see-also"></a>Consulte também

- Para ver uma consulta mais complexa, consulte [logs de site de análise usando análise de Lucerne de dados do Azure](data-lake-analytics-analyze-weblogs.md).
- Para começar a desenvolver aplicativos U-SQL, consulte [scripts de desenvolver U-SQL usando ferramentas de Lucerne de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para saber U-SQL, consulte [Introdução ao idioma do Azure dados Lucerne Analytics U-SQL](data-lake-analytics-u-sql-get-started.md).
- Tarefas de gerenciamento, consulte [Gerenciar o Azure dados Lucerne análise usando o portal Azure](data-lake-analytics-manage-use-portal.md).
- Para obter uma visão geral de análise de Lucerne de dados, consulte [Visão geral de análise de Lucerne de dados do Azure](data-lake-analytics-overview.md).
- Para ver o mesmo tutorial usando outras ferramentas, clique em seletores de guia no topo da página.
- Para registrar informações de diagnóstico, consulte [Acessando logs de diagnóstico para análise de Lucerne de dados do Azure](data-lake-analytics-diagnostic-logs.md)
