<properties
   pageTitle="Gerenciar Azure dados Lucerne análise usando o SDK do Azure para Node | Azure"
   description="Saiba como gerenciar contas de dados Lucerne Analytics, fontes de dados, trabalhos e usuários usando o SDK do Azure para Node"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="manage-azure-data-lake-analytics-using-azure-sdk-for-nodejs"></a>Gerenciar Azure dados Lucerne análise usando o SDK do Azure para Node


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

O SDK do Azure para Node podem ser usado para gerenciar contas de análise de Lucerne de dados do Azure, trabalhos e catálogos. Para ver o tópico de gerenciamento usando outras ferramentas, clique em Selecionar guia acima.

Agora, ele oferece suporte para:

  *  **Versão node: 0.10.0 ou superior**
  *  **Versão da API REST para conta: 2015-10-01-visualização**
  *  **Versão da API REST para catálogo: 2015-10-01-visualização**
  *  **Versão da API REST para trabalho: 2016-03-20-preview**

## <a name="features"></a>Recursos

- Gerenciamento de conta: criar, obter, lista, atualizar e excluir.
- Gerenciamento de trabalho: enviar, receber, lista, cancelar.
- Gerenciamento de catálogo: obter, lista, criar (segredos), atualizar (segredos), excluir (segredos).

## <a name="how-to-install"></a>Como instalar

```bash
npm install azure-arm-datalake-analytics
```

## <a name="authenticate-using-azure-active-directory"></a>Autenticar usando o Active Directory do Azure

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-analytics-client"></a>Crie o cliente de dados Lucerne Analytics

```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var acccountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## <a name="create-a-data-lake-analytics-account"></a>Criar uma conta de dados Lucerne Analytics

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-jobs"></a>Obter uma lista de trabalhos

```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-databases-in-the-data-lake-analytics-catalog"></a>Obter uma lista de bancos de dados no catálogo de análise de dados Lucerne
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Consulte também

- [SDK do Microsoft Azure para Node](https://github.com/azure/azure-sdk-for-node)
- [SDK do Microsoft Azure para Node - gerenciamento de repositório de Lucerne de dados](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)
