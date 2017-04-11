<properties 
    pageTitle="Novo esquema versão 2015-08-01-preview" 
    description="Aprenda como escrever a definição de JSON para a versão mais recente dos aplicativos de lógica" 
    authors="stepsic-microsoft-com" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="stepsic"/>
    
# <a name="new-schema-version-2015-08-01-preview"></a>Novo esquema versão 2015-08-01-preview

A API e o novo esquema versão para aplicativos de lógica possui um número de aprimoramentos que melhorar a confiabilidade e facilidade de uso dos aplicativos de lógica. Há 4 principais diferenças:

1. O tipo de ação **APIApp** foi atualizado para um novo tipo de ação **APIConnection** .
2. **Repita** foi renomeado para **Foreach**.
3. O aplicativo de API **Ouvinte HTTP** não é mais necessário.
4. Chamar fluxos de trabalho filho usa um novo esquema.

## <a name="1-moving-to-api-connections"></a>1. movendo às conexões de API

A maior alteração é que você não precisa implantar aplicativos de API em sua assinatura do Azure para usar da API. Há 2 maneiras de usar APIs:
* API gerenciada
* A API da Web personalizado

Cada um desses é tratada ligeiramente diferente porque seu gerenciamento e modelos de hospedagem são diferentes. Uma vantagem desse modelo é que você já não estiver restrito aos recursos que são implantados em seu grupo de recursos. 

### <a name="managed-apis"></a>APIs gerenciadas

Há um número de API que é gerenciada pelo Microsoft em seu nome, como o Office 365, Salesforce, Twitter, FTP etc... Alguns desses APIs gerenciados podem ser usados como-é, como o Bing traduzir, enquanto outras exigem configuração. Esta configuração é denominada uma *conexão*.

Por exemplo, quando você usa o Office 365, você precisa criar uma conexão que contém o token de entrada do Office 365. Esse token será armazenado com segurança e atualizado para que seu aplicativo de lógica sempre pode chamar a API do Office 365. Como alternativa, se você quiser conectar ao seu servidor SQL ou FTP, você precisa criar uma conexão com a cadeia de conexão. 

Dentro a definição essas ações são chamadas `APIConnection`. Aqui está um exemplo de uma conexão que chama o Office 365 para enviar um email:

```
{
    "actions": {
        "Send_Email": {
            "type": "ApiConnection",
            "inputs": {
                "host": {
                    "api": {
                        "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['shared_office365']['connectionId']"
                    }
                },
                "method": "post",
                "body": {
                    "Subject": "Reminder",
                    "Body": "Don't forget!",
                    "To": "me@contoso.com"
                },
                "path": "/Mail"
            }
        }
    }
}
```

A parte das entradas exclusivas conexões de API é o `host` objeto. Esta página contém duas partes: `api` e `connection`.

O `api` tem o tempo de execução URL de onde que API gerenciada do está hospedado. Você pode ver todas as APIs gerenciadas disponíveis para você chamando `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Quando você usa uma API, ele pode ou não pode ter qualquer **parâmetros de conexão** definidos. Se não é necessário nenhuma **conexão** . Em caso afirmativo, você precisará criar uma conexão. Quando você cria essa conexão terá o nome escolhido, e, em seguida, você se referir a isso na `connection` objeto dentro do `host` objeto. Para criar uma conexão em um grupo de recursos, chamada:

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

Com o corpo seguinte:


```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
    "parameterValues" : {
        "accountName" : "{The name of the storage account -- the set of parameters is different for each API}"
    }
  },
  "location" : "{Logic app's location}"
}
```

### <a name="deploying-managed-apis-in-an-azure-resource-manager-template"></a>Implantando APIs gerenciadas em um modelo de Gerenciador de recursos do Azure

Você pode criar um aplicativo completo em um modelo ARM desde que eles não exijam entrar interativo. Se ele exigir entrada, você pode configurar tudo com o modelo ARM, mas ainda terá visitar o portal para autorizar as conexões. 

```
    "resources": [{
        "apiVersion": "2015-08-01-preview",
        "name": "azureblob",
        "type": "Microsoft.Web/connections",
        "location": "[resourceGroup().location]",
        "properties": {
            "api": {
                "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
            },
            "parameterValues": {
                "accountName": "[parameters('storageAccountName')]",
                "accessKey": "[parameters('storageAccountKey')]"
            }
        }
    }, {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-08-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[resourceId('Microsoft.Web/connections', 'azureblob')]"
        ],
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
                "actions": {
                    "Create_file": {
                        "type": "apiconnection",
                        "inputs": {
                            "host": {
                                "api": {
                                    "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                                },
                                "connection": {
                                    "name": "@parameters('$connections')['azureblob']['connectionId']"
                                }
                            },
                            "method": "post",
                            "queries": {
                                "folderPath": "[concat('/',parameters('containerName'))]",
                                "name": "helloworld.txt"
                            },
                            "body": "@decodeDataUri('data:,Hello+world!')",
                            "path": "/datasets/default/files"
                        },
                        "conditions": []
                    }
                },
                "contentVersion": "1.0.0.0",
                "outputs": {},
                "parameters": {
                    "$connections": {
                        "defaultValue": {},
                        "type": "Object"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "Recurrence",
                        "recurrence": {
                            "frequency": "Day",
                            "interval": 1
                        }
                    }
                }
            },
            "parameters": {
                "$connections": {
                    "value": {
                        "azureblob": {
                            "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                            "connectionName": "azureblob",
                            "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                        }

                    }
                }
            }
        }
    }]
```

Você pode ver nesse exemplo que as conexões são recursos apenas normais que moram em seu grupo de recursos. Eles se referir a managedAPIs disponíveis para você em sua assinatura.

### <a name="your-custom-web-apis"></a>Suas APIs da Web personalizado

Se você usar sua própria API (especificamente, não gerenciado Microsoft daqueles), você deve usar a ação de **HTTP** interna para chamá-los. Para ter uma experiência ideal, você deve expor um ponto de extremidade de swagger para sua API. Isso permitirá o designer de aplicativo lógica processar as entradas e saídas para sua API. Sem um swagger, o designer só poderão mostrar as entradas e saídas como opacos objetos JSON.

Aqui está um exemplo mostrando os novos `metadata.apiDefinitionUrl` propriedade:
```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata" : {
              "apiDefinitionUrl" : "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method" : "GET"
            }
        }
    }
}
```

Se você hospedar seus API da Web do serviço de **Aplicativo** , em seguida, ele automaticamente aparecerá na lista de ações disponíveis no designer. Caso contrário, você terá que cole a URL diretamente. O ponto de extremidade de swagger deve ser não autenticado para ser usado dentro do designer de aplicativos lógica (embora você pode proteger a API com quaisquer métodos são compatíveis com o Swagger).

### <a name="using-your-already-deployed-api-apps-with-2015-08-01-preview"></a>Usando seus aplicativos de API já implantados com 2015-08-01-preview

Se você tiver implantado um aplicativo de API, você pode chamá-lo via a ação de **HTTP** .

Por exemplo, se você usar o Dropbox para listar os arquivos, você pode ter algo parecido com isto em sua definição de versão de esquema de **2014-12-01-preview** :

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
                }
            }
        }
    },
    "actions": {
        "dropboxconnector": {
            "type": "ApiApp",
            "inputs": {
                "apiVersion": "2015-01-14",
                "host": {
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Você pode construir a ação equivalente do HTTP como abaixo (a seção parâmetros do lógica aplicativo definição permanece inalterada):

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata" : {
              "apiDefinitionUrl" : "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method" : "POST",
                "body": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Examinando dessas propriedades um por um:

| Propriedade Action |  Descrição |
| --------------- | -----------  |
| `type` | `Http`Em vez de`APIapp` |
| `metadata.apiDefinitionUrl` | Se você quiser usar esta ação no designer de aplicativos de lógica, você desejará incluir o ponto de extremidade de metadados. Isso é construído da:`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Isso é construído da:`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Sempre`POST` |
| `inputs.body` | Idêntico aos parâmetros de aplicativo de api | 
| `inputs.authentication` | Idêntico a autenticação de aplicativo de api |

Essa abordagem deve funcionar para todas as ações de aplicativo de API. No entanto, tenha em mente que esses aplicativos de API anterior já não são suportados e você deve mover para uma das duas outras opções acima (uma API gerenciada ou sua API Web personalizado de hospedagem).

## <a name="2-repeat-renamed-to-foreach"></a>2. Repetir renomeado para Foreach

Para a versão anterior do esquema recebemos muitas comentários do cliente que **Repetir** estava confuso e não capturar corretamente que ele foi realmente um loop for each. Como resultado, podemos ter renomeado-lo para **Foreach**. Por exemplo:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "repeat": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{repeatItem()}"
            }
        }
    }
}
```

Agora deve ser escrita como:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "foreach": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{item()}"
            }
        }
    }
}
```

Anteriormente, a função `@repeatItem()` foi usado para fazer referência ao item atual sendo feita a iteração. Isso foi simplificado para just `@item()`. 

### <a name="referencing-the-outputs-of-the-foreach"></a>Referenciando saídas de Foreach
Para simplificar ainda mais, saídas de ações de **Foreach** não serão ajustadas em um objeto chamado **repeatItems**. Isso significa, enquanto as saídas de repetir acima foram:

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

Agora ele será:

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Ao referenciar essas saídas, para acessar o corpo da ação que você precisará fazer:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "repeat" : "@outputs('pingBing').repeatItems",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@repeatItem().outputs.body"
            }
        }
    }
}
```

Agora você pode fazer em vez disso:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "foreach" : "@outputs('pingBing')",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@item().outputs.body"
            }
        }
    }
}
```

Com essas alterações, as funções `@repeatItem()`, `@repeatBody()` e `@repeatOutputs()` são removidos.

## <a name="3-native-http-listener"></a>3. nativo ouvinte HTTP 
Os recursos de ouvinte HTTP agora são internos, para que você não precisa implantar um aplicativo de API de ouvinte HTTP. Leia sobre [os detalhes completos sobre como tornar sua lógica aplicativo ponto de extremidade acessível aqui](app-service-logic-http-endpoint.md). 

Com essas alterações, a função `@accessKeys()` é removido e foi substituído com o `@listCallbackURL()` função para os fins obtendo o ponto de extremidade (quando necessário). Além disso, agora você deve definir pelo menos um disparador em seu aplicativo de lógica agora. Se você quiser `/run` o fluxo de trabalho, você precisará ter uma de um `manual`, `apiConnectionWebhook` ou `httpWebhook` disparadores. 

## <a name="4-calling-child-workflows"></a>4. fluxos de trabalho de filho de chamada

Anteriormente, chamar fluxos de trabalho filho obrigatório indo para esse fluxo de trabalho, obtendo o token de acesso e, em seguida, colar que está para a definição do aplicativo lógica que você deseja chamar criança. Com a nova versão de esquema, o mecanismo de aplicativos lógica gerará automaticamente uma SAS em tempo de execução do fluxo de trabalho de filho, o que significa que você não precisa cole qualquer segredos a definição.  Aqui está um exemplo:

```
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName" : "myendpointtrigger"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "conditions" : []
}
```

Uma segunda melhoria é que podemos dará os fluxos de trabalho filho acesso total à solicitação de entrada. Isso significa que você pode passar parâmetros na seção *consultas* e no objeto *cabeçalhos* e que você pode definir totalmente todo o corpo.

Por fim, há as alterações necessárias para o fluxo de trabalho filho. Enquanto antes você poderia apenas chamar um fluxo de trabalho filho diretamente; Agora, você precisará definir um ponto de extremidade do disparador no fluxo de trabalho para o pai chamar. Em geral, isso significa que você adicionará um disparador do tipo **manual** e usá-lo na definição do pai. Observe que o `host` propriedade especificamente tem um `triggerName`, porque você sempre deve especificar qual disparadores que você está chamando.

## <a name="other-changes"></a>Outras alterações

### <a name="new-queries-property"></a>Nova propriedade de consultas
Todos os tipos de ação agora oferecem suporte a uma nova entrada chamada **consultas**. Isso pode ser um objeto estruturado em vez de ter de montar a cadeia de caracteres manualmente.

### <a name="parse-function-renamed"></a>função Parse renomeada
Como breve adicionaremos mais tipos de conteúdo, a `parse()` função foi renomeada para `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Em breve: APIs de integração do Enterprise
Neste momento, nós ainda não tiver gerenciado versões das APIs de integração do Enterprise disponíveis (como AS2). Esses estarão em breve como cobertos no [mapa](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). Enquanto isso, você pode usar seu APIs BizTalk implantado existentes por meio da ação de HTTP, conforme abordados acima em "usando seus aplicativos de API já implantados."
