<properties 
    pageTitle="Vinculação de recursos no Gerenciador de recursos do Azure | Microsoft Azure" 
    description="Crie um vínculo entre recursos relacionados em grupos de recursos diferentes no Gerenciador de recursos do Azure." 
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
    ms.topic="article" 
    ms.date="08/01/2016" 
    ms.author="tomfitz"/>

# <a name="linking-resources-in-azure-resource-manager"></a>Vinculação de recursos no Gerenciador de recursos do Azure

Durante a implantação, você pode marcar um recurso como dependente outro recurso, mas esse ciclo de vida termina em implantação. Após a implantação, não há nenhuma relação identificada entre recursos dependentes. Resource Manager fornece um recurso chamado recurso vinculação para estabelecer relações persistentes entre recursos.

Com o recurso de vinculação, você pode documentar relações que se estendem por grupos de recursos. Por exemplo, é comum para ter um banco de dados com o seu próprio ciclo de vida reside em um grupo de recursos e um aplicativo com um ciclo de vida diferente residem em um grupo de recurso diferente. O aplicativo se conecta ao banco de dados para que você deseja marcar um vínculo entre o aplicativo e o banco de dados. 

Todos os recursos vinculados devem pertencer a mesma assinatura. Cada recurso pode ser vinculado a 50 outros recursos. A única maneira de recursos relacionados de consulta é por meio da API REST. Se qualquer um dos recursos vinculados são excluído ou movido, o proprietário do link deve limpar o link restante. Você **não** avisado quando a exclusão de um recurso que está vinculado a outros recursos.

## <a name="linking-in-templates"></a>Vinculação em modelos

Para definir um link em um modelo, você pode incluir um tipo de recurso que combina o namespace do provedor de recursos e tipo do recurso de origem com **/providers/links**. O nome deve incluir o nome do recurso fonte. Você fornecer a identificação do recurso do recurso de destino. O exemplo a seguir estabelece um vínculo entre um site e uma conta de armazenamento.

    {
      "type": "Microsoft.Web/sites/providers/links",
      "apiVersion": "2015-01-01",
      "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
      "dependsOn": [ "[variables('siteName')]" ],
      "properties": {
        "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
        "notes": "This web site uses the storage account to store user information."
      }
    }


Para obter uma descrição completa do formato de modelo, consulte [recurso links - esquema de modelo](resource-manager-template-links.md).

## <a name="linking-with-rest-api"></a>Vinculação com a API REST

Para definir um vínculo entre recursos implantados, execute:

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

Substitua {id da assinatura} com sua id de assinatura. Substitua {-grupo de recursos}, {namespace do provedor, {-tipo de recurso} e {nome de recurso} com os valores que identificam o primeiro recurso no link. Substitua {nome do link} com o nome do link criar. Uso de 2015-01-01 para a versão da api.

Na solicitação, incluem um objeto que define o segundo recurso no link:

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

O elemento de propriedades contém o identificador para o recurso de segundo.

Você pode consultar links em sua assinatura com:

    https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Resources/links?api-version={api-version}

Para obter mais exemplos, incluindo como recuperar informações sobre links, consulte [Recursos vinculados](https://msdn.microsoft.com/library/azure/mt238499.aspx).

## <a name="next-steps"></a>Próximas etapas

- Você também pode organizar seus recursos com marcas. Para saber mais sobre os recursos de marcação, consulte [usando marcas para organizar seus recursos](resource-group-using-tags.md).
- Para obter uma descrição de como criar modelos e definir os recursos para ser implantado, consulte [modelos de criação de páginas](resource-group-authoring-templates.md).
