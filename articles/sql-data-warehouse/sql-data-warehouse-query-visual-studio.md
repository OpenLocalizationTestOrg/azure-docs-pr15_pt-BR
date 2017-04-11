<properties
   pageTitle="Consulta SQL Azure Data Warehouse (Visual Studio) | Microsoft Azure"
   description="Consulta SQL Data Warehouse com o Visual Studio."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="sonyama;barbkess"/>

# <a name="query-azure-sql-data-warehouse-visual-studio"></a>Consulta SQL Azure Data Warehouse (Visual Studio)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Aprendizado de máquina Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [O Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Use o Visual Studio a consulta depósito de dados do SQL Azure em poucos minutos. Este método usa a extensão do SQL Server dados ferramentas (SSDT) no Visual Studio. 

## <a name="prerequisites"></a>Pré-requisitos

Para usar este tutorial, você precisa:

+ Um depósito de dados do SQL existente. Para criar uma, consulte [criar um depósito de dados do SQL][].
+ SSDT para Visual Studio. Se você tiver o Visual Studio, você provavelmente já isso. Para instruções de instalação e opções, consulte [instalar o Visual Studio e SSDT][].
+ O nome totalmente qualificado do servidor SQL. Para encontrar isso, consulte [Conectar depósito de dados do SQL][].

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Conecte seu depósito de dados do SQL

1. Abra o Visual Studio 2013 ou 2015.
2. Abra o Explorador de objeto do SQL Server. Para fazer isso, selecione o **modo de exibição** > **SQL Server Object Explorer**.

    ![Explorador de objetos do SQL Server][1]

3. Clique no ícone **Adicionar SQL Server** .

    ![Adicione o SQL Server][2]

4. Preencha os campos em conectar a janela do servidor.

    ![Conectar ao servidor][3]

    - **Nome do servidor**. Insira o **nome do servidor** anteriormente identificado.
    - **Autenticação**. Selecione **autenticação integrada do Active Directory**ou a **autenticação do SQL Server** .
    - **Nome de usuário** e **senha**. Insira o nome de usuário e senha se a autenticação do SQL Server foi selecionada acima.
    - Clique em **Conectar**.

5. Para explorar, expanda o seu servidor do SQL Azure. Você pode exibir os bancos de dados associados ao servidor. Expanda AdventureWorksDW para ver as tabelas no seu banco de dados de exemplo.

    ![Explorar AdventureWorksDW][4]

## <a name="2-run-a-sample-query"></a>2. executar uma consulta de amostra

Agora que foi estabelecida uma conexão com o banco de dados, vamos escrever uma consulta.

1. Clique com botão direito seu banco de dados no Pesquisador de objetos do SQL Server.

2. Selecione **nova consulta**. Abre uma nova janela de consulta.

    ![Nova consulta][5]

3. Copie esta consulta TSQL na janela de consulta:

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. Execute a consulta. Para fazer isso, clique na seta verde ou use o atalho a seguir: `CTRL` + `SHIFT` + `E`.

    ![Executar consulta][6]

5. Examine os resultados da consulta. Neste exemplo, a tabela de FactInternetSales tem 60398 linhas.

    ![Resultados da consulta][7]

## <a name="next-steps"></a>Próximas etapas

Agora que você pode se conectar e consultar, tente [visualizando os dados com PowerBI][].

Para configurar seu ambiente para autenticação do Active Directory do Azure, consulte [autenticar depósito de dados do SQL][].

<!--Arcticles-->
[Conectar ao depósito de dados do SQL]: sql-data-warehouse-connect-overview.md
[Criar um depósito de dados do SQL]: sql-data-warehouse-get-started-provision.md
[Instalando o SSDT e o Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Autenticar depósito de dados do SQL]: sql-data-warehouse-authentication.md
[visualizando os dados com PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png
