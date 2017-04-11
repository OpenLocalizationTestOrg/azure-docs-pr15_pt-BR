<properties 
   pageTitle="Cenários de dados envolvendo dados Lucerne Store | Microsoft Azure" 
   description="Compreender os cenários diferentes e ferramentas usando os dados que podem incluídos, processada, baixadas e visualizadas em uma loja de Lucerne de dados" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/06/2016"
   ms.author="nitinme"/>

# <a name="using-azure-data-lake-store-for-big-data-requirements"></a>Usando o armazenamento de Lucerne de dados do Azure para requisitos de grande volume

Há quatro etapas principais no grande processamento de dados:

* A inclusão de grandes quantidades de dados em um armazenamento de dados, em tempo real ou em lotes
* Os dados de processamento
* Baixar os dados
* Visualizando os dados

Neste artigo, vamos examinar esses estágios relacionadas com armazenamento de Lucerne de dados do Azure para entender as opções e ferramentas disponíveis para atender às suas necessidades de grande volume.

## <a name="ingest-data-into-data-lake-store"></a>Incluir dados no repositório de Lucerne de dados

Esta seção realça as diferentes fontes de dados e as diferentes maneiras nas quais esses dados podem ser incluídos para uma conta de armazenamento de Lucerne de dados.

![Dados de recebimento no repositório de Lucerne de dados] (./media/data-lake-store-data-scenarios/ingest-data.png "Dados de recebimento no repositório de Lucerne de dados")

### <a name="ad-hoc-data"></a>Dados ad hoc

Isso representa os conjuntos de dados menores que são usados para criação de protótipo um aplicativo de dados grande. Há diferentes maneiras de assimilar dados ad hoc dependendo da fonte de dados.

| Fonte de dados        | Inclusão usando                                                                        |
|--------------------|----------------------------------------------------------------------------------------|
| Computador local     | <ul> <li>[Portal do Azure](/data-lake-store-get-started-portal.md)</li> <li>[PowerShell Azure](data-lake-store-get-started-powershell.md)</li> <li>[Azure CLI entre plataformas](data-lake-store-get-started-cli.md)</li> <li>[Usando ferramentas de Lucerne de dados para o Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md#upload-source-data-files) </li></ul> |
| Armazenamento do Azure Blob | <ul> <li>[Fábrica de dados do Azure](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)</li> <li>[Ferramenta de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp em execução em HDInsight cluster](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

 
### <a name="streamed-data"></a>Dados em fluxo

Isso representa dados que podem ser gerados por várias fontes como aplicativos, dispositivos, sensores, etc. Esses dados podem ser incluídos em uma loja de Lucerne dados por ferramentas variedade. Essas ferramentas geralmente irão capturar e processar os dados em uma base por evento em tempo real e então escreva os eventos em lotes no repositório de Lucerne de dados para que possa ser processadas ainda mais. 

A seguir é ferramentas que você pode usar:
 
* [Fluxo azure Analytics] (.. / fluxo-analytics-dados-Lucerne-saída) - eventos incluídos em Hubs de evento podem ser escritos para Lucerne de dados do Azure usando uma saída de armazenamento de Lucerne de dados do Azure.
* [Azure HDInsight tempestade](../hdinsight/hdinsight-storm-write-data-lake-store.md) - você pode gravar dados diretamente dados Lucerne armazenamento do cluster tempestade.
* [EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost) – você pode receber eventos de Hubs de evento e, em seguida, gravar dados Lucerne repositório usando o [SDK do .NET dados Lucerne Store](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Dados relacionais

Você também pode fonte de dados de bancos de dados relacionais. Por um período de tempo, bancos de dados relacionais coletam grandes quantidades de dados que podem fornecer a obtenção de informações de chave se processadas por meio de um pipeline de dados grande. Você pode usar as seguintes ferramentas para mover esses dados para o armazenamento de Lucerne de dados.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Fábrica de dados do Azure](../data-factory/data-factory-data-movement-activities.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dados de log do servidor Web (carregar usando aplicativos personalizados)

Esse tipo de conjunto de dados é chamado especificamente check-out, porque a análise dos dados de log do servidor web é um caso de uso comum para aplicativos de grande volume e requer grandes volumes de arquivos de log para ser carregado o armazenamento de Lucerne de dados. Você pode usar qualquer uma das seguintes ferramentas para criar seus próprios scripts ou aplicativos para carregar esses dados.

* [Azure CLI entre plataformas](data-lake-store-get-started-cli.md)
* [PowerShell Azure](data-lake-store-get-started-powershell.md)
* [Armazenamento de Lucerne de dados do Azure .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Fábrica de dados do Azure](../data-factory/data-factory-data-movement-activities.md)

Para carregar dados de log de servidor de web e também para carregar outros tipos de dados (por exemplo, dados de opiniões social), é uma boa abordagem para escrever seus próprios scripts/aplicativos personalizados, pois ele oferece a flexibilidade incluir dados carregando componente como parte do seu aplicativo de grande volume maior. Em alguns casos este código pode levar a forma de um script ou o utilitário de linha de comando simples. Em outros casos, o código pode ser usado para integrar grande processamento de dados em um aplicativo de negócios ou solução.


### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dados associados clusters Azure HDInsight

A maioria dos tipos de cluster HDInsight (Hadoop, HBase, tempestade) dá suporte a dados Lucerne Store como um repositório de armazenamento de dados. HDInsight clusters acessam dados do Azure armazenamento Blobs (WASB). Para obter melhor desempenho, você pode copiar os dados de WASB para uma conta de armazenamento de Lucerne de dados associada ao cluster. Você pode usar as ferramentas a seguir para copiar os dados.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Serviço de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Fábrica de dados do Azure](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)

### <a name="data-stored-in-on-premise-or-iaas-hadoop-clusters"></a>Os dados armazenados no local ou IaaS Hadoop clusters

Grandes quantidades de dados podem ser armazenadas em clusters existentes do Hadoop, localmente em máquinas usando HDFS. Os clusters Hadoop podem estar em uma implantação local ou podem estar em um cluster de IaaS no Azure. Pode haver requisitos para copiar esses dados para armazenamento de Lucerne de dados do Azure para uma abordagem individual ou de forma recorrente. Há várias opções que você pode usar para fazer isso. Abaixo está uma lista de alternativas e as compensações associadas.

| Abordagem  | Detalhes | Vantagens   | Considerações  |
|-----------|---------|--------------|-----------------|
| Usar fábrica de dados do Azure (AAD) para copiar dados diretamente do Hadoop clusters para armazenamento de Lucerne de dados do Azure | [AAD suporta HDFS como uma fonte de dados](../data-factory/data-factory-hdfs-connector.md) | AAD fornece suporte de-de-prontos para HDFS e gerenciamento de primeira classe ponta a ponta e monitoramento | Requer o Gateway de gerenciamento de dados para ser implantado no local ou no cluster IaaS |
| Exporte dados do Hadoop como arquivos. Copie os arquivos para armazenamento de Lucerne de dados do Azure usando o mecanismo apropriado.                                   | Você pode copiar arquivos para armazenamento de Lucerne de dados do Azure usando: <ul><li>[PowerShell Azure para o sistema operacional Windows](data-lake-store-get-started-powershell.md)</li><li>[Azure entre plataformas CLI para não - sistema operacional Windows](data-lake-store-get-started-cli.md)</li><li>Aplicativo personalizado usando qualquer Data Lucerne Store SDK</li></ul> | Rápido começar. Pode fazer uploads personalizadas                                                   | Processo de várias etapa que envolve várias tecnologias. Gerenciamento e monitoramento aumentará até ser um desafio ao longo do tempo, considerando a natureza personalizada das ferramentas |
| Use Distcp para copiar dados de Hadoop ao armazenamento do Azure. Copie dados do armazenamento do Azure para armazenamento de Lucerne de dados usando o mecanismo apropriado. | Você pode copiar dados de armazenamento do Azure para usar o armazenamento de Lucerne de dados: <ul><li>[Fábrica de dados do Azure](../data-factory/data-factory-data-movement-activities.md)</li><li>[Ferramenta de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache DistCp executados em clusters de HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul>| Você pode usar ferramentas de código-fonte aberto. | Processo de várias etapa que envolve várias tecnologias |

### <a name="really-large-datasets"></a>Realmente grandes conjuntos de dados

Para carregar conjuntos de dados que variam em vários terabytes, usando os métodos descritos acima, às vezes, possível lenta e cara. Nesses casos, você pode usar as opções abaixo.

* **Usando a rota expressa do Azure**. Rota expressa do Azure permite que você crie conexões privadas entre dos data centers do Azure e infraestrutura em seu local. Isso fornece uma opção confiável para transferir grandes quantidades de dados. Para obter mais informações, consulte a [documentação de rota expressa do Azure](../expressroute/expressroute-introduction.md).


* **"Offline" carregar dos dados**. Se usar rota expressa do Azure não for possível por algum motivo, você pode usar o [serviço de importação/exportação do Azure](../storage/storage-import-export-service.md) entregar unidades de disco rígido com seus dados a um centro de dados do Azure. Primeiro, os seus dados são carregados para Blobs de armazenamento do Azure. Depois, você pode usar o [Azure dados fábrica](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store) ou [na ferramenta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) para copiar dados de Blobs de armazenamento do Azure para armazenamento de Lucerne de dados.

    >[AZURE.NOTE] Enquanto usando o serviço de importação/exportação, os tamanhos de arquivo nos discos que você enviar ao Azure data center não deve ser maior que 200 GB.


## <a name="process-data-stored-in-data-lake-store"></a>Processar dados armazenados no repositório de Lucerne de dados

Depois que os dados estão disponíveis no repositório de Lucerne de dados, você pode executar análise de dados usando os aplicativos de grandes de dados com suporte. Atualmente, você pode usar Azurehdinsight e análises de Lucerne de dados do Azure para executar trabalhos de análise de dados nos dados armazenados no repositório de Lucerne de dados. 

![Analisar dados no repositório de Lucerne de dados] (./media/data-lake-store-data-scenarios/analyze-data.png "Analisar dados no repositório de Lucerne de dados")

Você pode examinar os exemplos a seguir.

* [Criar um cluster de HDInsight com dados Lucerne Store como armazenamento](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Usar a análise de dados Azure Lucerne com dados Lucerne Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)



## <a name="download-data-from-data-lake-store"></a>Baixar dados do armazenamento de Lucerne de dados

Talvez você também queira baixar ou mover dados do armazenamento de Lucerne de dados do Azure para cenários, como:

* Mova dados a outros repositórios interface com o pipeline de processamento de dados existente. Por exemplo, talvez você queira mover dados Lucerne do armazenamento de dados para o banco de dados do Azure SQL ou no SQL Server local.
* Baixe dados ao seu computador local para processamento em ambientes de IDE durante a criação de protótipos de aplicativo.

![Dados de saída Lucerne do armazenamento de dados] (./media/data-lake-store-data-scenarios/egress-data.png "Dados de saída Lucerne do armazenamento de dados")

Nesses casos, você pode usar qualquer uma das seguintes opções:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Fábrica de dados do Azure](../data-factory/data-factory-data-movement-activities.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Você também pode usar os seguintes métodos para escrever seu próprio script/aplicativo para baixar dados Lucerne do armazenamento de dados.

* [Azure CLI entre plataformas](data-lake-store-get-started-cli.md)
* [PowerShell Azure](data-lake-store-get-started-powershell.md)
* [Armazenamento de Lucerne de dados do Azure .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>Visualizar dados no repositório de Lucerne de dados

Você pode usar uma mistura de serviços para criar representações visuais de dados armazenados no repositório de Lucerne de dados.

![Visualizar dados no repositório de Lucerne de dados] (./media/data-lake-store-data-scenarios/visualize-data.png "Visualizar dados no repositório de Lucerne de dados")

* Você pode iniciar usando [Fábrica de dados do Azure para mover dados do repositório de Lucerne dados depósito de dados do SQL Azure](../data-factory/data-factory-data-movement-activities.md#supported-data-stores)
* Depois disso, você pode [integrar o Power BI com depósito de dados do SQL Azure](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi.md) para criar uma representação visual dos dados.
