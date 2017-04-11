<properties
    pageTitle="Arquivo morto de Hubs de evento Azure | Microsoft Azure"
    description="Visão geral do recurso de arquivo morto de Hubs de evento do Azure."
    services="event-hubs"
    documentationCenter=""
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="darosa;sethm"/>

# <a name="azure-event-hubs-archive"></a>Arquivo morto de Hubs de evento Azure

Arquivo morto do Azure evento Hubs permite entregar automaticamente os dados de streaming em seu Hubs de evento para uma conta de armazenamento de Blob de sua escolha com flexibilidade adicional para especificar uma hora ou dimensionar o intervalo de sua escolha. Configuração de arquivamento é rápida, há não há custos administrativos para executá-lo e redimensiona automaticamente com seu evento Hubs [unidades de produtividade](event-hubs-overview.md#capacity-and-security). Arquivamento de Hubs de evento é a maneira mais fácil para carregar dados streaming em Azure e permite concentrar-se em processamento de dados em vez de captura de dados.

Arquivo morto do Azure evento Hubs permite canais baseado em lote e em tempo real no mesmo fluxo de processo. Isso permite criar soluções que podem crescer com suas necessidades ao longo do tempo. Se você está construindo sistemas baseados em lote hoje com prestar atenção em direção futuro processamento em tempo real, ou você deseja adicionar um caminho de frio eficiente para uma solução em tempo real existente, arquivamento de Hubs de evento torna o trabalho com os dados mais fáceis de streaming.

## <a name="how-event-hubs-archive-works"></a>Como funciona o arquivamento de Hubs de evento

Hubs de evento é um buffer durável de retenção de tempo para ingresso de telemetria, semelhante a um log distribuído. A chave para dimensionar no evento Hubs é o [modelo de consumidor particionada](event-hubs-overview.md#partition-key). Cada partição é um segmento independente de dados e é consumida independentemente. Ao longo do tempo esses dados expiram, com base no período de retenção configurável. Como resultado, um Hub de evento determinado nunca obtém "muito cheio".

Arquivo morto de Hubs de evento permite especificar sua própria conta de armazenamento de Blob do Azure e o contêiner que será usada para armazenar os dados arquivados. Essa conta pode ser na mesma região como seu Hub de evento ou em outra região, adicionando à flexibilidade do recurso de arquivo morto de Hubs de evento.

Dados arquivados são escritos em formato de [Apache Avro][] ; um formato binário, compacto rápido que fornece estruturas de dados sofisticados com esquema in-line. Este formato é amplamente usado no ecossistema do Hadoop, bem como a análise de fluxo e fábrica de dados do Azure. Obter mais informações sobre como trabalhar com Avro estão disponíveis neste artigo.

### <a name="archive-windowing"></a>Janelas de arquivamento

Arquivo morto de Hubs de evento permite que você configurar uma janela para controlar o arquivamento. Essa janela é um tamanho mínimo e a configuração de hora com uma "primeiro wins política," que significa que o primeiro disparador encontrado causará uma operação de arquivamento. Se você tiver uma janela de arquivamento 15-minute/100 MB e envia 1 MB/s, a janela de tamanho irá disparar antes da janela de tempo. Cada partição arquivos independentemente e grava um blob de bloco concluído no momento do arquivo, chamado pela vez quando o intervalo de arquivo morto foi encontrado. A convenção de nomenclatura é da seguinte maneira:

```
<Namespace>/<EventHub>/<Partition>/<YYYY>/<MM>/<DD>/<HH>/<mm>/<ss>
```

### <a name="scaling-to-throughput-units"></a>Dimensionamento para unidades de produtividade

O tráfego de Hubs de evento é controlado pelo [unidades de produtividade](event-hubs-overview.md#capacity-and-security). Uma unidade única produtividade permite 1 MB/s ou 1000 eventos por segundo de ingresso e duas vezes essa quantidade de saída. Hubs de evento padrão pode ser configurado com 1-20 unidades de produtividade e mais podem ser comprado por meio de um aumento de cota [de solicitação de suporte][]. Uso além de unidades de produtividade comprados é limitado. Arquivo morto de Hubs de evento copia dados diretamente do armazenamento de Hubs de evento interno, ignorando cotas de egresso de unidade de produtividade e salvando seu egresso para outros leitores de processamento como a análise de fluxo ou Spark.

Depois de configurado, arquivamento de Hubs de evento é executado automaticamente assim que você enviar seu primeiro evento. Ele continua sendo executado em todos os momentos. Para tornar mais fácil para seu processamento downstream para saber se o processo está funcionando, o evento Hubs grava arquivos vazios quando há nenhum dado. Isso fornece uma cadência previsível e marcador que pode alimentar seus processadores de lote.

## <a name="setting-up-event-hubs-archive"></a>Configuração de arquivamento de Hubs de evento

Arquivo morto de Hubs de evento podem ser configurado no momento da criação de Hub de eventos por meio do portal ou Gerenciador de recursos do Azure. Você simplesmente habilitar arquivamento clicando **no** botão. Configurar uma conta de armazenamento e o contêiner clicando em seção **contêiner** da lâmina. Como arquivamento de Hubs de evento usa autenticação de serviço para com armazenamento, você não precisa especificar uma cadeia de conexão de armazenamento. O seletor de recurso seleciona o URI do recurso para sua conta de armazenamento automaticamente. Se você usar o Gerenciador de recursos do Azure, você deve fornecer esse URI explicitamente como uma cadeia de caracteres.

A janela de tempo padrão é de cinco minutos. O valor mínimo é 1, o máximo 15. A janela de **tamanho** tem um intervalo de 10-500 MB.

![][1]

## <a name="adding-archive-to-an-existing-event-hub"></a>Adicionando arquivo morto a um Hub de evento existente

Arquivos mortos podem ser configurados em Hubs de eventos existentes que estão em um namespace Hubs de evento. O recurso não está disponível nos espaços de nome de tipo mais antigos de mensagens ou misto. Para habilitar o arquivamento em um Hub de evento existente ou alterar as configurações de arquivo morto, clique em seu namespace para carregar a lâmina **Essentials** , em seguida, clique no Hub do evento para o qual você deseja habilitar ou alterar a configuração de arquivamento. Por fim, clique na seção **Propriedades** da lâmina abrir conforme mostrado na figura a seguir.

![][2]

Você também pode configurar o arquivamento de Hubs de eventos por meio de modelos do Gerenciador de recursos do Azure. Para obter mais informações, consulte [Este artigo](event-hubs-resource-manager-namespace-event-hub-enable-archive.md).

## <a name="exploring-the-archive-and-working-with-avro"></a>Explorar o arquivamento e trabalhar com Avro

Depois de configurado, o evento Hubs arquivar cria arquivos na conta de armazenamento do Azure e contêiner fornecido na janela de tempo configurado. Você pode exibir esses arquivos em qualquer ferramenta como o [Gerenciador de armazenamento do Azure][]. Você pode baixar os arquivos localmente para trabalhar nelas.

Os arquivos produzidos pelo arquivamento de Hubs de evento têm o esquema de Avro a seguir:

![][3]

Uma maneira fácil de explorar arquivos de Avro é usando o jar [Avro ferramentas][] do Apache. Após o download neste jar, você pode ver o esquema de um arquivo de Avro específico, execute o seguinte comando:

```
java -jar avro-tools-1.8.1.jar getschema \<name of archive file\>
```

Esse comando retorna

```
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Você também pode usar ferramentas de Avro para converter o arquivo em formato JSON e executar outro processamento.

Para executar o processamento mais avançado, baixe e instale o Avro para sua escolha da plataforma. No momento da redação deste artigo, há implementações disponíveis para C, C++, C\#, NodeJS, Java, PHP, Perl, Python e Ruby.

Apache Avro tem completos guias de Introdução para [Java][] e [Python][]. Você também pode ler o artigo [Introdução ao arquivo morto Hubs de evento](event-hubs-archive-python.md) .

## <a name="how-event-hubs-archive-is-charged"></a>Como arquivamento de Hubs de evento é cobrado

Arquivo morto de Hubs de evento é limitado da mesma forma para unidades de produtividade, como uma taxa por hora. O encargo é diretamente proporcional ao número de unidades de produtividade compradas do namespace. Como as unidades de produtividade são aumentadas e diminuídas, arquivamento de Hubs de evento aumenta e diminui para fornecer desempenho correspondente. Os medidores acontecem juntamente. O encargo para arquivamento de Hubs de evento é US $0,10 por hora por unidade de produtividade, oferecida com um desconto de 50% durante o período de visualização.

Arquivamento de Hubs de evento realmente é a maneira mais fácil de obter dados para Azure. Usando Lucerne de dados do Azure, fábrica de dados do Azure e Azure HDInsight, você pode realizar processamento em lotes e outras análises de sua escolha usando ferramentas familiares e plataformas em qualquer escala necessárias.

## <a name="next-steps"></a>Próximas etapas

Você pode aprender mais sobre o evento Hubs visitando os links a seguir:

- Um [aplicativo de exemplo que usa o evento Hubs][]completo.
- A amostra de [escala processamento de eventos com Hubs de evento][] .
- [Visão geral de Hubs de evento][]

[Apache Avro]: http://avro.apache.org/
[solicitação de suporte]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[1]: ./media/event-hubs-archive-overview/event-hubs-archive1.png
[2]: media/event-hubs-archive-overview/event-hubs-archive2.png
[Explorador de armazenamento do Azure]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-archive-overview/event-hubs-archive3.png
[Ferramentas de Avro]: http://www-us.apache.org/dist/avro/avro-1.8.1/java/avro-tools-1.8.1.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Visão geral de Hubs de evento]: event-hubs-overview.md
[aplicativo de exemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Dimensionar o processamento de eventos com Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3