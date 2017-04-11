<properties 
   pageTitle="Tabela de limites de análise de fluxo"
   description="Descreve os limites de sistema e tamanhos recomendados para conexões e componentes de análise de fluxo."
   services="stream-analytics"
   documentationCenter="NA"
   authors="jeffstokes72"
   manager="paulettm"
   editor="cgronlun" />
<tags 
   ms.service="stream-analytics"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-data"
   ms.date="07/25/2016"
   ms.author="jeffstok" />

| Identificador de limite | Limite       | Comentários |
|----------------- | ------------|--------- |
| Número máximo de unidades de Streaming por assinatura por região | 50 | Uma solicitação para aumentar o streaming unidades para sua assinatura além 50 pode ser feita contatando o [Suporte da Microsoft](https://support.microsoft.com/en-us). |
| Taxa de transferência máxima de uma unidade de Streaming | 1MB / s * | Taxa de transferência máxima por SO depende do cenário. Taxa de transferência real pode ser menor e depende da complexidade da consulta e partição. Detalhes adicionais podem ser encontradas no artigo [trabalhos de análise de fluxo de Azure escala para aumentar a produtividade](../articles/stream-analytics/stream-analytics-scale-jobs.md) . |
| Número máximo de entradas por trabalho | 60 | Há um limite rígido de 60 entradas por trabalho de análise de fluxo. |
| Número máximo de saídas por trabalho | 60 | Há um limite rígido de 60 saídas por trabalho de análise de fluxo. |
| Número máximo de funções por trabalho | 60 | Há um limite rígido de 60 funções por trabalho de análise de fluxo. |
| Número máximo de trabalhos por região | 1500 | Cada assinatura pode ter até 1500 trabalhos por região geográfica. |