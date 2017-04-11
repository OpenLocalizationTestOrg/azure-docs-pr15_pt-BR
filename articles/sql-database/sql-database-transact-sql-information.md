<properties
   pageTitle="Sem suporte no banco de dados do SQL Azure T-SQL | Microsoft Azure"
   description="Instruções de Transact-SQL que são inferiores totalmente compatíveis com o banco de dados do Azure SQL"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/30/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="azure-sql-database-transact-sql-differences"></a>Diferenças de Transact-SQL de banco de dados do SQL Azure


A maioria dos recursos Transact-SQL que dependem de aplicativos é suportada no Microsoft SQL Server e o Azure SQL Database. A seguir, uma lista parcial dos recursos com suporte para aplicativos:

- Tipos de dados.
- Operadores.
- Funções de cadeia de caracteres, aritmética, lógica e cursor.

No entanto, o banco de dados do Azure SQL foi criado para isolar recursos a partir de qualquer dependência em banco de dados **mestre** . Como consequência muitas atividades de nível de servidor são inadequadas do banco de dados do SQL e sem suporte. Recursos que são preteridos no SQL Server geralmente não são suportados no banco de dados do SQL.

> [AZURE.NOTE]
> Este tópico aborda os recursos que estão disponíveis com o banco de dados SQL quando atualizado para a versão atual; V12 de banco de dados do SQL. Para obter mais informações sobre V12, consulte [do SQL banco de dados V12 quais novo](sql-database-v12-whats-new.md).

As seções a seguir listam os recursos que são suportados parcialmente e os recursos que não são totalmente suportados.


## <a name="features-partially-supported-in-sql-database-v12"></a>Recursos com suporte parcial do V12 de banco de dados SQL

V12 de banco de dados do SQL é compatível com alguns mas não todos os argumentos que existem nas instruções SQL Server 2016 Transact-SQL correspondente. Por exemplo, a instrução CREATE PROCEDURE está disponível no entanto, todas as opções de CREATE PROCEDURE não estão disponíveis. Consulte os tópicos de vinculada sintaxe para obter detalhes sobre as áreas compatíveis de cada instrução.

- Bancos de dados: [criar](https://msdn.microsoft.com/library/dn268335.aspx )/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
- DMVs estão geralmente disponíveis para recursos que estão disponíveis.
- Funções: [criar](https://msdn.microsoft.com/library/ms186755.aspx)/[Alterar função](https://msdn.microsoft.com/library/ms186967.aspx)
- [ELIMINAR](https://msdn.microsoft.com/library/ms173730.aspx) 
- Logon: [criar](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
- Procedimentos armazenados: [criar](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
- Tabelas: [criar](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER](https://msdn.microsoft.com/library/ms190273.aspx)
- Tipos (personalizado): [Criar tipo](https://msdn.microsoft.com/library/ms175007.aspx)
- Usuários: [criar](https://msdn.microsoft.com/library/ms173463.aspx)/[Alterar usuário](https://msdn.microsoft.com/library/ms176060.aspx)
- Modos de exibição: [criar](https://msdn.microsoft.com/library/ms187956.aspx)/[Alterar modo de exibição](https://msdn.microsoft.com/library/ms173846.aspx)

## <a name="features-not-supported-in-sql-database"></a>Recursos não têm suportados no banco de dados SQL

- Agrupamento de objetos do sistema
- Conexão relacionado: instruções de ponto de extremidade, ORIGINAL_DB_NAME. Banco de dados SQL não oferece suporte a autenticação do Windows, mas dá suporte a autenticação do Active Directory do Azure semelhante. Alguns tipos de autenticação exigem a versão mais recente do SSMS. Para obter mais informações, consulte [Conectando-se ao banco de dados do SQL ou SQL dados depósito por usando Azure autenticação do Active Directory](sql-database-aad-authentication.md).
- Cross consultas de banco de dados usando nomes de parte de três ou quatro. (Consultas de bancos de dados de somente leitura são suportadas usando [consulta de banco de dados elástica](sql-database-elastic-query-overview.md).)
- Banco de dados entre o encadeamento de propriedade, configuração confiável
- Coletor de dados
- Diagramas de banco de dados
- Email de banco de dados
- DATABASEPROPERTY (em vez disso, use DATABASEPROPERTYEX)
- Logon EXECUTE AS
- Criptografia: gerenciamento extensível de chaves
- Eventos: eventos, notificações de eventos, notificações de consulta
- Recursos relacionados a colocação de arquivos de banco de dados, o tamanho e arquivos de banco de dados que são gerenciados automaticamente pelo Microsoft Azure.
- Recursos que se relacionam com alta disponibilidade, que é gerenciada por meio de sua conta do Microsoft Azure: fazer backup, restaurar, sempre ativado, espelhamento de banco de dados, o envio de log, modos de recuperação. Para obter mais informações, consulte Backup de banco de dados do SQL Azure e restauração.
- Recursos que contam com o leitor de log em execução no banco de dados SQL: replicação de envio por Push, captura de dados alterados.
- Recursos que contam com o SQL Server Agent ou banco de dados MSDB: trabalhos, alertas, operadores, gerenciamento baseado em política, email de banco de dados, servidores de gerenciamento central.
- FILESTREAM
- Funções: fn_get_sql fn_virtualfilestats, fn_virtualservernodes
- Tabelas temporárias globais
- Configurações do servidor relacionadas a hardware: memória segmentos de trabalho, afinidade da CPU, sinalizadores de rastreamento, etc. Use os níveis de serviço.
- HAS_DBACCESS
- ELIMINAR ESTATÍSTICAS DE TRABALHO
- Servidores vinculados, OPENQUERY, OPENROWSET, OPENDATASOURCE, inserir em MASSA e nomes de quatro partes
- Servidores mestre/destino
- .NET framework [integração CLR com o SQL Server](http://msdn.microsoft.com/library/ms254963.aspx)
- Gerenciador de recursos
- Pesquisa semântica
- Credenciais de servidor. Em vez disso, o banco de dados de uso escopo credenciais.
- Itens de nível Server: servidor funções, IS_SRVROLEMEMBER, sys.login_token. Permissões no nível do servidor não estão disponíveis, embora algumas são substituídas por permissões no nível do banco de dados. Algumas DMVs de nível de servidor não estão disponíveis, embora algumas são substituídas por DMVs de nível de banco de dados.
- Sem servidor express: localdb, instâncias de usuário
- Agente de serviço
- DEFINIR REMOTE_PROC_TRANSACTIONS
- DESLIGAMENTO
- sp_addmessage
- Opções de sp_configure e RECONFIGURE. Algumas opções estão disponíveis usando [Alterar configuração de escopo do banco de dados](https://msdn.microsoft.com/library/mt629158.aspx).
- sp_helpuser
- sp_migrate_user_to_contained
- Auditoria do SQL Server. Use a auditoria de banco de dados SQL, em vez disso.
- O criador de perfil do SQL Server
- Rastreamento do SQL Server
- Sinalizadores de rastreamento. Alguns itens de sinalizador de rastreamento foram movidos para modos de compatibilidade.
- Depuração Transact-SQL
- Gatilhos: Escopo do servidor ou disparadores de logon
- Política de uso: para alterar o contexto de banco de dados para outro banco de dados, você deve fazer uma nova conexão para o novo banco de dados.


## <a name="full-transact-sql-reference"></a>Referência de Transact-SQL completa

Para obter mais informações sobre gramática Transact-SQL, uso e exemplos, consulte [Referência de Transact-SQL (mecanismo de banco de dados)](https://msdn.microsoft.com/library/bb510741.aspx) nos Manuais Online do SQL Server. 

### <a name="about-the-applies-to-tags"></a>Sobre as marcas de "Aplica-se a"

A referência Transact-SQL inclui tópicos relacionados às versões do SQL Server 2008 atual. Abaixo do título do tópico lá é um ícone de barra, listando as quatro plataformas de SQL Server e indicando a capacidade de aplicação. Por exemplo, grupos de disponibilidade foram introduzidos no SQL Server 2012. O tópico de [Criar grupo AVAILABILTY](https://msdn.microsoft.com/library/ff878399.aspx) indica que a política se aplica a * * (começando com 2012) do SQL Server. A política não se aplica ao SQL Server 2008, SQL Server 2008 R2, banco de dados do SQL Azure, depósito de dados do SQL Azure ou Parallel Data Warehouse.

Em alguns casos, o assunto geral de um tópico pode ser usado em um produto, mas há pequenas diferenças entre os produtos. As diferenças são indicadas em pontos médios no tópico conforme apropriado.

