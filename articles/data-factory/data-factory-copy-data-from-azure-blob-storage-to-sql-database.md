<properties
    pageTitle="Copiar dados de armazenamento de Blob do banco de dados SQL | Microsoft Azure"
    description="Este tutorial mostra como usar a atividade de cópia em um pipeline de fábrica de dados do Azure para copiar dados de armazenamento de Blob do banco de dados SQL."
    Keywords="blob sql, armazenamento de blob, cópia de dados"
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="spelluru"/>

# <a name="copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Copiar dados do armazenamento de Blob usando fábrica de dados do banco de dados do SQL 
> [AZURE.SELECTOR]
- [Visão geral e os pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Assistente de cópia](data-factory-copy-data-wizard-tutorial.md)
- [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [O Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Modelo do Gerenciador de recursos Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)


Neste tutorial, você pode criar uma fábrica de dados com um pipeline para copiar dados de armazenamento de Blob do banco de dados SQL.

A atividade de cópia realiza a movimentação dos dados em fábrica de dados do Azure. Ele estiver usando um serviço globalmente disponível que pode copiar dados entre várias fontes de dados de uma maneira segura, confiável e flexível. Consulte o artigo de [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para obter detalhes sobre a atividade de cópia.  

> [AZURE.NOTE] Para obter uma visão geral detalhada do serviço fábrica de dados, consulte o artigo de [Introdução ao Azure dados Factory](data-factory-introduction.md) .

##<a name="prerequisites-for-the-tutorial"></a>Pré-requisitos para o tutorial
Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura do azure**.  Se você não tiver uma assinatura, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Consulte o artigo de [Avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/) para obter detalhes.
- **Conta de armazenamento do azure**. Você pode usar o armazenamento de blob como um armazenamento de dados de **origem** neste tutorial. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) para as etapas criar uma.
- **Banco de dados do SQL azure**. Você pode usar um banco de dados do SQL Azure como um armazenamento de dados de **destino** neste tutorial. Se você não tiver um banco de dados do SQL Azure que você pode usar o tutorial, veja [como criar e configurar um banco de dados do SQL Azure](../sql-database/sql-database-get-started.md) para criar uma.
- **SQL Server 2012/2014 ou Visual Studio 2013**. Use o SQL Server Management Studio ou Visual Studio para criar um banco de dados de exemplo e exibir os dados de resultado no banco de dados.  

## <a name="collect-blob-storage-account-name-and-key"></a>Coletar chave e o nome de conta de armazenamento de blob 
É necessário o nome de conta e chave da conta da sua conta de armazenamento do Azure para fazer este tutorial. Anote o **nome da conta** e **chave da conta** para a sua conta de armazenamento do Azure.

1. Faça logon no [portal do Azure](https://portal.azure.com/).
2. Clique em **mais serviços** no menu à esquerda e selecione **Contas de armazenamento**.

    ![Procurar - contas de armazenamento](media\data-factory-copy-data-from-azure-blob-storage-to-sql-database\browse-storage-accounts.png)
3. Na lâmina **Contas de armazenamento** , selecione a **conta de armazenamento do Azure** que você deseja usar neste tutorial.
4. Selecione o link de **teclas de acesso** em **configurações**.
5.  Clique em botão de **cópia** (imagem) ao lado da caixa de texto **nome da conta de armazenamento** e salvar/colá-la em outro (por exemplo: em um arquivo de texto).
6. Repita a etapa anterior para copiar ou anotação para baixo o **CHAVE1**.
    
    ![Tecla de acesso de armazenamento](media\data-factory-copy-data-from-azure-blob-storage-to-sql-database\storage-access-key.png)
7. Feche todas as lâminas clicando em **X**.

## <a name="collect-sql-server-database-user-names"></a>Coletar do SQL server, o banco de dados, nomes de usuário
É necessário os nomes de usuário para fazer este tutorial, banco de dados e servidor SQL Azure. Anote nomes de **servidor**, **banco de dados**e **usuário** para seu banco de dados do SQL Azure.

1. No **portal do Azure**, clique em **mais serviços** à esquerda e selecione **bancos de dados SQL**.
2. Na **lâmina de bancos de dados do SQL**, selecione o **banco de dados** que você deseja usar neste tutorial. Observe o **nome do banco de dados**.  
3. Na lâmina **banco de dados SQL** , clique em **Propriedades** , em **configurações**.
4. Anote os valores para o **Nome do servidor** e **Logon de administrador do servidor**.
5. Feche todas as lâminas clicando em **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Permitir que os serviços do Azure acessar o SQL server 
Certifique-se de **que a configuração de **Permitir acesso aos serviços Azure** ativado para seu servidor do SQL Azure para que o serviço de dados fábrica possa acessar seu servidor do SQL Azure** . Para verificar e ative essa configuração, siga estas etapas:

1. Clique em **mais serviços** hub à esquerda e **servidores SQL**.
2. Selecione o seu servidor e clique em **Firewall** em **configurações**. 
4. Na lâmina **as configurações de Firewall** , clique em **ativado** para **Permitir acesso aos serviços Azure**.
5. Feche todas as lâminas clicando em **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Preparar o banco de dados SQL e de armazenamento de Blob 
Agora, prepare o armazenamento de blob do Microsoft Azure e o banco de dados do SQL Azure para o tutorial, executando as seguintes etapas:  

1. Inicie o bloco de notas, cole o seguinte texto e salve-o como **emp.txt** **C:\ADFGetStarted** pasta no disco rígido.

        John, Doe
        Jane, Doe

2. Use ferramentas como o [Gerenciador de armazenamento do Azure](https://azurestorageexplorer.codeplex.com/) para criar o contêiner de **adftutorial** e para carregar o arquivo **emp.txt** para o contêiner.

    ![Explorador de armazenamento do Azure. Copiar dados de armazenamento de Blob do banco de dados SQL](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
3. Use o seguinte script SQL para criar a tabela de **emp** no seu banco de dados do SQL Azure.  


        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

    **Se você tiver o SQL Server 2012/2014 instalado no seu computador:** siga as instruções do [etapa 2: conectar ao banco de dados SQL do Gerenciando Azure SQL banco de dados usando o SQL Server Management Studio](../sql-database/sql-database-manage-azure-ssms.md#Step2) artigo conectar ao seu servidor do SQL Azure e executar o script SQL. Este artigo usa o [portal do Azure clássico](http://manage.windowsazure.com), não do [novo portal Azure](https://portal.azure.com), configurar firewall para um servidor do SQL Azure.

    Se o seu cliente não tem permissão para acessar o servidor do SQL Azure, você precisa configurar o firewall para o seu servidor do SQL Azure permitir o acesso do seu computador (endereço IP). Consulte [Este artigo](../sql-database/sql-database-configure-firewall-settings.md) para obter etapas para configurar o firewall para seu servidor do SQL Azure.

Você concluiu os pré-requisitos. Você pode criar uma fábrica de dados usando uma das seguintes maneiras. Clique em uma das guias na parte superior ou os links a seguir para executar o tutorial.     

- [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [O Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Assistente de cópia](data-factory-copy-data-wizard-tutorial.md)
