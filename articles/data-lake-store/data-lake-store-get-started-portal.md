<properties 
   pageTitle="Introdução ao armazenamento de dados de Lucerne | Azure" 
   description="Usar o portal para criar uma conta de armazenamento de Lucerne de dados e executar operações básicas na loja de Lucerne de dados" 
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
   ms.date="09/13/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Introdução ao armazenamento de Lucerne de dados do Azure usando o Portal do Azure

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node](data-lake-store-manage-use-nodejs.md)

Saiba como usar o Portal do Azure para criar uma conta de armazenamento de Lucerne de dados do Azure e executar operações básicas, tais como criar pastas, carregar e baixar arquivos de dados, excluir sua conta, etc. Para obter mais informações sobre armazenamento de Lucerne de dados, consulte [Visão geral do Azure Lucerne armazenamento de dados](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).

## <a name="do-you-learn-fast-with-videos"></a>Você aprenderá rapidamente com vídeos?

Assista aos seguintes vídeos para começar a usar o armazenamento de Lucerne de dados.

* [Criar uma conta de armazenamento de Lucerne de dados](https://mix.office.com/watch/1k1cycy4l4gen)
* [Gerenciar dados no repositório de Lucerne de dados usando o Explorador de dados](https://mix.office.com/watch/icletrxrh6pc)

## <a name="create-an-azure-data-lake-store-account"></a>Criar uma conta de armazenamento de Lucerne de dados do Azure

1. Entrar novo [Portal Azure](https://portal.azure.com).

2. Clique em **novo**, clique em **dados + armazenamento**e, em seguida, clique em **Repositório de Lucerne de dados do Azure**. Leia as informações na lâmina **Armazenamento de Lucerne de dados do Azure** e clique em **criar** no canto inferior esquerdo da lâmina.

3. Na **Nova Data Lucerne Store** lâmina, forneça os valores conforme mostrado na captura de tela abaixo:

    ![Criar uma nova conta de armazenamento de Lucerne de dados do Azure] (./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Criar uma nova conta de Lucerne de dados do Azure")

    - **Assinatura**. Selecione a assinatura em que você deseja criar uma nova conta de armazenamento de Lucerne de dados.
    - **Grupo de recursos**. Selecione um grupo de recursos existente ou clique em **criar um grupo de recursos** para criar um. Um grupo de recursos é um recipiente que contém recursos relacionados para um aplicativo. Para obter mais informações, consulte [Grupos de recursos no Azure](azure-resource-manager/resource-group-overview.md#resource-groups).
    - **Local**: selecione um local onde você deseja criar a conta de armazenamento de Lucerne de dados.

4. Selecione **Fixar em Startboard** se quiser que a conta de armazenamento de Lucerne de dados a serem acessados a partir do Startboard.

5. Clique em **criar**. Se você escolheu fixar a conta para a startboard, você será direcionado volta para o startboard e você pode ver o andamento do seu armazenamento de Lucerne dados de provisionamento de contas. Depois que a conta de armazenamento de Lucerne de dados está provisionada, a lâmina de conta aparece.

6. Expanda o **Essentials** suspensa para ver as informações sobre a sua conta de armazenamento de Lucerne de dados, como o recurso de grupo são uma parte da, o local, etc. Clique no ícone de **Início rápido** para ver links para outros recursos relacionados ao armazenamento de Lucerne de dados.

    ![Conta de seu armazenamento de Lucerne de dados do Azure] (./media/data-lake-store-get-started-portal/ADL.Account.QuickStart.png "Conta de seu Lucerne de dados do Azure")

## <a name="createfolder"></a>Criar pastas na conta de armazenamento de Lucerne de dados do Azure

Você pode criar pastas em sua conta de armazenamento de dados de Lucerne para gerenciar e armazenar dados.

1. Abra a conta de armazenamento de Lucerne de dados que você acabou de criar. No painel esquerdo, clique em **Procurar**, clique em **Dados Lucerne Store**e da lâmina do armazenamento de Lucerne de dados, clique no nome de conta sob a qual você deseja criar pastas. Se você fixado a conta para a startboard, clique nesse bloco de conta.

2. No seu blade de conta de armazenamento de Lucerne de dados, clique em **Explorador de dados**.

    ![Criar pastas na conta de armazenamento de Lucerne de dados] (./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Criar pastas na conta de armazenamento de Lucerne de dados")

3. Em seu lâmina de conta de armazenamento de Lucerne de dados, clique em **Nova pasta**, insira um nome para a nova pasta e clique em **Okey**.
    
    ![Criar pastas na conta de armazenamento de Lucerne de dados] (./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Criar pastas na conta de armazenamento de Lucerne de dados")
    
    A pasta recém-criado será listada na lâmina **Explorador de dados** . Você pode criar pastas aninhadas até qualquer nível.

    ![Criar pastas na conta Lucerne de dados] (./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Criar pastas na conta Lucerne de dados")


## <a name="uploaddata"></a>Carregar dados a conta de armazenamento de Lucerne de dados do Azure

Você pode carregar seus dados a uma conta de armazenamento do Azure dados Lucerne diretamente no nível raiz ou para uma pasta que você criou dentro da conta. Na captura de tela abaixo, siga as etapas para fazer o upload de um arquivo para uma subpasta da lâmina do **Explorador de dados** . Nesta captura de tela, o arquivo é carregado para uma subpasta mostrada na trilha (marcada por uma caixa vermelha).

Se você estiver procurando por alguns dados de exemplo carregar, você pode obter a pasta **Ambulância dados** do [Azure dados Lucerne gito repositório](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

![Carregar dados] (./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Carregar dados")


## <a name="properties"></a>Propriedades e ações disponíveis para os dados armazenados

Clique no arquivo recém-adicionado para abrir a lâmina de **Propriedades** . As propriedades associadas ao arquivo e as ações que você pode executar no arquivo estão disponíveis neste blade. Você também pode copiar o caminho completo para o arquivo em sua conta de armazenamento de Lucerne de dados do Azure, realçada na caixa vermelha na captura de tela abaixo.

![Propriedades nos dados] (./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Propriedades nos dados")

* Clique em **Visualizar** para ver uma visualização do arquivo, diretamente do navegador. Você pode especificar o formato da visualização também. Clique em **Visualizar**, clique em **Formatar** na lâmina **Visualização de arquivo** e na lâmina **Formato de arquivo de visualização** especificar as opções de como o número de linhas a serem exibidos, codificação usar, delimitador usar, etc.

  ![Formato de arquivo de visualização] (./media/data-lake-store-get-started-portal/ADL.File.Preview.png "Formato de arquivo de visualização")

* Clique em **Baixar** para baixar o arquivo no seu computador.

* Clique em **Renomear o arquivo** para renomear o arquivo.

* Clique em **Excluir arquivo** para excluir o arquivo.


## <a name="secure-your-data"></a>Proteger seus dados

Você pode proteger os dados armazenados em sua conta de armazenamento de Lucerne de dados do Azure usando o Active Directory do Azure e controle de acesso (ACLs). Para obter instruções sobre como fazer isso, consulte [Protegendo dados Azure dados Lucerne Store](data-lake-store-secure-data.md).


## <a name="delete-azure-data-lake-store-account"></a>Conta de armazenamento de Lucerne excluir Azure dados

Para excluir uma conta de armazenamento de Lucerne de dados do Azure, de blade seu armazenamento de Lucerne de dados, clique em **Excluir**. Para confirmar a ação, você será solicitado a inserir o nome da conta que deseja excluir. Insira o nome da conta e clique em **Excluir**.

![Excluir dados Lucerne conta] (./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Excluir dados Lucerne conta")


## <a name="next-steps"></a>Próximas etapas

- [Proteger os dados no repositório de Lucerne de dados](data-lake-store-secure-data.md)
- [Usar a análise de dados Azure Lucerne com dados Lucerne Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com dados Lucerne Store](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Logs de diagnóstico de acesso para armazenamento de Lucerne de dados](data-lake-store-diagnostic-logs.md)
