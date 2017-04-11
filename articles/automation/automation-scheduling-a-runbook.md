<properties 
   pageTitle="Agendando uma runbook no Azure automação | Microsoft Azure"
   description="Descreve como criar um cronograma no Azure automação, para que você possa iniciar automaticamente um runbook em uma hora específica ou em um agendamento recorrente."
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
   ms.date="08/05/2016"
   ms.author="bwren" />

# <a name="scheduling-a-runbook-in-azure-automation"></a>Agendando uma runbook na automação do Azure

Para agendar uma runbook na automação do Azure para iniciar em um horário especificado, vinculada a um ou mais agendamentos. Um cronograma pode ser configurado para executar uma vez ou em um volte a ocorrer por hora ou diariamente cronograma para runbooks no portal de clássico do Azure e runbooks no portal do Azure, você pode Além disso agendá-los para semanal, mensal, dias específicos da semana ou dias do mês ou um determinado dia do mês.  Um runbook pode ser vinculada a várias agendas e um cronograma pode ter vários runbooks vinculado a ele.


## <a name="creating-a-schedule"></a>Criando uma agenda

Você pode criar uma nova programação para runbooks no portal do Azure, no portal do clássico ou com o Windows PowerShell. Você também tem a opção de criação de uma nova programação quando você vincula um runbook para agendamento usando o portal de clássico ou o Azure Azure.

>[AZURE.NOTE] Quando você associa um cronograma com um runbook, automação armazena as versões atuais dos módulos em sua conta e vincula-los ao cronograma.  Isso significa que, se você tivesse um módulo com a versão 1.0 em sua conta quando você criou um cronograma e, em seguida, atualizar o módulo para a versão 2.0, o cronograma continuará a utilizar 1.0.  Para poder usar a versão de módulo atualizado, você deve criar uma nova agenda. 

### <a name="to-create-a-new-schedule-in-the-azure-classic-portal"></a>Para criar uma nova programação no portal de clássico do Azure

1. No portal do Azure clássico, selecione automação e, em seguida, selecione o nome de uma conta de automação.
1. Selecione a guia de **ativos** .
1. Na parte inferior da janela, clique em **Adicionar configuração**.
1. Clique em **Adicionar agenda**.
1. Digite um **nome** e, opcionalmente, uma **Descrição** para a nova agenda de schedule.your será executado **Uma vez**, **por hora**, **diária**, **semanal**ou **mensal**.
1. Especifique uma **Hora de início** e outras opções dependendo do tipo do agendamento que você selecionou.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Para criar uma nova programação no portal do Azure

1. No portal do Azure, da sua conta de automação, clique no bloco de **ativos** para abrir a lâmina de **ativos** .
2. Clique no bloco de **cronogramas** para abrir a lâmina **agendas** .
3. Clique em **Adicionar um cronograma** na parte superior da lâmina.
4. Na **nova agenda** lâmina, digite um **nome** e, opcionalmente, uma **Descrição** para a nova agenda.
5. Selecione se o agendamento executará uma vez ou em um cronograma redundantes selecionando **uma vez** ou **Recorrência**.  Se você selecionar **uma vez** especificar uma **hora de início** e, em seguida, clique em **criar**.  Se você selecionar **Recorrência**, especifique uma **hora de início** e a frequência com que frequência você deseja runbook repetir - por **hora**, **dia**, **semana**ou por **mês**.  Se você selecionar **semana** ou **mês** na lista suspensa, a **opção de recorrência** aparecerá na lâmina e durante a seleção, a **opção de recorrência** lâmina será apresentada e você pode selecionar o dia da semana, se você selecionou **semana**.  Se você selecionou o **mês**, você pode optar por **dias da semana** ou dias específicos do mês no calendário e por fim, fazer você deseja executá-lo no último dia do mês ou não e, em seguida, clique em **Okey**.   

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Para criar uma nova programação com o Windows PowerShell

Você pode usar o cmdlet [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) para criar uma nova programação de automação do Azure do runbooks clássico ou [New-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx) cmdlet para runbooks no portal do Azure. Especifique a hora de início para a agenda e a frequência que ele deve executar.

Os comandos de exemplo a seguir mostram como criar uma nova programação que executa cada dia em 3:30 PM iniciando em 20 de janeiro de 2015 com um cmdlet do gerenciamento de serviço do Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

Os comandos de exemplo a seguir mostra como criar um cronograma para o dia 30 de cada mês usando um cmdlet do Gerenciador de recursos do Azure e 15.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
    

## <a name="linking-a-schedule-to-a-runbook"></a>Vinculando um cronograma para um runbook

Um runbook pode ser vinculada a várias agendas e um cronograma pode ter vários runbooks vinculado a ele. Se um runbook tiver parâmetros, você pode fornecer valores para eles. Você deve fornecer valores para todos os parâmetros obrigatórios e fornece valores para os parâmetros opcionais.  Esses valores serão usados sempre que runbook é iniciado por essa programação.  Você pode anexar runbook mesmo a outra cronograma e especificar valores de parâmetros diferentes.


### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-classic-portal"></a>Vincular um cronograma para um runbook com o Azure portal clássico

1. No portal do Azure clássico, selecione **automação** e, em seguida, clique no nome de uma conta de automação.
2. Selecione a guia **Runbooks** .
3. Clique no nome do runbook agendar.
4. Clique na guia **cronograma** .
5. Se runbook atualmente não está vinculado a um cronograma, em seguida, você terá a opção para **vincular a um novo cronograma de** ou **vincular a um agendamento existente**.  Se runbook atualmente está vinculado a um cronograma, clique em **Link** na parte inferior da janela para acessar essas opções.
6. Se runbook tiver parâmetros, você será solicitado para seus valores.  

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Vincular um cronograma para um runbook com o portal do Azure

1. No portal do Azure, da sua conta de automação, clique no bloco **Runbooks** para abrir a lâmina **Runbooks** .
2. Clique no nome do runbook agendar.
3. Se runbook atualmente não está vinculado a um cronograma, em seguida, você terá a opção de criar um novo cronograma de ou vincular a um agendamento existente.  
4. Se runbook tiver parâmetros, você pode selecionar a opção **Modify executar configurações (padrão: Azure)** e a lâmina de **parâmetros** é apresentada onde você pode inserir as informações de acordo.  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Vincular um cronograma para um runbook com o Windows PowerShell

Você pode usar o [AzureAutomationScheduledRunbook de registro](http://msdn.microsoft.com/library/azure/dn690265.aspx) para vincular um cronograma para runbook clássico ou [Register-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx) cmdlet para runbooks no portal do Azure.  Você pode especificar valores para parâmetros do runbook com o parâmetro de parâmetros. Consulte [Iniciando um Runbook na automação do Azure](automation-starting-a-runbook.md) para obter mais informações sobre como especificar valores de parâmetro.

Os comandos de exemplo a seguir mostram como vincular um cronograma usando um cmdlet do gerenciamento de serviço do Azure com parâmetros.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

Os comandos de exemplo a seguir mostram como vincular um cronograma para um runbook usando um cmdlet do Gerenciador de recursos do Azure com parâmetros.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"

## <a name="disabling-a-schedule"></a>Desabilitar um cronograma

Quando você desativa uma agenda, qualquer runbooks vinculado a ele não será mais executado no cronograma. Manualmente, você pode desativar um cronograma ou definir uma hora de expiração para cronogramas com uma frequência quando você as cria. Quando o tempo de expiração é alcançado, o cronograma será desativado.

### <a name="to-disable-a-schedule-from-the-azure-classic-portal"></a>Para desativar um cronograma a partir do portal clássico Azure

Você pode desativar um cronograma no portal do clássico Azure da página de detalhes de programação de cronograma.

1. No portal do Azure clássico, selecione automação e, em seguida, clique no nome de uma conta de automação.
1. Selecione a guia de ativos.
1. Clique no nome de um cronograma para abrir a página de detalhes.
2. Alterar **habilitados** para **não**.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Para desativar um cronograma a partir do portal do Azure

1. No portal do Azure, da sua conta de automação, clique no bloco de **ativos** para abrir a lâmina de **ativos** .
2. Clique no bloco de **cronogramas** para abrir a lâmina **agendas** .
2. Clique no nome de um cronograma para abrir a lâmina de detalhes.
3. Alterar **habilitado** para **não**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Para desativar um cronograma com o Windows PowerShell

Você pode usar o cmdlet [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) para alterar as propriedades de um agendamento existente para um runbook clássico ou o cmdlet [Set-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx) para runbooks no portal do Azure. Para desabilitar o cronograma, especifique **Falso** para o parâmetro **IsEnabled** .

Os comandos de exemplo a seguir mostram como desabilitar um cronograma usando o cmdlet do gerenciamento de serviço do Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

Os comandos de exemplo a seguir mostram como desativar uma programação para um runbook usando um cmdlet do Gerenciador de recursos do Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"


## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como trabalhar com agendas, consulte [Ativos de cronograma na automação do Azure](http://msdn.microsoft.com/library/azure/dn940016.aspx)
- Para começar a usar runbooks na automação do Azure, consulte [Iniciando um Runbook na automação do Azure](automation-starting-a-runbook.md) 