<properties
    pageTitle="Criar sua primeira fábrica de dados (REST) | Microsoft Azure"
    description="Neste tutorial, você cria um pipeline de fábrica do Azure dados de amostra usando dados fábrica REST API."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"
/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/16/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-azure-data-factory-using-data-factory-rest-api"></a>Tutorial: Criar sua primeira fábrica de dados Azure usando dados fábrica REST API
> [AZURE.SELECTOR]
- [Visão geral e os pré-requisitos](data-factory-build-your-first-pipeline.md)
- [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [O Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modelo do Gerenciador de recursos](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

Neste artigo, você pode usar API de REST de fábrica de dados para criar sua primeira fábrica dados Azure.

## <a name="prerequisites"></a>Pré-requisitos
- Leia o artigo de [Visão geral do Tutorial](data-factory-build-your-first-pipeline.md) e conclua as etapas de **pré-requisito** .
- Instale o [Curl](https://curl.haxx.se/dlwiz/) em sua máquina. Você pode usar a ferramenta de ROTAÇÃO com comandos REST para criar uma fábrica de dados. 
- Siga as instruções [deste artigo](../resource-group-create-service-principal-portal.md) para: 
    1. Crie um aplicativo Web chamado **ADFGetStartedApp** no Active Directory do Azure.
    2. Obter **ID do cliente** e **chave secreta**. 
    3. Obter **identificação de locatário**. 
    4. Atribua o aplicativo de **ADFGetStartedApp** na função **Colaborador de fábrica de dados** .  
- Instale o [PowerShell Azure](../powershell-install-configure.md).  
- Abra o **PowerShell** e execute o seguinte comando. Mantenha Azure PowerShell aberta até o final deste tutorial. Se você fecha e reabrir, você precisa executar os comandos novamente.
    1. Executar **AzureRmAccount Login** e insira o nome de usuário e senha que você usa para entrar no portal do Azure.  
    2. Execute **Get-AzureRmSubscription** para exibir todas as assinaturas para essa conta.
    3. Executar **Get-AzureRmSubscription - o nome de inscrição NameOfAzureSubscription | Definir AzureRmContext** para selecionar a assinatura à qual você deseja trabalhar. Substitua **NameOfAzureSubscription** com o nome da sua assinatura do Azure. 
3. Crie um grupo de recursos Azure denominado **ADFTutorialResourceGroup** executando o seguinte comando do PowerShell:  

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Algumas das etapas deste tutorial suponha que você use o grupo de recursos denominado ADFTutorialResourceGroup. Se você usar um grupo de recursos diferentes, você precisa usar o nome do seu grupo de recursos no lugar de ADFTutorialResourceGroup neste tutorial.

## <a name="create-json-definitions"></a>Criar definições de JSON
Crie seguintes JSON arquivos na pasta onde se encontra curl.exe. 

### <a name="datafactoryjson"></a>DataFactory.JSON 
> [AZURE.IMPORTANT] Nome deve ser exclusivo, portanto, talvez você queira prefixo/sufixo ADFCopyTutorialDF para torná-lo um nome exclusivo. 

    {  
        "name": "FirstDataFactoryREST",  
        "location": "WestUS"
    }  

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.JSON
> [AZURE.IMPORTANT] Substitua **accountname** e **accountkey** com o nome e a chave da sua conta de armazenamento do Azure. Para saber como acessar sua chave de acesso de armazenamento, consulte [Exibir, copiar e as teclas de acesso do armazenamento gerar](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }


### <a name="hdinsightondemandlinkedservicejson"></a>hdinsightondemandlinkedservice.JSON

    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.2",
                "clusterSize": 1,
                "timeToLive": "00:30:00",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }

A tabela a seguir fornece descrições para as propriedades JSON usadas no trecho:

| Propriedade | Descrição |
| :------- | :---------- |
| Versão | Especifica que a versão do HDInsight criado para ser 3,2. | 
| ClusterSize | Tamanho do cluster HDInsight. | 
| TimeToLive | Especifica que o tempo ocioso de cluster HDInsight, antes que seja excluída. |
| linkedServiceName | Especifica a conta de armazenamento que é usada para armazenar os logs que são gerados pelo HDInsight |

Observe os seguintes pontos: 

- Fábrica dados cria um cluster de HDInsight **baseado no Windows** com o JSON acima. Você também poderia ter-criar um cluster de HDInsight **baseado em Linux** . Consulte [Serviço vinculada de HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes. 
- Você pode usar **seu próprio cluster HDInsight** em vez de usar um cluster de HDInsight sob demanda. Consulte [Serviço vinculada HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes.
- Cluster HDInsight cria um **contêiner padrão** no armazenamento de blob que você especificou no JSON (**linkedServiceName**). HDInsight não exclui este contêiner quando o cluster é excluído. Esse comportamento é por design. Com o serviço de HDInsight vinculado sob demanda, um HDInsight cluster é criado sempre que uma fatia é processada, a menos que haja uma existente live cluster (**timeToLive**) e é excluído quando o processamento é feito.

    Conforme mais fatias são processadas, você vê muitos contêineres em seu armazenamento de blob do Microsoft Azure. Se você não precisar delas para solução de problemas dos trabalhos, você talvez queira excluí-las para reduzir o custo de armazenamento. Os nomes desses contêineres seguem um padrão: "AAD**yourdatafactoryname**-**linkedservicename**- datetimestamp". Use ferramentas como o [Gerenciador de armazenamento da Microsoft](http://storageexplorer.com/) para excluir contêineres em seu armazenamento de blob do Microsoft Azure.

Consulte [Serviço vinculada de HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes. 

### <a name="inputdatasetjson"></a>inputdataset.JSON

    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }


O JSON define um dataset chamado **AzureBlobInput**, que representa dados de entrada para uma atividade no pipeline. Além disso, ele especifica que os dados de entrada encontra-se no contêiner de blob chamado **adfgetstarted** e a pasta chamada **inputdata**.

A tabela a seguir fornece descrições para as propriedades JSON usadas no trecho:

| Propriedade | Descrição |
| :------- | :---------- |
| tipo | A propriedade type é definida como AzureBlob porque os dados residem no armazenamento de blob do Microsoft Azure. |  
| linkedServiceName | refere-se a StorageLinkedService que você criou anteriormente. |
| nome do arquivo | Essa propriedade é opcional. Se você omitir essa propriedade, todos os arquivos da folderPath são recebidos. Nesse caso, somente o input.log é processada. |
| tipo | Os arquivos de log estão em formato de texto, por isso usamos TextFormat. | 
| columnDelimiter | colunas nos arquivos de log são delimitadas por um caractere de vírgula () |
| frequência/intervalo | frequência definida para o mês e o intervalo é 1, o que significa que as fatias de entrada estão disponíveis mensalmente. | 
| externo | Essa propriedade é definida como true se os dados de entrada não são gerados pelo serviço de dados fábrica. | 

### <a name="outputdatasetjson"></a>outputdataset.JSON

    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adfgetstarted/partitioneddata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            }
        }
    }

O JSON define um dataset chamado **AzureBlobOutput**, que representa os dados de saída para uma atividade no pipeline. Além disso, ele especifica que os resultados são armazenados no contêiner de blob chamado **adfgetstarted** e a pasta chamada **partitioneddata**. A seção de **disponibilidade** Especifica que o conjunto de dados de saída é produzido mensalmente.

### <a name="pipelinejson"></a>pipeline.JSON
> [AZURE.IMPORTANT] Substitua **storageaccountname** pelo nome da sua conta de armazenamento do Azure. 


    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [{
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<stroageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<stroageaccountname>t.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [{
                    "name": "AzureBlobInput"
                }],
                "outputs": [{
                    "name": "AzureBlobOutput"
                }],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }],
            "start": "2016-07-10T00:00:00Z",
            "end": "2016-07-11T00:00:00Z",
            "isPaused": false
        }
    }

No trecho de JSON, você está criando um pipeline que consiste em uma única atividade que usa a seção para processar dados em um cluster de HDInsight.

O arquivo de script da seção, **partitionweblogs.hql**, está armazenado na conta de armazenamento do Azure (especificado por scriptLinkedService, chamado **StorageLinkedService**) e na pasta de **script** no contêiner **adfgetstarted**.

A seção **define** Especifica as configurações de tempo de execução que são passadas para o script de seção como valores de configuração de seção (por exemplo ${hiveconf: inputtable}, ${hiveconf:partitionedtable}).

As propriedades de **início** e **fim** do pipeline Especifica o período ativo do pipeline.

A atividade de JSON, você especificar que o script de seção é executado na computação especificada pela **linkedServiceName** – **HDInsightOnDemandLinkedService**.

> [AZURE.NOTE] Consulte [Anatomia de um canal](data-factory-create-pipelines.md#anatomy-of-a-pipeline) para obter detalhes sobre propriedades JSON usadas no exemplo anterior. 

## <a name="set-global-variables"></a>Variáveis globais do conjunto

No Azure PowerShell, execute os seguintes comandos após substituir os valores com seu próprio:

> [AZURE.IMPORTANT] Consulte a seção [pré-requisitos](#prerequisites) para obter instruções sobre a obtenção de ID do cliente, segredo de cliente, locatários ID e ID da assinatura.   

    $client_id = "<client ID of application in AAD>"
    $client_secret = "<client key of application in AAD>"
    $tenant = "<Azure tenant ID>";
    $subscription_id="<Azure subscription ID>";

    $rg = "ADFTutorialResourceGroup"
    $adf = "FirstDataFactoryREST"



## <a name="authenticate-with-aad"></a>Autenticar com AAD

    $cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
    $responseToken = Invoke-Command -scriptblock $cmd;
    $accessToken = (ConvertFrom-Json $responseToken).access_token;
    
    (ConvertFrom-Json $responseToken) 



## <a name="create-data-factory"></a>Criar fábrica de dados

Nesta etapa, você criar uma fábrica de dados do Azure denominada **FirstDataFactoryREST**. Uma fábrica de dados pode ter um ou mais canais. Um pipeline pode ter uma ou mais atividades. Por exemplo, uma atividade de cópia para copiar dados de uma fonte para um armazenamento de dados de destino e uma atividade de HDInsight seção executar script de seção para transformar dados. Execute os seguintes comandos para criar a fábrica de dados: 

1. O comando atribua a variável chamada **cmd**. 

    Confirme que o nome da fábrica dados que você especifica aqui (ADFCopyTutorialDF) corresponde ao nome especificado na **datafactory.json**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
2. Execute o comando usando o **Comando chamar**.

        $results = Invoke-Command -scriptblock $cmd;
3. Exiba os resultados. Se a fábrica de dados foi criada com êxito, você verá o JSON para a fábrica de dados nos **resultados**; Caso contrário, você vê uma mensagem de erro.  

        Write-Host $results

Observe os seguintes pontos:
 
- O nome do Azure dados Factory deve ser exclusivo. Se você vir o erro nos resultados: **nome de fábrica de dados "FirstDataFactoryREST" não está disponível**, siga estas etapas:  
    1. Altere o nome (por exemplo, yournameFirstDataFactoryREST) no arquivo **datafactory.json** . Consulte o tópico de [Dados Factory - regras de nomenclatura](data-factory-naming-rules.md) para regras de nomenclatura para artefatos de fábrica de dados.
    2. No primeiro comando onde a variável **$cmd** é atribuída um valor, substitua FirstDataFactoryREST com o novo nome e executar o comando. 
    3. Execute os próximos dois comandos para chamar a API REST para criar a fábrica de dados e imprimir os resultados da operação. 
- Para criar instâncias de fábrica de dados, você precisa ser um colaborador/administrador da assinatura do Azure
- O nome da fábrica dados pode ser registrado como um nome DNS no futuro e, portanto, ficam visível publicamente.
- Se você receber o erro: "**esta assinatura não está registrada para usar o namespace Microsoft.DataFactory**", siga um destes procedimentos e tente publicar novamente: 

    - No Azure PowerShell, execute o seguinte comando para registrar o provedor de dados fábrica: 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Você pode executar o seguinte comando para confirmar que a fábrica de dados provedor está registrado: 
    
            Get-AzureRmResourceProvider
    - Faça logon usando a assinatura do Azure para o [portal do Azure](https://portal.azure.com) e navegue até uma lâmina de fábrica de dados (ou) criar uma fábrica de dados no portal do Azure. Esta ação registra automaticamente o provedor para você.

Antes de criar um pipeline, você precisa criar algumas entidades de fábrica de dados pela primeira vez. Você primeiro criar serviços vinculados para vincular dados armazena/calcula a seu armazenamento de dados, defina a entrada e saída conjuntos de dados para representar dados em armazenamentos de dados vinculados. 

## <a name="create-linked-services"></a>Criar serviços vinculados 
Nesta etapa, você vincular sua conta de armazenamento do Azure e um cluster de Azurehdinsight sob demanda sua fábrica de dados. A conta de armazenamento do Azure mantém os dados de entrada e saídos para o pipeline neste exemplo. O serviço de HDInsight vinculado é usado para executar o script de seção especificado na atividade do pipeline neste exemplo. 

### <a name="create-azure-storage-linked-service"></a>Criar serviço de armazenamento do Azure vinculado
Nesta etapa, você pode vincular sua conta de armazenamento do Azure a sua fábrica de dados. Com este tutorial, você pode usar a mesma conta de armazenamento do Azure para armazenar dados de entrada/saída e o arquivo de script HQL.

1. O comando atribua a variável chamada **cmd**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. Execute o comando usando o **Comando chamar**.
 
        $results = Invoke-Command -scriptblock $cmd;
3. Exiba os resultados. Se o serviço vinculado tiver sido criado com êxito, você verá o JSON para o serviço vinculado nos **resultados**; Caso contrário, você vê uma mensagem de erro.
  
        Write-Host $results

### <a name="create-azure-hdinsight-linked-service"></a>Criar serviço Azurehdinsight vinculado
Nesta etapa, você pode vincular um cluster de HDInsight sob demanda a sua fábrica de dados. Cluster HDInsight é automaticamente criado no tempo de execução e excluído após a conclusão processamento e ocioso durante o período de tempo especificado. Você pode usar seu próprio cluster HDInsight em vez de usar um cluster de HDInsight sob demanda. Consulte [Calcular vinculadas Services](data-factory-compute-linked-services.md) para obter detalhes.  

1. O comando atribua a variável chamada **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
2. Execute o comando usando o **Comando chamar**.

        $results = Invoke-Command -scriptblock $cmd;
3. Exiba os resultados. Se o serviço vinculado tiver sido criado com êxito, você verá o JSON para o serviço vinculado nos **resultados**; Caso contrário, você vê uma mensagem de erro.  

        Write-Host $results

## <a name="create-datasets"></a>Criar conjuntos de dados
Nesta etapa, você criar conjuntos de dados para representar a entrada e saída de dados para processamento de seção. Esses conjuntos de dados consultem **StorageLinkedService** você tiver criado anteriormente neste tutorial. Os pontos de serviço vinculada a uma conta de armazenamento do Azure e conjuntos de dados especificar contêiner, pasta, nome do arquivo no armazenamento que contém a entrada e saída de dados.   

### <a name="create-input-dataset"></a>Criar entrada de conjunto de dados
Nesta etapa, você cria o dataset entrado para representar os dados de entrada armazenados no armazenamento de Blob do Azure.

1. O comando atribua a variável chamada **cmd**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. Execute o comando usando o **Comando chamar**.

        $results = Invoke-Command -scriptblock $cmd;
3. Exiba os resultados. Se o conjunto de dados foi criado com êxito, você verá o JSON para o conjunto de dados nos **resultados**; Caso contrário, você vê uma mensagem de erro.
  
        Write-Host $results
### <a name="create-output-dataset"></a>Criar conjunto de dados de saída
Nesta etapa, você cria o dataset de saída para representar os dados de saída armazenados no armazenamento de Blob do Azure.

1. O comando atribua a variável chamada **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
2. Execute o comando usando o **Comando chamar**.

        $results = Invoke-Command -scriptblock $cmd;
3. Exiba os resultados. Se o conjunto de dados foi criado com êxito, você verá o JSON para o conjunto de dados nos **resultados**; Caso contrário, você vê uma mensagem de erro.
  
        Write-Host $results 

## <a name="create-pipeline"></a>Crie pipeline
Nesta etapa, você pode criar sua primeira pipeline com uma atividade de **HDInsightHive** . Fatia entrada está disponível mensalmente (frequência: mês, intervalo: 1), fatia de saída é gerada mensalmente e a propriedade Agendador da atividade também é definida como mensal. As configurações para o conjunto de dados de saída e o Agendador de atividade devem corresponder. Atualmente, conjunto de dados de saída é o que orienta o cronograma, então você deve criar um conjunto de dados de saída, mesmo se a atividade não produzir qualquer saída. Se a atividade não assumir qualquer entrada, você pode ignorar a criação do dataset de entrada.  

Confirme que você ver o arquivo **input.log** na pasta **adfgetstarted/inputdata** o armazenamento de blob do Microsoft Azure e execute o seguinte comando para implantar o pipeline. Como os horários de **início** e **fim** são definidos no passado e **isPaused** é definida como false, o pipeline (atividade no pipeline) executa imediatamente após a implantação. 

1. O comando atribua a variável chamada **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. Execute o comando usando o **Comando chamar**.

        $results = Invoke-Command -scriptblock $cmd;
3. Exiba os resultados. Se o conjunto de dados foi criado com êxito, você verá o JSON para o conjunto de dados nos **resultados**; Caso contrário, você vê uma mensagem de erro.  

        Write-Host $results
5. Parabéns, você criou com êxito o pipeline de primeiro uso do PowerShell do Azure!

## <a name="monitor-pipeline"></a>Pipeline de monitor
Nesta etapa, você pode usar dados fábrica REST API para monitorar fatias produzidas pelo pipeline.

    $ds ="AzureBlobOutput"

    $cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

    $results2 = Invoke-Command -scriptblock $cmd;

    IF ((ConvertFrom-Json $results2).value -ne $NULL) {
        ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
    } else {
            (convertFrom-Json $results2).RemoteException
    }


> [AZURE.IMPORTANT] 
> Criação de um cluster de HDInsight sob demanda normalmente leva um dia (aproximadamente 20 minutos). Portanto, espere o pipeline leva **aproximadamente 30 minutos** para processar na fatia.  

Execute o comando chamar e a próxima até ver a fatia no estado **pronto** ou **Falha** . Quando a fatia está no estado pronto, verifique a pasta **partitioneddata** no contêiner de **adfgetstarted** em seu armazenamento de blob para os dados de saída.  A criação de um cluster de HDInsight sob demanda normalmente leva algum tempo.

![dados de saída](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [AZURE.IMPORTANT] O arquivo de entrada é excluído quando a fatia é processada com êxito. Portanto, se você quiser executar novamente na fatia ou faça o tutorial novamente, carregue o arquivo de entrada (input.log) na pasta inputdata do contêiner adfgetstarted.

Você também pode usar o portal do Azure para monitorar as fatias e solucionar problemas. Consulte detalhes [monitorar canais usando o portal do Azure](data-factory-build-your-first-pipeline-using-editor.md##monitor-pipeline) .  

## <a name="summary"></a>Resumo 
Neste tutorial, você criou uma fábrica de dados do Azure para processar dados executando o script de seção em um cluster de hadoop HDInsight. Você usou o Editor de fábrica de dados no portal do Azure para fazer as seguintes etapas:  

1.  Criou uma **fábrica de dados**do Azure.
2.  Criou dois **Serviços vinculados**:
    1.  Serviço de **Armazenamento do azure** vinculado para vincular seu armazenamento de blob do Microsoft Azure que contém os arquivos de entrada/saída à fábrica dados.
    2.  Serviço vinculado de sob demanda de **Azurehdinsight** para vincular a um cluster de HDInsight Hadoop sob demanda à fábrica dados. Azure Data Factory cria um HDInsight Hadoop cluster just-in-time para processar dados de entrada e produzir dados de saída. 
3.  Criada dois **conjuntos de dados**, que descrevem dados de entrada e saídos para atividade de HDInsight seção no pipeline. 
4.  Criado um **pipeline** com uma atividade de **Seção de HDInsight** . 

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um pipeline com uma atividade de transformação (HDInsight atividade) que executa um script de seção em um cluster de Azurehdinsight sob demanda. Para ver como usar uma atividade de cópia para copiar dados de um Blob do Azure para SQL Azure, consulte [Tutorial: copiar dados de um Blob do Azure para SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Consulte também
| Tópico | Descrição |
| :---- | :---- |
| [Referência de API do resto de fábrica de dados](https://msdn.microsoft.com/library/azure/dn906738.aspx) |  Consulte a documentação abrangente sobre cmdlets de fábrica de dados |
| [Atividades de transformação de dados](data-factory-data-transformation-activities.md) | Este artigo fornece uma lista de atividades de transformação de dados (como HDInsight seção transformação usada neste tutorial) suportados pelo fábrica de dados do Azure. |
| [Planejamento e à execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspectos de programação e a execução do modelo de aplicativo de fábrica de dados do Azure. |
| [Canais](data-factory-create-pipelines.md) | Este artigo ajuda você a entender canais e atividades em fábrica de dados do Azure e como usá-las para construir ponta a ponta fluxos de trabalho orientados a dados para sua situação ou empresa. |
| [Conjuntos de dados](data-factory-create-datasets.md) | Este artigo ajuda você a entender conjuntos de dados em fábrica de dados do Azure.
| [Monitorar e gerenciar canais usando o Azure blades portal](data-factory-monitor-manage-pipelines.md) | Este artigo descreve como monitorar, gerenciar e depurar seus canais usando o Azure blades portal. |
| [Monitorar e gerenciar canais usando o aplicativo de monitoramento](data-factory-monitor-manage-app.md) | Este artigo descreve como monitorar, gerenciar e depurar canais usando o monitoramento e gerenciamento de aplicativo. 

