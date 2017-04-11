   <properties
   pageTitle="Carregar dados em um depósito de dados do SQL Azure | Microsoft Azure"
   description="Saiba os cenários comuns para carregamento em SQL Data Warehouse de dados. Eles incluem usando PolyBase, armazenamento de blob do Microsoft Azure, arquivos simples e entrega de disco. Você também pode usar ferramentas de terceiros."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/12/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="load-data-into-azure-sql-data-warehouse"></a>Carregar dados em um depósito de dados do SQL Azure

Um resumo das opções de cenário e recomendações para carregar dados em SQL Data Warehouse.

A parte mais difícil de carregar dados geralmente está preparando os dados para o carregamento. Azure simplifica carregamento usando o armazenamento de blob do Microsoft Azure como um armazenamento de dados comuns para muitos dos serviços, e fábrica de dados do Azure para coordenar a movimentação de dados e a comunicação entre os serviços do Azure. Esses processos são integrados com a tecnologia PolyBase que usa processamento altamente paralelo (MPP) para carregar dados em paralelo do armazenamento de blob do Microsoft Azure em SQL Data Warehouse. 

Para tutoriais que carregam bancos de dados de exemplo, consulte [Carregar bancos de dados de exemplo][].

## <a name="load-from-azure-blob-storage"></a>Carregar do armazenamento de blob do Microsoft Azure
A maneira mais rápida para importar dados para o SQL Data Warehouse é usar PolyBase para carregar dados do armazenamento de blob do Microsoft Azure. PolyBase usa o design de processamento altamente paralelo (MPP) do SQL Data Warehouse para carregar dados em paralelo do armazenamento de blob do Microsoft Azure. Para usar PolyBase, você pode usar comandos T-SQL ou um pipeline de fábrica de dados do Azure.

### <a name="1-use-polybase-and-t-sql"></a>1. usar PolyBase e T-SQL

Resumo do processo de carregamento:

2. Formate os dados como UTF-8 como PolyBase não suportamos UTF-16.
2. Mover seus dados para armazenamento de blob do Microsoft Azure e armazená-la em arquivos de texto.
3. Configurar objetos externos no SQL Data Warehouse para definir o local e o formato dos dados
4. Execute um comando T-SQL para carregar os dados em paralelo em uma nova tabela do banco de dados.

<!-- 5. Schedule and run a loading job. --> 

Para um tutorial, consulte [dados de carga de armazenamento de blob do Microsoft Azure depósito de dados do SQL (PolyBase)][].

### <a name="2-use-azure-data-factory"></a>2. usar fábrica de dados do Azure

Para uma maneira mais simples de usar PolyBase, você pode criar um pipeline de fábrica de dados do Azure que usa PolyBase para carregar dados do armazenamento de blob do Microsoft Azure em SQL Data Warehouse. Isso é rápido para configurar como você não precisa definir os objetos de T-SQL. Se você precisar consultar os dados externos sem importá-los, use T-SQL. 

Resumo do processo de carregamento:

2. Formate os dados como UTF-8 como PolyBase não suportamos UTF-16.
2. Mover seus dados para armazenamento de blob do Microsoft Azure e armazená-la em arquivos de texto.
3. Crie um pipeline de fábrica de dados do Azure para incluir os dados. Use a opção PolyBase.
4. Agendar e executar o pipeline.

Para um tutorial, consulte [dados de carga de armazenamento de blob do Microsoft Azure depósito de dados do SQL (Azure Data Factory)][].


## <a name="load-from-sql-server"></a>Carregar do SQL Server
Para carregar dados do SQL Server depósito de dados do SQL, você pode usar Integration Services (SSIS), transferir arquivos simples ou navio discos à Microsoft. Leitura para ver um resumo dos diferentes carregando processos e links para tutoriais.

Para planejar uma migração de dados completo do SQL Server depósito de dados do SQL, consulte [Visão geral de migração][]. 

### <a name="use-integration-services-ssis"></a>Usar o Integration Services (SSIS)
Se você já estiver usando pacotes do Integration Services (SSIS) para carregar no SQL Server, você pode atualizar seus pacotes para usar o SQL Server como a origem e o SQL Data Warehouse como destino. Isso é rápido e fácil de fazer, e é uma boa opção se você não está tentando migrar seu processo de carregamento para usar dados já na nuvem. A desvantagem é que a carga será mais lenta que usando PolyBase porque este SSIS não executará a carga em paralelo.

Resumo do processo de carregamento:

1. Revise o pacote do Integration Services para apontar para a instância do SQL Server para a fonte e o banco de dados do SQL Data Warehouse para o destino.
2. Migre seu esquema para SQL Data Warehouse, se ainda não estiver lá.
3. Alterar o mapeamento em seus pacotes usar apenas os tipos de dados que são suportados pelo SQL Data Warehouse.
3. Agendar e executar o pacote.

Para um tutorial, consulte [carregar dados do SQL Server para Azure dados depósito SSIS (SQL)][].

### <a name="use-azcopy-recommended-for--10-tb-data"></a>Use AZCopy (recomendado para dados de < 10 TB)
Se o tamanho de dados for < 10 TB, você pode exportar os dados do SQL Server para arquivos simples, copie os arquivos ao armazenamento de blob do Microsoft Azure e depois use PolyBase para carregar os dados em um depósito de dados do SQL

Resumo do processo de carregamento:

1. Use o utilitário de linha de comando bcp para exportar dados do SQL Server para arquivos simples.
2. Use o utilitário de linha de comando AZCopy para copiar dados de arquivos simples ao armazenamento de blob do Microsoft Azure.
3. Use PolyBase para carregar em um depósito de dados do SQL.

Para um tutorial, consulte [dados de carga de armazenamento de blob do Microsoft Azure depósito de dados do SQL (PolyBase)][].

### <a name="use-bcp"></a>Use bcp
Se você tiver uma pequena quantidade de dados que você pode usar bcp para carregar diretamente em um depósito de dados do SQL Azure.

Resumo do processo de carregamento:
1. Use o utilitário de linha de comando bcp para exportar dados do SQL Server para arquivos simples.
2. Use bcp para carregar dados de arquivos simples diretamente para o SQL Data Warehouse.

Para um tutorial, consulte [carregar dados do SQL Server depósito de dados do SQL Azure (bcp)][].


### <a name="use-importexport-recommended-for--10-tb-data"></a>Usar importação/exportação (recomendada para dados > 10 TB)
Se o tamanho de dados for > 10 TB e quiser movê-lo para Azure, recomendamos que você use nosso serviço de [Importação/exportação][]de remessa de disco. 

Resumo do processo de carregamento
2. Use o utilitário de linha de comando bcp para exportar dados do SQL Server para arquivos simples em discos transferível.
3. Envie os discos à Microsoft.
4. Microsoft carrega os dados para o SQL Data Warehouse

## <a name="load-from-hdinsight"></a>Carregar do HDInsight
SQL Data Warehouse suporta carregar dados de HDInsight via PolyBase. O processo é a mesma que Carregando dados do armazenamento de Blob do Azure - usando PolyBase para se conectar ao HDInsight para carregar dados. 

### <a name="1-use-polybase-and-t-sql"></a>1. usar PolyBase e T-SQL

Resumo do processo de carregamento:

2. Formate os dados como UTF-8 como PolyBase não suportamos UTF-16.
2. Mover seus dados para HDInsight e armazená-lo em arquivos de texto, formato ORC ou Parquet.
3. Configure objetos externos no SQL Data Warehouse para definir o local e o formato dos dados.
4. Execute um comando T-SQL para carregar os dados em paralelo em uma nova tabela do banco de dados.

Para um tutorial, consulte [dados de carga de armazenamento de blob do Microsoft Azure depósito de dados do SQL (PolyBase)][].

## <a name="recommendations"></a>Recomendações

Muitos dos nossos parceiros têm Carregando soluções. Para saber mais, veja uma lista de nossos [parceiros de soluções][]. 

Se seus dados é proveniente de uma fonte não-relacionais e deseja carregá-lo no SQL Data Warehouse você precisará transformá-lo em linhas e colunas antes de você carregá-lo. Os dados transformados não precisam ser armazenados em um banco de dados, ele pode ser armazenado em arquivos de texto.

Crie estatísticas em dados carregados recentemente. Depósito de dados SQL Azure faz ainda não suporte automático criar ou automático estatísticas de atualização.  Para obter o melhor desempenho de suas consultas, é importante criar estatísticas sobre todas as colunas de todas as tabelas após o carregamento primeiro ou quaisquer alterações substanciais ocorrerem nos dados.  Para obter detalhes, consulte [estatísticas][].


## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Carregar dados do armazenamento de blob do Microsoft Azure depósito de dados do SQL (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Carregar dados do armazenamento de blob do Microsoft Azure depósito de dados do SQL (Azure Data Factory)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Carregar dados do SQL Server para Azure dados depósito SSIS (SQL)]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[Carregar dados do SQL Server depósito de dados do SQL Azure (bcp)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[Carregar bancos de dados de exemplo]: ./sql-data-warehouse-load-sample-databases.md
[Visão geral de migração]: ./sql-data-warehouse-overview-migrate.md
[parceiros de soluções]: ./sql-data-warehouse-partner-business-intelligence.md
[Visão geral de desenvolvimento]: ./sql-data-warehouse-overview-develop.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Importação/exportação]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/
