<properties
   pageTitle="Solução de problemas de depósito de dados do SQL Azure | Microsoft Azure"
   description="Solucionando problemas de depósito de dados do SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="sonyama;barbkess"/>

# <a name="troubleshooting-azure-sql-data-warehouse"></a>Solução de problemas de depósito de dados do SQL Azure

Este tópico lista algumas das perguntas de solução de problemas mais comuns que ouvimos de nossos clientes.

## <a name="connecting"></a>Conectando

| Problema                              | Resolução                                      |
| :----------------------------------| :---------------------------------------------- |
| Falha de logon do usuário 'NT\LOGON'. (Microsoft SQL Server, erro: 18456) | Este erro ocorre quando um usuário AAD tenta se conectar ao banco de dados mestre, mas não tem um usuário no mestre.  Para corrigir esse problema duas especificar Data Warehouse do SQL que você deseja se conectar ao momento da conexão ou adicionar o usuário ao banco de dados mestre.  Consulte o artigo de [Visão geral de segurança][] para obter mais detalhes.|
|O servidor principal "MyUserName" não é possível acessar o banco de dados "mestre" no contexto de segurança atual. Não é possível abrir o banco de dados do usuário padrão. Falha no login. Falha de logon do usuário 'MyUserName'. (Microsoft SQL Server, erro: 916) | Este erro ocorre quando um usuário AAD tenta se conectar ao banco de dados mestre, mas não tem um usuário no mestre.  Para corrigir esse problema duas especificar Data Warehouse do SQL que você deseja se conectar ao momento da conexão ou adicionar o usuário ao banco de dados mestre.  Consulte o artigo de [Visão geral de segurança][] para obter mais detalhes.|
| Erro CTAIP                        | Este erro pode ocorrer quando um logon tiver sido criado no SQL server mestre banco de dados, mas não no banco de dados SQL Data Warehouse.  Se você encontrar esse erro, examine o artigo de [Visão geral de segurança][] .  Este artigo explica como criar criar um usuário e faça logon no mestre e, em seguida, como criar um usuário do banco de dados do SQL Data Warehouse.|
| Bloqueado pelo Firewall                |Bancos de dados do SQL Azure são protegidos por firewalls níveis de servidor e banco de dados para garantir que só conhecido endereços IP tiver acesso a um banco de dados. Os firewalls são seguros por padrão, o que significa que você deve ativar explicitamente e o endereço IP ou o intervalo de endereços antes que você possa se conectar.  Para configurar seu firewall para o access, siga as etapas no [acesso ao firewall configurar servidor para o IP do cliente][] nas [instruções de provisionamento][].|
| Não consegue se conectar com o driver ou ferramenta | SQL Data Warehouse recomenda usando [SSMS][], [SSDT para Visual Studio 2015][]ou [sqlcmd][] para consultar seus dados. Para obter mais detalhes sobre drivers e conectando depósito de dados do SQL, consulte os artigos de [Drivers para depósito de dados do SQL Azure][] e [Conectar depósito de dados do SQL Azure][] .|


## <a name="tools"></a>Ferramentas

| Problema                              | Resolução                                      |
| :----------------------------------| :---------------------------------------------- |
| Gerenciador de objeto do Visual Studio está faltando usuários AAD | Este é um problema conhecido.  Como alternativa, exiba os usuários na [database_principals][].  Consulte [autenticação depósito de dados do SQL Azure][] para saber mais sobre como usar o Active Directory do Azure com o SQL Data Warehouse.|

## <a name="performance"></a>Desempenho

|  Problema                             | Resolução                                      |
| :----------------------------------| :---------------------------------------------- |
| Solução de problemas de desempenho de consulta  | Se você estiver tentando solucionar problemas de uma determinada consulta, comece com [aprendendo a monitorar suas consultas][].|
| Planos e desempenho de consulta má geralmente é um resultado de estatísticas ausentes   | A causa mais comum de mau desempenho é falta de estatísticas sobre suas tabelas.  Ver [as estatísticas de tabela de manutenção] [ Statistics] para obter detalhes sobre como criar estatísticas e porque eles são essenciais ao seu desempenho.|
| Concorrência baixa / consultas na fila   | Noções básicas sobre [gerenciamento de carga de trabalho][] é importante para compreender como saldo alocação de memória com concorrência.|
| Como implementar práticas recomendadas    | Coloque o melhor para começar a aprender maneiras de melhorar o desempenho da consulta é o artigo de [SQL Data Warehouse práticas recomendadas][] .|
| Como melhorar o desempenho com dimensionamento  | Às vezes, a solução para melhorar o desempenho é simplesmente adicionar que mais poder às consultas dimensionando [Seu SQL Data Warehouse][]de computação.|
| Desempenho de consulta má como resultado de qualidade de índice ruim | Consultas de algumas vezes podem lentidão devido [columnstore má qualidade de índice][].  Consulte este artigo para obter mais informações e como [reconstruir índices para melhorar a qualidade de segmento][].|

## <a name="system-management"></a>Gerenciamento de sistema

|  Problema                             | Resolução                                      |
| :----------------------------------| :---------------------------------------------- |
| Msg 40847: Não pôde executar a operação porque o servidor excedem a cota de unidade de transação de banco de dados permitida de 45000. | Reduzir a [DWU][] do banco de dados que você está tentando criar ou [solicitar um aumento de cota][].|
| Investigar utilização de espaço    | Consulte [tamanhos de tabela][] para entender a utilização de espaço do seu sistema.|
| Ajuda com o gerenciamento de tabelas          | Consulte [Visão geral de tabela] [ Overview] artigo para obter ajuda com o gerenciamento de suas tabelas.  Este artigo também inclui links para tópicos mais detalhados como [tipos de dados de tabela][Data types], [Distribuindo uma tabela][Distribute], [uma tabela de indexação][Index], [partição uma tabela][Partition], [estatísticas de tabela de manutenção] [ Statistics] e [tabelas temporárias][Temporary].|

## <a name="polybase"></a>Polybase

|  Problema                             | Resolução                                      |
| :----------------------------------| :---------------------------------------------- |
| Erro de UTF-8                        |  Atualmente PolyBase suporta apenas carregar arquivos de dados que foram codificados UTF-8.  Consulte [trabalhar em torno a exigência de PolyBase UTF-8][] para obter orientações sobre como contornar essa limitação.|
| Carregamento falhar devido linhas grandes   | Atualmente o suporte de linha grande não está disponível para Polybase.  Isso significa que, se sua tabela contém varchar (max), nvarchar (max) ou varbinary (max), tabelas externas não podem ser usadas para carregar seus dados.  Cargas para linhas grandes está atualmente só é suportado através do Azure Data Factory (com BCP), análise de fluxo de Azure, SSIS, BCP ou da classe .NET SQLBulkCopy. Suporte a PolyBase linhas grandes será adicionado em uma versão futura.|
| carga de BCP da tabela com tipo de dados do MAX está falhando | Há um problema conhecido que requer que varchar (max), nvarchar (max) ou varbinary (max) ser colocado no final da tabela em alguns cenários.  Tente mover o máximo de colunas para o fim da tabela.|

## <a name="differences-from-sql-database"></a>Diferenças de banco de dados SQL

|  Problema                             | Resolução                                      |
| :----------------------------------| :---------------------------------------------- |
| Recursos sem suporte do banco de dados SQL  | Consulte [recursos de tabela sem suporte][].|
| Tipos de dados sem suporte do banco de dados SQL  | Consulte [tipos de dados sem suporte][].|
| EXCLUIR e limitações de atualização      | Consulte [soluções alternativas de atualizar][], [Excluir soluções alternativas][] e [Usando CTAS para contornar sintaxe de atualizar e excluir sem suporte][].  |
| Não há suporte para a declaração de mesclagem   | Consulte [Mesclar soluções alternativas][].|
| Limitações de procedimento armazenado       | Consulte [limitações de procedimento armazenado][] entender algumas das limitações de procedimentos armazenados.|
| UDFs não dão suporte a instruções SELECT | Isso é uma limitação atual do nossos UDFs.  Consulte [Criar função][] para a sintaxe que oferecemos suporte.   |

## <a name="next-steps"></a>Próximas etapas

Se você estiver foram não é possível encontrar uma solução para seu problema acima, aqui estão alguns outros recursos que você pode tentar.

- [Blogs]
- [Solicitações de recursos]
- [Vídeos]
- [GATO blogs da equipe]
- [Criar tíquete]
- [Fórum do MSDN]
- [Fórum de estouro de pilha]
- [Twitter]

<!--Image references-->

<!--Article references-->
[Visão geral de segurança]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT de 2015 do Visual Studio]: ./sql-data-warehouse-install-visual-studio.md
[Drivers para depósito de dados do SQL Azure]: ./sql-data-warehouse-connection-strings.md
[Conectar ao depósito de dados do SQL Azure]: ./sql-data-warehouse-connect-overview.md
[Criar tíquete]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Dimensionamento de seu Data Warehouse SQL]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[solicitar um aumento de cota]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change 
[Aprender como monitorar suas consultas]: ./sql-data-warehouse-manage-monitor.md
[Instruções de provisionamento]: ./sql-data-warehouse-get-started-provision.md
[Configurar o acesso de firewall do servidor para seu IP do cliente]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[Práticas recomendadas de depósito de dados do SQL]: ./sql-data-warehouse-best-practices.md
[Tamanhos de tabela]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Recursos de tabela sem suporte]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Tipos de dados sem suporte]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Columnstore má qualidade de índice]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Recria índices para melhorar a qualidade do segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Gerenciamento de carga de trabalho]: ./sql-data-warehouse-develop-concurrency.md
[Usando CTAS para contornar sintaxe de atualizar e excluir sem suporte]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[ATUALIZAR soluções alternativas]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[Excluir soluções alternativas]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[Mesclar soluções alternativas]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Limitações de procedimento armazenado]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Autenticação depósito de dados do SQL Azure]: ./sql-data-warehouse-authentication.md
[Trabalhando em torno a exigência de PolyBase UTF-8]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CRIAR FUNÇÃO]: https://msdn.microsoft.com/library/mt203952.aspx
[Sqlcmd]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[GATO blogs da equipe]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Solicitações de recursos]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Fórum de estouro de pilha]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

