<properties
    pageTitle="Tutorial HBase: Introdução ao clusters baseados em Linux HBase em Hadoop | Microsoft Azure"
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
    ms.topic="get-started-article"
    ms.date="10/19/2016"
    ms.author="jgao"/>



# <a name="hbase-tutorial-get-started-using-apache-hbase-with-linux-based-hadoop-in-hdinsight"></a>Tutorial HBase: começar a usar o Apache HBase com baseado em Linux Hadoop em HDInsight 

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Aprenda a criar um cluster de HBase no HDInsight, criar tabelas de HBase e consultar tabelas usando a seção. Para obter informações gerais do HBase, consulte [Visão geral de HDInsight HBase][hdinsight-hbase-overview].

As informações neste documento são específicas para clusters HDInsight baseados em Linux. Para obter informações sobre clusters baseados no Windows, use o seletor de tabulação no topo da página para alternar.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial HBase, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Proteger Shell(SSH)](hdinsight-hadoop-linux-use-ssh-unix.md). 
- [curl](http://curl.haxx.se/download.html).

### <a name="access-control-requirements"></a>Requisitos de controle de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>Criar HBase cluster

O procedimento a seguir usa um modelo do Gerenciador de recursos do Azure para criar um cluster de HBase baseados em Linux versão 3.4 e a conta de armazenamento do Azure de padrão dependente. Para entender os parâmetros usados no procedimento e outros métodos de criação de cluster, consulte [clusters baseados em Linux criar Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem a seguir para abrir o modelo no portal do Azure. O modelo está localizado em um contêiner de blob pública. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Da lâmina **implantação personalizada** , insira o seguinte:

    - **Assinatura**: selecione a sua assinatura do Azure que será usada para criar o cluster.
    - **Grupo de recursos**: criar um novo grupo de gerenciamento de recursos do Azure ou use um existente.
    - **Local**: especificar o local do grupo de recursos. 
    - **ClusterName**: insira um nome para o cluster HBase que será criado.
    - **Nome de login de cluster e de senha**: O nome de login padrão é **admin**.
    - **E SSH senha**: O nome de usuário padrão é **sshuser**.  Você pode renomeá-la.
     
    Outros parâmetros são opcionais.  
    
    Cada cluster possui uma dependência de conta de armazenamento de Blob do Azure. Depois de excluir um cluster, os dados retém na conta de armazenamento. O nome de conta de armazenamento do cluster padrão é o nome de cluster com "store" acrescentado. É codificada na seção de variáveis do modelo.
        
3. Selecione **eu concordar com os termos e condições mencionadas acima**e, em seguida, clique em **comprar**. Ele leva cerca de 20 minutos para criar um cluster.


>[AZURE.NOTE] Depois que um cluster de HBase é excluído, você pode criar outro cluster HBase usando o mesmo contêiner de blob do padrão. O novo cluster selecionará as tabelas de HBase que você criou no cluster original. Para evitar inconsistências, recomendamos que você desative as tabelas de HBase antes de excluir o cluster.

## <a name="create-tables-and-insert-data"></a>Criar tabelas e inserir dados

Você pode usar SSH conectem HBase clusters e, em seguida, usar o Shell de HBase para criar tabelas de HBase, inserir dados e dados de consulta. Para obter informações sobre como usar o SSH, consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md) e [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
 

Para a maioria das pessoas, dados é exibida no formato tabular:

![Dados tabulares HDInsight HBase][img-hbase-sample-data-tabular]

Em HBase que é uma implementação de BigTable, os mesmos dados aparência:

![HDInsight HBase bigtable dados][img-hbase-sample-data-bigtable]

Ele fará mais sentido após concluir o procedimento a seguir.  


**Para usar o shell de HBase**

1. A partir do SSH, execute o seguinte comando:

        hbase shell

4. Crie um HBase com famílias de duas colunas:

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

    Para obter mais informações sobre o esquema de tabela HBase, consulte [Introdução ao Design de esquema de HBase][hbase-schema]. Para obter mais comandos HBase, consulte o [guia de referência de Apache HBase][hbase-quick-start].

6. Sair do shell

        exit



**Em massa carregar dados na tabela de HBase de contatos**

HBase inclui vários métodos de carregamento de dados em tabelas.  Para obter mais informações, consulte [carregamento em massa](http://hbase.apache.org/book.html#arch.bulk.load).


Um arquivo de dados de exemplo é carregado em um contêiner de blob pública, *wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*.  O conteúdo do arquivo de dados é:

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

> [AZURE.NOTE] Este procedimento usa a tabela de HBase de contatos que você criou no último procedimento.

1. A partir do SSH, execute o seguinte comando para transformar o arquivo de dados para StoreFiles e armazenamento em um caminho relativo especificado pelo Dimporttsv.bulk.output:.  Se você estiver no Shell de HBase, use o comando Sair para sair.

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Execute o seguinte comando para carregar os dados de /example/data/storeDataFileOutput à tabela HBase:

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Você pode abrir o shell HBase e use o comando de verificação para listar o conteúdo da tabela.



## <a name="use-hive-to-query-hbase"></a>Usar a seção a consulta HBase

Você pode consultar dados em tabelas de HBase usando a seção. Esta seção cria uma tabela de seção que mapeia para a tabela de HBase e usa para consultar os dados na tabela HBase.

1. Abra **Acabamento**e conectar-se ao cluster.  Consulte as instruções no procedimento anterior.
2. Abra o shell de seção.

       hive
3. Execute o seguinte script de HiveQL para criar uma tabela de seção que mapeia para a tabela de HBase. Certifique-se de que criou a tabela de exemplo mencionada anteriormente neste tutorial usando o shell de HBase antes de executar essa instrução.

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. Execute o seguinte script de HiveQL para consultar os dados na tabela HBase:

        SELECT count(*) FROM hbasecontacts;

## <a name="use-hbase-rest-apis-using-curl"></a>Usar APIs REST de HBase usando ondulação

> [AZURE.NOTE] Ao usar Ondulação ou qualquer outro tipo de comunicação do resto com WebHCat, você deve autenticar as solicitações, fornecendo o nome de usuário e senha para o administrador de cluster HDInsight. Você também deve usar o nome de cluster como parte do URI Uniform Resource Identifier () usado para enviar as solicitações para o servidor.
>
> Para os comandos nesta seção, substitua o **nome de usuário** com o usuário para autenticar ao cluster e **senha** com a senha da conta de usuário. Substitua o nome do seu cluster **CLUSTERNAME** .
>
> A API REST é protegida por meio de [autenticação básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Você sempre deve fazer solicitações usando Secure HTTP (HTTPS) para ajudar a garantir que suas credenciais são enviadas com segurança no servidor.

1. Uma linha de comando, use o seguinte comando para verificar se você pode se conectar ao cluster HDInsight:

        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

    Você deve receber uma resposta semelhante à seguinte:

        {"status":"ok","version":"v1"}

    Os parâmetros usados neste comando são da seguinte maneira:

    * **-u** - o nome de usuário e senha usados para autenticar a solicitação.
    * **-G** - indica que esta é uma solicitação GET.

2. Use o seguinte comando para listar as tabelas de HBase existentes:

        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. Use o seguinte comando para criar uma nova tabela HBase com duas famílias de coluna:

        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
        -v

    O esquema é fornecido no formato JSon.

4. Use o seguinte comando para inserir alguns dados:

        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"Row\":{\"key\":\"MTAwMA==\",\"Cell\":{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}}}" \
        -v

    Você deve na Base 64 codificar os valores especificados na opção -d.  Em exmaple:

    - MTAwMA = =: 1000
    - UGVyc29uYWw6TmFtZQ = =: pessoal: nome
    - Sm9obiBEb2xl: John Dole

    [Falso-linha-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) permite que você insira vários valor (em lote).

5. Use o comando a seguir para obter uma linha:

        curl -u <UserName>:<Password> \
        -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
        -H "Accept: application/json" \
        -v

Para obter mais informações sobre o HBase Rest, consulte [O guia de referência de HBase Apache](https://hbase.apache.org/book.html#_rest).

## <a name="check-cluster-status"></a>Verificar o status de cluster

HBase em HDInsight vem com uma interface de usuário da Web para monitorar clusters. Usando a IU da Web, você pode solicitar estatísticas ou informações sobre regiões.

SSH também pode ser usado para solicitações de locais, como solicitações da web, ao cluster HDInsight de túnel. A solicitação, em seguida, serão ser roteada para o recurso solicitado, como se ela tinha se originou no nó HDInsight cluster principal. Para obter mais informações, consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel).

**Para estabelecer uma sessão de túnel de SSH**

1. Abra o **Acabamento**.  
2. Se você tiver fornecido uma chave SSH quando você criou sua conta de usuário durante o processo de criação, você deve executar o seguinte procedimento para selecionar a chave privada utilizar ao autenticar ao cluster:

    Na **categoria**, expanda a **Conexão**, expanda **SSH**e selecione **Auth**. Por fim, clique em **Procurar** e selecione o arquivo de .ppk que contém sua chave privada.

3. Em **categoria**, clique em **sessão**.
4. Entre as opções básicas para a tela de sessão de acabamento, insira os seguintes valores:

    - **Nome do host**: o endereço SSH do seu campo de servidor no nome do Host (ou endereço IP) HDInsight. O endereço SSH é o nome do seu cluster, em seguida, **-ssh.azurehdinsight.net**. Por exemplo, *meucluster-ssh.azurehdinsight.net*.
    - **Porta**: 22. A porta no headnode primária ssh é 22.  
5. Na seção **categoria** à esquerda da caixa de diálogo, expanda a **Conexão**, expanda **SSH**e clique em **túneis**.
6. Forneça as seguintes informações sobre as opções de controle de formulário de encaminhamento de porta SSH:

    - **Porta de origem** - a porta no cliente que você deseja encaminhar. Por exemplo, 9876.
    - **Dinâmico** - proxy SOCKS dinâmico permite que o roteamento.
7. Clique em **Adicionar** para adicionar as configurações.
8. Clique em **Abrir** na parte inferior da caixa de diálogo para abrir uma conexão SSH.
9. Quando solicitado, faça logon no servidor usando uma conta SSH. Isso estabelecer uma sessão SSH e habilitar o túnel.

**Para localizar o FQDN dos zookeepers usando Ambari**

1. Navegue até https://<ClusterName>.azurehdinsight.net/.
2. Insira suas credenciais de conta de usuário de cluster duas vezes.
3. No menu à esquerda, clique em **zookeeper**.
4. Clique em um dos três links **ZooKeeper Server** na lista de resumo.
5. Copie o **nome do host**. Por exemplo, zk0-CLUSTERNAME.xxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net.

**Para configurar um programa de cliente (Firefox) e verificar o status de cluster**

1. Abra o Firefox.
2. Clique no botão de **Menu abrir** .
3. Clique em **Opções**.
4. Clique em **Avançado**, clique em **rede**e, em seguida, clique em **configurações**.
5. Selecione **a configuração de proxy Manual**.
6. Insira os seguintes valores:

    - **Host Socks**: host local
    - **Porta**: usar a mesma porta que você configurou no acabamento SSH túnel.  Por exemplo, 9876.
    - **SOCKS v5**: (selecionado)
    - **DNS remoto**: (selecionado)
7. Clique em **Okey** para salvar as alterações.
8. Navegue até http://&lt;o FQDN de uma ZooKeeper >: mestre/60010-status.

Em um cluster de alta disponibilidade, você encontrará um link para o active HBase nó mestre atual que hospeda a IU da Web.

##<a name="delete-the-cluster"></a>Excluir o cluster

Para evitar inconsistências, recomendamos que você desative as tabelas de HBase antes de excluir o cluster.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial de HBase para HDInsight, você aprendeu como criar um cluster de HBase e como criar tabelas e exibir os dados nessas tabelas do shell HBase. Você também aprendeu como usar uma consulta de seção em dados em tabelas de HBase e como usar as APIs REST HBase c# para criar uma tabela de HBase e recuperar dados da tabela.

Para saber mais, consulte:

- [Visão geral de HDInsight HBase][hdinsight-hbase-overview]: HBase é um Apache, código-fonte aberto, NoSQL banco de dados criado no Hadoop que fornece acesso aleatório e consistência forte para grandes quantidades de dados não estruturados e semi-estruturados.


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
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png
