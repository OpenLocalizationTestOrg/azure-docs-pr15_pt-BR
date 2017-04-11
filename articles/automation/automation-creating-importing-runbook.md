<properties
    pageTitle="Criar ou importar um runbook na automação do Azure"
    description="Este artigo descreve como criar um novo runbook no Azure automação ou importá-lo de um arquivo."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/12/2016"
    ms.author="magoedte;bwren" />

# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Criar ou importar um runbook na automação do Azure

Você pode adicionar um runbook à automação do Azure, [Criando um novo](#creating-a-new-runbook) ou importando um runbook existente de um arquivo ou da [Galeria de Runbook](automation-runbook-gallery.md). Este artigo fornece informações sobre como criar e importar runbooks de um arquivo.  Você pode obter todos os detalhes sobre como acessar runbooks de comunidade e módulos no [módulo e Runbook galerias de automação do Azure](automation-runbook-gallery.md).

## <a name="creating-a-new-runbook"></a>Criando um novo runbook

Você pode criar um novo runbook no automação do Azure usando um dos portais Azure ou o Windows PowerShell. Após runbook tiver sido criado, você poderá editá-la usando informações [gráficas coautoria na automação do Azure](automation-graphical-authoring-intro.md)e de [Fluxo de trabalho de PowerShell de aprendizado](automation-powershell-workflow.md) .

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-classic-portal"></a>Para criar um novo runbook de automação do Azure com o portal do Azure clássico

Você só pode trabalhar com o [fluxo de trabalho do PowerShell runbooks](automation-runbook-types.md#powershell-workflow-runbooks) no portal do Azure.

1. No portal do Azure clássico, clique, **New**, **Serviços de aplicativo**, **automação**, **Runbook**, **Criar rápida**.
2. Insira as informações necessárias e clique em **criar**. O nome de runbook deve começar com uma letra e pode ter letras, números, sublinhados e traços.
3. Se você quiser editar runbook agora, clique em **Editar Runbook**. Caso contrário, clique em **Okey**.
4. Seu novo runbook aparecerão na guia **Runbooks** .


### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Para criar um novo runbook de automação do Azure com o portal do Azure

1. No portal do Azure, abra sua conta de automação.
2. Clique no bloco **Runbooks** para abrir a lista de runbooks.
3. Clique no botão **Adicionar um runbook** e, em seguida, **criar um novo runbook**.
2. Digite um **nome** para o runbook e selecione seu [tipo](automation-runbook-types.md). O nome de runbook deve começar com uma letra e pode ter letras, números, sublinhados e traços.
3. Clique em **criar** para criar runbook e abrir o editor.


### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Para criar um novo runbook de automação do Azure com o Windows PowerShell

Você pode usar o cmdlet [New-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619376.aspx) para criar um vazio [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Você pode especificar o parâmetro de **nome** para criar um runbook vazia que você pode editar mais tarde, ou você pode especificar o parâmetro de **caminho** para importar um arquivo de runbook. O parâmetro de **tipo** também deve ser incluído para especificar um dos quatro tipos de runbook.

Os comandos de exemplo a seguir mostram como criar um novo runbook vazia.

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Importando um runbook de um arquivo para a automação do Azure

Você pode criar um novo runbook no Azure automação importando um script PowerShell ou fluxo de trabalho do PowerShell (extensão. ps1) ou um runbook gráfica exportado (.graphrunbook).  Especifique o [tipo de runbook](automation-runbook-types.md) que será criado da importação levar em consideração as considerações a seguir.

- Um arquivo de .graphrunbook só pode ser importado em um novo [runbook gráfica](automation-runbook-types.md#graphical-runbooks)e runbooks gráficas só podem ser criadas de um arquivo de .graphrunbook.
- Um arquivo. ps1 contendo um fluxo de trabalho do PowerShell só pode ser importado em um [fluxo de trabalho do PowerShell runbook](automation-runbook-types.md#powershell-workflow-runbooks).  Se o arquivo contém vários fluxos de trabalho do PowerShell, a importação falhará. Você deve salvar cada fluxo de trabalho para seu próprio arquivo e importar cada separadamente.
- Um arquivo. ps1 que não contenha um fluxo de trabalho pode ser importado em um [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) ou um [fluxo de trabalho do PowerShell runbook](automation-runbook-types.md#powershell-workflow-runbooks).  Se ele é importado para um fluxo de trabalho do PowerShell runbook, em seguida, ele será convertido em um fluxo de trabalho e comentários serão incluídos no runbook especificando as alterações que foram feitas.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-classic-portal"></a>Importar um runbook de um arquivo com o portal do Azure clássico
Você pode usar o procedimento a seguir para importar um arquivo de script para automação do Azure.  Observe que você só pode importar um arquivo. ps1 para um runbook de fluxo de trabalho do PowerShell usando este portal.  Você deve usar o portal do Azure para outros tipos.

1. No portal de gerenciamento do Azure, selecione **automação** e, em seguida, selecione uma conta de automação.
2. Clique em **Importar**.
3. Clique em **Procurar arquivo** e localize o arquivo de script para importar.
4. Se você quiser editar runbook agora, clique em **Editar Runbook**. Caso contrário, clique em Okey.
5. Novo runbook aparecerão na guia **Runbooks** para a conta de automação.
6. Você deve [Publicar runbook](#publishing-a-runbook) para que possa executá-la.


### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Importar um runbook de um arquivo com o portal do Azure
Você pode usar o procedimento a seguir para importar um arquivo de script para automação do Azure.  

>[AZURE.NOTE] Observe que você só pode importar um arquivo. ps1 para um runbook de fluxo de trabalho do PowerShell no portal.

1. No portal do Azure, abra sua conta de automação.
2. Clique no bloco **Runbooks** para abrir a lista de runbooks.
3. Clique no botão **Adicionar um runbook** e, em seguida, **Importar**.
4. Clique em **arquivo de Runbook** para selecionar o arquivo para importar
2. Se o campo **nome** estiver habilitado, você tem a opção de alterá-lo.  O nome de runbook deve começar com uma letra e pode ter letras, números, sublinhados e traços.
3. O [tipo de runbook](automation-runbook-types.md) será automaticamente selecionada, mas você pode alterar o tipo após tomar as restrições aplicáveis para a conta. 
3. Novo runbook aparecerão na lista de runbooks para a conta de automação.
4. Você deve [Publicar runbook](#publishing-a-runbook) para que possa executá-la.

>[AZURE.NOTE] Depois de importar um runbook gráfica ou um gráfico runbook de fluxo de trabalho do PowerShell, você tem a opção Converter para outro tipo se quiser. Você não pode converter em textuais.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Importar um runbook de um arquivo de script com o Windows PowerShell

Você pode usar o cmdlet [AzureRMAutomationRunbook de importação](https://msdn.microsoft.com/library/mt603735.aspx) para importar um arquivo de script como um rascunho runbook de fluxo de trabalho do PowerShell. Se runbook já existir, a importação falhará a menos que você use o *-força* parâmetro. 

Os comandos de exemplo a seguir mostram como importar um arquivo de script para um runbook.

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## <a name="publishing-a-runbook"></a>Publicando um runbook

Quando você cria ou importar um novo runbook, você deve publicá-lo antes que você possa executá-lo.  Cada runbook na automação tem um rascunho e uma versão publicada. Somente a versão publicada está disponível para ser executado, e somente a versão de rascunho pode ser editada. A versão publicada não é afetada por quaisquer alterações para a versão de rascunho. Quando a versão de rascunho deve ser disponibilizada, em seguida, publicá-la que substitui a versão publicada com a versão de rascunho.

## <a name="to-publish-a-runbook-using-the-azure-classic-portal"></a>Publicar um runbook usando o portal de clássico do Azure

1. Abra o runbook no portal do Azure clássico.
1. Na parte superior da tela, clique em **autor**.
1. Na parte inferior da tela, clique em **Publicar** e clique em seguida **Sim** na mensagem de verificação.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Publicar um runbook usando o portal do Azure

1. Abra o runbook no portal do Azure.
1. Clique no botão **Editar** .
1. Clique no botão **Publicar** e, em seguida, **Sim** a mensagem de verificação.


## <a name="to-publish-a-runbook-using-windows-powershell"></a>Publicar um runbook usando o Windows PowerShell

Você pode usar o cmdlet [AzureRmAutomationRunbook publicar](https://msdn.microsoft.com/library/mt603705.aspx) para publicar um runbook com o Windows PowerShell. Os comandos de exemplo a seguir mostram como publicar um exemplo de runbook.

    $automationAccountName =  AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $RGName = "ResourceGroup"

    Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
    -Name $runbookName -ResourceGroupName $RGName


## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre como você pode se beneficiar a Runbook e a Galeria de módulo do PowerShell, consulte [galerias Runbook e módulo de automação do Azure](automation-runbook-gallery.md)
- Para saber mais sobre a edição runbooks PowerShell e ao fluxo de trabalho do PowerShell com um editor de texto, consulte [edição textual runbooks na automação do Azure](automation-edit-textual-runbook.md)
- Para saber mais sobre a criação de runbook gráfica, consulte [Graphical authoring na automação do Azure](automation-graphical-authoring-intro.md)
