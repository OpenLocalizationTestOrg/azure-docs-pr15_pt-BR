<properties
    pageTitle="Visão geral de consultas da elástica banco de dados banco de dados do SQL Azure | Microsoft Azure"
    description="Visão geral do recurso de consulta elástica"    
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/27/2016"
    ms.author="torsteng" />

# <a name="azure-sql-database-elastic-database-query-overview-preview"></a>Azure SQL Database elástica banco de dados visão geral de consultas (prévia)

O recurso de consulta de banco de dados elástica (na visualização) permite que você executa uma consulta Transact-SQL que abranja vários bancos de dados no banco de dados de SQL Azure (SQLDB). Ele permite executar consultas de bancos de dados para acessar tabelas remotas e se conectar ferramentas Microsoft e de terceiros (Excel, PowerBI, Tableau, etc.) para consultar em níveis de dados com vários bancos de dados. Usando esse recurso, você pode dimensionar consultas para níveis de dados grandes no banco de dados do SQL e visualizar os resultados em relatórios do business intelligence (BI).

## <a name="documentation"></a>Documentação

* [Começar a usar consultas de bancos de dados](sql-database-elastic-query-getting-started-vertical.md)
* [Relatório em bancos de dados de nuvem dimensionada-out](sql-database-elastic-query-getting-started.md)
* [Consulta através de bancos de dados de nuvem sharded (horizontalmente particionados)](sql-database-elastic-query-horizontal-partitioning.md)
* [Consulta através de bancos de dados de nuvem com diferentes esquemas (verticalmente particionados)](sql-database-elastic-query-vertical-partitioning.md)
* [SP\_executar \_remoto](https://msdn.microsoft.com/library/mt703714)


## <a name="why-use-elastic-queries"></a>Por que usar consultas elástica?

**Banco de dados do SQL Azure**

Consulta através de bancos de dados do SQL Azure completamente no T-SQL. Isso permite somente leitura consultando de bancos de dados remotos. Isso fornece uma opção para os clientes atuais do SQL Server local migrar aplicativos usando nomes de três e quatro parcial ou servidor vinculado ao banco de dados do SQL.

**Disponível no nível padrão** Consulta elástica é suportada no nível de desempenho padrão além da camada de desempenho Premium. Consulte a seção das limitações de visualização abaixo das limitações de desempenho para níveis de desempenho inferiores.

**Notificações por push para bancos de dados remotos**

Consultas elástica agora podem enviar parâmetros SQL para os bancos de dados remotos para execução.

**Execução do procedimento armazenado**

Executar o procedimento armazenado remoto chamadas ou funções remotas usando [sp\_executar \_remoto](https://msdn.microsoft.com/library/mt703714).

**Flexibilidade**

Tabelas externas com o query elástica agora podem se referir a tabelas remotas com um esquema diferente ou o nome da tabela.

## <a name="elastic-database-query-scenarios"></a>Cenários de consulta de banco de dados elástica

O objetivo é facilitar a consultando cenários onde vários bancos de dados contribuam linhas em um único resultado geral. A consulta ou pode ser composta pelo usuário ou aplicativo diretamente ou indiretamente por meio de ferramentas que estão conectadas ao banco de dados. Isso é especialmente útil ao criar relatórios, usando as ferramentas de integração de BI ou dados comerciais — ou qualquer aplicativo que não pode ser alterado. Com uma consulta elástica, você pode consultar entre vários bancos de dados usando a experiência de conectividade do SQL Server familiar em ferramentas como o Excel, PowerBI, Tableau ou Cognos.
Uma consulta elástica permite fácil acesso a um conjunto inteiro de bancos de dados por meio de consultas emitidos por SQL Server Management Studio ou Visual Studio e facilita consultando entre bancos de dados de estrutura de entidade ou outros ambientes de ORM. Figura 1 mostra um cenário onde um aplicativo de nuvem existente (que usa a [biblioteca de cliente do banco de dados elástica](sql-database-elastic-database-client-library.md)) baseia-se em um nível de dados externa em escala, e uma consulta elástica é usada para relatórios de bancos de dados.

**Figura 1** Consulta de banco de dados elástica usada na camada de dados externa dimensionada

![Consulta de banco de dados elástica usada na camada de dados externa dimensionada][1]

Cenários de cliente para consulta elástica são caracterizados por seguintes topologias:

* **Vertical partição – consultas de bancos de dados** (Topologia 1): os dados são particionados verticalmente entre vários bancos de dados em uma camada de dados. Geralmente, diferentes conjuntos de tabelas residem em bancos de dados diferentes. Isso significa que o esquema é diferente em bancos de dados diferentes. Por exemplo, todas as tabelas para estoque estão em um banco de dados enquanto todas as tabelas relacionadas contábil estiverem em um segundo banco de dados. Casos comuns de uso com essa topologia exigem uma consulta através de ou compilar relatórios entre tabelas em vários bancos de dados.
* **Horizontal partição – fragmentação** (Topologia 2): dados são particionados horizontalmente para distribuir linhas em uma escalados check-out de dados camada. Com essa abordagem, o esquema é idêntico em todos os bancos de dados de participantes. Essa abordagem também é chamada de "fragmentação". Fragmentação pode ser executada e gerenciado usando (1) o banco de dados Elástico ferramentas bibliotecas ou (2) autofragmentação. Uma consulta elástica é usada para consultar ou compilar relatórios em muitos fragmentos.

> [AZURE.NOTE] Consulta de banco de dados elástica funciona melhor para eventuais cenários de relatórios, onde a maioria do processamento pode ser executada no nível de dados. Para cargas de trabalho pesadas relatórios ou cenários de warehouse de dados com consultas mais complexas, também considere o uso de [Depósito de dados do SQL Azure](https://azure.microsoft.com/services/sql-data-warehouse/).


## <a name="elastic-database-query-topologies"></a>Elástica topologias de consulta de banco de dados

### <a name="topology-1-vertical-partitioning--cross-database-queries"></a>Topologia 1: Partição Vertical – entre bancos de dados consultas

Para começar a codificação, consulte [Introdução à consulta de bancos de dados (partição vertical)](sql-database-elastic-query-getting-started-vertical.md).

Uma consulta elástica pode ser usada para tornar os dados localizados em um banco de dados SQLDB disponível para outros bancos de dados SQLDB. Isso permite consultas de um banco de dados para se referir a tabelas em qualquer outro SQLDB banco de dados remoto. A primeira etapa é definir uma fonte de dados externa para cada banco de dados remoto. Fonte de dados externa é definida no banco de dados local do qual você deseja obter acesso a tabelas localizado no banco de dados remoto. Não são necessárias alterações no banco de dados remoto. Para verticais partição cenários típicos onde bancos de dados diferentes têm diferentes esquemas, consultas elástica podem ser usadas para implementar casos comuns de uso, como o acesso aos dados de referência e bancos de dados consultando.

**Dados de referência**: 1 de topologia é usado para gerenciamento de dados de referência. Na figura abaixo, as duas tabelas (T1 e T2) com dados de referência são mantidas em um banco de dados dedicado. Usando uma consulta elástica, você agora pode acessar tabelas T1 e T2 remotamente de outros bancos de dados, conforme mostrado na figura. Topologia de uso 1 se tabelas de referência são pequenas ou remotas consultas em tabela de referência ter predicados seletivos.

**Figura 2** Partição - usando dados de referência de consulta para consulta elástica vertical

![Partição - usando dados de referência de consulta para consulta elástica vertical][3]

**Consultando entre bancos de dados**: elástica consultas casos de uso de habilitar que exigem consultando a vários bancos de dados SQLDB. Figura 3 mostra quatro bancos de dados diferentes: CRM, estoque, RH e produtos. Consultas executadas em um banco de dados também precisam de acesso para um ou todos os outros bancos de dados. Usando uma consulta elástica, você pode configurar seu banco de dados para esse caso executando algumas instruções DDL simples em cada um dos quatro bancos de dados. Após essa configuração única, acesso a uma tabela remota é tão simple quanto fazendo referência a uma tabela local de suas consultas de T-SQL ou de suas ferramentas de BI. Essa abordagem é recomendada se as consultas remotas não retornam resultados grandes.

**Figura 3** Partição vertical - usando elástica para a consulta em vários bancos de dados

![Partição vertical - usando elástica para a consulta em vários bancos de dados][4]

### <a name="topology-2-horizontal-partitioning--sharding"></a>Topologia 2: Horizontal partição – fragmentação

Camada de dados usando o query elástica para realizar tarefas relatórios sobre um sharded, ou seja, horizontalmente particionadas, requer um [mapa de fragmentar elástica banco de dados](sql-database-elastic-scale-shard-map-management.md) para representar os bancos de dados da camada de dados. Normalmente, somente um mapa fragmentar único é usado neste cenário e um banco de dados dedicado com recursos de consulta elástica serve como ponto de entrada para consultas de relatório. Somente este banco de dados dedicado precisa acessar o mapa de fragmentar. Figura 4 ilustra essa topologia e sua configuração com o mapa de banco de dados e fragmentar elástica consulta. Os bancos de dados na camada de dados podem ser de qualquer versão de banco de dados do Azure SQL ou edição. Para obter mais informações sobre a biblioteca de cliente do banco de dados elástica e criar mapas de fragmentar, consulte [Fragmentar mapear gerenciamento](sql-database-elastic-scale-shard-map-management.md).

**Figura 4** Partição - usando o query elástica para relatórios sobre níveis de dados sharded horizontal

![Partição - usando o query elástica para relatórios sobre níveis de dados sharded horizontal][5]

> [AZURE.NOTE] O banco de dados de consulta de banco de dados elástica dedicado deve ser um banco de dados do SQL DB v12. Não existem restrições sobre os fragmentos próprios.

Para começar a codificação, consulte [Introdução à consulta de banco de dados elástica para a horizontal partição (fragmentação)](sql-database-elastic-query-getting-started.md).

## <a name="implementing-elastic-database-queries"></a>Consultas de banco de dados elástica implementação

As etapas para implementar elástica consulta para os cenários de partição verticais e horizontais são discutidas nas seções a seguir. Eles também consultem a documentação mais detalhada para os cenários de partição diferentes.

### <a name="vertical-partitioning---cross-database-queries"></a>Partição vertical - bancos de dados consultas

As etapas a seguir configuram consultas de banco de dados elástica para cenários de partição verticais que requerem acesso a uma tabela localizada em bancos de dados remotos SQLDB com o mesmo esquema:

*    [Criar chave de mestre](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [Criar banco de dados com escopo CREDENCIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    [Criar/SOLTAR fonte de dados EXTERNOS](https://msdn.microsoft.com/library/dn935022.aspx) minhaFonteDeDados do tipo **RDBMS**
*    [Tabela externa criar/SOLTAR](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Depois de executar as instruções DDL, você pode acessar a tabela remota "MinhaTabela" como se fosse uma tabela local. Banco de dados do SQL Azure automaticamente abre uma conexão ao banco de dados remoto, processa sua solicitação no banco de dados remoto e retorna os resultados.
Obter mais informações sobre as etapas necessárias para o cenário de partição vertical podem ser encontradas na [consulta elástica para partição vertical](sql-database-elastic-query-vertical-partitioning.md).  

### <a name="horizontal-partitioning---sharding"></a>Partição horizontal - fragmentação

As etapas a seguir Configurar consultas de banco de dados elástica para horizontais cenários partição que requerem acesso a um conjunto de tabela que se encontram em (normalmente) várias SQLDB bancos de dados remotos:

*    [Criar chave de mestre](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [Criar banco de dados com escopo CREDENCIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    Crie um [mapa de fragmentar](sql-database-elastic-scale-shard-map-management.md) representando sua camada de dados usando a biblioteca de cliente elástica banco de dados.   
*    [Criar/SOLTAR fonte de dados EXTERNOS](https://msdn.microsoft.com/library/dn935022.aspx) minhaFonteDeDados do tipo **SHARD_MAP_MANAGER**
*    [Tabela externa criar/SOLTAR](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Após executar essas etapas, você pode acessar a tabela horizontalmente particionada "MinhaTabela" como se fosse uma tabela local. Banco de dados do SQL Azure automaticamente abre várias conexões paralelas com os bancos de dados remotos onde as tabelas são armazenadas física, processa as solicitações nos bancos de dados remotos e retorna os resultados.
Obter mais informações sobre as etapas necessárias para o cenário de partição horizontal podem ser encontradas na [consulta elástica para partição horizontal](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="t-sql-querying"></a>Consultando T-SQL
Depois que você definiu suas fontes de dados externos e suas tabelas externas, você pode usar regulares cadeias de caracteres de conexão do SQL Server para se conectar aos bancos de dados onde você definiu suas tabelas externas. Você pode executar instruções T-SQL sobre suas tabelas externas dessa conexão com limitações descritas abaixo. Você pode obter mais informações e exemplos de consultas de T-SQL nos tópicos a documentação de [partição horizontal](sql-database-elastic-query-horizontal-partitioning.md) e [partição vertical](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Conectividade de ferramentas
Você pode usar cadeias de caracteres de conexão do SQL Server regulares conectem seus aplicativos e ferramentas de integração de BI ou dados de bancos de dados com tabelas externas. Certifique-se de que o SQL Server é suportado como uma fonte de dados para sua ferramenta. Uma vez conectado, consulte o banco de dados de consulta elástica e as tabelas externas no banco de dados exatamente como você faria com qualquer outro SQL Server banco de dados que você se conectar à sua ferramenta.

> [AZURE.IMPORTANT] Autenticação usando o Azure Active Directory com consultas elástica não é suportada atualmente.

## <a name="cost"></a>Custo

Consulta elástica está incluída no custo de bancos de dados do Azure SQL Database. Observe que topologias onde seus bancos de dados remotos estão em uma central de dados diferentes que o ponto de extremidade de consulta elástica são suportadas, mas egresso de dados de bancos de dados remotos cobrado regulares [taxas Azure](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Limitações de visualização
* Executar sua primeira consulta elástica pode levar alguns minutos no nível de desempenho padrão. Esse tempo é necessário para carregar a funcionalidade de consulta elástica; melhora o desempenho do carregamento com níveis de desempenho mais altos.
* Script de fontes de dados externos ou tabelas externas de SSMS ou SSDT ainda não é suportado.
* Importar/exportar para SQL DB ainda não suporta tabelas externas e fontes de dados externos. Se você precisar usar importação/exportação, solte esses objetos antes de exportar e, em seguida, recriá-las após a importação.
* Consulta de banco de dados elástica atualmente suporta apenas o acesso somente leitura para tabelas externas. Entretanto, você pode usar todas as funcionalidades T-SQL no banco de dados onde a tabela externa está definida. Isso pode ser útil para, por exemplo, se mantiver resultados temporários usando, por exemplo, selecione < column_list > em < local_table > ou para definir procedimentos armazenados do banco de dados de consulta elástica que se referem a tabelas externas.
* Exceto para nvarchar (max), não há suporte para tipos LOB em definições de tabela externa. Como alternativa, você pode criar um modo de exibição no banco de dados remoto que projeta o tipo LOB em nvarchar (max), definir sua tabela externa sobre o modo de exibição em vez da tabela base e convertê-la volta para o tipo LOB original em suas consultas.
* Estatísticas de coluna sobre tabelas externas não são suportadas no momento. Estatísticas de tabelas são suportadas, mas precisam ser criados manualmente.

## <a name="feedback"></a>Comentários
Compartilhe comentários sobre sua experiência com consultas elástica conosco em Disqus abaixo, os fóruns do MSDN, ou em Stackoverflow. Estamos interessados em todos os tipos de comentários sobre o serviço (defeitos, arestas, lacunas de recurso).

## <a name="more-information"></a>Mais informações

Você pode encontrar mais informações sobre a consulta de bancos de dados e a cenários de partição verticais nos seguintes documentos:

* [Consultando entre bancos de dados e a visão geral de partição vertical](sql-database-elastic-query-vertical-partitioning.md)
* Tente nosso tutorial passo a passo para ter uma completa exemplo prático executado em minutos: [Introdução à consulta de bancos de dados (partição vertical)](sql-database-elastic-query-getting-started-vertical.md).


Obter mais informações sobre cenários de partição e fragmentação horizontais estão disponíveis aqui:

* [Visão geral de partição e fragmentação horizontal](sql-database-elastic-query-horizontal-partitioning.md)
* Tente nosso tutorial passo a passo para ter uma completa exemplo prático executado em minutos: [Introdução à consulta de banco de dados elástica para a horizontal partição (fragmentação)](sql-database-elastic-query-getting-started.md).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
