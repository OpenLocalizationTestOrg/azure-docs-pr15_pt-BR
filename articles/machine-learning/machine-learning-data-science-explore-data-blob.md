<properties 
    pageTitle="Explorar dados no armazenamento de blob do Microsoft Azure com Pandas | Microsoft Azure" 
    description="Como explorar dados armazenados no contêiner de blob do Microsoft Azure usando Pandas." 
    services="machine-learning,storage" 
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

#<a name="explore-data-in-azure-blob-storage-with-pandas"></a>Explorar dados no armazenamento de blob do Microsoft Azure com Pandas

Este documento aborda como explorar dados armazenados no contêiner de blob do Microsoft Azure usando o pacote de [Pandas](http://pandas.pydata.org/) Python.

Os seguintes links de **menu** para tópicos que descrevem como usar as ferramentas para explorar os dados de vários ambientes de armazenamento. Esta tarefa é uma etapa no [Processo de ciência de dados]().

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você tenha:

* Criar uma conta de armazenamento do Azure. Se precisar de instruções, consulte [criar uma conta de armazenamento do Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Seus dados armazenados em uma conta de armazenamento de blob do Microsoft Azure. Se precisar de instruções, consulte [mover dados de e de armazenamento do Azure](../storage/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Carregar os dados em um DataFrame Pandas
Para explorar e manipular um conjunto de dados, ele deve primeiro ser baixado de fonte de blob para um arquivo local, em seguida, pode ser carregado em um DataFrame Pandas. Aqui estão as etapas a seguir para este procedimento:

1. Baixar dados do Azure blob com o seguinte exemplo de código Python usando o serviço blob. Substitua a variável no código a seguir com valores específicos: 

        from azure.storage.blob import BlobService
        import tables
        
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))


2. Ler os dados em um quadro de dados Pandas do arquivo baixado.

        #LOCALFILE is the file path 
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Agora você está pronto para explorar os dados e gerar recursos este conjunto de dados.

##<a name="blob-dataexploration"></a>Exemplos de exploração de dados usando o Pandas

Aqui estão alguns exemplos de maneiras de explorar dados usando Pandas:

1. Inspecionar o **número de linhas e colunas** 

        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. **Inspecionar** o primeiro ou último algumas **linhas** no dataset seguinte:

        dataframe_blobdata.head(10)
        
        dataframe_blobdata.tail(10)

3. Verifique o **tipo de dados** de que cada coluna foi importada como usando o seguinte código de amostra
    
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. Marque as **estatísticas básicas** para as colunas no conjunto de dados da seguinte maneira
 
        dataframe_blobdata.describe()
    
5. Examine o número de entradas para cada valor de coluna da seguinte maneira

        dataframe_blobdata['<column_name>'].value_counts()

6. **Contar valores ausentes** em comparação com o número real de entradas em cada coluna usando o seguinte código de amostra

        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
     
7.  Se você tiver **ausente valores** para uma coluna específica nos dados, você pode soltá-los da seguinte maneira:

        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape

    Outra maneira de substituir valores ausentes é com a função de modo:
    
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        

8. Criar um gráfico de **histograma** usando o número de compartimentos variável plotar a distribuição de uma variável 
    
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
        
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    
9. Examine **correlações** entre variáveis usando um scatterplot ou usando a função correlação internos

        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
        
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()
