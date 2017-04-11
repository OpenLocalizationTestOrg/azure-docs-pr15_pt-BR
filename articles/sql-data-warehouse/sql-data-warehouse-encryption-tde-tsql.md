<properties
   pageTitle="Criptografia de dados transparente em SQL Data Warehouse (T-SQL) | Microsoft Azure"
   description="Criptografia de dados transparente (TDE) em SQL Data Warehouse (T-SQL)"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="get-started-with-transparent-data-encryption-tde"></a>Começar com criptografia de dados transparente (TDE)


> [AZURE.SELECTOR]
- [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md)
- [Autenticação](sql-data-warehouse-authentication.md)
- [Criptografia (Portal)](sql-data-warehouse-encryption-tde.md)
- [Criptografia (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permssions"></a>Permissões necessárias

Para habilitar a criptografia de dados transparente (TDE), você deve ser um administrador ou um membro da função dbmanager.

## <a name="enabling-encryption"></a>Habilitando a criptografia

Siga estas etapas para habilitar TDE para um depósito de dados do SQL:

1. Conectar ao banco de dados *mestre* no servidor que hospeda o banco de dados usando um logon que é um administrador ou um membro da função **dbmanager** no banco de dados mestre
2. Execute a seguinte instrução para criptografar o banco de dados.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Desabilitar a criptografia

Siga estas etapas para desativar TDE para um depósito de dados do SQL:

1. Conectar ao banco de dados *mestre* usando um logon que é um administrador ou um membro da função **dbmanager** no banco de dados mestre
2. Execute a seguinte instrução para criptografar o banco de dados.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [AZURE.NOTE] Um depósito de dados do SQL pausado deve ser reiniciado antes de fazer alterações nas configurações de TDE.

## <a name="verifying-encryption"></a>Verificando a criptografia

Para verificar o status de criptografia para um depósito de dados do SQL, siga as etapas abaixo:

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

## <a name="encryption-dmvs"></a>DMVs de criptografia  

- [Databases][] 
- [sys.dm_pdw_nodes_database_encryption_keys][]


<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[Databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
