<properties
    pageTitle="Conexão de dados: entradas de um fluxo de evento de fluxo de dados | Microsoft Azure"
    description="Saiba mais sobre como configurar uma conexão de dados para a análise de fluxo chamado 'entradas'. Entradas incluem um fluxo de dados de eventos e também fazem referência aos dados."
    keywords="fluxo de dados, o conexão de dados, o fluxo de evento"
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

# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>Conexão de dados: conheça entradas de fluxo de eventos para a análise de fluxo de dados

A conexão de dados para a análise de fluxo é um fluxo de dados de eventos de uma fonte de dados. Isso se chama "entrada". Análise de fluxo tem integração de primeira classe com o Azure fluxo fontes Hub de evento, IoT Hub e Blob armazenamento de dados que pode ser da assinatura Azure iguais ou diferente como seu trabalho de análise.

## <a name="data-input-types-data-stream-and-reference-data"></a>Tipos de entrada de dados: dados de referência e fluxo de dados
Como os dados são enviados para uma fonte de dados, ele é consumido pelo trabalho de análise de fluxo e processado em tempo real. Entradas são divididas em dois tipos distintos: entradas de fluxo de dados e entradas de dados de referência.

### <a name="data-stream-inputs"></a>Entradas de fluxo de dados
Um fluxo de dados é ilimitada sequência de eventos chegando ao longo do tempo. Trabalhos de análise de fluxo devem incluir dados pelo menos uma entrada de fluxo para ser consumidas e transformados pelo trabalho. Armazenamento de blob, Hubs de evento e IoT Hubs têm suporte como fontes de entrada de fluxo de dados. Hubs de evento são usados para coletar fluxos de eventos de vários dispositivos e serviços, como feeds de atividades de mídia social, informações de ações comercial ou dados de sensores. IoT Hubs são otimizados para coletar dados de dispositivos conectados em cenários de Internet das coisas (IoT).  Armazenamento de blob pode ser usado como uma fonte de entrada para a inclusão de grandes volumes de dados como um fluxo.  

### <a name="reference-data"></a>Dados de referência
Análise de fluxo suporta um segundo tipo de entrada conhecido como dados de referência. Isso é auxiliares dados que é estático ou lentamente mudanças ao longo do tempo e normalmente são usados para realizar correlação e pesquisas. Armazenamento de Blob do Azure atualmente é a fonte de entrada com suporte apenas para os dados de referência. Blobs de fonte de dados de referência são limitadas a 100MB no tamanho.
Para saber como criar entradas de dados de referência, consulte [Dados de referência de uso](stream-analytics-use-reference-data.md)  

## <a name="create-a-data-stream-input-with-an-event-hub"></a>Criar uma entrada de fluxo de dados com um Hub de evento

[Hubs de evento do Azure](https://azure.microsoft.com/services/event-hubs/) são altamente escaláveis publicação assinatura ingestor de evento. Ele pode coletar milhões de eventos por segundo, para que você possa processar e analisar grandes quantidades de dados produzidos por seus aplicativos e os dispositivos conectados. É uma das entradas da mais comumente usadas para análise de fluxo. Hubs de evento e a análise de fluxo juntos fornecem aos clientes uma solução completa para a análise de tempo real. Evento Hubs permitem aos clientes alimentar eventos Azure em tempo real e trabalhos de análise de fluxo podem processá-las em tempo real. Por exemplo, os clientes podem enviar web cliques, leituras sensor, eventos de log on-line para Hubs de evento e criar trabalhos de análise de fluxo para usar Hubs de evento como os fluxos de entrada de dados em tempo real filtragem, agregar e correlação.

É importante observar que o carimbo de hora padrão dos eventos provenientes Hubs de evento na análise de fluxo é o carimbo de hora que o evento chegou no Hub de evento que é EventEnqueuedUtcTime. Para processar os dados como um fluxo usando um carimbo de hora na carga do evento, a palavra-chave [Carimbo de hora por](https://msdn.microsoft.com/library/azure/dn834998.aspx) deve ser usada.

### <a name="consumer-groups"></a>Grupos do consumidor

Cada entrada de Hub de evento de análise de fluxo deve ser configurada para que o seu próprio grupo de consumidor. Quando um trabalho contém uma associação a mesmo ou várias entradas, alguma entrada pode ser lidos por mais de um leitor de fluxo contínuo, que afeta o número de leitores em um grupo único consumidor. Para evitar exceder o limite de evento Hub de 5 leitores por grupo consumidor por partição, é uma prática recomendada para designar um grupo de consumidor para cada trabalho de análise de fluxo. Observe que também há um limite de 20 grupos de consumidor por Hub de evento. Para obter detalhes, consulte o [Guia de programação de Hubs de evento](../event-hubs/event-hubs-programming-guide.md).

### <a name="configure-event-hub-as-an-input-data-stream"></a>Configurar o Hub de evento como um fluxo de dados de entrada

A tabela a seguir explica cada propriedade no guia com sua descrição de entrada do Hub de eventos:

| NOME DA PROPRIEDADE | DESCRIÇÃO |
|------|------|
| Alias de entrada | Um nome amigável que será usado na consulta trabalho para fazer referência a essa entrada |
| Namespace do barramento de serviço | Um namespace barramento de serviço é um contêiner para um conjunto de entidades de mensagens. Quando você criou um novo Hub de evento, você também criou um namespace barramento de serviço. |
| Hub de evento | O nome do seu Hub de eventos de entrada. |
| Nome de política do Hub de evento | A política de acesso compartilhado, que pode ser criada na guia evento Hub configurar. Cada política de acesso compartilhado terá um nome, permissões que você definiu, e as teclas de acesso. |
| Chave de política de Hub de evento | A chave de acesso compartilhado usada para autenticar o acesso ao namespace barramento de serviço. |
| Grupo de consumidor de Hub de evento (opcional) | O grupo de consumidor a inclusão de dados do Hub evento. Se não especificado, trabalhos de análise de fluxo usará o grupo de consumidor padrão para incluir dados de Hub do evento. É recomendável usar um grupo de consumidor distinto para cada tarefa de fluxo de análise. |
| Formato de serialização de evento | Para certificar-se de que suas consultas funcionam da forma esperada, a análise de fluxo precisa saber qual formato de serialização (JSON, CSV ou Avro), você está usando para fluxos de dados de entrada. |
| Codificação | UTF-8 é o único formato de codificação com suporte no momento. |

Quando seus dados é proveniente de uma fonte de Hub de evento, você pode acessar para alguns campos de metadados em sua consulta de análise do fluxo. A tabela a seguir lista os campos e sua descrição.

| PROPRIEDADE | DESCRIÇÃO |
|------|------|
| EventProcessedUtcTime | A data e a hora em que o evento foi processado pelo fluxo Analytics. |
| EventEnqueuedUtcTime | A data e a hora em que o evento foi recebido pelo Hubs de evento. |
| PartitionId | O ID de partição com base em zero para o adaptador de entrada. |

Por exemplo, você pode escrever uma consulta como o seguinte:

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

## <a name="create-an-iot-hub-data-stream-input"></a>Criar uma entrada de fluxo de dados IoT Hub

Hub de Iot Azure é altamente escaláveis publicação assinatura ingestor de evento otimizado para IoT cenários.
É importante observar que o carimbo de hora padrão dos eventos provenientes IoT Hubs na análise de fluxo é o carimbo de hora que o evento chegou no IoT Hub que é EventEnqueuedUtcTime. Para processar os dados como um fluxo usando um carimbo de hora na carga do evento, a palavra-chave [Carimbo de hora por](https://msdn.microsoft.com/library/azure/dn834998.aspx) deve ser usada.

> [AZURE.NOTE] Apenas as mensagens enviadas com uma propriedade de DeviceClient podem ser processadas.

### <a name="consumer-groups"></a>Grupos do consumidor

Cada entrada de fluxo Analytics IoT Hub deve ser configurada para que o seu próprio grupo de consumidor. Quando um trabalho contém uma associação a mesmo ou várias entradas, alguma entrada pode ser lidos por mais de um leitor de fluxo contínuo, que afeta o número de leitores em um grupo único consumidor. Para evitar exceder o limite de IoT Hub de 5 leitores por grupo consumidor por partição, é uma prática recomendada para designar um grupo de consumidor para cada trabalho de análise de fluxo.

### <a name="configure-iot-hub-as-an-data-stream-input"></a>Configurar o Hub de IoT como um fluxo de dados de entrada

A tabela a seguir explica cada propriedade na guia entrada IoT Hub com sua descrição:

| NOME DA PROPRIEDADE | DESCRIÇÃO |
|------|------|
| Alias de entrada | Um nome amigável que será usado na consulta trabalho para fazer referência a essa entrada. |
| Hub de IoT | Um IoT Hub é um contêiner para um conjunto de entidades mensagens. |
| Ponto de extremidade | O nome do seu ponto de extremidade do IoT Hub. |
| Nome de política de acesso compartilhado | A política de acesso compartilhado dar acesso ao IoT Hub. Cada política de acesso compartilhado terá um nome, permissões que você definiu, e as teclas de acesso. |
| Chave de política de acesso compartilhado | A chave de acesso compartilhado usada para autenticar o acesso ao IoT Hub. |
| Grupo de consumidor (opcional) | O grupo de consumidor a inclusão de dados do IoT Hub. Se não especificado, trabalhos de análise de fluxo usará o grupo de consumidor padrão para incluir dados do IoT Hub. É recomendável usar um grupo de consumidor distinto para cada tarefa de fluxo de análise. |
| Formato de serialização de evento | Para certificar-se de que suas consultas funcionam da forma esperada, a análise de fluxo precisa saber qual formato de serialização (JSON, CSV ou Avro), você está usando para fluxos de dados de entrada. |
| Codificação | UTF-8 é o único formato de codificação com suporte no momento. |

Quando seus dados é proveniente de uma fonte de IoT Hub, você pode acessar para alguns campos de metadados em sua consulta de análise do fluxo. A tabela a seguir lista os campos e sua descrição.

| PROPRIEDADE | DESCRIÇÃO |
|------|------|
| EventProcessedUtcTime | A data e a hora em que o evento foi processado. |
| EventEnqueuedUtcTime | A data e a hora em que o evento foi recebido pelo IoT Hub. |
| PartitionId | O ID de partição com base em zero para o adaptador de entrada. |
| IoTHub.MessageId | Usado para comunicação bidirecional no IoT Hub de correlação. |
| IoTHub.CorrelationId | Usada em comentários no IoT Hub e respostas a mensagens. |
| IoTHub.ConnectionDeviceId | A identificação autenticada usada para enviar a mensagem, carimbo de data / em mensagens de servicebound pelo IoT Hub. |
| IoTHub.ConnectionDeviceGenerationId | O generationId do dispositivo autenticado usado para enviar essa mensagem, carimbo de data / em mensagens de servicebound pelo IoT Hub. |
| IoTHub.EnqueuedTime | Quando a mensagem foi recebida pelo IoT Hub de tempo. |
| IoTHub.StreamId | Propriedade de evento personalizado adicionada pelo dispositivo remetente. |

## <a name="create-a-blob-storage-data-stream-input"></a>Criar uma entrada de fluxo de dados de armazenamento de Blob

Cenários com grandes quantidades de dados não estruturados para armazenar na nuvem, o armazenamento de Blob oferece uma solução econômica e escalável. Dados de [armazenamento de Blob](https://azure.microsoft.com/services/storage/blobs/) geralmente são considerados dados "inativos", mas ela pode ser processada como um fluxo de dados pela análise de fluxo. Um cenário comum para entradas de armazenamento de Blob com a análise de fluxo é o processamento de log, onde telemetria é capturada de um sistema e precisa ser analisados e processados para extrair dados significativos.

É importante observar que o carimbo de hora padrão dos eventos de armazenamento de Blob em análise de fluxo é o carimbo de hora que o blob última modificação quais *isBlobLastModifiedUtcTime*. Para processar os dados como um fluxo usando um carimbo de hora na carga do evento, a palavra-chave [Carimbo de hora por](https://msdn.microsoft.com/library/azure/dn834998.aspx) deve ser usada.

Observe também que CSV formatado entradas **exigir** uma linha de cabeçalho para definir campos para o conjunto de dados. Mais campos de linha de cabeçalho devem ser **exclusivo**.

> [AZURE.NOTE] Análise de fluxo não oferece suporte para adicionar conteúdo a um blob existente. Análise de fluxo exibirá um blob apenas uma vez e qualquer alteração feita depois este leitura não será processada. A prática recomendada é carregar todos os dados de uma vez e não adicionar todos os eventos adicionais para o armazenamento de blob.

A tabela a seguir explica cada propriedade na guia entrada de armazenamento do Blob com sua descrição:

<table>
<tbody>
<tr>
<td>NOME DA PROPRIEDADE</td>
<td>DESCRIÇÃO</td>
</tr>
<tr>
<td>Alias de entrada</td>
<td>Um nome amigável que será usado na consulta trabalho para fazer referência a essa entrada.</td>
</tr>
<tr>
<td>Conta de armazenamento</td>
<td>O nome da conta de armazenamento onde estão localizados seus arquivos de blob.</td>
</tr>
<tr>
<td>Chave da conta de armazenamento</td>
<td>A chave secreta associada à conta de armazenamento.</td>
</tr>
<tr>
<td>Contêiner de armazenamento
</td>
<td>Os contêineres fornecem um agrupamento lógico para blobs armazenados no serviço do Microsoft Azure Blob. Quando você carrega um blob serviço Blob, você deve especificar um contêiner de blob.</td>
</tr>
<tr>
<td>Padrão de prefixo do caminho [opcional]</td>
<td>O caminho de arquivo usado para localizar seu blobs dentro do contêiner especificado.
Dentro do caminho, você pode optar por especificar instâncias de uma ou mais das seguintes 3 variáveis:<BR>{date}, {hora,}<BR>{partição}<BR>Exemplo 1: cluster1/logs / {date} / {tempo} / {partição}<BR>Exemplo 2: cluster1/logs / {date}<P>Observe que "*" não é um valor permitido para pathprefix. Apenas válidos <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">caracteres de blob do Microsoft Azure</a> são permitidos.</td>
</tr>
<tr>
<td>Formato de data [opcional]</td>
<td>Se o token de data é usado no caminho do prefixo, você pode selecionar o formato de data em que os arquivos são organizados. Exemplo: DD/MM/AAAA</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Se o token de tempo é usado no caminho do prefixo, especifique o formato de hora em que os arquivos são organizados. Atualmente o único valor com suporte é HH.</td>
</tr>
<tr>
<td>Formato de serialização de evento</td>
<td>Para certificar-se de que suas consultas funcionam da forma esperada, a análise de fluxo precisa saber qual formato de serialização (JSON, CSV ou Avro), você está usando para fluxos de dados de entrada.</td>
</tr>
<tr>
<td>Codificação</td>
<td>Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Análise de fluxo oferece suporte a um número de delimitadores comuns para dados serializados no formato CSV. Valores com suporte são vírgula, ponto e vírgula, espaço, tabulação e barra vertical.</td>
</tr>
</tbody>
</table>

Quando seus dados é proveniente de uma fonte de armazenamento de Blob, você pode acessar alguns campos de metadados em sua consulta de análise do fluxo. A tabela a seguir lista os campos e sua descrição.

| PROPRIEDADE | DESCRIÇÃO |
|------|------|
| BlobName | O nome do blob entrado que o evento veio. |
| EventProcessedUtcTime | A data e a hora em que o evento foi processado pelo fluxo Analytics. |
| BlobLastModifiedUtcTime | A data e a hora em que o blob foi modificado pela última vez. |
| PartitionId | O ID de partição com base em zero para o adaptador de entrada. |

Por exemplo, você pode escrever uma consulta como o seguinte:

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````


## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [Fórum de análise de fluxo do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
Você aprendeu sobre as opções de conexão de dados no Azure para seus trabalhos de análise de fluxo. Para saber mais sobre a análise de fluxo, consulte:

- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
