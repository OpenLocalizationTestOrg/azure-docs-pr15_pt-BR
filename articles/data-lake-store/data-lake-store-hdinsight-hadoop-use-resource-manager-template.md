<properties
   pageTitle="Criar clusters de HDInsight com armazenamento de Lucerne de dados do Azure usando o recurso Gerenciador de modelos | Microsoft Azure"
   description="Use o Gerenciador de recursos do Azure modelos para criar e usar clusters HDInsight com armazenamento de Lucerne de dados do Azure"
   services="data-lake-store,hdinsight"
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
   ms.date="10/21/2016"
   ms.author="nitinme"/>

# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-resource-manager-template"></a>Criar um cluster de HDInsight com armazenamento de Lucerne de dados usando o modelo do Gerenciador de recursos do Azure

> [AZURE.SELECTOR] - [Usando o Portal](data-lake-store-hdinsight-hadoop-use-portal.md) - [usando o PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md) - [usando o Gerenciador de recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Saiba como usar um modelo do Gerenciador de recursos do Azure para configurar um cluster de HDInsight com acesso ao repositório de Lucerne de dados do Azure. Algumas considerações importantes para esta versão:

-   **Para Spark clusters (Linux) e clusters de Hadoop/tempestade (Windows e Linux)**, o armazenamento de dados Lucerne só pode ser usado como uma conta de armazenamento adicional. A conta de armazenamento padrão para esses clusters ainda será Blobs de armazenamento do Azure (WASB).

-   **Para HBase clusters (Windows e Linux)**, o armazenamento de Lucerne de dados pode ser usado como um armazenamento padrão ou armazenamento adicional.

> [AZURE.NOTE] Alguns pontos importantes nota.
>
> - Opção para criar clusters de HDInsight com acesso ao repositório de Lucerne de dados está disponível somente para versões de HDInsight 3,2 e 3.4 (para Hadoop, HBase e tempestade clusters no Windows, bem como Linux). Para clusters Spark no Linux, esta opção só está disponível em HDInsight 3.4 clusters.
>
> - Conforme mencionado acima, armazenamento de dados de Lucerne está disponível como armazenamento padrão para alguns tipos de cluster (HBase) e armazenamento adicional para outros tipos de cluster (Hadoop, Spark, tempestade). Usar o armazenamento de Lucerne de dados como uma conta de armazenamento adicional não afeta o desempenho ou a capacidade de leitura/gravação ao armazenamento do cluster. Em um cenário onde os dados Lucerne Store é usado como armazenamento adicional, arquivos relacionados ao cluster (como logs, etc.) são gravados o armazenamento de padrão (Blobs do Azure), enquanto os dados que você deseja processar podem ser armazenados em uma conta de armazenamento de Lucerne de dados.
>

Neste artigo, vamos provisionar um cluster Hadoop com dados Lucerne Store como armazenamento adicional.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

-   **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).

-   **Azure PowerShell 1.0 ou maior**. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

- **Principais de serviço do azure Active Directory**. As etapas deste tutorial fornecem instruções sobre como criar uma entidade de serviço no Azure AD. No entanto, você deve ser um administrador do Azure AD para poder criar uma entidade de segurança do serviço. Se você for um administrador do Azure AD, você pode ignorar esse pré-requisito e continuar com o tutorial.
    
    **Se você estiver não um administrador do Azure AD**, não será capaz de executar as etapas necessárias para criar uma entidade de segurança do serviço. Nesse caso, o administrador do Azure AD deve primeiro criar uma entidade de serviço antes de criar um cluster de HDInsight com armazenamento de Lucerne de dados. Além disso, o capital de serviço deve ser criado usando um certificado, conforme descrito em [criar um serviço principal com o certificado](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).

## <a name="create-an-hdinsight-cluster-with-azure-data-lake-store"></a>Criar um cluster de HDInsight com armazenamento de Lucerne de dados do Azure

O modelo do Gerenciador de recursos e os pré-requisitos para usar o modelo, estão disponíveis no GitHub em [implantar um cluster de HDInsight Linux com novo armazenamento de Lucerne de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Siga as instruções fornecidas neste link para criar um cluster de HDInsight com o Azure dados Lucerne Store como o armazenamento adicional.

As instruções no link mencionados acima exigem PowerShell. Antes de começar com essas instruções, certifique-se de que você faça logon sua conta do Azure. Área de trabalho, abra uma nova janela do PowerShell do Azure e insira os trechos de código a seguintes. Quando solicitado a fazer logon no, certifique-se de que você faça logon como um do proprietário da admininistrators assinatura:

```
# Log in to your Azure account
Login-AzureRmAccount

# List all the subscriptions associated to your account
Get-AzureRmSubscription

# Select a subscription
Set-AzureRmContext -SubscriptionId <subscription ID>
```

## <a name="upload-sample-data-to-the-azure-data-lake-store"></a>Carregar dados de exemplo no repositório de Lucerne de dados do Azure

O modelo do Gerenciador de recursos cria uma nova conta de armazenamento de Lucerne de dados e associa cluster HDInsight. Agora você deve carregar alguns dados de exemplo para a loja de Lucerne de dados. Você precisará esses dados posteriormente no tutorial para executar trabalhos de um cluster de HDInsight com acesso a dados no repositório de Lucerne dados. Para obter instruções sobre como carregar dados, consulte [carregar um arquivo para seu armazenamento de Lucerne de dados](data-lake-store-get-started-portal.md#uploaddata). Se você estiver procurando por alguns dados de exemplo carregar, você pode obter a pasta **Ambulância dados** do [Azure dados Lucerne gito repositório](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Defina ACLs relevantes nos dados de exemplo

Para garantir que os dados de exemplo que você carregar são acessíveis do cluster HDInsight, você deve garantir que o aplicativo do Azure AD que é usado para estabelecer a identidade entre o cluster HDInsight e armazenamento de dados de Lucerne tem acesso ao arquivo/pasta que você está tentando acessar. Para fazer isso, execute as seguintes etapas.

1.  Localize o nome do aplicativo Azure AD que está associado a cluster de HDInsight e o repositório de Lucerne de dados. Uma maneira para procurar o nome é abrir o blade de cluster HDInsight que você criou usando o modelo do Gerenciador de recursos, clique na guia **Cluster AAD identidade** e procure o valor de **Nome de exibição Principal do serviço**.

2.  Agora, fornece acesso a esse aplicativo do Azure AD no arquivo/pasta que você deseja acessar do cluster HDInsight. Para definir as ACLs direita no arquivo/pasta no repositório de Lucerne de dados, consulte [Protegendo dados dados Lucerne Store](data-lake-store-secure-data.md#assign-users-or-security-group-as-acls-to-the-azure-data-lake-store-file-system).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Executar trabalhos de teste no cluster HDInsight para usar o armazenamento de Lucerne de dados

Depois que você configurou um cluster de HDInsight, você pode executar trabalhos de teste no cluster para testar que o cluster HDInsight pode acessar dados Lucerne Store. Para fazer isso, vamos executar um trabalho de seção de exemplo que cria uma tabela usando os dados de exemplo que você carregou anteriormente em seu armazenamento de Lucerne de dados.

### <a name="for-a-linux-cluster"></a>Para um cluster de Linux

Nesta seção, você irá SSH para o cluster e executar a um exemplo de consulta de seção. Windows não oferece um cliente SSH interno. Recomendamos o uso de **Acabamento**, que pode ser baixado em [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obter mais informações sobre como usar o acabamento, consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1.  Uma vez conectado, inicie a CLI seção usando o comando a seguir:

    ```
    hive
    ```

2.  Usando a CLI, insira as instruções a seguir para criar uma nova tabela denominada **veículos** , usando os dados de exemplo na loja de Lucerne de dados:

    ```
    DROP TABLE vehicles;
    CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
    SELECT * FROM vehicles LIMIT 10;
    ```

    Você deve ver uma saída semelhante à seguinte:

    ```
    1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
    1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
    1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
    1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
    1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
    1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
    1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
    1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
    1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
    1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
    ```

### <a name="for-a-windows-cluster"></a>Para um cluster do Windows

Use os seguintes cmdlets para executar a consulta de seção. Nessa consulta podemos criar uma tabela de dados no repositório de Lucerne dados e execute uma consulta seleção na tabela criada.

```
$queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"

$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

$hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials
```

Isso terá a seguinte saída. **ExitValue** 0 na saída sugere que o trabalho concluído com êxito.

```
Cluster         : hdiadlcluster.
HttpEndpoint    : hdiadlcluster.azurehdinsight.net
State           : SUCCEEDED
JobId           : job_1445386885331_0012
ParentId        :
PercentComplete :
ExitValue       : 0
User            : admin
Callback        :
Completed       : done
```

Recupere a saída do trabalho usando o seguinte cmdlet:

```
Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials
```

A saída de trabalho semelhante à seguinte:

```
1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
```

## <a name="access-data-lake-store-using-hdfs-commands"></a>Acessar dados Lucerne Store usando os comandos HDFS

Depois de ter configurado o cluster HDInsight para usar dados Lucerne Store, você pode usar os comandos do shell HDFS para acessar o repositório.

### <a name="for-a-linux-cluster"></a>Para um cluster de Linux

Nesta seção, você irá SSH em cluster e executar os comandos HDFS. Windows não oferece um cliente SSH interno. Recomendamos o uso de **Acabamento**, que pode ser baixado em [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obter mais informações sobre como usar o acabamento, consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Uma vez conectado, use o seguinte comando de sistema de arquivos HDFS para listar os arquivos no repositório Lucerne dados.

```
hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
```

Isso deve listar o arquivo que você carregou anteriormente ao armazenamento de Lucerne de dados.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder
```

Você também pode usar o `hdfs dfs -put` comando carregar alguns arquivos ao armazenamento de Lucerne de dados e depois use `hdfs dfs -ls` para verificar se os arquivos foram carregados com êxito.

### <a name="for-a-windows-cluster"></a>Para um cluster do Windows

1.  Entrar novo [Portal Azure](https://portal.azure.com).

2.  Clique em **Procurar**, clique em **clusters de HDInsight**e clique em cluster HDInsight que você criou.

3.  Na lâmina cluster, clique em **Área de trabalho remota**e na lâmina **Área de trabalho remota** , clique em **Conectar**.

    ![Remoto em cluster HDI](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png)

    Quando solicitado, digite as credenciais fornecidas para o usuário de desktop remoto.

4.  Na sessão remota, iniciar o Windows PowerShell e use os comandos de sistema de arquivos HDFS para listar os arquivos no repositório de Lucerne de dados do Azure.

    ```
    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
    ```

    Isso deve listar o arquivo que você carregou anteriormente ao armazenamento de Lucerne de dados.

    ```
    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv
    ```

    Você também pode usar o `hdfs dfs -put` comando carregar alguns arquivos ao armazenamento de Lucerne de dados e depois use `hdfs dfs -ls` para verificar se os arquivos foram carregados com êxito.

## <a name="next-steps"></a>Próximas etapas

-   [Copiar dados de Blobs de armazenamento do Azure para armazenamento de Lucerne de dados](data-lake-store-copy-data-wasb-distcp.md)
