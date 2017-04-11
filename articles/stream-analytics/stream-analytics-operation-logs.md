<properties 
    pageTitle="Depurar usando operação e logs de serviço na análise de fluxo | Microsoft Azure" 
    description="Logs de operações de análise de fluxo de instruções de uso" 
    keywords="logs de serviço"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

# <a name="debug-stream-analytics-jobs-using-service-and-operation-logs"></a>Depurar trabalhos de análise de fluxo usando logs de serviço e operação

Todos os serviços do Azure fornecem mensagens de log operacional aos usuários para registrar detalhes relacionados às operações de gerenciamento. Em análise de fluxo de Azure, essas informações podem ser usadas para fins como exibir o status do trabalho, o andamento do trabalho, de depuração e comece a mensagens de falha para acompanhar o andamento de uma tarefa ao longo do tempo, de processamento de saída.

## <a name="find-operation-logs-in-the-azure-management-portal"></a>Localizar a operação logs no portal de gerenciamento do Azure

Logs de operação podem ser acessadas de duas maneiras:  

- Painel do trabalho fluxo Analytics  
- Serviços de gerenciamento no portal do Azure clássico  

## <a name="dashboard-of-the-stream-analytics-job"></a>Painel do trabalho fluxo Analytics

Um link para os registros correspondentes de um trabalho de análise de fluxo é exibido na guia do painel de trabalho. Se você clicar no link, ele definirá os filtros de uma maneira que ela mostra logs mais recentes para esse trabalho específico.

  ![Selecione os logs de serviços de gerenciamento](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)  

## <a name="management-services"></a>Serviços de gerenciamento

Para navegar manualmente os logs de operação para a análise de fluxo e outros serviços no portal do Azure clássico:

1.  Clique em **Serviços de gerenciamento** no [portal do Azure clássico](https://manage.windowsazure.com).
2.  Selecione **A análise de fluxo** para o **tipo** e o nome do trabalho para **Nome do serviço**.  

  ![Selecione a análise de fluxo](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)  

## <a name="find-audit-logs-in-the-azure-portal"></a>Localizar os logs de auditoria no portal do Azure ##

Para localizar logs operacionais do seu trabalho de análise de fluxo no portal do Azure, clique em **Procurar** e selecione **logs de auditoria**.

  ![Selecione a análise de fluxo de portal Azure](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)  

Isso abrirá uma lâmina mostrando eventos dos últimos 7 dias para todos os recursos na sua assinatura.  Você pode filtrar para ver os eventos de um tipo de especificar ou período clicando no comando de **filtro** .

  ![Selecione a análise de fluxo de portal Azure](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)  

## <a name="get-log-details"></a>Obter detalhes de log

Você pode filtrar por Status para exibir os logs para o seu trabalho e intervalo de tempo.

No portal de gerenciamento do Azure, clique no botão **detalhes** na parte inferior da janela para exibir mais detalhes sobre um evento selecionado. 

  ![Selecione detalhes](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)  

No portal do Azure, clique em uma entrada de log para ver os eventos detalhados dentro dela.

  ![Portal do Azure selecione detalhes](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)  

A partir daí, você pode abrir a lâmina **detalhes** clicando no evento.

  ![Portal do Azure selecione detalhes](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)  

## <a name="debug-a-failed-job"></a>Depurar um trabalho falhou

No portal de gerenciamento do Azure, clique no ícone Pesquisar e digite 'Falha'. Isso proporciona um resultado de todos os registros com falhas. 

  ![Depuração um trabalho falhou](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)  

No portal do Azure, você pode filtrar por nível de mensagem para exibir eventos de **crítico** .

  ![Depuração de portal Azure](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)  

Você pode selecionar qualquer uma das falhas de e clique em **detalhes** para obter mais informações sobre o erro.  Algumas mensagens de erro também fornecem informações sobre como reduzir o problema. 

  ![Detalhes da operação](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)  

No caso de precisar contatar o [suporte](https://azure.microsoft.com/support/options/) ou fornecer informações para a equipe através do [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics), observe os detalhes da operação, especificamente a **ID de correlação**. 

## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [Fórum de análise de fluxo do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
