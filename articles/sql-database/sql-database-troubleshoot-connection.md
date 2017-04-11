<properties
    pageTitle="Banco de dados no servidor não está disponível no momento, se conectar ao banco de dados SQL | Microsoft Azure"
    description="Solucionar problemas de banco de dados no servidor não está disponível atualmente erro quando um aplicativo se conecta ao banco de dados SQL."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""
    keywords="banco de dados no servidor não está disponível no momento, se conectar ao banco de dados sql"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="daleche"/>

# <a name="error-database-on-server-is-not-currently-available-when-connecting-to-sql-database"></a>Erro "O banco de dados no servidor não está disponível atualmente" conectar ao banco de dados sql
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Quando um aplicativo se conecta a um banco de dados do SQL Azure, você recebe a seguinte mensagem de erro:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [AZURE.NOTE] Esta mensagem de erro é geralmente temporária (curta duração).

Este erro ocorre quando o banco de dados do Azure está sendo movido (ou reconfigurar) e seu aplicativo perde sua conexão ao banco de dados SQL. Eventos de reconfiguração de banco de dados do SQL ocorre devido a um evento planejado (por exemplo, uma atualização de software) ou um evento não planejado (por exemplo, uma falha de processo ou balanceamento de carga). A maioria dos eventos de reconfiguração são geralmente curta duração e devem ser concluídas em menos de 60 segundos no máximo. No entanto, esses eventos ocasionalmente podem levar mais tempo para concluir, como quando uma transação grande faz com que uma recuperação demorada.

## <a name="steps-to-resolve-transient-connectivity-issues"></a>Etapas para resolver problemas de conectividade temporárias
1.  Verifique o [Painel de serviço do Microsoft Azure](https://azure.microsoft.com/status) para qualquer interrupções conhecidas ocorridas durante o tempo durante o qual os erros foram relatados pelo aplicativo.
2. Aplicativos que conectem a um serviço de nuvem como o Azure SQL Database devo esperar eventos de reconfiguração periódicos e implementar novamente lógica para lidar com esses erros em vez de faceamento esses erros do aplicativo aos usuários. Examine a seção [erros temporárias](sql-database-connectivity-issues.md) e as melhores práticas e diretrizes a [Visão geral de desenvolvimento de banco de dados SQL](sql-database-develop-overview.md) para obter mais informações e gerais repetir estratégias de design. Em seguida, consulte exemplos de código em [Bibliotecas de Conexão de banco de dados SQL e SQL Server](sql-database-libraries.md) para obter informações específicas.
3.  Como um banco de dados se aproxima seus limites de recurso, ele pode parecer ser um problema de conectividade temporárias. Consulte [Solucionar problemas de desempenho](sql-database-troubleshoot-performance.md).
4.  Se continuam a problemas de conectividade, ou se a duração para a qual o seu aplicativo encontra o erro excede 60 segundos ou se você vir várias ocorrências do erro em um determinado dia, enviar uma solicitação de suporte Azure selecionando **Obter suporte** no site de [Suporte do Azure](https://azure.microsoft.com/support/options) .

## <a name="next-steps"></a>Próximas etapas
- Se você receber um erro diferente, avalie a [mensagem de erro](sql-database-develop-error-messages.md) para dicas sobre a causa.
- Se o problema for persistente, visite as orientações no [Solucionar problemas de conexão comuns ao Azure SQL Database](sql-database-troubleshoot-common-connection-issues.md).
