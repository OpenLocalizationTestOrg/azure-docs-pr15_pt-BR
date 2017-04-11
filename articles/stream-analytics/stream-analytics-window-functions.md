<properties
    pageTitle="Introdução às funções de fluxo Analytics janela | Microsoft Azure"
    description="Saiba mais sobre as três funções de janela no fluxo Analytics (queda saltos, deslizando)."
    keywords="queda de janela, janela, saltos janela deslizante"
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


# <a name="introduction-to-stream-analytics-window-functions"></a>Introdução às funções de janela de análise de fluxo

Em muitos tempo real streaming cenários, é necessário realizar operações apenas nos dados contidos no windows Temporais. Suporte nativo para funções em janela é um recurso chave da análise de fluxo de Azure que se move agulha em produtividade do desenvolvedor na criação de trabalhos de processamento de fluxo complexas. Análise de fluxo permite aos desenvolvedores usar janelas [**queda**](https://msdn.microsoft.com/library/dn835055.aspx), [**Hopping**](https://msdn.microsoft.com/library/dn835041.aspx) e [**variável**](https://msdn.microsoft.com/library/dn835051.aspx) para executar operações temporais em fluxo de dados. Vale a pena observar que todas as operações de [janela](https://msdn.microsoft.com/library/dn835019.aspx) resultados no **final** da janela de saída. A saída da janela será o único evento com base na função de agregação usada. O evento terá o carimbo de hora do final da janela e todas as funções de janela são definidas com um comprimento fixo. Por último é importante observar que todas as funções de janela devem ser usadas em uma cláusula [**GROUP BY**](https://msdn.microsoft.com/library/dn835023.aspx) .

![Conceitos de funções de janela de análise de fluxo](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Janela de queda

Queda janela funções são usadas para um fluxo de dados de segmento em segmentos de tempo distintos e executar uma função em relação a eles, como no exemplo a seguir. Os principais diferenciais de uma janela de queda são que eles repetir, não se sobreponham e um evento não pode pertencer a mais de uma janela de queda.

![Funções de janela de análise de fluxo queda de Introdução](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Se propagam através de janela

Saltos saltos de funções de janela para frente no tempo por um período fixo. Talvez seja fácil imaginá-los como janelas queda que podem se sobrepor, portanto eventos podem pertencer a mais de um conjunto de resultados de janela Hopping. Para tornar uma janela Hopping iguais a uma queda janela um simplesmente especifique o tamanho de salto para ser o mesmo que o tamanho da janela. 

![Funções de janela de análise de fluxo saltos Introdução](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Janela deslizante

Funções de janela deslizante, ao contrário de queda ou Hopping janelas, produzem uma saída **somente** quando ocorre um evento. Cada janela terá pelo menos um evento e a janela continuamente avança por uma € (epsilo). Como o Windows saltos, eventos podem pertencer a mais de uma janela deslizante.

![Funções de janela de análise de fluxo deslizando Introdução](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>Obtendo ajuda com funções de janela

Para obter assistência, tente nosso [Fórum de análise de fluxo do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
