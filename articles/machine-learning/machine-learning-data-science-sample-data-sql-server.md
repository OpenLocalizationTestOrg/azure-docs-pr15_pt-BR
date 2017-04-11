<properties 
    pageTitle="Amostra de dados no SQL Server no Azure | Microsoft Azure" 
    description="Dados de exemplo no SQL Server no Azure" 
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
    ms.date="09/19/2016" 
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>Dados de exemplo no SQL Server no Azure


Este documento mostra como os dados armazenados no SQL Server no Azure exemplo usando a linguagem de programação Python ou SQL. Ele também mostra como mover dados de amostrados para aprendizado de máquina do Azure salvá-lo em um arquivo, carregá-la um Azure blob e, em seguida, leitura em Studio de aprendizado de máquina do Azure.

A amostragem Python usa a biblioteca ODBC [pyodbc](https://code.google.com/p/pyodbc/) para se conectar ao servidor do SQL Azure e a biblioteca de [Pandas](http://pandas.pydata.org/) para fazer a amostragem.

>[AZURE.NOTE] O código SQL de amostra neste documento pressupõe que os dados estiverem em um servidor SQL Azure. Se não estiver, consulte [mover dados para o SQL Server no Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) tópico para obter instruções sobre como mover seus dados para o SQL Server no Azure.

**Por que seus dados de exemplo?**
Se o conjunto de dados que você planeja analisar for grande, geralmente é uma boa ideia amostra busca os dados para reduzi-la para um tamanho menor, mas representante e mais gerenciável. Isso facilita a compreensão de dados, exploração e engenharia de recurso. Sua função no [Processo de ciência de dados da equipe (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) é permitir criação de protótipo rápida das funções de processamento de dados e modelos de aprendizado de máquina.

No **menu** abaixo links para tópicos que descrevem como dados de exemplo do vários ambientes de armazenamento. 

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Esta tarefa de amostragem é uma etapa do [Processo de ciência de dados da equipe (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

##<a name="SQL"></a>Usando o SQL

Esta seção descreve vários métodos usando SQL para executar amostragem aleatória simple com os dados no banco de dados. Escolha um método de acordo com o tamanho de dados e sua distribuição.

Os dois itens a seguintes mostram como usar newid no SQL Server para executar a amostragem. O método escolhido depende como aleatória que você deseja que a amostra seja (pk_id no código de exemplo abaixo será considerado uma chave primária gerado automaticamente).

1. Amostra de aleatória menos estrita

        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())

2. Amostra mais aleatória 

        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

TABLESAMPLE pode ser usado para amostragem bem como demonstrou abaixo. Isso pode ser uma abordagem melhor se o tamanho de dados for grande (supondo que os dados em páginas diferentes não são correlacionados) e para a consulta ser concluída em um tempo razoável.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

>[AZURE.NOTE] Você pode explorar e gerar recursos de amostrados dados armazenando-o em uma nova tabela


###<a name="sql-aml"></a>Conectando ao aprendizado de máquina Azure

Você pode usar as consultas de exemplo acima diretamente dos de aprendizado de máquina do Azure [Importar dados] [ import-data] módulo amostra busca os dados instantâneos e colocá-lo em uma experiência de aprendizado de máquina do Azure. Uma captura de tela de usar o módulo do leitor para ler os dados de amostrados é mostrada abaixo:
   
![leitor sql][1]

##<a name="python"></a>Usando a linguagem de programação Python 

Esta seção demonstra usando a [biblioteca de pyodbc](https://code.google.com/p/pyodbc/) para estabelecer um ODBC conectar a um banco de dados SQL server em Python. A cadeia de conexão de banco de dados é da seguinte maneira: (substitua nomedoservidor, dbname, nome de usuário e senha com a sua configuração):

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A biblioteca de [Pandas](http://pandas.pydata.org/) em Python fornece um conjunto sofisticado de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação de Python. O código a seguir lê uma amostra de 0,1% dos dados de uma tabela no banco de dados do SQL Azure em um dados Pandas:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

Agora, você pode trabalhar com os dados de amostrados no quadro Pandas dados. 

###<a name="python-aml"></a>Conectando ao aprendizado de máquina Azure

Você pode usar o código de exemplo a seguir para salvar os dados de amostra para baixo para um arquivo e carregue-o em um blob do Microsoft Azure. Os dados no blob podem ser diretamente lido em uma experiência de aprendizado de máquina Azure usando os [Dados de importação] [ import-data] módulo. As etapas são da seguinte maneira: 

1. Escreva o quadro de dados pandas para um arquivo local

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Carregue o arquivo local ao Azure blob

        from azure.storage import BlobService
        import tables

        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
        
        try:
       
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
        
        except:         
            print ("Something went wrong with uploading blob:"+BLOBNAME)

3. Ler dados do Azure blob usando [Importar dados] de aprendizado de máquina do Azure[ import-data] módulo, como mostra a captura de tela abaixo:
 
![blob de leitor][2]

## <a name="the-team-data-science-process-in-action-example"></a>O processo de ciência de dados de equipe no exemplo de ação

Para obter um exemplo de ponta a ponta explicação passo a passo do processo de ciência de dados de equipe um usando um conjunto de dados público, consulte [processo de ciência de dados de equipe em ação: usando o SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png

 [import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
