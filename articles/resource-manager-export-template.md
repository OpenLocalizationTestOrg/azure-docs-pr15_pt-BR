<properties
    pageTitle="Exportar modelo do Gerenciador de recursos do Azure | Microsoft Azure"
    description="Use o Azure recurso Gerenciar para exportar um modelo de um grupo de recursos existente."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="tomfitz"/>

# <a name="export-an-azure-resource-manager-template-from-existing-resources"></a>Exportar um modelo do Gerenciador de recursos do Azure de recursos existentes

Gerenciador de recursos permite que você exportar um modelo do Gerenciador de recursos de recursos existentes em sua assinatura. Você pode usar esse modelo gerado para saber mais sobre a sintaxe de modelo ou para automatizar a reorganização da sua solução, conforme necessário.

É importante observar que há duas maneiras diferentes para exportar um modelo:

- Você pode exportar o modelo real que você usou para uma implantação. O modelo exportado inclui todos os parâmetros e variáveis exatamente como eles apareciam no modelo original. Essa abordagem é útil quando você implantou recursos por meio do portal. Agora, você deseja ver como construir o modelo para criar esses recursos.
- Você pode exportar um modelo que representa o estado atual do grupo de recursos. O modelo exportado não é baseado em qualquer modelo que você usou para implantação. Em vez disso, ele cria um modelo que é um instantâneo do grupo de recursos. O modelo exportado tem muitos valores embutidos e provavelmente não tantos parâmetros como você faria normalmente definir. Essa abordagem é útil quando você tiver modificado o grupo de recursos por meio do portal ou scripts. Agora, você precisa capturar o grupo de recursos como um modelo.

Este tópico mostra as duas abordagens.

Neste tutorial, você entrar no portal do Azure, crie uma conta de armazenamento e exportar o modelo para essa conta de armazenamento. Adicionar uma rede virtual para modificar o grupo de recursos. Por fim, você pode exportar um novo modelo que representa o estado atual. Embora este artigo aborda uma infraestrutura simplificada, você pode usar essas mesmas etapas para exportar um modelo para uma solução mais complicada.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. No [portal do Azure](https://portal.azure.com), selecione **novo** > **armazenamento** > **conta de armazenamento**.

      ![criar um armazenamento](./media/resource-manager-export-template/create-storage.png)

2. Crie uma conta de armazenamento com o nome de **armazenamento**, suas iniciais e a data. O nome da conta de armazenamento deve ser exclusivo entre Azure. Se o nome já está em uso, você verá uma mensagem de erro indicando que o nome está em uso. Tente uma variação. Grupo de recursos, crie um novo grupo de recursos e chame- **ExportGroup**. Você pode usar os valores padrão para as outras propriedades. Selecione **criar**.

      ![Forneça os valores para armazenamento](./media/resource-manager-export-template/provide-storage-values.png)

A implantação pode levar alguns minutos. Após concluir a implantação, sua assinatura contém a conta de armazenamento.

## <a name="view-a-template-from-deployment-history"></a>Exibir um modelo do histórico de implantação

1. Vá para a lâmina de grupo de recursos para o novo grupo de recursos. Observe que a lâmina mostra o resultado da última implantação. Clique neste link.

      ![blade de grupo de recursos](./media/resource-manager-export-template/resource-group-blade.png)

2. Você ver um histórico das implantações do grupo. No seu caso, a lâmina provavelmente lista apenas uma implantação. Selecione essa implantação.

     ![última implantação](./media/resource-manager-export-template/last-deployment.png)

3. A lâmina exibe um resumo da implantação. O resumo inclui o status de implantação e suas operações e os valores que você forneceu para parâmetros. Para ver o modelo que você usou para a implantação, selecione o **modelo de exibição**.

     ![Resumo de implantação do modo de exibição](./media/resource-manager-export-template/deployment-summary.png)

4. Gerenciador de recursos recupera os seguintes arquivos de seis para você:

   1. **Modelo** - o modelo que define a infraestrutura para sua solução. Quando você criou a conta de armazenamento por meio do portal, Gerenciador de recursos usado um modelo para implantá-lo e salvos nesse modelo para referência futura.
   2. **Parâmetros** - um arquivo de parâmetro que você pode usar para passar valores durante a implantação. Ele contém os valores que você forneceu durante a primeira implantação, mas você pode alterar qualquer um desses valores quando você reimplantar o modelo.
   3. **CLI** - um Azure comando linha interface (CLI) arquivo de script que você pode usar para implantar o modelo.
   4. **PowerShell** - arquivo de script do PowerShell do Azure que você pode usar para implantar o modelo.
   5. **.NET** - classe .NET A que você pode usar para implantar o modelo.
   6. **Ruby** - classe Ruby A que você pode usar para implantar o modelo.

     Os arquivos estão disponíveis por meio de links na lâmina. Por padrão, a lâmina exibe o modelo.

       ![modelo de exibição](./media/resource-manager-export-template/view-template.png)

     Vamos atenção especial para o modelo. Seu modelo deve ser semelhante a:

        {"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", "contentVersion": "1.0.0.0", "parâmetros": {"nome": {"tipo": "Cadeia"}, "accountType": {"tipo": "Cadeia"}, "local": {"tipo": "Cadeia"}, "encryptionEnabled": {"ValorPadrão": falso, "tipo": "Bool"}}, "recursos": [{"tipo": "Microsoft.Storage/storageAccounts", "sku": {"nome": "[parameters('accountType')]"}, "tipo": "Armazenamento", "nome": "[parameters('name')]", "apiVersion": "2016-01-01", "local": "[parameters('location')]", "propriedades": {"criptografia": {"serviços": {"blob": {"habilitado": "[parameters('encryptionEnabled')]"}}, "keySource": "Microsoft.Storage"}}}]}
 
Este modelo é o modelo real usado para criar sua conta de armazenamento. Observe que ele contém parâmetros que permitem que você implantar diferentes tipos de contas de armazenamento. Para saber mais sobre a estrutura de um modelo, consulte [modelos de coautoria Gerenciador de recursos do Azure](resource-group-authoring-templates.md). Para a lista completa das funções que você pode usar em um modelo, consulte [funções de modelo do Gerenciador de recursos do Azure](resource-group-template-functions.md).


## <a name="add-a-virtual-network"></a>Adicionar uma rede virtual

O modelo que você baixou na seção anterior representado a infraestrutura para essa implantação original. No entanto, ele serão não conta para quaisquer alterações feitas após a implantação.
Para ilustrar esse problema, vamos modificar o grupo de recursos, adicionando uma rede virtual através do portal.

1. Na lâmina recurso grupo, selecione **Adicionar**.

      ![adicionar recurso](./media/resource-manager-export-template/add-resource.png)

2. Selecione **rede Virtual** os recursos disponíveis.

      ![Selecione uma rede virtual](./media/resource-manager-export-template/select-vnet.png)

2. Nomeie sua rede virtual **VNET**e usar os valores padrão para as outras propriedades. Selecione **criar**.

      ![definir um alerta](./media/resource-manager-export-template/create-vnet.png)

3. Após a rede virtual tem implantado com êxito ao seu grupo de recursos, examine novamente o histórico de implantação. Agora, você ver duas implantações. Se você não vir a segunda implantação, talvez seja necessário fechar o blade de grupo de recursos e reabri-lo. Selecione a implantação mais recente.

      ![histórico de implantação](./media/resource-manager-export-template/deployment-history.png)

4. Exiba o modelo para essa implantação. Observe que ele define somente a rede virtual. Ele não inclui a conta de armazenamento que você implantado anteriormente. Você não tem mais um modelo que representa todos os recursos no seu grupo de recursos.

## <a name="export-the-template-from-resource-group"></a>Exportar o modelo de grupo de recursos

Para obter o estado atual do seu grupo de recursos, exporte um modelo que mostra um instantâneo do grupo de recursos.  

> [AZURE.NOTE] Você não pode exportar um modelo para um grupo de recursos que tem mais de 200 recursos.

1. Para exibir o modelo para um grupo de recursos, selecione **script de automação**.

      ![grupo de recursos de exportação](./media/resource-manager-export-template/export-resource-group.png)

     Nem todos os tipos de recursos de suporte a função de modelo de exportação. Se seu grupo de recursos contém apenas a conta de armazenamento e a rede virtual mostrados neste artigo, você não verá um erro. No entanto, se você tiver criado outros tipos de recurso, você poderá ver uma mensagem de erro informando que não há um problema com a exportação. Saiba como lidar com esses problemas na seção [corrigir problemas de exportação](#fix-export-issues) .

      

2. Você ver novamente os seis arquivos que você pode usar para reimplantar a solução, mas desta vez o modelo é um pouco diferente. Este modelo tem apenas dois parâmetros: uma para o nome da conta de armazenamento e outra para o nome de rede virtual.

        "parameters": {
          "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
          },
          "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
          }
        },

     Gerenciador de recursos não recuperar os modelos que você usou durante a implantação. Em vez disso, ele gerado um novo modelo com base na configuração atual dos recursos. Por exemplo, o modelo define o valor de local e replicação de conta do armazenamento como:

        "location": "northeurope",
        "tags": {},
        "properties": {
            "accountType": "Standard_RAGRS"
        },

3. Você tem algumas opções para continuar a trabalhar com este modelo. Você pode baixar o modelo e trabalhar nele localmente com um editor de JSON. Ou, você pode salvar o modelo para sua biblioteca e trabalhar nela por meio do portal.

     Se você estiver confortável usando um editor de JSON como [VS código](resource-manager-vs-code.md) ou [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md), talvez você prefira baixando o modelo localmente e usando o editor. Se você não estiver configurado com um editor de JSON, talvez você prefira edição do modelo por meio do portal. O resto deste tópico supõe que você salvou o modelo à sua biblioteca no portal. No entanto, você fazer as mesmas alterações de sintaxe para o modelo se trabalhar localmente com um editor de JSON ou por meio do portal.

     Para trabalhar localmente, selecione **Download**.

      ![Baixar modelo](./media/resource-manager-export-template/download-template.png)

     Para trabalhar por meio do portal, selecione **Adicionar à biblioteca**.

      ![Adicionar à biblioteca](./media/resource-manager-export-template/add-to-library.png)

     Ao adicionar um modelo para a biblioteca, atribua ao modelo um nome e descrição. Em seguida, selecione **Salvar**.

     ![definir valores de modelo](./media/resource-manager-export-template/set-template-values.png)

4. Para exibir um modelo salvo em sua biblioteca, selecione **mais serviços**, digite **modelos** para filtrar os resultados, selecione **modelos**.

      ![localizar modelos](./media/resource-manager-export-template/find-templates.png)

5. Selecione o modelo com o nome que você salvou.

      ![Selecione modelo](./media/resource-manager-export-template/select-library-template.png)

## <a name="customize-the-template"></a>Personalizar o modelo

O modelo exportado funciona bem se você deseja criar a mesma conta de armazenamento e a rede virtual para cada implantação. No entanto, o gerente de recursos fornece opções para que você pode implantar modelos com muito mais flexibilidade. Por exemplo, durante a implantação, você talvez queira especificar o tipo de conta de armazenamento para criar ou os valores a serem usados para o prefixo de endereço de rede virtual e o prefixo de sub-rede.

Nesta seção, você adiciona parâmetros para o modelo exportado para que você pode reutilizar o modelo quando você implanta estes recursos para outros ambientes. Você também pode adicionar alguns recursos ao seu modelo para diminuir a probabilidade de encontrar um erro quando você implanta o modelo. Você não tem mais a descoberta um nome exclusivo para sua conta de armazenamento. Em vez disso, o modelo cria um nome exclusivo. Restringir os valores que podem ser especificados para o tipo de conta de armazenamento apenas com opções válidas.

1. Selecione **Editar** para personalizar o modelo.

     ![Mostrar modelo](./media/resource-manager-export-template/show-template.png)

1. Selecione o modelo.

     ![Editar modelo](./media/resource-manager-export-template/edit-template.png)

1. Para poder passar os valores que você talvez queira especificar durante a implantação, substitua a seção **parâmetros** novas definições de parâmetro. Observe que os valores de **allowedValues** para **storageAccount_accountType**. Se você acidentalmente fornecer um valor inválido, esse erro é reconhecido antes de inicia a implantação. Observe também que você estiver fornecendo somente um prefixo para o nome da conta de armazenamento e o prefixo está limitado aos 11 caracteres. Quando você limitar o prefixo de 11 caracteres, você garantir que o nome completo não exceda o número máximo de caracteres para uma conta de armazenamento. O prefixo permite que você aplique uma convenção de nomenclatura para suas contas de armazenamento. Você verá como criar um nome exclusivo na próxima etapa.

        "parameters": {
          "storageAccount_prefix": {
            "type": "string",
            "maxLength": 11
          },
          "storageAccount_accountType": {
            "defaultValue": "Standard_RAGRS",
            "type": "string",
            "allowedValues": [
              "Standard_LRS",
              "Standard_ZRS",
              "Standard_GRS",
              "Standard_RAGRS",
              "Premium_LRS"
            ]
          },
          "virtualNetwork_name": {
            "type": "string"
          },
          "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "string"
          },
          "subnetName": {
            "defaultValue": "subnet-1",
            "type": "string"
          },
          "subnetAddressPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "string"
          }
        },

2. A seção de **variáveis** do modelo está vazia. Na seção **variáveis** , você criar valores que simplificam a sintaxe para o restante do seu modelo. Substitua esta seção com uma nova definição de variável. A variável **storageAccount_name** concatena o prefixo do parâmetro a uma string exclusiva que é gerado com base no identificador do grupo de recursos. Você não tem mais a descoberta um nome exclusivo ao fornecer um valor de parâmetro.

        "variables": {
          "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
        },

3. Para usar os parâmetros e a variável nas definições de recurso, substitua a seção **recursos** novas definições de recurso. Observe que pouco mudou as definições de recurso diferente o valor que está atribuído para a propriedade de recurso. As propriedades são iguais às propriedades do modelo exportado. Você simplesmente está atribuindo propriedades para valores de parâmetro em vez de valores embutidos. O local dos recursos está definido para usar o mesmo local como o grupo de recursos através da expressão **resourceGroup () .location** . A variável que você criou para o nome da conta de armazenamento é referenciada através da expressão de **variáveis** .

        "resources": [
          {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetwork_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "properties": {
              "addressSpace": {
                "addressPrefixes": [
                  "[parameters('addressPrefix')]"
                ]
              },
              "subnets": [
                {
                  "name": "[parameters('subnetName')]",
                  "properties": {
                    "addressPrefix": "[parameters('subnetAddressPrefix')]"
                  }
                }
              ]
            },
            "dependsOn": []
          },
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccount_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "tags": {},
            "properties": {
                "accountType": "[parameters('storageAccount_accountType')]"
            },
            "dependsOn": []
          }
        ]

4. Selecione **Okey** quando terminar a edição do modelo.

5. Selecione **Salvar** para salvar as alterações para o modelo.

     ![Salvar modelo](./media/resource-manager-export-template/save-template.png)

6. Para implantar o modelo atualizado, selecione **implantar**.

     ![implantar o modelo](./media/resource-manager-export-template/deploy-template.png)

7. Forneça os valores de parâmetro e selecione um novo grupo de recursos para implantar os recursos para.

## <a name="update-the-downloaded-parameters-file"></a>Atualizar o arquivo baixado parâmetros

Se você estiver trabalhando com os arquivos baixados (em vez de biblioteca do portal), você precisa atualizar o arquivo baixado parâmetro. Ele não coincida mais os parâmetros no seu modelo. Você não precisa usar um arquivo de parâmetro, mas ele pode simplificar o processo quando você reimplantar um ambiente. Use os valores padrão que são definidos no modelo de muitos dos parâmetros para que seu arquivo de parâmetro precisa apenas dois valores.

Substitua o conteúdo do arquivo parameters.json com:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

O arquivo de parâmetro atualizado fornece valores somente para os parâmetros que não têm um valor padrão. Você pode fornecer valores para os outros parâmetros quando quiser que um valor que é diferente do valor padrão.

## <a name="fix-export-issues"></a>Corrigir problemas de exportação

Nem todos os tipos de recursos de suporte a função de modelo de exportação. Gerenciador de recursos especificamente não exportar alguns tipos de recursos para evitar expor dados confidenciais. Por exemplo, se você tiver uma cadeia de conexão no site config, provavelmente não desejar que ele explicitamente exibido em um modelo exportado. Você pode contornar esse problema adicionando manualmente os recursos que estão faltando novamente no seu modelo.

> [AZURE.NOTE] Somente você encontrar problemas de exportação ao exportar de um grupo de recursos, em vez do seu histórico de implantação. Se sua última implantação representa com precisão o estado atual do grupo de recursos, você deve exportar o modelo do histórico de implantação, em vez do grupo de recursos. Exporte somente de um grupo de recursos quando você tiver feito alterações ao grupo de recursos que não são definidos em um único modelo.

Por exemplo, se você exportar um modelo para um grupo de recursos que contém um aplicativo web, o banco de dados SQL e a cadeia de conexão no site config, você vê a seguinte mensagem:

![Mostrar erro](./media/resource-manager-export-template/show-error.png)

Selecionando a mensagem mostra exatamente quais tipos de recursos não foram exportados. 
     
![Mostrar erro](./media/resource-manager-export-template/show-error-details.png)

Este tópico mostra correções comuns.

### <a name="connection-string"></a>Cadeia de Conexão

No recurso de sites da web, adicione uma definição para a cadeia de conexão ao banco de dados:

```
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "connectionstrings",
      "dependsOn": [
          "[concat('Microsoft.Web/Sites/', parameters('<site-name>'))]"
      ],
      "properties": {
          "DefaultConnection": {
            "value": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('<database-server-name>'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('<database-name>'), ';User Id=', parameters('<admin-login>'), '@', parameters('<database-server-name>'), ';Password=', parameters('<admin-password>'), ';')]",
              "type": "SQLServer"
          }
      }
    }
  ]
}
```    

### <a name="web-site-extension"></a>Extensão de site da Web

No recurso de site da web, adicione uma definição para o código instalar:

```
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "name": "MSDeploy",
      "type": "extensions",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('<site-name>'))]"
      ],
      "properties": {
        "packageUri": "[concat(parameters('<artifacts-location>'), '/', parameters('<package-folder>'), '/', parameters('<package-file-name>'), parameters('<sas-token>'))]",
        "dbType": "None",
        "connectionString": "",
        "setParameters": {
          "IIS Web Application Name": "[parameters('<site-name>')]"
        }
      }
    }
  ]
}
```

### <a name="virtual-machine-extension"></a>Extensão de máquina virtual

Para obter exemplos de extensões de máquina virtual, consulte [Exemplos de configuração de extensão de máquina virtual do Azure Windows](./virtual-machines/virtual-machines-windows-extensions-configuration-samples.md).

### <a name="virtual-network-gateway"></a>Gateway de rede virtual

Adicione um tipo de recurso do gateway de rede virtual.

```
{
  "type": "Microsoft.Network/virtualNetworkGateways",
  "name": "[parameters('<gateway-name>')]",
  "apiVersion": "2015-06-15",
  "location": "[resourceGroup().location]",
  "properties": {
    "gatewayType": "[parameters('<gateway-type>')]",
    "ipConfigurations": [
      {
        "name": "default",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('<vnet-name>'), parameters('<new-subnet-name>'))]"
          },
          "publicIpAddress": {
            "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('<new-public-ip-address-Name>'))]"
          }
        }
      }
    ],
    "enableBgp": false,
    "vpnType": "[parameters('<vpn-type>')]"
  },
  "dependsOn": [
    "Microsoft.Network/virtualNetworks/codegroup4/subnets/GatewaySubnet",
    "[concat('Microsoft.Network/publicIPAddresses/', parameters('<new-public-ip-address-Name>'))]"
  ]
},
```

### <a name="local-network-gateway"></a>Gateway de rede local

Adicione um tipo de recurso de gateway da rede local.

```
{
    "type": "Microsoft.Network/localNetworkGateways",
    "name": "[parameters('<local-network-gateway-name>')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "localNetworkAddressSpace": {
        "addressPrefixes": "[parameters('<address-prefixes>')]"
      }
    }
}
```

### <a name="connection"></a>Conexão

Adicione um tipo de recurso de conexão.

```
{
    "apiVersion": "2015-06-15",
    "name": "[parameters('<connection-name>')]",
    "type": "Microsoft.Network/connections",
    "location": "[resourceGroup().location]",
    "properties": {
        "virtualNetworkGateway1": {
        "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', parameters('<gateway-name>'))]"
      },
      "localNetworkGateway2": {
        "id": "[resourceId('Microsoft.Network/localNetworkGateways', parameters('<local-gateway-name>'))]"
      },
      "connectionType": "IPsec",
      "routingWeight": 10,
      "sharedKey": "[parameters('<shared-key>')]"
    }
},
```


## <a name="next-steps"></a>Próximas etapas

Parabéns! Você aprendeu como exportar um modelo de recursos que você criou no portal.

- Você pode implantar um modelo através do [PowerShell](resource-group-template-deploy.md), [Azure CLI](resource-group-template-deploy-cli.md)ou [API REST](resource-group-template-deploy-rest.md).
- Para ver como exportar um modelo através do PowerShell, consulte [Usando o PowerShell Azure com o Gerenciador de recursos do Azure](powershell-azure-resource-manager.md).
- Para ver como exportar um modelo através do Azure CLI, consulte [usar a CLI do Azure para Mac, Linux e Windows com o Gerenciador de recursos do Azure](xplat-cli-azure-resource-manager.md).
