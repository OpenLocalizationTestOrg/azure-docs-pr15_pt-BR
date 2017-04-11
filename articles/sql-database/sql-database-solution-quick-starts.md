<properties
   pageTitle="Início rápido do SQL Azure banco de dados solução | Microsoft Azure"
   description="Saiba mais sobre soluções de banco de dados do SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="09/06/2016"
   ms.author="carlrab"/>

# <a name="explore-azure-sql-database-solution-quick-starts"></a>Explorar o início rápido de solução de banco de dados SQL Azure

Este artigo contém uma visão geral de como o Azure SQL banco de dados solução rápida começa. Estas inicia rápida estão localizado no repositório de amostras do GitHub SQL Server e demonstram o uso de banco de dados SQL em uma solução completa com base em cenários reais. Para tutoriais passo a passo simples que demonstram o uso de um determinado recurso de banco de dados SQL, consulte [tutoriais de explorar o Azure SQL Database](sql-database-explore-tutorials.md).

## <a name="try-the-wingtiptickets-demo-and-hands-on-lab"></a>Experimente o WingTipTickets demonstração e laboratório

A demonstração de [WingTipTickets de banco de dados do SQL Azure](https://github.com/microsoft/wingtiptickets) e laboratório demonstram um aplicativo de amostra baseados em pesquisa do Azure que é usado para vender ingressos para um show e Azure SQL Database.


## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools"></a>Coletar e monitorar dados de uso de recursos em vários pools

[Início rápido do solução: telemetria Pool elástica usando o PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) fornece uma solução para coletar e monitorar o uso de recursos do banco de dados SQL em vários pools em uma assinatura. Quando você possui um grande número de bancos de dados em uma assinatura, é difícil de monitorar cada pool elástica separadamente.

Para resolver esse problema, você pode combinar cmdlets do PowerShell do banco de dados SQL e consultas de T-SQL para coletar dados de uso de recursos de vários pools e seus bancos de dados. Isso ajuda você a monitorar e analisar o uso de recursos com mais eficiência.

Esse início rápido fornece um conjunto de scripts do PowerShell e consultas de T-SQL juntamente com a documentação sobre o que faz a solução e como implementá-lo.

## <a name="get-started-with-elastic-database-in-an-saas-scenario"></a>Introdução ao banco de dados elástica em um cenário de SaaS

 [Início rápido do solução: painel de controle personalizado para SaaS Pool elástica](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) fornece uma solução para um cenário de Software como-solução (SaaS) que utiliza o recurso de banco de dados elástica do banco de dados SQL para fornecer um back-end do banco de dados econômica e escaláveis para um aplicativo de SaaS.

Nesta solução, você orientará durante a implementação de um aplicativo web. Este aplicativo web permite que você visualize a carga que é criada em um banco de dados Elástico por um gerador de carga que usa um painel personalizado que complementam o portal do Azure.

Esse início rápido fornece um gerador de carga e monitoramento web app juntamente com a documentação sobre o que o aplicativo faz e como usá-lo.

## <a name="create-an-azure-sql-database-by-using-code-first-development-and-the-entity-framework"></a>Criar um banco de dados do SQL Azure usando o desenvolvimento do Code First e a estrutura de entidades

O vídeo e o exemplo em [Code First para um novo banco de dados](https://msdn.microsoft.com/data/jj193542.aspx) fornece uma introdução ao desenvolvimento Code First direcionado para um novo banco de dados. Este cenário se destina a um banco de dados que não existe, mas que será criado pelo código primeiro. Como alternativa, o cenário cria um banco de dados vazio ao qual Code First adiciona novas tabelas.

Código primeiro permite que você defina seu modelo usando classes c# ou Visual Basic .NET. Você pode executar configuração adicional opcional usando atributos em suas classes e propriedades ou usando uma API fluent.

## <a name="integrate-elastic-database-tools-into-an-entity-framework-application"></a>Integrar ferramentas elástica banco de dados em um aplicativo de estrutura de entidades

A amostra de [biblioteca de cliente do banco de dados elástica com estrutura de entidade](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) mostra as alterações que você precisa fazer em um aplicativo de estrutura de entidade para integrá-lo com [Ferramentas de banco de dados elástica](sql-database-elastic-scale-get-started.md). O foco está em redigir [Fragmentar mapear gerenciamento](sql-database-elastic-scale-shard-map-management.md) e [roteamento de dependentes de dados](sql-database-elastic-scale-data-dependent-routing.md) com a entidade Framework código primeira abordagem.

O [Código primeiro a uma nova amostra de banco de dados para EF](http://msdn.microsoft.com/data/jj193542.aspx) serve como nosso exemplo em execução em todo este exemplo. O código de exemplo que acompanha este documento é parte do conjunto de ferramentas de banco de dados elástica de amostras nas amostras de código do Visual Studio.

## <a name="integrate-elastic-database-tools-with-row-level-security"></a>Integrar ferramentas elástica banco de dados com segurança em nível de linha

[Vários locatários aplicativos com segurança em nível de linha e ferramentas de banco de dados elástica](sql-database-elastic-tools-multi-tenant-row-level-security.md) mostra as alterações que você precisa fazer em um aplicativo de estrutura de entidade para integrar [Ferramentas elástica banco de dados](sql-database-elastic-scale-get-started.md) com [segurança em nível de linha](https://msdn.microsoft.com/library/dn765131). Este exemplo ilustra como usar essas tecnologias juntos para criar um aplicativo com uma camada de dados altamente escaláveis que ofereça suporte a vários locatários fragmentos.

Você pode fazer isso usando o ADO.NET SqlClient ou estrutura de entidade. Este exemplo estende a [biblioteca de cliente do banco de dados elástica com estrutura de entidade](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) adicionando suporte para bancos de dados de vários locatários fragmentar.
Cria um aplicativo de console simples para a criação de blogs e postagens, com quatro locatários e dois bancos de dados de vários locatários fragmentar.

## <a name="create-online-surveys-with-the-tailspin-surveys-application"></a>Criar pesquisas onlinehttps com o aplicativo de Tailspin pesquisas

Este [aplicativo de exemplo de pesquisas de Tailspin](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) é um aplicativo de web vários locatários, chamado de pesquisas, que permite aos usuários criar pesquisas onlinehttps. O exemplo endereços algumas principais preocupações sobre como gerenciar identidades de usuário em um aplicativo de vários locatários, incluindo inscrição, autenticação, autorização e funções de aplicativo.

## <a name="learn-about-the-latest-security-features-of-sql-database-with-the-contoso-clinic-demo-application"></a>Saiba mais sobre os novos recursos de segurança do banco de dados do SQL com o aplicativo de demonstração de clínica Contoso

Este [aplicativo Contoso clínica demonstração](https://github.com/Microsoft/azure-sql-security-sample) apresenta os novos recursos de segurança do banco de dados SQL.

## <a name="next-steps"></a>Próximas etapas

[Explorar tutoriais do Azure SQL Database](sql-database-explore-tutorials.md)
