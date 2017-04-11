<properties 
    pageTitle="Como iniciar o streaming trabalhos no fluxo Analytics | Microsoft Azure" 
    description="Como executar um fluxo de trabalho no Azure fluxo Analytics | segmento de caminho de aprendizagem."
    keywords="trabalhos de streaming"
    documentationCenter=""
    services="stream-analytics"
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

# <a name="how-to-run-a-streaming-job-in-azure-stream-analytics"></a>Como executar um fluxo de trabalho na análise de fluxo do Azure

Quando uma entrada de trabalho, a consulta e a saída todos foram especificados que você pode iniciar o trabalho de análise de fluxo.

Para iniciar o seu trabalho:

1.  No portal do Azure clássico, no painel de trabalho, clique em **Iniciar** na parte inferior da página.

    ![Iniciar trabalho de botão](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)  

    No portal do Azure, clique em **Iniciar** na parte superior da sua página de trabalho.

    ![Trabalho de inicial portal Azure botão](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)  

2.  Especifique um valor de **Saída iniciar** para determinar quando este trabalho começarão a produzir saída. A configuração padrão para trabalhos que não foram iniciados anteriormente é **Hora de início do trabalho**, o que significa que o trabalho será iniciado imediatamente dados de processamento. Você também pode especificar um horário **personalizado** no passado (para consumir dados históricos) ou no futuro (para atrasar o processamento até um tempo futuro). Para casos quando um trabalho foi anteriormente iniciado e interrompido, a opção **Última vez interrompido** está disponível para retomar o trabalho da última vez em saída e evitar perda de dados.  

    ![Iniciar o fluxo de trabalho de tempo](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)  

    ![Azure portal streaming trabalho inicial tempo](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)  

3.  Confirme sua seleção. O status do trabalho será alterado para *Iniciar* e em breve moverá para *execução* quando o trabalho iniciou. Você pode monitorar o andamento da operação de **Iniciar** no **Hub de notificação**:

    ![andamento fluxo de trabalho](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)  

    ![Portal Azure streaming progresso de trabalho](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)  

## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [Fórum de análise de fluxo do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
