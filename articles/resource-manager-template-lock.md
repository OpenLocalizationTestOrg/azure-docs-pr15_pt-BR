<properties
   pageTitle="Modelo do Gerenciador de recursos para bloqueios de recursos | Microsoft Azure"
   description="Mostra o esquema de Gerenciador de recursos para implantar bloqueios de recursos através de um modelo."
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
   ms.date="10/03/2016"
   ms.author="tomfitz"/>

# <a name="resource-locks-template-schema"></a>Esquema de modelo de bloqueios do recurso

Cria um bloqueio em um recurso e seu filho recursos.

## <a name="schema-format"></a>Formato de esquema

Para criar um bloqueio, adicione o seguinte esquema a seção de recursos do modelo.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## <a name="values"></a>Valores

As tabelas a seguir descrevem os valores que você precisa definir no esquema.

| Nome | Necessário | Descrição |
| ---- | -------- | ----------- |
| tipo | Sim | O tipo de recurso para criar.<br /><br />Para recursos:<br />**{namespace} / {digite} / provedores/bloqueios**<br /><br/>Grupos de recursos:<br />**Microsoft.Authorization/locks** |
| apiVersion | Sim | A versão da API utilizar para criar o recurso.<br /><br />Uso:<br />**2015-01-01**<br /><br /> |
| nome | Sim | Um valor que especifica o recurso Bloquear e um nome para o bloqueio. Podem ter até 64 caracteres e não pode conter % <>,, &,?, ou caracteres de controle.<br /><br />Para recursos:<br />**{resource}/Microsoft.Authorization/{lockname}**<br /><br />Grupos de recursos:<br />**{lockname}** |
| dependsOn | Não | Uma lista separada por vírgulas de um recurso nomes ou identificadores exclusivos do recurso.<br /><br />A coleção de recursos que esse bloqueio depende. Se o recurso que você está bloqueando for implantado no mesmo modelo, inclua esse nome de recurso nesse elemento para garantir que o recurso é implantado pela primeira vez. | 
| Propriedades | Sim | Um objeto que identifica o tipo de bloqueio e observações sobre o bloqueio.<br /><br />Consulte [objeto de propriedades](#properties-object). |  

### <a name="properties-object"></a>objeto de propriedades

| Nome | Necessário | Descrição |
| ---- | -------- | ----------- |
| nível   | Sim | O tipo de bloqueio para aplicar o escopo.<br /><br />**CannotDelete** - os usuários podem modificar recurso mas não excluí-la.<br />**Somente leitura** - os usuários podem ler a partir de um recurso, mas não é possível excluí-la ou executar quaisquer ações nela. |
| anotações   | Não | Descrição do bloqueio. Pode ter até 512 caracteres. |


## <a name="how-to-use-the-lock-resource"></a>Como usar o recurso de bloqueio

Esse recurso é adicionado ao seu modelo para impedir que as ações especificadas em um recurso. O bloqueio se aplica a todos os usuários e grupos.

Para criar ou excluir bloqueios de gerenciamento, você deve ter acesso a **Microsoft.Authorization/** * ou * *Microsoft.Authorization/locks/* ** ações. Das funções internas, apenas **proprietário** e **usuário acesso Administrador * * recebem essas ações. Para obter informações sobre controle de acesso baseado em função, consulte [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md).

O bloqueio é aplicado ao recurso especificado e quaisquer recursos filho.

Você pode remover um bloqueio com o comando do PowerShell **Remover AzureRmResourceLock** ou com a [operação de exclusão](https://msdn.microsoft.com/library/azure/mt204562.aspx) da API REST.

## <a name="examples"></a>Exemplos

O exemplo a seguir se aplica um bloqueio não é possível excluir a um aplicativo web.

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
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

O exemplo a seguir aplica um bloqueio não é possível excluir ao grupo de recursos.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre a estrutura do modelo, consulte [modelos de coautoria Gerenciador de recursos do Azure](resource-group-authoring-templates.md).
- Para obter mais informações sobre bloqueios, consulte [recursos de bloqueio com o Gerenciador de recursos do Azure](resource-group-lock-resources.md).
