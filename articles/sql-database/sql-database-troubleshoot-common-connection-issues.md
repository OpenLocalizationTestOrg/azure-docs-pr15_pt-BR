<properties
    pageTitle="Solucionar problemas de conexão comuns do banco de dados do Azure SQL"
    description="Etapas para identificar e resolver erros comuns de conexão de banco de dados do SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>

# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Solucionar problemas de conexão ao banco de dados do SQL Azure

Quando a conexão de banco de dados do Azure SQL falha, você recebe [mensagens de erro](sql-database-develop-error-messages.md). Este artigo é um tópico centralizado que ajuda você a solucionar problemas de conectividade do Azure SQL Database. Ele apresenta [o comum causa](#cause) de problemas de conexão, recomenda [uma ferramenta de solução de problemas](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) que ajudam a identidade o problema e fornece etapas de solução de problemas para resolver [erros de temporárias](#troubleshoot-transient-errors) e [persistente ou não transitório](#troubleshoot-the-persistent-errors). Por fim, ela lista [todos os artigos relevantes para problemas de conectividade do Azure SQL Database](#all-topics-for-azure-sql-database-connection-problems).

Se você encontrar os problemas de conexão, tente as etapas de solução de problemas descritos neste artigo.
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Causa

Problemas de Conexão podem ser causados por qualquer um destes procedimentos:

- Falha ao aplicar diretrizes de design e práticas recomendadas durante o processo de design do aplicativo.  Consulte [Visão geral de desenvolvimento de banco de dados do SQL](sql-database-develop-overview.md) para começar.
- Reconfiguração de banco de dados do SQL Azure
- Configurações de firewall
- Tempo limite de Conexão
- Informações de login incorreto
- Limite máximo alcançado em alguns recursos de banco de dados do Azure SQL

Em geral, problemas de conexão ao banco de dados do Azure SQL podem ser classificados da seguinte maneira:

- [Erros temporárias (curta duração ou intermitentes)](#troubleshoot-transient-errors)
- [Persistentes ou não transitório erros (erros que regularmente recorrente)](#troubleshoot-the-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Tente a solução de problemas para problemas de conectividade de banco de dados do Azure SQL

Se você encontrar um erro de conexão específicas, experimente [Esta ferramenta](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), que irá ajudá-lo rapidamente a identidade e resolver o problema.

## <a name="troubleshoot-transient-errors"></a>Solucionar problemas de erros temporárias
Se seu aplicativo estiver enfrentando erros temporárias, consulte os tópicos a seguir para obter dicas sobre como solucionar problemas e reduzir a frequência desses erros:

- [Solução de problemas de banco de dados &lt;x&gt; no servidor &lt;y&gt; não estiver disponível (erro: 40613)](sql-database-troubleshoot-connection.md)
- [Solucionar problemas, diagnosticar e evitar erros de conexão de SQL e temporárias do banco de dados SQL](sql-database-connectivity-issues.md)

<a id="troubleshoot-the-persistent-errors" name="troubleshoot-the-persistent-errors"></a>

## <a name="troubleshoot-persistent-errors-non-transient-errors"></a>Solucionar problemas de erros persistentes (não transitório erros)

Se o aplicativo constantemente falhar para se conectar ao banco de dados do SQL Azure, ele geralmente indica um problema com um dos seguintes procedimentos:

- Configuração do firewall. O firewall de banco de dados ou do lado do cliente do SQL Azure está bloqueando conexões com o Azure SQL Database.
- Reconfiguração no lado do cliente de rede: por exemplo, um novo endereço IP ou um servidor proxy.
- Erro de usuário: por exemplo, digitado incorretamente parâmetros de conexão, como o nome do servidor na cadeia de conexão.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Etapas para resolver problemas de conectividade persistente

1.  Configure [regras de firewall](sql-database-configure-firewall-settings.md) para permitir que o cliente do endereço IP.
2.  Em todos os firewalls entre o cliente e a Internet, certifique-se de que a porta 1433 está aberta para conexões de saída. Examine [Configurar Firewall do Windows para permitir o acesso do SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) para sugestões adicionais.
3.  Verifique se a cadeia de conexão e outras configurações de conexão. Consulte a seção de cadeia de Conexão no [tópico de problemas de conectividade](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4.  Verificar a integridade do serviço no painel de controle. Se você achar que há uma interrupção regional, consulte [recuperar uma interrupção](sql-database-disaster-recovery.md) para obter as etapas para recuperar para uma nova região.

## <a name="all-topics-for-azure-sql-database-connection-problems"></a>Todos os tópicos para problemas de conexão de banco de dados do Azure SQL

A tabela a seguir lista cada tópico de problema de conexão que se aplica diretamente para o serviço do Azure SQL Database.


| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 1 | [Solucionar problemas de conexão ao banco de dados do SQL Azure](sql-database-troubleshoot-common-connection-issues.md) | Esta é a página inicial para solucionar problemas de conectividade no Azure SQL Database. Descreve como identificar e resolver erros temporárias e persistentes ou não transitório erros Azure SQL Database. |
| 2 | [Solucionar problemas, diagnosticar e evitar erros de conexão de SQL e temporárias do banco de dados SQL](sql-database-connectivity-issues.md) | Saiba como solucionar problemas, diagnosticar e evitar que um erro de conexão de SQL ou temporárias no Azure SQL Database. |
| 3 | [Orientação de manipulação de falhas temporária geral](best-practices-retry-general.md) | Fornece orientações gerais para manipulação de falhas temporárias ao conectar com o Azure SQL Database. |
| 4 | [Solucionar problemas de conectividade com o banco de dados do Microsoft Azure SQL](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database) | Esta ferramenta ajuda a identidade seu problema solucionar erros de conexão. |
| 5 | [Solucionar problemas de "banco de dados &lt;x&gt; no servidor &lt;y&gt; não está disponível atualmente. Tente a conexão mais tarde"Erro](sql-database-troubleshoot-connection.md) | Descreve como identificar e resolver um erro de 40613: "banco de dados &lt;x&gt; no servidor &lt;y&gt; não está disponível atualmente. Tente a conexão mais tarde". |
| 6 | [Códigos de erro SQL para aplicativos de cliente do banco de dados do SQL: erro de conexão e outros problemas de banco de dados](sql-database-develop-error-messages.md) | Fornece informações sobre códigos de erro SQL para aplicativos de cliente do banco de dados SQL, como os erros comuns de conexão de banco de dados, problemas de cópia do banco de dados e erros gerais. |
| 7 | [Diretrizes de desempenho de banco de dados do SQL Azure para bancos de dados único](sql-database-performance-guidance.md) | Fornece orientação para ajudá-lo a determinar qual nível de serviço é adequado para seu aplicativo. Também fornece recomendações para ajustar o aplicativo para aproveitar ao máximo o seu banco de dados do SQL Azure. |
| 8 | [Visão geral de desenvolvimento de banco de dados do SQL](sql-database-develop-overview.md) | Fornece links para exemplos de código para várias tecnologias que você pode usar para se conectar aos e interagir com o Azure SQL Database. |
| 9 | Atualizar a página de v12 Azure SQL Database ([portal Azure](sql-database-upgrade-server-portal.md), [PowerShell](sql-database-upgrade-server-powershell.md)) | Fornece instruções para atualizar os servidores de V11 de banco de dados do SQL Azure e bancos de dados existentes para V12 de banco de dados do SQL Azure usando o portal do Azure ou PowerShell. |


## <a name="next-steps"></a>Próximas etapas

- [Solucionar problemas de desempenho do Azure SQL Database](sql-database-troubleshoot-performance.md)
- [Solucionar problemas de permissões de banco de dados do Azure SQL](sql-database-troubleshoot-permissions.md)
- [Ver todos os tópicos para o serviço de banco de dados do Azure SQL](sql-database-index-all-articles.md)
- [Procurar a documentação do Microsoft Azure](http://azure.microsoft.com/search/documentation/)
- [Exibir as atualizações mais recentes para o serviço de banco de dados do Azure SQL](http://azure.microsoft.com/updates/?service=sql-database)


## <a name="additional-resources"></a>Recursos adicionais

- [Visão geral de desenvolvimento de banco de dados do SQL](sql-database-develop-overview.md)
- [Orientação de manipulação de falhas temporária geral](../best-practices-retry-general.md)
- [Bibliotecas de Conexão de banco de dados SQL e SQL Server](sql-database-libraries.md)
- [Caminho de aprendizagem para usar o banco de dados do SQL Azure](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database)
- [O caminho de aprendizagem para usar ferramentas e recursos elástica banco de dados](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale) 
