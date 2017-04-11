<properties
 pageTitle="Autenticação de saída do Agendador"
 description="Autenticação de saída do Agendador"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/15/2016"
 ms.author="deli"/>

# <a name="scheduler-outbound-authentication"></a>Autenticação de saída do Agendador

Trabalhos do Agendador talvez precise chamar serviços que exijam autenticação. Dessa forma, um serviço chamado pode determinar se o trabalho do Agendador poderá acessar seus recursos. Alguns desses serviços incluem outros serviços do Azure, Salesforce.com, Facebook e sites personalizados seguros.

## <a name="adding-and-removing-authentication"></a>Adicionando e removendo autenticação

Adição de autenticação para um trabalho do Agendador é simple: adicionar um elemento filho JSON `authentication` para o `request` elemento ao criar ou atualizar um trabalho. Segredos passaram para o serviço de Agendador em uma solicitação de colocar, PATCH ou POSTAGEM – como parte do `authentication` objeto – nunca são retornados em respostas. No respostas, informações de segredo são definidas como nulo ou pode ter um símbolo pública que representa a entidade autenticada.

Para remover autenticação, colocar ou PATCH o trabalho explicitamente, definindo a `authentication` objeto como nulo. Você não verá as propriedades de autenticação novamente em resposta.

Atualmente, os tipos de autenticação com suporte apenas são a `ClientCertificate` modelo (para usar os certificados de cliente SSL/TLS), o `Basic` modelo (para autenticação básica) e o `ActiveDirectoryOAuth` modelo (para autenticação do Active Directory OAuth.)

## <a name="request-body-for-clientcertificate-authentication"></a>Corpo de solicitação de autenticação ClientCertificate

Ao adicionar a autenticação usando o `ClientCertificate` do modelo, especifique os seguintes elementos adicionais no corpo da solicitação.  

|Elemento|Descrição|
|:---|:---|
|_autenticação (elemento pai)_|Objeto de autenticação para usar um certificado de cliente SSL.|
|_tipo_|Necessário. Tipo de autenticação. Para certificados de cliente SSL, o valor deve ser `ClientCertificate`.|
|_PFX_|Necessário. Codificado na Base 64 o conteúdo do arquivo PFX.|
|_senha_|Necessário. Senha para acessar o arquivo PFX.|


## <a name="response-body-for-clientcertificate-authentication"></a>Corpo de resposta para autenticação ClientCertificate

Quando uma solicitação é enviada com informações de autenticação, a resposta contém os seguintes elementos de autenticação.

|Elemento |Descrição |
|:--|:--|
|_autenticação (elemento pai)_ |Objeto de autenticação para usar um certificado de cliente SSL.|
|_tipo_ |Tipo de autenticação. Para certificados de cliente SSL, o valor é `ClientCertificate`.|
|_certificateThumbprint_ |A impressão digital do certificado.|
|_certificateSubjectName_ |O nome distinto assunto do certificado.|
|_certificateExpiration_ |A data de validade do certificado.|

## <a name="sample-rest-request-for-clientcertificate-authentication"></a>Exemplo de solicitação REST para autenticação ClientCertificate

```
PUT https://management.azure.com/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-clientcertificate-authentication"></a>Resposta do resto de exemplo para autenticação ClientCertificate

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="request-body-for-basic-authentication"></a>Corpo da solicitação para autenticação básica

Ao adicionar a autenticação usando o `Basic` do modelo, especifique os seguintes elementos adicionais no corpo da solicitação.

|Elemento|Descrição|
|:--|:--|
|_autenticação (elemento pai)_ |Objeto de autenticação para usando a autenticação básica.|
|_tipo_ |Necessário. Tipo de autenticação. Para a autenticação básica, o valor deve ser `Basic`.|
|_nome de usuário_ |Necessário. Nome de usuário para autenticar.|
|_senha_ |Necessário. Senha para autenticação.|

## <a name="response-body-for-basic-authentication"></a>Corpo de resposta para a autenticação básica

Quando uma solicitação é enviada com informações de autenticação, a resposta contém os seguintes elementos de autenticação.

|Elemento|Descrição|
|:--|:--|
|_autenticação (elemento pai)_ |Objeto de autenticação para usando a autenticação básica.|
|_tipo_ |Tipo de autenticação. Para autenticação básica, o valor é `Basic`.|
|_nome de usuário_ |O nome de usuário autenticado.|

## <a name="sample-rest-request-for-basic-authentication"></a>Solicitação de restante de amostra para autenticação básica

```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-basic-authentication"></a>Resposta do resto de exemplo para autenticação básica

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="request-body-for-activedirectoryoauth-authentication"></a>Corpo de solicitação de autenticação de ActiveDirectoryOAuth

Ao adicionar a autenticação usando o `ActiveDirectoryOAuth` do modelo, especifique os seguintes elementos adicionais no corpo da solicitação.

|Elemento |Descrição |
|:--|:--|
|_autenticação (elemento pai)_ |Objeto de autenticação para usando autenticação de ActiveDirectoryOAuth.|
|_tipo_ |Necessário. Tipo de autenticação. Para autenticação de ActiveDirectoryOAuth, o valor deve ser `ActiveDirectoryOAuth`.|
|_locatário_ |Necessário. O identificador de locatário do locatário Azure AD.|
|_público-alvo_ |Necessário. Isso é definido para https://management.core.windows.net/.|
|_clientId_ |Necessário. Fornece o identificador de cliente para o aplicativo do Azure AD.|
|_segredo_ |Necessário. Segredo do cliente que está solicitando o token.|

### <a name="determining-your-tenant-identifier"></a>Determinando o identificador de locatário

Você pode encontrar o identificador de locatário do locatário Azure AD executando `Get-AzureAccount` no Azure PowerShell.

## <a name="response-body-for-activedirectoryoauth-authentication"></a>Corpo de resposta para autenticação de ActiveDirectoryOAuth

Quando uma solicitação é enviada com informações de autenticação, a resposta contém os seguintes elementos de autenticação.

|Elemento |Descrição |
|:--|:--|
|_autenticação (elemento pai)_ |Objeto de autenticação para usando autenticação de ActiveDirectoryOAuth.|
|_tipo_ |Tipo de autenticação. Para autenticação de ActiveDirectoryOAuth, o valor é `ActiveDirectoryOAuth`.|
|_locatário_ |O identificador de locatário do locatário Azure AD. |
|_público-alvo_ |Isso é definido para https://management.core.windows.net/.|
|_clientId_ |O identificador de cliente para o aplicativo do Azure AD.|

## <a name="sample-rest-request-for-activedirectoryoauth-authentication"></a>Exemplo de solicitação REST para autenticação de ActiveDirectoryOAuth

```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-activedirectoryoauth-authentication"></a>Resposta do resto de exemplo para autenticação de ActiveDirectoryOAuth

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "tenant":"microsoft.onmicrosoft.com",
               "audience":"https://management.core.windows.net/",
               "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type":"ActiveDirectoryOAuth"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "lastExecutionTime":"2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime":"2016-03-16T19:11:00Z",
         "executionCount":5,
         "failureCount":5,
         "faultedCount":1
      }
   }
}
```

## <a name="see-also"></a>Consulte também


 [O que é o Agendador?](scheduler-intro.md)

 [Hierarquia de entidades, terminologia e conceitos de Agendador Azure](scheduler-concepts-terms.md)

 [Começar a usar o Agendador no portal do Azure](scheduler-get-started-portal.md)

 [Planos e cobranças no Agendador do Azure](scheduler-plans-billing.md)

 [Referência de API do Agendador REST Azure](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Agendador Azure](scheduler-powershell-reference.md)

 [Azure Agendador alta disponibilidade e confiabilidade](scheduler-high-availability-reliability.md)

 [Limites de Agendador Azure, padrões e códigos de erro](scheduler-limits-defaults-errors.md)
