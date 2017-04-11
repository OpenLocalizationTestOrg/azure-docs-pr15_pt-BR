<properties
   pageTitle="Introdução ao armazenamento de Lucerne de dados usando a interface de linha de comando de plataforma híbrida | Microsoft Azure"
   description="Usar a linha de comando do Azure entre plataformas para criar uma conta de armazenamento de Lucerne de dados e executar operações básicas"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-command-line"></a>Introdução ao armazenamento de Lucerne de dados do Azure usando linha de comando do Azure

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node](data-lake-store-manage-use-nodejs.md)

Aprenda a usar o Azure interface de linha de comando para criar uma conta de armazenamento de Lucerne de dados do Azure e executar operações básicas, tais como criar pastas, carregar e baixar arquivos de dados, excluir sua conta, etc. Para obter mais informações sobre armazenamento de Lucerne de dados, consulte [Visão geral da Lucerne armazenamento de dados](data-lake-store-overview.md).

A CLI do Azure está implementada Node. Ele pode ser usado em qualquer plataforma que ofereça suporte Node, incluindo Windows, Mac e Linux. O Azure é abrir origem. O código-fonte é gerenciado no GitHub em <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. Este artigo aborda usando somente o CLI Azure com armazenamento de Lucerne de dados. Para um guia geral sobre como usar o Azure CLI, consulte [como usar a CLI Azure] [azure-command-line-tools].


##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).

- **Azure CLI** - consulte [instalar e configurar o CLI Azure](../xplat-cli-install.md) para obter informações de instalação e configuração. Certifique-se de que você reinicie o computador depois de instalar a CLI.

## <a name="authentication"></a>Autenticação

Este artigo usa uma abordagem de autenticação mais simples com dados Lucerne loja em que você faça logon como um usuário do usuário final. O nível de acesso a dados Lucerne repositório conta e sistema de arquivos, em seguida, é regulado pelo nível de acesso do usuário conectado. No entanto, há outras abordagens também para autenticar com dados Lucerne Store, que são a **autenticação do usuário final** ou **ao serviço**. Para obter instruções e mais informações sobre como se autenticar, consulte [autenticar com armazenamento de Lucerne de dados usando o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

##<a name="login-to-your-azure-subscription"></a>Login à sua assinatura do Azure

1. Siga as etapas documentadas em [conectar a uma assinatura do Azure a Interface do Azure de linha (Azure comando)](../xplat-cli-connect.md) e conectar-se a sua assinatura usando o `azure login` método.

2. Lista as assinaturas associadas à sua conta usando o `azure account list` comando.

        info:    Executing command account list
        data:    Name              Id                                    Current
        data:    ----------------  ------------------------------------  -------
        data:    Azure-sub-1       ####################################  true
        data:    Azure-sub-2       ####################################  false

    Da saída acima, **Azure-sub-1** está ativado e a outra assinatura é **Azure-sub-2**. 

3. Selecione a assinatura que você deseja trabalhar em. Se você deseja trabalhar na assinatura do Azure-sub-2, use o `azure account set` comando.

        azure account set Azure-sub-2


## <a name="create-an-azure-data-lake-store-account"></a>Criar uma conta de armazenamento de Lucerne de dados do Azure

Abra um prompt de comando, shell ou uma sessão de terminal e execute os seguintes comandos.

2. Alternar para modo de Gerenciador de recursos do Azure usando o seguinte comando:

        azure config mode arm


5. Crie um novo grupo de recursos. O comando a seguir, forneça os valores de parâmetro que você deseja usar.

        azure group create <resourceGroup> <location>

    Se o nome do local contiver espaços, coloque-o entre aspas. Por exemplo "Leste dos EUA 2".

5. Crie a conta de armazenamento de Lucerne de dados.

        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## <a name="create-folders-in-your-data-lake-store"></a>Criar pastas no seu armazenamento de Lucerne de dados

Você pode criar pastas em sua conta de armazenamento de Lucerne de dados do Azure para gerenciar e armazenar dados. Use o seguinte comando para criar uma pasta chamada "mynewfolder" na raiz do armazenamento de Lucerne de dados.

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Por exemplo:

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## <a name="upload-data-to-your-data-lake-store"></a>Carregar dados em seu armazenamento de Lucerne de dados

Você pode carregar seus dados para armazenamento de Lucerne dados diretamente no nível raiz ou para uma pasta que você criou dentro da conta. Os trechos de código a seguintes demonstram como carregar alguns dados de exemplo para a pasta (**mynewfolder**) que você criou na seção anterior.

Se você estiver procurando por alguns dados de exemplo carregar, você pode obter a pasta **Ambulância dados** do [Azure dados Lucerne gito repositório](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Baixar o arquivo e armazená-lo em um diretório local no seu computador, como C:\sampledata\.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Por exemplo:

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## <a name="list-files-in-data-lake-store"></a>Lista de arquivos no repositório de Lucerne de dados

Use o seguinte comando para listar os arquivos em uma conta de armazenamento de Lucerne de dados.

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Por exemplo:

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

A saída deste deve ser semelhante ao seguinte:

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Renomear, baixar e excluir dados do seu armazenamento de Lucerne de dados

* **Para renomear um arquivo**, use o seguinte comando:

        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>

    Por exemplo:

        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv

* **Para baixar um arquivo**, use o seguinte comando. Verifique se o caminho de destino que você especificar já existe.

        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>

    Por exemplo:

        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"

* **Para excluir um arquivo**, use o seguinte comando:

        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>

    Por exemplo:

        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv

    Quando solicitado, digite **Y** para excluir o item.

## <a name="view-the-access-control-list-for-a-folder-in-data-lake-store"></a>Exibir a lista de controle de acesso para uma pasta no repositório de Lucerne de dados

Use o comando a seguir para exibir as ACLs em uma pasta de armazenamento de Lucerne de dados. Na versão atual, ACLs podem ser definidas somente na raiz do armazenamento de Lucerne de dados. Portanto, o parâmetro de caminho abaixo sempre será raiz (/).

    azure datalake store permissions show <dataLakeStoreName> <path>

Por exemplo:

    azure datalake store permissions show mynewdatalakestore /


## <a name="delete-your-data-lake-store-account"></a>Excluir sua conta de armazenamento de Lucerne de dados

Use o seguinte comando para excluir uma conta de armazenamento de Lucerne de dados.

    azure datalake store account delete <dataLakeStoreAccountName>

Por exemplo:

    azure datalake store account delete mynewdatalakestore

Quando solicitado, digite **Y** para excluir a conta.


## <a name="next-steps"></a>Próximas etapas

- [Proteger os dados no repositório de Lucerne de dados](data-lake-store-secure-data.md)
- [Usar a análise de dados Azure Lucerne com dados Lucerne Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com dados Lucerne Store](data-lake-store-hdinsight-hadoop-use-portal.md)


[azure-command-line-tools]: ../xplat-cli-install.md
