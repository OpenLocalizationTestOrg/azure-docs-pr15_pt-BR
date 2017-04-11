<properties
   pageTitle="Modos de exibição em soluções de gerenciamento do pacote de gerenciamento de operações (OMS) | Microsoft Azure"
   description="Soluções de gerenciamento no pacote de gerenciamento de operações (OMS) normalmente incluirá um ou mais modos de exibição para visualizar dados.  Este artigo descreve como exportar um modo de exibição criado pelo Designer de modo de exibição e incluí-lo em uma solução de gerenciamento. "
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="bwren" />

# <a name="views-in-operations-management-suite-oms-management-solutions-preview"></a>Modos de exibição em soluções de gerenciamento do pacote de gerenciamento de operações (OMS) (prévia)

>[AZURE.NOTE]Esta é a documentação preliminar para a criação de soluções de gerenciamento no OMS que estão atualmente na visualização. Qualquer esquema descrita abaixo está sujeita a alterações.    

[Soluções de gerenciamento no pacote de gerenciamento de operações (OMS)](operations-management-suite-solutions.md) normalmente incluirá um ou mais modos de exibição para visualizar dados.  Este artigo descreve como exportar um modo de exibição criado pelo [Designer de modo de exibição](../log-analytics/log-analytics-view-designer.md) e incluí-lo em uma solução de gerenciamento.  

>[AZURE.NOTE]Os exemplos deste artigo usam parâmetros e variáveis que são necessárias ou comuns para soluções de gerenciamento e descrito em [soluções de gerenciamento de criação no pacote de gerenciamento de operações (OMS)](operations-management-suite-solutions-creating.md) 


## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já está familiarizado com a forma para [criar uma solução de gerenciamento](operations-management-suite-solutions-creating.md) e a estrutura de um arquivo de solução.


## <a name="overview"></a>Visão geral

Para incluir um modo de exibição em uma solução de gerenciamento, crie um **recurso** para ele no [arquivo de solução](operations-management-suite-solutions-creating.md).  O JSON que descreva a configuração detalhada do modo de exibição é geralmente complexo apesar e não algo que um autor de solução típica seria capaz de criar manualmente.  O método mais comum é criar o modo de exibição usando o [Designer de modo de exibição](../log-analytics/log-analytics-view-designer.md), exportá-lo e, em seguida, adicione sua configuração detalhada à solução. 

As etapas básicas para adicionar uma exibição para uma solução são da seguinte maneira.  Cada etapa é descrita mais detalhadamente nas seções a seguir.

1. Exporte o modo de exibição para um arquivo.
2. Crie o recurso de modo de exibição na solução.
3. Adicione os detalhes de exibição.

## <a name="export-the-view-to-a-file"></a>Exportar o modo de exibição para um arquivo
Siga as instruções no [Designer de modo de exibição de análise de Log](../log-analytics/log-analytics-view-designer.md) para exportar um modo de exibição para um arquivo.  O arquivo exportado será no formato JSON com os mesmos [elementos como o arquivo de solução](operations-management-suite-solutions-creating.md#management-solution-files).  

O elemento de **recursos** do arquivo exibição terá um recurso com um tipo de **Microsoft.OperationalInsights/workspaces** que representa o espaço de trabalho do OMS.  Este elemento terá um subelemento com um tipo de **modos de exibição** que representa o modo de exibição e contém sua configuração detalhada.  Você irá copiar os detalhes desse elemento e, em seguida, copiá-la para sua solução.


## <a name="create-the-view-resource-in-the-solution"></a>Criar o recurso de modo de exibição na solução
Adicione o seguinte recurso de modo de exibição para o elemento de **recursos** do seu arquivo de solução.  Isso usa variáveis descritos abaixo que você também deve adicionar.  Observe que as propriedades de **painel** e **OverviewTile** são espaços reservados que você substituirá com as propriedades correspondentes do arquivo exportado do modo de exibição.
 
    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile": 
        }
    }

Adicione as seguintes variáveis para o elemento de [variáveis](operations-management-suite-solutions-creating.md#variables) do arquivo de solução e substitua os valores de sua solução.

    "LogAnalyticsApiVersion": "2015-11-01-preview",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."


Observe que você pode copiar o recurso de modo de exibição inteiro do seu arquivo de modo de exibição exportado, mas você precisa fazer as seguintes alterações para funcionar em sua solução.  

- O **tipo** para o recurso de modo de exibição precisa ser alterado dos **modos de exibição** para **Microsoft.OperationalInsights/workspaces**.
- A propriedade **nome** para o recurso de modo de exibição precisa ser alterado para incluir o nome do espaço de trabalho.
- A dependência no espaço de trabalho deve ser removido desde que o recurso de espaço de trabalho não está definido na solução.
- Propriedade **DisplayName** precisa ser adicionado ao modo de exibição.  O **Id**, **nome**e **DisplayName** devem ser compatíveis.
- Nomes de parâmetro devem ser alterados para corresponder o conjunto necessário de parâmetros.
- Variáveis devem ser definidos na solução e usados nas propriedades adequadas.

## <a name="add-the-view-details"></a>Adicione os detalhes de exibição
O recurso de modo de exibição no arquivo exportado do modo de exibição conterá dois elementos no elemento **Propriedades** chamado **Dashboard** e **OverviewTile** que contêm a configuração detalhada do modo de exibição.  Copie esses dois elementos e seu conteúdo para o elemento de **Propriedades** do recurso de exibição no seu arquivo de solução. 

## <a name="example"></a>Exemplo
Por exemplo, o exemplo a seguir mostra um arquivo de solução simples com um modo de exibição.  Nas reticências (…) são exibidas para o conteúdo de **painel** e **OverviewTile** por razões de espaço.


    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
        ]
    }




## <a name="next-steps"></a>Próximas etapas

- Aprenda detalhes completos de criação de [soluções de gerenciamento](operations-management-suite-solutions-creating.md).
- Inclua [runbooks de automação em sua solução de gerenciamento](operations-management-suite-solutions-resources-automation.md).