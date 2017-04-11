<properties 
    pageTitle="Como escrever consultas em fluxo Analytics | Microsoft Azure" 
    description="Escrever consultas em análise de fluxo e os dados da consulta | segmento de caminho de aprendizagem."
    keywords="como escrever consultas, dados de consulta, escrever uma consulta, escrever consultas"
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

# <a name="how-to-write-queries-in-stream-analytics"></a>Como escrever consultas no fluxo de análise

Escrever consultas para lógica do Azure a análise de fluxo de processamento de fluxo é implementado como uma "consulta de colocada" que é definida antes de um trabalho é iniciado e executadas em dados que atingir o trabalho. A transformação de dados é expressa em uma linguagem de consulta do tipo SQL, que é amplamente um subconjunto de T-SQL com algumas extensões de idioma adicionado como [janelas](https://msdn.microsoft.com/library/azure/dn835019.aspx) usado para expressar semântica temporal.

## <a name="writing-queries"></a>Escrever consultas: ##

1. No seu trabalho de análise de fluxo no portal de gerenciamento do Azure, clique em **consulta**.

    ![Consulta seleção](./media/stream-analytics-write-queries/1-stream-analytics-write-queries.png)  

    No Portal do Azure, clique em **consulta**.

    ![Selecione a visualização de consulta](./media/stream-analytics-write-queries/query-preview-portal.png)  

2.  Novos trabalhos tem um modelo de consulta para ajudar você a começar. O modelo de consulta executa uma consulta "passagem" que projetos todos os campos de eventos de entrada na saída.  

    - Se você tiver definido pelo menos uma entrada e saída para o seu trabalho, você pode substituir o espaço reservado "[YourOutputAlias]" e "[YourInputAlias]" campos com os aliases de entrada e saída que você deseja usam primeiro. Além disso, você ainda pode autor e testar sua consulta no Portal de clássico do Azure sem definir entradas e saídas no trabalho.
    - Se você desejar executar processamento mais que uma passagem simple, você pode editar a definição de consulta. Para começar a criação de consultas, dê uma olhada alguns consulta comum padrões são capturados [aqui](stream-analytics-stream-analytics-query-patterns.md).  
  
    ![Janela dados da consulta](./media/stream-analytics-write-queries/2-stream-analytics-write-queries.png)  

## <a name="to-validate-query-data-is-working"></a>Para validar dados de consulta está funcionando: ##

Você pode testar sua consulta se comporta como esperado executando-o no navegador sobre um ou mais arquivos locais JSON que contém os dados de teste. Isso não iniciar o trabalho ou tiver qualquer implicação de cobrança.

> [AZURE.NOTE] Atualmente o teste de consulta no navegador não é suportado no Portal do Azure.  

1.  Certifique-se de que não há nenhum erros na consulta (caso contrário, o botão de teste será desabilitado) e, em seguida, clique no botão de teste.  

    ![Dados da consulta teste](./media/stream-analytics-write-queries/3-stream-analytics-write-queries.png)  

2.  Você será solicitado para especificar arquivos para cada uma das entradas referenciadas na consulta. Neste exemplo, a consulta de modelo foi deixada como-é, portanto, a caixa de diálogo é pedir uma entrada denominada "yourinputalias".  

    ![Consulta de dados de teste](./media/stream-analytics-write-queries/4-stream-analytics-write-queries.png)  

3.  Navegue até um arquivo de teste. Vários arquivos de exemplo estão disponíveis no [github](https://github.com/Azure/azure-stream-analytics/tree/master/Sample Data) e você também pode recuperar dados de amostra de suas próprias entradas de fluxo de dados por meio da função de dados de exemplo na guia entradas.  

    ![Entrada de uma consulta](./media/stream-analytics-write-queries/5-stream-analytics-write-queries.png)  

4.  Depois de fechar a caixa de diálogo, sua consulta será executada sobre os dados de teste e você verá os resultados na parte inferior da página de consulta.  

    ![Resumo de consulta](./media/stream-analytics-write-queries/6-stream-analytics-write-queries.png)  

## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [Fórum de análise de fluxo do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
