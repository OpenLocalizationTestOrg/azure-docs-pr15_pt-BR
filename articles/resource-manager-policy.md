<properties
    pageTitle="Diretiva de Gerenciador de recursos Azure | Microsoft Azure"
    description="Descreve como usar diretiva de Gerenciador de recursos do Azure para evitar violações em escopos diferentes como assinatura, grupos de recursos ou recursos individuais."
    services="azure-resource-manager"
    documentationCenter="na"
    authors="ravbhatnagar"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="gauravbh;tomfitz"/>

# <a name="use-policy-to-manage-resources-and-control-access"></a>Usar diretiva para gerenciar recursos e controlar o acesso

Gerenciador de recursos de Azure agora permite controlar acesso por meio de diretivas personalizadas. Com as diretivas, você pode impedir que os usuários em sua organização quebre convenções que são necessárias para gerenciar recursos da sua organização. 

Criar definições de políticas que descrevem as ações ou os recursos que são especificamente negados. Você atribuir as definições de política no escopo desejado, como a assinatura, grupo de recursos ou um recurso individual. 

Neste artigo, explicaremos a estrutura básica da linguagem de definição de política que você pode usar para criar políticas. Em seguida, podemos descrevem como você pode aplicar essas políticas em escopos diferentes.

## <a name="how-is-it-different-from-rbac"></a>Como ele é diferente do RBAC?

Há algumas diferenças essenciais entre política e controle de acesso baseado em função, mas a primeira coisa a entender é que políticas e RBAC funcionam juntos. Para usar políticas, você deve ser autenticado por meio de RBAC. Ao contrário de RBAC, política é um padrão permitir e explícita negar sistema. 

RBAC destaca as ações que um **usuário** pode realizar em escopos diferentes. Por exemplo, um usuário específico é adicionado à função Colaborador de um grupo de recursos no escopo desejado, para que o usuário pode fazer alterações a esse grupo de recursos. 

Política se concentra nos **recursos** ações em vários escopos. Por exemplo, por meio de políticas, você pode controlar os tipos de recursos que podem ser provisionados ou restringem os locais nos quais os recursos podem ser provisionados.

## <a name="common-scenarios"></a>Cenários comuns

Um cenário comum é requerem marcas departamentais para finalidade de devolução. Uma organização talvez queira permitir operações somente quando o Centro de custo apropriado está associado; Caso contrário, eles negar a solicitação. Essa política ajuda a cobrar o Centro de custos apropriado para as operações executadas.

Outro cenário comum é que a organização talvez queira controlar os locais onde os recursos são criados. Ou eles talvez queira controlar o acesso aos recursos, permitindo que somente determinados tipos de recursos para ser provisionado.

Da mesma forma, uma organização pode controlar o catálogo de serviço ou impor as convenções de nomenclatura desejadas para os recursos.

Usando políticas, esses cenários facilmente podem ser obtidos.

## <a name="policy-definition-structure"></a>Estrutura de definição de política

Definição de política é criada usando JSON. Ele consiste em um ou mais operadores lógicos/condições que definem as ações e um efeito que informa o que acontece quando as condições são atendidas. O esquema é publicado em [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 

Basicamente, uma política contém os seguintes elementos:

**Condição/lógica operadores:** um conjunto de condições que podem ser manipulados por meio de um conjunto de operadores lógicos.

**Efeito:** o que acontece quando a condição é satisfeita – negar ou auditoria. Um efeito de auditoria emite um log de serviço de evento de aviso. Por exemplo, um administrador pode criar uma política que faz com que um evento de auditoria se alguém cria uma máquina virtual grande. O administrador pode examinar os logs mais tarde.

    {
      "if" : {
          <condition> | <logical operator>
      },
      "then" : {
          "effect" : "deny | audit | append"
      }
    }
    
## <a name="policy-evaluation"></a>Avaliação de política

Políticas são avaliadas quando recursos são criados. No caso de implantação de modelo, políticas são avaliadas durante a criação de cada recurso no modelo. 

> [AZURE.NOTE] Atualmente, não é política avaliar tipos de recursos que não suportam marcas, tipo e local, como o tipo de recurso Microsoft.Resources/deployments. Esse suporte será adicionado no futuro. Para evitar problemas de compatibilidade com versões anteriores, você deverá especificar explicitamente tipo quando políticas de criação. Por exemplo, uma política de marca que não especificar tipos é aplicada para todos os tipos. Nesse caso, uma implantação do modelo pode falhar se houver um recurso aninhado que não dá suporte a marcas e o tipo de recurso de implantação foi adicionado à avaliação de política. 

## <a name="logical-operators"></a>Operadores lógicos

Os operadores lógicos suportados juntamente com a sintaxe são:

| Nome do operador     | Sintaxe         |
| :------------- | :------------- |
| Não            | "não": {&lt;condição ou operador &gt;}             |
| E           | "allOf": [{&lt;condição ou operador &gt;}, {&lt;condição ou operador &gt;}] |
| Ou                         | "Nenhum": [{&lt;condição ou operador &gt;}, {&lt;condição ou operador &gt;}] |

Gerenciador de recursos permite especificar lógica complexa em sua política pelos operadores aninhadas. Por exemplo, você pode negar criação de recurso em um local específico para um tipo de recurso especificado. Um exemplo de operadores aninhadas é mostrado abaixo.

## <a name="conditions"></a>Condições

Uma condição avalia se um **campo** ou **fonte** atende a determinados critérios. Os nomes de condição com suporte e sintaxe são:

| Nome da condição | Sintaxe                |
| :------------- | :------------- |
| É igual a             | "é igual a": "&lt;valor&gt;"               |
| Como                  | "curtir": "&lt;valor&gt;"                   |
| Contém          | "contém": "&lt;valor&gt;"|
| Em                        | "em": ["&lt;valor1&gt;","&lt;valor2&gt;"]|
| ContainsKey    | "containsKey": "&lt;keyName&gt;" |
| Existe     | "existe": "&lt;bool&gt;" |

### <a name="fields"></a>Campos

Condições são formadas usando campos e fontes. Um campo representa propriedades na carga de solicitação de recurso que é usado para descrever o estado do recurso. Uma fonte representa características da solicitação em si. 

Os seguintes campos e fontes são suportadas:

Campos: **nome**, **tipo**, **tipo**, **local**, **marcas**, **marcas.** *, and * *alias de propriedade**. 

### <a name="property-aliases"></a>Alias de propriedade 
Alias de propriedade é um nome que pode ser usado em uma definição de política para acessar as recurso tipo propriedades específicas, como configurações e skus. Ele funciona em todas as versões de API onde existe a propriedade. Aliases podem ser recuperadas usando a API REST mostrado abaixo (Powershell suporte será adicionado no futuro):

    GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
    
A definição de um alias é mostrada abaixo. Como você pode ver, um alias define caminhos em diferentes versões de API, mesmo quando há uma alteração de nome de propriedade. 

    "aliases": [
        {
          "name": "Microsoft.Storage/storageAccounts/sku.name",
          "paths": [
            {
              "path": "properties.accountType",
              "apiVersions": [
                "2015-06-15",
                "2015-05-01-preview"
              ]
            },
            {
              "path": "sku.name",
              "apiVersions": [
                "2016-01-01"
              ]
            }
          ]
        }
    ]

Atualmente, os aliases com suporte são:

| Nome de alias | Descrição |
| ---------- | ----------- |
| {resourceType}/sku.name | Tipos de recursos com suporte são: Microsoft.Compute/virtualMachines,<br />Microsoft.Storage/storageAccounts,<br />Microsoft.Web/serverFarms,<br /> Microsoft.Scheduler/jobcollections,<br />Microsoft.DocumentDB/databaseAccounts,<br />Microsoft.Cache/Redis,<br />Microsoft.CDN/profiles |
| {resourceType}/sku.family | Tipo de recurso com suporte é Microsoft.Cache/Redis |
| {resourceType}/sku.capacity | Tipo de recurso com suporte é Microsoft.Cache/Redis |
| Microsoft.Compute/virtualMachines/imagePublisher |  |
| Microsoft.Compute/virtualMachines/imageOffer  |  |
| Microsoft.Compute/virtualMachines/imageSku  |  |
| Microsoft.Compute/virtualMachines/imageVersion  |  |
| Microsoft.Cache/Redis/enableNonSslPort |  |
| Microsoft.Cache/Redis/shardCount |  |
| Microsoft.SQL/servers/version |  |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveId |  |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveName |  |
| Microsoft.SQL/servers/databases/edition |  |
| Microsoft.SQL/servers/databases/elasticPoolName |  |
| Microsoft.SQL/servers/elasticPools/dtu |  |
| Microsoft.SQL/servers/elasticPools/edition |  |

Atualmente, política só funciona em solicitações de colocar. 

## <a name="effect"></a>Efeito
Diretiva dá suporte a três tipos de efeito - **Negar**, **auditoria**e **Acrescentar**. 

- Negar gera um evento no log de auditoria e falhar a solicitação
- Auditoria gera um evento no log de auditoria, mas não falha na solicitação
- Acrescentar adiciona o conjunto de campos definido para a solicitação 

Para **Acrescentar**, você deve fornecer os seguintes detalhes:

    ....
    "effect": "append",
    "details": [
      {
        "field": "field name",
        "value": "value of the field"
      }
    ]

O valor pode ser uma cadeia de caracteres ou um objeto no formato JSON. 

## <a name="policy-definition-examples"></a>Exemplos de definição de políticas

Agora vamos observar como podemos definir a política para obter cenários anteriores.

### <a name="chargeback-require-departmental-tags"></a>Devolução: Requerem marcas departamentais

A seguinte diretiva nega solicitações que não têm uma marca que contém a chave "costCenter".

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

A seguinte diretiva acrescenta marca costCenter com um valor predefinido quando nenhuma marca está presente. 

    {
      "if": {
        "field": "tags",
        "exists": "false"
      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags",
            "value": {"costCenter":"myDepartment" }
          }
        ]
      }
    }
    
A seguinte diretiva acrescenta marca costCenter com um valor predefinido quando a marca costCenter não estiver presente, mas outras marcas estão presentes. 

    {
      "if": {
        "allOf": [
          {
            "field": "tags",
            "exists": "true"
          },
          {
            "field": "tags.costCenter",
            "exists": "false"
          }
        ]
    
      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags.costCenter",
            "value": "myDepartment"
          }
        ]
      }
    }


### <a name="geo-compliance-ensure-resource-locations"></a>Localização geográfica conformidade: Garantir locais do recurso

O exemplo a seguir mostra uma política que nega solicitações onde local não é Europa do Norte ou Europa Oeste.

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="service-curation-select-the-service-catalog"></a>Curadoria de serviço: Selecione o catálogo de serviço

O exemplo a seguir mostra uma política que permite ações somente nos serviços do tipo Microsoft.Resources/\*, Microsoft.Compute/\*, Microsoft.Storage/\*, Microsoft.Network/\* são permitidos. Qualquer outra coisa é negada.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "field" : "type",
              "like" : "Microsoft.Resources/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Compute/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Storage/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="use-approved-skus"></a>Use SKUs aprovados

O exemplo a seguir mostra o uso de alias de propriedade para restringir SKUs. No exemplo, apenas Standard_LRS e Standard_GRS são aprovados para contas de armazenamento.

    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "allof": [
                {
                  "field": "Microsoft.Storage/storageAccounts/sku.name",
                  "in": ["Standard_LRS", "Standard_GRS"]
                }
              ]
            }
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }
    

### <a name="naming-convention"></a>Convenção de nomenclatura

O exemplo a seguir mostra o uso de curinga, que é suportado pela condição "curtir". A condição informa que, se o nome corresponder ao padrão mencionado (namePrefix\*nameSuffix), em seguida, negar a solicitação.

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }
    
### <a name="tag-requirement-just-for-storage-resources"></a>Requisito de marca apenas de recursos de armazenamento

O exemplo a seguir mostra como aninhar operadores lógicos para exigir uma marca de aplicativo para somente os recursos de armazenamento.

    {
        "if": {
            "allOf": [
              {
                "not": {
                  "field": "tags",
                  "containsKey": "application"
                }
              },
              {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
              }
            ]
        },
        "then": {
            "effect": "audit"
        }
    }

## <a name="policy-assignment"></a>Atribuição de política

Diretivas são aplicadas em escopos diferentes como assinatura, grupos de recursos e recursos individuais. Políticas são herdadas por todos os recursos de filho. Portanto, se uma política é aplicada a um grupo de recursos, é aplicável a todos os recursos desse grupo de recursos.

## <a name="creating-a-policy"></a>Criar uma política

Esta seção fornece detalhes sobre como uma política pode ser criada usando API REST.

### <a name="create-policy-definition-with-rest-api"></a>Criar definição de política com a API REST

Você pode criar uma política com a [API REST para definições de política](https://msdn.microsoft.com/library/azure/mt588471.aspx). A API REST permite criar e excluir definições de política e obter informações sobre definições existentes.

Para criar uma política, execute:

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

Com um corpo de solicitação semelhante ao seguinte exemplo:

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "name":"testdefinition"
    }


Para a versão da api use *2016-04-01*. Para obter exemplos e obter mais detalhes, consulte [API REST para definições de política](https://msdn.microsoft.com/library/azure/mt588471.aspx).

### <a name="create-policy-definition-using-powershell"></a>Criar definição de política de uso do PowerShell

Você pode criar uma definição de política usando o cmdlet New-AzureRmPolicyDefinition. O exemplo a seguir cria uma política para permitir que recursos somente na Europa do Norte e Europa Oeste.

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{  
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'          

A saída de execução é armazenada no objeto de $policy e pode ser usada durante a atribuição de política. Para o parâmetro de política, o caminho para um arquivo de .json que contém a política também pode ser fornecido em vez de especificando o embutido de política.

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain    regions" -Policy "path-to-policy-json-on-disk"

### <a name="create-policy-definition-using-azure-cli"></a>Criar definição de política usando CLI do Azure

Você pode criar uma definição de política usando a CLI azure com o comando de definição de política conforme mostrado abaixo. O exemplo a seguir cria uma política para permitir que recursos somente na Europa do Norte e Europa Oeste.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{   
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'    
    

É possível especificar o caminho para um arquivo de .json contendo a política em vez de especificando o embutido de política, conforme mostrado abaixo.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"


## <a name="applying-a-policy"></a>Aplicar uma política

### <a name="policy-assignment-with-rest-api"></a>Atribuição de política com a API REST

Você pode aplicar a definição de política no escopo desejado por meio da [API REST para atribuições de política](https://msdn.microsoft.com/library/azure/mt588466.aspx). A API REST permite criar e excluir atribuições de política e obter informações sobre atribuições existentes.

Para criar uma atribuição de política, execute:

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{Política-atribuição} é o nome da atribuição política. Para a versão da api use *2016-04-01*. 

Com um corpo de solicitação semelhante ao seguinte exemplo:

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "name":"VMPolicyAssignment"
    }

Para obter exemplos e obter mais detalhes, consulte [API REST para atribuições de política](https://msdn.microsoft.com/library/azure/mt588466.aspx).

### <a name="policy-assignment-using-powershell"></a>Atribuição de política usando o PowerShell

Você pode aplicar a política criada acima através do PowerShell ao escopo desejado usando o cmdlet New-AzureRmPolicyAssignment:

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
Veja $policy é o objeto de política que foi retornado como resultado de executar o cmdlet New-AzureRmPolicyDefinition, conforme mostrado acima. O escopo aqui é o nome do grupo de recursos que você especificar.

Se você quiser remover a atribuição de política acima, você poderá fazer isso da seguinte maneira:

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Você pode obter, alterar ou remover definições de políticas por meio de Get-AzureRmPolicyDefinition, Set-AzureRmPolicyDefinition e remover AzureRmPolicyDefinition cmdlets respectivamente.

Da mesma forma, você pode obter, alterar ou remover atribuições de política pela Get-AzureRmPolicyAssignment, Set-AzureRmPolicyAssignment e remover AzureRmPolicyAssignment cmdlets, respectivamente.

### <a name="policy-assignment-using-azure-cli"></a>Atribuição de política usando CLI do Azure

Você pode aplicar a política criada acima por meio do Azure CLI ao escopo desejado, usando o comando de atribuição de política:

    azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/<policy-name> --scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
O escopo aqui é o nome do grupo de recursos que você especificar. Se o valor do parâmetro política-definição-id for desconhecido, é possível obtê-lo através da CLI do Azure, conforme mostrado abaixo: 

    azure policy definition show <policy-name>

Se você quiser remover a atribuição de política acima, você poderá fazer isso da seguinte maneira:

    azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Você pode obter, alterar ou remover política definições por meio da definição de política mostram, definir e excluir comandos respectivamente.

Da mesma forma, você pode obter, alterar ou remover política atribuições com a atribuição de política mostram comandos e delete respectivamente.

##<a name="policy-audit-events"></a>Eventos de política de auditoria

Depois que você aplicou sua política, você começará a ver eventos relacionados à política. Você pode ir portal, use PowerShell ou a CLI do Azure para obter esses dados. 

### <a name="policy-audit-events-using-powershell"></a>Usando o PowerShell eventos de política de auditoria

Para exibir todos os eventos relacionados a negar o efeito, você pode usar o seguinte comando do PowerShell:

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/deny/action"} 

Para exibir todos os eventos relacionados a auditoria efeito, você pode usar o seguinte comando:

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/audit/action"} 

### <a name="policy-audit-events-using-azure-cli"></a>Eventos de política de auditoria usando CLI do Azure

Ver todos os eventos de um recurso de grupo que relacionados para negar o efeito, você pode usar o seguinte comando CLI:

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/deny/action\")"

Para exibir todos os eventos relacionados a auditoria efeito, você pode usar o seguinte comando CLI:

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/audit/action\")"
