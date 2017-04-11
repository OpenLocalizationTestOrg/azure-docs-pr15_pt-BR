<properties
   pageTitle="Gerenciar power de computação no depósito de dados do SQL Azure (visão geral) | Microsoft Azure"
   description="Escala de desempenho recursos no armazém de dados do SQL Azure. Dimensionar ajustando DWUs ou pausar e continuar recursos de computação para economizar custos."
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
   ms.date="09/03/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>Gerenciar power de computação no depósito de dados do SQL Azure (visão geral)

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTANTE](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)

A arquitetura do SQL Data Warehouse separa armazenamento e computação, permitindo que cada dimensionar de maneira independente. Como resultado, você pode dimensionar desempenho economizando custos pagando somente pela desempenho quando precisar. 

Esta visão geral descreve os seguintes recursos de dimensionamento de desempenho do SQL Data Warehouse e oferece recomendações sobre como e quando usá-los. 

- Escala de poder de computação ajustando [dados depósito unidades (DWUs)][]
- Recursos de computação pausar ou continuar

<a name="scale-performance-bk"></a>

## <a name="scale-performance"></a>Dimensionar o desempenho

Em SQL Data Warehouse, você pode rapidamente dimensionar desempenho check-out ou por crescentes ou decrescentes recursos de computação de CPU, memória e largura de banda de e/s. Para dimensionar o desempenho, tudo o que você precisa fazer é ajustar o número de [unidades (DWUs) do data warehouse][] que SQL Data Warehouse aloca seu banco de dados. SQL Data Warehouse rapidamente faz a alteração e lida com todas as alterações subjacentes de hardware e software.

Já se foram os dias onde você precisa pesquisar qual tipo de processadores, quanta memória ou que tipo de armazenamento que você precisa ter ótimo desempenho no data warehouse. Colocando seu Data Warehouse na nuvem, você já não precisa lidar com problemas de hardware de baixo nível. Em vez disso, o SQL Data Warehouse faz essa pergunta: quão rápido você deseja analisar seus dados? 

### <a name="how-do-i-scale-performance"></a>Como dimensionar o desempenho?

Para consumidor aumentar ou diminuir seu poder de computação, simplesmente altere a configuração [do data warehouse unidades (DWUs)][] para seu banco de dados. Desempenho aumentará linear conforme você adicionar mais DWU.  Em níveis superiores de DWU, você precisa adicionar mais de 100 DWUs para observar uma melhora significativa no desempenho. Para ajudá-lo a selecionar saltos significativos em DWUs, oferecemos os níveis DWU que fornecerão os melhores resultados.
 
Para ajustar DWUs, você pode usar qualquer um desses métodos individuais.

- [Escala de calcular power com o portal do Azure][]
- [Poder com PowerShell de computação de escala][]
- [Alimentação de computação de escala com APIs REST][]
- [Poder com TSQL de computação de escala][]

### <a name="how-many-dwus-should-i-use"></a>Quantos DWUs devo usar?
 
Desempenho em SQL Data Warehouse expandida linear, e a alteração de uma computação escala para outro (digamos de 100 DWUs para DWUs 2000) acontece em segundos. Isso lhe dá a flexibilidade para experimentar configurações diferentes de DWU até determinar melhor ajuste do seu cenário.

Para entender o que é o valor DWU ideal, tente dimensionamento para cima e para baixo e executando algumas consultas após carregar seus dados. Como o dimensionamento é rápido, você pode tentar um número de níveis diferentes de desempenho em uma hora ou menos. Lembre-se que SQL Data Warehouse foi projetado para processar grandes quantidades de dados e ver seus recursos true para dimensionamento, especialmente nas escalas maiores que oferecemos, você desejará utilizar um grande conjunto de dados que se aproxima ou excede 1 TB.

Recomendações para encontrar o DWU melhor para sua carga de trabalho:

1. Para um data warehouse em desenvolvimento, comece selecionando um pequeno número de DWUs.  Um bom ponto de partida é DW400 ou DW200.
2. Monitore o desempenho do aplicativo, observando o número de DWUs selecionada em comparação com o desempenho que você observar.
3. Determine quanto desempenho mais rápido ou mais lento para você atingir o nível de desempenho ideal para suas necessidades supondo escala linear.
4. Aumente ou diminua o número de DWUs proporcionalmente como muito mais rápido ou mais lento se desejar de sua carga de trabalho para executar. O serviço responderá rapidamente e ajustar os recursos de computação para atender aos requisitos de DWU de novo.
5. Continue a fazer ajustes até atingir um nível de desempenho ideal para suas necessidades de negócios.

### <a name="when-should-i-scale-dwus"></a>Quando eu devo dimensionar DWUs?

Quando você precisar resultados mais rápidos, aumentar sua DWUs e pagar para obter melhor desempenho.  Quando precisar menos poder computação, diminuir sua DWUs e pagar somente para o que é necessário. 

Recomendações para quando dimensionar DWUs:

1. Se seu aplicativo tiver uma carga de trabalho flutuante, escala DWU níveis para cima ou para baixo para acomodar picos e pontos baixos. Por exemplo, se sua carga de trabalho picos normalmente no final do mês, planeja adicionar mais DWUs durante os dias de pico, em seguida, dimensionar para baixo, depois que o período de pico sobre.
2. Antes de executar uma operação de carregamento ou transformação de dados intenso, expandir DWUs para que seus dados estão disponíveis mais rapidamente.

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Computação de pausa

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar um banco de dados, use qualquer um desses métodos individuais.

- [Pausar computação com o portal do Azure][]
- [Computação de pause com o PowerShell][]
- [Pausar computação com APIs REST][]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Computação de currículo

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para retomar a um banco de dados, use qualquer um desses métodos individuais.

- [Computação de currículo com o portal do Azure][]
- [Computação de currículo com PowerShell][]
- [Computação de currículo com APIs REST][]

## <a name="permissions"></a>Permissões

O banco de dados de dimensionamento exigem as permissões descritas [ALTER DATABASE][].  Pausar e continuar exigem a permissão de [SQL DB Colaborador][] , especificamente Microsoft.Sql/servers/databases/action.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Próximas etapas
Consulte os artigos a seguir para ajudá-lo a entender alguns conceitos chave de desempenho adicionais:

- [Gerenciamento de carga de trabalho e concorrência][]
- [Visão geral de design de tabela][]
- [Distribuição da tabela][]
- [Indexação de tabela][]
- [Partição de tabela][]
- [Estatísticas de tabela][]
- [Práticas recomendadas][]

<!--Image reference-->

<!--Article references-->
[unidades de dados de depósito (DWUs)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

[Escala de calcular power com o portal do Azure]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-bk
[Poder com PowerShell de computação de escala]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Alimentação de computação de escala com APIs REST]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Poder com TSQL de computação de escala]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Pausar computação com o portal do Azure]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Computação de pause com o PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Pausar computação com APIs REST]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Computação de currículo com o portal do Azure]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Computação de currículo com PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Computação de currículo com APIs REST]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Gerenciamento de carga de trabalho e concorrência]: ./sql-data-warehouse-develop-concurrency.md
[Visão geral de design de tabela]: ./sql-data-warehouse-tables-overview.md
[Distribuição da tabela]: ./sql-data-warehouse-tables-distribute.md
[Indexação de tabela]: ./sql-data-warehouse-tables-index.md
[Partição de tabela]: ./sql-data-warehouse-tables-partition.md
[Estatísticas de tabela]: ./sql-data-warehouse-tables-statistics.md
[Práticas recomendadas]: ./sql-data-warehouse-best-practices.md 
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB Colaborador]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTERAR BANCO DE DADOS]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
