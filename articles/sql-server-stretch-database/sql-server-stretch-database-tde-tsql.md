<properties
   pageTitle="Habilitar a criptografia de dados transparente (TDE) para SQL Server Alongar banco de dados no Azure TSQL | Microsoft Azure"
   description="Habilitar a criptografia de dados transparente (TDE) para SQL Server Alongar banco de dados em TSQL Azure"
   services="sql-server-stretch-database"
   documentationCenter=""
   authors="douglaslMS"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-server-stretch-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/14/2016"
   ms.author="douglaslMS"/>

# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Habilitar a criptografia de dados transparente (TDE) para alongar banco de dados no Azure (Transact-SQL)
> [AZURE.SELECTOR]
- [Portal do Azure](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

Criptografia de dados transparente (TDE) ajuda a proteger contra as ameaças de atividade mal-intencionado realizando em tempo real criptografia e descriptografia do banco de dados, backups associados e arquivos de log de transação inativos sem a necessidade de mudanças no aplicativo.

TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada a chave de criptografia do banco de dados. A chave de criptografia do banco de dados estiver protegida por um certificado de servidor interno. O certificado do servidor interno é exclusivo para cada servidor do Azure. Microsoft gira automaticamente esses certificados pelo menos a cada 90 dias. Para obter uma descrição geral do TDE, consulte [Criptografia de dados transparente (TDE)].

##<a name="enabling-encryption"></a>Habilitando a criptografia

Para habilitar TDE para um Azure banco de dados que armazena os dados são migrados de banco de dados habilitado para estender o SQL Server, faça o seguinte:

1. Conectar ao banco de dados *mestre* no Azure servidor que hospeda o banco de dados usando um logon que é um administrador ou um membro da função **dbmanager** no banco de dados mestre
2. Execute a seguinte instrução para criptografar o banco de dados.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

##<a name="disabling-encryption"></a>Desabilitar a criptografia

Para desativar TDE para um Azure banco de dados que armazena os dados são migrados de banco de dados habilitado para estender o SQL Server, faça o seguinte:

1. Conectar ao banco de dados *mestre* usando um logon que é um administrador ou um membro da função **dbmanager** no banco de dados mestre
2. Execute a seguinte instrução para criptografar o banco de dados.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

##<a name="verifying-encryption"></a>Verificando a criptografia

Para verificar o status de criptografia para um banco de dados do Azure que está armazenando os dados são migrados de banco de dados habilitado para estender o SQL Server, faça o seguinte:

1. Conectar-se a instância *mestre* ou banco de dados usando um logon que é um administrador ou um membro da função **dbmanager** no banco de dados mestre
2. Execute a seguinte instrução para criptografar o banco de dados.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Um resultado de ```1``` indica um banco de dados criptografado, ```0``` indica um banco de dados não criptografado.


<!--Anchors-->
[Criptografia de dados transparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
