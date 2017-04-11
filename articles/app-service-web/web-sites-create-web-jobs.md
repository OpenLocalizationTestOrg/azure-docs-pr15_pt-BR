<properties 
    pageTitle="Realize tarefas em segundo plano com WebJobs" 
    description="Saiba como executar tarefas em segundo plano no Azure web apps." 
    services="app-service" 
    documentationCenter="" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/27/2016" 
    ms.author="tdykstra"/>

# <a name="run-background-tasks-with-webjobs"></a>Realize tarefas em segundo plano com WebJobs

## <a name="overview"></a>Visão geral

Você pode executar programas ou scripts em WebJobs em seu aplicativo web do [Aplicativo de serviço](http://go.microsoft.com/fwlink/?LinkId=529714) de três maneiras: sob demanda, continuamente, ou em um cronograma. Não há nenhum custo adicional para usar WebJobs.

Este artigo mostra como implantar WebJobs usando o [Portal do Azure](https://portal.azure.com). Para obter informações sobre como implantar usando o Visual Studio ou um processo de fornecimento contínuo, veja [como implantar o Azure WebJobs para aplicativos Web](websites-dotnet-deploy-webjobs.md).

O SDK do Azure WebJobs simplifica WebJobs muitas tarefas de programação. Para obter mais informações, consulte [o que é o SDK do WebJobs](websites-dotnet-webjobs-sdk.md).

 Funções do Azure (atualmente em preview) é outra maneira de executar programas e scripts em um serviço de aplicativo do Azure. Para obter mais informações, consulte [Visão geral de funções do Azure](../azure-functions/functions-overview.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="acceptablefiles"></a>Tipos de arquivo aceitáveis para scripts ou programas

Os seguintes tipos de arquivo são aceitos:

* . cmd,. bat, .exe (usando o windows cmd)
* . ps1 (usando o powershell)
* sh (usando bash)
* . PHP (usando php)
* .py (usando python)
* . js (usando o nó)
* . jar (usando java)

## <a name="CreateOnDemand"></a>Criar uma sob demanda WebJob no portal

1. Na lâmina **Web App** do [Portal do Azure](https://portal.azure.com), clique em **todas as configurações > WebJobs** para mostrar a lâmina **WebJobs** .
    
    ![WebJob lâmina](./media/web-sites-create-web-jobs/wjblade.png)
    
5. Clique em **Adicionar**. A caixa de diálogo **Adicionar WebJob** é exibida.
    
    ![Adicionar WebJob lâmina](./media/web-sites-create-web-jobs/addwjblade.png)
    
2. Em **nome**, forneça um nome para o WebJob. O nome deve começar com uma letra ou um número e não pode conter caracteres especiais diferente de "-" e "_".
    
4. Na caixa **Executar como** , escolha **executar sob demanda**.
    
3. Na caixa de **Carregamento de arquivo** , clique no ícone de pasta e navegue até o arquivo zip que contém o script. O arquivo zip deve conter seu executável (.exe. cmd. bat sh. php .py. js), além de quaisquer arquivos de suporte necessários para executar o programa ou script.
    
5. Marque **criar** para carregar o script em seu aplicativo web. 
    
    O nome especificado para o WebJob aparece na lista na lâmina **WebJobs** .
    
6. Para executar o WebJob, seu nome na lista de atalho e clique em **Executar**.
    
    ![Executar WebJob](./media/web-sites-create-web-jobs/runondemand.png)
    
## <a name="CreateContinuous"></a>Criar um WebJob continuamente em execução

1. Para criar um WebJob continuamente em execução, siga as mesmas etapas para criar um WebJob que é executado de uma vez, mas, na caixa **Executar como** , escolha **contínuo**.

2. Para iniciar ou interromper um WebJob contínuo, clique com botão direito do WebJob na lista e clique em **Iniciar** ou **Parar**.
    
> [AZURE.NOTE] Se o seu aplicativo web é executado em mais de uma instância, um WebJob continuamente em execução será executado em todas as suas instâncias. Sob demanda e agendadas WebJobs executados em uma única instância selecionada para balanceamento de carga, Microsoft Azure.
    
> Para WebJobs contínua confiável e em todas as instâncias, ative o sempre * configuração para o web app caso contrário, ele podem interromper a execução quando o site de host SCM esteve ocioso por muito tempo.

## <a name="CreateScheduledCRON"></a>Criar uma WebJob agendada usando uma expressão CRON

Essa técnica está disponível para aplicativos Web em execução no modo básico, padrão ou Premium e requer a configuração **Sempre** esteja habilitada no aplicativo.

Para transformar uma WebJob de demanda em uma WebJob agendada, simplesmente incluir uma `settings.job` arquivo na raiz do seu arquivo de zip WebJob. Este arquivo JSON deve incluir uma `schedule` propriedade com uma [expressão CRON](https://en.wikipedia.org/wiki/Cron), por exemplo abaixo.

A expressão CRON é composta por 6 campos: `{second} {minute} {hour} {day} {month} {day of the week}`.

Por exemplo, para acionar seu WebJob a cada 15 minutos, suas `settings.job` teria:

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

Outros exemplos de cronograma CRON:

- Cada hora (ou seja, sempre que a contagem de minutos é 0):`0 0 * * * *` 
- Cada hora de 9H para 5 PM:`0 0 9-17 * * *` 
- Em 9:30 AM diariamente:`0 30 9 * * *`
- Em 9:30 AM todos os dias da semana:`0 30 9 * * 1-5`

**Observação**: ao implantar um WebJob do Visual Studio, certifique-se de marcar sua `settings.job` propriedades do arquivo como 'Copiar se mais recente'.


## <a name="CreateScheduled"></a>Criar uma WebJob agendada usando o Agendador do Azure

A seguinte técnica alternativa utiliza o Agendador do Azure. Nesse caso, o WebJob não tem qualquer conhecimento direto da agenda. Em vez disso, o Agendador do Azure obtém configurado para disparar sua WebJob em um cronograma. 

Portal do Azure ainda não tem a capacidade de criar uma WebJob agendada, mas até que recurso é adicionado, que você poderá fazer isso usando o [portal clássico](http://manage.windowsazure.com).

1. No [portal clássico](http://manage.windowsazure.com) ir para a página de WebJob e clique em **Adicionar**.

1. Na caixa **Executar como** , escolha **executar em um cronograma**.
    
    ![Novo trabalho agendado][NewScheduledJob]
    
2. Escolha o **Agendador de região** para o seu trabalho e, em seguida, clique na seta no canto inferior direito da caixa de diálogo para prosseguir para a próxima tela.

3. Na caixa de diálogo **Criar trabalho** , escolha o tipo de **Recorrência** desejada: **trabalho único** ou **recorrente trabalho**.
    
    ![Agendar recorrência][SchdRecurrence]
    
4. Também escolha uma hora de **início** : **agora** ou **em uma hora específica**.
    
    ![Hora de início do cronograma][SchdStart]
    
5. Se você quer começar em uma hora específica, escolha seus valores de tempo iniciais em **Iniciar em**.
    
    ![Início do cronograma em uma hora específica][SchdStartOn]
    
6. Se você escolheu um trabalho recorrente, você tem o **Repetir cada** opção para especificar a frequência de ocorrência e a opção **Que terminam em** especificar uma hora de término.
    
    ![Agendar recorrência][SchdRecurEvery]
    
7. Se você escolher **semanas**, você pode marque a caixa de **Em um agendamento em Particular** e especifique os dias da semana em que você deseja que o trabalho seja executado.
    
    ![Cronograma dias da semana][SchdWeeksOnParticular]
    
8. Se você escolher **meses** e marque a caixa de **Em um agendamento em Particular** , você pode definir o trabalho para executar em determinados numerada **dias** no mês. 
    
    ![Cronograma de datas específico no mês][SchdMonthsOnPartDays]
    
9. Se você escolher **Dias da semana**, você pode selecionar qual dia ou dias da semana no mês que você deseja que o trabalho para executar em.
    
    ![Agendar dias da semana específico em um mês][SchdMonthsOnPartWeekDays]
    
10. Por fim, você também pode usar a opção **ocorrências** para escolher em qual semana no mês (primeiro, segundo, terceiro etc) que você deseja que o trabalho para executar nos dias da semana que você especificou.
    
    ![Agende dias da semana específico em determinado semanas em um mês][SchdMonthsOnPartWeekDaysOccurences]
    
11. Após ter criado um ou mais trabalhos, seus nomes aparecerão na guia WebJobs com seu status, tipo de cronograma e outras informações. Informações de histórico para os últimos 30 WebJobs são mantidas.
    
    ![Lista de trabalhos][WebJobsListWithSeveralJobs]
    
### <a name="Scheduler"></a>Tarefas agendadas e o Agendador do Azure

Tarefas agendadas podem ser configuradas nas páginas do Azure Agendador do [portal clássico](http://manage.windowsazure.com).

1.  Na página WebJobs, clique em link de **cronograma** do trabalho para navegar até a página de portal do Agendador do Azure. 
    
    ![Link para o Agendador do Azure][LinkToScheduler]
    
2. Na página do Agendador, clique no trabalho.
    
    ![Trabalho na página do portal Agendador][SchedulerPortal]
    
3. A página de **ação do trabalho** se abre, onde você pode configurar melhor o trabalho. 
    
    ![Ação do trabalho PageInScheduler][JobActionPageInScheduler]
    
## <a name="ViewJobHistory"></a>Exibir o histórico de trabalho

1. Para exibir o histórico de execução de um trabalho, incluindo trabalhos criados com o SDK do WebJobs, clique no link correspondente na coluna de **Logs** do blade WebJobs. (Você pode usar o ícone de área de transferência para copiar a URL da página de arquivo de log para a área de transferência, se desejar).
    
    ![Link de logs](./media/web-sites-create-web-jobs/wjbladelogslink.png)
        
2. Clicando no link abre a página de detalhes para o WebJob. Esta página mostra o nome do comando Executar, a hora da última-executou, e seu sucesso ou falha. Em **recente trabalho é executado**, clique em um horário para ver mais detalhes.
    
    ![WebJobDetails][WebJobDetails]
    
3. A página de **Detalhes de executar WebJob** aparece. Clique em **Saída de alternância** para ver o texto do conteúdo do log. O log de saída está no formato de texto. 
    
    ![Detalhes de execução de trabalho de Web][WebJobRunDetails]
    
4. Para ver o texto de saída em uma janela separada do navegador, clique no link **Baixar** . Para baixar o texto em si, clique com botão direito no link e use as opções do navegador para salvar o conteúdo do arquivo.
    
    ![Baixar o log de saída][DownloadLogOutput]
    
5. O link de **WebJobs** na parte superior da página fornece uma maneira conveniente para acessar uma lista de WebJobs no painel Histórico.
    
    ![Vincular a lista de WebJobs][WebJobsLinkToDashboardList]
    
    ![Lista de WebJobs no painel de controle de histórico][WebJobsListInJobsDashboard]
    
    Clicando em um desses links leva você para a página de detalhes de WebJob para o trabalho que você selecionou.


## <a name="WHPNotes"></a>Anotações
    
- Web apps no modo gratuito podem expirar após 20 minutos se não houver nenhuma solicitação para o site de scm (implantação) e o portal do aplicativo da web não abrem no Azure. As solicitações para o site real não irá redefinir isso.
- Código para um trabalho contínuo precisa ser escrita para ser executado em um loop infinito.
- Trabalhos contínuos continuamente executados somente quando o aplicativo da web está para cima.
- Básico e oferecer modos padrão a sempre no recurso que, quando ativada, impede que aplicativos web se tornar ocioso.
- Você só pode depurar continuamente executando WebJobs. Não há suporte para a depuração WebJobs agendado ou sob demanda.

## <a name="NextSteps"></a>Próximas etapas
 
Para obter mais informações, consulte [Recursos de recomendados do Azure WebJobs][WebJobsRecommendedResources].

[PSonWebJobs]:http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx
[WebJobsRecommendedResources]:http://go.microsoft.com/fwlink/?LinkId=390226

[OnDemandWebJob]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
[WebJobsList]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
[NewContinuousJob]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
[NewScheduledJob]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
[SchdRecurrence]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
[SchdStart]: ./media/web-sites-create-web-jobs/06SchdStart.png
[SchdStartOn]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
[SchdRecurEvery]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
[SchdWeeksOnParticular]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
[SchdMonthsOnPartDays]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
[SchdMonthsOnPartWeekDays]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
[SchdMonthsOnPartWeekDaysOccurences]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
[RunOnce]: ./media/web-sites-create-web-jobs/13RunOnce.png
[WebJobsListWithSeveralJobs]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
[WebJobLogs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
[WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
[WebJobRunDetails]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
[DownloadLogOutput]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
[WebJobsLinkToDashboardList]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
[WebJobsListInJobsDashboard]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
[LinkToScheduler]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
[SchedulerPortal]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
[JobActionPageInScheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png
 
