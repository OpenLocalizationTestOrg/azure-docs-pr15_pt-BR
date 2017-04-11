<properties
   pageTitle="Criptografia de dados transparente em SQL Data Warehouse (Portal) | Microsoft Azure"
   description="Criptografia de dados transparente (TDE) no depósito de dados do SQL"
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

# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Começar com transparente dados criptografia (TDE) no depósito de dados do SQL

> [AZURE.SELECTOR]
- [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md)
- [Autenticação](sql-data-warehouse-authentication.md)
- [Criptografia (Portal)](sql-data-warehouse-encryption-tde.md)
- [Criptografia (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permssions"></a>Permissões necessárias

Para habilitar a criptografia de dados transparente (TDE), você deve ser um administrador ou um membro da função dbmanager.

## <a name="enabling-encryption"></a>Habilitando a criptografia

Para habilitar TDE para um depósito de dados do SQL, siga as etapas abaixo:

1. Abra o banco de dados no [portal do Azure](https://portal.azure.com)
2. Na lâmina banco de dados, clique no botão **configurações**
3. Selecione a opção de **criptografia de dados transparente**![][1]
4. Selecione a configuração **na**![][2]
5. Selecione **Salvar**
![][3]  

## <a name="disabling-encryption"></a>Desabilitar a criptografia

Para desativar TDE para um depósito de dados do SQL, siga as etapas abaixo:

1. Abra o banco de dados no [portal do Azure](https://portal.azure.com)
2. Na lâmina banco de dados, clique no botão **configurações**
3. Selecione a opção de **criptografia de dados transparente**![][1]
4. Selecione a configuração de **Desativar**![][4]
5. Selecione **Salvar**
![][5]  

## <a name="encryption-dmvs"></a>DMVs de criptografia

Criptografia pode ser confirmada com as seguintes DMVs:

- [Databases]
- [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[Databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
