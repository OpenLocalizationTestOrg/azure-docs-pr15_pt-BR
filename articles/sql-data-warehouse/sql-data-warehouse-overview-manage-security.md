<properties
   pageTitle="Proteger um banco de dados em SQL Data Warehouse | Microsoft Azure"
   description="Dicas para proteger um banco de dados no depósito de dados do SQL Azure para desenvolvimento de soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="secure-a-database-in-sql-data-warehouse"></a>Proteger um banco de dados no depósito de dados do SQL

> [AZURE.SELECTOR]
- [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md)
- [Autenticação](sql-data-warehouse-authentication.md)
- [Criptografia (Portal)](sql-data-warehouse-encryption-tde.md)
- [Criptografia (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Este artigo explica as Noções básicas para proteger seu banco de dados de depósito de dados do SQL Azure. Em particular, este artigo irá iniciá-lo com os recursos para limitar o acesso, proteção de dados e monitorar as atividades em um banco de dados.

## <a name="connection-security"></a>Segurança de Conexão

Segurança de Conexão se refere a como você restringir e conexões seguras seu banco de dados usando regras de firewall e criptografia de conexão.

Regras de firewall são usadas pelo servidor e o banco de dados para rejeitar tentativas de conexão de endereços IP que não foram explicitamente na lista branca. Para permitir conexões de seu aplicativo ou endereço IP público da máquina cliente, você deve primeiro criar uma regra de firewall de nível de servidor usando o Portal do Azure, API REST ou PowerShell. Como prática recomendada, você deve restringir os intervalos de endereços IP permitidos através do firewall servidor tanto quanto possíveis.  Para acessar o depósito de dados do SQL Azure do seu computador local, certifique-se de comunicação de saída na porta TCP 1433 permite que o firewall em sua rede e computador local.  Para obter mais informações, consulte [firewall do Azure SQL Database][], [sp_set_firewall_rule][]e [sp_set_database_firewall_rule][].

Conexões com o SQL Data Warehouse são criptografados por padrão.  Modificando configurações de conexão para desabilitar a criptografia são ignoradas.

## <a name="authentication"></a>Autenticação

Autenticação se refere a como você prova sua identidade ao conectar ao banco de dados. SQL Data Warehouse atualmente oferece suporte a autenticação do SQL Server com um nome de usuário e senha, bem como um Active Directory do Azure. 

Quando você criou o servidor lógico para seu banco de dados, você especificou um logon "administrador do servidor" com um nome de usuário e senha. Usando essas credenciais, você pode autenticar a qualquer banco de dados no servidor como o proprietário do banco de dados, ou "dbo" por meio de autenticação do SQL Server.

No entanto, como uma prática recomendada, os usuários de sua organização devem usar uma conta diferente para autenticar. Dessa maneira, você pode limitar as permissões concedidas ao aplicativo e reduzir os riscos de atividades maliciosas em caso de código do seu aplicativo é vulnerável a um ataque de inclusão de SQL. 

Para criar um usuário autenticado do SQL Server, se conectar ao banco de dados **mestre** no seu servidor com seu administrador de logon de servidor e crie um novo logon de servidor.  Além disso, é uma boa ideia criar um usuário no banco de dados mestre para usuários de depósito de dados do SQL Azure. Criar um usuário no mestre permite que um usuário faça logon usando ferramentas como SSMS sem especificar um nome de banco de dados.  Ele também permite que eles usem o Explorador de objetos para exibir todos os bancos de dados em um servidor SQL.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Em seguida, se conectar ao seu **banco de dados do SQL Data Warehouse** com seu administrador de logon de servidor e criar um usuário de banco de dados com base no logon de servidor que você acabou de criar.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Se um usuário fará operações adicionais como criar logon ou criar novos bancos de dados, eles também precisam ser atribuído para a `Loginmanager` e `dbmanager` funções no banco de dados mestre. Para obter mais informações sobre essas funções adicionais e autenticar um banco de dados do SQL, consulte [Gerenciar bancos de dados e logon no Azure SQL Database][].  Para obter mais detalhes sobre Azure AD para depósito de dados do SQL, consulte [Conectando-se ao SQL dados depósito por usando Azure autenticação do Active Directory][].


## <a name="authorization"></a>Autorização

Autorização refere-se ao que você pode fazer em um banco de dados de depósito de dados do SQL Azure e isso é controlado pelo associações de função da sua conta de usuário e permissões. Como prática recomendada, você deve conceder aos usuários os privilégios mínimos necessários. Depósito de dados SQL Azure torna isso fácil de gerenciar com funções em T-SQL:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

A conta de administrador do servidor com que está se conectando é um membro de db_owner, que tem autoridade para fazer algo no banco de dados. Salve esta conta para implantar as atualizações de esquema e outras operações de gerenciamento. Use a conta "ApplicationUser" com permissões mais limitadas para conectar-se de seu aplicativo para o banco de dados com os privilégios mínimos necessitados para seu aplicativo.

Existem maneiras de limitar ainda mais que um usuário pode fazer com o banco de dados do Azure SQL:

- Granulares [permissões][] permitem que você controle quais operações que você pode fazer em colunas individuais, tabelas, exibições, procedimentos e outros objetos no banco de dados. Use permissões granulares para ter mais controle e conceder as permissões mínimas necessárias. O sistema de permissão granular é um pouco complicado e exigirá alguns estudo para usar com eficiência.
- [Funções de banco de dados][] diferente db_datareader e db_datawriter podem ser usadas para criar contas de usuário de aplicativo mais avançadas ou menos poderosas contas de gerenciamento. As funções de banco de dados fixa internos fornecem uma maneira fácil de conceder permissões, mas podem resultar em conceder mais permissões que são necessárias.
- [Procedimentos armazenados][] pode ser usado para limitar as ações que podem ser executadas no banco de dados.

Gerenciando servidores lógicos e bancos de dados a partir do Portal de clássico do Azure ou usar a API do Gerenciador de recursos do Azure é controlada pelo atribuições de função da sua conta de usuário do portal. Para obter mais informações sobre este tópico, consulte [controle de acesso baseado em função no Portal do Azure][].

## <a name="encryption"></a>Criptografia

Azure SQL dados depósito transparente dados criptografia (TDE) ajuda a proteger contra as ameaças de atividade mal-intencionado realizando em tempo real criptografia e descriptografia dos dados inativos.  Quando você criptografa seu banco de dados, backups associados e arquivos de log de transação são criptografados sem exigir alterações aos seus aplicativos. TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada a chave de criptografia do banco de dados. No banco de dados SQL a chave de criptografia do banco de dados está protegida por um certificado de servidor interno. O certificado do servidor interno é exclusivo para cada servidor de banco de dados SQL. Microsoft gira automaticamente esses certificados pelo menos a cada 90 dias. O algoritmo de criptografia usado pelo SQL Data Warehouse é AES-256. Para obter uma descrição geral do TDE, consulte [Criptografia transparente de dados][].

Você pode criptografar o banco de dados usando o [Portal do Azure] [ Encryption with Portal] ou [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Próximas etapas

Para obter detalhes e exemplos sobre como conectar seu depósito de dados do SQL com protocolos diferentes, consulte [conectar ao depósito de dados do SQL][].

<!--Image references-->

<!--Article references-->
[Conectar ao depósito de dados do SQL]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Conectando depósito de dados do SQL usando a autenticação do Active Directory do Azure]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Firewall de banco de dados do SQL Azure]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Funções de banco de dados]: https://msdn.microsoft.com/library/ms189121.aspx
[Gerenciar bancos de dados e logon no banco de dados do SQL Azure]: https://msdn.microsoft.com/library/ee336235.aspx
[Permissões]: https://msdn.microsoft.com/library/ms191291.aspx
[Procedimentos armazenados]: https://msdn.microsoft.com/library/ms190782.aspx
[Criptografia de dados transparente]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Controle de acesso baseado em função no Portal do Azure]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
