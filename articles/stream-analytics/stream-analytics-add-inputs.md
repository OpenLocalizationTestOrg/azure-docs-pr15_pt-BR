<properties
    pageTitle="Adicionar uma entrada de dados para os trabalhos de análise de fluxo | Microsoft Azure"
    description="Saiba como conectar uma fonte de dados para o seu trabalho de análise de fluxo como streaming a entrada de dados de evento Hubs ou referência de dados do armazenamento de Blog."
    keywords="entrada de dados, fluxo de dados"
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


# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Adicionar um fluxo de entrada ou referência de dados a um trabalho de análise de fluxo

Saiba como conectar uma fonte de dados para o seu trabalho de análise de fluxo como streaming a entrada de dados de evento Hubs ou referência de dados do armazenamento de Blob.

Azure trabalhos de análise de fluxo podem ser conectados a entrada de dados de um ou mais, cada um deles definir uma conexão a uma fonte de dados existente. Como os dados são enviados à fonte de dados, ele é consumido pelo trabalho de análise de fluxo e processado em tempo real como fluxo de dados. Análise de fluxo tem integração de primeira classe com [Hubs de evento do Azure](https://azure.microsoft.com/services/event-hubs/) e [armazenamento de Blob do Azure](../storage/storage-dotnet-how-to-use-blobs.md) dentro e fora da assinatura do trabalho.

Este artigo é uma etapa no [caminho de aprendizagem de análise de fluxo](/documentation/learning-paths/stream-analytics/).

## <a name="data-input-streaming-data-and-reference-data"></a>Entrada de dados: fluxo de dados e dados de referência

Existem dois tipos diferentes de entradas na análise de fluxo: fluxos de dados e dados de referência.

- **Fluxos de dados**: trabalhos de análise de fluxo devem incluir dados pelo menos uma entrada de fluxo para ser consumidas e transformados pelo trabalho. Azure armazenamento de Blob e Hubs de evento do Azure são suportados como fontes de entrada de fluxo de dados. Azure Hubs de evento são usados para coletar fluxos de eventos de dispositivos conectados, serviços e aplicativos. Armazenamento de Blob do Azure pode ser usado como uma fonte de entrada para a inclusão de grandes volumes de dados como um fluxo.  
- **Dados de referência**: um segundo tipo de dados de referência de chamada de entrada auxiliar ofereça suporte a análise de fluxo.  Em vez de dados em movimento, esses dados são estático ou diminuindo a alteração.  Normalmente, ele é usado para realizar pesquisas e correlações com fluxos de dados para criar um conjunto de dados mais elaborado.  Armazenamento de Blob do Azure atualmente é a fonte de entrada com suporte apenas para os dados de referência.  

Para adicionar uma entrada para o trabalho de análise de fluxo:

1. No portal do Azure clique **entradas** e clique em **Adicionar uma entrada** no seu trabalho de análise de fluxo.

    ![Portal clássico Azure - adicionar uma entrada.](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  

    No portal do Azure clique no bloco de **entradas** em seu trabalho de análise de fluxo.  

    ![Portal Azure - adicionar a entrada de dados.](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  

2. Especificar o tipo de entrada: **fluxo de dados** ou **dados de referência**.

    ![Adicionar os dados corretos de entrada, transmitida ou referência](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  

    ![Adicionar os dados corretos de entrada, transmitida ou referência](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  

3. Se criar uma entrada de fluxo de dados, especifique o tipo de fonte para a entrada.  Esta etapa pode ser ignorada durante a criação de dados de referência como somente Blob storage é suportado no momento.

    ![Adicionar a entrada de dados de fluxo de dados](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  

    ![Adicionar a entrada de dados de fluxo de dados](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  

4. Forneça um nome amigável para essa entrada na caixa de entrada Alias.  Esse nome será usado na consulta do seu trabalho posteriormente para fazer referência à entrada.

    Preencha o restante das propriedades de conexão necessária para conectar-se à fonte de dados. Esses campos variam por tipo de entrada e origem e são definidos em detalhes [aqui](stream-analytics-create-a-job.md).  

    ![Adicionar a entrada de dados do hub de evento](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  

5. Especifica as configurações de serialização para os dados de entrada:
    - Para garantir que suas consultas funcionam da forma esperada, especifique o **Formato de serialização do evento** de dados de entrada.  Formatos de serialização compatível são JSON, CSV e Avro.
    - Verifique se a **codificação** para os dados.  UTF-8 é o único formato de codificação com suporte no momento.

    ![Configurações de serialização de dados para as entrada de dados](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  

    ![Configurações de serialização de dados para as entrada de dados](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  

6. Depois de concluir a criação de entrada, análise de fluxo verificará que ela pode se conectar à fonte de entrada.  Você pode exibir o status da operação de Conexão de teste no hub de notificação.

    ![Testar a conexão de dados streaming de entrada](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  

    ![Testar a conexão de dados streaming de entrada](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>Obter ajuda com streaming entradas de dados
Para obter assistência, tente nosso [Fórum de análise de fluxo do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
