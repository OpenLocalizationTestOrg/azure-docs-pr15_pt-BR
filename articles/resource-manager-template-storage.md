<properties
   pageTitle="Modelo do Gerenciador de recursos para armazenamento | Microsoft Azure"
   description="Mostra o esquema de Gerenciador de recursos para implantar contas de armazenamento por meio de um modelo."
   services="azure-resource-manager,storage"
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
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# <a name="storage-account-template-schema"></a>Esquema de modelo de conta de armazenamento

Cria uma conta de armazenamento.

## <a name="schema-format"></a>Formato de esquema

Para criar uma conta de armazenamento, adicione o seguinte esquema na seção de recursos do modelo.

    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": string,
        "location": string,
        "properties": 
        {
            "accountType": string
        }
    }

## <a name="values"></a>Valores

As tabelas a seguir descrevem os valores que você precisa definir no esquema.

| Nome | Valor |
| ---- | ---- |
| tipo | Enumeração<br />Necessário<br />**Microsoft.Storage/storageAccounts**<br /><br />O tipo de recurso para criar. |
| apiVersion | Enumeração<br />Necessário<br />**2015-06-15** ou **2015-05-01-preview**<br /><br />A versão da API utilizar para criar o recurso. | 
| nome | Cadeia de caracteres<br />Necessário<br />Entre 3 e 24 caracteres, apenas números e letras minúsculas.<br /><br />O nome da conta de armazenamento para criar. O nome deve ser exclusivo em todos do Azure. Considere a possibilidade de usar a função de [uniqueString](resource-group-template-functions.md#uniquestring) com a convenção de nomenclatura, conforme mostrado no exemplo abaixo. |
| local | Cadeia de caracteres<br />Necessário<br />Uma região que ofereça suporte a contas de armazenamento. Para determinar as regiões válidas, veja [compatíveis regiões](resource-manager-supported-services.md#supported-regions).<br /><br />A região para hospedar a conta de armazenamento. |
| Propriedades | Objeto<br />Necessário<br />[objeto de propriedades](#properties)<br /><br />Um objeto que especifica o tipo de conta de armazenamento para criar. |

<a id="properties" />
### <a name="properties-object"></a>objeto de propriedades

| Nome | Valor |
| ---- | ---- | 
| accountType | Cadeia de caracteres<br />Necessário<br />**Standard_LRS**, **Standard_ZRS**, **Standard_GRS**, **Standard_RAGRS**ou **Premium_LRS**<br /><br />O tipo de conta de armazenamento. Os valores permitidos correspondem às padrão localmente redundantes, redundantes de zona padrão, redundantes de localização geográfica padrão, localização geográfica-redundantes padrão de acesso de leitura e Premium localmente redundantes. Para obter informações sobre esses tipos de conta, consulte [replicação de armazenamento do Azure](./storage/storage-redundancy.md ). |

    
## <a name="examples"></a>Exemplos

O exemplo a seguir implanta uma conta de armazenamento padrão localmente redundantes com um nome exclusivo com base na id do grupo de recursos.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2015-06-15",
                "name": "[concat('storage', uniqueString(resourceGroup().id))]",
                "location": "[resourceGroup().location]",
                "properties": 
            {
                    "accountType": "Standard_LRS"
            }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>Modelos de início rápido

Há muitos modelos de início rápido que incluem uma conta de armazenamento. Os modelos a seguir ilustram alguns cenários comuns:

- [Criar uma conta de armazenamento padrão](https://azure.microsoft.com/documentation/templates/101-storage-account-create)
- [Implantação simples de uma máquina virtual Windows](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows)
- [Implantação simples de uma VM Linux](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux)
- [Criar um perfil de CDN, um ponto de extremidade de CDN com uma conta de armazenamento como origem](https://azure.microsoft.com/documentation/templates/201-cdn-with-storage-account)
- [Criar um Farm do SharePoint Availabilty alta com 9 VMs usando a extensão de DSC Powershell](https://azure.microsoft.com/documentation/templates/sharepoint-server-farm-ha)
- [Implantação simples de um 5 nó seguro Cluster de estrutura de serviço com WAD habilitado](https://azure.microsoft.com/documentation/templates/service-fabric-secure-cluster-5-node-1-nodetype-wad)
- [Criar uma máquina Virtual a partir de uma imagem do Windows com 4 discos de dados vazia](https://azure.microsoft.com/documentation/templates/101-vm-multiple-data-disk)


## <a name="next-steps"></a>Próximas etapas

- Para obter informações gerais sobre o armazenamento, consulte [Introdução ao armazenamento Microsoft Azure](./storage/storage-introduction.md).
- Por exemplo, modelos que usa uma nova conta de armazenamento com uma máquina Virtual, consulte [implantar uma simples máquina virtual Linux](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) ou [implantar uma simples máquina de virtual do Windows](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).
