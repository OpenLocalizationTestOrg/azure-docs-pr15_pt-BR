<properties
   pageTitle="Dependências no Gerenciador de recurso modelos | Microsoft Azure"
   description="Descreve como definir um recurso como dependente outro recurso durante a implantação para garantir recursos sejam implantados na ordem correta."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="defining-dependencies-in-azure-resource-manager-templates"></a>Definindo dependências no Gerenciador de recursos do Azure modelos

Para um determinado recurso, pode haver outros recursos que devem existir antes que o recurso seja implantado. Por exemplo, um SQL server deve existir antes de tentar implantar um banco de dados do SQL. Você definir essa relação marcando um recurso como dependente do outro recurso. Normalmente, você define uma dependência com o elemento de **dependsOn** , mas você também pode defini-la por meio da função de **referência** . 

Gerenciador de recursos avalia as dependências entre recursos e implanta-los em sua ordem dependente. Quando recursos não são dependentes um do outro, o Gerenciador de recursos os implanta em paralelo.

## <a name="dependson"></a>dependsOn

Dentro de seu modelo, o elemento de dependsOn permite que você defina um recurso como um dependente em um ou mais recursos. Seu valor pode ser uma lista separada por vírgulas de nomes de recursos. 

O exemplo a seguir mostra um conjunto de escala de máquina virtual que depende de um balanceador de carga, rede virtual e um loop que cria várias contas de armazenamento. Esses outros recursos não são mostrados no exemplo a seguir, mas eles precisam existir em outro lugar no modelo.

    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('namingInfix')]",
      "location": "[variables('location')]",
      "apiVersion": "2016-03-30",
      "tags": {
        "displayName": "VMScaleSet"
      },
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      ...
    }

Para definir uma dependência entre um recurso e recursos que são criados por meio de um loop de cópia, defina o elemento dependsOn ao nome do loop. Por exemplo, consulte [criar várias instâncias de recursos no Gerenciador de recursos do Azure](resource-group-create-multiple.md).

Enquanto você pode ser inclinados a usar dependsOn para mapear relações entre seus recursos, é importante entender por que você está fazendo-lo porque ele pode afetar o desempenho da sua implantação. Por exemplo, para documentar como os recursos são interconectados, dependsOn não é a abordagem certa. Você não pode consultar quais recursos foram definidos no elemento dependsOn após a implantação. Usando dependsOn, você afeta o tempo de implantação porque o Gerenciador de recursos não implantar em paralelos dois recursos que têm uma dependência. Relações entre recursos do documento, em vez disso use o [recurso de vinculação](resource-group-link-resources.md).

## <a name="child-resources"></a>Recursos de filho

A propriedade de recursos permite que você especifique recursos filho que estão relacionados ao recurso está sendo definido. Recursos de filho só podem ser definidos cinco níveis. É importante observar que uma dependência implícita não é criada entre um recurso de filho e o pai. Se você precisar o recurso de filho para ser implantado após o recurso pai, você deve declarar explicitamente essa dependência com a propriedade dependsOn. 

Cada recurso pai aceita somente determinados tipos de recursos como recursos de filho. Os tipos de recurso aceitas são especificados no [esquema de modelo](https://github.com/Azure/azure-resource-manager-schemas) do recurso pai. O nome do tipo de recurso de filho inclui o nome do tipo de recurso pai, como **Microsoft.Web/sites/config** e **Microsoft.Web/sites/extensions** são ambos os recursos de filho da **Microsoft.Web/sites**.

O exemplo a seguir mostra um SQL server e o banco de dados SQL. Observe que uma dependência explícita é definida entre o banco de dados SQL e SQL server, mesmo que o banco de dados é um filho do servidor.

    "resources": [
      {
        "name": "[variables('sqlserverName')]",
        "type": "Microsoft.Sql/servers",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "SqlServer"
        },
        "apiVersion": "2014-04-01-preview",
        "properties": {
          "administratorLogin": "[parameters('administratorLogin')]",
          "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
        },
        "resources": [
          {
            "name": "[parameters('databaseName')]",
            "type": "databases",
            "location": "[resourceGroup().location]",
            "tags": {
              "displayName": "Database"
            },
            "apiVersion": "2014-04-01-preview",
            "dependsOn": [
              "[variables('sqlserverName')]"
            ],
            "properties": {
              "edition": "[parameters('edition')]",
              "collation": "[parameters('collation')]",
              "maxSizeBytes": "[parameters('maxSizeBytes')]",
              "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
            }
          }
        ]
      }
    ]


## <a name="reference-function"></a>função de referência

A [função de referência](resource-group-template-functions.md#reference) permite uma expressão para gerar seu valor a partir de outro nome JSON e pares de valor ou recursos de tempo de execução. Expressões de referência declarar implicitamente que um recurso depende de outro. 

    reference('resourceName').propertyPath

Você pode usar esse elemento ou o elemento dependsOn para especificar dependências, mas você não precisa usar ambos para o mesmo recurso dependente. Sempre que possível, use uma referência implícita para evitar inadvertidamente adicionando uma dependência desnecessária.

Para saber mais, consulte [referência de função](resource-group-template-functions.md#reference).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a criação de modelos do Gerenciador de recursos do Azure, consulte [modelos de criação de páginas](resource-group-authoring-templates.md). 
- Para obter uma lista das funções disponíveis em um modelo, consulte [funções de modelo](resource-group-template-functions.md).

