<properties
    pageTitle="Explorar dados em tabelas de seção com consultas de seção | Microsoft Azure"
    description="Explore dados em tabelas de seção usando consultas de seção."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="bradsev" />

# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Explorar dados em tabelas de seção com consultas de seção

Este documento fornece exemplos de scripts de seção que são usados para explorar dados em tabelas de seção em um cluster de HDInsight Hadoop.

Os seguintes links de **menu** para tópicos que descrevem como usar as ferramentas para explorar os dados de vários ambientes de armazenamento.

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você tenha:

* Criar uma conta de armazenamento do Azure. Se precisar de instruções, consulte [criar uma conta de armazenamento do Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Provisionado um cluster Hadoop personalizados com o serviço de HDInsight. Se precisar de instruções, consulte [Personalizar o Azure HDInsight Hadoop Clusters para análise avançada](machine-learning-data-science-customize-hadoop-cluster.md).
* Os dados são carregados em tabelas de seção no Azure HDInsight Hadoop clusters. Se não tiver, siga as instruções em [criar e carregar dados às tabelas de seção](machine-learning-data-science-move-hive-tables.md) para carregar dados em tabelas de seção primeiro.
* Acesso remoto habilitado ao cluster. Se precisar de instruções, consulte [acessar o nó de cabeça do Hadoop Cluster](machine-learning-data-science-customize-hadoop-cluster.md#headnode).
* Se precisar de instruções sobre como enviar consultas de seção, consulte [como enviar consultas de seção](machine-learning-data-science-move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Scripts de consulta de seção de exemplo para exploração de dados

1. Obter a contagem de observações por partição `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Obter a contagem de observações por dia `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. Obtenha os níveis em uma coluna de categorias  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Obter o número de níveis em combinação de duas colunas de categorias `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Obter a distribuição para colunas numéricas  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Extrair os registros de associação de duas tabelas

        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Scripts de consulta adicionais para cenários de dados de viagem de táxi

Exemplos de consultas que são específicos para cenários [NYC táxi viagem de dados](http://chriswhong.com/open-data/foil_nyc_taxi/) também são fornecidos no [repositório de Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Essas consultas já tem esquema de dados especificada e estiver prontas para ser enviado para executar.
