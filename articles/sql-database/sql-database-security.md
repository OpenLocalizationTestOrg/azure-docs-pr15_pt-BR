<properties
   pageTitle="Visão geral sobre a segurança do banco de dados SQL"
   description="Saiba mais sobre a segurança de banco de dados do SQL Azure e SQL Server, incluindo as diferenças entre a nuvem e SQL Server local quando se trata de autenticação, autorização, segurança de conexão, criptografia e conformidade."
   services="sql-database"
   documentationCenter=""
   authors="tmullaney"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="06/09/2016"
   ms.author="thmullan;jackr"/>


# <a name="securing-your-sql-database"></a>Proteger seu banco de dados do SQL

## <a name="overview"></a>Visão geral

Este artigo explica as Noções básicas para proteger a camada de dados de um aplicativo usando Azure SQL Database. Em particular, este artigo irá iniciá-lo com os recursos para limitar o acesso, proteção de dados, e monitorar as atividades em um banco de dados criado na [começar a usar o tutorial do banco de dados SQL](sql-database-get-started.md). Para obter uma visão geral completa dos recursos de segurança disponíveis em todas as versões do SQL, consulte o [Centro de segurança para o mecanismo de banco de dados do SQL Server e Azure SQL Database](https://msdn.microsoft.com/library/bb510589). Informações adicionais também estão disponíveis em [segurança e Azure SQL Database white paper técnico](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF).

## <a name="connection-security"></a>Segurança de Conexão

Segurança de Conexão se refere a como você restringir e conexões seguras seu banco de dados usando regras de firewall e criptografia de conexão.

Regras de firewall são usadas pelo servidor e o banco de dados para rejeitar tentativas de conexão de endereços IP que não foram explicitamente na lista branca. Para permitir que o aplicativo ou endereço IP público da máquina cliente tentar se conectar a um novo banco de dados, você deve primeiro criar uma regra de firewall de nível de servidor usando o Portal de clássico do Azure, API REST ou PowerShell. Como prática recomendada, você deve restringir os intervalos de endereços IP permitidos através do firewall servidor tanto quanto possíveis. Para obter mais informações, consulte [Firewall de banco de dados do SQL Azure](https://msdn.microsoft.com/library/ee621782).

Todas as conexões com o banco de dados do Azure SQL exigem criptografia (SSL/TLS) em todas as vezes enquanto dados são "em trânsito" e do banco de dados. Na cadeia de conexão do seu aplicativo, você deve especificar parâmetros para criptografar a conexão e *não* confiar no certificado de servidor (Isso é feito para você se você copiar a cadeia de caracteres de conexão sair do Portal de clássico do Azure), caso contrário, a conexão não verificará a identidade do servidor e estarão sujeitas a "no-a-" ataques com interceptação. Para o driver ADO.NET, por exemplo, esses parâmetros de cadeia de conexão são **criptografar = True** e **TrustServerCertificate = False**. Para obter mais informações, consulte [criptografia de Conexão de banco de dados do Azure SQL e validação do certificado](https://msdn.microsoft.com/library/azure/ff394108#encryption).


## <a name="authentication"></a>Autenticação

Autenticação se refere a como você prova sua identidade ao conectar ao banco de dados. Banco de dados SQL compatível com dois tipos de autenticação:

 - **Autenticação do SQL**, que usa um nome de usuário e senha
 - **Autenticação do azure Active Directory**, que usa identidades gerenciadas pelo Active Directory do Azure e tem suporte para domínios gerenciados e integrados

Quando você criou o servidor lógico para seu banco de dados, você especificou um logon "administrador do servidor" com um nome de usuário e senha. Usando essas credenciais, você pode autenticar a qualquer banco de dados no servidor como o proprietário do banco de dados, ou "dbo". Se você quiser usar a autenticação do Azure Active Directory, você deve criar outro administrador do servidor chamado "Azure AD administrador," que é permitido para administrar Azure AD usuários e grupos. Esse administrador também pode executar todas as operações que um administrador de servidor comum possível. Consulte [Conectando-se ao banco de dados por usando Azure Active Directory autenticação do SQL](sql-database-aad-authentication.md) para instruções passo a passo de como criar um administrador do Azure AD para habilitar a autenticação do Azure Active Directory.

Como uma prática recomendada seu aplicativo deve usar uma conta diferente para autenticar – dessa maneira, você pode limitar as permissões concedidas ao aplicativo e reduzir os riscos de atividades maliciosas em caso de código do seu aplicativo é vulnerável a um ataque de inclusão de SQL. A abordagem recomendada é criar um [contidos usuário de banco de dados](https://msdn.microsoft.com/library/ff929188), que permite que seu aplicativo autenticar diretamente para um banco de dados. Você pode criar um usuário de banco de dados independente que usa a autenticação do SQL executando o seguinte comando T-SQL enquanto estiver conectado ao seu banco de dados do usuário como o logon de administrador do servidor:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Se você criou um administrador do Azure AD, você pode criar um usuário de banco de dados independente que usa autenticação do Azure Active Directory executando o seguinte comando T-SQL enquanto estiver conectado ao seu banco de dados do usuário como administrador do Azure AD:

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

Nos dois casos, a cadeia de conexão do seu aplicativo deve especificar essas credenciais de usuário, em vez do logon de administrador do servidor, para se conectar ao banco de dados.

Para obter mais informações sobre a autenticação de um banco de dados do SQL, consulte [Gerenciar bancos de dados e logon no Azure SQL Database](sql-database-manage-logins.md).


## <a name="authorization"></a>Autorização
Autorização refere-se ao que você pode fazer em um banco de dados do SQL Azure e isso é controlado pelo associações de função da sua conta de usuário e permissões. Como prática recomendada, você deve conceder aos usuários os privilégios mínimos necessários. Banco de dados do SQL Azure torna isso fácil de gerenciar com funções em T-SQL:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

A conta de administrador do servidor com que está se conectando é um membro de db_owner, que tem autoridade para fazer algo no banco de dados. Salve esta conta para implantar as atualizações de esquema e outras operações de gerenciamento. Use a conta "ApplicationUser" com permissões mais limitadas para conectar-se de seu aplicativo para o banco de dados com os privilégios mínimos necessitados para seu aplicativo.

Existem maneiras de limitar ainda mais que um usuário pode fazer com o banco de dados do Azure SQL:

* [Funções de banco de dados](https://msdn.microsoft.com/library/ms189121) diferente db_datareader e db_datawriter podem ser usadas para criar contas de usuário de aplicativo mais avançadas ou menos poderosas contas de gerenciamento.
* Granulares [permissões](https://msdn.microsoft.com/library/ms191291) permitem que você controle quais operações que você pode fazer em colunas individuais, tabelas, exibições, procedimentos e outros objetos no banco de dados.
* [Representação](https://msdn.microsoft.com/library/vstudio/bb669087) e [autenticação de módulo](https://msdn.microsoft.com/library/bb669102) podem ser usados com segurança elevar permissões temporariamente.
* [Segurança em nível de linha](https://msdn.microsoft.com/library/dn765131) pode ser usado limitar quais linhas de um usuário pode acessar.
* [Masking de dados](sql-database-dynamic-data-masking-get-started.md) podem ser usados para limitar a exposição de dados confidenciais.
* [Procedimentos armazenados](https://msdn.microsoft.com/library/ms190782) pode ser usado para limitar as ações que podem ser executadas no banco de dados.

Gerenciando servidores lógicos e bancos de dados a partir do Portal de clássico do Azure ou usar a API do Gerenciador de recursos do Azure é controlada pelo atribuições de função da sua conta de usuário do portal. Para obter mais informações sobre este tópico, consulte [controle de acesso baseado em função no Portal do Azure](../active-directory./role-based-access-control-configure.md).


## <a name="encryption"></a>Criptografia

Banco de dados do SQL Azure pode ajudar a proteger seus dados criptografando seus dados quando for "at rest" ou armazenados em arquivos de banco de dados e backups, usando [Criptografia transparente de dados](http://go.microsoft.com/fwlink/?LinkId=526242). Para criptografar o banco de dados, conecte-se como um proprietário do banco de dados e executar:

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Para outras maneiras criptografar seus segredos de dados, considere:

* [Criptografia de nível de célula](https://msdn.microsoft.com/library/ms179331.aspx) para criptografar colunas específicas ou até mesmo células de dados com chaves de criptografia diferentes.
* Se precisar de um módulo de segurança de Hardware ou gerenciamento central de sua hierarquia de chave de criptografia, considere o uso [Do Azure chave cofre com o SQL Server em uma máquina virtual do Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).
* [Sempre criptografadas](https://msdn.microsoft.com/library/mt163865.aspx) (no modo de visualização) torna a criptografia transparente para aplicativos e permite que os clientes criptografar dados confidenciais dentro de aplicativos de cliente sem compartilhar as chaves de criptografia com o banco de dados SQL.

## <a name="auditing"></a>Auditoria

Auditoria e acompanhamento de eventos de banco de dados podem ajudá-lo a manter a conformidade regulatória e identificar atividades suspeitas. Auditoria de banco de dados do SQL permite que você para registrar eventos em seu banco de dados a uma auditoria fazer logon em sua conta de armazenamento do Azure. Auditoria de banco de dados do SQL também integra o Microsoft Power BI para facilitar a análises e relatórios de busca detalhada. Para obter mais informações, consulte [Introdução ao auditoria de banco de dados do SQL](sql-database-auditing-get-started.md).

Detecção de ameaças de banco de dados do SQL fornece uma camada adicional de segurança na parte superior de auditoria. Ele permite que você responder a ameaças conforme eles ocorrem, fornecendo alertas de segurança em atividades anormais. Para obter mais informações, consulte [Introdução ao detecção de ameaças de banco de dados do SQL](sql-database-threat-detection-get-started.md).  

## <a name="compliance"></a>Conformidade

Além dos recursos e funcionalidades que podem ajudar seu aplicativo atendem a vários requisitos de conformidade de segurança, Azure SQL Database também acima participa auditorias regulares e tenha sido certificada em relação a um número de padrões de conformidade. Para obter mais informações, consulte a [Central de confiabilidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/), onde você pode encontrar a lista mais atual das [certificações de conformidade do banco de dados SQL](https://azure.microsoft.com/support/trust-center/services/).
