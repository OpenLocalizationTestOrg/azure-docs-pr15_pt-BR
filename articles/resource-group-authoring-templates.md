<properties
   pageTitle="Modelos do Azure Gerenciador de recursos de criação | Microsoft Azure"
   description="Crie modelos de Gerenciador de recursos do Azure usando a sintaxe declarativa de JSON para implantar aplicativos do Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="tomfitz"/>

# <a name="authoring-azure-resource-manager-templates"></a>Criação de modelos do Gerenciador de recursos do Azure

Este tópico descreve a estrutura de um modelo do Gerenciador de recursos do Azure. Ele apresenta as diferentes seções de um modelo e as propriedades que estão disponíveis em dessas seções. O modelo consiste em JSON e expressões que você pode usar para construir valores para a sua implantação. 

Para exibir o modelo para os recursos que você já implantou, consulte [Exportar um modelo do Gerenciador de recursos do Azure de recursos existentes](resource-manager-export-template.md). Para obter orientação sobre como criar um modelo, consulte [Explicação passo a passo de modelo do Gerenciador de recursos](resource-manager-template-walkthrough.md). Para obter recomendações sobre criação de modelos, consulte [práticas recomendadas para a criação de modelos do Gerenciador de recursos do Azure](resource-manager-template-best-practices.md).

Um editor de JSON boa pode simplificar a tarefa de criação de modelos. Para obter informações sobre como usar o Visual Studio com seus modelos, consulte [Criando e implantando grupos de recursos Azure por meio do Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Para obter informações sobre como usar o código de VS, consulte [Trabalhando com modelos de Gerenciador de recursos do Azure no Visual Studio código](resource-manager-vs-code.md).

Limite o tamanho de seu modelo para 1 MB e cada arquivo de parâmetro para 64 KB. O limite de 1 MB se aplica para o estado final do modelo depois que ela foi expandida com definições de recurso iterativo e valores de variáveis e parâmetros. 

## <a name="template-format"></a>Formato de modelo

Em sua estrutura mais simples, um modelo contém os seguintes elementos:

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| Nome do elemento   | Necessário | Descrição
| :------------: | :------: | :----------
| $schema        |   Sim    | Local do arquivo de esquema de JSON que descreve a versão do idioma modelo. Use o URL mostrado no exemplo anterior.
| contentVersion |   Sim    | Versão do modelo (como 1.0.0.0). Você pode fornecer qualquer valor para esse elemento. Ao implantar usando o modelo de recursos, esse valor pode ser usado para certificar-se de que o modelo certo está sendo usado.
| parâmetros     |   Não     | Valores que são fornecidos quando implantação é executada para personalizar a implantação do recurso.
| variáveis      |   Não     | Valores que são usados como fragmentos JSON no modelo para simplificar expressões de linguagem do modelo.
| recursos      |   Sim    | Tipos de recursos que são implantados ou atualizados em um grupo de recursos.
| saídas        |   Não     | Valores que são retornados após a implantação.

Vamos examinar as seções do modelo mais detalhadamente neste tópico. Por agora, examinaremos algumas da sintaxe que constituem o modelo.

## <a name="expressions-and-functions"></a>Expressões e funções

A sintaxe básica do modelo é JSON. Entretanto, expressões e funções estendem o JSON que está disponível no modelo. Com expressões, você cria valores que não são valores literais estritas. Expressões são colocadas entre colchetes [e] e são avaliados quando o modelo é implantado. Expressões podem aparecer em qualquer lugar em um valor de cadeia de caracteres JSON e retornar sempre outro valor JSON. Se você precisa usar uma cadeia de caracteres literal que começa com um colchete [, você deve usar dois colchetes [[.

Normalmente, você usa expressões com funções para executar operações para configurar a implantação. Assim como em JavaScript, chamadas de função são formatadas como **functionName(arg1,arg2,arg3)**. Você se referir a propriedades usando os operadores de ponto e [índice].

O exemplo a seguir mostra como usar várias funções ao construir valores:
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

Para obter uma lista completa das funções de modelo, consulte [funções de modelo do Gerenciador de recursos do Azure](resource-group-template-functions.md). 


## <a name="parameters"></a>Parâmetros

Na seção parâmetros do modelo, você especificar quais valores que você pode inserir ao implantar os recursos. Esses valores de parâmetro permitem que você personalizar a implantação fornecendo valores que são adaptadas para um determinado ambiente (como desenvolvimento, teste e produção). Você não precisa fornecer parâmetros no seu modelo, mas sem parâmetros seu modelo sempre seria implantar os mesmos recursos com os mesmos nomes, locais e propriedades.

Você pode usar esses valores de parâmetro em todo o modelo para definir valores para os recursos implantados. Somente os parâmetros que são declarados na seção parâmetros podem ser usados em outras seções do modelo.

Definir parâmetros com a seguinte estrutura:

    "parameters": {
       "<parameter-name>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<default-value-of-parameter>",
         "allowedValues": [ "<array-of-allowed-values>" ],
         "minValue": <minimum-value-for-int>,
         "maxValue": <maximum-value-for-int>,
         "minLength": <minimum-length-for-string-or-array>,
         "maxLength": <maximum-length-for-string-or-array-parameters>,
         "metadata": {
             "description": "<description-of-the parameter>" 
         }
       }
    }

| Nome do elemento   | Necessário | Descrição
| :------------: | :------: | :----------
| nome do parâmetro  |   Sim    | Nome do parâmetro. Deve ser um identificador JavaScript válido.
| tipo           |   Sim    | Tipo do valor do parâmetro. Consulte a lista abaixo dos tipos permitidos.
| defaultValue   |   Não     | Valor padrão para o parâmetro, se nenhum valor for fornecido para o parâmetro.
| allowedValues  |   Não     | Matriz de valores permitidos para o parâmetro para certificar-se de que o valor correto é fornecido.
| minValue       |   Não     | O valor mínimo para os parâmetros de tipo int, esse valor é inclusivo.
| maxValue       |   Não     | O valor máximo para os parâmetros de tipo int, esse valor é inclusivo.
| minLength      |   Não     | O tamanho mínimo de cadeia de caracteres, secureString e parâmetros de tipo de matriz, esse valor é inclusivo.
| maxLength      |   Não     | O comprimento máximo de cadeia de caracteres, secureString e parâmetros de tipo de matriz, esse valor é inclusivo.
| Descrição    |   Não     | Descrição do parâmetro, que é exibido para os usuários do modelo por meio da interface de portal modelo personalizado.

Os valores e tipos permitidos são:

- **cadeia de caracteres**
- **secureString**
- **int**
- **bool**
- **objeto** 
- **secureObject**
- **matriz**

Para especificar um parâmetro como opcional, forneça defaultValue (pode ser uma cadeia de caracteres vazia). 

Se você especificar um nome de parâmetro que corresponde a um dos parâmetros no comando para implantar o modelo, você precisará fornecer um valor para um parâmetro com o sufixo **FromTemplate**. Por exemplo, se você incluir um parâmetro chamado **ResourceGroupName** no modelo que é a mesma que o parâmetro **ResourceGroupName** na [New-AzureRmResourceGroupDeployment] [ deployment2cmdlet] cmdlet, você for solicitado a fornecer um valor para **ResourceGroupNameFromTemplate**. Em geral, você deve evitar essa confusão nomeando não parâmetros com o mesmo nome usado para operações de implantação de parâmetros.

>[AZURE.NOTE] Todas as senhas, chaves e outros segredos devem usar o tipo de **secureString** . Parâmetros de modelo com o tipo de secureString não podem ser lido após a implantação do recurso. 

O exemplo a seguir mostra como definir parâmetros:

    "parameters": {
      "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
      },
      "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
      },
      "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
      },
      "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
      }
    }

Como os valores de parâmetro de entrada durante a implantação, consulte [implantar um aplicativo com o Gerenciador de recursos do Azure modelo](resource-group-template-deploy.md#parameter-file). 

## <a name="variables"></a>Variáveis

Na seção variáveis, você construir valores que podem ser usados em todo o seu modelo. Normalmente, as variáveis são baseados em valores fornecidos dos parâmetros. Você não precisa definir variáveis, mas eles geralmente simplificarem seu modelo, reduzindo expressões complexas.

Definir variáveis com a estrutura a seguir:

    "variables": {
       "<variable-name>": "<variable-value>",
       "<variable-name>": { 
           <variable-complex-type-value> 
       }
    }

O exemplo a seguir mostra como definir uma variável que é construída de dois valores de parâmetro:

     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

O exemplo a seguir mostra uma variável que é um tipo JSON complexo e variáveis que são criados de outras variáveis:

    "parameters": {
       "environmentName": {
         "type": "string",
         "allowedValues": [
           "test",
           "prod"
         ]
       }
    },
    "variables": {
       "environmentSettings": {
         "test": {
           "instancesSize": "Small",
           "instancesCount": 1
         },
         "prod": {
           "instancesSize": "Large",
           "instancesCount": 4
         }
       },
       "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
    }

## <a name="resources"></a>Recursos

Na seção de recursos, você deve definir os recursos que são implantados ou atualizados. Esta seção pode ficar complicada porque você deve compreender os tipos que você está implantando para fornecer os valores corretos. 

Você define recursos com a seguinte estrutura:

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "comments": "<your-reference-notes>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "copy": {
           "name": "<name-of-copy-loop>",
           "count": "<number-of-iterations>"
         }
         "resources": [
           "<array-of-child-resources>"
         ]
       }
    ]

| Nome do elemento             | Necessário | Descrição
| :----------------------: | :------: | :----------
| apiVersion               |   Sim    | Versão da API REST utilizar para criar o recurso.
| tipo                     |   Sim    | Tipo de recurso. Esse valor é uma combinação do namespace do provedor de recursos e o tipo de recurso (como **Microsoft.Storage/storageAccounts**).
| nome                     |   Sim    | Nome do recurso. O nome deve seguir restrições do componente URI definidas no RFC3986. Além disso, os serviços do Azure que expõem o nome do recurso para parceiros externos validar o nome para garantir que ele não é uma tentativa de simular outra identidade. Consulte [Verificar o nome do recurso](https://msdn.microsoft.com/library/azure/mt219035.aspx).
| local                 |   Varia  | Suporte para locais de localização geográfica do recurso fornecido. Você pode selecionar qualquer um dos locais disponíveis, mas geralmente faz sentido escolher um que seja Fechar para seus usuários. Geralmente, também faz sentido colocar recursos que interagem uns com os outros na mesma região. A maioria dos tipos de recursos exigem um local, mas alguns tipos (como uma atribuição de função) não exigem um local.
| marcas                     |   Não     | Marcas que estão associadas ao recurso.
| comentários                 |   Não     | Suas anotações para documentar os recursos no seu modelo
| dependsOn                |   Não     | Recursos que depende do recurso que está sendo definido. As dependências entre recursos são avaliadas e recursos são implantados em sua ordem dependente. Quando recursos não são dependentes uns dos outros, eles são implantados em paralelo. O valor pode ser uma lista separada por vírgulas de um recurso nomes ou identificadores exclusivos do recurso.
| Propriedades               |   Não     | Configurações específicas do recurso. Os valores para as propriedades são a mesma que os valores que você fornecer no corpo da solicitação para a operação de API REST (método de colocar) criar o recurso. Para links para documentação do esquema de recursos ou API REST, consulte [Gerenciador de recursos de provedores, regiões, as versões de API e esquemas](resource-manager-supported-services.md).
| Copiar                     |   Não     | Se for necessário mais de uma instância, o número de recursos para criar. Para obter mais informações, consulte [criar várias instâncias de recursos no Gerenciador de recursos do Azure](resource-group-create-multiple.md). |
| recursos                |   Não     | Recursos de filho que dependem do recurso que está sendo definido. Você pode fornecer somente os tipos de recursos que são permitidos pelo esquema do recurso pai. O nome totalmente qualificado do tipo de recurso filho inclui o tipo de recurso pai, como **Microsoft.Web/sites/extensions**. Dependência no recurso pai não é inferida; Você deve definir explicitamente essa dependência. 

Sabendo quais valores para especificar para **apiVersion**, **tipo**e **local** não é imediatamente óbvio. Felizmente, você pode determinar esses valores através do PowerShell do Azure ou CLI do Azure.

Para obter todos os provedores de recurso com o **PowerShell**, use:

    Get-AzureRmResourceProvider -ListAvailable

Na lista fornecida, localize os provedores de recurso que você está interessado. Para obter os tipos de recursos para um provedor de recursos (como armazenamento), use:

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes

Para obter as versões de API para um tipo de recurso (tais contas de armazenamento), use:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).ApiVersions

Para obter suporte locais para um tipo de recurso, use:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).Locations

Para obter todos os provedores de recurso com **Azure CLI**, use:

    azure provider list

Na lista fornecida, localize os provedores de recurso que você está interessado. Para obter os tipos de recursos para um provedor de recursos (como armazenamento), use:

    azure provider show Microsoft.Storage

Para acessar locais com suporte e as versões de API, use:

    azure provider show Microsoft.Storage --details --json

Para saber mais sobre os provedores de recursos, consulte [Gerenciador de recursos de provedores, regiões, as versões de API e esquemas](resource-manager-supported-services.md).

A seção de recursos contém uma matriz de recursos para implantar. Dentro de cada recurso, você também pode definir um conjunto de recursos filho. Portanto, a seção de recursos pode ter uma estrutura como:

    "resources": [
       {
           "name": "resourceA",
       },
       {
           "name": "resourceB",
           "resources": [
               {
                   "name": "firstChildResourceB",
               },
               {   
                   "name": "secondChildResourceB",
               }
           ]
       },
       {
           "name": "resourceC",
       }
    ]


O exemplo a seguir mostra um recurso de **Microsoft.Web/serverfarms** e um **Microsoft.Web/sites** com um recurso de **extensões** de filho. Observe que o site está marcado como dependentes no farm de servidores desde que o farm de servidores deve existir antes do site pode ser implantado. Observe também que o recurso de **extensões** é um filho do site.

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "name": "[parameters('hostingPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "HostingPlan"
        },
        "sku": {
          "name": "[parameters('skuName')]",
          "capacity": "[parameters('skuCapacity')]"
        },
        "properties": {
          "name": "[parameters('hostingPlanName')]",
          "numberOfWorkers": 1
        }
      },
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/sites",
        "name": "[parameters('siteName')]",
        "location": "[resourceGroup().location]",
        "tags": {
          "environment": "test",
          "team": "Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
        ],
        "properties": {
          "name": "[parameters('siteName')]",
          "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
        },
        "resources": [
          {
            "apiVersion": "2015-08-01",
            "type": "extensions",
            "name": "MSDeploy",
            "dependsOn": [
              "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
            ],
            "properties": {
              "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
              "dbType": "None",
              "connectionString": "",
              "setParameters": {
                "Application Path": "[parameters('siteName')]"
              }
            }
          }
        ]
      }
    ]


## <a name="outputs"></a>Saídas

Na seção saídas, você especificar valores que são retornados da implantação. Por exemplo, é possível retornar o URI para acessar um recurso implantado.

O exemplo a seguir mostra a estrutura de uma definição de saída:

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>"
       }
    }

| Nome do elemento   | Necessário | Descrição
| :------------: | :------: | :----------
| outputName     |   Sim    | Nome do valor de saída. Deve ser um identificador JavaScript válido.
| tipo           |   Sim    | Tipo do valor de saída. Valores de saída suportam os mesmos tipos como parâmetros de entrada do modelo.
| valor          |   Sim    | Expressão de idioma do modelo que é avaliada e retornada como o valor de saída.


O exemplo a seguir mostra um valor retornado da seção de saídas.

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

Para obter mais informações sobre como trabalhar com saída, consulte o [estado de compartilhamento em modelos do Gerenciador de recursos do Azure](best-practices-resource-manager-state.md).

## <a name="next-steps"></a>Próximas etapas
- Para exibir modelos concluídos para muitos tipos diferentes de soluções, consulte os [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/).
- Para obter detalhes sobre as funções que você pode usar de dentro de um modelo, consulte [Funções de modelo de Gerenciador de recursos do Azure](resource-group-template-functions.md).
- Para combinar vários modelos durante a implantação, consulte [usando modelos vinculados com o Gerenciador de recursos do Azure](resource-group-linked-templates.md).
- Você pode precisar usar recursos que existem dentro de um grupo de recurso diferente. Este cenário é comum ao trabalhar com contas de armazenamento ou redes virtuais que são compartilhadas entre vários grupos de recursos. Para obter mais informações, consulte a [função de identificação de recurso](resource-group-template-functions.md#resourceid).


[deployment2cmdlet]: https://msdn.microsoft.com/library/mt740620(v=azure.200).aspx
