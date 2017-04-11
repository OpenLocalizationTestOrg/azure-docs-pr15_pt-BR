<properties
   pageTitle="Habilitar a criptografia de dados transparente (TDE) para SQL Server Alongar banco de dados no Azure | Microsoft Azure"
   description="Habilitar a criptografia de dados transparente (TDE) para SQL Server Alongar banco de dados no Azure"
   services="sql-server-stretch-database"
   documentationCenter=""
   authors="douglaslMS"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-server-stretch-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/14/2016"
   ms.author="douglaslMS"/>

# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Habilitar a criptografia de dados transparente (TDE) para alongar banco de dados no Azure
> [AZURE.SELECTOR]
- [Portal do Azure](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

Criptografia de dados transparente (TDE) ajuda a proteger contra as ameaças de atividade mal-intencionado realizando em tempo real criptografia e descriptografia do banco de dados, backups associados e arquivos de log de transação inativos sem a necessidade de mudanças no aplicativo.

TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada a chave de criptografia do banco de dados. A chave de criptografia do banco de dados estiver protegida por um certificado de servidor interno. O certificado do servidor interno é exclusivo para cada servidor do Azure. Microsoft gira automaticamente esses certificados pelo menos a cada 90 dias. Para obter uma descrição geral do TDE, consulte [Criptografia de dados transparente (TDE)].

##<a name="enabling-encryption"></a>Habilitando a criptografia

Para habilitar TDE para um Azure banco de dados que armazena os dados são migrados de banco de dados habilitado para estender o SQL Server, faça o seguinte:

1. Abra o banco de dados no [portal do Azure](https://portal.azure.com)
2. Na lâmina banco de dados, clique no botão **configurações**
3. Selecione a opção de **criptografia de dados transparente**![][1]
4. Selecione a configuração **em** e selecione **Salvar**
![][2]


##<a name="disabling-encryption"></a>Desabilitar a criptografia

Para desativar TDE para um Azure banco de dados que armazena os dados são migrados de banco de dados habilitado para estender o SQL Server, faça o seguinte:

1. Abra o banco de dados no [portal do Azure](https://portal.azure.com)
2. Na lâmina banco de dados, clique no botão **configurações**
3. Selecione a opção de **criptografia de dados transparente**
4. Selecione a configuração de **Desativar** e selecione **Salvar**




<!--Anchors-->
[Criptografia de dados transparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
