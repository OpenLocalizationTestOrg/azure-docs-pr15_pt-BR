<properties
   pageTitle="Introdução ao armazenamento de dados de Lucerne | Azure"
   description="Usar o PowerShell do Azure para criar uma conta de armazenamento de Lucerne de dados e executar operações básicas"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/04/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Introdução ao armazenamento de Lucerne de dados do Azure usando o PowerShell do Azure

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node](data-lake-store-manage-use-nodejs.md)

Aprenda a usar o PowerShell do Azure para criar uma conta de armazenamento de Lucerne de dados do Azure e executar operações básicas, tais como criar pastas, carregar e baixar arquivos de dados, excluir sua conta, etc. Para obter mais informações sobre armazenamento de Lucerne de dados, consulte [Visão geral da Lucerne armazenamento de dados](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

* **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).

* **Azure PowerShell 1.0 ou maior**. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

## <a name="authentication"></a>Autenticação

Este artigo usa uma abordagem de autenticação mais simples com dados Lucerne repositório onde você será solicitado a inserir suas credenciais de conta do Microsoft Azure. O nível de acesso a dados Lucerne repositório conta e sistema de arquivos, em seguida, é regulado pelo nível de acesso do usuário conectado. No entanto, há outras abordagens também para autenticar com dados Lucerne Store, que são a **autenticação do usuário final** ou **ao serviço**. Para obter instruções e mais informações sobre como se autenticar, consulte [autenticar com armazenamento de Lucerne de dados usando o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-an-azure-data-lake-store-account"></a>Criar uma conta de armazenamento de Lucerne de dados do Azure

1. Área de trabalho, abra uma nova janela do Windows PowerShell e insira o trecho a seguir para fazer logon sua conta do Azure, definir a assinatura e registrar o provedor de dados Lucerne Store. Quando solicitado a fazer logon no, certifique-se de que você faça logon como um do proprietário da admininistrators assinatura:

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"


2. Uma conta de armazenamento do Azure dados Lucerne está associada um grupo de recursos do Azure. Comece criando um grupo de recursos do Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Criar um grupo de recursos do Azure] (./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Criar um grupo de recursos do Azure")

2. Crie uma conta de armazenamento de Lucerne de dados do Azure. O nome que você especificar deve conter apenas letras minúsculas e números.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Criar uma conta de armazenamento de Lucerne de dados do Azure] (./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Criar uma conta de armazenamento de Lucerne de dados do Azure")

3. Verifique se a conta foi criada com êxito.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    A saída para isso deve ser **verdadeiro**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Criar estruturas de diretório em seu armazenamento de Lucerne de dados do Azure

Você pode criar diretórios em sua conta de armazenamento de Lucerne de dados do Azure para gerenciar e armazenar dados.

1. Especifique um diretório raiz.

        $myrootdir = "/"

2. Crie um novo diretório chamado **mynewdirectory** sob a raiz especificada.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory

3. Verifique se que o novo diretório é criado com êxito.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Ele deve mostrar uma saída semelhante ao seguinte:

    ![Verifique se o diretório] (./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verifique se o diretório")


## <a name="upload-data-to-your-azure-data-lake-store"></a>Carregar dados em seu armazenamento de Lucerne de dados do Azure

Você pode carregar seus dados para armazenamento de Lucerne dados diretamente no nível raiz ou para uma pasta que você criou dentro da conta. Os trechos de código a seguintes demonstram como carregar alguns dados de exemplo para o diretório (**mynewdirectory**) que você criou na seção anterior.

Se você estiver procurando por alguns dados de exemplo carregar, você pode obter a pasta **Ambulância dados** do [Azure dados Lucerne gito repositório](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Baixar o arquivo e armazená-lo em um diretório local no seu computador, como C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Renomear, baixar e excluir dados do seu armazenamento de Lucerne de dados

Para renomear um arquivo, use o seguinte comando:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Para baixar um arquivo, use o seguinte comando:

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Para excluir um arquivo, use o seguinte comando:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Quando solicitado, digite **Y** para excluir o item. Se você tiver mais de um arquivo a ser excluído, você pode fornecer todos os caminhos separados por vírgula.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Excluir sua conta de armazenamento de Lucerne de dados do Azure

Use o seguinte comando para excluir sua conta de armazenamento de Lucerne de dados.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Quando solicitado, digite **Y** para excluir a conta.


## <a name="next-steps"></a>Próximas etapas

- [Proteger os dados no repositório de Lucerne de dados](data-lake-store-secure-data.md)
- [Usar a análise de dados Azure Lucerne com dados Lucerne Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com dados Lucerne Store](data-lake-store-hdinsight-hadoop-use-portal.md)
