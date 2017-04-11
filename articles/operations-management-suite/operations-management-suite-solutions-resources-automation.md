<properties
   pageTitle="Recursos de automação em soluções OMS | Microsoft Azure"
   description="Soluções em OMS normalmente incluirá runbooks na automação do Azure para automatizar processos como coletar e processar dados de monitoramento.  Este artigo descreve como incluir runbooks e seus recursos relacionados em uma solução."
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
   ms.date="10/19/2016"
   ms.author="bwren" />

# <a name="automation-resources-in-oms-solutions-preview"></a>Recursos de automação em soluções OMS (prévia)

>[AZURE.NOTE]Esta é a documentação preliminar para a criação de soluções de gerenciamento no OMS que estão atualmente na visualização. Qualquer esquema descrita abaixo está sujeita a alterações.   

[Soluções de gerenciamento OMS](operations-management-suite-solutions.md) normalmente incluirá runbooks na automação do Azure para automatizar processos como coletar e processar dados de monitoramento.  Além de runbooks, contas de automação inclui ativos como variáveis e agendas que oferecem suporte a runbooks usados na solução.  Este artigo descreve como incluir runbooks e seus recursos relacionados em uma solução.

>[AZURE.NOTE]Os exemplos deste artigo usam parâmetros e variáveis que são necessárias ou comuns para soluções de gerenciamento e descrito em [soluções de gerenciamento de criação no pacote de gerenciamento de operações (OMS)](operations-management-suite-solutions-creating.md) 


## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já está familiarizado com a forma para [criar uma solução de gerenciamento](operations-management-suite-solutions-creating.md) e a estrutura de um arquivo de solução.

## <a name="samples"></a>Amostras
Você pode obter modelos do Gerenciador de recursos de exemplo para recursos de automação de [modelos de início rápido no GitHub](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates).

## <a name="automation-account"></a>Conta de automação
Todos os recursos no Azure automação estão contidos em uma [conta de automação](../automation/automation-security-overview.md#automation-account-overview).  Conforme descrito no [espaço de trabalho do OMS e a conta de automação](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account) a conta de automação não está incluída na solução de gerenciamento, mas deve existir antes da solução está instalada.  Se ele não estiver disponível, a instalação de solução falhará.

O nome da sua conta de automação está na nome de cada recurso de automação.  Isso é feito na solução com o parâmetro **accountName** como no seguinte exemplo de um recurso de runbook.
    
    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
Recursos de [automação do Azure runbook](../automation/automation-runbook-types.md) têm um tipo de **Microsoft.Automation/automationAccounts/runbooks** e as propriedades da tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| runbookType | Especifica os tipos de runbook. <br><br> Script - script do PowerShell <br>PowerShell - fluxo de trabalho do PowerShell <br> GraphPowerShell - runbook de script do PowerShell gráfica <br> GraphPowerShellWorkflow - runbook de fluxo de trabalho do PowerShell gráfica   |
| logProgress | Especifica se [os registros de andamento](../automation/automation-runbook-output-and-messages.md) devem ser gerados para runbook. |
| logVerbose  | Especifica se [registros detalhados](../automation/automation-runbook-output-and-messages.md) devem ser gerados para runbook. |
| Descrição | Descrição opcional para runbook. |
| publishContentLink | Especifica o conteúdo do runbook. <br><br>URI - Uri ao conteúdo do runbook.  Este será um arquivo. ps1 para runbooks PowerShell e Script e um arquivo exportado runbook gráficas para runbook um gráfico.  <br> versão - do runbook para seu próprio controle. |

Um exemplo de um recurso de runbook está abaixo.  Nesse caso, ele recupera runbook da [Galeria do PowerShell](https://www.powershellgallery.com).

    "name": "[concat(parameters('accountName'), '/Start-AzureV2VMs'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]"
    ],
    "tags": { },
    "properties": {
        "runbookType": "PowerShell",
        "logProgress": "true",
        "logVerbose": "true",
        "description": "",
        "publishContentLink": {
            "uri": "https://www.powershellgallery.com/api/v2/items/psscript/package/Update-ModulesInAutomationToLatestVersion/1.0/Start-AzureV2VMs.ps1",
            "version": "1.0"
        }
    }

### <a name="starting-a-runbook"></a>Iniciando um runbook
Há dois métodos para iniciar uma runbook em uma solução de gerenciamento.

- Para iniciar o runbook quando a solução estiver instalada, crie um [recurso de trabalho](#automation-jobs) conforme descrito abaixo.
- Para iniciar o runbook em um cronograma, crie um [recurso de cronograma](#schedules) conforme descrito abaixo. 


## <a name="automation-jobs"></a>Trabalhos de automação
Para iniciar uma runbook quando a solução de gerenciamento estiver instalada, você cria um recurso de **trabalho** .  Recursos de trabalho têm um tipo de **Microsoft.Automation/automationAccounts/jobs** e as propriedades da tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| runbook    | Único **nome** entidade com o nome do runbook.  |
| parâmetros | Entidade para cada parâmetro requerido pelo runbook. |


O trabalho inclui o nome de runbook e quaisquer valores de parâmetro sejam enviadas aos runbook.  O trabalho deve [dependem](operations-management-suite-solutions-creating.md#resources) runbook que ele está iniciando desde runbook deve ser criado antes do trabalho.  Você também pode criar dependências em outros trabalhos runbooks que devem ser concluídas antes do atual.

O nome de um recurso de trabalho deve conter um GUID que é geralmente atribuído por um parâmetro.  Você pode ler mais sobre os parâmetros GUID no [Criando soluções no pacote de gerenciamento de operações (OMS)](operations-management-suite-solutions-creating.md#parameters).  

A seguir é um exemplo de um recurso de trabalho que inicia uma runbook quando a solução de gerenciamento estiver instalada.  Um outros runbooks devem ser concluídos antes de um for iniciado, para que ele tenha dependências nos trabalhos para esse runbook.  Os detalhes do trabalho são fornecidos por meio de parâmetros e variáveis em vez de sendo especificada diretamente.

    {
        "name": "[concat(parameters('accountName'), '/', parameters('startVmsRunbookGuid'))]",
        "type": "Microsoft.Automation/automationAccounts/jobs",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "location": "[parameters('regionId')]",
        "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]",
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('initialPrepRunbookGuid'))]"
        ],
        "tags": { },
        "properties": {
            "runbook": {
                "name": "[variables('startVmsRunbookName')]"
            },
            "parameters": {
                "AzureConnectionAssetName": "[variables('AzureConnectionAssetName')]",
                "ResourceGroupName": "[variables('ResourceGroupName')]"
            }
        }
    }


## <a name="certificates"></a>Certificados
[Certificados de automação do Azure](../automation/automation-certificates.md) têm um tipo de **Microsoft.Automation/automationAccounts/certificates** e as propriedades da tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| base64Value   | Valor de base 64 do certificado. |
| impressão digital    | Impressão digital do certificado. |

Um exemplo de um recurso de certificado está abaixo.

    "name": "[concat(parameters('accountName'), '/MyCertificate')]",
    "type": "certificates",
    "apiVersion": "2015-01-01-preview",
    "location": "[parameters('regionId')]",
    "tags": {},
    "dependsOn": [
    ],
    "properties": {
        "base64Value": "MIIC1jCCAA8gAwIAVgIYJY4wXCXH/YAHMtALh7qEFzANAgkqhkiG5w0AAQUFGDAUMRIwEBYDVQQDEwlsA3NhAGevc3QqHhcNMTZwNjI5MHQxMjAsWhcNOjEwNjI5NKWwMDAwWjAURIwEAYDVQQBEwlsA2NhAGhvc3QwggEiMA0GCSqGSIA3DQEAAQUAA4IADwAwggEKAoIAAQDIyzv2A0RUg1/AAryI9W1DGAHAqqGdlFfTkUSDfv+hEZTAwKv0p8daqY6GroT8Du7ctQmrxJsy8JxIpDWxUaWwXtvv1kR9eG9Vs5dw8gqhjtOwgXvkOcFdKdQwA82PkcXoHlo+NlAiiPPgmHSELGvcL1uOgl3v+UFiiD1ro4qYqR0ITNhSlq5v2QJIPnka8FshFyPHhVtjtKfQkc9G/xDePW8dHwAhfi8VYRmVMmJAEOLCAJzRjnsgAfznP8CZ/QUczPF8LuTZ/WA/RaK1/Arj6VAo1VwHFY4AZXAolz7xs2sTuHplVO7FL8X58UvF7nlxq48W1Vu0l8oDi2HjvAgMAAAGjJDAiMAsGA1UdDwREAwIEsDATAgNVHSUEDDAKAggrAgEFNQcDATANAgkqhkiG9w0AAQUFAAOCAQEAk8ak2A5Ug4Iay2v0uXAk95qdAthJQN5qIVA13Qay8p4MG/S5+aXOVz4uMXGt18QjGds1A7Q8KDV4Slnwn95sVgA5EP7akvoGXhgAp8Dm90sac3+aSG4fo1V7Y/FYgAgpEy4C/5mKFD1ATeyyhy3PmF0+ZQRJ7aLDPAXioh98LrzMZr1ijzlAAKfJxzwZhpJamAwjZCYqiNZ54r4C4wA4QgX9sVfQKd5e/gQnUM8gTQIjQ8G2973jqxaVNw9lZnVKW3C8/QyLit20pNoqX2qQedwsqg3WCUcPRUUqZ4NpQeHL/AvKIrt158zAfU903yElAEm2Zr3oOUR4WfYQ==",
        "thumbprint": "F485CBE5569F7A5019CB68D7G6D987AC85124B4C"
    }

## <a name="credentials"></a>Credenciais
[Credenciais de automação do Azure](../automation/automation-credentials.md) têm um tipo de **Microsoft.Automation/automationAccounts/credentials** e as propriedades da tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| nome de usuário   | Nome de usuário para a credencial. |
| senha   | Senha da credencial. |

Um exemplo de um recurso de credencial está abaixo.

    "name": "[concat(parameters('accountName'), '/', variables('credentialName'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks/credentials",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "userName": "User01",
        "password": "password"
    }


## <a name="schedules"></a>Agendas
[Cronogramas de automação do Azure](../automation/automation-schedules.md) têm um tipo de **Microsoft.Automation/automationAccounts/schedules** e as propriedades da tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| Descrição | Descrição opcional para a agenda. |
| hora de início   | Especifica a hora de início de uma agenda como um objeto DateTime. Uma cadeia de caracteres pode ser fornecida se ele pode ser convertido em um DateTime válido. |
| isEnabled   | Especifica se o agendamento está habilitado. |
| intervalo    | O tipo de intervalo para o cronograma.<br><br>dia<br>hora |
| frequência   | Frequência com que o cronograma deve ser disparada em número de dias ou horas. |

Um exemplo de um recurso de cronograma está abaixo.

    "name": "[concat(parameters('accountName'), '/', variables('variableName'))]",
    "type": "microsoft.automation/automationAccounts/schedules",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Schedule for StartByResourceGroup runbook",
        "startTime": "10/30/2016 12:00:00",
        "isEnabled": "true",
        "interval": "day",
        "frequency": "1"
    }


## <a name="variables"></a>Variáveis
[Variáveis de automação do Azure](../automation/automation-variables.md) têm um tipo de **Microsoft.Automation/automationAccounts/variables** e as propriedades da tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| Descrição | Descrição opcional para a variável. |
| isEncrypted | Especifica se a variável deve ser criptografada. |
| tipo        | Tipo de dados para a variável. |
| valor       | Valor para a variável. |

Um exemplo de um recurso de variável está abaixo.

    "name": "[concat(parameters('accountName'), '/', variables('StartTargetResourceGroupsAssetName')) ]",
    "type": "microsoft.automation/automationAccounts/variables",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Description for the variable.",
        "isEncrypted": "true",
        "type": "string",
        "value": "'This is a string value.'"
    }


## <a name="modules"></a>Módulos
Sua solução de gerenciamento não precisa definir [módulos globais](../automation/automation-integration-modules.md) usados por seus runbooks porque eles sempre estarão disponíveis.  Você precisa incluir um recurso para qualquer outro módulo usado por seus runbooks e runbook deve depender do recurso de módulo para garantir que ele é criado antes runbook.

[Módulos de integração](../automation/automation-integration-modules.md) têm um tipo de **Microsoft.Automation/automationAccounts/modules** e as propriedades da tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| contentLink | Especifica o conteúdo do módulo. <br><br>URI - Uri ao conteúdo do runbook.  Este será um arquivo. ps1 para runbooks PowerShell e Script e um arquivo exportado runbook gráficas para runbook um gráfico.  <br> versão - do runbook para seu próprio controle. |

Um exemplo de um recurso de módulo está abaixo.

    {       
        "name": "[concat(parameters('accountName'), '/', variables('OMSIngestionModuleName'))]",
        "type": "Microsoft.Automation/automationAccounts/modules",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "properties": {
            "contentLink": {
                "uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
            }
        }
    }

### <a name="updating-modules"></a>Atualizando módulos
Se você atualizar uma solução de gerenciamento que inclui um runbook que usa um cronograma e a nova versão da sua solução tem um novo módulo usado por esse runbook, runbook pode usar a versão antiga do módulo.  Você deve incluir o seguinte runbooks em sua solução e criar um trabalho para executá-las antes de quaisquer outros runbooks.  Isso garante que todos os módulos são atualizados como necessário antes que o runbooks são carregados.

- [Atualização ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) garante que todos os módulos usados pelo runbooks em sua solução são a versão mais recente.  
- [Gerenciamento de MS ReRegisterAutomationSchedule](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) registrar novamente todos os recursos de programação para garantir que o runbooks vinculados a elas com a opção usar os mais recentes módulos.


A seguir é um exemplo dos elementos necessários de uma solução para dar suporte a atualização de módulo.
    
    "parameters": {
        "ModuleImportGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        },
        "ReRegisterRunbookGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        }
    },

    "variables": {
        "ModuleImportRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ModuleImportRunbookUri": "https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/Content/Update-ModulesInAutomationToLatestVersion.ps1",
        "ModuleImportRunbookDescription": "Imports modules and also updates all Azure dependent modules that are in the same Automation Account.",
        "ReRegisterSchedulesRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ReRegisterSchedulesRunbookUri": "https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/Content/ReRegisterAutomationSchedule-MS-Mgmt.ps1",
        "ReRegisterSchedulesRunbookDescription": "Reregisters schedules to ensure that they use latest modules."
    },

    "resources": [
        {
            "name": "[concat(parameters('accountName'), '/', variables('ModuleImportRunbookName'))]",
            "type": "Microsoft.Automation/automationAccounts/runbooks",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "dependsOn": [
            ],
            "location": "[parameters('regionId')]",
            "tags": { },
            "properties": {
                "runbookType": "PowerShell",
                "logProgress": "true",
                "logVerbose": "true",
                "description": "[variables('ModuleImportRunbookDescription')]",
                "publishContentLink": {
                    "uri": "[variables('ModuleImportRunbookUri')]",
                    "version": "1.0.0.0"
                }
            }
        },
        {
            "name": "[concat(parameters('accountName'), '/', parameters('ModuleImportGuid'))]",
            "type": "Microsoft.Automation/automationAccounts/jobs",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "location": "[parameters('regionId')]",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ModuleImportRunbookName'))]"
            ],
            "tags": { },
            "properties": {
                "runbook": {
                    "name": "[variables('ModuleImportRunbookName')]"
                },
                "parameters": {
                    "ResourceGroupName": "[resourceGroup().name]",
                    "AutomationAccountName": "[parameters('accountName')]",
                    "NewModuleName": "AzureRM.Insights"
                }
            }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('ReRegisterSchedulesRunbookName'))]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "PowerShell",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('ReRegisterSchedulesRunbookDescription')]",
            "publishContentLink": {
              "uri": "[variables('ReRegisterSchedulesRunbookUri')]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', parameters('reRegisterSchedulesGuid'))]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ReRegisterSchedulesRunbookName'))]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('ReRegisterSchedulesRunbookName')]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
        }
    ]


## <a name="next-steps"></a>Próximas etapas

- [Adicionar um modo de exibição à sua solução](operations-management-suite-solutions-resources-views.md) para visualizar os dados coletados.