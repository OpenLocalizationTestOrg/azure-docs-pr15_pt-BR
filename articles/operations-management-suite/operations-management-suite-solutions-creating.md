<properties
   pageTitle="Criação de soluções de gerenciamento no pacote de gerenciamento de operações (OMS) | Microsoft Azure"
   description="Soluções de gerenciamento de estendem a funcionalidade do pacote de gerenciamento de operações (OMS), fornecendo cenários de pacotes de gerenciamento que os clientes podem adicionar ao seu espaço de trabalho do OMS.  Este artigo fornece detalhes sobre como você pode criar soluções de gerenciamento a ser usado em seu próprio ambiente ou disponibilizado aos seus clientes."
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
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="creating-management-solutions-in-operations-management-suite-oms-preview"></a>Criação de soluções de gerenciamento no pacote de gerenciamento de operações (OMS) (prévia)

>[AZURE.NOTE]Esta é a documentação preliminar para a criação de soluções de gerenciamento no OMS que estão atualmente na visualização. Qualquer esquema descrita abaixo está sujeita a alterações.  

Soluções de gerenciamento de estendem a funcionalidade do pacote de gerenciamento de operações (OMS), fornecendo cenários de pacotes de gerenciamento que os clientes podem adicionar ao seu espaço de trabalho do OMS.  Este artigo fornece detalhes sobre como criar suas próprias soluções de gerenciamento que você pode usar em seu próprio ambiente ou disponibilizar para os clientes através da comunidade.

## <a name="planning-your-management-solution"></a>Planejamento de sua solução de gerenciamento
Soluções de gerenciamento OMS incluem vários recursos com suporte um cenário de gerenciamento particular.  Ao planejar sua solução, você deve focalizar o cenário que você está tentando alcançar e todos os recursos necessários para dar suporte a ele.  Cada solução deve estar auto contido e defina cada recurso que requer, mesmo se um ou mais recursos também são definidos por outras soluções.  Quando uma solução de gerenciamento estiver instalada, cada recurso é criado, a menos que já existe, e você pode definir o que acontece com recursos quando uma solução é removida.  

Por exemplo, uma solução de gerenciamento pode conter um [runbook de automação do Azure](../automation/automation-intro.md) que coleta de dados para o repositório de análise de Log usando um [cronograma](../automation/automation-schedules.md) e um [modo de exibição](../log-analytics/log-analytics-view-designer.md) que fornece várias visualizações dos dados coletados.  O mesmo agendamento pode ser usado por outra solução.  Como o autor de solução de gerenciamento, você poderia definir todos os três recursos mas especificar que o runbook e exibir devem ser automaticamente removidos quando a solução é removida.    Você também poderia definir o cronograma mas especificar que deve permanecer no local se a solução foram removida caso ele ainda estava em uso por outra solução.

## <a name="management-solution-files"></a>Arquivos de solução de gerenciamento
Soluções de gerenciamento são implementadas como [modelos de gerenciamento de recursos](../resource-manager-template-walkthrough.md).  A tarefa principal em aprender como soluções de gerenciamento do autor está aprendendo como [autor de um modelo](../resource-group-authoring-templates.md).  Este artigo fornece detalhes exclusivos de modelos usados para soluções e como definir recursos de solução típica.

A estrutura básica de um arquivo de solução de gerenciamento é o mesmo como um [Modelo do Gerenciador de recursos](resource-group-authoring-templates.md#template-format) que é da seguinte maneira.  Cada uma das seções a seguir descreve os elementos de nível superior e e seu conteúdo em uma solução.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parâmetros

[Parâmetros](../resource-group-authoring-templates.md#parameters) são valores que você precisa do usuário ao instalar a solução de gerenciamento.  Há parâmetros padrão que todas as soluções terá e você pode adicionar parâmetros adicionais conforme necessário para sua solução específica.  Como os usuários fornecerá os valores de parâmetro ao instalar sua solução dependerá o parâmetro específico e como a solução está sendo instalada.


Quando um usuário instala sua solução de gerenciamento por meio do [Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-solutions) ou [modelos de início rápido do Azure](operations-management-suite-solutions.md#finding-and-installing-solutions) que ele será solicitado a selecionar um [espaço de trabalho do OMS e conta de automação](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account).  Eles são usados para preencher os valores de cada um dos parâmetros padrão.  O usuário não será solicitado a fornecer diretamente valores para os parâmetros padrão, mas ele será solicitado a fornecer valores para quaisquer parâmetros adicionais.

Quando o usuário instala sua solução [outro método](operations-management-suite-solutions.md#finding-and-installing-solutions), ele devem fornecer um valor para todos os parâmetros padrão e todos os parâmetros adicionais.

Um parâmetro de amostra é mostrado abaixo.

    "Daily Start Time": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

A tabela a seguir descreve os atributos de um parâmetro.

| Atributo | Descrição |
|:--|:--|
| tipo        | Tipo de dados para o parâmetro. O controle de entrada exibido para o usuário depende do tipo de dados.<br><br>bool - caixa suspensa<br>cadeia de caracteres - caixa de texto<br>int - caixa de texto<br>SecureString - campo de senha<br> |
| categoria    | Categoria opcional para o parâmetro.  Parâmetros na mesma categoria são agrupados. |
| controle     | Funcionalidade adicional para parâmetros de cadeia de caracteres.<br><br>DateTime - controle de Datetime é exibida.<br>GUID - valor Guid é gerado automaticamente e o parâmetro não é exibido. |
| Descrição | Descrição opcional para o parâmetro.  Exibido em um balão de informações ao lado de parâmetro. |


### <a name="standard-parameters"></a>Parâmetros padrão
A tabela a seguir lista os parâmetros padrão para todas as soluções de gerenciamento.  Esses valores são preenchidos para o usuário em vez de solicitando-los quando sua solução é instalada a partir do Azure Marketplace ou modelos de início rápido.  O usuário deve fornecer valores para que elas se a solução estiver instalada com outro método.

>[AZURE.NOTE]A interface do usuário no Azure Marketplace e modelos de início rápido está esperando os nomes de parâmetro na tabela.  Se você usar nomes de parâmetros diferentes, em seguida, o usuário será solicitado a fornecê-las e elas não serão automaticamente preenchidas.


| Parâmetro | Tipo | Descrição |
|:--|:--|:--|
| accountName       | cadeia de caracteres |  Nome da conta de automação Azure. |
| pricingTier       | cadeia de caracteres | Preços camada de espaço de trabalho de análise de Log e conta de automação do Azure. |
| regionId          | cadeia de caracteres | Região da conta de automação do Azure. |
| nomedasolucao      | cadeia de caracteres | Nome da solução. |
| Nome     | cadeia de caracteres | Nome do espaço de trabalho de análise de log. |
| workspaceRegionId | cadeia de caracteres | Região do espaço de trabalho de análise de Log. |





### <a name="sample"></a>Exemplo
A seguir é uma entidade de parâmetro de amostra para uma solução.  Isso inclui todos os parâmetros padrão e dois parâmetros adicionais na mesma categoria.

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "A valid Azure Automation account name"
            }
        },
        "workspaceRegionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        },
        "jobIdGuid": {
        "type": "string",
            "metadata": {
                "description": "GUID for a runbook job",
                "control": "guid",
                "category": "Schedule"
            }
        },
        "startTime": {
            "type": "string",
            "metadata": {
                "description": "Time for starting the runbook.",
                "control": "datetime",
                "category": "Schedule"
            }
        }


Você pode fazer referência a valores de parâmetro em outros elementos da solução com a sintaxe de **parâmetros ('nome do parâmetro')**.  Por exemplo, para acessar o nome do espaço de trabalho, você usaria **parameters('workspaceName')** 

## <a name="variables"></a>Variáveis

O elemento de **variáveis** inclui os valores que você usará o restante da solução de gerenciamento.  Esses valores não são expostos para o usuário instalar a solução.  Eles destinam-se para fornecer o autor com um único local onde eles podem gerenciar os valores que podem ser usados várias vezes em toda a solução. Você deve colocar quaisquer valores específicos à sua solução em variáveis em vez de codificá-los no elemento de **recursos** .  Isso torna o código mais legível e permite que você altere facilmente esses valores em versões posteriores.

A seguir é um exemplo de um elemento de **variáveis** com parâmetros típicos usados em soluções.

    "variables": { 
        "SolutionVersion": "1.1", 
        "SolutionPublisher": "Contoso", 
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Consulte valores de variáveis por meio da solução com as de sintaxe **variáveis ('nome da variável')**.  Por exemplo, para acessar a variável nomedasolucao, você usaria **variables('solutionName')** 


## <a name="resources"></a>Recursos

O elemento de **recursos** define os diferentes incluídos em sua solução de gerenciamento de recursos.  Esta será a parte maior e mais complexa do modelo.  Recursos são definidos com a seguinte estrutura.  

    "resources": [
        {
            "name": "<name-of-the-resource>",           
            "apiVersion": "<api-version-of-resource>",
            "type": "<resource-provider-namespace/resource-type-name>",     
            "location": "<location-of-resource>",
            "tags": "<name-value-pairs-for-resource-tagging>",
            "comments": "<your-reference-notes>",
            "dependsOn": [
                "<array-of-related-resource-names>"
            ],
            "properties": "<unique-settings-for-the-resource>",
            "resources": [
                "<array-of-child-resources>"
            ]
        }
    ]

### <a name="dependencies"></a>Dependências
Os elementos de **dependsOn** Especifica uma [dependência](../resource-group-define-dependencies.md) outro recurso.  Quando a solução estiver instalada, um recurso não é criado até que todas as suas dependências foram criadas.  Por exemplo, a sua solução pode [Iniciar uma runbook](operations-management-suite-solutions-resources-automation.md#runbooks) quando ele é instalado usando um [recurso de trabalho](operations-management-suite-solutions-resources-automation.md#automation-jobs).  O recurso de trabalho seria dependente do recurso de runbook para certificar-se de que o runbook é criado antes do trabalho é criado.

### <a name="oms-workspace-and-automation-account"></a>Espaço de trabalho do OMS e conta de automação
As soluções de gerenciamento exigem um [espaço de trabalho OMS](../log-analytics/log-analytics-manage-access.md) contêm exibições e uma [conta de automação](../automation/automation-security-overview.md#automation-account-overview) para conter runbooks e recursos relacionados.  Eles devem ser disponíveis antes dos recursos na solução são criados e não devem ser definidos na solução em si.  O usuário será [especificar um espaço de trabalho e uma conta](operations-management-suite-solutions.md#oms-workspace-and-automation-account) quando eles implantarem sua solução, mas como o autor, você deve considerar os seguintes pontos.


## <a name="solution-resource"></a>Recurso de solução
Cada solução requer uma entrada de recurso no elemento de **recursos** que define a solução em si.  Isso terá um tipo de **Microsoft.OperationsManagement/solutions**.  A seguir é um exemplo de um recurso de solução.  Seus diferentes elementos são descritos nas seções a seguir.

    "name": "[concat(variables('SolutionName'), '[ ' ,parameters('workspacename'), ' ]')]",
    "location": "[parameters('workspaceRegionId')]",
    "tags": { },
    "type": "Microsoft.OperationsManagement/solutions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]",
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
    ]
    "properties": {
        "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
        "referencedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]"
        ],
        "containedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
        ]
    },
    "plan": {
        "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
        "Version": "[variables('SolutionVersion')]",
        "product": "AzureSQLAnalyticSolution",
        "publisher": "[variables('SolutionPublisher')]",
        "promotionCode": ""
    }

### <a name="solution-name"></a>Nome da solução
O nome da solução deve ser exclusivo em sua assinatura do Azure. O valor recomendado usar é o seguinte.  Isso utiliza uma variável chamada **nomedasolucao** para o nome de base e o parâmetro de **nome** para garantir que o nome seja exclusivo.

    [concat(variables('SolutionName'), ' [' ,parameters('workspaceName'), ']')]

Isso seria resolver para um nome semelhante ao seguinte.

    My Solution Name [MyWorkspace]
 

### <a name="dependencies"></a>Dependências
O recurso de solução deve ter uma [dependência](../resource-group-define-dependencies.md) em todos os outros recursos na solução, pois precisam existir antes da solução pode ser criada.  Você pode fazer isso adicionando uma entrada para cada recurso no elemento **dependsOn** .


### <a name="properties"></a>Propriedades
O recurso de solução tem as propriedades da tabela a seguir.  Isso inclui os recursos referenciados e contidos pela solução que define como o recurso é gerenciado após a solução está instalada.  Cada recurso na solução deve estar listado na propriedade **containedResources** ou o **referencedResources** .

| Propriedade | Descrição |
|:--|:--|
| workspaceResourceId | ID do espaço de trabalho OMS no formulário * <Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<nome do espaço de trabalho\>*. |
| referencedResources   | Lista de recursos na solução que não devem ser removidos quando a solução é removida. |
| containedResources   | Lista de recursos na solução que deve ser removido quando a solução é removida. |

O exemplo acima é para uma solução com um runbook, um cronograma e modo de exibição.  O agendamento e runbook são *referenciadas* no elemento **properties** para que eles não são removidos quando a solução é removida.  O modo de exibição é *contidos* para que ele é removido quando a solução é removida.


### <a name="plan"></a>Planejar
A entidade de **plano** do recurso solução possui as propriedades da tabela a seguir. 

| Propriedade | Descrição |
|:--|:--|
| nome          | Nome da solução. |
| Versão       | Versão da solução conforme determinado pelo autor. |
| produto       | Cadeia de caracteres exclusiva para identificar a solução. |
| Publisher     | Editor da solução. |


## <a name="other-resources"></a>Outros recursos
Você pode obter os detalhes e exemplos de recursos que são comuns para soluções de gerenciamento nos seguintes artigos.

- [Modos de exibição e painéis](operations-management-suite-solutions-resources-views.md)
- [Recursos de automação](operations-management-suite-solutions-resources-automation.md)

## <a name="testing-a-management-solution"></a>Teste de uma solução de gerenciamento
Antes de implantar sua solução de gerenciamento, é recomendável que você teste usando [AzureRmResourceGroupDeployment de teste](../resource-group-template-deploy.md#deploy-with-powershell).  Isto irá validar o arquivo de solução e identificar problemas antes de tentar implantá-lo.



## <a name="next-steps"></a>Próximas etapas

- Conheça os detalhes de [modelos de coautoria Gerenciador de recursos do Azure](../resource-group-authoring-templates.md).
- Pesquisar [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates) para amostras de modelos diferentes do Gerenciador de recursos.
- Exiba os detalhes para [Adicionar modos de exibição para uma solução de gerenciamento](operations-management-suite-solutions-resources-views.md).
- Exiba os detalhes para [Adicionar recursos de automação para uma solução de gerenciamento](operations-management-suite-solutions-resources-automation.md).
 