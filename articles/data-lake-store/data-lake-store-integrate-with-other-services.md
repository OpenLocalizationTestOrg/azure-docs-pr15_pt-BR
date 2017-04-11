<properties
   pageTitle="Integração de armazenamento de dados de Lucerne com outros serviços do Azure | Microsoft Azure"
   description="Entender como o armazenamento de dados de Lucerne integra-se com outros serviços do Azure"
   documentationCenter=""
   services="data-lake-store"
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="integrating-data-lake-store-with-other-azure-services"></a>Integração de armazenamento de dados de Lucerne com outros serviços do Azure

Armazenamento de Lucerne de dados do Azure pode ser usado em conjunto com outros serviços do Azure para habilitar uma ampla variedade de cenários. O artigo a seguir lista os serviços que dados Lucerne Store pode ser integrado.

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Usar dados Lucerne armazenamento com Azure HDInsight

Você pode provisionar um cluster de [Azurehdinsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) que usa o armazenamento de Lucerne de dados como o armazenamento compatível com HDFS. Nesta versão, para Hadoop e tempestade clusters no Windows e Linux, você pode usar dados Lucerne Store somente como um armazenamento adicional. Desses clusters ainda usam o armazenamento do Azure (WASB) como o armazenamento padrão. No entanto, para clusters de HBase no Windows e Linux, você pode usar dados Lucerne Store como o armazenamento padrão, armazenamento adicional ou ambos.

Para obter instruções sobre como configurar um cluster de HDInsight com armazenamento de Lucerne de dados, consulte:

* [Provisionar um cluster de HDInsight com armazenamento de Lucerne de dados usando o Portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Provisionar um cluster de HDInsight com armazenamento de Lucerne de dados usando o PowerShell do Azure](data-lake-store-hdinsight-hadoop-use-powershell.md)

**Prefere vídeos?** Siga os links abaixo para assistir a vídeos com instruções sobre como usar o armazenamento de dados de Lucerne com clusters de HDInsight.

* [Criar um cluster de HDInsight com acesso ao repositório de Lucerne de dados](https://mix.office.com/watch/l93xri2yhtp2)
* Depois que o cluster estiver configurado, [dados do Access no repositório de Lucerne de dados usando os scripts de seção e porco](https://mix.office.com/watch/1n9g5w0fiqv1q)


## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Usar dados Lucerne armazenamento com dados Azure Lucerne Analytics

[Análise de Lucerne de dados do Azure](../data-lake-analytics/data-lake-analytics-overview.md) permite que você trabalhe com dados de grande em escala de nuvem. Ele dinamicamente provisiona recursos e permite que você fazer a análise terabytes ou mesmo exabytes de dados que podem ser armazenados em um número de fontes de dados com suporte, um deles sendo dados Lucerne Store. A análise de dados Lucerne especialmente é otimizada para trabalhar com o Azure dados Lucerne Store - fornecendo cargas de trabalho de grande volume de nível mais alto de desempenho, produtividade e paralelização para você.

Para obter instruções sobre como usar a análise de dados Lucerne com dados Lucerne Store, consulte [Introdução ao dados Lucerne análise usando dados Lucerne Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

**Prefere vídeos?** Siga os links abaixo para assistir a vídeos com instruções sobre como usar o armazenamento de dados de Lucerne com clusters de HDInsight.

* [Conectar dados Azure Lucerne Analytics para armazenamento de Lucerne de dados do Azure](https://mix.office.com/watch/qwji0dc9rx9k)
* [Armazenamento de Lucerne de dados do Access Azure via Lucerne a análise de dados](https://mix.office.com/watch/1n0s45up381a8)


## <a name="use-data-lake-store-with-azure-data-factory"></a>Usar dados Lucerne armazenamento com dados Azure fábrica

Você pode usar [Fábrica de dados do Azure](https://azure.microsoft.com/services/data-factory/) para receber dados de tabelas do Azure, banco de dados do SQL Azure, data warehouse do SQL Azure, Blobs de armazenamento do Azure e bancos de dados local. Sendo um cidadão de primeira classe no ecossistema do Azure, fábrica de dados do Azure pode ser usada para coordenar o recebimento de dados destas fonte para armazenamento de Lucerne de dados do Azure.

Para obter instruções sobre como usar fábrica de dados do Azure com dados Lucerne Store, consulte [mover dados para e do armazenamento de Lucerne de dados usando dados fábrica](../data-factory/data-factory-azure-datalake-connector.md).

**Vídeos novamente!** Consulte [Coordenação de dados usando fábrica de dados do Azure para armazenamento de Lucerne de dados do Azure](https://mix.office.com/watch/1oa7le7t2u4ka). 

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Copiar dados de Blobs de armazenamento do Azure para armazenamento de Lucerne de dados

Armazenamento de Lucerne de dados do Azure fornece uma ferramenta de linha de comando, AdlCopy, que permite copiar dados do armazenamento de Blob do Azure para uma conta de armazenamento de Lucerne de dados. Para obter mais informações, consulte [copiar dados de Blobs de armazenamento do Azure para armazenamento de Lucerne de dados](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Copiar dados entre Azure SQL Database e armazenamento de Lucerne de dados

Você pode usar Apache Sqoop para importar e exportar dados entre o banco de dados do SQL Azure e o repositório de Lucerne de dados. Para obter mais informações, consulte [copiar dados entre dados Lucerne Store e o banco de dados do SQL Azure usando Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

**Assista a este vídeo** em [Usando Apache Sqoop para mover dados entre o armazenamento de Lucerne de dados do Azure e fontes relacionais](https://mix.office.com/watch/1butcdjxmu114).

## <a name="use-data-lake-store-with-stream-analytics"></a>Usar dados Lucerne armazenamento com a análise de fluxo

Você pode usar o armazenamento de Lucerne de dados como uma das saídas para armazenar dados transmitidos usando a análise de fluxo Azure. Para obter mais informações, consulte [transmitir dados do Azure armazenamento de Blob no repositório de Lucerne de dados usando a análise de fluxo Azure](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-store-with-power-bi"></a>Usar dados Lucerne armazenamento com o Power BI

Você pode usar o Power BI para importar dados de uma conta de armazenamento de Lucerne de dados para analisar e visualize os dados. Para obter mais informações, consulte [analisar dados no repositório de Lucerne de dados usando o Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-store-with-data-catalog"></a>Usar dados Lucerne armazenamento com o catálogo de dados

Você pode registrar dados Lucerne do armazenamento de dados para o catálogo de dados do Azure para tornar os dados detectáveis em toda a organização. Para obter mais informações, consulte [registrar dados Lucerne do armazenamento de dados no catálogo de dados do Azure](data-lake-store-with-data-catalog.md).


## <a name="see-also"></a>Consulte também

- [Visão geral do Azure Lucerne de repositório de dados](data-lake-store-overview.md)
- [Introdução ao armazenamento de Lucerne de dados usando o Portal](data-lake-store-get-started-portal.md)
- [Introdução ao armazenamento de Lucerne de dados usando o PowerShell](data-lake-store-get-started-powershell.md)  
