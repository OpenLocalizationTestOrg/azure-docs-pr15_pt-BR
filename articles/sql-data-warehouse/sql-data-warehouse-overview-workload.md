<properties
   pageTitle="Carga de trabalho de depósito de dados"
   description="Elasticidade do SQL Data Warehouse lhe permite crescer, reduzir ou pausar computação power usando uma escala de unidades de depósito de dados (DWUs). Este artigo explica as métricas de depósito de dados e como se relacionam com DWUs. "
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/25/2016"
   ms.author="barbkess;mausher;jrj;sonyama"/>


# <a name="data-warehouse-workload"></a>Carga de trabalho de depósito de dados
Uma carga de trabalho de depósito de dados se refere a todas as operações que ocorrer em relação a um depósito de dados. A carga de trabalho de depósito de dados abrange todo o processo de carregar dados para o depósito, realizando análises e relatórios sobre o data warehouse, gerenciamento de dados no data warehouse e exportação de dados de data warehouse. A profundidade e amplitude desses componentes são geralmente proporcionais com o nível de maturidade do data warehouse.


## <a name="new-to-data-warehousing"></a>Começando a data warehouse?
Um depósito de dados é uma coleção de dados que é carregado de uma ou mais fontes de dados e são usados para realizar tarefas como relatórios e análise de dados do business intelligence.

Depósitos de dados são caracterizados por consultas que digitalizar números maiores de linhas, intervalos grandes de dados e podem retornar resultados relativamente grandes para fins de análise e relatórios. Depósitos de dados também são caracterizados por cargas de dados relativamente grandes versus pequenas nível da transação inserções/atualizações/exclusões.

- Um depósito de dados funciona melhor quando os dados são armazenados de maneira que otimiza consultas que precisam digitalizar grande número de linhas ou intervalos grandes de dados. Esse tipo de varredura funciona melhor quando os dados são armazenados e pesquisados por colunas, em vez de linhas.

>[AZURE.NOTE] Índice columnstore na memória, que usa o armazenamento de coluna, fornece até 10 x ganhos de compactação e 100 x ganhos de desempenho de consulta sobre árvores binárias tradicionais para consultas de análise e geração de relatórios. Consideramos columnstore índices como o padrão para armazenar e examinar dados grandes em um data warehouse.

- Um depósito de dados tem requisitos diferentes que um sistema otimiza para processamento (OLTP) de transações online. O sistema OLTP tem muitos inserir, atualizar e excluir operações. Essas operações de busca para linhas específicas da tabela. Tabela buscas executar melhor quando os dados são armazenados em uma forma de linha por linha. Os dados podem ser classificados e rapidamente pesquisados com uma divisão e conquiste abordagem uma pesquisa binária de árvore ou árvore de chamada.


## <a name="data-loading"></a>Carregamento de dados
Carregamento de dados é uma grande parte da carga de trabalho de depósito de dados. Empresas geralmente têm um sistema OLTP ocupado que controla as alterações ao longo do dia quando os clientes estão gerando transações comerciais. Periodicamente, geralmente à noite durante uma janela de manutenção, as transações são movidas ou copiadas para o data warehouse. Depois que os dados no data warehouse, analistas podem executar análise e tomar decisões de negócios nos dados.

- Normalmente, o processo de carregar é chamado ETL para extrair, transformação e carga. Geralmente, os dados precisam ser transformados para que seja consistente com outros dados no data warehouse. Empresas usava anteriormente, servidores ETL dedicados para realizar as transformações. Agora, com tal processamento paralelo fast amplamente você pode carregar dados em SQL Data Warehouse primeiro e, em seguida, executar as transformações. Esse processo é chamado extrair, carga e transformar (ELT) e está se tornando um novo padrão para a carga de trabalho de depósito de dados.

> [AZURE.NOTE] Com o SQL Server 2016, agora você pode executar a análise em tempo real em uma tabela OLTP. Isso não substitui a necessidade de um depósito de dados armazenar e analisar dados, mas ele oferece uma maneira para executar análise em tempo real.

### <a name="reporting-and-analysis-queries"></a>Consultas de análise e geração de relatórios
Consultas de relatórios e análise com frequência são categorizadas em pequeno, médio e grande com base em um número de critérios, mas geralmente é com base no tempo. Na maioria dos depósitos de dados, há uma carga de trabalho mista do fast execução versus consultas de execução demorada. Em cada caso, é importante para determinar esta combinação e para determinar sua frequência (por hora, diariamente, final do mês, final do trimestre e assim por diante). É importante entender que a carga de trabalho de consulta misto, juntamente com concorrência, levar ao planejamento de um data warehouse adequado da capacidade.

- Planejamento da capacidade pode ser uma tarefa complexa para uma carga de trabalho de consulta misto, especialmente quando você precisa de um tempo de avanço longo adicionar capacidade ao data warehouse. SQL Data Warehouse remove a urgência de planejamento de capacidade desde que você pode ampliar ou reduzir capacidade de computação a qualquer momento e, como armazenamento e capacidade de computação são dimensionados de maneira independente.

### <a name="data-management"></a>Gerenciamento de dados
Gerenciamento de dados é importante, especialmente quando você sabe que você pode ficar sem espaço em disco no futuro próximo. Depósitos de dados geralmente dividem os dados em intervalos significativos, que são armazenados como partições em uma tabela. Todos os produtos baseados em SQL Server permitem que você mover partições e sair de tabela. Esta partição migrando permite que você move dados antigos para armazenamento mais barato e manter os dados mais recentes disponíveis em armazenamento online.

- Índices de ColumnStore suportam tabelas particionadas. Para columnstore índices, tabelas particionadas são usadas para gerenciamento de dados e arquivamento. Para tabelas armazenadas de linha por linha, partições tem um papel maior no desempenho da consulta.  

- PolyBase desempenha um papel importante no gerenciamento de dados. Usando PolyBase, você tem a opção de arquivar dados antigos para armazenamento de blob do Hadoop ou Azure.  Isso fornece muitas opções desde que os dados ainda estiverem online.  Poderá demorar mais para recuperar dados de Hadoop, mas a desvantagem de tempo de recuperação pode superam o custo de armazenamento.

### <a name="exporting-data"></a>Exportação de dados
Uma maneira para disponibilizar para relatórios e análise de dados é enviar dados de data warehouse para servidores dedicados para execução de relatórios e análise. Esses servidores são chamados armazéns de dados. Por exemplo, você poderia pré-processar dados de relatório e, em seguida, exportá-lo do data warehouse para muitos servidores em todo o mundo, para torná-la amplamente disponível para clientes e analistas.

- Para gerar relatórios, cada noite você poderia preencher servidores de relatórios somente leitura com um instantâneo dos dados diários. Isso proporciona maior largura de banda para clientes enquanto diminui as necessidades de recursos de computação na data warehouse. De um aspecto de segurança, armazéns de dados permite que você reduza o número de usuários que têm acesso ao data warehouse.
- Para análise, você pode criar um cubo de análise na data warehouse e executar análise contra o data warehouse, ou pré-processar dados e exportá-lo para o servidor de análise para análise posterior.

## <a name="next-steps"></a>Próximas etapas
Agora que você conhece um pouco SQL Data Warehouse, saiba como rapidamente [criar um depósito de dados do SQL][] e [carregar dados de amostra][].

<!--Image references-->

<!--Article references-->
[carregar dados de amostra]: ./sql-data-warehouse-load-sample-databases.md
[criar um depósito de dados do SQL]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other web references-->
