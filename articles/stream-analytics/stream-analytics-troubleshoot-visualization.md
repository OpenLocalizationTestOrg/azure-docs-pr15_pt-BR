<properties
    pageTitle="Visualizar e solucionar problemas de trabalhos de análise de fluxo | Microsoft Azure"
    description="Saiba como visualizar um pipeline de trabalho de análise de fluxo de autoatendimento de solução de problemas usando o recurso de diagrama de diagnóstico."
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>Visualizar e trabalhos de análise de fluxo de solução de problemas

Em análise de fluxo, como com outras tecnologias baseado em nuvem, solução de problemas é às vezes necessária para verificar por que um trabalho não produz o resultado esperado (ou qualquer saída na verdade). Com isso em mente, a análise de fluxo oferece a capacidade de visualizar um fluxo de trabalho. Isso também é útil como uma ferramenta de modelagem e tem o benefício de lado para esses que exigem documentação de seu trabalho.

No painel de visualização as entradas estão visíveis, bem como a consulta está sendo executada e, em seguida, todas as saídas configuradas. Problemas de conectividade ou configuração podem ficar mais evidente e também pode ser útil ver uma representação visual da sua configuração.

## <a name="using-the-diagnosis-diagram-tool"></a>Usando a ferramenta de diagrama de diagnóstico

Para acessar esse visualizador, basta clicar no botão "Diagnóstico diagrama" na lâmina "Configurações" do do trabalho de análise de fluxo.

![Stream-Analytics-Troubleshoot-Visualization-Diagnosis-Diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Cada entrada e saída é estado de cor codificado para indicar atual desse componente, conforme mostrado abaixo.

![Stream-Analytics-Troubleshoot-Visualization-Input-Map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Quando o usuário deseja examinar etapas de consulta intermediária entender os padrões de fluxo de dados dentro de um trabalho, a ferramenta de visualização fornece um modo de exibição do detalhamento de consulta em suas etapas de componente e a sequência de fluxo. Clicar em cada etapa de consulta mostrará a seção correspondente em uma consulta painel conforme ilustrado de edição. 

![Stream-Analytics-Troubleshoot-Visualization-Intermediate-Steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)




## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
