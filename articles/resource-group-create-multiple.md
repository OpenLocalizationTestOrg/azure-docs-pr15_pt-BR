<properties
   pageTitle="Implantar várias instâncias de recursos | Microsoft Azure"
   description="Use a operação de cópia e matrizes em um modelo do Gerenciador de recursos do Azure para iteramos várias vezes quando a implantação de recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/30/2016"
   ms.author="tomfitz"/>

# <a name="create-multiple-instances-of-resources-in-azure-resource-manager"></a>Criar várias instâncias de recursos no Gerenciador de recursos do Azure

Este tópico mostra como iteramos no seu modelo de Gerenciador de recursos do Azure para criar várias instâncias de um recurso.

## <a name="copy-copyindex-and-length"></a>Copiar, copyIndex e comprimento

Dentro do recurso para criar várias vezes, você pode definir um objeto de **cópia** que especifica o número de vezes que a iteração. A cópia leva o seguinte formato:

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

Você pode acessar o valor de iteração atual com a função **copyIndex()** , como mostrado abaixo dentro da função concat.

    [concat('examplecopy-', copyIndex())]

Ao criar vários recursos de uma matriz de valores, você pode usar a função **comprimento** para especificar a contagem. Você pode fornecer a matriz como o parâmetro para a função de comprimento.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## <a name="use-index-value-in-name"></a>Use o valor de índice no nome

Você pode usar a cópia operação criar várias instâncias de um recurso que são nomeados exclusivamente com base no índice incremento. Por exemplo, talvez você queira adicionar um número exclusivo para o final de cada nome de recurso que é implantado. Para implantar três sites chamado:

- examplecopy-0
- examplecopy-1
- examplecopy-2.

Use o modelo a seguir:

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          }
      } 
    ]

## <a name="offset-index-value"></a>Valor de índice de deslocamento

Você notará no exemplo anterior que o valor de índice vai de zero a 2. Para deslocar o valor de índice, você pode passar um valor na função **copyIndex()** , como **copyIndex(1)**. O número de iterações realizem ainda está especificado no elemento de cópia, mas o valor de copyIndex é deslocado pelo valor especificado. Portanto, usando o mesmo modelo como exemplo anterior, mas especificando **copyIndex(1)** seria implantar três sites chamado:

- examplecopy-1
- examplecopy-2
- examplecopy-3

## <a name="use-copy-with-array"></a>Usar cópia com array
   
A operação de cópia é particularmente úteis ao trabalhar com matrizes porque você pode percorrer cada elemento da matriz. Para implantar três sites chamado:

- examplecopy-Contoso
- Fabrikam examplecopy
- examplecopy-Coho

Use o modelo a seguir:

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      }
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          } 
      } 
    ]

Claro, você define a contagem de cópia para um valor diferente do comprimento da matriz. Por exemplo, você pode criar uma matriz com muitos valores e, em seguida, passe em um valor de parâmetro que especifica quantos os elementos da matriz para implantar. Nesse caso, você definir o número de cópias, conforme mostrado no primeiro exemplo. 

## <a name="depending-on-resources-in-a-loop"></a>Dependendo dos recursos em um loop

Você pode especificar que um recurso ser implantado após outro recurso usando o elemento de **dependsOn** . Quando você precisa implantar um recurso que depende do conjunto de recursos em um loop, você pode usar fornecer o nome do loop cópia no elemento **dependsOn** . O exemplo a seguir mostra como implantar 3 contas de armazenamento antes de implantar a máquina Virtual. A definição de máquina Virtual completa não é mostrada. Observe que o elemento de cópia tem **nome** definido como **storagecopy** e o elemento de **dependsOn** para as máquinas virtuais também é definido para **storagecopy**.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "resources": [
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "accountType": "Standard_LRS"
                 },
                "copy": { 
                    "name": "storagecopy", 
                    "count": 3 
                }
            },
           {
               "apiVersion": "2015-06-15", 
               "type": "Microsoft.Compute/virtualMachines", 
               "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
               "dependsOn": ["storagecopy"],
               ...
           }
        ],
        "outputs": {}
    }

## <a name="looping-on-a-nested-resource"></a>Loop em um recurso aninhado

Você não pode usar um loop de cópia de um recurso aninhado. Se você precisar criar várias instâncias de um recurso que você normalmente define como aninhado dentro de outro recurso, em vez disso, você deve criar o recurso como um recurso de nível superior e definir a relação com o recurso de pai através das propriedades de **tipo** e **nome** .

Por exemplo, suponha que você geralmente define um conjunto de dados como um recurso aninhado dentro de uma fábrica de dados.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "string"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "[parameters('dataSetName')]",
            "dependsOn": [
                "[parameters('dataFactoryName')]"
            ],
            ...
        }
    }]
    
Para criar várias instâncias de conjuntos de dados, você precisa alterar seu modelo, conforme mostrado abaixo. Observe o tipo qualificado completo e o nome inclui o nome de fábrica de dados.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "array"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat(parameters('dataFactoryName'), '/', parameters('dataSetName')[copyIndex()])]",
        "dependsOn": [
            "[parameters('dataFactoryName')]"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "[length(parameters('dataSetName'))]" 
        } 
        ...
    }]

## <a name="create-multiple-instances-when-copy-wont-work"></a>Criar várias instâncias quando copiar não funcionará

Você só pode usar **cópia** em tipos de recursos, não em propriedades dentro de um tipo de recurso. Isso pode criar problemas para você quando você quiser criar várias instâncias de algo que faz parte de um recurso. Um cenário comum é criar vários discos de dados para uma máquina Virtual. Você não pode usar **cópia** com os discos de dados como **dataDisks** é uma propriedade na máquina Virtual, não seu próprio tipo de recurso. Em vez disso, você cria uma matriz com quantos discos de dados que você precisa e passar o número real de discos de dados para criar. Na definição de máquina virtual, você pode usar a função **tirar** para obter apenas o número de elementos que deseja realmente da matriz.

Um exemplo completo desse padrão é mostrada no modelo de [criar uma máquina virtual com uma seleção dinâmica de discos de dados](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) .

As seções relevantes do modelo de implantação são mostradas abaixo. Muitas o modelo foi removida para realçar as seções envolvidas na dinamicamente a criação de um número de discos de dados. Observe que o parâmetro **numDataDisks** que permite passar o número de discos para criar. 

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ...
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        ...
        "storageProfile": {
          ...
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```


## <a name="next-steps"></a>Próximas etapas
- Se você quiser saber mais sobre as seções de um modelo, consulte [Criação de modelos do Azure Gerenciador de recursos](./resource-group-authoring-templates.md).
- Para todas as funções que você pode usar em um modelo, consulte [Funções de modelo de Gerenciador de recursos do Azure](./resource-group-template-functions.md).
- Para saber como implantar o seu modelo, consulte [implantar um aplicativo com modelo de Gerenciador de recursos do Azure](resource-group-template-deploy.md).
