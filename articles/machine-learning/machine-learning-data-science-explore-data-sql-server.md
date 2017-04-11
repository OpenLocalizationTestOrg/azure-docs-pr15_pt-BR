<properties 
    pageTitle="Explorar dados no SQL Server Virtual Machine no Azure | Microsoft Azure" 
    description="Como explorar dados armazenados em um servidor de SQL máquina virtual no Azure." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/13/2016" 
    ms.author="bradsev" /> 

#<a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Explorar dados no SQL Server Virtual Machine no Azure


Este documento aborda como explorar dados armazenados em um servidor de SQL máquina virtual no Azure. Isso pode ser feito por dados disputa usando SQL ou usando uma linguagem de programação como Python.

Os seguintes links de **menu** para tópicos que descrevem como usar as ferramentas para explorar os dados de vários ambientes de armazenamento. Esta tarefa é uma etapa no processo de análise de Cortana (PONTA).

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


> [AZURE.NOTE] As instruções SQL de amostra neste documento pressupõem que dados sejam no SQL Server. Se não for, consulte o mapa de processo de ciência de dados nuvem para aprender a mover seus dados para o SQL Server.



## <a name="sql-dataexploration"></a>Explorar dados do SQL com scripts SQL

Aqui estão alguns exemplos de scripts SQL que podem ser usados para explorar armazenamentos de dados no SQL Server.

1. Obter a contagem de observações por dia

    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 

2. Obtenha os níveis em uma coluna de categorias

    `select  distinct <column_name> from <databasename>`

3. Obter o número de níveis em combinação de duas colunas de categorias 

    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. Obter a distribuição para colunas numéricas

    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [AZURE.NOTE] Para um exemplo prático, você pode usar o [conjunto de dados NYC táxi](http://www.andresmh.com/nyctaxitrips/) e consultem o IPNB intitulado [NYC dados disputa usando o bloco de anotações de IPython e SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para um treinamento de ponta a ponta.

##<a name="python"></a>Explorar dados do SQL com Python

Usar Python para explorar dados e gerar recursos quando os dados são no SQL Server é semelhante ao processamento de dados no Azure blob usando Python, conforme documentadas em [dados de processo Azure Blob em seu ambiente de ciência de dados](machine-learning-data-science-process-data-blob.md). Os dados precisam ser carregado do banco de dados em um pandas DataFrame e, em seguida, podem ser processados. Podemos documentar o processo de conexão para o banco de dados e carregar os dados para o DataFrame nesta seção.

O seguinte formato de cadeia de conexão pode ser usado para conectar a um banco de dados do SQL Server no Python usando pyodbc (substituir nomedoservidor, dbname, nome de usuário e senha com valores específicos):

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [biblioteca de Pandas](http://pandas.pydata.org/) em Python fornece um conjunto sofisticado de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação de Python. O seguinte código lê os resultados retornados de um banco de dados do SQL Server em um quadro de dados Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Agora você pode trabalhar com a DataFrame Pandas como cobertos no tópico [dados de processo Azure Blob em seu ambiente de ciência de dados](machine-learning-data-science-process-data-blob.md).

## <a name="cortana-analytics-process-in-action-example"></a>Processo de análise de Cortana no exemplo de ação

Para obter um exemplo de ponta a ponta explicação passo a passo do processo de análise de Cortana usando um conjunto de dados público, consulte [o processo de ciência de dados de equipe em ação: usando o SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

 
