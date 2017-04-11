<properties 
    pageTitle="Como configurar dados saídas para trabalhos de análise de fluxo | Microsoft Azure" 
    description="Configurar saídas para trabalhos de análise de fluxo | segmento de caminho de aprendizagem."
    keywords="dados de saída, movimentação de dados"
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

# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>Como configurar dados saídas para trabalhos de análise de fluxo

Azure trabalhos de análise de fluxo podem ser conectados a um ou mais saídas de dados, que definem uma conexão para um receptor de dados existente. Como o trabalho de análise de fluxo processa e transforma os dados de entrada, um fluxo de eventos de saída de dados é gravado saída do seu trabalho.

Dados de análise de fluxo saídas podem ser usadas para painéis em tempo real ou alertas, origem disparar fluxos de trabalho de movimentação de dados, ou simplesmente arquivar dados para processamento em lotes posteriormente. Análise de fluxo tem integração de primeira classe com vários serviços Azure, que estão documentadas em detalhes aqui.

Para adicionar uma saída ao seu trabalho de análise de fluxo:

1. No Portal do Azure clássico, clique em **saídas** e clique em **Adicionar saída** em seu trabalho de análise de fluxo.

    ![Adicionar saídas](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  

    No portal do Azure clique no bloco de **saídas** no seu trabalho de análise de fluxo.

    ![Porta Azure adicionar saídas](./media/stream-analytics-add-outputs/5-stream-analytics-add-outputs.png)

2. Especifique o tipo de saída:

    ![Escolha o tipo de movimentação de dados](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  

    ![Portal do Azure escolha o tipo de movimentação de dados](./media/stream-analytics-add-outputs/6-stream-analytics-add-outputs.png)

3. Forneça um nome amigável para essa saída na caixa **Alias de saída** . Esse nome pode ser usado na consulta do seu trabalho posteriormente para consultar a saída.  
    
    Preencha o restante das propriedades conexão necessários para se conectar à sua saída.  Esses campos variam por tipo de saída e são definidos em detalhes aqui.  

    ![Adicionar propriedades de saída de dados](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)  

4. Dependendo do tipo de saída, talvez você precise especificar como os dados são serializados ou formatados. As configurações de serialização específicas para cada tipo de saída estão documentadas aqui.

    Preencha o restante das propriedades de conexão necessária para conectar-se à fonte de dados. Esses campos variam por tipo de entrada e origem e são definidos em detalhes [aqui](stream-analytics-create-a-job.md).  

    ![Adicionar dados saída ao hub de evento](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)  

    ![Saída de dados de portal Azure hub de evento](./media/stream-analytics-add-outputs/7-stream-analytics-add-outputs.png)  

> [Azure.Note] Qualquer elemento de saída adicionado ao trabalho, deve existir antes do trabalho é iniciado e eventos iniciar fluindo. Por exemplo, se você usar o armazenamento de Blob como uma saída, o trabalho não criará uma conta de armazenamento automaticamente. Ele precisa ser criados pelo usuário antes do trabalho ASA é iniciado.

## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [Fórum de análise de fluxo do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
