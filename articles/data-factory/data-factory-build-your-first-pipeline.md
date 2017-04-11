<properties
    pageTitle="Tutorial de fábrica de dados: primeiro pipeline de dados | Microsoft Azure"
    description="Este tutorial de fábrica de dados do Azure mostra como criar e agendar uma fábrica de dados que processa dados usando o script de seção em um cluster de Hadoop."
    services="data-factory"
    keywords="dados Azure fábrica tutorial, hadoop cluster, seção hadoop"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-pipeline-to-process-data-using-hadoop-cluster"></a>Tutorial: Criar sua primeira pipeline para processar dados usando Hadoop cluster 
> [AZURE.SELECTOR]
- [Visão geral e os pré-requisitos](data-factory-build-your-first-pipeline.md)
- [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [O Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modelo do Gerenciador de recursos](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

Neste tutorial, você criar sua primeira fábrica de dados Azure com um pipeline de dados que processa dados executando o script de seção em um cluster de Azurehdinsight (Hadoop). 

> [AZURE.NOTE] Este artigo não fornecem uma visão conceitual da fábrica de dados do Azure. Para obter uma visão conceitual do serviço, consulte [Introdução à fábrica de dados do Azure](data-factory-introduction.md). Consulte de [caminho de aprendizagem de fábrica de dados](https://azure.microsoft.com/documentation/learning-paths/data-factory/) de uma maneira recomendada para navegar pelos nosso conteúdo para aprender a fábrica de dados.

## <a name="whats-covered-in-this-tutorial"></a>O que está incluído neste tutorial? 
**Fábrica de dados do Azure** permite compor a **movimentação** de dados e tarefas de **processamento** de dados como fluxos de trabalho orientados a dados (também chamados de canais de dados). Saiba como criar sua primeira atividade de pipeline com um processamento de dados (ou transformação de dados) de dados. Esta atividade usa um cluster de HDInsight Hadoop para transformar e analisar os logs da web de amostra.  

Neste tutorial, você deve executar as seguintes etapas:

1.  Crie uma **fábrica de dados**. Uma fábrica de dados pode conter um ou mais dados tubulações dados mover e processo. 
2.  Crie **Serviços vinculados**. Crie um serviço vinculado para vincular a um armazenamento de dados ou um serviço de computação à fábrica dados. Um armazenamento de dados como o armazenamento do Azure retém dados de entrada/saída de atividades no pipeline. Um serviço de computação como cluster HDInsight Hadoop processos/transformações dados.    
3.  Criar entrada e saída **conjuntos de dados**. Um conjunto de dados de entrada representa a entrada para uma atividade no pipeline e um conjunto de dados de saída representa a saída para a atividade.
3.  Crie o **pipeline**. Um pipeline pode ter uma ou mais atividades (exemplos: Copiar atividade, atividade de seção de HDInsight). Este exemplo utiliza a atividade de seção de HDInsight que executa um script de seção em um cluster de HDInsight Hadoop. O script cria uma tabela que faz referência aos dados de log de web bruto armazenados em armazenamento de blob do Microsoft Azure primeiro e, em seguida, partições os dados processados por ano e mês.

    Há dois tipos de atividades suportadas pela fábrica de dados do Azure. Eles são: [atividades de movimentação de dados](data-factory-data-movement-activities.md) e [atividades de transformação de dados](data-factory-data-transformation-activities.md). Não há atividade de movimentação de dados apenas uma, que é a atividade de cópia. Neste tutorial, você não usar a atividade de cópia. Para um tutorial sobre como usar a atividade de cópia, consulte [Tutorial: copiar dados de um Azure blob para SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Atividade de HDInsight seção que usar neste tutorial é uma das atividades de transformação de dados suportadas pela fábrica de dados.  
 
Aqui é o **modo de exibição de diagrama** da fábrica de dados de amostra que construir neste tutorial. 

![Modo de exibição de diagrama no tutorial de fábrica de dados](./media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)

Neste tutorial, a pasta de **inputdata** do contêiner Azure blob **adfgetstarted** contém um arquivo denominado input.log. Este arquivo de log tem entradas dos três meses: janeiro, fevereiro e março de 2016. Aqui estão as linhas de amostra para cada mês no arquivo de entrada. 

    2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
    2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
    2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

Quando o arquivo é processado pelo pipeline com HDInsight seção atividade, a atividade executa um script de seção no cluster HDInsight que partições entrada dados por ano e mês. O script cria três pastas de saída que contêm um arquivo com entradas de cada mês.  

    adfgetstarted/partitioneddata/year=2016/month=1/000000_0
    adfgetstarted/partitioneddata/year=2016/month=2/000000_0
    adfgetstarted/partitioneddata/year=2016/month=3/000000_0

Das linhas do exemplo mostradas acima, primeiro aquelas (2016-01-01) escrita para o arquivo 000000_0 no mês = 1 pasta. Da mesma forma, segundo escrito para o arquivo no mês = 2 pasta e a terceira um escrito para o arquivo no mês = pasta 3.  


## <a name="pre-requisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter os seguintes pré-requisitos:

1.  **Assinatura do azure** - se você não tiver uma assinatura do Azure, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Consulte o artigo de [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) em como você pode obter uma conta de avaliação gratuita.

2.  **Armazenamento do azure** – você usa uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) . Após ter criado a conta de armazenamento, anote o **nome de conta** e uma **tecla de acesso**. Consulte [Exibir, copiar e as teclas de acesso do armazenamento gerar](../storage/storage-create-storage-account.md#view-and-copy-storage-access-keys). 

### <a name="upload-files-to-azure-storage-for-the-tutorial"></a>Carregar arquivos para o armazenamento do Azure para o tutorial
Antes de iniciar o tutorial, você precisa preparar sua conta de armazenamento do Azure com os arquivos de amostra para o tutorial.

1. Carregar o arquivo de consulta de seção (HQL) para a pasta de **scripts** do contêiner **adfgetstarted** blob.
2. Carregar arquivo de entrada para a pasta **inputdata** do contêiner **adfgetstarted** blob. 

#### <a name="create-hql-script-file"></a>Criar o arquivo de script HQL 

1. Inicie **o bloco de notas** e cole o seguinte script HQL. Este script de seção cria duas tabelas: **WebLogsRaw** e **WebLogsPartitioned**. Clique em **arquivo** no menu e selecione **Salvar como**. Alternar para a pasta **C:\adfgetstarted** no disco rígido. Selecione * *todos os arquivos (*.*) **para o** Salvar como, digite** campo. Insira **partitionweblogs.hql** para o ****de nome de arquivo. Confirme se o **codificação de** campo na parte inferior da caixa de diálogo está definido como **ANSI**. Caso contrário, defina-o como **ANSI * *.  

        set hive.exec.dynamic.partition.mode=nonstrict;
        
        DROP TABLE IF EXISTS WebLogsRaw; 
        CREATE TABLE WebLogsRaw (
          date  date,
          time  string,
          ssitename string,
          csmethod  string,
          csuristem  string,
          csuriquery string,
          sport int,
          susername string,
          cipcsUserAgent string,
          csCookie string,
          csReferer string,
          cshost  string,
          scstatus  int,
          scsubstatus  int,
          scwin32status  int,
          scbytes int,
          csbytes int,
          timetaken int
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        LINES TERMINATED BY '\n' 
        tblproperties ("skip.header.line.count"="2");
        
        LOAD DATA INPATH '${hiveconf:inputtable}' OVERWRITE INTO TABLE WebLogsRaw;
        
        DROP TABLE IF EXISTS WebLogsPartitioned ; 
        create external table WebLogsPartitioned (  
          date  date,
          time  string,
          ssitename string,
          csmethod  string,
          csuristem  string,
          csuriquery string,
          sport int,
          susername string,
          cipcsUserAgent string,
          csCookie string,
          csReferer string,
          cshost  string,
          scstatus  int,
          scsubstatus  int,
          scwin32status  int,
          scbytes int,
          csbytes int,
          timetaken int
        )
        partitioned by ( year int, month int)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
        STORED AS TEXTFILE 
        LOCATION '${hiveconf:partitionedtable}';
        
        INSERT INTO TABLE WebLogsPartitioned  PARTITION( year , month) 
        SELECT
          date,
          time,
          ssitename,
          csmethod,
          csuristem,
          csuriquery,
          sport,
          susername,
          cipcsUserAgent,
          csCookie,
          csReferer,
          cshost,
          scstatus,
          scsubstatus,
          scwin32status,
          scbytes,
          csbytes,
          timetaken,
          year(date),
          month(date)
        FROM WebLogsRaw

Durante a execução, a atividade de seção no pipeline de fábrica de dados passa valores a **inputtable** e **partitionedtable** parâmetros conforme mostrado no seguinte trecho:  

        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"

O **storageaccountname** é o nome da sua conta de armazenamento do Azure.
 
#### <a name="create-a-sample-input-file"></a>Criar um arquivo de entrada de exemplo
Usando o bloco de notas, crie um arquivo denominado **input.log** na **c:\adfgetstarted** com o seguinte conteúdo: 

    #Software: Microsoft Internet Information Services 8.0
    #Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step5.png X-ARR-LOG-ID=9b0c14b1-434d-495a-9b0d-46775194257b 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 37931 871 32
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step6.png X-ARR-LOG-ID=f99cff81-ec38-4a13-b2fe-21b10adca996 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 27146 871 47
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step1.png X-ARR-LOG-ID=af94d3b4-8e05-4871-82c4-638f866d4e83 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 66259 871 140
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47

#### <a name="upload-input-file-and-hql-file-to-your-azure-blob-storage"></a>Carregar o arquivo de entrada e HQL ao seu armazenamento de Blob do Azure

Esta seção fornece instruções sobre como usar a ferramenta de **AzCopy** para copiar arquivos input.log e partitionweblogs.hql para armazenamento de Blob do Azure. Você pode usar qualquer ferramenta de sua escolha (por exemplo: [Microsoft Azure Storage Explorer](http://storageexplorer.com/), [CloudXPlorer pelo ClumsyLeaf Software](http://clumsyleaf.com/products/cloudxplorer) para realizar esta tarefa.   
     
1. Baixe a [versão mais recente do **AzCopy**](http://aka.ms/downloadazcopy), ou a [versão mais recente de visualização](http://aka.ms/downloadazcopypr). Consulte o artigo de [como usar o AzCopy](../storage/storage-use-azcopy.md) para obter instruções sobre como usar o utilitário.
2. Navegue até a pasta c:\adfgetstarted e execute o seguinte comando: 

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy" /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/inputdata /DestKey:<storageaccesskey>  /Pattern:input.log

    Este comando carrega o arquivo de **input.log** para a conta de armazenamento (**adfgetstarted** contêiner e **inputdata** pasta). Substitua **storageaccountname** com o nome de sua conta de armazenamento e **storageaccesskey** com a chave de acesso de armazenamento.

    > [AZURE.NOTE] Esse comando cria um recipiente chamado **adfgetstarted** em seu armazenamento de Blob do Azure e copia o arquivo de **input.log** da sua unidade local para a pasta **inputdata** no contêiner. 
    
3. Após o arquivo foi carregado com êxito, você vê o resultado semelhante ao seguinte de AzCopy.
    
        Finished 1 of total 1 file(s).
        [2015/12/16 23:07:33] Transfer summary:
        -----------------
        Total files transferred: 1
        Transfer successfully:   1
        Transfer skipped:        0
        Transfer failed:         0
        Elapsed time:            00.00:00:01
5. Execute o seguinte comando para carregar o arquivo **partitionweblogs.hql** para a pasta de **script** do contêiner **adfgetstarted** . Aqui está o comando: 
    
        AzCopy /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/script /DestKey:<storageaccesskey>  /Pattern:partitionweblogs.hql

Você concluiu os pré-requisitos. Você pode criar uma fábrica de dados usando uma das seguintes maneiras. Clique em uma das guias na parte superior ou os links a seguir para executar o tutorial. 

- [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [O Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modelo do Gerenciador de recursos](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)