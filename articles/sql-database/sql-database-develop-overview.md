<properties
    pageTitle="Visão geral de desenvolvimento de banco de dados SQL | Microsoft Azure"
    description="Saiba mais sobre bibliotecas de conectividade disponível e práticas recomendadas para aplicativos que se conectam ao banco de dados do SQL."
    services="sql-database"
    documentationCenter=""
    authors="annemill"
    manager="jhubbard"
    editor="genemi"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="annemill"/>

# <a name="sql-database-development-overview"></a>Visão geral de desenvolvimento de banco de dados do SQL
Este artigo explica as considerações básicas que um desenvolvedor deve estar ciente ao escrever código para se conectar ao banco de dados do SQL Azure.

## <a name="language-and-platform"></a>Idioma e plataforma
Exemplos de código estão disponíveis para várias plataformas e idiomas de programação. Você pode encontrar links para as amostras de código em: 

* Mais informações: [bibliotecas de Conexão de banco de dados SQL e SQL Server](sql-database-libraries.md)

## <a name="resource-limitations"></a>Limitações de recursos
Banco de dados do SQL Azure gerencia os recursos disponíveis para um banco de dados usando dois mecanismos diferentes: gestão de recursos e imposição de limites.

* Mais informações: [limites de recursos do banco de dados do SQL Azure](sql-database-resource-limits.md)

## <a name="security"></a>Segurança
Banco de dados do SQL Azure fornece recursos para limitar o acesso, proteção de dados e monitorar as atividades em um banco de dados do SQL.

* Mais informações: [proteger seu banco de dados do SQL](sql-database-security.md)

## <a name="authentication"></a>Autenticação
* Banco de dados do SQL Azure suporta os usuários de autenticação do SQL Server e logon, bem como os usuários de [autenticação do Active Directory do Azure](sql-database-aad-authentication.md) e logon.
* Você precisa especificar um determinado banco de dados, em vez de banco de dados *mestre* o padrão.
* Você não pode usar a instrução Transact-SQL **USE myDatabaseName;** no banco de dados SQL para alternar para outro banco de dados.
* Mais informações: [segurança de banco de dados SQL: gerenciar a segurança de acesso e logon do banco de dados](sql-database-manage-logins.md)

## <a name="resiliency"></a>Resiliência
Quando ocorre um erro temporário ao conectar ao banco de dados SQL, seu código deverá repetir a chamada.  Recomendamos que repetir lógica de retirada do uso de lógica, para que ele não sobrecarregar o banco de dados do SQL com vários clientes repetindo simultaneamente.

* Exemplos de código: para exemplos de código que ilustram uma lógica de repetição, consulte exemplos para o idioma de sua preferência em: [bibliotecas de Conexão de banco de dados SQL e SQL Server](sql-database-libraries.md)
* Mais informações: [mensagens de erro para programas de cliente do banco de dados SQL](sql-database-develop-error-messages.md)

## <a name="managing-connections"></a>Gerenciando conexões
* Em sua lógica de conexão do cliente, substitua o tempo limite padrão para que ele seja 30 segundos.  O padrão de 15 segundos é muito curto para conexões que dependem da internet.
* Se você estiver usando um [pool de conexão](http://msdn.microsoft.com/library/8xx3tyca.aspx), certifique-se de fechar a conexão instantâneas seu programa não está usando ativamente-lo e não está se preparando para reutilizá-la.

## <a name="network-considerations"></a>Considerações de rede
* No computador que hospeda seu programa cliente, certifique-se do que Firewall permite comunicação de saída TCP na porta 1433.  Mais informações: [Configurar um firewall de banco de dados do SQL Azure](sql-database-configure-firewall-settings.md)
* Se o programa cliente se conecta ao V12 de banco de dados do SQL enquanto seu cliente é executado em uma máquina virtual Azure (máquina virtual), você deverá abrir determinados intervalos de porta na máquina virtual. Mais informações: [portas além 1433 para ADO.NET 4,5 e V12 de banco de dados do SQL](sql-database-develop-direct-route-ports-adonet-v12.md)
* Conexões de cliente para V12 de banco de dados do SQL Azure, às vezes, ignoram o proxy e interagem diretamente com o banco de dados. Portas diferente 1433 ficam importantes. Mais informações: [portas além 1433 para ADO.NET 4,5 e V12 de banco de dados do SQL](sql-database-develop-direct-route-ports-adonet-v12.md)

## <a name="data-sharding-with-elastic-scale"></a>Fragmentação de dados com dimensionamento Elástico
Dimensionamento Elástico simplifica o processo de dimensionamento-out (e em). 

* [Padrões de design para aplicativos de vários locatários SaaS com banco de dados do SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Roteamento dependentes de dados](sql-database-elastic-scale-data-dependent-routing.md)
* [Introdução ao visualização de dimensionamento Elástico de banco de dados do SQL Azure](sql-database-elastic-scale-get-started.md)

## <a name="next-steps"></a>Próximas etapas

Explore todos os [recursos do banco de dados SQL](https://azure.microsoft.com/services/sql-database/).
