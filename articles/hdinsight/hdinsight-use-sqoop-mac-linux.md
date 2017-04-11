<properties
    pageTitle="Usar Hadoop Sqoop em HDInsight baseado em Linux | Microsoft Azure"
    description="Saiba como executar Sqoop importar e exportar entre um Hadoop baseado em Linux em cluster HDInsight e um banco de dados do SQL Azure."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="larryfr"/>

#<a name="use-sqoop-with-hadoop-in-hdinsight-ssh"></a>Usar Sqoop com Hadoop em HDInsight (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como usar Sqoop para importar e exportar entre um cluster de HDInsight baseados em Linux e o banco de dados do banco de dados do SQL Azure ou SQL Server.

> [AZURE.NOTE] As etapas neste artigo usam SSH para se conectar a um cluster de HDInsight baseados em Linux. Clientes do Windows também podem usar o PowerShell do Azure e HDInsight .NET SDK para trabalhar com Sqoop em clusters baseados em Linux. Use o seletor de tabulação para abrir esses artigos.

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Hadoop um cluster em HDInsight** e um __Banco de dados do Azure SQL__: as etapas neste documento se baseiam o cluster e o banco de dados criado usando o documento de [banco de dados do SQL e criar cluster](hdinsight-use-sqoop.md#create-cluster-and-sql-database) . Se você já tiver um cluster de HDInsight e o banco de dados SQL, você pode substituir aqueles para os valores usados neste documento.
- **Estação de trabalho**: um computador com um cliente SSH.

##<a name="install-freetds"></a>Instalar o FreeTDS

1. Use SSH para se conectar ao cluster HDInsight baseados em Linux. O endereço para usar ao conectar está `CLUSTERNAME-ssh.azurehdinsight.net` e a porta é `22`.

    Para obter mais informações sobre como usar o SSH para se conectar ao HDInsight, consulte os seguintes documentos:

    * **Clientes Linux, Unix ou OS X**: consulte [conectar a um cluster baseado no Linux HDInsight Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Clientes do Windows**: consulte [conectar a um cluster baseado no Linux HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

3. Use o comando a seguir para instalar o FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

    FreeTDS será usado em várias etapas para se conectar ao banco de dados SQL.

##<a name="create-the-table-in-sql-database"></a>Crie a tabela no banco de dados SQL

> [AZURE.IMPORTANT] Se você estiver usando um cluster de HDInsight e o banco de dados do SQL criado usando as etapas em [criar cluster e banco de dados SQL](hdinsight-use-sqoop.md), ignore as etapas desta seção, como o banco de dados e a tabela foram criados como parte das etapas no documento.

1. Da conexão SSH ao HDInsight, use o seguinte comando para se conectar ao servidor de banco de dados SQL e criar a tabela que será usada no restante destas etapas:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Você receberá saída similar ao seguinte:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. No `1>` solicitar, insira as seguintes linhas:

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])
        GO
        CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
        GO

    Quando o `GO` instrução é inserida, as instruções anterior serão avaliadas. Primeiro, a tabela de **mobiledata** é criada e um índice de cluster é adicionado a ela (obrigatório pelo banco de dados do SQL).

    Use o seguinte para verificar que a tabela foi criada:

        SELECT * FROM information_schema.tables
        GO

    Você deve ver o resultado similar ao seguinte:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

8. Insira `exit` na `1>` aviso sair do utilitário tsql.

##<a name="sqoop-export"></a>Exportação de Sqoop

3. Da conexão SSH ao HDInsight, se o seguinte comando para verificar que Sqoop pode ver seu banco de dados do SQL:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Isso deve retornar uma lista de bancos de dados, incluindo o banco de dados de **sqooptest** que você criou anteriormente.

4. Use o comando a seguir para exportar dados do **hivesampletable** à tabela **mobiledata** :

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasbs:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    Isso instrui Sqoop para se conectar ao banco de dados SQL no banco de dados de **sqooptest** e exportar os dados a **wasbs: / / / seção/depósito/hivesampletable** (físicos arquivos para o *hivesampletable*,) para a tabela de **mobiledata** .

5. Após concluir o comando, use o seguinte para se conectar ao banco de dados usando TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Uma vez conectado, use as instruções a seguir para verificar que os dados foram exportados para a tabela de **mobiledata** :

        SELECT * FROM mobiledata
        GO

    Você deve ver uma listagem dos dados na tabela. Tipo de `exit` para sair do utilitário tsql.

##<a name="sqoop-import"></a>Importação de Sqoop

1. Use este procedimento para importar dados da tabela **mobiledata** no banco de dados SQL, para o **wasbs: / / / tutoriais/usesqoop/importeddata** diretório em HDInsight:

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    Os dados importados terá campos separados por um caractere de tabulação e as linhas serão encerradas por um caractere de nova linha.

2. Quando a importação for concluída, use o comando a seguir lista os dados no novo diretório:

        hadoop fs -text wasbs:///tutorials/usesqoop/importeddata/part-m-00000

##<a name="using-sql-server"></a>Usando o SQL Server

Você também pode usar Sqoop para importar e exportar dados do SQL Server, no seu centro de dados ou em uma máquina Virtual hospedado no Azure. As diferenças entre usar o banco de dados SQL e SQL Server são:

* HDInsight e o SQL Server devem estar na mesma rede Virtual do Azure

    > [AZURE.NOTE] HDInsight suporta somente redes virtuais baseada no local e não funcionam no momento com redes virtuais baseadas em grupo de afinidade.

    Quando você estiver usando o SQL Server no seu centro de dados, você deve configurar a rede virtual como *- to-site* ou *ponto-a-site*.

    > [AZURE.NOTE] Para redes virtuais **ponto-a-site** , SQL Server deve estar executando o cliente VPN aplicativo de configuração, que está disponível no **painel** de sua configuração de rede virtual Azure.

    Para obter mais informações rede Virtual do Azure, consulte [Visão geral de rede Virtual](../virtual-network/virtual-networks-overview.md).

* SQL Server deve ser configurado para permitir a autenticação do SQL. Para obter mais informações, consulte [Escolher um modo de autenticação](https://msdn.microsoft.com/ms144284.aspx)

* Você pode precisar configurar o SQL Server para aceitar conexões remotas. Consulte [como solucionar problemas de conexão com o mecanismo de banco de dados do SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) para obter mais informações

* Você deve criar o banco de dados do **sqooptest** no SQL Server usando um utilitário como o **SQL Server Management Studio** ou **tsql** - as etapas para usar a CLI Azure só funcionam para o banco de dados do Azure SQL

    As instruções TSQL para criar a tabela de **mobiledata** são semelhantes aqueles usados para banco de dados SQL, com exceção de criação de um clusterd index - isso não é necessário para o SQL Server:

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])

* Ao conectar com o SQL Server de HDInsight, você pode precisar usar o endereço IP do SQL Server, a menos que você configurou um sistema de nome de domínio (DNS) para resolver nomes em uma rede Virtual Azure. Por exemplo:

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##<a name="limitations"></a>Limitações

* Exportação em massa - baseados em Linux com HDInsight, o conector de Sqoop usado para exportar dados para o Microsoft SQL Server ou banco de dados do Azure SQL atualmente não oferece suporte para inserções em massa.

* Em lotes - com HDInsight baseados em Linux, ao usar o `-batch` mudar ao executar inserções, Sqoop executará várias inserções em vez de processamento em lotes as operações de inserção.

##<a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como usar Sqoop. Para saber mais, consulte:

- [Usar Oozie com HDInsight][hdinsight-use-oozie]: usar Sqoop ação em um fluxo de trabalho Oozie.
- [Analisar dados de atrasos de voos usando HDInsight][hdinsight-analyze-flight-data]: usar seção analisar voos atrasar dados e depois use Sqoop para exportar dados para um banco de dados do SQL Azure.
- [Carregar dados ao HDInsight][hdinsight-upload-data]: encontrar outros métodos para carregar dados para armazenamento de Blob HDInsight/Azure.



[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
