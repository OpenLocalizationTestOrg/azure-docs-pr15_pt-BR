<properties 
   pageTitle="Configurações de runbook"
   description="Descreve as configurações para uma runbook no Azure automação e como alterá-los usando o Portal de gerenciamento do Azure e o Windows PowerShell."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="bwren" />

# <a name="runbook-settings"></a>Configurações de runbook

Cada runbook no Azure automação tem várias configurações que ajudá-lo para ser identificado e alterar seu comportamento de log. Cada uma dessas configurações é descrita abaixo seguido de procedimentos sobre como modificá-los.

## <a name="settings"></a>Configurações

### <a name="name-and-description"></a>Nome e descrição

Você não pode alterar o nome de um runbook depois que ela foi criada. A descrição é opcional e pode ter até 512 caracteres.

### <a name="tags"></a>Marcas

As marcas permitem que você atribuir distintas palavras e frases para ajudar a identificar um runbook. Por exemplo, quando você envia uma runbook na [Galeria de Runbook](https://msdn.microsoft.com/library/dn781422.aspx), você especificar determinadas marcas para identificar quais categorias runbook deve estar listada na. Você pode especificar várias marcas para um runbook separando-os com vírgulas.

### <a name="logging"></a>Registro em log

Por padrão, os registros de detalhado e o andamento não são gravados ao histórico de trabalho. Você pode alterar as configurações de um determinado runbook para efetuar esses registros. Para obter mais informações sobre esses registros, consulte [Runbook saída e mensagens](https://msdn.microsoft.com/library/dn879148.aspx).

## <a name="changing-runbook-settings"></a>Alterar as configurações de runbook

### <a name="changing-runbook-settings-with-the-azure-management-portal"></a>Alterar as configurações de runbook com o Portal de gerenciamento do Azure

Você pode alterar as configurações para uma runbook no Portal de gerenciamento do Azure da página para runbook **Configure** .

1. No Portal de gerenciamento do Azure, selecione **automação** e, em seguida, clique no nome de uma conta de automação.
1. Selecione a guia **Runbooks** .
1. Clique no nome de um runbook.
1. Selecione a guia **Configurar** .

### <a name="changing-runbook-settings-with-windows-powershell"></a>Alterar as configurações de runbook com o Windows PowerShell

Você pode usar o cmdlet [Set-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690275.aspx) para alterar as configurações para uma runbook. Se você quiser especificar várias marcas de formatação, você pode fornecer ou uma matriz ou uma única cadeia de caracteres com valores separado por vírgula para o parâmetro de marcas. Você pode acessar as marcas atuais com o [Get-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690278.aspx).

Os comandos de exemplo a seguir mostram como definir as propriedades para um runbook. Este exemplo adiciona três marcas para as marcas existentes e especifica que registros detalhados devem estar conectados.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="related-articles"></a>Artigos relacionados
- [Saída do runbook e mensagens](../automation-runbook-output-and-messages) 
- [Criar ou importar um Runbook](https://msdn.microsoft.com/library/dn643637.aspx) 