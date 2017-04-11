<properties
   pageTitle="Gerenciador de recursos restante APIs | Microsoft Azure"
   description="Uma visão geral dos exemplos de autenticação e o uso de APIs de resto do Gerenciador de recursos"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="navale;tomfitz;"/>
   
# <a name="resource-manager-rest-apis"></a>APIs de resto do Gerenciador de recursos

> [AZURE.SELECTOR]
- [PowerShell Azure](powershell-azure-resource-manager.md)
- [CLI Azure](xplat-cli-azure-resource-manager.md)
- [Portal](./azure-portal/resource-group-portal.md) 
- [API REST](resource-manager-rest-api.md)

Atrás de cada chamada ao Gerenciador de recursos do Azure, atrás de cada modelo implantado, atrás de cada conta de armazenamento configurado há uma ou várias chamadas à API RESTful do Gerenciador de recursos do Azure. Este tópico é dedicado a essas APIs e como você pode chamá-los sem usar qualquer SDK todo. Isso pode ser muito útil se você quiser controle total de todas as solicitações para Azure ou se o SDK para seu idioma preferencial não está disponível ou não dá suporte as operações que deseja executar.

Este artigo não passará por cada API exposto no Azure, mas preferir usar alguns como um exemplo de como você pode vai adiante e conectá-los. Se você compreender as Noções básicas que você pode ir adiante e leia a [Referência à API REST Azure Gerenciador de recursos](https://msdn.microsoft.com/library/azure/dn790568.aspx) para encontrar informações detalhadas sobre como usar o restante das APIs.

## <a name="authentication"></a>Autenticação
Autenticação do BRAÇO é tratada por Azure Active Directory (AD). Para se conectar a qualquer API primeiro é necessário para autenticar com Azure AD para receber um token de autenticação que você pode passar para cada solicitação. Como estamos descrevendo uma chamada pura diretamente para as APIs REST, podemos também presumem que você não deseja autenticar com uma senha de nome de usuário normal onde um pop-Valorizações tela pode solicitar nome de usuário e senha e até mesmo outros mecanismos de autenticação usados em dois cenários de autenticação de fator. Portanto, vamos criar o que é chamado de um aplicativo do Azure AD e um Principal de serviço que será usado para fazer logon com. Mas lembre-se de que vários procedimentos de autenticação de suporte do Azure AD e todos eles podem ser usados para recuperar esse token de autenticação que precisamos para solicitações subsequentes de API.
Siga [criar Azure AD aplicativo e princípio de serviço](./resource-group-create-service-principal-portal.md) para obter instruções passo a passo.

### <a name="generating-an-access-token"></a>Gerar um Token de acesso 
Autenticação contra Azure AD é feita ao chamar Azure AD, localizado em login.microsoftonline.com. Para autenticar você precisa ter as seguintes informações:

* ID de locatário do Azure AD (o nome do que Azure AD que você está usando para efetuar login, geralmente a mesma da sua empresa, mas não é necessário)
* ID de aplicativo (obtido durante a etapa de criação de aplicativo do Azure AD)
* Senha (que você selecionou ao criar o aplicativo do Azure AD)

No abaixo solicitação HTTP, certifique-se de substituir "ID do Azure AD locatário", "ID de aplicativo" e "Senha" com os valores corretos.

**Solicitação de HTTP genérico:**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

… será (se autenticação sucesso) resultam em uma resposta semelhante a esta:

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(O access_token na resposta acima foi reduzida para aumentar a legibilidade)

**Gerando token de acesso usando Bash:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**Gerando token de acesso usando o PowerShell:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

A resposta contém um Token de acesso, informações sobre quanto tempo esse token é válida e informações sobre quais recursos você pode usar esse token para.
O token de acesso que você recebeu na chamada HTTP anterior deve ser passado para todas as solicitações à API BRAÇO como um cabeçalho chamado "Autorização" com o valor "Portador YOUR_ACCESS_TOKEN". Observe o espaço entre "Portador" e seu Token de acesso.

Como você pode ver o resultado de HTTP acima, o símbolo é válido para um período específico de tempo durante o qual você deve cache e usar esse mesmo token novamente. Mesmo que seja possível autenticar Azure AD para cada chamada de API, seria altamente ineficaz.

## <a name="calling-arm-rest-apis"></a>Chamando BRAÇO restante APIs

[REST APIs do Azure Gerenciador de recursos estão documentadas aqui](https://msdn.microsoft.com/library/azure/dn790568.aspx) e ele do sair do escopo para este tutorial para o uso de cada do documento. Esta documentação usará apenas algumas APIs para explicar o uso básico das APIs e depois que estamos você consultar a documentação do oficial.

### <a name="list-all-subscriptions"></a>Lista todas as assinaturas

Uma das operações mais simples que você pode fazer é listar as assinaturas disponíveis que você pode acessar. No abaixo de solicitação, você pode ver como o Token de acesso é passado como um cabeçalho.

(Substitua YOUR_ACCESS_TOKEN seu Token de acesso real.)

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

… e como resultado, você receberá uma lista de assinaturas que esse serviço Principal tem permissão para acessar

(IDs de assinatura abaixo foi reduzidas para fins de legibilidade)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>Listar todos os grupos de recursos em uma assinatura específica

Todos os recursos disponíveis com as APIs BRAÇO são aninhados dentro de um grupo de recursos. Vamos consulta ARM para os grupos de recursos existentes em nossa assinatura usando o abaixo solicitação GET HTTP. Observe como a ID da assinatura é passado como parte da URL neste momento.

(Substitua YOUR_ACCESS_TOKEN e SUBSCRIPTION_ID com real Token de acesso e ID da assinatura)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

A resposta que você obtenha dependerá se você tem qualquer grupos de recursos definidos e em caso afirmativo, quantos.

(IDs de assinatura abaixo foi reduzidas para fins de legibilidade)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Até agora podemos tiver apenas foi consultando as APIs ARM para obter informações, é hora criamos alguns recursos, em vez disso e vamos começar a mais simples de todos eles, um grupo de recursos. A solicitação HTTP a seguir cria um novo grupo de recursos em um região/local de sua escolha e adiciona uma ou mais marcas a ela (exemplo abaixo, na verdade, somente adiciona uma marca).

(Substitua YOUR_ACCESS_TOKEN, SUBSCRIPTION_ID, RESOURCE_GROUP_NAME com real Token de acesso, ID da assinatura e nome do grupo de recursos que você deseja criar)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

Se for bem-sucedido, você receberá uma resposta semelhante a esta

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Você criou com êxito um grupo de recursos no Azure. Parabéns!

### <a name="deploy-resources-to-a-resource-group-using-an-arm-template"></a>Implantar recursos em um grupo de recursos usando um modelo de ARM

Com BRAÇO, você pode implantar seus recursos usando modelos de ARM. Um modelo de ARM define vários recursos e suas dependências. Para essa seção nós assumirá apenas você está familiarizado com modelos de ARM e apenas mostraremos como fazer a chamada de API para iniciar a implantação de um. Uma documentação detalhada dos modelos de BRAÇO pode ser encontrada aqui.

Implantação de um modelo de ARM não diferem muito como você chamar outros APIs. Um aspecto importante é que a implantação de um modelo pode levar muito tempo, dependendo de qual é dentro do modelo e a chamada API retornará somente e cabe a você como desenvolvedor para consultar status da implantação para saber quando a implantação é feita.

Neste exemplo, vamos usar um modelo de ARM expostos publicamente disponível no [GitHub](https://github.com/Azure/azure-quickstart-templates). O modelo, vamos usar implantará uma VM Linux à região Oeste EUA. Embora este modelo terá o modelo disponível em um repositório público como GitHub, você também pode selecionar para passar o modelo completo como parte da solicitação. Observe que fornecemos valores de parâmetros como parte da solicitação que será usada dentro do modelo usado.

(Substitua SUBSCRIPTION_ID, RESOURCE_GROUP_NAME, DEPLOYMENT_NAME, YOUR_ACCESS_TOKEN, GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME, ADMIN_USER_NAME, ADMIN_PASSWORD e DNS_NAME_FOR_PUBLIC_IP com valores apropriados para sua solicitação)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

A resposta JSON bastante tempo para esta solicitação foram omitidos para melhorar a legibilidade desta documentação. A resposta conterá informações sobre a implantação de modelo que você acabou de criar.

