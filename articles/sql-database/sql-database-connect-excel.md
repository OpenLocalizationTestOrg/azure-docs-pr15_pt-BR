<properties
    pageTitle="Conectar o Excel ao banco de dados SQL | Microsoft Azure"
    description="Saiba como conectar o Microsoft Excel ao banco de dados do SQL Azure na nuvem. Importar dados para o Excel para relatar e exploração de dados."
    services="sql-database"
    keywords="conectar-se ao sql do excel, importar os dados do excel"
    documentationCenter=""
    authors="joseidz"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/05/2016"
    ms.author="joseidz"/>


# <a name="sql-database-tutorial-connect-excel-to-an-azure-sql-database-and-create-a-report"></a>Tutorial do banco de dados SQL: conectar o Excel a um banco de dados do SQL Azure e criar um relatório

> [AZURE.SELECTOR]
- [O Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Saiba como conectar o Excel a um banco de dados do SQL na nuvem para que você possa importar dados e criar tabelas e gráficos com base em valores no banco de dados. Neste tutorial, que você irá configurar a conexão entre o Excel e uma tabela de banco de dados, salve o arquivo que armazena os dados e as informações de conexão para o Excel e, em seguida, criar um gráfico dinâmico a partir os valores de banco de dados.

Você precisará de um banco de dados SQL Azure antes de começar. Se você não tiver uma, consulte [criar seu primeiro banco de dados SQL](sql-database-get-started.md) para acessar um banco de dados com dados de exemplo para cima e em execução em poucos minutos. Neste artigo, você vai importar dados de exemplo para o Excel nesse artigo, mas você pode seguir as etapas semelhantes em seus próprios dados.

Você também precisará uma cópia do Excel. Este artigo usa o [Microsoft Excel 2016](https://products.office.com/en-US/).

## <a name="connect-excel-to-a-sql-database-and-create-an-odc-file"></a>Conectar o Excel a um banco de dados do SQL e criar um arquivo odc

1.  Para conectar o Excel ao banco de dados SQL, abra o Excel e crie uma nova pasta de trabalho ou abra uma pasta de trabalho do Excel existente.

2.  Na barra de menus na parte superior da página clique em **dados**, clique em **De outras fontes**e clique em **Do SQL Server**.

    ![Selecionar fonte de dados: conectar o Excel ao banco de dados SQL.](./media/sql-database-connect-excel/excel_data_source.png)

    O Assistente para Conexão de dados é aberto.

3.  Na caixa de diálogo **conectar ao servidor de banco de dados** , digite o **nome do servidor** de banco de dados SQL você deseja se conectar no formulário <*nomedoservidor*>**. database.windows.net**. Por exemplo, **adworkserver.database.windows.net**.

4.  Em **credenciais de logon**, clique em **usar o seguinte nome de usuário e senha**, digite o **Nome de usuário** e **senha** que você configurar para o servidor de banco de dados SQL quando você o criou e clique em **Avançar**.

    ![Digite as credenciais de nome e faça logon de servidor](./media/sql-database-connect-excel/connect-to-server.png)

    > [AZURE.TIP] Dependendo do seu ambiente de rede, você não poderá conectar-se ou você pode perder a conexão se o servidor de banco de dados SQL não permitir o tráfego do seu endereço IP do cliente. Acesse o [portal do Azure](https://portal.azure.com/), clique servidores SQL seu servidor, clique em firewall em configurações e em Adicionar seu endereço IP do cliente. Veja [como configurar o firewall](sql-database-configure-firewall-settings.md) para obter detalhes.

5. Na caixa de diálogo **Selecionar banco de dados e tabela** , selecione o banco de dados que você deseja trabalhar na lista e clique nas tabelas ou modos de exibição que você quer trabalhar com (escolhemos **vGetAllCategories**) e, em seguida, clique em **Avançar**.

    ![Selecione um banco de dados e a tabela.](./media/sql-database-connect-excel/select-database-and-table.png)

    Abre a caixa de diálogo **Salvar o arquivo de Conexão de dados e concluir** , onde você pode fornecer informações sobre o arquivo de conexão (*. odc) de banco de dados do Office que o Excel usa. Você pode deixar os padrões ou personalizar suas seleções.

6. Você pode deixar os padrões, mas observe o **Nome do arquivo** em particular. **Pesquisar palavras-chave** , um **Nome amigável**e uma **Descrição**ajudam você e outros usuários lembrar o que você estiver se conectando ao e localize a conexão. Clique em **sempre tentar usar este arquivo para atualizar os dados** , se desejar obter informações de conexão armazenadas no arquivo odc para que ele possa atualizar quando conectá-la e clique em **Concluir**.

    ![Salvando um arquivo odc](./media/sql-database-connect-excel/save-odc-file.png)

    Caixa de diálogo **Importar dados** é exibida.

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importar os dados para o Excel e crie um gráfico dinâmico
Agora que você já estabeleceu a conexão e criado o arquivo com as informações de dados e de conexão, você está lendo para importar os dados.

1. Na caixa de diálogo **Importar dados** , clique na opção desejada para apresentar seus dados da planilha e clique em **Okey**. Escolhemos **gráfico dinâmico**. Você também pode optar por criar uma **nova planilha** ou para **Adicionar dados a um modelo de dados**. Para obter mais informações sobre modelos de dados, consulte [criar um modelo de dados no Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). Clique em **Propriedades** para explorar informações sobre o arquivo odc que você criou na etapa anterior e escolha opções para atualizar os dados.

    ![Escolher o formato de dados no Excel](./media/sql-database-connect-excel/import-data.png)

    A planilha agora tem uma tabela dinâmica vazia e um gráfico.

8. Em **Campos de tabela dinâmica**, selecione todas as caixas de seleção para os campos que você deseja exibir.

    ![Configure o relatório de banco de dados.](./media/sql-database-connect-excel/power-pivot-results.png)

> [AZURE.TIP]Se você quiser se conectar a outras planilhas e pastas de trabalho do Excel ao banco de dados, clique em **dados**, clique em **conexões**, clique em **Adicionar**, escolha a conexão que você criou na lista e clique em **Abrir**.
> ![Abrir uma conexão de outra pasta de trabalho](./media/sql-database-connect-excel/open-from-another-workbook.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [conectar-se ao banco de dados do SQL com o SQL Server Management Studio](sql-database-connect-query-ssms.md) para consultar avançada e análise.
- Saiba mais sobre os benefícios de [pools Elástico](sql-database-elastic-pool.md).
- Saiba como [criar um aplicativo web que se conecta ao banco de dados do SQL no back-end](../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
