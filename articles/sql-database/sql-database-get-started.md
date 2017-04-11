<properties
    pageTitle="Tutorial do banco de dados SQL: criar um banco de dados do SQL | Microsoft Azure"
    description="Saiba como configurar um servidor de banco de dados SQL lógico, regra de firewall do servidor, banco de dados SQL e dados de exemplo. Além disso, saiba como conectar-se com ferramentas de cliente, configurar usuários e configurar uma regra de firewall do banco de dados."
    keywords="tutorial do banco de dados SQL, criar um banco de dados do sql"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/07/2016"
    ms.author="carlrab"/>


# <a name="sql-database-tutorial-create-a-sql-database-in-minutes-by-using-the-azure-portal"></a>Tutorial do banco de dados SQL: criar um banco de dados do SQL em minutos usando o portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Neste tutorial, você vai aprender como usar o portal do Azure para:

- Crie um banco de dados do SQL Azure com dados de exemplo.
- Crie uma regra de firewall de nível de servidor para um único endereço IP ou um intervalo de endereços IP.

Você pode executar as mesmas tarefas usando [c#](sql-database-get-started-csharp.md) ou [PowerShell](sql-database-get-started-powershell.md).

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

<a name="create-logical-server-bk"></a>

## <a name="create-your-first-azure-sql-database"></a>Criar seu primeiro banco de dados do SQL Azure 

1. Se você ainda não estiver conectado, conecte o [portal do Azure](http://portal.azure.com).
2. Clique em **novo**, clique em **dados + armazenamento**e, em seguida, localize o **Banco de dados SQL**.

    ![Novo banco de dados do sql 1](./media/sql-database-get-started/sql-database-new-database-1.png)

3. Clique em **Banco de dados SQL** para abrir a lâmina de banco de dados SQL. O conteúdo neste blade varia dependendo do número de suas assinaturas e seus objetos existentes (como servidores existentes).

    ![Novo banco de dados do sql 2](./media/sql-database-get-started/sql-database-new-database-2.png)

4. Na caixa de texto **nome do banco de dados** , forneça um nome para seu primeiro banco de dados - como "meu banco de dados". Uma marca de seleção verde indica que você forneceu um nome válido.

    ![Novo banco de dados do sql 3](./media/sql-database-get-started/sql-database-new-database-3.png)

5. Se você tiver várias assinaturas, selecione uma assinatura.
6. Em **grupo de recursos**, clique em **Criar novo** e forneça um nome para o primeiro grupo de recursos - como "meu--grupo de recursos". Uma marca de seleção verde indica que você forneceu um nome válido.

    ![Novo banco de dados do sql 4](./media/sql-database-get-started/sql-database-new-database-4.png)

7. Em **Selecionar fonte**, clique em **amostra** e clique em **AdventureWorksLT [V12]**em **Selecione amostra** .

    ![Novo banco de dados do sql 5](./media/sql-database-get-started/sql-database-new-database-5.png)

8. Em **servidor**, clique em **Configurar configurações necessárias**.

    ![Novo banco de dados do sql 6](./media/sql-database-get-started/sql-database-new-database-6.png)

9. No blade Server, clique em **criar um novo servidor**. Um banco de dados do SQL Azure é criado em um objeto de servidor, que pode ser um novo servidor ou um servidor existente.

    ![Novo banco de dados do sql 7](./media/sql-database-get-started/sql-database-new-database-7.png)

10. Examine a lâmina de **novo servidor** para entender as informações que necessárias para fornecer para seu novo servidor.

    ![Novo banco de dados do sql 8](./media/sql-database-get-started/sql-database-new-database-8.png)

11. Na caixa de texto **nome do servidor** , forneça um nome para seu primeiro servidor - como "Meu-new-server-object". Uma marca de seleção verde indica que você forneceu um nome válido.

    ![Novo banco de dados do sql 9](./media/sql-database-get-started/sql-database-new-database-9.png)
 
12. Em **logon de administrador do servidor**, forneça um nome de usuário para o logon de administrador para este servidor - como "Meu--conta de administrador". Este login é conhecido como o logon principal do servidor. Uma marca de seleção verde indica que você forneceu um nome válido.

    ![Novo banco de dados do sql 10](./media/sql-database-get-started/sql-database-new-database-10.png)

13. Em **senha** e **Confirmar senha**, forneça uma senha para o servidor de conta de logon principal - como "p@ssw0rd1". Uma marca de seleção verde indica que você forneceu uma senha válida.

    ![Novo banco de dados do sql 11](./media/sql-database-get-started/sql-database-new-database-11.png)
 
14. Em **local**, selecione um centro de dados apropriado para seu local - como "Austrália Oriental".

    ![Novo banco de dados do sql 12](./media/sql-database-get-started/sql-database-new-database-12.png)

15. Em * * criar V12 server (última atualização), observe que você só tem a opção de criar uma versão atual do SQL Azure server.

    ![Novo banco de dados do sql 13](./media/sql-database-get-started/sql-database-new-database-13.png)

16. Observe que, por padrão, a caixa de seleção para **Permitir azure serviços para acessar o servidor** está marcada e não pode ser alterada aqui. Isso é uma opção avançada. Você pode alterar essa configuração nas configurações de firewall do servidor para esse objeto de servidor, embora a maioria dos cenários isso não é necessário.

    ![Novo banco de dados do sql 14](./media/sql-database-get-started/sql-database-new-database-14.png)

17. No novo blade server, revise suas seleções e clique em **Selecionar** para selecionar este novo servidor para seu novo banco de dados.

    ![Novo banco de dados do sql 15](./media/sql-database-get-started/sql-database-new-database-15.png)

18. Na lâmina banco de dados SQL, em **nível de preço**, clique em **S2 padrão** e clique em **básico** para escolher o nível de preços barato para seu primeiro banco de dados. Você sempre pode alterar o nível de preços mais tarde.

    ![Novo banco de dados do sql 16](./media/sql-database-get-started/sql-database-new-database-16.png)

19. Na lâmina banco de dados SQL, revise suas seleções e clique em **criar** para criar seu primeiro servidor e banco de dados. Os valores que você forneceu são validados e implantação começa.

    ![Novo banco de dados do sql 17](./media/sql-database-get-started/sql-database-new-database-17.png)

20. Na barra de ferramentas portal, clique nos itens de **notificações** para verificar o status da sua implantação.

    ![Novo banco de dados do sql 18](./media/sql-database-get-started/sql-database-new-database-18.png)

>[AZURE.IMPORTANT]Quando terminar de implantação, seu novo Azure SQL server e o banco de dados são criados no Azure. Você não poderá se conectar ao seu novo servidor ou banco de dados usando as ferramentas do SQL Server até que você crie uma regra de firewall do servidor para abrir o firewall do banco de dados SQL para conexões de fora do Azure.

[AZURE.INCLUDE [Create server firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>Próximas etapas
Agora que você concluiu este tutorial do banco de dados SQL e criado um banco de dados com alguns dados de exemplo, estiver pronto para explorar usando suas ferramentas favoritas.

- Se você estiver familiarizado com Transact-SQL e SQL Server Management Studio (SSMS), saiba como [conectar e consulta um banco de dados do SQL com SSMS](sql-database-connect-query-ssms.md).

- Se você souber o Excel, saiba como [conectar a um banco de dados SQL Azure com o Excel](sql-database-connect-excel.md).

- Se você estiver pronto para iniciar a codificação, escolha a linguagem de programação em [bibliotecas de Conexão de banco de dados SQL e SQL Server](sql-database-libraries.md).

- Se você desejar mover seus bancos de dados do SQL Server local no Azure, consulte [migrar um banco de dados do banco de dados SQL](sql-database-cloud-migrate.md) para saber mais.

- Se você deseja carregar alguns dados em uma nova tabela de um arquivo CSV usando a ferramenta de linha de comando BCP, consulte [carregar dados no banco de dados SQL de um arquivo CSV usando BCP](sql-database-load-from-csv-with-bcp.md).

- Se você quiser comece a explorar a segurança do Azure SQL Database, consulte [Introdução à segurança](sql-database-get-started-security.md)


## <a name="additional-resources"></a>Recursos adicionais

[O que é o banco de dados SQL?](sql-database-technical-overview.md)
