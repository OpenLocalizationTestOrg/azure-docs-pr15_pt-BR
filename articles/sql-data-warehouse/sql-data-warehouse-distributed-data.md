<properties
   pageTitle="Os dados são distribuídos e distribuído opções de tabela para os sistemas de amplamente paralelas processamento (MPP) de depósito de dados do SQL e Parallel Data Warehouse | Microsoft Azure"
   description="Saiba como os dados são distribuídos para amplamente paralelas processamento (MPP) e as opções para distribuir tabelas no depósito de dados do SQL Azure e Parallel Data Warehouse."
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
   ms.date="10/10/2016"
   ms.author="barbkess"/>


# <a name="distributed-data-and-distributed-tables-for-massively-parallel-processing-mpp"></a>Dados distribuídos e tabelas distribuídas para amplamente paralelas processamento (MPP)

Saiba como os dados do usuário são distribuídos no depósito de dados do SQL Azure e Parallel Data Warehouse, que são sistemas de amplamente paralelas processamento (MPP) da Microsoft. Projetar sua data warehouse para usar dados distribuídos efetivamente ajuda você a obter as benefícios da arquitetura MPP de processamento de consultas. Algumas opções de design de banco de dados podem ter um impacto significativo sobre como melhorar o desempenho da consulta.  

>[AZURE.NOTE] Azure SQL Data Warehouse e Parallel Data Warehouse usam o mesmo design amplamente paralelas processamento (MPP), mas eles têm algumas diferenças devido a plataforma subjacente. SQL Data Warehouse é uma plataforma como um serviço (PaaS) que é executado no Azure. Parallel Data Warehouse é executado no sistema de plataforma Analytics (APS) que é um dispositivo local que é executada no Windows Server.

## <a name="what-is-distributed-data"></a>O que é dados distribuídos?

SQL Data Warehouse e Parallel Data Warehouse, dados distribuídos refere-se aos dados de usuário que estão armazenados em vários locais através do sistema MPP. Cada um desses locais funciona como uma unidade de processamento que executa consultas em sua parte dos dados e de armazenamento independente. Dados distribuídos são fundamentais para execução de consultas em paralelo para alcançar o desempenho da consulta alta.

Para distribuir dados, a data warehouse atribui a cada linha de uma tabela de usuário para um local distribuído.  Você pode distribuir tabelas com uma distribuição hash ou um método de alternada. O método de distribuição é especificado na instrução CREATE TABLE. 

## <a name="hash-distributed-tables"></a>Tabelas de hash distribuídos
  
O diagrama a seguir ilustra como um completo (tabela não-distribuídos) é armazenado como uma tabela de hash distribuídos. Uma função determinante atribui cada linha pertencem a uma distribuição. A definição de tabela, uma das colunas é designada como a coluna de distribuição. A função de hash usa o valor da coluna de distribuição para atribuir cada linha para uma distribuição.

Há considerações de desempenho para a seleção de uma coluna de distribuição, como a execução de distinctness, skew de dados e os tipos de consultas no sistema.
  
![Tabela de distribuído] (media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Tabela de distribuído")  
  
-   Cada linha pertence a uma distribuição.  
  
-   Um algoritmo de hash determinante atribui a cada linha a uma distribuição.  
  
-   O número de linhas de tabela por distribuição varia conforme exibido pelos tamanhos diferentes das tabelas.

## <a name="round-robin-distributed-tables"></a>Tabelas distribuídas alternada

Uma tabela distribuída alternada distribui as linhas atribuindo cada linha a uma distribuição de forma sequencial. É rápido carregar dados em uma tabela de repetição alternada, mas o desempenho da consulta pode ser mais lento.  Ingressar em uma tabela de repetição alternada geralmente requer reshuffling as linhas para habilitar a consulta produzir um resultado preciso, que leva tempo.

## <a name="distributed-storage-locations-are-called-distributions"></a>Locais de armazenamento distribuído são chamados distribuições

Cada local distribuído é chamado de uma distribuição. Quando uma consulta é executado em paralelo, cada distribuição executa uma consulta SQL em sua parte dos dados. SQL Data Warehouse usa o banco de dados SQL para executar a consulta. Parallel Data Warehouse usa o SQL Server para executar a consulta. Esse design de arquitetura de nada compartilhado é fundamental para alcançar a computação paralela escala-out.

### <a name="can-i-view-the-distributions"></a>Para exibir as distribuições?

Cada distribuição tem uma ID de distribuição e fica visível nos modos de exibição de sistema que pertencem aos SQL Data Warehouse e Parallel Data Warehouse. Você pode usar a ID de distribuição para solucionar problemas de desempenho da consulta e outros problemas. Para obter uma lista dos modos de exibição do sistema, consulte o modo de [exibição do sistema MPP](sql-data-warehouse-reference-tsql-statements.md).

## <a name="difference-between-a-distribution-and-a-compute-node"></a>Diferença entre uma distribuição e um nó de computação

Uma distribuição é a unidade básica para armazenar dados distribuídos e processamento de consultas em paralelo. Distribuições são agrupadas em nós de computação. Cada nó de computação rastreia uma ou mais distribuições.  

-   Sistema de plataforma de análise usa nós de computação como um componente central dos recursos de arquitetura e dimensionamento de hardware. Ela sempre usa oito distribuições por nó de computação, conforme mostrado no diagrama para tabelas hash distribuídos. O número de nós de computação e, portanto, o número de distribuições, são determinadas pelo número de nós de computação que compra para o dispositivo. Por exemplo, se você comprar oito nós de computação, você obtenha 64 distribuições (8 nós x 8 distribuições/nó de computação). 

-   SQL Data Warehouse tem um número fixo de 60 distribuições e um número flexível de nós de computação. Os nós de computação são implementados com os recursos de computação e armazenamento Azure. O número de nós de computação pode alterados de acordo com a carga de trabalho do serviço de back-end e a capacidade de computação (DWUs) especificado para o data warehouse. Quando o número de nós de computação alterada, o número de Distribuições por nó de computação também é alterado. 

### <a name="can-i-view-the-compute-nodes"></a>Para exibir os nós de computação?

Cada nó de computação tem uma identificação de nó e fica visível nos modos de exibição de sistema que pertencem aos SQL Data Warehouse e Parallel Data Warehouse.  Você pode ver o nó de computação olhando para a coluna de node_id nos modos de exibição do sistema cujos nomes começam com sys.pdw_nodes. Para obter uma lista dos modos de exibição do sistema, consulte o modo de [exibição do sistema MPP](sql-data-warehouse-reference-tsql-statements.md).

## <a name="Replicated"></a>Replicado tabelas para Parallel Data Warehouse 
  
Aplica-se a: Parallel Data Warehouse

Além de usar tabelas distribuídas, Parallel Data Warehouse oferece uma opção para duplicar tabelas. Um *replicados tabela* é uma tabela que está armazenada em mudada em cada nó de computação. Duplicar uma tabela remove a necessidade de transferir suas linhas de tabela entre nós de computação antes de usar a tabela em uma junção ou agregação. Tabelas replicadas somente são viáveis com tabelas pequenas devido o armazenamento extra necessário para armazenar a tabela inteira em cada nó de computação.  
  
O diagrama a seguir mostra uma tabela replicada armazenada em cada nó de computação. A tabela replicada é armazenada em todos os disco atribuído a nó de computação. Essa estratégia de disco é implementada usando grupos de arquivos do SQL Server.  
  
![Tabela de replicado] (media/sql-data-warehouse-distributed-data/replicated-table.png "Tabela de replicado") 
  
## <a name="next-steps"></a>Próximas etapas
  
Para usar tabelas distribuídas efetivamente, consulte [tabelas de distribuição no depósito de dados do SQL](sql-data-warehouse-tables-distribute.md)  
  



  
