<properties 
    pageTitle="Processar dados blob Azure com análises avançadas | Microsoft Azure" 
    description="Processar dados no armazenamento de Blob do Azure." 
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
    ms.date="09/19/2016"
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>Processar dados blob Azure com análises avançadas

Este documento abrange explorando dados e geração de recursos dos dados armazenados no armazenamento de Blob do Azure. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Carregar os dados em um quadro de dados Pandas
Para explorar e manipular um dataset, ele deve ser baixado da origem de blob para um arquivo local que pode ser carregado, em seguida, em um quadro de dados Pandas. Aqui estão as etapas a seguir este procedimento:

1. Os dados do Azure blob com o seguinte código Python de amostra usando serviço blob de download. Substitua a variável no código abaixo com valores específicos: 

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


##<a name="blob-dataexploration"></a>Exploração de dados

Aqui estão alguns exemplos de maneiras para explorar dados usando Pandas:

1. Inspecione o número de linhas e colunas 

        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. Inspecione as primeira ou últimos algumas linhas no conjunto de dados como a seguir:

        dataframe_blobdata.head(10)
        
        dataframe_blobdata.tail(10)

3. Verifique o tipo de dados, que cada coluna foi importada como usando o seguinte código de amostra
    
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. Verifique o estatísticas básico das colunas no conjunto de dados da seguinte maneira
 
        dataframe_blobdata.describe()
    
5. Veja o número de entradas para cada valor de column da seguinte maneira

        dataframe_blobdata['<column_name>'].value_counts()

6. Contagem de valores ausentes versus o número real de entradas em cada coluna usando o seguinte código de amostra

        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
     
7.  Se você tiver valores ausentes para uma coluna específica nos dados, você pode soltá-los da seguinte maneira:

        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape

    Outra maneira para substituir os valores ausentes é com a função de modo:
    
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        

8. Criar uma plotagem histograma usando um número variável de bandejas plotar a distribuição de uma variável 
    
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
        
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    
9. Examinar correlações entre variáveis usando um scatterplot ou usando a função internas de correlação

        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
        
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    
    
##<a name="blob-featuregen"></a>Geração de recurso
    
Podemos gerar recursos usando Python da seguinte maneira:

###<a name="blob-countfeature"></a>Valor do indicador com base em geração de recurso

Recursos de categorias podem ser criados da seguinte maneira:

1. Inspecione a distribuição da coluna categorias:
    
        dataframe_blobdata['<categorical_column>'].value_counts()

2. Gerar valores de indicador para cada um dos valores da coluna

        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. Ingressar a coluna de indicador com o quadro de dados original 
 
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. Remova a variável original em si:

        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
    
###<a name="blob-binningfeature"></a>Geração de recurso binning

Para gerar binned recursos, podemos continue da seguinte maneira:

1. Adicionar uma sequência de colunas a serem bin uma coluna numérica
 
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
        
2. Converter binning para uma sequência de variáveis boolean

        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
    
3. Finalmente, ingressar as variáveis fictícios volta para o quadro de dados original

        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool) 


##<a name="sql-featuregen"></a>Gravar dados de volta no Azure blob e consumindo no aprendizado de máquina do Windows Azure

Depois de ter explorado os dados e criado os recursos necessários, você pode carregar os dados (de amostra ou featurized) para um Azure blob e consumi-lo em aprendizado de máquina do Windows Azure usando as seguintes etapas: Observe que os recursos adicionais podem ser criados no Azure máquina Learning Studio também. 
1. Gravar o quadro de dados no arquivo local

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Carregar os dados blob Azure da seguinte maneira:

        from azure.storage.blob import BlobService
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

3. Agora os dados podem ser lidos do blob usando o aprendizado de máquina do Azure [Importar dados] [ import-data] módulo, conforme mostrado na tela abaixo:
 
![blob leitor][1]

[1]: ./media/machine-learning-data-science-process-data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 
