<properties
   pageTitle="Migração de banco de dados do SQL Server para o banco de dados SQL | Microsoft Azure"
   description="Saiba quanto local do SQL Server banco de dados migração para o banco de dados do Azure SQL na nuvem. Use ferramentas de migração do banco de dados para testar a compatibilidade antes de migração de banco de dados."
   keywords="migração, migração de banco de dados do sql server, ferramentas de migração do banco de dados do banco de dados, migrar banco de dados, migrar banco de dados sql"
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

# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>Migração de banco de dados do SQL Server para o banco de dados SQL na nuvem

Neste artigo, saiba como migrar um banco de dados posterior ou local SQL Server 2005 para Azure SQL Database. Nesse processo de migração do banco de dados, você migrar seu esquema e seus dados do banco de dados do SQL Server no seu ambiente atual para o banco de dados SQL. Para ter êxito, o banco de dados existente deve primeiro passar um teste de compatibilidade. Com [V12 de banco de dados do SQL](sql-database-v12-whats-new.md), há alguns problemas de compatibilidade restantes, diferente de problema relacionado ao operações em nível de servidor e bancos de dados. Bancos de dados e aplicativos que dependem [parcial ou não suporte funções](sql-database-transact-sql-information.md) precisa de algumas reformulação para corrigir essas incompatibilidades antes do banco de dados do SQL Server pode ser migrado.

Para migrar, a seguir estão as etapas que você tire:

- **Teste para compatibilidade**: validar compatibilidade de banco de dados com [V12 de banco de dados do SQL](sql-database-v12-whats-new.md). 
- **Corrigir problemas de compatibilidade, se houver**: se validação falhar, você deve corrigir os erros de validação.  
- **Executar a migração** Depois que o banco de dados for compatível, você pode usar um ou vários métodos para executar a migração. 

SQL Server fornece vários métodos para realizar cada uma dessas tarefas. Este artigo fornece uma visão geral dos métodos disponíveis para cada tarefa. O diagrama a seguir ilustra as etapas e os métodos.

  ![Diagrama de migração de VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)
  
 > [AZURE.NOTE] Para migrar um banco de dados não SQL Server, incluindo o Microsoft Access, Sybase, MySQL Oracle e DB2 banco de dados de SQL Azure, consulte [Assistente de migração do SQL Server](http://blogs.msdn.com/b/ssma/).

## <a name="database-migration-tools-test-sql-server-database-compatibility-with-sql-database"></a>Ferramentas de migração do banco de dados testam compatibilidade de banco de dados do SQL Server com o banco de dados SQL

Para testar se há problemas de compatibilidade do banco de dados SQL antes de iniciar o processo de migração do banco de dados, use um dos seguintes métodos:

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Supervisor de atualização](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

- [Ferramentas de dados do SQL Server para Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): SSDT usa as regras de compatibilidade mais recentes para detectar incompatibilidades V12 de banco de dados do SQL. Se incompatibilidades for detectadas, você pode corrigir problemas detectados diretamente nesta ferramenta. Esse método é o método recomendado para testar e corrigir problemas de compatibilidade de V12 de banco de dados do SQL. 
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage é um utilitário de linha de comando que testa para problemas de compatibilidade e gera um relatório contendo problemas de compatibilidade detectado. Se você usar esta ferramenta, verifique se que você usar a versão mais recente para usar as regras de compatibilidade mais recentes. Se forem detectados erros, você deve usar outra ferramenta corrigir problemas de compatibilidade detectado - SSDT é recomendável.  
- [Assistente de aplicativo a exportar a camada de dados no SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): esse assistente detecta e relatórios de erros para a tela. Se não forem detectados erros, você pode continuar e concluir a migração para o banco de dados SQL. Se forem detectados erros, você deve usar outra ferramenta corrigir problemas de compatibilidade detectado - SSDT é recomendável.
- [O Microsoft SQL Server 2016 atualizar Advisor Preview](http://www.microsoft.com/download/details.aspx?id=48119): essa ferramenta autônomo, que está no modo de visualização, detecta e gera um relatório de incompatibilidades V12 de banco de dados do SQL. Esta ferramenta ainda não tem as regras de compatibilidade mais recentes. Se nenhum erro for detectado, você pode continuar e concluir a migração para o banco de dados SQL. Se forem detectados erros, você deve usar outra ferramenta corrigir problemas de compatibilidade detectado - SSDT é recomendável. 
- [Assistente de migração do SQL Azure ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): SAMW é uma ferramenta de codeplex que usa as regras de compatibilidade de V11 de banco de dados do SQL Azure para detectar incompatibilidades V12 de banco de dados do SQL Azure. Se incompatibilidades for detectadas, alguns problemas podem ser corrigidos diretamente nesta ferramenta. Esta ferramenta pode descobrir incompatibilidades que não precisam ser corrigidos. Ele foi a primeira Azure SQL Database migração assistência ferramenta disponível e ativamente é suportado por comunidade do SQL Server. Além disso, esta ferramenta pode concluir a migração de dentro da ferramenta em si. 

## <a name="fix-database-migration-compatibility-issues"></a>Corrigir problemas de compatibilidade de migração de banco de dados

Se problemas de compatibilidade for detectados, você deverá corrigi-los antes de continuar com a migração de banco de dados do SQL Server. Há uma grande variedade de problemas de compatibilidade que podem ocorrer, dependendo da maneira ambos na versão do SQL Server no banco de dados de origem e a complexidade do banco de dados que você está migrando. Versões mais antigas do SQL Server tem mais problemas de compatibilidade. Use os seguintes recursos, além de uma pesquisa direcionada de Internet usando seu mecanismo de pesquisa de opções:

- [Recursos de banco de dados do SQL Server não têm suportados no banco de dados do SQL Azure](sql-database-transact-sql-information.md)
- [Funcionalidade de mecanismo de banco de dados descontinuados no SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Funcionalidade de mecanismo de banco de dados descontinuados no SQL Server de 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Funcionalidade de mecanismo de banco de dados descontinuados no SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Funcionalidade de mecanismo de banco de dados descontinuados no SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Funcionalidade de mecanismo de banco de dados descontinuados no SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Além de pesquisar na Internet e usar esses recursos, use o [Fóruns da comunidade MSDN SQL Server](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) ou [StackOverflow](http://stackoverflow.com/).

Use uma das ferramentas de migração do banco de dados a seguir para corrigir os problemas detectados:

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

- Usar [As ferramentas de dados do SQL Server para Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): para usar SSDT, você importar seu esquema de banco de dados para as ferramentas de dados do SQL Server para Visual Studio "SSDT") e construir o projeto para uma implantação V12 de banco de dados do SQL. Em seguida, você corrija todos os problemas de compatibilidade detectado na SSDT. Ao concluir, você sincroniza as alterações de volta para o banco de dados de origem (ou uma cópia do banco de dados de origem. SSDT atualmente é o método recomendado para testar e corrigir problemas de compatibilidade de V12 de banco de dados do SQL. Siga o link para um [detalhado usando SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md).
- Use o [SQL Server Management Studio ("SSMS")](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md): para usar SSMS, executar comandos Transact-SQL para corrigir os erros detectados usando outra ferramenta. Este método é principalmente para usuários avançados modificar o esquema de banco de dados diretamente no banco de dados de origem. 
- Usar o [Assistente de migração do SQL Azure ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): para usar SAMW, você gera um script Transact-SQL a partir do banco de dados de origem. O assistente transforma o script, sempre que possível, para tornar o esquema compatível com o V12 de banco de dados SQL. Ao concluir, SAMW pode conectar ao V12 de banco de dados SQL para executar o script. Esta ferramenta também analisa arquivos de rastreamento para determinar problemas de compatibilidade. O script pode ser gerado com esquema somente ou pode incluir dados no formato BCP.

## <a name="migrate-a-compatible-sql-server-database-to-sql-database"></a>Migrar um banco de dados do SQL Server compatível para o banco de dados SQL

Para migrar um banco de dados do SQL Server compatível, a Microsoft fornece vários métodos de migração para vários cenários. O método escolhido depende da sua tolerância para tempo de inatividade, o tamanho e complexidade de seu banco de dados do SQL Server e a conectividade com a nuvem do Microsoft Azure.  

> [AZURE.SELECTOR]
- [Assistente de migração de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exportar para arquivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importar de arquivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Replicação de transação](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Para escolher o método de migração, a primeira pergunta a peça é se você pode pagar tirar o banco de dados da produção durante a migração. Migrar um banco de dados enquanto transações ativas estão ocorrendo pode resultar em inconsistências de banco de dados e corrupção de banco de dados possível. Há vários métodos para desativar um banco de dados, desabilitar conectividade de cliente a criação de um [instantâneo de banco de dados](https://msdn.microsoft.com/library/ms175876.aspx).

Para migrar com o tempo de inatividade mínimo, use [replicação de transação do SQL Server](sql-database-cloud-migrate-compatible-using-transactional-replication.md) se seu banco de dados atende aos requisitos para replicação de transação. Se você pode pagar algum tempo de inatividade ou se você estiver executando uma migração de teste de um banco de dados de produção para migração posterior, considere um dos três métodos a seguir:

- [Assistente de migração de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): para pequenas e médias bancos de dados, migrar um banco de dados posterior ou compatível com SQL Server 2005 é tão simple quanto executando a [Implantar o banco de dados para o Assistente de banco de dados do Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) no SQL Server Management Studio.
- [Exportar para arquivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) e, em seguida, [Importar de arquivo de BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): se você tiver desafios de conectividade (sem conectividade, pouca largura de banda ou problemas de tempo limite) e para médias e grandes bancos de dados, usar um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) . Com esse método, você exporta o esquema do SQL Server e os dados para um arquivo BACPAC. Você importar o arquivo BACPAC para banco de dados do SQL usando dados camada aplicativo Assistente para exportação no SQL Server Management Studio ou o utilitário de prompt de comando do [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) .
- Use BACPAC e BCP juntas: usar um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) e [BCP](https://msdn.microsoft.com/library/ms162802.aspx) para bancos de dados muito maiores para alcançar maior paralelização para aumenta o desempenho, embora com maior complexidade. Com esse método, migre o esquema e os dados separadamente.
 - [Exportar o esquema somente para um arquivo BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
 - [Importar o esquema somente a partir de arquivo BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) para banco de dados SQL.
 - Use [BCP](https://msdn.microsoft.com/library/ms162802.aspx) para extrair os dados em arquivos simples e, em seguida, [carga paralela](https://technet.microsoft.com/library/dd425070.aspx) esses arquivos em Azure SQL Database.

     ![SQL Server migração de banco de dados – migrar o banco de dados do SQL para a nuvem.](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

## <a name="next-steps"></a>Próximas etapas

- [A visualização de Supervisor de atualização do Microsoft SQL Server 2016](http://www.microsoft.com/download/details.aspx?id=48119)
- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

##<a name="additional-resources"></a>Recursos adicionais

- [V12 de banco de dados do SQL](sql-database-v12-whats-new.md)
[Transact-SQL parcial ou não suporte funções](sql-database-transact-sql-information.md)
- [Migrar bancos de dados do SQL Server usando o Assistente de migração do SQL Server](http://blogs.msdn.com/b/ssma/)
