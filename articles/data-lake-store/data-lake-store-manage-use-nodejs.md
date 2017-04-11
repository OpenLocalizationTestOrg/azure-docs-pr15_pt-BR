<properties 
   pageTitle="Introdução ao Azure armazenamentos de Lucerne de dados usando o SDK do Azure para Node | Microsoft Azure"
   description="Saiba como usar Node para trabalhar com contas de armazenamento de Lucerne de dados e o sistema de arquivos." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-sdk-for-nodejs"></a>Introdução ao armazenamento de Lucerne de dados do Azure usando o SDK do Azure para Node

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node](data-lake-store-manage-use-nodejs.md)


Aprenda a usar o SDK do Azure para Node para criar uma conta de armazenamento de Lucerne de dados do Azure e executar operações básicas, tais como criar pastas, carregar e baixar arquivos de dados, excluir sua conta, etc. Para obter mais informações sobre armazenamento de Lucerne de dados, consulte [Visão geral da Lucerne armazenamento de dados](data-lake-store-overview.md). Atualmente, o SDK suporta

  *  **Versão node: 0.10.0 ou superior**
  *  **Versão da API REST para conta: 2015-10-01-visualização**
  *  **Versão da API REST para sistema de arquivos: 2015-10-01-visualização**

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).

- **Criar um aplicativo do Active Directory do Azure**. Use o aplicativo do Azure AD para autenticar o aplicativo de armazenamento de Lucerne de dados com o Azure AD. Há diferentes abordagens para autenticar com Azure AD, que são a **autenticação do usuário final** ou **ao serviço**. Para obter instruções e mais informações sobre como se autenticar, consulte [autenticar com armazenamento de Lucerne de dados usando o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="how-to-install"></a>Como instalar

```bash
npm install azure-arm-datalake-store
```

## <a name="authenticate-using-azure-active-directory"></a>Autenticar usando o Active Directory do Azure

Os trechos abaixo mostram duas formas separadas de autenticar com armazenamento de Lucerne de dados usando o Azure AD. Para uma discussão detalhada sobre vários métodos para usar para autenticação com dados Lucerne Store, consulte [autenticar com armazenamento de Lucerne de dados usando o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

O trecho a seguir também requer entradas como o nome de domínio do Azure AD, ID do cliente para um aplicativo do Azure AD, etc. Todos esses detalhes podem ser recuperados de um Azure AD aplicativo que você deve criado, os detalhes do que também estão incluídos no link acima.

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-store-clients"></a>Criar os clientes de armazenamento de Lucerne de dados

```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, "your-subscription-id");
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials);
```

## <a name="create-a-data-lake-store-account"></a>Criar uma conta de armazenamento de Lucerne de dados

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlsacct';
var location = 'eastus2';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location
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

## <a name="create-a-file-with-content"></a>Criar um arquivo com o conteúdo
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var fileToCreate = '/myfolder/myfile.txt';
var options = {
  streamContents: new Buffer('some string content')
}

filesystemClient.fileSystem.listFileStatus(accountName, fileToCreate, options, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    // no result is returned, only a 201 response for success.
    console.log('response is: ' + util.inspect(response, {depth: null}));
  }
});
```

## <a name="get-a-list-of-files-and-folders"></a>Obter uma lista de arquivos e pastas

```javascript
var util = require('util');
var accountName = 'testadlsacct';
var pathToEnumerate = '/myfolder';
filesystemClient.fileSystem.listFileStatus(accountName, pathToEnumerate, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Consulte também

- [SDK do Microsoft Azure para Node](https://github.com/azure/azure-sdk-for-node)
- [SDK do Microsoft Azure para Node - gerenciamento de Lucerne a análise de dados](https://www.npmjs.com/package/azure-arm-datalake-analytics)
