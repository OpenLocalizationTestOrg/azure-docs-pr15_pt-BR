<properties
   pageTitle="Visão geral de provedor de recursos de rede | Microsoft Azure"
   description="Saiba mais sobre o novo provedor de recursos de rede no Gerenciador de recursos do Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="network-resource-provider"></a>Provedor de recursos de rede
Uma necessidade de base no sucesso de negócios de hoje, é a capacidade de criar e gerenciar aplicativos de reconhecimento de rede de grande escala de maneira ágil, flexível, segura e repetitiva. Azure Resource Manager (ARM) permite que você crie aplicativos, como um único conjunto de recursos em grupos de recursos. Esses recursos são gerenciados por meio de vários provedores de recurso em ARM.

Gerenciador de recursos de Azure depende de provedores de recurso diferente para fornecer acesso aos seus recursos. Existem três provedores de recurso principal: rede, armazenamento e computação. Este documento discute as características e benefícios do provedor de recursos de rede, incluindo:

- **Metadados** – você pode adicionar informações aos recursos usando marcas. Essas marcas podem ser usadas para controlar a utilização de recursos em assinaturas e grupos de recursos.
- **Maior controle da sua rede** - rede recursos são flexíveis e você pode controlá-los de forma mais granular. Isso significa que você tem mais flexibilidade no gerenciamento os recursos de rede.
- **Configuração mais rápida** - porque recursos de rede são flexíveis, você pode criar e organizar os recursos de rede em paralelo. Isso tem drasticamente reduzido tempo de configuração.
- **Controle de acesso com base em função** - RBAC fornece funções padrão, com escopo de segurança específico, além de permitir a criação de funções personalizadas para o gerenciamento seguro.
- **Gerenciamento e implantação simplificados** - é mais fácil implantar e gerenciar aplicativos, desde que pode ser que você pode criar uma pilha de aplicativo inteiro como um único conjunto de recursos em um grupo de recursos. E rápidas de implantar, desde que você pode implantar fornecendo simplesmente uma carga JSON do modelo.
- **Personalização rápida** - você pode usar modelos de estilo declarativa para habilitar personalização rápida e repetitiva de implantações.
- **Personalização repetitiva** - você pode usar modelos de estilo declarativa para habilitar personalização rápida e repetitiva de implantações.
- **Interfaces de gerenciamento** - você pode usar qualquer uma das seguintes interfaces para gerenciar seus recursos:
    - API com base restante
    - PowerShell
    - .NET SDK
    - Node SDK
    - Java SDK
    - CLI Azure
    - Portal de visualização
    - Linguagem de modelo ARM

## <a name="network-resources"></a>Recursos de rede
Agora você pode gerenciar recursos de rede independentemente, em vez de ter todos eles gerenciado por meio de um recurso de computação único (uma máquina virtual). Isso garante maior grau de flexibilidade e agilidade em redigir uma infraestrutura de escala grandes e complexas em um grupo de recursos.

Um modo de exibição conceitual de uma implantação de amostra envolvendo um aplicativo de vários níveis é apresentado abaixo. Cada recurso que você vir, como NICs, endereços IP públicos e VMs, pode ser gerenciado independentemente.

![Modelo de recursos de rede](./media/resource-groups-networking/Figure2.png)

Cada recurso contém um conjunto comum de propriedades e defina a propriedade individual. As propriedades comuns estão:

|Propriedade|Descrição|Valores de exemplo|
|---|---|---|
|**nome**|Nome do recurso exclusivo. Cada tipo de recurso tem seus próprio restrições de nomenclatura.|PIP01, VM01, NIC01|
|**local**|Azure região no qual o recurso reside|westus, eastus|
|**ID**|Identificação exclusiva de URI com base|/Subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP|

Você pode verificar as propriedades individuais de recursos nas seções a seguir.

[AZURE.INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## <a name="management-interfaces"></a>Interfaces de gerenciamento
Você pode gerenciar seus recursos de rede Azure usando interfaces diferentes. Neste documento nos concentraremos na linha dessas interfaces: API REST e modelos.

### <a name="rest-api"></a>API REST
Conforme mencionado anteriormente, os recursos da rede podem ser gerenciados por meio de uma variedade de interfaces, inclusive API REST, .NET SDK, Node SDK, Java SDK, PowerShell, CLI, Portal Azure e modelos.

A API Rest em conformidade com a especificação de protocolo HTTP 1.1. A estrutura URI geral da API é apresentada a seguir:

    https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

E os parâmetros entre chaves representam os seguintes elementos:

- **id da assinatura** - sua id do Azure assinatura.
- **namespace do recurso-provedor** - namespace para o provedor sendo usado. O valor para o provedor de recursos de rede é *Microsoft.Network*.
- **região-nome** - o nome da região do Azure

Os seguintes métodos HTTP são suportados ao fazer chamadas a API REST:

- **Colocar** - usados para criar um recurso de um determinado tipo, modificar uma propriedade de recurso ou alterar uma associação entre recursos.
- **Obtenha** - usado para recuperar informações de um recurso provisionado.
- **Excluir** - usado para excluir um recurso existente.

A solicitação e a resposta em conformidade com um formato de carga JSON. Para obter mais detalhes, consulte [APIs de gerenciamento de recursos do Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx).

### <a name="arm-template-language"></a>Linguagem de modelo ARM
Além de gerenciamento de recursos imperativamente (via APIs ou SDK), você também pode usar um estilo de programação declarativo para criar e gerenciar recursos de rede usando a linguagem de modelo de ARM.

Uma representação de amostra de um modelo é fornecida abaixo –

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "<version-number-of-template>",
      "parameters": { <parameter-definitions-of-template> },
      "variables": { <variable-definitions-of-template> },
      "resources": [ { <definition-of-resource-to-deploy> } ],
      "outputs": { <output-of-template> }    
    }

O modelo é principalmente uma descrição de JSON os recursos e os valores de instância inseridos por meio de parâmetros. O exemplo a seguir pode ser utilizado para criar uma rede virtual com 2 sub-redes.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
          "location": {
            "type": "String",
            "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
            "metadata" : {
              "Description" : "Deployment location"
            }
          },
          "virtualNetworkName":{
            "type" : "string",
            "defaultValue":"myVNET",
            "metadata" : {
              "Description" : "VNET name"
            }
          },
          "addressPrefix":{
            "type" : "string",
            "defaultValue" : "10.0.0.0/16",
            "metadata" : {
              "Description" : "Address prefix"
            }

          },
          "subnet1Name": {
            "type" : "string",
            "defaultValue" : "Subnet-1",
            "metadata" : {
              "Description" : "Subnet 1 Name"
            }
          },
          "subnet2Name": {
            "type" : "string",
            "defaultValue" : "Subnet-2",
            "metadata" : {
              "Description" : "Subnet 2 name"
            }
          },
          "subnet1Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.0.0/24",
            "metadata" : {
              "Description" : "Subnet 1 Prefix"
            }
          },
          "subnet2Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.1.0/24",
            "metadata" : {
              "Description" : "Subnet 2 Prefix"
            }
          }
        },
        "resources": [
        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[parameters('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[parameters('subnet1Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet1Prefix')]"
                }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet2Prefix')]"
                }
              }
            ]
          }
        }
        ]
    }

Você tem a opção de fornecer os valores de parâmetro manualmente ao usar um modelo, ou você pode usar um arquivo de parâmetro. O exemplo a seguir mostra um conjunto possível de valores de parâmetro para ser usado com o modelo acima:

    {
      "location": {
          "value": "East US"
      },
      "virtualNetworkName": {
          "value": "VNET1"
      },
      "subnet1Name": {
          "value": "Subnet1"
      },
      "subnet2Name": {
          "value": "Subnet2"
      },
      "addressPrefix": {
          "value": "192.168.0.0/16"
      },
      "subnet1Prefix": {
          "value": "192.168.1.0/24"
      },
      "subnet2Prefix": {
          "value": "192.168.2.0/24"
      }
    }


Principais vantagens do uso de modelos são:

- Você pode criar uma infraestrutura complexa em um grupo de recursos em um estilo declarativo. A coordenação de criar os recursos, incluindo gerenciamento de dependência, é tratada pelo ARM.
- A infraestrutura pode ser criada de maneira repetida em várias regiões e em uma região alterando simplesmente parâmetros.
- O estilo declarativo leva a mais curto prazo de entrega na criação de modelos e implantar a infraestrutura.

Para modelos de exemplo, consulte [modelos de início rápido Azure](https://github.com/Azure/azure-quickstart-templates).

Para obter mais informações sobre a linguagem de modelo de ARM, consulte [Linguagem de modelo de Gerenciador de recursos do Azure](../resource-group-authoring-templates.md).

O modelo de exemplo acima usa a rede virtual e recursos de sub-rede. Há outros recursos de rede que você pode usar conforme listados abaixo:

### <a name="using-a-template"></a>Usando um modelo

Você pode implantar os serviços no Azure de um modelo usando o PowerShell, AzureCLI, ou realizando um clique para implantar do GitHub. Para implantar os serviços de um modelo no GitHub, execute as seguintes etapas:

1. Abra o arquivo template3 do GitHub. Como exemplo, abra [rede Virtual com duas sub-redes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Clique em **implantar no Azure**e, em seguida, entrar no portal do Azure com credenciais.
3. Verifique se o modelo e clique em **Salvar**.
4. Clique em **Editar parâmetros** e selecione um local, como *Oeste EUA*, para o vnet e sub-redes.
5. Se necessário, altere os parâmetros **ADDRESSPREFIX** e **SUBNETPREFIX** e clique em **Okey**.
6. Clique em **Selecionar um grupo de recursos** e clique no grupo de recursos que você deseja adicionar a vnet e sub-redes. Como alternativa, você pode criar um novo grupo de recursos clicando **ou criar novos**.
3. Clique em **criar**. Observe o bloco exibindo **implantação do modelo de provisionamento**. Depois de implantação, você verá uma tela semelhante a um abaixo.

![Implantação de modelo de amostra](./media/resource-groups-networking/Figure6.png)


## <a name="next-steps"></a>Próximas etapas

[Linguagem de modelo do Azure Gerenciador de recursos](../resource-group-authoring-templates.md)

[Rede Azure – usados em modelos](https://github.com/Azure/azure-quickstart-templates)

[Calcular o provedor de recursos](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)

[Visão geral do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md)
