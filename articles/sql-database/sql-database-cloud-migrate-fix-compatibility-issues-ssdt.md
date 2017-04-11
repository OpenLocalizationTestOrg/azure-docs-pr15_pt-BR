<properties
   pageTitle="Corrigir problemas de compatibilidade de banco de dados do SQL Server antes de migração para o banco de dados SQL | Microsoft Azure"
   description="Microsoft Azure SQL Database, migração de banco de dados, compatibilidade, o Assistente de migração do SQL Azure, SSDT"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-sql-server-data-tools-for-visual-studio"></a>Migrar um banco de dados do SQL Server para o banco de dados do SQL Azure usando as ferramentas de dados do SQL Server para Visual Studio 

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Supervisor de atualização](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

Neste artigo, você aprende a detectar e corrigir problemas de compatibilidade do banco de dados do SQL Server usando as ferramentas de dados do SQL Server para Visual Studio antes de migração para o Azure SQL Database.

## <a name="using-sql-server-data-tools-for-visual-studio"></a>Usando ferramentas de dados do SQL Server para Visual Studio

Use as ferramentas de dados do SQL Server para Visual Studio ("SSDT") para importar o esquema de banco de dados para um projeto de banco de dados do Visual Studio para análise. Para analisar, especifique a plataforma de destino para o projeto como V12 de banco de dados do SQL e depois compilar o projeto. Se a compilação for bem-sucedida, o banco de dados é compatível. Se a construção falhar, você poderá resolver os erros em SSDT (ou uma das outras ferramentas abordadas neste tópico). Depois que o project cria com êxito, você poderá publicá-lo novamente como uma cópia do banco de dados de origem. Em seguida, você pode usar o recurso de comparação de dados no SSDT para copiar os dados do banco de dados de origem para o banco de dados do Azure SQL V12 compatível. Você pode migrar este banco de dados atualizado. Para usar esta opção, baixe a [versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx).

  ![Diagrama de migração de VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  > [AZURE.NOTE] Se somente de esquema migração for necessária, o esquema pode ser publicado diretamente do Visual Studio diretamente ao Azure SQL Database. Use esse método quando o esquema de banco de dados requer mais alterações que pode ser tratado pelo Assistente de migração sozinho.

## <a name="detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Detectar problemas de compatibilidade usando as ferramentas de dados do SQL Server para Visual Studio
   
1.  Abra o **Explorador de objetos do SQL Server** no Visual Studio. Use **Adicionar SQL Server** para conectar-se a instância do SQL Server que contém o banco de dados que está sendo migrado. Localize o banco de dados no Pesquisador de objetos, clique com botão direito do banco de dados e selecione **Criar novo projeto...**     
    
    ![Novo projeto](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)    
   
2.  Defina as configurações de importação para **Importar somente objetos no escopo do aplicativo**. Desmarque as opções para importar o seguinte: referenciados logon, permissões e configurações de banco de dados.    

    ![texto ALT](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)    

3.  Clique em **Iniciar** para importar o banco de dados e criar o projeto que contém um arquivo de script T-SQL para cada objeto do banco de dados. Os arquivos de script são aninhados em pastas dentro do projeto.    

    ![texto ALT](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)    

4.  No Visual Studio Solution Explorer, clique com botão direito no projeto de banco de dados e selecione Propriedades. Na página **Configurações do Project** , configure a plataforma de destino para V12 de banco de dados do Microsoft Azure SQL.    
    
    ![texto ALT](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)    
    
5.  Clique com botão direito do projeto e selecione **Construir** para construir o projeto.    
    
    ![texto ALT](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)    
    
6.  A **Lista de erros** exibe cada incompatibilidade. Nesse caso, o nome de usuário NT \ serviço é incompatível. Como ele é incompatível, você pode comentá-lo ou removê-lo (e as implicações da remoção Este login e a função da solução do banco de dados de endereço).     
    
    ![texto ALT](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)    
    
## <a name="fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Corrigindo problemas de compatibilidade usando as ferramentas de dados do SQL Server para Visual Studio

1.  Clique duas vezes o primeiro script para abrir o script em uma janela de consulta e comente o script e, em seguida, execute o script.     
    ![texto ALT](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

2.  Repita esse processo para cada script contendo incompatibilidades até que não permaneçam nenhum erro.    
    ![texto ALT](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
    
3.  Quando o banco de dados é gratuito de erros, clique com botão direito do projeto e selecione **Publicar**. Uma cópia do banco de dados de origem for criada e publicada (é altamente recomendável usar uma cópia, pelo menos inicialmente).     
 - Antes de publicar, dependendo da versão do SQL Server de origem (anteriores ao SQL Server 2014), você talvez precise redefinir plataforma de destino do projeto para permitir a implantação.     
 - Se você estiver migrando um banco de dados do SQL Server mais antigo, não apresenta os recursos no projeto que não são compatíveis com a fonte do SQL Server até migrar o banco de dados para uma versão mais recente do SQL Server.     

        ![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)    
    
        ![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)    
        
4.  No Pesquisador de objetos do SQL Server, clique com botão direito seu banco de dados de origem e clique em **Comparação de dados**. Comparar o projeto ao banco de dados original ajuda você a entender quais alterações foram feitas pelo assistente. Selecione a sua versão do Azure SQL V12 do banco de dados e clique em **Concluir**.    
    
    ![texto ALT](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)    
    
    ![texto ALT](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)    

5.  Examine as diferenças detectadas e clique em **Destino de atualização** para migrar os dados do banco de dados de origem para o banco de dados do Azure SQL V12.     
    
    ![texto ALT](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)    
    
6.  Escolha um método de implantação. Consulte [migrar um banco de dados do SQL Server compatível para o banco de dados SQL.](sql-database-cloud-migrate.md)  

## <a name="next-steps"></a>Próximas etapas

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de banco de dados SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcial ou não suporte funções](sql-database-transact-sql-information.md)
- [Migrar bancos de dados do SQL Server usando o Assistente de migração do SQL Server](http://blogs.msdn.com/b/ssma/)
