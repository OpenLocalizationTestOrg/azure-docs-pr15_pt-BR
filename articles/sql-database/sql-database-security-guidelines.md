<properties
   pageTitle="Diretrizes de segurança de banco de dados do SQL Azure e limitações | Microsoft Azure"
   description="Saiba mais sobre diretrizes de banco de dados do Microsoft Azure SQL e limitações relacionadas à segurança."
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
   ms.date="10/18/2016"
   ms.author="rickbyh"/>

# <a name="azure-sql-database-security-guidelines-and-limitations"></a>Limitações e diretrizes de segurança de banco de dados do SQL azure

Este tópico descreve as diretrizes de banco de dados do Microsoft Azure SQL e limitações relacionadas à segurança. Considere os seguintes pontos ao gerenciar a segurança dos seus bancos de dados do SQL Azure.

## <a name="access-to-the-virtual-master-database"></a>Acesso ao banco de dados mestre virtual

Normalmente, somente os administradores precisam de acesso ao banco de dados mestre. Acesso de rotina para cada banco de dados do usuário deve ser por meio de usuários de banco de dados independente não-administrador criados em cada banco de dados. Quando você usa os usuários do banco de dados independente, você não precisa criar logon no banco de dados mestre. Para obter mais informações, consulte [Contidos banco de dados de usuários - tornando o banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).


## <a name="firewall"></a>Firewall

O firewall do SQL Server, que é delimitado para todo o servidor do SQL Azure geralmente é configurado por meio do portal e só deve admitir os endereços IP usados por administradores. Para criar uma regra de firewall escopo do banco de dados que abre o necessário intervalo de endereços IP para cada banco de dados, conectar a um banco de dados do usuário e use o [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) instrução Transact-SQL.

O serviço de banco de dados do Azure SQL só está disponível por meio de porta TCP 1433. Para acessar um banco de dados SQL do seu computador, certifique-se de que o firewall do computador cliente permite que a comunicação de saída TCP na porta TCP 1433. Se não é necessário para outros aplicativos, bloquear conexões de entrada na porta TCP 1433. 

Como parte do processo de conexão, conexões do Azure máquinas virtuais serão redirecionados para um endereço IP diferente e porta, exclusiva para cada função de trabalho. É o número da porta no intervalo de 11000 a 11999. Para obter mais informações sobre portas TCP, consulte [portas além 1433 para ADO.NET 4,5 e V12 de banco de dados do SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


## <a name="authentication"></a>Autenticação

Usar autenticação do Active Directory (segurança integrada) sempre que possível. Para obter informações sobre como configurar a autenticação do AD, consulte [Conectando-se ao banco de dados por usando Azure Active Directory autenticação do SQL](sql-database-aad-authentication.md)e [Escolhendo um modo de autenticação](https://msdn.microsoft.com/library/ms144284.aspx) nos Manuais Online do SQL Server. 

Use banco de dados contidos usuários para melhorar a escalabilidade. Para obter mais informações, consulte [Contidos usuários banco de dados - tornando o banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx), [CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx)e [Bancos de dados contidos](https://technet.microsoft.com/library/ff929071.aspx).

O mecanismo de banco de dados fecha conexões que permaneçam ociosas por mais de 30 minutos. A conexão deve efetuar login novamente antes que ela possa ser usada.

Continuamente conexões ativas ao banco de dados do SQL exigem reautorização (realizada pelo mecanismo de banco de dados) pelo menos a cada 10 horas. O mecanismo de banco de dados tenta reautorização usando a senha originalmente enviada e nenhuma entrada do usuário é necessária. Por razões de desempenho, quando uma senha é redefinida no banco de dados SQL, a conexão não é ser autenticado novamente, mesmo se a conexão for redefinida devido a pool de conexão. Isso é diferente do comportamento no SQL Server local. Se a senha foi alterada desde que a conexão foi autorizado inicialmente, a conexão deve ser terminado e uma nova conexão feitas usando a nova senha. Um usuário com a permissão de conexão de banco de dados de ELIMINAR explicitamente pode encerrar uma conexão ao banco de dados do SQL usando o comando [ELIMINAR](https://msdn.microsoft.com/library/ms173730.aspx) .

## <a name="logins-and-users"></a>Logon e usuários

Ao gerenciar logon e seus usuários no banco de dados SQL, há restrições.


- Você deve estar conectado ao banco de dados **mestre** ao executar o ``CREATE/ALTER/DROP DATABASE`` instruções. -O usuário de banco de dados no banco de dados mestre correspondente ao login principal nível do servidor não pode ser alterado ou descartado. 
- Inglês é o idioma padrão do logon principal nível do servidor.
- Somente os administradores (login principal do nível do servidor ou administrador do Azure AD) e os membros da função **dbmanager** banco de dados no banco de dados **mestre** tem permissão para executar o ``CREATE DATABASE`` e ``DROP DATABASE`` instruções.
- Você deve estar conectado ao banco de dados mestre ao executar o ``CREATE/ALTER/DROP LOGIN`` instruções. No entanto usar logon não é recomendado. Use os usuários do banco de dados independente.
- Para conectar a um banco de dados do usuário, você deve fornecer o nome do banco de dados na cadeia de conexão.
- Somente o logon principal do nível de servidor e os membros da função de banco de dados do **loginmanager** no banco de dados **mestre** tem permissão para executar o ``CREATE LOGIN``, ``ALTER LOGIN``, e ``DROP LOGIN`` instruções.
- Ao executar o ``CREATE/ALTER/DROP LOGIN`` e ``CREATE/ALTER/DROP DATABASE`` instruções em um aplicativo de ADO.NET, usando os comandos parametrizados não é permitida. Para obter mais informações, consulte [comandos e parâmetros](https://msdn.microsoft.com/library/ms254953.aspx).
- Ao executar o ``CREATE/ALTER/DROP DATABASE`` e ``CREATE/ALTER/DROP LOGIN`` instruções, cada um dessas instruções devem ser a instrução somente em um lote de Transact-SQL. Caso contrário, ocorrerá um erro. Por exemplo, o seguinte Transact-SQL verifica se o banco de dados existe. Se ele existir, uma ``DROP DATABASE`` instrução é chamada para remover o banco de dados. Porque o ``DROP DATABASE`` instrução não é a única instrução no lote, executando a seguinte instrução Transact-SQL resulta em um erro.

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

- Ao executar o ``CREATE USER`` instrução com a ``FOR/FROM LOGIN`` opção, ela deverá ser a única declaração em um lote de Transact-SQL.
- Ao executar o ``ALTER USER`` instrução com a ``WITH LOGIN`` opção, ela deverá ser a única declaração em um lote de Transact-SQL.
- Para ``CREATE/ALTER/DROP`` um usuário requer o ``ALTER ANY USER`` permissão de banco de dados.
- Quando o proprietário de uma função de banco de dados tenta adicionar ou remover outro usuário de banco de dados de ou para essa função de banco de dados, o seguinte erro pode ocorrer: **usuário ou função 'Nome' não existir neste banco de dados.** Este erro ocorre porque o usuário não é visível para o proprietário. Para resolver esse problema, conceda o proprietário da função o ``VIEW DEFINITION`` permissão de usuário. 

Para obter mais informações sobre logon e usuários, consulte [Gerenciar bancos de dados e logon no Azure SQL Database](sql-database-manage-logins.md).


## <a name="see-also"></a>Consulte também

[Firewall de banco de dados do SQL Azure](sql-database-firewall-configure.md)

[Como: definir configurações de Firewall (banco de dados do SQL Azure)](sql-database-configure-firewall-settings.md)

[Gerenciar bancos de dados e logon no banco de dados do SQL Azure](sql-database-manage-logins.md)

[Centro de segurança para o mecanismo de banco de dados do SQL Server e o banco de dados do SQL Azure](https://msdn.microsoft.com/library/bb510589)
