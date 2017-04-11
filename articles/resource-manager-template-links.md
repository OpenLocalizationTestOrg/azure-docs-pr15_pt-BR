<properties
   pageTitle="Modelo do Gerenciador de recursos para recursos de vinculação | Microsoft Azure"
   description="Mostra o esquema de Gerenciador de recursos para implantar links entre recursos relacionados através de um modelo."
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
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# <a name="resource-links-template-schema"></a>Esquema de modelo de links do recurso

Cria um vínculo entre dois recursos. O link é aplicado a um recurso conhecido como o recurso de fonte. O segundo recurso no link é conhecido como o recurso de destino.

## <a name="schema-format"></a>Formato de esquema

Para criar um vínculo, adicione o seguinte esquema na seção de recursos do modelo.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## <a name="values"></a>Valores

As tabelas a seguir descrevem os valores que você precisa definir no esquema.

| Nome | Valor |
| ---- | ---- |
| tipo | Enumeração<br />Necessário<br />**{namespace} / {digite} / provedores/links**<br /><br />O tipo de recurso para criar. Namespace {} e {tipo} valores referem-se o tipo de recursos e de espaço para nome do provedor do recurso fonte. |
| apiVersion | Enumeração<br />Necessário<br />**2015-01-01**<br /><br />A versão da API utilizar para criar o recurso. |  
| nome | Cadeia de caracteres<br />Necessário<br />**{resouce}/Microsoft.Resources/{linkname}**<br /> até 64 caracteres e não pode conter % <>,, &,?, ou caracteres de controle.<br /><br />Um valor que especifica o nome do recurso de fonte tanto um nome para o link. |
| dependsOn | Matriz<br />Opcional<br />Uma lista separada por vírgulas de um recurso nomes ou identificadores exclusivos do recurso.<br /><br />A coleção de recursos que esse link depende. Se os recursos que você está vinculando são implantados no mesmo modelo, inclua os nomes de recurso nesse elemento para garantir que eles sejam implantados primeiro. | 
| Propriedades | Objeto<br />Necessário<br />[objeto de propriedades](#properties)<br /><br />Um objeto que identifica o recurso para vincular a e anotações sobre o link. |  

<a id="properties" />
### <a name="properties-object"></a>objeto de propriedades

| Nome | Valor |
| ------- | ---- |
| Alvo | Cadeia de caracteres<br />Necessário<br />**{identificação do recurso}**<br /><br />O identificador do recurso de destino para vincular a. |
| anotações | Cadeia de caracteres<br />Opcional<br />até 512 caracteres<br /><br />Descrição do bloqueio. |


## <a name="how-to-use-the-link-resource"></a>Como usar o recurso de link

Aplicar um vínculo entre dois recursos quando os recursos têm uma dependência que continua após a implantação. Por exemplo, um aplicativo pode se conectar a um banco de dados em um grupo de recurso diferente. Você pode definir essa dependência criando um link do aplicativo no banco de dados. Links permitem a relação entre dois recursos do documento. Posteriormente, você ou outra pessoa em sua organização pode consultar um recurso para obter links para descobrir como o recurso funciona com outros recursos.

Todos os recursos vinculados devem pertencer a mesma assinatura. Cada recurso pode ser vinculado a 50 outros recursos. Se qualquer um dos recursos vinculados são excluído ou movido, o proprietário do link deve limpar o link restante.

Para trabalhar com links restante, consulte [Recursos vinculados](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Use o seguinte comando do PowerShell do Azure para ver todos os links na sua assinatura. Você pode fornecer outros parâmetros para limitar os resultados.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

## <a name="examples"></a>Exemplos

O exemplo a seguir aplica um bloqueio de somente leitura para um aplicativo web.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
            },
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
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>Modelos de início rápido

Os seguintes modelos de início rápido implantar recursos com um link.

- [Alerta para fila com o aplicativo de lógica](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
- [Alerta a margem de atraso ao aplicativo de lógica](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
- [Provisionar um aplicativo de API com um gateway existente](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
- [Provisionar um aplicativo de API com um novo gateway](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
- [Criar um aplicativo de aplicativo de lógica mais API usando um modelo](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
- [Aplicativo de lógica que envia uma mensagem de texto quando um alerta é acionado](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)


## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre a estrutura do modelo, consulte [modelos de coautoria Gerenciador de recursos do Azure](resource-group-authoring-templates.md).
