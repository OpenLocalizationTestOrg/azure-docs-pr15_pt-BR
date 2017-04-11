<properties
   pageTitle="Diretrizes e limitações de geral de banco de dados do SQL Azure"
   description="Esta página descreve algumas limitações gerais para banco de dados do SQL Azure, bem como áreas de interoperabilidade e suporte."
   services="sql-database"
   documentationCenter="na"
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/06/2016"
   ms.author="carlrab" />

# <a name="azure-sql-database-general-limitations-and-guidelines"></a>Diretrizes e limitações de geral de banco de dados do SQL Azure

Este tópico fornece diretrizes e limitações gerais do Azure SQL Database. Para obter uma compreensão completa de cotas, gerenciamento de recursos e suporte, consulte os [recursos adicionais](#additional-guidelines) no final deste tópico.

## <a name="connectivity-and-authentication"></a>Autenticação e conectividade

  - Não há suporte para a autenticação do Windows. Consulte [Gerenciar bancos de dados e logon no banco de dados do SQL Azure](sql-database-manage-logins.md). No entanto, a autenticação do Azure Active Directory é compatível com algumas limitações. Consulte [conectar ao banco de dados do SQL com a autenticação do Active Directory do Azure](sql-database-aad-authentication.md).

  - Banco de dados SQL do Microsoft Azure suporta dados tabulares fluxo (TDS) protocolo cliente versão 7.3 ou posterior.

  - Apenas as conexões de TCP/IP são permitidas.

  - Não há suporte para o navegador do SQL Server 2008 SQL Server porque o banco de dados do Microsoft Azure SQL não tem portas dinâmicas, somente a porta 1433.

## <a name="sql-server-agentjobs"></a>Agente/trabalhos do SQL Server

Banco de dados do Microsoft Azure SQL não oferece suporte para o SQL Server Agent, porém você pode usar trabalhos Elástico para executar trabalhos em um para muitos bancos de dados. Para obter mais informações sobre trabalhos elástica, consulte [Elástico trabalhos](sql-database-elastic-jobs-overview.md).

## <a name="sql-server-collation-support"></a>Suporte de agrupamento do SQL Server

O agrupamento de banco de dados padrão usado pelo Microsoft Azure SQL Database é **SQL_LATIN1_GENERAL_CP1_CI_AS**, onde **LATIN1_GENERAL** é inglês (Estados Unidos), **CP1** é a página de código 1252, **CI** diferencia maiusculas de minúsculas e **está acento** . Não é possível alterar o agrupamento para V12 bancos de dados. Para obter mais informações sobre como configurar o agrupamento, consulte [Agrupar (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="naming-requirements"></a>Requisitos de nomenclatura

Certos nomes de usuário não são permitidos por razões de segurança. Você não pode usar os nomes a seguir:

 - **administração**
 - **administrador**
 - **convidado**
 - **raiz**
 - **SA**

Nomes para todos os novos objetos devem ser compatíveis com as regras do SQL Server para identificadores. Para obter mais informações, consulte [identificadores](https://msdn.microsoft.com/library/ms175874.aspx).

Além disso, os nomes de login e o usuário não podem conter o \ caractere (não há suporte para a autenticação do Windows).

## <a name="additional-guidelines"></a>Diretrizes adicionais

- Além das limitações gerais descritas neste artigo, o banco de dados SQL tem limitações com base em sua **camada de serviço**e as cotas de recursos específicos. Para obter uma visão geral dos níveis de serviço, consulte [níveis de serviço de banco de dados SQL](sql-database-service-tiers.md).

- Para outros limites de banco de dados SQL, consulte [Limites de recursos de banco de dados do Azure SQL](sql-database-resource-limits.md).

- Para segurança diretrizes relacionadas, consulte [diretrizes de segurança de banco de dados do Azure SQL e limitações](sql-database-security-guidelines.md).

- Outra área relacionada envolve a compatibilidade com banco de dados do SQL Azure com versões de local do SQL Server, como 2014 do SQL Server e SQL Server 2016. Versão mais recente do V12 do Azure SQL Database fez muitos aprimoramentos nesta área. Para obter mais detalhes, consulte [o que há de novo no V12 de banco de dados do SQL](sql-database-v12-whats-new.md).

- Para obter informações sobre a disponibilidade de driver e suporte para o banco de dados SQL, consulte [Bibliotecas de Conexão de banco de dados SQL e SQL Server](sql-database-libraries.md).
