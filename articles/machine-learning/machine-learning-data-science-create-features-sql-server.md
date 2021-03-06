<properties
    pageTitle="Criar recursos para os dados no SQL Server usando o SQL e Python | Microsoft Azure"
    description="Processo de dados do SQL Azure"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="bradsev;fashah;garye" />


# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Criar recursos para os dados no SQL Server usando o SQL e Python


Este documento mostra como gerar recursos para dados armazenados em um servidor de SQL máquina virtual no Azure que ajudam a algoritmos aprendemos com mais eficiência com os dados. Isso pode ser feito usando SQL ou usando uma linguagem de programação como Python, que são demonstrou aqui.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]Este **menu** fornece links para tópicos que descrevem como criar recursos para os dados em vários ambientes. Esta tarefa é uma etapa do [Processo de ciência de dados da equipe (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

> [AZURE.NOTE] Para um exemplo prático, você pode consulte [NYC táxi dataset](http://www.andresmh.com/nyctaxitrips/) e consulte a IPNB intitulado [NYC dados disputa usando o bloco de anotações de IPython e SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para um treinamento de ponta a ponta.


## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você tenha:

* Criar uma conta de armazenamento do Azure. Se precisar de instruções, consulte [criar uma conta de armazenamento do Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Seus dados armazenados no SQL Server. Se não tiver, consulte [mover dados para um banco de dados do SQL Azure para aprendizado de máquina do Azure](machine-learning-data-science-move-sql-azure.md) para obter instruções sobre como mover os dados lá.


## <a name="sql-featuregen"></a>Geração de recurso com SQL

Nesta seção, podemos descrevem maneiras de gerar recursos usando SQL:  

1. [Contar com base geração de recurso](#sql-countfeature)
2. [Geração de recurso binning](#sql-binningfeature)
3. [Distribuindo os recursos de uma única coluna](#sql-featurerollout)


> [AZURE.NOTE] Depois de gerar recursos adicionais, você pode adicioná-los como colunas à tabela de dados existente ou criar uma nova tabela com os recursos adicionais e a chave primária, que pode ser associado com a tabela original.

### <a name="sql-countfeature"></a>Contar com base geração de recurso

Este documento demonstra duas formas de geração de recursos de contagem. O primeiro método usa soma condicional e o segundo método usa a cláusula 'where'. Eles depois podem ser Unidos com a tabela original (usando colunas de chave primária) para que os recursos de contar junto com os dados originais.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>Geração de recurso binning

O exemplo a seguir mostra como gerar recursos binned por binning (usando 5 compartimentos) uma coluna numérica que pode ser usada como um recurso em vez disso:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Distribuindo os recursos de uma única coluna

Nesta seção, demonstraremos como uma única coluna de rolo-out em uma tabela para gerar recursos adicionais. O exemplo pressupõe que há uma coluna de latitude ou longitude na tabela a partir da qual você está tentando gerar recursos.

Aqui está uma breve introdução em dados de localização de latitude/longitude (recursos de stackoverflow `http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Isso é útil entender antes de featurizing no campo local:

- Entrar nos informa se estamos Norte ou Sul, Leste ou oeste no globo.
- Um zero centenas dígito informa estamos usando longitude, latitude não!
- A dezenas dígito oferece uma posição para cerca de 1.000 quilômetros. Oferece informações úteis sobre quais continente ou mar, estamos conosco.
- O dígito de unidades (um grau decimal) dá uma posição para cima até 111 quilômetros (60 náuticas milhas, cerca de 69 milhas). Ele pode informe aproximadamente quais grande estado ou país que estamos no.
- A primeira casa decimal vale até 11.1 km: ele pode distinguir a posição de uma cidade grande de uma cidade grande adjacentes.
- Segunda casa decimal vale até 1.1 km: ela pode separar uma Vila do próximo.
- Terceira casa decimal vale m 110 até que ele pode identificar um campo agrícola grande ou campus institucional.
- Casa decimal quarta vale m até 11: ele pode identificar um pacote do terreno. É comparável à precisão típica de uma unidade GPS não corrigida sem interferência.
- Casa decimal quinta vale m 1.1 até ele distinguir árvores uns dos outros. Precisão este nível com unidades GPS comerciais só pode ser obtida com correção diferencial.
- Casa decimal sexta vale m até 0.11: você pode usar isso para dispor estruturas em detalhes, para projetar cenários, criando estradas. Ele deve ser mais de BOM o suficiente para controlar os movimentos do glaciers e rios. Isso pode ser feito sem tomar medidas criteriosa com GPS, como GPS differentially corrigido.

As informações de localização podem pode ser featurized da seguinte maneira, separando região, local e informações de cidade. Observe que uma vez também pode chamar um ponto final de restante como API do Bing Maps disponíveis em `https://msdn.microsoft.com/library/ff701710.aspx` obter as informações de região/Distrito.

    select
        <location_columnname>
        ,round(<location_columnname>,0) as l1       
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

Os recursos de localização com base acima podem ser usados mais para gerar os recursos de count adicional, conforme descrito anteriormente.


> [AZURE.TIP] Você pode inserir programaticamente os registros usando a linguagem de sua preferência. Talvez você precise inserir os dados em blocos para melhorar a eficiência de gravação [Check-out o exemplo de como fazer isso usando pyodbc aqui](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
Outra alternativa é inserir dados no banco de dados usando o [utilitário BCP](https://msdn.microsoft.com/library/ms162802.aspx)

### <a name="sql-aml"></a>Conectando ao aprendizado de máquina Azure

O recurso recentemente gerado pode ser adicionado como uma coluna a uma tabela existente ou armazenado em uma nova tabela e associado com a tabela original para aprendizado de máquina. Os recursos podem ser gerados ou acessados se já criou, usando o módulo de [Importar dados](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) no Azure ML conforme mostrado abaixo:

![leitores de azureml](./media/machine-learning-data-science-process-sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Usando uma linguagem de programação como Python

Usando Python para gerar recursos quando os dados são no SQL Server é semelhante ao processamento de dados no Azure blob usando Python como documentadas nos [dados de processo Azure Blob no ambiente ciência dados](machine-learning-data-science-process-data-blob.md). Os dados precisam ser carregado do banco de dados em um quadro de dados pandas e, em seguida, podem ser processados. Podemos documentar o processo de conexão para o banco de dados e carregar os dados para o quadro de dados nesta seção.

O seguinte formato de cadeia de conexão pode ser usado para conectar a um banco de dados do SQL Server no Python usando pyodbc (substituir nomedoservidor, dbname, nome de usuário e senha com valores específicos):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [biblioteca de Pandas](http://pandas.pydata.org/) em Python fornece um conjunto sofisticado de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação de Python. O código abaixo lê os resultados retornados de um banco de dados do SQL Server em um quadro de dados Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Agora você pode trabalhar com o quadro de dados Pandas como coberta de tópicos [criar recursos para dados de armazenamento de blob do Microsoft Azure usando Panda](machine-learning-data-science-create-features-blob.md).
