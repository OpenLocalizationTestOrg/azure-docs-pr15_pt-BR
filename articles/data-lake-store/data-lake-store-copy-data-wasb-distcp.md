<properties
   pageTitle="Copiar dados para e do WASB para armazenamento de Lucerne de dados usando Distcp | Microsoft Azure"
   description="Use a ferramenta de Distcp para copiar dados de e para Blobs de armazenamento do Azure para armazenamento de Lucerne de dados"
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
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>Use Distcp para copiar dados entre Blobs de armazenamento do Azure e armazenamento de Lucerne de dados

> [AZURE.SELECTOR]
- [Usando DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [Usando AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)


Quando você tiver criado um cluster de HDInsight que tenha acesso a uma conta de armazenamento de Lucerne de dados, você pode usar ferramentas de ecossistema Hadoop como Distcp para copiar dados **em** um armazenamento de cluster HDInsight (WASB) para uma conta de armazenamento de Lucerne de dados. Este artigo fornece instruções sobre como fazer isso.

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).
- **Ativar sua assinatura do Azure** para visualização pública dados Lucerne Store. Consulte [as instruções](data-lake-store-get-started-portal.md#signup).
- **Cluster Azurehdinsight** com acesso a uma conta de armazenamento de Lucerne de dados. Consulte [criar um cluster de HDInsight com armazenamento de Lucerne de dados](data-lake-store-hdinsight-hadoop-use-portal.md). Verifique se que você habilita a área de trabalho remota para o cluster.

## <a name="do-you-learn-fast-with-videos"></a>Você aprenderá rapidamente com vídeos?

[Assista a este vídeo](https://mix.office.com/watch/1liuojvdx6sie) sobre como copiar dados entre Blobs de armazenamento do Azure e armazenamento de dados de Lucerne usando DistCp.

## <a name="use-distcp-from-remote-desktop-windows-cluster-or-ssh-linux-cluster"></a>Use Distcp da área de trabalho remota (cluster do Windows) ou SSH (Linux cluster)

Um cluster de HDInsight vem com o utilitário de Distcp, que pode ser usado para copiar dados de diferentes fontes em um cluster de HDInsight. Se você tiver configurado o cluster HDInsight para usar o armazenamento de Lucerne de dados como um armazenamento adicional, o utilitário de Distcp pode ser usado-de-prontos para copiar dados de e para uma conta de armazenamento de Lucerne de dados também. Nesta seção examinaremos como usar o utilitário Distcp.

1. Se você tiver um cluster do Windows, remoto em um cluster de HDInsight que tem acesso a uma conta de armazenamento de Lucerne de dados. Para obter instruções, consulte [conectar a clusters usando o RDP](../hdinsight/hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp). Em cluster Desktop, abra a linha de comando do Hadoop.

    Se você tiver um cluster de Linux, use SSH para se conectar ao cluster. Consulte [conectar a um cluster de HDInsight baseados em Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster). Execute os comandos do prompt de SSH.

3. Verifique se você pode acessar o Azure armazenamento Blobs (WASB). Execute o seguinte comando:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Isso deve fornecer uma lista de conteúdo no blob de armazenamento.

4. Da mesma forma, verifique se você pode acessar a conta de armazenamento de Lucerne de dados do cluster. Execute o seguinte comando:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    Isso deve fornecer uma lista de arquivos/pastas na conta de armazenamento de Lucerne de dados.

5. Use Distcp para copiar dados de WASB para uma conta de armazenamento de Lucerne de dados.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    Isso irá copiar o conteúdo da **** pasta/exemplo/dados/gutenberg/no WASB para **/myfolder** na conta de armazenamento de Lucerne de dados.

6. Da mesma forma, use Distcp para copiar dados de conta de armazenamento de Lucerne de dados para WASB.

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Isso irá copiar o conteúdo de **/myfolder** na conta de armazenamento de Lucerne de dados para a **** pasta/exemplo/dados/gutenberg/em WASB.

## <a name="see-also"></a>Consulte também

- [Copiar dados de Blobs de armazenamento do Azure para armazenamento de Lucerne de dados](data-lake-store-copy-data-azure-storage-blob.md)
- [Proteger os dados no repositório de Lucerne de dados](data-lake-store-secure-data.md)
- [Usar a análise de dados Azure Lucerne com dados Lucerne Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com dados Lucerne Store](data-lake-store-hdinsight-hadoop-use-portal.md)
