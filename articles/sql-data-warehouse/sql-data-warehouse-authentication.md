<properties
   pageTitle="Autenticação depósito de dados do SQL Azure | Microsoft Azure"
   description="Autenticação do Active Directory (AAD) e SQL Server Azure depósito de dados do SQL Azure."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="byham"
   manager="barbkess"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/24/2016"
   ms.author="rickbyh;barbkess;sonyama"/>

# <a name="authentication-to-azure-sql-data-warehouse"></a>Autenticação depósito de dados do SQL Azure

> [AZURE.SELECTOR]
- [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md)
- [Autenticação](sql-data-warehouse-authentication.md)
- [Criptografia (Portal)](sql-data-warehouse-encryption-tde.md)
- [Criptografia (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Para conectar ao SQL Data Warehouse, você deve passar credenciais de segurança para fins de autenticação. Após estabelecer uma conexão, certas configurações de conexão são configuradas como parte de estabelecer a sessão de consulta.  

Para obter mais informações sobre segurança e como habilitar conexões para seu data warehouse, consulte [seguro um banco de dados no depósito de dados do SQL][].

## <a name="sql-authentication"></a>Autenticação do SQL
Para conectar ao SQL Data Warehouse, você deve fornecer as seguintes informações:

- Nomedoservidor totalmente qualificado
- Especificar a autenticação do SQL
- Nome de usuário
- Senha
- Banco de dados padrão (opcional)

Por padrão, sua conexão conecta-se ao banco de dados *mestre* e não o banco de dados do usuário. Para se conectar ao seu banco de dados do usuário, você pode optar por fazer uma das duas coisas:

- Especifique o banco de dados padrão ao registrar seu servidor com o SQL Server Object Explorer em SSDT, SSMS, ou em sua cadeia de caracteres de conexão do aplicativo. Por exemplo, inclua o parâmetro InitialCatalog para uma conexão ODBC.
- Realça o banco de dados do usuário antes de criar uma sessão em SSDT.

> [AZURE.NOTE] A instrução Transact-SQL **MyDatabase uso;** não há suporte para alterar o banco de dados para uma conexão. Para obter orientação sobre como se conectar a SQL Data Warehouse com SSDT, consulte o artigo de [consulta com o Visual Studio][] .

## <a name="azure-active-directory-aad-authentication"></a>Autenticação do Azure Active Directory (AAD)

[Active Directory do Azure] [ What is Azure Active Directory] autenticação é um mecanismo de conexão depósito de dados do Microsoft Azure SQL usando identidades do Azure Active Directory (AD Azure). Com a autenticação do Active Directory do Azure, você pode gerenciar centralmente as identidades dos usuários de banco de dados e outros serviços da Microsoft em um local central. Gerenciamento de identificação central fornece um local único para gerenciar usuários de depósito de dados do SQL e simplifica o gerenciamento de permissões. 

### <a name="benefits"></a>Benefícios

Benefícios do Azure Active Directory incluem:

- Fornece uma alternativa para a autenticação do SQL Server.
- Ajuda a parar a proliferação de identidades dos usuários em servidores de banco de dados.
- Permite a rotação de senha em um único lugar
- Gerencie permissões de banco de dados usando grupos (AAD) externos.
- Elimina o armazenamento de senhas Ativando autenticação integrada do Windows e outras formas de autenticação compatíveis com o Active Directory do Azure.
- Usos contido usuários de banco de dados para autenticar identidades no nível do banco de dados.
- Suporta a autenticação baseada em token para aplicativos que se conectam depósito de dados do SQL.
- Suporta autenticação multifator por meio de autenticação Universal do Active Directory para SQL Server Management Studio. Para obter uma descrição da autenticação multifator, consulte [SSMS suporte para Azure AD MFA com o banco de dados SQL e SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

> [AZURE.NOTE] Active Directory do Azure ainda é relativamente novo e tem algumas limitações. Para garantir que o Azure Active Directory é uma boa opção para seu ambiente, consulte [recursos do Azure AD e limitações][], especificamente as considerações adicionais.

### <a name="configuration-steps"></a>Etapas de configuração

Siga estas etapas para configurar a autenticação do Active Directory do Azure.

1. Criar e preencher um Active Directory do Azure
2. Opcional: Associar ou alterar o active directory que está associado a sua assinatura do Azure
3. Crie um administrador do Active Directory do Azure para depósito de dados do SQL Azure.
4. Configurar computadores cliente
5. Criar usuários de banco de dados contidos no seu banco de dados mapeado para identidades do Azure AD
6. Conectar ao seu data warehouse usando identidades do Azure AD

Atualmente, os usuários do Active Directory do Azure não são mostrados no Pesquisador de objetos SSDT. Como alternativa, exiba os usuários na [database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
  
### <a name="find-the-details"></a>Encontrar os detalhes
- Conclua as etapas detalhadas. As etapas detalhadas para configurar e usar a autenticação do Active Directory do Azure são praticamente idênticas para depósito de dados do SQL Azure e o Azure SQL Database. Siga as etapas detalhadas no tópico [Conectando-se ao banco de dados do SQL ou SQL dados depósito por usando Azure autenticação do Active Directory](../sql-database/sql-database-aad-authentication.md).
- Criar funções de banco de dados personalizado e adicionar usuários às funções. Conceda permissões granulares às funções. Para obter mais informações, consulte [Introdução ao permissões de mecanismo de banco de dados](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Próximas etapas

Para iniciar consultando seu data warehouse com Visual Studio e outros aplicativos, consulte [consulta com o Visual Studio][].

<!-- Article references -->
[Proteger um banco de dados no depósito de dados do SQL]: ./sql-data-warehouse-overview-manage-security.md
[Consulta com o Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]: ../active-directory/active-directory-whatis.md
[Limitações e os recursos do azure AD]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
