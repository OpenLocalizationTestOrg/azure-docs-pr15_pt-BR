<properties
   pageTitle="Copiar dados de Blobs de armazenamento do Azure para armazenamento de dados de Lucerne | Microsoft Azure"
   description="Use a ferramenta de AdlCopy para copiar dados de Blobs de armazenamento do Azure para armazenamento de Lucerne de dados"
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
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="copy-data-from-azure-storage-blobs-to-data-lake-store"></a>Copiar dados de Blobs de armazenamento do Azure para armazenamento de Lucerne de dados

> [AZURE.SELECTOR]
- [Usando DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [Usando AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)

Armazenamento de Lucerne de dados do Azure fornece uma ferramenta de linha de comando, [AdlCopy](http://aka.ms/downloadadlcopy), para copiar os dados das seguintes fontes:

* De armazenamento do Azure Blobs dados Lucerne loja. Você não pode usar AdlCopy para copiar dados Lucerne do armazenamento de dados para blobs de armazenamento do Azure.

* Entre duas contas de armazenamento de Lucerne de dados do Azure. 

Além disso, você pode usar a ferramenta de AdlCopy em dois modos diferentes:

* **Autônomo**, onde a ferramenta usa recursos de armazenamento de Lucerne de dados para executar a tarefa.

* **Usando uma conta de dados Lucerne Analytics**, onde as unidades atribuídas à sua conta de análise de Lucerne de dados são usadas para executar a operação de cópia. Talvez você queira usar esta opção quando você estiver procurando para realizar as tarefas de cópia de maneira previsível.

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).

- Contêiner de **Blobs de armazenamento do Azure** com alguns dados.

- **Conta do azure dados Lucerne Analytics (opcional)** - consulte [Introdução ao Azure dados Lucerne Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) para obter instruções sobre como criar uma conta de armazenamento de Lucerne de dados.

- **Ferramenta de AdlCopy**. Instale a ferramenta de AdlCopy de [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Sintaxe da ferramenta AdlCopy

Use a seguinte sintaxe para trabalhar com a ferramenta de AdlCopy

    AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Unit <Number of Analytics units> /Pattern 

Os parâmetros na sintaxe são descritos abaixo:

| Opção    | Descrição                                                                                                                                                                                                                                                                                                                                                                                                          |
|-----------|------------|
| Fonte    | Especifica o local dos dados de origem no blob de armazenamento do Azure. A fonte pode ser um contêiner de blob, um blob ou outra conta de armazenamento de Lucerne de dados.                                                                                                                                                                                                                                                                                                 |
| Destino      | Especifica o destino de repositório de Lucerne de dados para copiar para.                                                                                                                                                                                                                                                                                                                                                                |
| SourceKey | Especifica a tecla de acesso de armazenamento para a fonte de blob do armazenamento do Azure. Isso é necessário somente se a fonte for um contêiner de blob ou um blob.                                                                                                                                                                                                                                                                                                                                                 |
| Conta   | **Opcional**. Use esta opção se você quiser usar conta do Azure dados Lucerne análise para executar o trabalho de cópia. Se você usa a opção de conta na sintaxe, mas não especificar uma conta de dados Lucerne Analytics, AdlCopy usa uma conta padrão para executar o trabalho. Além disso, se você usar esta opção, adicione a origem (do Azure armazenamento de Blob) e destino (Azure dados Lucerne Store) como fontes de dados para sua conta de dados Lucerne Analytics.  |
| Unidades     |  Especifica o número de unidades de dados Lucerne Analytics que será usado para o trabalho de cópia. Essa opção é obrigatória se você usar a opção de **conta** para especificar a conta de dados Lucerne Analytics.
| Padrão   | Especifica um padrão de regex que indica quais blobs ou arquivos para copiar. AdlCopy usa correspondência diferencia maiusculas de minúsculas. O padrão usado quando nenhum padrão é especificado é copiar todos os itens. Especificar vários padrões de arquivo não é suportada.                                                                                                                                                                                                                                                                                                                                               



## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Usar AdlCopy (como autônomo) para copiar dados de um blob de armazenamento do Azure

1. Abra um prompt de comando e navegue até a pasta onde AdlCopy é instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.

2. Execute o seguinte comando para copiar um blob específico do contêiner de origem para um armazenamento de Lucerne de dados:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Por exemplo:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    
    >[AZURE.NOTE] A sintaxe acima Especifica o arquivo a ser copiado para uma pasta na conta de armazenamento de Lucerne de dados. Ferramenta de AdlCopy cria uma pasta, se o nome de pasta especificada não existe.

    Você será solicitado a inserir as credenciais para a Azure de assinatura em que você possui sua conta de armazenamento de Lucerne de dados. Você verá uma saída semelhante à seguinte:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

3. Você também pode copiar todos os blobs de um contêiner para a conta de armazenamento de Lucerne de dados usando o seguinte comando:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Por exemplo:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-store-account"></a>Usar AdlCopy (como autônomo) para copiar dados de outra conta de armazenamento de Lucerne de dados

Você também pode usar AdlCopy para copiar dados entre duas contas de armazenamento de Lucerne de dados.

1. Abra um prompt de comando e navegue até a pasta onde AdlCopy é instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.

2. Execute o seguinte comando para copiar um arquivo específico de uma conta de armazenamento de Lucerne de dados para outro.

        AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/

    Por exemplo:

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

    >[AZURE.NOTE] A sintaxe acima Especifica o arquivo a ser copiado para uma pasta no destino conta de armazenamento de Lucerne de dados. Ferramenta de AdlCopy cria uma pasta, se o nome de pasta especificada não existe.

    Você será solicitado a inserir as credenciais para a Azure de assinatura em que você possui sua conta de armazenamento de Lucerne de dados. Você verá uma saída semelhante à seguinte:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

3. O comando a seguir copia todos os arquivos de uma pasta específica na fonte de conta de armazenamento de Lucerne de dados para uma pasta no destino conta de armazenamento de Lucerne de dados.

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Usar AdlCopy (com conta de dados Lucerne Analytics) para copiar dados

Você também pode usar sua conta de dados Lucerne análise para executar o trabalho AdlCopy para copiar dados de blobs de armazenamento do Azure para armazenamento de Lucerne de dados. Normalmente você poderia usar esta opção quando os dados para serem movidos estão no intervalo de gigabytes e terabytes e deseja produtividade de desempenho melhor e previsíveis.

Para usar sua conta de dados Lucerne Analytics com AdlCopy para copiar de um Blob de armazenamento do Azure, a fonte (do Azure armazenamento de Blob) deve ser adicionada como uma fonte de dados para sua conta de dados Lucerne Analytics. Para obter instruções sobre como adicionar fontes de dados adicionais à sua conta de dados Lucerne Analytics, consulte [Gerenciar a análise de dados Lucerne fontes de dados de conta](..//data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

>[AZURE.NOTE] Se você estiver copiando de uma conta de armazenamento do Azure dados Lucerne como fonte usando uma conta de análise de Lucerne de dados, você não precisa associar a conta de armazenamento de Lucerne de dados com a conta de dados Lucerne Analytics. O requisito para associar o repositório de origem com a conta de dados Lucerne Analytics é apenas quando a fonte é uma conta de armazenamento do Azure.

Execute o seguinte comando para copiar de um blob de armazenamento do Azure para uma conta de armazenamento de Lucerne de dados usando a análise de Lucerne dados conta:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Por exemplo:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2


Da mesma forma, execute o seguinte comando para copiar de um blob de armazenamento do Azure para uma conta de armazenamento de Lucerne de dados usando a análise de Lucerne dados conta:

    AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Use AdlCopy para copiar dados usando a correspondência de padrões

Nesta seção, você aprende a usar AdlCopy para copiar dados de uma fonte (em nosso exemplo abaixo usamos Azure armazenamento de Blob) para uma conta de armazenamento de Lucerne de dados de destino usando a correspondência de padrões. Por exemplo, você pode usar as etapas abaixo para copiar todos os arquivos com extensão. csv de blob de origem para o destino.

1. Abra um prompt de comando e navegue até a pasta onde AdlCopy é instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.

2. Execute o seguinte comando para copiar todos os arquivos com extensão *. csv de um blob específico do contêiner de origem para um armazenamento de Lucerne de dados:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Por exemplo:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

    
## <a name="billing"></a>Cobrança

* Se você usar a ferramenta de AdlCopy como autônoma será cobrada para custos de saída para mover dados, se a conta de armazenamento do Azure de origem não está na mesma região como o armazenamento de Lucerne de dados.

* Se você usar a ferramenta de AdlCopy com sua conta de dados Lucerne Analytics, aplicará padrão [taxas de cobrança Lucerne a análise de dados](https://azure.microsoft.com/pricing/details/data-lake-analytics/) .

## <a name="considerations-for-using-adlcopy"></a>Considerações para usar AdlCopy

* AdlCopy (para a versão 1.0.5), suporta copiando dados de fontes coletivamente com mais de milhares de arquivos e pastas. No entanto, se você encontrar problemas copiando um conjunto de dados grande, você pode distribuir as arquivos/pastas em subpastas diferentes e use o caminho para as subpastas como fonte, em vez disso.

## <a name="next-steps"></a>Próximas etapas

- [Proteger os dados no repositório de Lucerne de dados](data-lake-store-secure-data.md)
- [Usar a análise de dados Azure Lucerne com dados Lucerne Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com dados Lucerne Store](data-lake-store-hdinsight-hadoop-use-portal.md)
