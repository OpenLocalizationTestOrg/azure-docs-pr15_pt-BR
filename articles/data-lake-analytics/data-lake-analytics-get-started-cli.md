<properties 
   pageTitle="Introdução ao Azure dados Lucerne análise usando a Interface de linha de comando do Azure | Microsoft Azure" 
   description="Saiba como usar a Interface de linha do Azure para criar uma conta de armazenamento de Lucerne de dados, criar um trabalho de dados Lucerne análise usando U-SQL e enviar o trabalho. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Tutorial: Introdução ao Azure dados Lucerne análise usando a Interface de linha de comando (CLI) do Azure

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Saiba como usar CLI do Azure para criar contas de análise de Lucerne de dados do Azure, definir trabalhos de dados Lucerne Analytics em [U-SQL](data-lake-analytics-u-sql-get-started.md)e enviar trabalhos para contas de dados Lucerne Analytics. Para obter mais informações sobre a análise de dados Lucerne, consulte [Visão geral de análise de Lucerne de dados do Azure](data-lake-analytics-overview.md).

Neste tutorial, você irá desenvolver um trabalho que lê uma guia do arquivo de valores (TSV) separados e converte-o em um arquivo CSV (valores) de separados por vírgulas. Para percorrer o tutorial mesmo usando outras ferramentas compatíveis, clique nas guias na parte superior desta seção.

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).
- **CLI azure**. Consulte [instalar e configurar o Azure CLI](../xplat-cli-install.md).
    - Baixe e instale o **pré-lançamento** [do Azure CLI ferramentas](https://github.com/MicrosoftBigData/AzureDataLake/releases) para concluir esta demonstração.
- **Autenticação**, usando o seguinte comando:

        azure login
    Para obter mais informações sobre a autenticação usando uma conta corporativa ou escolar, consulte [conectar a uma assinatura do Azure da CLI Azure](../xplat-cli-connect.md).
- **Alternar para o modo do Gerenciador de recursos do Azure**, usando o seguinte comando:

        azure config mode arm
        
## <a name="create-data-lake-analytics-account"></a>Criar conta de dados Lucerne Analytics

Você deve ter uma conta de dados Lucerne Analytics antes de poder executar todos os trabalhos. Para criar uma conta de dados Lucerne Analytics, especifique o seguinte:

- **Grupo de recursos do Azure**: A dados Lucerne Analytics conta deve ser criada em um grupo de recursos do Azure. [Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md) permite que você trabalhe com os recursos em seu aplicativo como um grupo. Você pode implantar, atualizar ou excluir todos os recursos para seu aplicativo em uma operação única e coordenado.  

    Enumerar os grupos de recursos em sua assinatura:
    
        azure group list 
    
    Para criar um novo grupo de recursos:

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Nome de conta de análise de dados Lucerne**
- **Local**: um dos Azure data centers que ofereça suporte a análise de dados Lucerne.
- **Conta padrão dados Lucerne**: cada conta dados Lucerne Analytics tem uma conta de dados Lucerne padrão.

    A lista a conta Lucerne de dados existente:
    
        azure datalake store account list

    Para criar uma nova conta de Lucerne de dados:

        azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"

    > [AZURE.NOTE] O nome da conta Lucerne de dados deve conter apenas letras minúsculas e números.



**Para criar uma conta de dados Lucerne Analytics**

        azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

        azure datalake analytics account list
        azure datalake analytics account show "<Data Lake Analytics Account Name>"          

![A análise de dados Lucerne Mostrar conta](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [AZURE.NOTE] O nome da conta dados Lucerne análise deve conter apenas letras minúsculas e números.


## <a name="upload-data-to-data-lake-store"></a>Carregar dados dados Lucerne repositório

Neste tutorial, você irá processar alguns logs de pesquisa.  O log de pesquisa pode ser armazenado no repositório de dados Lucerne ou armazenamento de Blob do Azure. 

Portal do Azure fornece uma interface de usuário para copiar alguns arquivos de dados de exemplo para a conta de dados Lucerne padrão, que incluem um arquivo de log de pesquisa. Consulte [preparar dados de origem](data-lake-analytics-get-started-portal.md#prepare-source-data) para carregar os dados para a conta de armazenamento de Lucerne de dados padrão.

Para carregar arquivos usando o cli, use o seguinte comando:

    azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
    azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

Lucerne a análise de dados também pode acessar o armazenamento de Blob do Azure.  Para carregar dados para armazenamento de Blob do Azure, consulte [usando a CLI Azure com o armazenamento do Azure](../storage/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Enviar dados Lucerne Analytics trabalhos

Os trabalhos de dados Lucerne Analytics são gravados na linguagem SQL U. Para saber mais sobre U-SQL, consulte [referência de linguagem de U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348)e [começar a usar linguagem U-SQL](data-lake-analytics-u-sql-get-started.md) .

**Para criar um script de trabalho de dados Lucerne Analytics**

- Criar um arquivo de texto com o seguinte script U-SQL e salve o arquivo de texto em sua estação de trabalho:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Este script U-SQL lê o arquivo de dados de origem usando **Extractors.Tsv()**e, então, cria um arquivo csv usando **Outputters.Csv()**. 
    
    Não modifique os dois caminhos, a menos que você copia o arquivo de origem para um local diferente.  A análise de dados Lucerne criará a pasta de saída, se ele não existir.
    
    É mais simples usar caminhos relativos para arquivos armazenavam em default dados contas Lucerne. Você também pode usar caminhos absolutos.  Por exemplo 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Você deve usar caminhos absolutos para acessar arquivos em contas de armazenamento vinculadas.  A sintaxe para arquivos armazenados em conta vinculada de armazenamento do Azure é:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Contêiner de Blob Azure com permissões de acesso de contêineres público ou públicos blobs não são suportados no momento.      

    
**Para enviar o trabalho**


    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"
    
    
Os seguintes comandos podem ser usados para listar trabalhos, obter detalhes de trabalho e cancelar trabalhos:

    azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
    azure datalake analytics job list "<Data Lake Analytics Account Name>"
    azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"

Depois que o trabalho for concluído, você pode usar os seguintes cmdlets para o arquivo de lista e baixar o arquivo:
    
    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
    azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
    azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## <a name="see-also"></a>Consulte também

- Para ver o mesmo tutorial usando outras ferramentas, clique em seletores de guia no topo da página.
- Para ver uma consulta mais complexa, consulte [logs de site de análise usando análise de Lucerne de dados do Azure](data-lake-analytics-analyze-weblogs.md).
- Para começar a desenvolver aplicativos U-SQL, consulte [scripts de desenvolver U-SQL usando ferramentas de Lucerne de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para saber U-SQL, consulte [Introdução ao idioma do Azure dados Lucerne Analytics U-SQL](data-lake-analytics-u-sql-get-started.md).
- Tarefas de gerenciamento, consulte [Gerenciar Azure dados Lucerne análise usando o Portal do Azure](data-lake-analytics-manage-use-portal.md).
- Para obter uma visão geral de análise de Lucerne de dados, consulte [Visão geral de análise de Lucerne de dados do Azure](data-lake-analytics-overview.md).

