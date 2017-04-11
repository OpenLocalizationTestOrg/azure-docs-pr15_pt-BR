<properties 
    pageTitle="Introdução ao fluxo Analytics | Microsoft Azure" 
    description="Saiba mais sobre a análise de fluxo, um serviço gerenciado que ajuda a analisar dados de streaming de Internet das coisas (IoT) em tempo real." 
    keywords="análise como um serviço, gerenciado serviços, processamento de fluxo, streaming analytics, qual é a análise de fluxo"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>


# <a name="what-is-stream-analytics"></a>O que é a análise de fluxo?

Azure análise de fluxo é um mecanismo de processamento de eventos em tempo real totalmente gerenciado, econômica que ajuda a desbloquear ideias profunda de dados. Análise de fluxo torna mais fácil configurar cálculos analíticos em tempo real nos dados streaming de dispositivos, sensores, sites da web, mídias sociais, aplicativos, sistemas de infraestrutura e muito mais.

Com apenas alguns cliques no portal do Azure, você pode criar um trabalho de análise de fluxo, especificando a fonte de entrada de dados streaming, o receptor de saída para os resultados do seu trabalho, e uma transformação de dados expressa em uma linguagem SQL semelhante. Você pode monitorar e ajustar a escala/velocidade do seu trabalho no portal do Azure expandir de alguns quilobytes para um gigabyte ou mais dos eventos processados por segundo.

Análise de fluxo utiliza anos de trabalho do Microsoft Research desenvolver altamente ajustadas streaming mecanismos para processamento de temporais, bem como as integrações de idioma do intuitivas especificações de tal.

## <a name="what-can-i-use-stream-analytics-for"></a>O que posso usar a análise de fluxo de?
Grandes quantidades de dados estão fluindo em alta velocidade durante a transmissão hoje. Empresas que podem processar e atuam sobre esses fluxo dados em tempo real drasticamente podem melhorar a eficiência e se diferenciar no mercado. Cenários de análise de streaming em tempo real podem ser encontrados em todos os setores: análise real, personalizada, negociação de estoque e alertas oferecidas por empresas de serviços financeiros; detecção de fraude em tempo real; Serviços de proteção de dados e identidade; inclusão confiável e análise de dados gerados pelo sensores e atuadores incorporado em objetos físicos (Internet das coisas ou IoT); sequência de cliques do Web analytics; e aplicativos de CRM (gerenciamento) de relação de cliente emitir alertas quando a experiência do cliente em um período está degradada. Empresas que estão procurando a maneira mais flexível, confiável e econômica de fazer tal análise de dados em tempo real de fluxo de evento para o sucesso no mundo dos negócios modernos altamente competitiva.

## <a name="key-capabilities-and-benefits"></a>Principais recursos e benefícios
-   **Facilidade de uso:** Análise de fluxo é compatível com um modelo de consulta simples e declarativa para descrever as transformações. Para otimizar para facilidade de uso, a análise de fluxo usa uma variante de T-SQL e remove a necessidade de clientes lidar com complexidades técnicas de sistemas de processamento de fluxo. Usando a [linguagem de consulta de análise de fluxo](https://msdn.microsoft.com/library/azure/dn834998.aspx) no editor de consultas no navegador, você recebe IntelliSense sentido preenchimento automático para ajudá-lo a pode rapidamente e facilmente implementar consultas de série de tempo, incluindo junções baseado em temporal, agregações em janelas, filtros temporais e outras operações comuns como junções, agregados, projeções e filtros. Além disso, teste em relação a um arquivo de dados de exemplo de consulta de navegador permite desenvolvimento iterativo rápido.  

-   **Escalabilidade:** Análise de fluxo é capaz de lidar com a taxa de transferência de evento alta de até 1GB/segundo. Integração com [Hubs de evento do Azure](https://azure.microsoft.com/services/event-hubs/) e o [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/) permitem a solução para inclusão milhões de eventos por segundo vindo de dispositivos conectados, clickstreams e arquivos de log, para mencionar alguns. Para fazer isso, a análise de fluxo utiliza a funcionalidade de partição dos Hubs de evento, que pode gerar 1 MB/s por partição. Os usuários são capazes de partição a computação em um número de etapas lógicas dentro da definição de consulta, cada uma com a capacidade de serem mais particionado para aumentar a escalabilidade.  

-   **Confiabilidade, a capacidade de repetição e a recuperação rápida:** Um serviço gerenciado na nuvem, a análise de fluxo ajuda a evitar a perda de dados e fornece continuidade de negócios em caso de falhas por meio de recursos internos de recuperação. A capacidade para manter o estado internamente, o serviço fornece resultados repetitivo garantindo que é possível arquivar eventos e reaplicar o processamento no futuro, sempre obtendo os mesmos resultados. Isso permite aos clientes voltar no tempo e investigar cálculos ao fazer a análise de causas raiz, hipóteses, etc.  

-   **Baixo custo:** Como um serviço de nuvem, a análise de fluxo é otimizada para fornecer aos usuários um custo muito baixo para comece a usar e manter soluções de análise em tempo real. O serviço é criado para você pagar enquanto você trabalha com base no uso de Streaming unidade e a quantidade de dados processados pelo sistema. Uso é derivado com base no volume de eventos processados e a quantidade de energia de computação provisionada dentro do cluster para lidar com os respectivos trabalhos de análise de fluxo.  

-   **Fazem referência aos dados:** Análise de fluxo fornece aos usuários a capacidade de especificar e usar dados de referência. Isso pode ser históricos dados ou simplesmente não streaming que muda com menos frequência ao longo do tempo. O sistema simplifica o uso de dados de referência para ser tratado como qualquer outro fluxo evento entrada para ingressar com outros fluxos de evento incluídos em tempo real para executar transformações.  

-   **Funções definidas pelo usuário:** Análise de fluxo tem integração com o aprendizado de máquina do Azure para definir chamadas de função no serviço de aprendizado de máquina como parte de uma consulta de análise do fluxo. Isso expande os recursos de análise de fluxo para aproveitar as soluções existentes de aprendizado de máquina do Azure. Para obter mais informações sobre isso, consulte o [tutorial de integração de aprendizado de máquina](stream-analytics-machine-learning-integration-tutorial.md).

-   **Conectividade:** Análise de fluxo conecta-se diretamente aos Hubs de evento do Azure e Azure IoT Hubs para inclusão de fluxo e o serviço do Azure Blob para inclusão dados históricos. Os resultados podem ser escritos de análise de fluxo Blobs de armazenamento do Azure ou tabelas, DB do SQL Azure, armazenamentos de Lucerne de dados do Azure, DocumentDB, Hubs de evento, tópicos de barramento de serviço do Azure ou filas e Power BI, onde ele pode ser visualizado, ainda mais processado pelos fluxos de trabalho, usado na análise de lote via [Azurehdinsight](https://azure.microsoft.com/services/hdinsight/) ou processado novamente como uma série de eventos. Ao usar o evento Hubs é possível compor a análise de fluxo de vários junto com outros mecanismos de processamento e fontes de dados sem perder a natureza streaming os cálculos.  

## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [Fórum de análise de fluxo do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
Você já foram introduzidas análise de fluxo, um serviço gerenciado do streaming a análise de dados da Internet das coisas. Para saber mais sobre este serviço, consulte:

- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

