<properties
    pageTitle="Mover dados de um SQL Server no local para SQL Azure com fábrica de dados do Azure | Azure"
    description="Configure um pipeline de AAD que compõem duas atividades de migração de dados que juntos mover dados diariamente entre bancos de dados no local e na nuvem."
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
    ms.date="09/14/2016"
    ms.author="bradsev" />


# <a name="move-data-from-an-on-premise-sql-server-to-sql-azure-with-azure-data-factory"></a>Mover dados de um SQL server no local para SQL Azure com fábrica de dados do Azure

Este tópico mostra como mover dados de um banco de dados no local SQL Server para um banco de dados do SQL Azure por meio de armazenamento de Blob do Azure usando o Azure dados fábrica (AAD).

Os links a seguir **menu** para os tópicos que descrevem como a inclusão de dados em ambientes de destino onde os dados podem ser armazenados e processados durante o processo de ciência de dados de equipe.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## <a name="intro"></a>Introdução: O que é AAD e quando deve ser usada para migrar os dados?

Azure fábrica de dados é um serviço de integração de dados totalmente gerenciado baseado em nuvem que organiza e automatize a movimentação e transformação de dados. O conceito chave no modelo AAD é pipeline. Um pipeline é um agrupamento lógico de atividades, cada um deles define as ações a serem executadas nos dados contidos em conjuntos de dados. Serviços vinculados são usados para definir as informações necessárias para fábrica de dados para conexão com os recursos de dados.

Com AAD, serviços de processamento de dados existentes podem ser compostos em canais de dados que são gerenciados e altamente disponível na nuvem. Esses canais de dados podem ser agendados para inclusão, preparar, transformar, analisar e publicar dados e AAD gerencia e organiza os dados complexos e dependências de processamento. As soluções podem ser rapidamente criadas e implantadas na nuvem, conectando um número crescente de locais e na nuvem fontes de dados.

Considere o uso AAD:

- Quando dados precisam ser migrada continuamente em um cenário de híbrido que acessa recursos no local e a nuvem 
- Quando os dados é transação ou precisam ser modificada ou tem lógica de negócios adicionada a ele, quando está sendo migrado. 

AAD permite o agendamento e monitoramento de trabalhos usando scripts JSON simples que gerenciam a movimentação dos dados periodicamente. AAD também tem outros recursos como o suporte para operações complexas. Para obter mais informações sobre AAD, consulte a documentação em [Fábrica de dados do Azure (AAD)](https://azure.microsoft.com/services/data-factory/).


## <a name="scenario"></a>O cenário

Podemos configurar um pipeline de AAD que compõem duas atividades de migração de dados. Juntos eles movimentem dados diariamente entre um banco de dados do SQL no local e um banco de dados do SQL Azure na nuvem. As duas atividades são:

* copiar dados de um banco de dados do SQL Server no local para uma conta de armazenamento de Blob do Azure
* copiar dados da conta de armazenamento de Blob do Azure para um banco de dados do SQL Azure.

>[AZURE.NOTE] As etapas mostradas aqui foram adaptadas do tutorial de mais detalhado fornecido pela equipe do AAD: [mover dados entre fontes locais e nuvem com Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md) referências para as seções relevantes do tópico são fornecidas quando apropriado.


## <a name="prereqs"></a>Pré-requisitos
Este tutorial supõe que você possui:

* Uma **assinatura do Azure**. Se você não tiver uma assinatura, você pode inscrever-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento do Azure**. Você usa uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account) . Após ter criado a conta de armazenamento, você precisa obter a chave de conta usada para acessar o armazenamento. Consulte [Exibir, copiar e as teclas de acesso do armazenamento gerar](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Acesso a um **banco de dados do SQL Azure**. Se você deve configurar um banco de dados do SQL Azure, o tpoic [Introdução ao Microsoft Azure SQL Database](../sql-database/sql-database-get-started.md) fornece informações sobre como configurar uma nova instância de um banco de dados do SQL Azure.
* Instalado e configurado **Azure PowerShell** localmente. Para obter instruções, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

> [AZURE.NOTE] Este procedimento usa o [portal do Azure](https://portal.azure.com/).


##<a name="upload-data"></a>Carregue os dados para o SQL Server no local

Usamos [NYC táxi dataset](http://chriswhong.com/open-data/foil_nyc_taxi/) para demonstrar o processo de migração. O dataset NYC táxi está disponível, conforme observado nessa postagem, no Azure blob storage [NYC táxi dados](http://www.andresmh.com/nyctaxitrips/). Os dados tem dois arquivos, o arquivo trip_data.csv, que contém detalhes de viagem, e o arquivo trip_far.csv, que contém detalhes da tarifa pago para cada viagem. Um exemplo e uma descrição desses arquivos são fornecidas em [NYC táxi viagens Dataset descrição](machine-learning-data-science-process-sql-walkthrough.md#dataset).


Você pode adaptar o procedimento apresentado aqui para um conjunto de seus próprios dados ou siga as etapas conforme descrito usando o dataset NYC táxi. Para carregar o dataset NYC táxi em seu banco de dados do SQL Server no local, siga o procedimento descrito em [Massa importar dados para o banco de dados do SQL Server](machine-learning-data-science-process-sql-walkthrough.md#dbload). Estas instruções são para um SQL Server em uma máquina Virtual do Azure, mas o procedimento para carregar o SQL Server no local é a mesma.


##<a name="create-adf"></a>Criar uma fábrica de dados do Azure

As instruções para criar uma nova fábrica de dados do Azure e um grupo de recursos no [portal do Azure](https://portal.azure.com/) são fornecidas [criar uma fábrica de dados do Azure](../data-factory/data-factory-build-your-first-pipeline-using-editor.md#step-1-creating-the-data-factory). Nome do novo AAD instância *adfdsp* e de grupo criado o recurso *adfdsprg*.


## <a name="install-and-configure-up-the-data-management-gateway"></a>Instalar e configurar o Gateway de gerenciamento de dados

Para permitir que seus canais em uma fábrica de dados do Azure para trabalhar com um SQL Server no local, você precisa adicioná-lo como um serviço vinculado à fábrica dados. Para criar um serviço vinculada para um SQL Server no local, faça o seguinte:

- Baixe e instale o Gateway de gerenciamento de dados do Microsoft logon no computador local. 
- Configure o serviço vinculado para a fonte de dados local usar o gateway. 

O Gateway de gerenciamento de dados serializa e desserializa os dados de origem e receptor no computador onde ele é hospedado.

Para obter instruções de configuração e detalhes sobre o Gateway de gerenciamento de dados, consulte [mover dados entre fontes locais e nuvem com o Gateway de gerenciamento de dados](../data-factory/data-factory-move-data-between-onprem-and-cloud.md)


## <a name="adflinkedservices"></a>Criar serviços vinculados para se conectar aos recursos de dados

Um serviço vinculado define as informações necessárias para fábrica de dados do Azure para se conectar a um recurso de dados. O procedimento passo a passo para criar serviços vinculados é fornecido em [criar serviços vinculados](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-2-create-linked-services).

Temos três recursos neste cenário para os quais serviços vinculados são necessários.

1. [Serviço vinculado para no local SQL Server](#adf-linked-service-onprem-sql)
2. [Serviço de vinculados para o armazenamento de Blob do Azure](#adf-linked-service-blob-store)
3. [Serviço de vinculados para o banco de dados do SQL Azure](#adf-linked-service-azure-sql)


###<a name="adf-linked-service-onprem-sql"></a>Serviço de vinculados para o banco de dados do SQL Server no local

Para criar o serviço vinculado para o SQL Server no local:

- Clique no **Repositório de dados** na página inicial AAD Portal clássico do Azure 
- Selecione **SQL** e digite as credenciais de *usuário* e *senha* para o SQL Server no local. Você precisa digitar o nome do servidor como um **nome de instância do NomeServidor barra invertida (NomedoServidor \ NomedaInstância) totalmente qualificado**. Nome do serviço vinculada *adfonpremsql*.

###<a name="adf-linked-service-blob-store"></a>Serviço vinculado blob

Para criar o serviço vinculado para a conta de armazenamento de Blob do Azure:

- Clique no **Repositório de dados** na página inicial AAD Portal clássico do Azure
- Selecione a **Conta de armazenamento do Azure** 
- Insira o nome de chave e contêiner de conta do armazenamento de Blob do Azure. Nome do serviço vinculada *adfds*.

###<a name="adf-linked-service-azure-sql"></a>Serviço de vinculados para o banco de dados do SQL Azure

Para criar o serviço vinculado para o banco de dados do SQL Azure:

- Clique no **Repositório de dados** na página inicial AAD Portal clássico do Azure
- Selecione **SQL Azure** e insira as credenciais de *usuário* e *senha* do banco de dados do SQL Azure. *Nome de usuário* deve ser especificado como *user@servername*.   


##<a name="adf-tables"></a>Definir e criar tabelas para especificar como acessar os conjuntos de dados

Crie tabelas que especificam a estrutura, local e disponibilidade dos conjuntos de dados com os procedimentos a seguir baseada em script. Os arquivos JSON são usados para definir as tabelas. Para obter mais informações sobre a estrutura desses arquivos, consulte [conjuntos de dados](../data-factory/data-factory-create-datasets.md).

> [AZURE.NOTE]  Você deve executar o `Add-AzureAccount` cmdlet antes de executar o cmdlet [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) para confirmar que a assinatura à direita Azure está selecionada para a execução do comando. Para a documentação desse cmdlet, consulte [Adicionar AzureAccount](https://msdn.microsoft.com/library/azure/dn790372.aspx).

As definições baseado em JSON nas tabelas usam os nomes a seguir:

* o **nome da tabela** no SQL server no local é *nyctaxi_data*
* o **nome do contêiner** na conta de armazenamento de Blob do Azure é *containername*  

Três definições de tabela são necessárias para esse pipeline AAD:

1. [Tabela do SQL no local](#adf-table-onprem-sql)
2. [Tabela de blob](#adf-table-blob-store)
3. [SQL Azure tabela](#adf-table-azure-sql)

> [AZURE.NOTE]  Esses procedimentos usam o PowerShell do Azure para definir e criar as atividades de AAD. Mas essas tarefas também podem ser realizadas usando o portal do Azure. Para obter detalhes, consulte [Criar entrada e saída conjuntos de dados](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-3-create-input-and-output-datasets).

###<a name="adf-table-onprem-sql"></a>Tabela do SQL no local

A definição da tabela para o SQL Server no local especificada no arquivo JSON a seguir:

        {
            "name": "OnPremSQLTable",
            "properties":
            {
                "location":
                {
                "type": "OnPremisesSqlServerTableLocation",
                "tableName": "nyctaxi_data",
                "linkedServiceName": "adfonpremsql"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1,   
                "waitOnExternal":
                {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
                }

                }
            }
        }

Os nomes de coluna não foram incluídos aqui. Você pode selecionar sub nos nomes de coluna, incluindo-las aqui (para seleção de detalhes do tópico da [documentação AAD](../data-factory/data-factory-data-movement-activities.md ) .

Copie a definição de JSON da tabela para um arquivo chamado *onpremtabledef.json* arquivo e salve-o em um local conhecido (aqui considerado *C:\temp\onpremtabledef.json*). Crie a tabela no AAD com o seguinte cmdlet do PowerShell do Azure:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


###<a name="adf-table-blob-store"></a>Tabela de blob
Definição da tabela para o local de blob de saída está no seguinte (isso mapeia os dados ingeridos do local ao Azure blob):

        {
            "name": "OutputBlobTable",
            "properties":
            {
                "location":
                {
                "type": "AzureBlobLocation",
                "folderPath": "containername",
                "format":
                {
                "type": "TextFormat",
                "columnDelimiter": "\t"
                },
                "linkedServiceName": "adfds"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1
                }
            }
        }

Copie a definição de JSON da tabela para um arquivo chamado *bloboutputtabledef.json* arquivo e salve-o em um local conhecido (aqui considerado *C:\temp\bloboutputtabledef.json*). Crie a tabela no AAD com o seguinte cmdlet do PowerShell do Azure:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

###<a name="adf-table-azure-sq"></a>SQL Azure tabela
Definição da tabela para o SQL Azure está no seguinte (este esquema mapeia os dados provenientes do blob) de saída:

    {
        "name": "OutputSQLAzureTable",
        "properties":
        {
            "structure":
            [
                { "name": "column1", type": "String"},
                { "name": "column2", type": "String"}                
            ],
            "location":
            {
                "type": "AzureSqlTableLocation",
                "tableName": "your_db_name",
                "linkedServiceName": "adfdssqlazure_linked_servicename"
            },
            "availability":
            {
                "frequency": "Day",
                "interval": 1            
            }
        }
    }

Copie a definição de JSON da tabela para um arquivo chamado *AzureSqlTable.json* arquivo e salve-o em um local conhecido (aqui considerado *C:\temp\AzureSqlTable.json*). Crie a tabela no AAD com o seguinte cmdlet do PowerShell do Azure:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  


##<a name="adf-pipeline"></a>Definir e criar o pipeline

Especifica as atividades que pertencem ao pipeline e criar o pipeline com os procedimentos a seguir baseada em script. Um arquivo JSON é usado para definir as propriedades de pipeline.

* O script pressupõe que o **nome de pipeline** é *AMLDSProcessPipeline*.
* Observe também que definimos a periodicidade do pipeline para ser executado diariamente e use o tempo de execução padrão para o trabalho (12: 00 UTC).

> [AZURE.NOTE]Os procedimentos a seguir usam o PowerShell do Azure para definir e criar o pipeline de AAD. Mas essa tarefa também pode ser feita usando o portal do Azure. Para obter detalhes, consulte [criar e executar um pipeline](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-4-create-and-run-a-pipeline).

Usando as definições de tabela fornecidas anteriormente, a definição de pipeline para o AAD especificada da seguinte maneira:

        {
            "name": "AMLDSProcessPipeline",
            "properties":
            {
                "description" : "This pipeline has one Copy activity that copies data from an on-premise SQL to Azure blob",
                 "activities":
                [
                    {
                        "name": "CopyFromSQLtoBlob",
                        "description": "Copy data from on-premise SQL server to blob",     
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OnPremSQLTable"} ],
                        "outputs": [ {"name": "OutputBlobTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "SqlSource",
                                "sqlReaderQuery": "select * from nyctaxi_data"
                            },
                            "sink":
                            {
                                "type": "BlobSink"
                            }   
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 0,
                            "timeout": "01:00:00"
                        }       

                     },

                    {
                        "name": "CopyFromBlobtoSQLAzure",
                        "description": "Push data to Sql Azure",        
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OutputBlobTable"} ],
                        "outputs": [ {"name": "OutputSQLAzureTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "BlobSource"
                            },
                            "sink":
                            {
                                "type": "SqlSink",
                                "WriteBatchTimeout": "00:5:00",             
                            }           
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 2,
                            "timeout": "02:00:00"
                        }
                     }
                ]
            }
        }

Copie essa definição de JSON do pipeline para um arquivo chamado *pipelinedef.json* arquivo e salve-o em um local conhecido (aqui considerado *C:\temp\pipelinedef.json*). Crie o pipeline no AAD com o seguinte cmdlet do PowerShell do Azure:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json

Confirme que você pode ver o pipeline do AAD no Portal de clássico do Azure aparecer como seguinte (quando você clica em diagrama)

![](media/machine-learning-data-science-move-sql-azure-adf/DJP1kji.png)


##<a name="adf-pipeline-start"></a>Inicie o Pipeline
O pipeline agora pode ser executado usando o seguinte comando:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Os valores de parâmetro *startdate* e *enddate* precisam ser substituído com as datas reais entre os quais deseja que o pipeline de executar.

Depois que o pipeline é executado, você deve ser capaz de ver os dados aparecem no contêiner selecionado para o blob, um arquivo por dia.

Observe que não utilizaram a funcionalidade fornecida pelo AAD aos dados de barra vertical incremental. Para obter mais informações sobre como fazer isso e outros recursos fornecidos pelo AAD, consulte a [documentação de AAD](https://azure.microsoft.com/services/data-factory/).
