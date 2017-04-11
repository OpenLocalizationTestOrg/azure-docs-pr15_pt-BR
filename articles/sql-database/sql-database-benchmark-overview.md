<properties
    pageTitle="Visão geral sobre referência de banco de dados do SQL Azure"
    description="Este tópico descreve a referência de banco de dados do SQL Azure usado para medir o desempenho do Azure SQL Database."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="06/21/2016"
    ms.author="carlrab" />

# <a name="azure-sql-database-benchmark-overview"></a>Visão geral sobre referência de banco de dados do SQL Azure

## <a name="overview"></a>Visão geral
Banco de dados SQL do Microsoft Azure oferece três [níveis de serviço](sql-database-service-tiers.md) com vários níveis de desempenho. Cada nível de desempenho fornece um conjunto crescente de recursos ou 'energia', projetado para fornecer a produtividade de cada vez mais alta.

É importante poder mostre como potência crescente de cada nível de desempenho se traduz em desempenho aumento de banco de dados. Para fazer este Microsoft desenvolveu a referência de banco de dados do Azure SQL (ASDB). Referência exercícios uma mistura de operações básicas encontrados em todas as cargas de trabalho OLTP. Podemos medir a produtividade alcançada para bancos de dados em execução em cada nível de desempenho.

Os recursos e energia de cada nível de desempenho e nível de serviço são expressas em termos de [Unidades de transação de banco de dados (DTUs)](sql-database-technical-overview.md#understand-dtus). DTUs oferece uma maneira de descrever a capacidade relativa de um nível de desempenho com base em uma medida combinada de CPU, memória, ler e gravar taxas oferecidas por cada nível de desempenho. Dobrar a classificação de DTU de um banco de dados equivale a dobrar a potência de banco de dados. Referência nos permite avaliar o impacto sobre o desempenho de banco de dados de potência crescente oferecida por cada nível de desempenho ao exercer operações de banco de dados reais, enquanto Dimensionar o tamanho do banco de dados, número de usuários e taxas de transação proporcionalmente recursos fornecidos ao banco de dados.

Expressando a produtividade do nível de serviço básico usando transações por hora, o nível de serviço padrão usando transações por minuto e o nível de serviço Premium usando transações por segundo, ele facilita a relacionar rapidamente o desempenho potencial de cada nível de serviço para os requisitos de um aplicativo.

## <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Correlação os resultados de referência ao desempenho de banco de dados do mundo real
É importante entender que ASDB, como todos os parâmetros de comparação, é representante e indicativas somente. As taxas de transação obtidas com o aplicativo de referência não será a mesma que aqueles que pode ser obtido com outros aplicativos. Referência consiste em uma coleção de transação diferentes tipos de executar em um esquema que contém uma variedade de tipos de dados e tabelas. Enquanto o parâmetro de comparação exercícios as mesmas operações básicas que são comuns a todas as cargas de trabalho OLTP, ele não representar qualquer classe específica do banco de dados ou aplicativo. O objetivo de referência é fornecer um guia razoável para o desempenho relativo de um banco de dados que pode ser esperado ao dimensionar para cima ou para baixo entre os níveis de desempenho. Na realidade, bancos de dados são de complexidade e tamanhos diferentes, encontrar diferentes combinações de cargas de trabalho e responderão de maneiras diferentes. Por exemplo, um aplicativo que requer muita IO pode pressionar limites de IO mais cedo, ou um aplicativo que requer muita CPU pode pressionar limites de CPU mais cedo. Não há nenhuma garantia de que qualquer determinado banco de dados será dimensionada da mesma maneira como o padrão de referência em aumentar carga.

Referência e sua metodologia são descritos mais detalhadamente abaixo.

## <a name="benchmark-summary"></a>Resumo de referência
ASDB avalia o desempenho de uma mistura de operações de banco de dados básicos que ocorrem com mais frequência em cargas de trabalho (OLTP) de processamento de transações online. Embora a referência é projetada com computação em mente, o esquema de banco de dados, de população de dados em nuvem e transações foram projetadas para ser amplamente representa os elementos básicos mais comumente usados em cargas de trabalho OLTP.

## <a name="schema"></a>Esquema
O esquema foi projetado para ter suficiente variedade e a complexidade para dar suporte a uma ampla variedade de operações. As execuções em relação a um banco de dados composto dos seis tabelas. As tabelas se encaixam em três categorias: tamanho fixo, dimensionamento e crescimento. Existem duas tabelas de tamanho fixo; três tabelas dimensionamento; e uma tabela crescente. Tabelas de tamanho fixo têm um número constante de linhas. Tabelas de dimensionamento têm uma cardinalidade que é proporcional ao desempenho de banco de dados, mas não altera durante a avaliação de desempenho. A tabela crescente é dimensionada como uma tabela de dimensionamento em carregamento inicial, mas as alterações de cardinalidade ao longo da execução do parâmetro de comparação, como linhas são inseridas e excluídas.

O esquema inclui uma mistura de tipos de dados, incluindo inteiro, numérico, caractere e data/hora. O esquema inclui chaves primárias e secundárias, mas não quaisquer chaves estrangeiras – ou seja, há sem restrições de integridade referencial entre tabelas.

Um programa de geração de dados gera os dados para o banco de dados inicial. Número inteiro e os dados numéricos são gerados com diversas estratégias. Em alguns casos, os valores são distribuídos aleatoriamente em um intervalo. Em outros casos, um conjunto de valores é aleatoriamente permutado para garantir que uma distribuição específica é mantida. Campos de texto são gerados de uma lista ponderada de palavras para produzir dados aparência realistas.

O banco de dados é dimensionado com base em um "fator de escala". O fator de escala (abreviado como IT) determina a cardinalidade o dimensionamento e crescimento tabelas. Conforme descrito a seguir na seção usuários e ritmo, o tamanho do banco de dados, número de usuários e desempenho máximo todos dimensionar proporção entre si.

## <a name="transactions"></a>Transações
A carga de trabalho consiste em nove tipos de transação, conforme mostrado na tabela a seguir. Cada transação destina-se para realçar um determinado conjunto de características do sistema no banco de dados mecanismo e o sistema de hardware, com alto contraste das outras transações. Essa abordagem torna mais fácil avaliar o impacto de diferentes componentes para o desempenho geral. Por exemplo, a transação "Leitura pesado" produz um número significativo de operações de leitura do disco.

| Tipo de transação | Descrição |
|---|---|
| Leia Lite | SELECIONAR; na memória; somente leitura |
| Média de leitura | SELECIONAR; principalmente na memória; somente leitura |
| Leitura pesado | SELECIONAR; principalmente não na memória; somente leitura |
| Atualização Lite | ATUALIZAÇÃO; na memória; leitura e gravação |
| Atualizar pesado | ATUALIZAÇÃO; principalmente não na memória; leitura e gravação |
| Inserir Lite | INSERIR; na memória; leitura e gravação |
| Inserir pesado | INSERIR; principalmente não na memória; leitura e gravação |
| Excluir | EXCLUIR; combinação de na memória e não na memória; leitura e gravação |
| CPU pesado | SELECIONAR; na memória; carga de CPU relativamente pesada; somente leitura |

## <a name="workload-mix"></a>Combinação de carga de trabalho
Transações são selecionadas aleatoriamente de uma distribuição ponderada com a seguinte combinação geral. Da combinação geral tem uma taxa de leitura/gravação de aproximadamente 2:1.

| Tipo de transação | % de Mix |
|---|---|
| Leia Lite | 35 |
| Média de leitura | 20 |
| Leitura pesado | 5 |
| Atualização Lite | 20 |
| Atualizar pesado | 3 |
| Inserir Lite | 3 |
| Inserir pesado | 2 |
| Excluir | 2 |
| CPU pesado | 10 |

## <a name="users-and-pacing"></a>Usuários e ritmo
A carga de trabalho de referência é orientada de uma ferramenta que envia transações em um conjunto de conexões para simular o comportamento de um número de usuários simultâneos. Embora todas as conexões e transações estiverem máquina gerada, para simplificar chamamos essas conexões de "usuários". Embora cada usuário funciona independentemente dos todos os outros usuários, todos os usuários executam o mesmo ciclo de etapas mostrado abaixo:

1. Estabelece uma conexão de banco de dados.
2. Repita até sinalizado para sair:
    - Selecione uma transação aleatoriamente (de uma distribuição ponderada).
    - Executar a transação selecionada e medir o tempo de resposta.
    - Aguarde um atraso ritmo.
3. Feche a conexão de banco de dados.
4. Sair.

O atraso ritmo (na etapa 2c) é selecionado aleatoriamente, mas com uma distribuição com uma média de segundo 1.0. Portanto, cada usuário pode, em média, gerar no máximo uma transação por segundo.

## <a name="scaling-rules"></a>Regras de escala
O número de usuários é determinado pelo tamanho do banco de dados (em unidades de fator de escala). Não há um usuário para cada cinco unidades de fator de escala. Devido a demora ritmo, um usuário pode gerar no máximo uma transação por segundo, em média.

Por exemplo, um escala fator de 500 (IT = 500) banco de dados terá 100 usuários e pode obter uma velocidade máxima de 100 TPS. Para direcionar um TPS maior taxa requer um banco de dados maior e mais usuários.

A tabela a seguir mostra o número de usuários que realmente mantidos para cada nível de desempenho e nível de serviço.

| Nível de serviço (nível de desempenho) | Usuários | Tamanho do banco de dados |
|---|---|---|
| Básico | 5 | MB 720 |
| Padrão (S0) | 10 | 1 GB |
| Padrão (S1) | 20 | 2.1 GB |
| Padrão (S2) | 50 | 7.1 GB |
| Premium (P1) | 100 | 14 GB |
| Premium (P2) | 200 | 28 GB |
| Premium (P6/P3) | 800 | GB 114 |

## <a name="measurement-duration"></a>Duração de medida
Uma referência válida executar requer uma duração de medição estável de pelo menos uma hora.

## <a name="metrics"></a>Métricas
Principais métricas na referência são produtividade e tempo de resposta.

- Produtividade é a medida de desempenho essenciais em referência. Produtividade é relatada em transações por unidade de tempo, contando todos os tipos de transação.
- Tempo de resposta é uma medida da capacidade de previsão de desempenho. A restrição de tempo de resposta varia de acordo com a classe de serviço, com maiores classes de serviço tendo um requisito de tempo de resposta mais rigorosa, conforme mostrado abaixo.

| Classe de serviço  | Medida de produtividade | Requisito de tempo de resposta |
|---|---|---|
| Premium | Transações por segundo | 95 Percentil 0,5 segundos |
| Padrão | Transações por minuto | 90 percentil aos 1,0 segundos |
| Básico | Transações por hora | 80 percentil em segundos 2.0 |

## <a name="conclusion"></a>Conclusão
A referência de banco de dados do SQL Azure avalia o desempenho relativo do banco de dados do SQL Azure executando o intervalo de níveis de serviço disponíveis e níveis de desempenho. Referência exercícios uma mistura de operações de banco de dados básicos que ocorrem com mais frequência em cargas de trabalho (OLTP) de processamento de transações online. Por medir o desempenho real, o padrão de referência fornece uma avaliação mais significativa do impacto na taxa de transferência de como alterar o nível de desempenho que é possível listando apenas os recursos oferecidos pelo cada nível como IOPS, tamanho de memória e a velocidade da CPU. No futuro, podemos continuará a evoluir referência para ampliar seu escopo e expandir os dados fornecidos.

## <a name="resources"></a>Recursos
[Introdução ao banco de dados SQL](sql-database-technical-overview.md)

[Níveis de serviço e níveis de desempenho](sql-database-service-tiers.md)

[Orientações sobre desempenho para bancos de dados único](sql-database-performance-guidance.md)
