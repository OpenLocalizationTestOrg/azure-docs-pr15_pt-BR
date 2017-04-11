<properties
    pageTitle="Tutorial HBase: Introdução ao HBase em Hadoop | Microsoft Azure"
    description="Siga este tutorial HBase para começar a usar o Apache HBase com Hadoop em HDInsight. Criar tabelas do shell HBase e consultá-los usando a seção."
    keywords="Apache hbase, hbase, hbase shell, hbase tutorial"
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="jgao"/>



# <a name="hbase-tutorial-get-started-using-apache-hbase-with-windows-based-hadoop-in-hdinsight"></a>Tutorial HBase: começar a usar o Apache HBase com baseado no Windows Hadoop em HDInsight

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Aprenda a criar clusters HBase no HDInsight, criar tabelas de HBase e consultar as tabelas usando Apache seção. Para obter informações gerais do HBase, consulte [Visão geral de HDInsight HBase][hdinsight-hbase-overview].

As informações neste documento são específicas para clusters HDInsight baseados no Windows. Para obter informações sobre clusters baseados no Windows, use o seletor de tabulação no topo da página para alternar.

> [AZURE.NOTE] HBase (versão 0.98.0) em HDInsight baseado no Windows só está disponível para uso com HDInsight 3.1 clusters (com base em Apache Hadoop e fio COLORIDO 2.4.0). Para obter informações de versão, consulte [Novidades nas versões Hadoop cluster fornecidas pelo HDInsight?][hdinsight-versions]

## <a name="before-you-begin"></a>Antes de começar

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de começar este tutorial HBase, você deve ter o seguinte:

- **A Microsoft Azure assinatura**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Uma estação de trabalho** com o Visual Studio 2013 ou maior: para obter instruções, consulte [Instalar o Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

### <a name="access-control-requirements"></a>Requisitos de controle de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>Criar HBase cluster

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Para criar um cluster de HBase usando o portal do Azure**

1. Entre [portal do Azure][azure-management-portal].
2. Clique em **novo** ou **+** no canto superior esquerdo de canto e clique em **dados + Analytics**, **HDInsight**.
3. Insira os seguintes valores:

    - **Nome de cluster** - Insira um nome para identificar este cluster.
    - **Tipo de cluster** - selecione **HBase**.
    - **Sistema operacional de cluster** - selecione **Windows**.  Para criar HBase baseados em Linux cluster, consulte [HBase tutorial: começar a usar o Apache HBase com Hadoop em HDInsight (Linux)](hdinsight-hbase-tutorial-get-started-linux.md).
    - **Versão** - selecione uma versão de HBase.
    - **Assinatura** - selecione a assinatura Azure usada para criar este cluster.
    - **Grupo de recursos** - criar um novo grupo de recursos Azure ou selecione um existente. Para obter mais informações, consulte [Visão geral do Gerenciador de recursos do Azure](azure-resource-manager/resource-group-overview.md)
    - **Credenciais** - para cluster baseado no Windows, você pode criar um usuário de cluster (também conhecido como HTTP usuário, usuário de serviço web HTTP) e um usuário de área de trabalho remota. Clique em **Habilitar a área de trabalho remota** para adicionar as credenciais de usuário de desktop remoto. A próxima seção requer RDP.
    - **Fonte de dados** - criar uma nova conta de armazenamento do Azure ou selecione uma conta de armazenamento do Azure existente a ser usado como o sistema de arquivo padrão para o cluster. O local padrão de conta de armazenamento determina o local do cluster. A conta padrão de armazenamento e o cluster conjunta devem localizar no mesmo data center.
    - **Níveis de preços de nó** - selecione o número de servidores de região para cluster HBase

        > [AZURE.WARNING] Para alta disponibilidade dos serviços de HBase, você deve criar um cluster que contém pelo menos **três** nós. Isso garante que, se um nó falhar, as regiões de dados HBase estão disponíveis em outros nós.

        > Se você estiver aprendendo HBase, sempre escolha 1 para o tamanho do cluster e excluir o cluster após cada utilização para reduzir o custo.

    - **Configuração opcional** - rede virtual configurar Azure, configurar ações de Script e adicionar contas de armazenamento adicional.

4. Clique em **criar**.

>[AZURE.NOTE] Depois que um cluster de HBase é excluído, você pode criar outro cluster HBase usando a mesma conta de armazenamento padrão e o contêiner de blob do padrão. O novo cluster selecionará as tabelas de HBase que você criou no cluster original. Para evitar inconsistências, recomendamos que você desative as tabelas de HBase antes de excluir o cluster.

## <a name="create-tables-and-insert-data"></a>Criar tabelas e inserir dados

Atualmente, há duas maneira de acessar HBase. Esta seção aborda usando o shell de HBase. A próxima seção aborda usando o SDK do .NET.

Para a maioria das pessoas, dados é exibida no formato tabular:

![hdinsight hbase os dados tabulares][img-hbase-sample-data-tabular]

Em HBase que é uma implementação de BigTable, os mesmos dados aparência:

![hdinsight hbase bigtable dados][img-hbase-sample-data-bigtable]

Ele vai faz mais sentido após concluir o procedimento a seguir.  

**Para usar o shell de HBase**

1. Use RDP para se conectar ao seu cluster HBase no HDInsight. Para as instruções de RDP, consulte [Gerenciar Hadoop clusters em HDInsight usando o Portal do Azure][hdinsight-manage-portal].
2. Dentro de sua sessão RDP, clique no atalho de **linha de comando do Hadoop** localizado na área de trabalho.
3. Abra o shell de HBase:

        cd %HBASE_HOME%\bin
        hbase shell

4. Crie um HBase com duas famílias de coluna:

        create 'Contacts', 'Personal', 'Office'
        list
5. Inserir alguns dados:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'

    ![hdinsight hadoop hbase shell][img-hbase-shell]

6. Obtenha uma única linha

        get 'Contacts', '1000'

    Você verá os mesmos resultados como usando o comando de verificação porque há apenas uma linha.

    Para obter mais informações sobre o esquema de tabela Hbase, consulte [Introdução ao Design de esquema de HBase][hbase-schema]. Para obter mais comandos HBase, consulte o [guia de referência de Apache HBase][hbase-quick-start].


6. Sair do shell

        exit

**Em massa carregar dados na tabela de HBase de contatos**

HBase inclui vários métodos de carregamento de dados em tabelas. Para obter mais informações, consulte [carregamento em massa](http://hbase.apache.org/book.html#arch.bulk.load).


Um arquivo de dados de exemplo é carregado em um contêiner de blob pública, wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt. O conteúdo do arquivo de dados é:

    8396    Calvin Raji     230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu        646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie        508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson    674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller   397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile    592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee       870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes      599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander 670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander   998-555-0171    230-555-0200    771 Northridge Drive

Você pode criar um arquivo de texto e carregue o arquivo à sua própria conta de armazenamento, se desejar. Para obter as instruções, consulte [carregar dados para trabalhos de Hadoop na HDInsight][hdinsight-upload-data].

> [AZURE.NOTE] Este procedimento usa a tabela de HBase de contatos criada no último procedimento.

1. Dentro de sua sessão RDP, clique no atalho de **linha de comando do Hadoop** localizado na área de trabalho.
2. Altere o diretório:

        cd %HBASE_HOME%\bin

3. Execute o seguinte comando para transformar o arquivo de dados para StoreFiles e armazenamento em um caminho relativo especificado pelo Dimporttsv.bulk.output:

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Execute o seguinte comando para carregar os dados de /example/data/storeDataFileOutput à tabela HBase:

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Você pode abrir o shell HBase e use o comando de verificação para listar o conteúdo da tabela.



## <a name="use-hive-to-query-hbase-tables"></a>Use a seção para tabelas de consulta de HBase

Você pode consultar dados armazenados em HBase usando a seção. Esta seção cria uma tabela de seção que mapeia para a tabela de HBase e usa para consultar os dados na tabela HBase.

**Para abrir o painel de cluster**

1. Navegue até **https://<HDInsight Cluster Name>.azurehdinsight.net/**.
5. Insira o nome de usuário da conta de usuário Hadoop e a senha. O nome de usuário padrão é **admin** e a senha é o que você inseriu durante o processo de criação. Abre uma nova guia do navegador.
6. Clique em **Editor de seção** na parte superior da página. O Editor de seção tem esta aparência:

    ![Painel de controle de cluster HDInsight.][img-hdinsight-hbase-hive-editor]

**Executar consultas de seção**

1. Inserir o seguinte script de HiveQL em Editor de seção e clique em **Enviar** para criar uma tabela de seção que mapeia para a tabela de HBase. Certifique-se de que você criou a tabela de exemplo mencionada anteriormente neste tutorial usando o shell de HBase antes de executar essa instrução.

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

    Aguarde até que as atualizações de **Status** **concluídas**.

2. Insira o seguinte script de HiveQL em Editor de seção e clique em **Enviar**. A consulta de seção consulta os dados na tabela HBase:

        SELECT count(*) FROM hbasecontacts;

4. Para recuperar os resultados da consulta seção, clique no link de **Exibir detalhes** na janela da **Sessão de trabalho** quando o trabalho execução concluída. Haverá apenas um arquivo de saída de trabalho porque você colocou um registro na tabela HBase.




**Para procurar o arquivo de saída**

1. No Console de consulta, clique em **Navegador de arquivo**.
2. Clique na conta de armazenamento do Azure que é usada como o sistema de arquivo padrão para o cluster HBase.
3. Clique no nome de cluster HBase. O contêiner de conta de armazenamento do Azure padrão usa o nome de cluster.
4. Clique em **usuário**e, em seguida, clique em **administrador**. (Isso é o nome de usuário do Hadoop.)
6. Clique no nome de trabalho com o horário da **Última modificação** que corresponde a hora quando a consulta Selecionar seção executou.
4. Clique em **stdout**. Salve o arquivo e abra o arquivo com o bloco de notas. Haverá um arquivo de saída.

    ![Navegador de arquivos do HDInsight HBase seção Editor][img-hdinsight-hbase-file-browser]

## <a name="use-the-net-hbase-rest-api-client-library"></a>Usar a biblioteca de cliente .NET HBase REST API

Você deve baixar a biblioteca de cliente API REST de HBase para .NET do GitHub e crie o projeto para que você possa usar o SDK do .NET HBase. O procedimento a seguir inclui as instruções para essa tarefa.

1. Crie um novo aplicativo c# Visual Studio Desktop Console do Windows.
2. Abra o NuGet Package Manager Console clicando em **Ferramentas** > **NuGet Package Manager** > **Package Manager Console**.
3. Execute o seguinte comando NuGet no console:

        Install-Package Microsoft.HBase.Client

5. Adicione as seguintes instruções de **uso** na parte superior do arquivo:

        using Microsoft.HBase.Client;
        using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Substitua a função **principal** com o seguinte:

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version.
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

            //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
            Scanner scanSettings = new Scanner()
            {
                batch = 10,
                startRow = BitConverter.GetBytes(25),
                endRow = BitConverter.GetBytes(35)
            };

            ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
            CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
            {
                foreach (CellSet.Row row in next.rows)
                {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
                }
            }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. Defina as três primeiras variáveis na função **principal** .
8. Pressione **F5** para executar o aplicativo.

## <a name="check-cluster-status"></a>Verificar o status de cluster

HBase em HDInsight vem com uma interface de usuário da Web para monitorar clusters. Usando a IU da Web, você pode solicitar estatísticas ou informações sobre regiões.

Para abrir a IU da Web, você deve RDP em cluster e, em seguida, clique no atalho HMaster informações Web UI na área de trabalho ou usa a seguinte URL em um navegador da web:

    http://zookeeper[0-2]:60010/master-status

Em um cluster de alta disponibilidade, você encontrará um link para o active HBase nó mestre atual que hospeda a IU da Web.

##<a name="delete-the-cluster"></a>Excluir o cluster
Para evitar inconsistências, recomendamos que você desative as tabelas de HBase antes de excluir o cluster.
[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="whats-next"></a>O que vem a seguir?
Neste tutorial de HBase para HDInsight, você aprendeu como criar um cluster de HBase e como criar tabelas e exibir os dados nessas tabelas do shell HBase. Você também aprendeu como usar uma consulta de seção em dados em tabelas de HBase e como usar as APIs REST HBase c# para criar uma tabela de HBase e recuperar dados da tabela.

Para obter mais informações, consulte:

- [Visão geral de HDInsight HBase][hdinsight-hbase-overview].
HBase é um Apache, código-fonte aberto, NoSQL banco de dados criado no Hadoop que fornece acesso aleatório e consistência forte para grandes quantidades de dados não estruturados e semi-estruturados.
- [Criar clusters de HBase em rede Virtual do Azure][hdinsight-hbase-provision-vnet].
Com a integração de rede virtual, HBase clusters podem ser implantados à mesma rede virtual como seus aplicativos para que os aplicativos possam se comunicar com HBase diretamente.
- [Replicação de configurar HBase em HDInsight](hdinsight-hbase-geo-replication.md). Saiba como configurar a replicação de HBase entre dois centros de dados Azure.
- [Analisar sentimento Twitter com HBase em HDInsight][hbase-twitter-sentiment].
Saiba como fazer [análise sentimento](http://en.wikipedia.org/wiki/Sentiment_analysis) em tempo real de dados grandes usando HBase em um cluster de Hadoop no HDInsight.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-bigtable.png
