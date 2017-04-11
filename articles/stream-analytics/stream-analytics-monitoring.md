<properties 
    pageTitle="Trabalho de análise de fluxo de Noções básicas sobre monitoramento | Microsoft Azure" 
    description="Noções básicas sobre o monitoramento do trabalho de análise de fluxo" 
    keywords="monitor de consulta"
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

# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Entender a análise de fluxo de trabalho monitoramento e como monitorar consultas

## <a name="introduction-the-monitor-page"></a>Introdução: Página do monitor

O portal de gerenciamento do Azure e o Portal do Azure superfície métricas de chave de desempenho que podem ser usadas para monitorar e solucionar problemas de desempenho de sua consulta e trabalho. 

No portal de gerenciamento do Azure, clique na guia **Monitor** de um trabalho em execução de análise de fluxo para ver essas métricas. Não há um atraso de no máximo 1 minuto nas métricas de desempenho aparecendo na página Monitor.  

  ![Trabalho de monitoramento Dashboard](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

No Portal do Azure, navegue até o trabalho de análise de fluxo você estiver interessado em métricas vendo para e exibir a seção de **monitoramento** .  

  ![Trabalho de Portal monitoramento Azure Dashboard](./media/stream-analytics-monitoring/06-stream-analytics-monitoring.png)  

Na primeira vez em que um trabalho de análise de fluxo é criado em uma região, você precisará configurar diagnósticos para aquela região. Para fazer isso, clique em qualquer lugar na seção de **monitoramento** e a lâmina de **Diagnóstico** será exibida. Aqui você pode habilitar o diagnóstico e especifique uma conta de armazenamento para monitoramento de dados.  

  ![Consulta de Portal configurar Azure diagnóstico](./media/stream-analytics-monitoring/07-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Disponível para a análise de fluxo de métricas


| Métrica | Definição |
|--------|-------------|
| % De utilização do SO | A utilização da unidade de Streaming atribuído a um trabalho a partir da guia escala do trabalho. Esse indicador alcançar 80% ou acima, há probabilidade alta que o processamento de eventos pode ser atrasado ou parado fazendo progresso. |
| Eventos de entrada | Quantidade de dados recebidos pelo trabalho de análise de fluxo, em número de eventos. Isso pode ser usado para validar que eventos estão sendo enviados para a fonte de entrada. |
| Eventos de saída | Quantidade de dados enviados pelo trabalho de análise de fluxo para o destino de saída, em número de eventos. |
| Eventos de fora da ordem | Número de eventos recebidos fora ordem em que foram descartados ou dado um carimbo de hora ajustado, com base na política de ordenação de evento. Isso pode ser afetado pela configuração da configuração da janela de tolerância de fora de ordem. |
| Erros de conversão de dados | Número de erros de conversão de dados incorridos por um trabalho de análise de fluxo. |
| Erros de tempo de execução | Número de erros que ocorrem durante a execução de uma a análise de fluxo de trabalho. |
| Eventos de entrada atrasados | Número de eventos que chegam atrasadas da fonte que tenha sido descartados ou seu carimbo de hora foi ajustado, com base na configuração de política de pedidos de eventos da configuração da janela de tolerância de chegada atrasado. |

## <a name="customizing-monitoring-in-the-azure-management-portal"></a>Personalizando o monitoramento no portal de gerenciamento do Azure ##

Até 6 métricas podem ser exibidas em um gráfico.

Para alternar entre exibir valores relativos (valor final somente para cada métrica) e valores absolutos (eixo Y exibido), selecione relativo ou absoluto na parte superior do gráfico.

  ![Monitor de consulta relativa absoluta](./media/stream-analytics-monitoring/02-stream-analytics-monitoring.png)  

Métricas podem ser exibidas no gráfico Monitor agregações de 1 hora, 12 horas, 24 horas ou 7 dias.

Para alterar o intervalo de tempo exibe o gráfico métricas, selecione 1 hora, 24 horas, ou 7 dias na parte superior do gráfico.

  ![Monitor de consulta escala de tempo](./media/stream-analytics-monitoring/03-stream-analytics-monitoring.png)  

Você pode definir regras que podem notificá-lo por email caso o trabalho cruza um limite definido. 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Personalizando o monitoramento no Portal do Azure ##

Você pode ajustar o tipo de gráfico, métricas mostrados e intervalo nas configurações Editar gráfico de horário. Para obter detalhes, consulte [como personalizar monitoramento](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Escala de tempo do Azure consulta Portal Monitor](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  

## <a name="job-status"></a>Status do trabalho

O status de trabalhos de análise de fluxo pode ser visto no Portal de clássico do Azure onde você pode ver uma lista de trabalhos. Você pode ver a lista de trabalhos clicando no ícone de análise de fluxo no Portal de clássico do Azure.

| Status | Definição |
|--------|------------|
| Criado | Um trabalho tiver sido criado, porém não foi iniciado. |
| Iniciando | Um usuário clicasse em Iniciar o trabalho e o trabalho está iniciando |
| Em execução | O trabalho é alocado, processamento de entrada ou aguardando para processar a entrada. Se o trabalho mostra um estado de funcionamento sem produzir saída, é provável que a janela de tempo de processamento de dados é grande ou a lógica de consulta é complicada. Outro motivo pode ser que atualmente lá não quaisquer dados sendo enviados para o trabalho. |
| Interrompendo | Um usuário clicar em parar o trabalho e o trabalho está sendo interrompido. |
| Interrompido | O trabalho foi interrompido. |
| Degradado | Este estado indica que um trabalho de análise de fluxo está encontrando erros temporárias (para ex. Erros de entrada/saídos, erros de processamento, erros de conversão etc.). O trabalho ainda está sendo executado, porém, existem muitas erros sendo gerado. Este trabalho precisa de atenção do cliente e o cliente pode ver os logs de operações para os erros. |
| Falha | Isso indica que o trabalho falhou devido a erros, e o processamento parou. O cliente precisa para verificar os logs de operações para depurar os erros. |
| Excluindo | Isso indica que o trabalho está sendo excluído. |

## <a name="diagnosis"></a>Diagnóstico

No portal de gerenciamento do Azure, o painel de trabalho fornece informações sobre onde você precisa procurar o diagnóstico, ou seja, entradas, saídas e/ou as operações de logon. Você pode clicar no link para acessar o local apropriado para examinar o diagnóstico.

  ![Monitor de consulta erro](./media/stream-analytics-monitoring/04-stream-analytics-monitoring.png)  

Clicar no recurso de entrada ou saído fornece informações detalhadas de diagnóstico. Isso é atualizado com as informações de diagnóstico mais recentes, enquanto o trabalho está em execução.

  ![Diagnóstico de consulta](./media/stream-analytics-monitoring/05-stream-analytics-monitoring.png)  

## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [Fórum de análise de fluxo do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
