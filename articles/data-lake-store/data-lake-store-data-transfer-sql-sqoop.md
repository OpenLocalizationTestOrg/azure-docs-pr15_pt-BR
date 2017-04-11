<properties 
   pageTitle="Copiar dados entre o armazenamento de Lucerne de dados e o banco de dados do SQL Azure usando Sqoop | Microsoft Azure"
   description="Use Sqoop para copiar dados entre Azure SQL Database e armazenamento de Lucerne de dados" 
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
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="copy-data-between-data-lake-store-and-azure-sql-database-using-sqoop"></a>Copiar dados entre o armazenamento de Lucerne de dados e o banco de dados do SQL Azure usando Sqoop

Saiba como usar Apache Sqoop para importar e exportar dados entre o banco de dados do SQL Azure e o repositório de Lucerne de dados.
 

## <a name="what-is-sqoop"></a>O que é Sqoop?

Aplicativos de grande volume são uma escolha natural para processar dados não estruturados e semiestruturados, como logs e arquivos. No entanto, também pode haver necessário processar dados estruturados que são armazenados em bancos de dados relacionais.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) é uma ferramenta projetada para transferir dados entre bancos de dados relacionais e um repositório de dados grande, como o armazenamento de Lucerne de dados. Você pode usá-lo para importar dados de um sistema de gerenciamento de banco de dados relacional (RDBMS) como o Azure SQL Database para armazenamento de Lucerne de dados. Você pode, em seguida, transformar e analisar os dados usando cargas de trabalho de grande volume e exporte os dados de volta para um RDBMS. Neste tutorial, você usa um banco de dados do SQL Azure como seu banco de dados relacional para importar/exportar de.
 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).
- **Ativar sua assinatura do Azure** para visualização pública dados Lucerne Store. Consulte [as instruções](data-lake-store-get-started-portal.md#signup). 
- **Cluster Azurehdinsight** com acesso a uma conta de armazenamento de Lucerne de dados. Consulte [criar um cluster de HDInsight com armazenamento de Lucerne de dados](data-lake-store-hdinsight-hadoop-use-portal.md). Este artigo pressupõe que você tenha um cluster de HDInsight Linux com acesso dados Lucerne loja.
- **Banco de dados do SQL azure**. Para obter instruções sobre como criar uma, consulte [criar um banco de dados do SQL Azure](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Você aprenderá rapidamente com vídeos?

[Assista a este vídeo](https://mix.office.com/watch/1butcdjxmu114) sobre como copiar dados entre Blobs de armazenamento do Azure e armazenamento de dados de Lucerne usando DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Criar tabelas de amostra do banco de dados do SQL Azure

1. Começar, crie duas tabelas de amostra do banco de dados do SQL Azure. Use o [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou Visual Studio para se conectar ao banco de dados do SQL Azure e, em seguida, execute as seguintes consultas.

    **Criar Tabela1**

        CREATE TABLE [dbo].[Table1]( 
        [ID] [int] NOT NULL, 
        [FName] [nvarchar](50) NOT NULL, 
        [LName] [nvarchar](50) NOT NULL, 
        CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
            ( 
                [ID] ASC 
            ) 
        ) ON [PRIMARY] 
        GO

    **Criar Tabela2**

        CREATE TABLE [dbo].[Table2]( 
        [ID] [int] NOT NULL, 
        [FName] [nvarchar](50) NOT NULL, 
        [LName] [nvarchar](50) NOT NULL, 
        CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
            ( 
                [ID] ASC 
            ) 
        ) ON [PRIMARY] 
        GO

2. Em **Tabela1**, adicione alguns dados de exemplo. Deixe **Tabela2** vazio. Podemos importará dados de **Table1** dados Lucerne loja. Em seguida, podemos será exporte dados Lucerne do armazenamento de dados para **Tabela2**. Execute o trecho a seguir.

         
        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson'); 
  

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-store"></a>Sqoop de uso de um cluster de HDInsight com acesso ao repositório de Lucerne de dados

Um cluster de HDInsight já tem os pacotes de Sqoop disponíveis. Se você tiver configurado o cluster HDInsight para usar o armazenamento de Lucerne de dados como um armazenamento adicional, você pode usar Sqoop (sem qualquer alteração de configuração) para importar/exportar dados entre um banco de dados relacional (neste exemplo, Azure SQL Database) e uma conta de armazenamento de Lucerne de dados. 

1. Para este tutorial, vamos supor que você criou um cluster de Linux, então você deve usar SSH para se conectar ao cluster. Consulte [conectar a um cluster de HDInsight baseados em Linux](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).

2. Verifique se você pode acessar a conta de armazenamento de Lucerne de dados do cluster. Execute o seguinte comando no prompt de SSH:

        
        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

    Isso deve fornecer uma lista de arquivos/pastas na conta de armazenamento de Lucerne de dados.

### <a name="import-data-from-azure-sql-database-into-data-lake-store"></a>Importar dados do Azure SQL Database para armazenamento de Lucerne de dados

3. Navegue até a pasta onde os pacotes de Sqoop estão disponíveis. Normalmente, isso ficará em `/usr/hdp/<version>/sqoop/bin`. 

4. Importe os dados de **Table1** para a conta de armazenamento de Lucerne de dados. Use a seguinte sintaxe:

        
        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Observe que espaço reservado **banco de dados--nome do servidor sql** representa o nome do servidor onde o banco de dados do SQL Azure está sendo executado. espaço reservado para o **nome de banco de dados SQL** representa o nome do banco de dados real.

    Por exemplo,

        
        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

5. Verifique se que os dados são transferidos para a conta de armazenamento de Lucerne de dados. Execute o seguinte comando:

        
        hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Você verá a seguinte saída.

        
        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Cada **parte-m -** * arquivo corresponde a uma linha na tabela de origem, * *Tabela1**. Você pode exibir o conteúdo da parte-m -* arquivos para verificar.


### <a name="export-data-from-data-lake-store-into-azure-sql-database"></a>Exportar dados do armazenamento de dados de Lucerne em banco de dados do SQL Azure

6. Exporte os dados de conta de armazenamento de Lucerne dados à tabela vazia, **Tabela2**, do banco de dados do SQL Azure. Use a seguinte sintaxe.

        
        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Por exemplo,

        
        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

6. Verifique se que os dados foi carregados para a tabela de banco de dados SQL. Use o [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou Visual Studio para se conectar ao banco de dados do SQL Azure e execute a consulta a seguir.

        
        SELECT * FROM TABLE2

    Isso deve ter a seguinte saída.

        ID  FName   LName
        ------------------
        1   Neal    Kell
        2   Lila    Fulton
        3   Erna    Myers
        4   Annette Simpson

## <a name="see-also"></a>Consulte também

- [Copiar dados de Blobs de armazenamento do Azure para armazenamento de Lucerne de dados](data-lake-store-copy-data-azure-storage-blob.md)
- [Proteger os dados no repositório de Lucerne de dados](data-lake-store-secure-data.md)
- [Usar a análise de dados Azure Lucerne com dados Lucerne Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com dados Lucerne Store](data-lake-store-hdinsight-hadoop-use-portal.md)
