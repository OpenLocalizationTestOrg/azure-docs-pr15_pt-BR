<properties
   pageTitle="Criar clusters de HDInsight com armazenamento de Lucerne de dados do Azure usando o PowerShell | Azure"
   description="Usar o PowerShell do Azure para criar e usar clusters HDInsight com Lucerne de dados do Azure"
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

# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-powershell"></a>Criar um cluster de HDInsight com armazenamento de Lucerne de dados usando o PowerShell do Azure

> [AZURE.SELECTOR]
- [Usando o Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Usando o PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)
- [Usando o Gerenciador de recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Aprenda a usar o PowerShell do Azure para configurar um cluster de HDInsight (Hadoop, HBase ou tempestade) com acesso ao repositório de Lucerne de dados do Azure. Algumas considerações importantes para esta versão:

* **Para Spark clusters (Linux) e clusters de Hadoop/tempestade (Windows e Linux)**, o armazenamento de dados Lucerne só pode ser usado como uma conta de armazenamento adicional. A conta de armazenamento padrão para esses clusters ainda será Blobs de armazenamento do Azure (WASB).

* **Para HBase clusters (Windows e Linux)**, o armazenamento de Lucerne de dados pode ser usado como um armazenamento padrão ou armazenamento adicional.

> [AZURE.NOTE] Alguns pontos importantes nota. 
> 
> * Opção para criar clusters de HDInsight com acesso ao repositório de Lucerne de dados está disponível somente para versões de HDInsight 3,2 e 3.4 (para Hadoop, HBase e tempestade clusters no Windows, bem como Linux). Para clusters Spark no Linux, esta opção só está disponível em HDInsight 3.4 clusters.
>
> * Conforme mencionado acima, armazenamento de dados de Lucerne está disponível como armazenamento padrão para alguns tipos de cluster (HBase) e armazenamento adicional para outros tipos de cluster (Hadoop, Spark, tempestade). Usar o armazenamento de Lucerne de dados como uma conta de armazenamento adicional não afeta o desempenho ou a capacidade de leitura/gravação ao armazenamento do cluster. Em um cenário onde os dados Lucerne Store é usado como armazenamento adicional, arquivos relacionados ao cluster (como logs, etc.) são gravados o armazenamento de padrão (Blobs do Azure), enquanto os dados que você deseja processar podem ser armazenados em uma conta de armazenamento de Lucerne de dados.


Neste artigo, vamos provisionar um cluster Hadoop com dados Lucerne Store como armazenamento adicional.

Configurando HDInsight para trabalhar com dados Lucerne repositório usando o PowerShell envolve as seguintes etapas:

* Criar um repositório de Lucerne de dados do Azure
* Configurar a autenticação baseada em função acesso aos dados Lucerne Store
* Criar HDInsight cluster com autenticação para armazenamento de Lucerne de dados
* Executar um trabalho de teste no cluster

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).

- **Azure PowerShell 1.0 ou maior**. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

- **SDK do Windows**. Você pode instalá-lo [aqui](https://dev.windows.com/en-us/downloads). Use isto para criar um certificado de segurança.

- **Principais de serviço do azure Active Directory**. As etapas deste tutorial fornecem instruções sobre como criar uma entidade de serviço no Azure AD. No entanto, você deve ser um administrador do Azure AD para poder criar uma entidade de segurança do serviço. Se você for um administrador do Azure AD, você pode ignorar esse pré-requisito e continuar com o tutorial.
    
    **Se você estiver não um administrador do Azure AD**, não será capaz de executar as etapas necessárias para criar uma entidade de segurança do serviço. Nesse caso, o administrador do Azure AD deve primeiro criar uma entidade de serviço antes de criar um cluster de HDInsight com armazenamento de Lucerne de dados. Além disso, o capital de serviço deve ser criado usando um certificado, conforme descrito em [criar um serviço principal com o certificado](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate). 


## <a name="create-an-azure-data-lake-store"></a>Criar um repositório de Lucerne de dados do Azure

Siga estas etapas para criar um repositório de Lucerne de dados.

1. Área de trabalho, abra uma nova janela do PowerShell do Azure e insira o trecho a seguir. Quando solicitado a fazer logon no, certifique-se de que você faça logon como um do proprietário da admininistrators assinatura:

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    >[AZURE.NOTE] Se você receber um erro semelhante ao `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` ao registrar o provedor de recursos de armazenamento de Lucerne de dados, é possível que seu subsrcription não está na lista branca para armazenamento de Lucerne de dados do Azure. Verifique se que você ativar sua assinatura do Azure para visualização pública dados Lucerne Store seguindo estas [instruções](data-lake-store-get-started-portal.md#signup).

3. Uma conta de armazenamento do Azure dados Lucerne está associada um grupo de recursos do Azure. Comece criando um grupo de recursos do Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Criar um grupo de recursos do Azure] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Criar um grupo de recursos do Azure")

2. Crie uma conta de armazenamento de Lucerne de dados do Azure. O nome da conta que você especifica deve conter apenas letras minúsculas e números.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Criar uma conta de Lucerne de dados do Azure] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Criar uma conta de Lucerne de dados do Azure")

3. Verifique se a conta foi criada com êxito.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    A saída para isso deve ser **verdadeiro**.

4. Carregue alguns dados de exemplo para Lucerne de dados do Azure. Usaremos isso neste artigo para verificar se os dados são acessíveis a partir de um cluster de HDInsight. Se você estiver procurando por alguns dados de exemplo carregar, você pode obter a pasta **Ambulância dados** do [Azure dados Lucerne gito repositório](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).


        $myrootdir = "/"
        Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Configurar a autenticação baseada em função acesso aos dados Lucerne Store

Cada assinatura do Azure está associada um Active Directory do Azure. Usuários e serviços que acessar os recursos da assinatura usando o Portal de clássico do Azure ou a API do Gerenciador de recursos do Azure devem primeiro autenticar com essa Azure Active Directory. Acesso a assinaturas do Azure e serviços designando-lhes a função adequada em um recurso Azure.  Para serviços, um serviço principal identifica o serviço no Azure Active Directory (AAD). Esta seção ilustra como conceder a um serviço de aplicativo, como HDInsight, acesso a um recurso Azure (a conta de armazenamento de Lucerne de dados do Azure que você criou anteriormente) criando um principal de serviço para o aplicativo e atribuindo funções a essa por meio do PowerShell do Azure.

Para configurar a autenticação do Active Directory do Azure Lucerne de dados, você deve executar as seguintes tarefas.

* Criar um certificado autoassinado
* Criar um aplicativo no Active Directory do Azure e uma entidade de serviço

### <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Verifique se que você tem o [Windows SDK](https://dev.windows.com/en-us/downloads) instalado antes de prosseguir com as etapas nesta seção. Você também deve ter criado um diretório, como **C:\mycertdir**, onde o certificado será criado.

1. Na janela do PowerShell, navegue até o local onde você instalou o SDK do Windows (normalmente, `C:\Program Files (x86)\Windows Kits\10\bin\x86` e use o [MakeCert] [ makecert] utilitário para criar um certificado autoassinado e uma chave privada. Use os seguintes comandos.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir
        $startDate = (Get-Date).ToString('MM/dd/yyyy')
        $endDate = (Get-Date).AddDays(365).ToString('MM/dd/yyyy')

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -b $startDate -e $endDate -r -len 2048

    Você será solicitado a digitar a senha de chave privada. Depois que o comando executa com êxito, você verá um **CertFile.cer** e **mykey.pvk** no diretório de certificado que você especificou.

4. Use o [Pvk2Pfx] [ pvk2pfx] utilitário para converter os arquivos. pvk e. cer que MakeCert criado em um arquivo. pfx. Execute o seguinte comando.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Quando solicitado insira a senha da chave privada especificados anteriormente. O valor especificado para o parâmetro de **OC** é a senha que está associada com o arquivo. pfx. Depois que o comando é concluído com êxito, você verá também uma CertFile.pfx no diretório de certificado que você especificou.

###  <a name="create-an-azure-active-directory-and-a-service-principal"></a>Criar um Active Directory do Azure e uma entidade de serviço

Nesta seção, você execute as etapas para criar um serviço principal para um aplicativo do Azure Active Directory, atribua uma função para o serviço principal e autenticar como o capital de serviço, fornecendo um certificado. Execute os seguintes comandos para criar um aplicativo do Azure Active Directory.

1. Cole os seguintes cmdlets na janela do console do PowerShell. Verifique se o valor especificado para a propriedade **- DisplayName** é exclusivo. Além disso, os valores para **A home page** e **- IdentiferUris** são valores de espaço reservado e não são verificados.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
                    -DisplayName "HDIADL" `
                    -HomePage "https://contoso.com" `
                    -IdentifierUris "https://mycontoso.com" `
                    -KeyValue $credential  `
                    -KeyType "AsymmetricX509Cert"  `
                    -KeyUsage "Verify"  `
                    -StartDate $startDate  `
                    -EndDate $endDate

        $applicationId = $application.ApplicationId

2. Criar uma entidade de serviço usando o ID de aplicativo.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id

3. Conceda acesso principal serviço para o arquivo/pasta de armazenamento de Lucerne de dados que você irá acessar do cluster HDInsight. O trecho a seguir fornece acesso à raiz da conta de armazenamento de Lucerne de dados.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All

    No prompt, digite **Y** para confirmar.

## <a name="create-an-hdinsight-cluster-with-authentication-to-data-lake-store"></a>Criar um cluster de HDInsight com autenticação para armazenamento de Lucerne de dados

Nesta seção, podemos criar um cluster de HDInsight Hadoop. Nesta versão, o cluster HDInsight e o repositório de Lucerne de dados devem ser no mesmo local (Leste dos EUA 2).

1. Iniciar com recuperar a ID do locatário de assinatura. Você precisará que posteriormente.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. Nesta versão, para um cluster de Hadoop, dados Lucerne Store só pode ser usado como um armazenamento adicional para o cluster. O armazenamento padrão ainda será os blobs de armazenamento do Azure (WASB). Portanto, vamos primeiro criar a conta de armazenamento e contêineres de armazenamento necessários para o cluster.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name

        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $containerName -Context $destContext

3. Crie o cluster HDInsight. Use os seguintes cmdlets.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $rdpCredentials = Get-Credential

        New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.2" -RdpCredential $rdpCredentials -RdpAccessExpiry (Get-Date).AddDays(14) -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Depois que o cmdlet for concluído com êxito, você deverá ver uma saída como esta:

        Name                      : hdiadlcluster
        Id                        : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/hdiadlgroup/providers/Mi
                                    crosoft.HDInsight/clusters/hdiadlcluster
        Location                  : East US 2
        ClusterVersion            : 3.2.7.707
        OperatingSystemType       : Windows
        ClusterState              : Running
        ClusterType               : Hadoop
        CoresUsed                 : 16
        HttpEndpoint              : hdiadlcluster.azurehdinsight.net
        Error                     :
        DefaultStorageAccount     :
        DefaultStorageContainer   :
        ResourceGroup             : hdiadlgroup
        AdditionalStorageAccounts :

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Executar trabalhos de teste no cluster HDInsight para usar o armazenamento de Lucerne de dados

Depois que você configurou um cluster de HDInsight, você pode executar trabalhos de teste no cluster para testar que o cluster HDInsight pode acessar dados Lucerne Store. Para fazer isso, vamos executar um trabalho de seção de exemplo que cria uma tabela usando os dados de exemplo que você carregou anteriormente em seu armazenamento de Lucerne de dados.

### <a name="for-a-linux-cluster"></a>Para um cluster de Linux

Nesta seção, você irá SSH para o cluster e executar a um exemplo de consulta de seção. Windows não oferece um cliente SSH interno. Recomendamos o uso de **Acabamento**, que pode ser baixado em [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obter mais informações sobre como usar o acabamento, consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Uma vez conectado, inicie a CLI seção usando o comando a seguir:

        hive

2. Usando a CLI, insira as instruções a seguir para criar uma nova tabela denominada **veículos** , usando os dados de exemplo na loja de Lucerne de dados:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    Você deve ver uma saída semelhante à seguinte:

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


### <a name="for-a-windows-cluster"></a>Para um cluster do Windows

Use os seguintes cmdlets para executar a consulta de seção. Nessa consulta podemos criar uma tabela de dados no repositório de Lucerne dados e execute uma consulta seleção na tabela criada.

    $queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"

    $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

    $hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

    Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials

Isso terá a seguinte saída. **ExitValue** 0 na saída sugere que o trabalho concluído com êxito.

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

Recupere a saída do trabalho usando o seguinte cmdlet:

    Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials

A saída de trabalho semelhante à seguinte:

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


## <a name="access-data-lake-store-using-hdfs-commands"></a>Acessar dados Lucerne Store usando os comandos HDFS

Depois de ter configurado o cluster HDInsight para usar dados Lucerne Store, você pode usar os comandos do shell HDFS para acessar o repositório.

### <a name="for-a-linux-cluster"></a>Para um cluster de Linux

Nesta seção, você irá SSH em cluster e executar os comandos HDFS. Windows não oferece um cliente SSH interno. Recomendamos o uso de **Acabamento**, que pode ser baixado em [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obter mais informações sobre como usar o acabamento, consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Uma vez conectado, use o seguinte comando de sistema de arquivos HDFS para listar os arquivos no repositório Lucerne dados.

    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Isso deve listar o arquivo que você carregou anteriormente ao armazenamento de Lucerne de dados.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

Você também pode usar o `hdfs dfs -put` comando carregar alguns arquivos ao armazenamento de Lucerne de dados e depois use `hdfs dfs -ls` para verificar se os arquivos foram carregados com êxito.


### <a name="for-a-windows-cluster"></a>Para um cluster do Windows

1. Entrar novo [Portal Azure](https://portal.azure.com).

2. Clique em **Procurar**, clique em **clusters de HDInsight**e clique em cluster HDInsight que você criou.

3. Na lâmina cluster, clique em **Área de trabalho remota**e na lâmina **Área de trabalho remota** , clique em **Conectar**.

    ![Remoto em cluster HDI] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png "Criar um grupo de recursos do Azure")

    Quando solicitado, digite as credenciais fornecidas para o usuário de desktop remoto.

4. Na sessão remota, iniciar o Windows PowerShell e use os comandos de sistema de arquivos HDFS para listar os arquivos no repositório de Lucerne de dados do Azure.

        hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

    Isso deve listar o arquivo que você carregou anteriormente ao armazenamento de Lucerne de dados.

        15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
        Found 1 items
        -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv

    Você também pode usar o `hdfs dfs -put` comando carregar alguns arquivos ao armazenamento de Lucerne de dados e depois use `hdfs dfs -ls` para verificar se os arquivos foram carregados com êxito.

## <a name="see-also"></a>Consulte também

* [Portal: Criar um cluster de HDInsight para usar dados Lucerne Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
