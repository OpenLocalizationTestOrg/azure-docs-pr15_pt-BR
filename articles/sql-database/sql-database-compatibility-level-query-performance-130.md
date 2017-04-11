<properties
    pageTitle="Nível de compatibilidade, como avaliar | Microsoft Azure"
    description="Etapas e ferramentas para determinar qual nível de compatibilidade é melhor para seu banco de dados no banco de dados do Azure SQL ou Microsoft SQL Server"
    services="sql-database"
    documentationCenter=""
    authors="alainlissoir"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.devlang="NA"
    ms.tgt_pltfrm="NA"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="alainl"/>


# <a name="improved-query-performance-with-compatibility-level-130-in-azure-sql-database"></a>Melhor desempenho de consulta com compatibilidade 130 nível no banco de dados do SQL Azure


Banco de dados do SQL Azure está em execução transparente centenas de milhares de bancos de dados em vários níveis de compatibilidade diferente, preservando e garantir a compatibilidade com a versão correspondente do Microsoft SQL Server para todos os seus clientes!

Portanto, nada impede que os clientes que alteram bancos de dados existentes para o nível de compatibilidade mais recente aproveitar as novas Otimizador de consulta e os recursos de processador de consulta. Como um lembrete de histórico, o alinhamento de versões SQL para níveis de compatibilidade do padrão são os seguintes:

- 100: no SQL Server 2008 e SQL Azure V11 de banco de dados.
- 110: no SQL Server 2012 e SQL Azure V11 de banco de dados.
- 120: no SQL Server 2014 e SQL Azure V12 de banco de dados.
- 130: no SQL Server 2016 e SQL Azure V12 de banco de dados.


> [AZURE.IMPORTANT] Iniciando no **meio junho 2016**, no banco de dados do SQL Azure, o nível de compatibilidade padrão será 130 em vez de 120 para bancos de dados **recém-criado** .
> 
> Bancos de dados criados antes de meados junho de 2016 serão *não* será afetado e manterá seu nível de compatibilidade atual (100, 110 ou 120). Bancos de dados que migrar de versão do Azure SQL Database que V11 para V12 não terão seu nível de compatibilidade alterado seja.


Neste artigo, exploraremos os benefícios de nível de compatibilidade 130 e como aproveitar os benefícios. Podemos endereço os efeitos lado possíveis sobre o desempenho de consulta para os aplicativos de SQL existentes.


## <a name="about-compatibility-level-130"></a>Sobre o nível de compatibilidade 130


Primeiro, se você quiser saber o nível de compatibilidade atual de seu banco de dados, execute a seguinte instrução de Transact-SQL.


```
SELECT compatibility_level
    FROM sys.databases
    WHERE name = '<YOUR DATABASE_NAME>’;
```


Antes dessa alteração ao nível 130 acontece para bancos de dados **recentemente** criado, vamos revisar quais essa alteração é tudo sobre a alguns exemplos de consulta bem básica e ver como qualquer pessoa pode se beneficiar com ele.

Processamento de consulta em bancos de dados relacionais pode ser muito complexo e pode levar a muita ciência e matemática para entender as opções de design inerente e comportamentos. Neste documento, o conteúdo foi intencionalmente simplificado para garantir que qualquer pessoa com algumas técnicas básicas mínima possível entender o impacto da alteração de nível de compatibilidade e determinar como ele pode se beneficiar aplicativos.

Vamos dar uma rápida olhada no qual o nível de compatibilidade 130 traz a tabela.  Você pode encontrar mais detalhes em [Alterar o nível de compatibilidade do banco de dados (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx), mas aqui está um breve resumo:

- A operação de inserção de uma instrução Insert-selecionar pode ser multithread ou pode ter um plano paralelo, enquanto antes dessa operação foi thread único.
- Memória otimizado tabela e consultas de variáveis de tabela agora podem ter planos paralelos, enquanto antes dessa operação também foi thread único.
- Estatísticas de memória otimizada tabela agora podem ser reduzidas e são atualizadas automaticamente. Consulte [Novidades do mecanismo de banco de dados: na memória OLTP](https://msdn.microsoft.com/library/bb510411.aspx#InMemory) para obter mais detalhes.
- Modo de lote v/s modo linha muda com índices de repositório de coluna
  - Classifica em uma tabela com um índice de coluna Store agora está no modo em lotes.
  - Agregações de janelas agora operam no modo de lote como declarações de TSQL LATÊNCIA/cliente potencial.
  - Consultas em tabelas de repositório de coluna com várias cláusulas distinct operam no modo em lotes.
  - Consultas em execução em DOP = 1 ou um plano serial também executar no modo em lotes.
- Por último, melhorias de cardinalidade estimativa realmente estejam chegando com o nível de compatibilidade 120, mas para aqueles em execução em um nível de compatibilidade inferior (ou seja, 100 ou 110), a movimentação para compatibilidade nível 130 também colocará esses aprimoramentos e esses também podem se beneficiar o desempenho de consulta de seus aplicativos.


## <a name="practicing-compatibility-level-130"></a>Nível de compatibilidade praticar 130


Primeiro, vamos algumas tabelas, índices e dados aleatórios criados para pratique algumas desses novos recursos. Os exemplos de script TSQL podem ser executados em SQL Server 2016 ou em Azure SQL Database. No entanto, ao criar um banco de dados do SQL Azure, verifique se que você escolher no mínimo um banco de dados de P2 porque você precisa de pelo menos algumas cores para permitir que vários threads e, portanto, se beneficiar desses recursos.


```
-- Create a Premium P2 Database in Azure SQL Database

CREATE DATABASE MyTestDB
    (EDITION=’Premium’, SERVICE_OBJECTIVE=’P2′);
GO

-- Create 2 tables with a column store index on
-- the second one (only available on Premium databases)

CREATE TABLE T_source
    (Color varchar(10), c1 bigint, c2 bigint);

CREATE TABLE T_target
    (c1 bigint, c2 bigint);

CREATE CLUSTERED COLUMNSTORE INDEX CCI ON T_target;
GO

-- Insert few rows.

INSERT T_source VALUES
    (‘Blue’, RAND() * 100000, RAND() * 100000),
    (‘Yellow’, RAND() * 100000, RAND() * 100000),
    (‘Red’, RAND() * 100000, RAND() * 100000),
    (‘Green’, RAND() * 100000, RAND() * 100000),
    (‘Black’, RAND() * 100000, RAND() * 100000);

GO 200

INSERT T_source SELECT * FROM T_source;

GO 10
```


Agora, vamos dar uma olhada em alguns dos recursos de processamento de consulta aí com o nível de compatibilidade 130.


## <a name="parallel-insert"></a>Inserir paralela


Executar as declarações de TSQL abaixo executa a operação de inserção em nível de compatibilidade 120 e 130, qual respectivamente executa a operação de inserção em um único modelo encadeado (120) e em um modelo de multithread (130).


```
-- Parallel INSERT … SELECT … in heap or CCI
-- is available under 130 only

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO 

-- The INSERT part is in serial

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130
GO

-- The INSERT part is in parallel

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

SET STATISTICS XML OFF;
```


Solicitando o valor real o plano de consulta, olhando para sua representação gráfica ou seu conteúdo XML, você pode determinar quais estimativa de cardinalidade função está em execução. Observando a planos lado a lado na Figura 1, podemos claramente ver que a execução de coluna Store inserir vai de serial em 120 paralelo no 130. Além disso, observe que a alteração do ícone iterador no plano de 130 mostrando duas setas paralelas, ilustrando o fato de que agora a execução do iterador realmente paralela. Se você tiver grandes operações de inserção para concluir, a execução paralela, vinculada ao número de núcleo que você tem à sua disposição para o banco de dados, terá melhor desempenho; até um 100 vezes mais rápido dependendo da sua situação!


*Figura 1: Operação de inserção muda de serial para paralelo compatibilidade com nível 130.*


![Figura 1](./media/sql-database-compatibility-level-query-performance-130/figure-1.jpg)


## <a name="serial-batch-mode"></a>Modo de lotes SERIAL


Da mesma forma, mover para o nível de compatibilidade 130 ao processar linhas de dados permite o processamento de modo de lote. Primeiro, operações de modo de lote só estão disponíveis quando você tem um índice de repositório de coluna no lugar. Segundo, um lote geralmente representa aproximadamente 900 linhas e usa uma lógica de código otimizada para multinucleados CPU, maior produtividade de memória e utiliza diretamente os dados compactados do armazenamento coluna sempre que possível. Nessas condições, SQL Server 2016 pode processar aproximadamente 900 linhas de uma vez, em vez de 1 linha no momento, e como consequência, custos gerais indiretos da operação agora é compartilhado por lote inteiro, reduzindo o custo por linha de total. Esse valor compartilhada de operações combinado com a compactação de repositório de coluna basicamente reduz a latência envolvidos em uma operação de modo de lote SELECT. Você pode encontrar mais detalhes sobre o armazenamento de coluna e lote modo [Columnstore índices](https://msdn.microsoft.com/library/gg492088.aspx)guia.


```
-- Serial batch mode execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT (C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO 

– The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Como visíveis abaixo, observando a consulta planos lado a lado na Figura 2, podemos ver que o modo de processamento mudou com o nível de compatibilidade e como consequência, ao executar as consultas em ambos os nível de compatibilidade completamente, podemos ver que a maioria do tempo de processamento é gasto no modo de linha (86%) em comparação com o modo em lotes (14%), onde 2 lotes foram processadas. Aumentar o conjunto de dados, o benefício aumentará.


*Figura 2: Selecione alterações de operação serial para o modo de lote com o nível de compatibilidade 130.*


![Figura 2](./media/sql-database-compatibility-level-query-performance-130/figure-2.jpg)


## <a name="batch-mode-on-sort-execution"></a>Modo de lote na execução de classificação


Semelhante ao acima, mas aplicado a uma operação de classificação, a transição do modo de linha (nível de compatibilidade 120) para o modo de lotes (nível de compatibilidade 130) melhora o desempenho da operação de classificação pelas mesmas razões.


```
-- Batch mode on sort execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Visível lado a lado na Figura 3, podemos ver que a operação de classificação no modo de linha representa 81% do custo, enquanto o modo em lotes somente representa 19% do custo (respectivamente 81% e 56% em Classificar em si).


*Figura 3: A operação de classificação altera da linha para o modo de lote com o nível de compatibilidade 130.*


![Figura 3](./media/sql-database-compatibility-level-query-performance-130/figure-3.png)


Obviamente, esses exemplos contenham apenas milhares de linhas, que é nada quando observando os dados disponíveis na maioria dos servidores de SQL estes dias. Apenas de projeto esses contra milhões de linhas em vez disso, e isso pode traduzir vários minutos de execução reservados diariamente pendentes a natureza de sua carga de trabalho.


## <a name="cardinality-estimation-ce-improvements"></a>Melhorias de estimativa (CE) de cardinalidade


Introduzido com o SQL Server 2014, qualquer banco de dados em execução em um nível de compatibilidade 120 ou acima usarão a estimativa de cardinalidade nova funcionalidade. Essencialmente, a estimativa de cardinalidade é a lógica usada para determinar como o SQL server irá executar uma consulta com base em seu custo estimado. A previsão é calculada com a entrada de estatísticas associadas a objetos envolvidos na consulta. Praticamente, em um alto nível, funções de previsão de cardinalidade são estimativas de contagem de linha juntamente com informações sobre a distribuição de valores, contagens distintas, e contagens duplicadas contidos em tabelas e objetos referenciados na consulta. Obtendo essas estimativas errado, pode levar em disco desnecessário e/s devido a concessões de memória insuficiente (isto é, contato com líquidos TempDB) ou a uma seleção de execução de uma série de plano ao longo de uma execução de planos paralelos, para mencionar alguns. Conclusão, estimativas incorretas podem levar a uma degradação do desempenho geral da execução da consulta. No outro lado, melhores estimativas, estimativas mais precisas, leva a melhores execuções de consulta!

Conforme mencionado anteriormente, estimativas e otimizações de consulta são um assunto complexo, mas se você quiser saber mais sobre planos de consulta e avaliador de cardinalidade, você pode se referir ao documento no [Otimizando seus planos de consulta com o estimador de cardinalidade de 2014 do SQL Server](https://msdn.microsoft.com/library/dn673537.aspx) para aprofundar.


## <a name="which-cardinality-estimation-do-you-currently-use"></a>Quais estimativa de cardinalidade você usa atualmente?


Para determinar em quais estimativa de cardinalidade suas consultas estiver executando, vamos simplesmente usar as amostras de consulta abaixo. Observe que este exemplo primeiro será executado em nível de compatibilidade 110, indicando o uso das funções de previsão de cardinalidade antigos.


```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 110;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Após a execução for concluída, clique no link XML e examinar as propriedades do primeiro iterador conforme mostrado abaixo. Observe o nome da propriedade chamado CardinalityEstimationModelVersion atualmente definida na 70. Isso não significa que o nível de compatibilidade do banco de dados está definido para a versão do SQL Server 7.0 (ele está definido na 110 como visíveis nas instruções TSQL acima), mas o valor 70 simplesmente representa a funcionalidade de cardinalidade estimativa herdada disponível desde o SQL Server 7.0, que tinham sem revisões principais até o SQL Server 2014 (que vem com um nível de compatibilidade de 120).


*Figura 4: O CardinalityEstimationModelVersion está definido como 70 ao usar um nível de compatibilidade de 110 ou abaixo.*


![Figura 4](./media/sql-database-compatibility-level-query-performance-130/figure-4.png)


Como alternativa, você pode alterar o nível de compatibilidade para 130 e desabilitar o uso da função previsão de cardinalidade novo usando o LEGACY_CARDINALITY_ESTIMATION definido como ativado com [Alterar configuração de escopo do banco de dados](https://msdn.microsoft.com/library/mt629158.aspx). Isso será exatamente o mesmo que usar 110 de uma estimativa de cardinalidade função ponto de Vista, enquanto estiver usando o nível de compatibilidade de processamento de consultas mais recente. Isso, que você pode se beneficiar a nova consulta processamento recursos aí com o nível de compatibilidade mais recente (ou seja, o modo de lote), mas ainda dependem da funcionalidade de cardinalidade estimativa antiga se for necessário.


```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Simplesmente movendo para o nível de compatibilidade 120 ou 130 habilita a nova funcionalidade de cardinalidade estimativa. Nesse caso, o padrão CardinalityEstimationModelVersion será definido adequadamente para 120 ou 130 como visíveis abaixo.


```
-- New CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


*Figura 5: O CardinalityEstimationModelVersion está definido como 130 ao usar um nível de compatibilidade de 130.*


![Figura 5](./media/sql-database-compatibility-level-query-performance-130/figure-5.jpg)


## <a name="witnessing-the-cardinality-estimation-differences"></a>Testemunhando as diferenças de cardinalidade estimativa


Agora, vamos executar um pouco mais complexo que envolvem uma INNER JOIN com uma cláusula WHERE com alguns predicados da consulta e vamos examinar a estimativa de contagem de linha da função previsão de cardinalidade antiga primeiro.


```
-- Old CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


A execução eficaz essa consulta retorna 200,704 linhas, enquanto a estimativa de linha com a funcionalidade de cardinalidade estimativa antiga declarações 194,284 linhas. Obviamente, como disse antes, esses resultados de contagem de linha também dependerá com que frequência você executou os exemplos anteriores, que preenche as tabelas de amostra repetidamente em cada execução. Obviamente, os predicados em sua consulta também terá uma influência sobre a estimativa real além da forma de tabela, dados de conteúdo, e como esses dados realmente correlação uns com os outros.


*Figura 6: A estimativa de contagem de linha é 194,284 ou 6.000 linhas fora das 200,704 linhas esperadas.*


![Figura 6](./media/sql-database-compatibility-level-query-performance-130/figure-6.jpg)


Da mesma maneira, vamos agora executar a mesma consulta com a nova funcionalidade de cardinalidade estimativa.


```
-- New CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Examinando o abaixo, podemos ver que a estimativa de linha é 202,877, ou muito detalhada e maior do que a estimativa de cardinalidade antigo.

*Figura 7: A estimativa de contagem de linha agora é 202,877, em vez de 194,284.*


![Figura 7](./media/sql-database-compatibility-level-query-performance-130/figure-7.jpg)


Na verdade, o conjunto de resultados é 200,704 linhas (mas tudo isso depende com que frequência você executar as consultas dos exemplos anteriores, mas mais importante, porque o TSQL usa a declaração aleatório (), os valores reais retornados podem variar de uma execução para o próximo). Portanto, nesse exemplo específico, a nova estimativa de cardinalidade faz um trabalho melhor em Estimando o número de linhas porque 202,877 é muito mais próximo 200,704, que 194,284! Último, se você alterar a cláusula WHERE predicados de igualdade (em vez de ">" por exemplo), isso pode tornar as estimativas entre o antigo e a nova função de cardinalidade ainda mais diferente, dependendo de quantos corresponde ao que você pode obter.

Obviamente, nesse caso, sendo ~ 6000 linhas fora da contagem real não representar muitos dados em algumas situações. Agora, transpor para milhões de linhas em várias tabelas e consultas mais complexas e às vezes a estimativa pode ser desativado milhões de linhas e, portanto, o risco de separação-up o plano de execução errado ou solicitando concessões de memória insuficiente levam a contato com líquidos TempDB e para exibir mais e/s, são muito maiores.

Se você tiver a oportunidade, prática essa comparação com mais comum consultas e conjuntos de dados e ver para si mesmo por quanto de algumas das estimativas antigas e novas são afetadas, enquanto alguns podem apenas se tornar mais desativar da realidade ou alguns outros apenas simplesmente mais próximo à linha real contagens realmente retornado nos conjuntos de resultado. Tudo isso dependerá da forma de suas consultas, as características de banco de dados do SQL Azure, a natureza e o tamanho de seus conjuntos de dados e as estatísticas disponíveis sobre eles. Se você acabou de criar sua instância de banco de dados de SQL Azure, o otimizador de consulta terá que criar seu conhecimento do zero em vez de reutilizando estatísticas compostas a anterior consulta é executada. Portanto, as estimativas são muito contextual e quase específicas para cada situação de servidor e aplicativo. É um aspecto importante ter em mente!


## <a name="some-considerations-to-take-into-account"></a>Algumas considerações para levar em consideração


Embora a maioria das cargas de trabalho poderiam se beneficiar com o nível de compatibilidade 130, antes de adotar o nível de compatibilidade para seu ambiente de produção, você tem, basicamente 3 opções:

1. Você move para o nível de compatibilidade 130 e ver como realizar coisas. Caso você perceber que algumas perdas, você simplesmente definir o nível da compatibilidade voltar ao seu nível original ou manter 130 e inversa somente a estimativa de cardinalidade volta para o modo herdado (conforme explicado acima, isso sozinho pode solucionar o problema).
2. Você testar seus aplicativos existentes sob carga de produção semelhantes, ajuste fino e valida o desempenho antes de ir para produção. No caso de problemas, mesmo que acima, você pode sempre voltar para o nível de compatibilidade original ou simplesmente reverte a estimativa de cardinalidade novamente para o modo herdado.
3. Como uma opção final e a maneira mais recente para atender a essas perguntas, é aproveitar o repositório de consulta. Que é a opção recomendada de hoje! Para ajudar a análise de suas consultas em compatibilidade nível 120 ou abaixo versus 130, não recomendamos que você suficiente para armazenamento de consulta. Repositório de consulta está disponível com a versão mais recente do V12 de banco de dados do SQL Azure, e ele foi criado para ajudá-lo com a solução de problemas de desempenho de consulta. Pense o repositório de consulta como um gravador de dados de voos para seu banco de dados coletar e apresentar informações detalhadas de históricas sobre todas as consultas. Isso simplifica judiciais de desempenho, reduzindo o tempo para diagnosticar e resolver problemas. Você pode encontrar mais informações ao [repositório de consulta: um gravador de dados de voos para seu banco de dados](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/).


AT alto nível, se você já tiver um conjunto de bancos de dados em execução no nível de compatibilidade 120 ou abaixo e pretende mover algumas para 130, ou como sua carga de trabalho automaticamente provisionar novos bancos de dados que serão assim ser definida por padrão para 130, considere o seguinte:

- Antes de alterar para o novo nível de compatibilidade em produção, habilite o armazenamento de consulta. Você pode consultar a [alterar o modo de compatibilidade do banco de dados e usar o armazenamento de consulta](https://msdn.microsoft.com/library/bb895281.aspx) para obter mais informações.
- Em seguida, teste críticas todas as cargas de trabalho usando dados representantes e consultas de um ambiente de produção e comparar o desempenho experiente e conforme relatados por repositório de consulta. Se você enfrentar algum perdas, você pode identificar as consultas existência com o armazenamento de consulta e use o plano forçando opção Store de consulta (também conhecidos como plano de fixação). Nesse caso, você definitivamente ficar com o nível de compatibilidade 130 e use o plano de consulta anterior conforme sugerido pelo armazenamento de consulta.
- Se você deseja aproveitar os novos recursos e funcionalidades do Azure SQL Database (que está executando o SQL Server 2016), mas é sensíveis a mudanças colocadas pelo nível de compatibilidade 130, como último recurso, você pode considerar forçar o nível de compatibilidade de volta para o nível adequado para sua carga de trabalho usando uma instrução ALTER DATABASE. Mas, primeiro, lembre-se de que o plano de armazenamento de consulta fixação opção é a melhor opção porque não usando 130 é basicamente mantendo-se com o nível de funcionalidade de uma versão antiga do SQL Server.
- Se você tiver vários locatários aplicativos que se estendem por vários bancos de dados, talvez seja necessário atualizar a lógica de provisionamento de seus bancos de dados para garantir um nível de compatibilidade consistente em todos os bancos de dados; aquelas antigas e provisionadas recentemente. O desempenho de carga de trabalho de aplicativo poderia ser sensível ao fato de que alguns bancos de dados são executados em níveis de compatibilidade diferente e, portanto, consistência de nível de compatibilidade entre qualquer banco de dados pode ser necessária para fornecer a mesma experiência aos seus clientes todo o quadro. Observe que não é uma exigência, ele realmente depende de como seu aplicativo é afetado pelo nível de compatibilidade.
- Por último, sobre a estimativa de cardinalidade e como alterar o nível de compatibilidade, antes de prosseguir em produção, recomenda-se para testar sua carga de trabalho de produção sob as condições novo para determinar se seu aplicativo benefícios das melhorias de cardinalidade estimativa.


## <a name="conclusion"></a>Conclusão


Usar o banco de dados do SQL Azure para tirar proveito de todos os aprimoramentos do SQL Server 2016 claramente pode melhorar sua execuções de consulta. Assim como-é! Claro, como qualquer novo recurso, uma avaliação adequada deve ser feita para determinar as condições exatas em qual sua carga de trabalho do banco de dados funciona melhor. A experiência mostra que a maioria dos carga de trabalho devem pelo menos executar transparente em nível de compatibilidade 130, aproveitando nova consulta funções e nova estimativa de cardinalidade de processamento. Que disse, Realisticamente, sempre há algumas exceções e fazendo o vencimento PRI auditoria é uma avaliação importante para determinar o quanto você pode se beneficiar esses aprimoramentos. E, novamente, o armazenamento de consulta podem ser de uma grande ajuda fazendo este trabalho!

Como SQL Azure evolui, você pode esperar um nível de compatibilidade 140 no futuro. Quando tempo é apropriado, vamos começar falando sobre o que esse nível de compatibilidade futura 140 colocará, assim como podemos brevemente discutido aqui o nível de compatibilidade 130 é agregando hoje.

Por agora, vamos não esquecer, iniciando de 2016 de junho, Azure SQL Database alterará o nível de compatibilidade do padrão de 120 para 130 para bancos de dados recém-criado. Lembre-se!


## <a name="references"></a>Referências


- [Novidades do mecanismo de banco de dados](https://msdn.microsoft.com/library/bb510411.aspx#InMemory)

- [Blog: Repositório de consulta: um gravador de dados de voos para seu banco de dados, por Borko Novakovic, de 2016 8 de junho](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)

- [Nível de compatibilidade de banco de dados ALTER (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)

- [ALTERAR A CONFIGURAÇÃO DE ESCOPO DO BANCO DE DADOS](https://msdn.microsoft.com/library/mt629158.aspx)

- [Nível de compatibilidade 130 para V12 de banco de dados do SQL Azure](https://azure.microsoft.com/updates/compatibility-level-130-for-azure-sql-database-v12/)

- [Otimizar sua consulta planos com o SQL Server avaliador de cardinalidade de 2014](https://msdn.microsoft.com/library/dn673537.aspx)

- [Guia de índices ColumnStore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Blog: Consulta melhor desempenho com nível de compatibilidade 130 no banco de dados do SQL Azure, por Alain Lissoir, de 2016 6 de maio](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/)



<!--
Improved Query Performance with Compatibility Level 130 in Azure SQL Database

May 6, 2016 by Alain Lissoir (AlainL), on GitHub 'alainlissoir'.

https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/

..... Now, above.
....................
..... Soon, below?

CAPS / MSDN ideally, but instead on ACom:
.. # Assess effects of latest compatibility level on query performance, how to

sql-database-compatibility-level-query-performance-130.md

genemi = MightyPen , 2016-05-20  Friday  17:00pm
-->
