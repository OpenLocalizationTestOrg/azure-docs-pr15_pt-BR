<properties 
    pageTitle="Editando runbooks textual na automação do Azure"
    description="Este artigo fornece procedimentos diferentes para trabalhar com o PowerShell e ao fluxo de trabalho do PowerShell runbooks na automação do Azure usando o editor de texto."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="stevenka"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="02/23/2016"
    ms.author="magoedte;bwren" />

# <a name="editing-textual-runbooks-in-azure-automation"></a>Editando runbooks textual na automação do Azure

O editor textual na automação do Azure pode ser usado para editar [runbooks PowerShell](automation-runbook-types.md#powershell-runbooks) e [runbooks de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Isso tem os recursos típicos de outros editores de código como intellisense e codificação de cores com recursos especiais adicionais para ajudá-lo a acessar recursos comuns a runbooks.  Este artigo fornece etapas detalhadas para realizar funções diferentes com este editor.

O editor textual inclui um recurso para inserir o código para atividades, ativos e runbooks filho em um runbook. Em vez de digitar o código por conta própria, você pode selecionar de uma lista de recursos disponíveis e têm o código apropriado inserido em runbook.

Cada runbook no Azure automação tem duas versões, rascunho e publicado. Você pode editar a versão de rascunho do runbook e depois publicá-lo para que ele pode ser executado. A versão publicada não pode ser editada. Para obter mais informações, consulte [um runbook de publicação](automation-creating-importing-runbook.md#publishing-a-runbook) .

Para trabalhar com [Gráficos Runbooks](automation-runbook-types.md#graphical-runbooks), consulte [Graphical coautoria na automação do Azure](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Para editar um runbook com o portal do Azure

Use o procedimento a seguir para abrir um runbook para edição no editor de texto.

1. No portal do Azure, selecione sua conta de automação.
2. Clique no bloco **Runbooks** para abrir a lista de runbooks.
3. Clique no nome do runbook que você deseja editar e, em seguida, clique no botão **Editar** .
6. Execute a edição necessários.
7. Clique em **Salvar** quando suas edições forem concluídas.
8. Se quiser que a última versão de rascunho do runbook para ser publicado, clique em **Publicar** .

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Inserir um cmdlet em um runbook

2. Na tela do editor de texto, posicione o cursor onde você deseja colocar o cmdlet.
3. Expanda o nó de **Cmdlets** no controle de biblioteca. 
3. Expanda o módulo que contém o cmdlet que você deseja usar.
4. Clique com o botão direito do mouse mouse o cmdlet para inserir e selecione **Adicionar a tela**.  Se o cmdlet tem mais de um parâmetro definido, em seguida, o conjunto padrão será adicionado.  Você também pode expandir o cmdlet para selecionar um conjunto de parâmetros diferentes.
4. O código para o cmdlet é inserido com sua lista inteira de parâmetros.
5. Forneça um valor apropriado no lugar do tipo de dados cercado por <> de chaves para todos os parâmetros obrigatórios.  Remova quaisquer parâmetros que você não precisa.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Para inserir o código para um runbook filho em um runbook

2. Na tela do editor de texto, posicione o cursor onde você deseja colocar o código para [runbook filho](automation-child-runbooks.md).
3. Expanda o nó **Runbooks** no controle de biblioteca. 
3. Clique com o botão direito do mouse mouse runbook para inserir e selecione **Adicionar a tela**.
4. O código para runbook filho é inserido com os espaços reservados para os parâmetros de runbook.
5. Substitua os espaços reservados com valores apropriados para cada parâmetro.

### <a name="to-insert-an-asset-into-a-runbook"></a>Inserir um ativo em um runbook

2. Na tela do editor de texto, posicione o cursor onde você deseja colocar o código para runbook filho.
3. Expanda o nó de **ativos** no controle de biblioteca. 
4. Expanda o nó para o tipo de ativo desejado.
3. Clique com o botão direito do mouse mouse ativo para inserir e selecione **Adicionar a tela**.  [Variáveis ativos](automation-variables.md), selecione **Adicionar "obter variável" a tela** ou **Adicionar "Definir variável" tela** dependendo se você quiser obter ou definir a variável.
4. O código do ativo é inserido runbook.



## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Para editar um runbook com o portal do Azure

Use o procedimento a seguir para abrir um runbook para edição no editor de texto.

1. No portal do Azure, selecione **automação** e, em seguida, clique no nome de uma conta de automação.
2. Selecione a guia **Runbooks** .
3. Clique no nome do runbook que você deseja editar e selecione a guia de **autor** .
5. Clique no botão **Editar** na parte inferior da tela.
6. Execute a edição necessários.
7. Clique em **Salvar** quando suas edições forem concluídas.
8. Se quiser que a última versão de rascunho do runbook para ser publicado, clique em **Publicar** .

### <a name="to-insert-an-activity-into-a-runbook"></a>Inserir uma atividade em um Runbook

1. Na tela do editor de texto, posicione o cursor onde você deseja colocar a atividade.
1. Na parte inferior da tela, clique em **Inserir** e em seguida **atividade**.
1. Na coluna da **Integração do módulo** , selecione o módulo que contém a atividade.
1. No painel de **atividade** , selecione uma atividade.
1. Na coluna **Descrição** , observe a descrição da atividade. Opcionalmente, você pode clicar em Exibir Ajuda ao iniciar a Ajuda para a atividade no navegador detalhada.
1. Clique na seta à direita.  Se a atividade tiver parâmetros, eles serão listados para as suas informações.
1. Clique no botão de seleção.  A atividade de execução de código será inserido em runbook.
1. Se a atividade requer parâmetros, forneça um valor apropriado no lugar do tipo de dados cercado por <> de chaves.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Para inserir o código para um runbook filho em um runbook

1. Na tela do editor de texto, posicione o cursor onde você deseja colocar [runbook filho](automation-child-runbooks.md).
2. Na parte inferior da tela, clique em **Inserir** e, em seguida, **Runbook**.
3. Selecione runbook para inserir a partir do centro da coluna e clique na seta à direita.
4. Se runbook tiver parâmetros, eles serão listados para as suas informações.
5. Clique no botão de seleção.  Código para executar runbook selecionado será inserido no runbook atual.
7. Se runbook requer parâmetros, forneça um valor apropriado no lugar do tipo de dados cercado por <> de chaves.

### <a name="to-insert-an-asset-into-a-runbook"></a>Inserir um ativo em um runbook

1. Na tela do editor de texto, posicione o cursor onde você deseja colocar a atividade para recuperar o ativo.
1. Na parte inferior da tela, clique em **Inserir** e em seguida **configuração**.
1. Na coluna **Ação de configuração** , selecione a ação desejada.
1. Selecione os ativos disponíveis no centro da coluna.
1. Clique no botão de seleção.  Código para obter ou definir o ativo será inserido no runbook.



## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Para editar um runbook de automação do Azure usando o Windows PowerShell

Para editar um runbook com o Windows PowerShell, você pode usa o editor de sua escolha e salvá-lo em um arquivo. ps1. Você pode usar o cmdlet [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) para recuperar o conteúdo da runbook e depois cmdlet [Set-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) substitua runbook rascunho existente modificada.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Para recuperar o conteúdo de um Runbook usando o Windows PowerShell

Os comandos de exemplo a seguir mostram como recuperar o script para um runbook e salvá-lo em um arquivo de script. Neste exemplo, a versão de rascunho é recuperada. Também é possível recuperar a versão publicada do runbook Embora esta versão não pode ser alterada.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"
    
    $runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
    $runbookContent = $runbookDefinition.Content

    Out-File -InputObject $runbookContent -FilePath $scriptPath

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Para alterar o conteúdo de um Runbook usando o Windows PowerShell

Os comandos de exemplo a seguir mostram como substituir o conteúdo existente de um runbook com o conteúdo de um arquivo de script. Observe que este é o mesmo procedimento de amostra como [Importar um runbook de um arquivo de script com o Windows PowerShell](../automation-creating-or-importing-a-runbook#ImportRunbookScriptPS).

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName

## <a name="related-articles"></a>Artigos relacionados

- [Criar ou importar um runbook na automação do Azure](automation-creating-importing-runbook.md)
- [Fluxo de trabalho do PowerShell de aprendizagem](automation-powershell-workflow.md)
- [Gráfica de coautoria na automação do Azure](automation-graphical-authoring-intro.md)
- [Certificados](automation-certificates.md)
- [Conexões](automation-connections.md)
- [Credenciais](automation-credentials.md)
- [Agendas](automation-schedules.md)
- [Variáveis](automation-variables.md)