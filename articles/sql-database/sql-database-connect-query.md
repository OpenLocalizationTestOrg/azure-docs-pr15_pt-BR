<properties
    pageTitle="Conectar ao banco de dados do SQL com uma consulta c# | Microsoft Azure"
    description="Escreva um programa em c# para consultar e se conectar ao banco de dados SQL. Informações sobre endereços IP, cadeias de caracteres de conexão, seguro e gratuito Visual Studio."
    services="sql-database"
    keywords="consulta de banco de dados c#, c# consulta, se conectar ao banco de dados SQL C#"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="stevestein"/>



# <a name="connect-to-a-sql-database-with-visual-studio"></a>Conectar a um banco de dados do SQL com o Visual Studio

> [AZURE.SELECTOR]
- [O Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Saiba como se conectar a um banco de dados do SQL Azure no Visual Studio. 

## <a name="prerequisites"></a>Pré-requisitos


Para conectar a um banco de dados do SQL usando o Visual Studio, você precisa do seguinte: 


- Um banco de dados do SQL para se conectar ao. Este artigo usa o banco de dados de exemplo **AdventureWorks** . Para acessar o banco de dados de exemplo AdventureWorks, consulte [criar o banco de dados de demonstração](sql-database-get-started.md).


- Atualização do 2013 Studio Visual 4 (ou posterior). Agora, a Microsoft fornece comunidade do Visual Studio *gratuitamente*.
 - [Comunidade do Visual Studio, download](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Mais opções para liberar o Visual Studio](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)




## <a name="open-visual-studio-from-the-azure-portal"></a>Abra o Visual Studio do portal do Azure


1. Faça logon no [portal do Azure](https://portal.azure.com/).

2. Clique em **Mais serviços** > **bancos de dados SQL**
3. Abra a lâmina de banco de dados **AdventureWorks** Localizando e clicando em banco de dados *AdventureWorks* .

6. Clique no botão de **Ferramentas** na parte superior da lâmina banco de dados:

    ![Nova consulta. Conectar ao banco de dados do SQL server: SQL Server Management Studio](./media/sql-database-connect-query/tools.png)

7. Clique em **Abrir no Visual Studio** (se você precisar Visual Studio, clique no link download):

    ![Nova consulta. Conectar ao banco de dados do SQL server: SQL Server Management Studio](./media/sql-database-connect-query/open-in-vs.png)


8. O Visual Studio abre com a janela de **conectar ao servidor** já configurada para se conectar ao servidor e banco de dados selecionado no portal.  (Clique em **Opções** para verificar se a conexão está definido para o banco de dados correto.) Digite sua senha de administrador do servidor e clique em **Conectar**.


    ![Nova consulta. Conectar ao banco de dados do SQL server: SQL Server Management Studio](./media/sql-database-connect-query/connect.png)


8. Se você não tiver uma regra de firewall definido para o endereço IP do seu computador, você recebe uma mensagem de *não conseguir se conectar* aqui. Para criar uma regra de firewall, consulte [Configurar uma regra de firewall de nível de servidor de banco de dados do SQL Azure](sql-database-configure-firewall-settings.md).


9. Depois de conectar com êxito, a janela de **SQL Server Object Explorer** abre com uma conexão ao seu banco de dados.

    ![Nova consulta. Conectar ao banco de dados do SQL server: SQL Server Management Studio](./media/sql-database-connect-query/sql-server-object-explorer.png)


## <a name="run-a-sample-query"></a>Executar uma consulta de amostra

Agora que estamos conectados ao banco de dados, as etapas a seguir mostram como executar uma consulta simples:

2. O banco de dados de atalho e selecione **Nova consulta**.

    ![Nova consulta. Conectar ao banco de dados do SQL server: SQL Server Management Studio](./media/sql-database-connect-query/new-query.png)

3. Na janela de consulta, copie e cole o código a seguir.

        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;

4. Clique no botão **Executar** para executar a consulta:

    ![Sucesso. Conectar ao banco de dados do SQL server: SVisual Studio](./media/sql-database-connect-query/run-query.png)

## <a name="next-steps"></a>Próximas etapas

- Abrir bancos de dados SQL no Visual Studio usa as ferramentas de dados do SQL Server. Para obter mais detalhes, consulte [Ferramentas de dados do SQL Server](https://msdn.microsoft.com/library/hh272686.aspx).
- Para conectar a um banco de dados do SQL usando o código, consulte [conectar ao banco de dados SQL usando .NET (c#)](sql-database-develop-dotnet-simple.md).



