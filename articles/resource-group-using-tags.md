<properties
    pageTitle="Usando marcas para organizar seus recursos Azure | Microsoft Azure"
    description="Mostra como aplicar marcas para organizar recursos para cobrança e gerenciar."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="AzurePortal"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2016"
    ms.author="tomfitz"/>


# <a name="using-tags-to-organize-your-azure-resources"></a>Usando marcas para organizar seus recursos Azure

Gerenciador de recursos permite organizar logicamente recursos, aplicando marcas. As marcas consistem em pares chave/valor que identificam recursos com propriedades que você definir. Para marcar recursos como pertencentes à mesma categoria, aplique a mesma marca a esses recursos.

Quando você vê recursos com uma marca específica, você ver os recursos de todos os seus grupos de recursos. Você não está limitado a somente os recursos no mesmo grupo de recursos, que permite que você organize seus recursos de uma maneira independente as relações de implantação. Marcas podem ser úteis quando você precisa organizar recursos para gerenciamento ou de cobrança.

Cada marca que você adiciona a um recurso ou grupo de recursos é automaticamente adicionada à taxonomia toda a assinatura. Você também pode preencher a taxonomia para sua assinatura com nomes de marca e os valores que você gostaria de usar como recursos estão marcados no futuro.

Cada recurso ou grupo de recursos pode ter no máximo 15 marcas. O nome da marca está limitado aos 512 caracteres e o valor de marca está limitado a 256 caracteres.

> [AZURE.NOTE] Você só pode aplicar marcas para os recursos que suportam operações do Gerenciador de recursos. Se você criou uma máquina Virtual, rede Virtual ou armazenamento através do modelo de implantação clássico (tais como por meio do portal clássico), você não pode aplicar uma marca a esse recurso. Para oferecer suporte a marcação, reimplante esses recursos por meio do Gerenciador de recursos. Todos os outros recursos de suporte de marcação.

## <a name="templates"></a>Modelos

Para marcar um recurso durante a implantação, simplesmente adicionar o elemento de **marcas** para o recurso que você está implantando e forneça o nome de marca e valor. O nome de marca e valor não precisa previamente existem na sua assinatura. Você pode fornecer até 15 marcas para cada recurso.

O exemplo a seguir mostra uma conta de armazenamento com uma marca.

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

Atualmente, Gerenciador de recursos não suporta um objeto para os nomes de marca e valores de processamento. Em vez disso, passar um objeto para os valores de marca, mas você ainda deve especificar os nomes de marca, conforme mostrado no exemplo a seguir.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "tagvalues": {
          "type": "object",
          "defaultValue": {
            "dept": "Finance",
            "project": "Test"
          }
        }
      },
      "resources": [
      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "examplestorage",
        "tags": {
          "dept": "[parameters('tagvalues').dept]",
          "project": "[parameters('tagvalues').project]"
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "Standard_LRS"
        }
      }]
    }


## <a name="portal"></a>Portal

[AZURE.INCLUDE [resource-manager-tag-resource](../includes/resource-manager-tag-resources.md)]

## <a name="powershell"></a>PowerShell

[AZURE.INCLUDE [resource-manager-tag-resources](../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli"></a>CLI Azure

[AZURE.INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="rest-api"></a>API REST

O portal e PowerShell ambos usam a [API do Gerenciador de recursos REST](https://msdn.microsoft.com/library/azure/dn848368.aspx) nos bastidores. Se você precisar integrar marcação em outro ambiente, você pode obter marcas com um GET na id do recurso e atualizar o conjunto de marcas com uma chamada de PATCH.


## <a name="tags-and-billing"></a>Marcas e cobrança

Para serviços com suporte, você pode usar marcas para agrupar os dados de cobranças. Por exemplo, [máquinas virtuais integrada com o Gerenciador de recursos do Azure](./virtual-machines/virtual-machines-windows-compare-deployment-models.md) permitem definir e aplicar marcas para organizar o uso de cobrança para máquinas virtuais. Se você estiver executando várias VMs para organizações diferentes, você pode usar as marcas para uso de grupo pelo Centro de custos.  
Você também pode usar marcas para categorizar custos por ambiente de tempo de execução; como o uso de cobrança para VMs em execução no ambiente de produção.

Você pode recuperar informações sobre marcas por meio do [uso de recursos do Azure e APIs do RateCard](billing-usage-rate-card-overview.md) ou do arquivo de valores separados por vírgula (CSV) de uso. Você baixa o arquivo de uso do [portal de contas Azure](https://account.windowsazure.com/) ou [portal EA](https://ea.azure.com). Para obter mais informações sobre programação de acesso a informações de cobrança, consulte [obter ideias para seu consumo de recursos do Microsoft Azure](billing-usage-rate-card-overview.md). Para operações de API REST, consulte [Referência da API REST Azure cobrança](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Quando você baixar o uso de CSV para serviços que oferecem suporte a marcas com cobranças, as marcas são exibidas na coluna de **marcas** . Para obter mais detalhes, consulte [compreender a sua fatura do Microsoft Azure](billing/billing-understand-your-bill.md).

![Ver as marcas no cobrança](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Próximas etapas

- Você pode aplicar restrições e convenções em sua assinatura com políticas personalizadas. A política que você definir pode exigir que todos os recursos tenham um valor para uma determinada marca. Para obter mais informações, consulte a [Política de uso para gerenciar recursos e controlar o acesso](resource-manager-policy.md).
- Para uma introdução ao uso do PowerShell do Azure ao implantar recursos, consulte [Usando o PowerShell Azure com o Gerenciador de recursos do Azure](./powershell-azure-resource-manager.md).
- Para obter uma introdução ao uso do Azure CLI ao implantar recursos, consulte [usando a CLI do Azure para Mac, Linux e Windows Azure no gerenciamento de recursos](./xplat-cli-azure-resource-manager.md).
- Para uma introdução ao usando o portal, consulte [usando o portal do Azure para gerenciar seus recursos Azure](./azure-portal/resource-group-portal.md)  
