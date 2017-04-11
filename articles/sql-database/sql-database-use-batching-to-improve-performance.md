 <properties
    pageTitle="Como usar o processamento em lotes para melhorar o desempenho do aplicativo de banco de dados do Azure SQL"
    description="O tópico fornece evidências que lotes operações de banco de dados bastante imroves a velocidade e escalabilidade de seus aplicativos do Azure SQL Database. Embora essas técnicas lotes funcionam para qualquer banco de dados do SQL Server, o foco do artigo é no Azure."
    services="sql-database"
    documentationCenter="na"
    authors="annemill"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="07/12/2016"
    ms.author="annemill" />

# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Como usar o processamento em lotes para melhorar o desempenho do aplicativo de banco de dados SQL

Processamento em lotes operações de banco de dados do Azure SQL significativamente melhora o desempenho e escalabilidade de seus aplicativos. Para entender os benefícios, a primeira parte deste artigo aborda alguns resultados de teste de amostra que comparam solicitações sequenciais e em lote para um banco de dados do SQL. O resto do artigo mostra as técnicas, cenários e considerações para ajudar você a usar o processamento em lotes com êxito em seus aplicativos do Azure.

**Autores**: Vitor Roth, Silvano Coriani, Trent Swanson (escala completa 180 Inc)

**Revisores**: Conor Cunningham, Michael Thomassy

## <a name="why-is-batching-important-for-sql-database"></a>Por que é lotes importantes para o banco de dados SQL?
Processamento em lotes chamadas para um serviço remoto é uma estratégia conhecida para aumentar o desempenho e escalabilidade. Corrigidos os custos de processamento para qualquer interações com um serviço remoto, como serialização, transferência de rede e desserialização. Quantas transações separadas em um único lote de embalagem minimiza esses custos.

Neste artigo, queremos examinar vários banco de dados do SQL lotes estratégias e cenários. Embora essas estratégias também são importantes para aplicativos de locais que usam o SQL Server, há vários motivos para destacar o uso de processamento em lotes de banco de dados do SQL:

- Há potencialmente maior latência de rede em acessando o banco de dados SQL, especialmente se você estiver acessando o banco de dados SQL do fora do Centro de dados do Microsoft Azure mesmo.
- As características de vários locatários do banco de dados do SQL significa que a eficiência da camada de um acesso de dados está relacionada à escalabilidade geral do banco de dados. Banco de dados SQL deve impedir que qualquer locatário/usuário único monopolizando recursos de banco de dados para o ponto de prejudicar outros locatários. Em resposta ao uso em excesso cotas predefinidos, banco de dados SQL pode reduzir a produtividade ou responder com a otimização de exceções. Eficiência, como o processamento em lotes, permite fazer mais trabalho em banco de dados SQL antes de contatar esses limites. 
- Processamento em lotes também é eficaz para arquiteturas que usam vários bancos de dados (fragmentação). A eficiência da sua interação com cada unidade de banco de dados ainda é um fator-chave na sua escalabilidade geral. 

Um dos benefícios do uso de banco de dados SQL é que você não precisa gerenciar os servidores que hospedam o banco de dados. No entanto, essa infraestrutura gerenciada também significa que você precisa pensar de forma diferente sobre otimizações de banco de dados. Você não pode consultar para melhorar a infraestrutura de rede ou de hardware do banco de dados. Microsoft Azure controla esses ambientes. A principal área que você pode controlar é como o seu aplicativo interage com o banco de dados SQL. Processamento em lotes é uma dessas otimizações. 

A primeira parte do papel examina várias técnicas lotes para aplicativos .NET que usam o banco de dados SQL. As duas últimas seções cobrem lotes diretrizes e cenários.

## <a name="batching-strategies"></a>Estratégias de lotes

### <a name="note-about-timing-results-in-this-topic"></a>Observação sobre os resultados de intervalo neste tópico
>[AZURE.NOTE] Os resultados não são avaliações mas destinam-se para mostrar **desempenho relativo**. Os intervalos são baseados em uma média de pelo menos 10 execuções de teste. Operações são inserções em uma tabela vazia. Esses testes foram medido pré-V12 e eles não correspondem necessariamente produtividade que podem ocorrer em um banco de dados de V12 usando os novos [níveis de serviço](sql-database-service-tiers.md). O benefício relativo da técnica lote será semelhante.

### <a name="transactions"></a>Transações
Parece estranho para começar uma revisão de processamento em lotes discutindo transações. Mas o uso do lado do cliente transações tem um efeito sutil lote de servidor que melhora o desempenho. E transações podem ser adicionadas com apenas algumas linhas de código, para que eles fornecem uma maneira rápida de melhorar o desempenho de operações sequenciais.

Considere o seguinte código c# que contém uma sequência de inserir e atualizar operações em uma tabela simples.

    List<string> dbOperations = new List<string>();
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
    dbOperations.Add("insert MyTable values ('new value',1)");
    dbOperations.Add("insert MyTable values ('new value',2)");
    dbOperations.Add("insert MyTable values ('new value',3)");

O seguinte código ADO.NET sequencialmente executa essas operações.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();
    
        foreach(string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn);
            cmd.ExecuteNonQuery();                   
        }
    }

A melhor maneira de otimizar este código é implementar algum tipo de cliente em lotes dessas chamadas. Mas não há uma maneira simples de aumentar o desempenho desse código, simplesmente disposição a sequência de chamadas em uma transação. Aqui está o mesmo código que usa uma transação.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();
        SqlTransaction transaction = conn.BeginTransaction();
    
        foreach (string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
            cmd.ExecuteNonQuery();
        }
    
        transaction.Commit();
    }

Transações realmente estão sendo usadas em ambas estes exemplos. No primeiro exemplo, cada chamada individual é uma transação implícita. No segundo exemplo, uma transação explícita quebra todas as chamadas. Por documentação a [gravação com previsão de log de transação](https://msdn.microsoft.com/library/ms186259.aspx), os registros de log são transferidos para o disco quando se compromete a transação. Portanto, incluindo mais chamadas em uma transação, a gravação no log de transação pode atrasar até que a transação é confirmada. Na verdade, você está ativando o processamento em lotes as gravações para o log de transação do servidor.

A tabela a seguir mostra alguns resultados de teste do ad-hoc. Os testes executados as inserções sequenciais mesmas com e sem transações. Para obter mais perspectiva, o primeiro conjunto de testes executados remotamente um laptop no banco de dados do Microsoft Azure. O segundo conjunto de testes tiver executado a partir de um serviço de nuvem e o banco de dados que ambos estivessem dentro mesmo Microsoft Azure Data Center (Oeste EUA). A tabela a seguir mostra a duração em milissegundos de inserções sequenciais com e sem transações.

**Local para o Azure**:

| Operações | Nenhuma transação (ms) | Transação (ms) |
|---|---|---|
| 1 | 130 | 402 |
| 10 | 1208 | 1226 |
| 100 | 12662 | 10395 |
| 1000 | 128852 | 102917 |


**Azure ao Azure (mesmo Data Center)**:

| Operações | Nenhuma transação (ms) | Transação (ms) |
|---|---|---|
| 1 | 21 | 26 |
| 10 | 220 | 56 |
| 100 | 2145 | 341 |
| 1000 | 21479 | 2756 |

>[AZURE.NOTE] Resultados não são avaliações. Consulte a [Observação sobre resultados de intervalo neste tópico](#note-about-timing-results-in-this-topic).

Com base nos resultados do teste anterior, realmente quebrar uma única operação em uma transação diminui o desempenho. Mas à medida que você aumenta o número de operações em uma única transação, a melhoria de desempenho se torna mais marcada. A diferença de desempenho também é mais perceptível quando todas as operações ocorrem dentro do data center Microsoft Azure. A latência aumento do uso de banco de dados SQL do fora do data center Microsoft Azure ofusca o ganho de desempenho do uso de transações.

Embora o uso de transações pode aumentar o desempenho, continue para [observar as práticas recomendadas para conexões e transações](https://msdn.microsoft.com/library/ms187484.aspx). Manter a transação mais breve possível e, em seguida, feche a conexão de banco de dados após concluir o trabalho. O usando a instrução no exemplo anterior garante que a conexão é fechada quando conclui o bloco de código subsequentes.

O exemplo anterior demonstra que você pode adicionar uma transação local a qualquer código ADO.NET com duas linhas. Transações oferecem uma maneira rápida de melhorar o desempenho do código que faz sequencial inserir, atualizar e excluir operações. No entanto, para o desempenho mais rápido, considere alterar o código mais para tirar proveito do lado do cliente em lotes, como os parâmetros com valor de tabela.

Para obter mais informações sobre transações no ADO.NET, consulte [Transações locais no ADO.NET](https://msdn.microsoft.com/library/vstudio/2k2hy99x.aspx).

### <a name="table-valued-parameters"></a>Parâmetros com valor de tabela
Parâmetros com valor de tabela dá suporte a tipos de tabela definidos pelo usuário como parâmetros de instruções Transact-SQL, procedimentos armazenados e funções. Essa técnica de lote do lado do cliente permite que você envie várias linhas de dados dentro de parâmetro com valor de tabela. Para usar parâmetros com valor de tabela, primeiro defina um tipo de tabela. A seguinte instrução Transact-SQL cria um tipo de tabela denominado **MyTableType**.

    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
 

No código, você cria uma **tabela de dados** com os mesmos nomes exatos e tipos do tipo de tabela. Passar esta **tabela de dados** em um parâmetro em uma consulta de texto ou chamada do procedimento armazenado. O exemplo a seguir mostra essa técnica:

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        DataTable table = new DataTable();
        // Add columns and rows. The following is a simple example.
        table.Columns.Add("mytext", typeof(string));
        table.Columns.Add("num", typeof(int));    
        for (var i = 0; i < 10; i++)
        {
            table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
        }
    
        SqlCommand cmd = new SqlCommand(
            "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
            connection);
                    
        cmd.Parameters.Add(
            new SqlParameter()
            {
                ParameterName = "@TestTvp",
                SqlDbType = SqlDbType.Structured,
                TypeName = "MyTableType",
                Value = table,
            });
    
        cmd.ExecuteNonQuery();
    }

No exemplo anterior, o objeto **SqlCommand** insere linhas de um parâmetro com valor de tabela, **@TestTvp**. O objeto de **tabela de dados** criado anteriormente é atribuído a esse parâmetro com o método **SqlCommand.Parameters.Add** . Processamento em lotes as inserções em uma chamada significativamente melhora o desempenho sobre inserções sequenciais.

Para melhorar ainda mais o exemplo anterior, use um procedimento armazenado em vez de um comando baseado em texto. O comando de Transact-SQL a seguir cria um procedimento armazenado que utiliza o parâmetro de **SimpleTestTableType** com valor de tabela.

    CREATE PROCEDURE [dbo].[sp_InsertRows] 
    @TestTvp as MyTableType READONLY
    AS
    BEGIN
    INSERT INTO MyTable(mytext, num) 
    SELECT mytext, num FROM @TestTvp
    END
    GO

Em seguida, altere a declaração de objeto **SqlCommand** no exemplo de código anterior ao seguinte.

    SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
    cmd.CommandType = CommandType.StoredProcedure;

Na maioria dos casos, parâmetros com valor de tabela têm desempenho equivalente ou melhor que outras técnicas lotes. Parâmetros com valor de tabela costumam ser preferível, porque eles são mais flexíveis que outras opções. Por exemplo, outras técnicas, como cópia em massa SQL, só permitem a inserção de novas linhas. Mas com parâmetros com valor de tabela, você pode usar lógica no procedimento armazenado para determinar quais linhas são as atualizações e quais são insere. O tipo de tabela também pode ser modificado para contém uma coluna de "Operação" que indica se a linha especificada deve ser inserida, atualizada ou excluída.

A tabela a seguir mostra os resultados de teste de ad hoc para o uso de parâmetros com valor de tabela em milissegundos.

| Operações | Local para o Azure (ms)  | Data Center mesmo Azure (ms) |
|---|---|---|
| 1 | 124 | 32 |
| 10 | 131 | 25 |
| 100 | 338 | 51 |
| 1000 | 2615 | 382 |
| 10000 | 23830 | 3586 |

>[AZURE.NOTE] Resultados não são avaliações. Consulte a [Observação sobre resultados de intervalo neste tópico](#note-about-timing-results-in-this-topic).

O ganho de desempenho de processamento em lotes é evidente. No teste sequencial anterior, operações de 1000 levaram 129 segundos fora do data center e 21 segundos de dentro do data center. Mas com parâmetros com valor de tabela, operações de 1000 levam apenas 2.6 segundos fora do data center e 0,4 segundos dentro do data center.

Para obter mais informações sobre parâmetros com valor de tabela, consulte [Table-Valued parâmetros](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>Cópia em massa SQL
Cópia em massa SQL é outra maneira de inserir grandes quantidades de dados em um banco de dados de destino. Aplicativos .NET podem usar a classe **SqlBulkCopy** para executar operações de inserção em massa. **SqlBulkCopy** é semelhante a função para a ferramenta de linha de comando, **Bcp.exe**ou a instrução Transact-SQL, **Inserir em MASSA**. O exemplo de código a seguir mostra como copiar em massa das linhas na **tabela de dados**, tabela, a tabela de destino no SQL Server, MyTable fonte.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
        {
            bulkCopy.DestinationTableName = "MyTable";
            bulkCopy.ColumnMappings.Add("mytext", "mytext");
            bulkCopy.ColumnMappings.Add("num", "num");
            bulkCopy.WriteToServer(table);
        }
    }

Há alguns casos onde cópia em massa é preferível parâmetros com valor de tabela. Consulte a tabela de comparação de parâmetros com valor de tabela versus operações de inserção em MASSA no tópico [Table-Valued parâmetros](https://msdn.microsoft.com/library/bb510489.aspx).

Os resultados de teste de ad-hoc a seguir mostram o desempenho do processamento em lotes com **SqlBulkCopy** em milissegundos.

| Operações | Local para o Azure (ms)  | Data Center mesmo Azure (ms) |
|---|---|---|
| 1 | 433 | 57 |
| 10 | 441 | 32 |
| 100 | 636 | 53 |
| 1000 | 2535 | 341 |
| 10000 | 21605 | 2737 |

>[AZURE.NOTE] Resultados não são avaliações. Consulte a [Observação sobre resultados de intervalo neste tópico](#note-about-timing-results-in-this-topic).

Em tamanhos de lote menores, os parâmetros de com valor de tabela de uso superou o desempenho da classe **SqlBulkCopy** . No entanto, **SqlBulkCopy** executada 12 – 31% mais rápido que parâmetros com valor de tabela para os testes de 1.000 e 10.000 linhas. Como parâmetros com valor de tabela, o **SqlBulkCopy** é uma boa opção para inserções em lote, especialmente quando comparado com o desempenho de operações em lote não.

Para obter mais informações sobre a cópia em massa no ADO.NET, consulte [Operações de cópia em massa no SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Instruções de parametrizada INSERT de várias linhas
Uma alternativa para pequenos lotes é construir uma instrução INSERT parametrizada grande que insere várias linhas. O exemplo de código a seguir demonstra essa técnica.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
            "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";
    
        SqlCommand cmd = new SqlCommand(insertCommand, connection);
    
        for (int i = 1; i <= 10; i += 2)
        {
            cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
            cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
        }
    
        cmd.ExecuteNonQuery();
    }
 

Este exemplo destina-se para mostrar o conceito básico. Um cenário mais realista seria percorra as entidades necessárias para construir a cadeia de caracteres de consulta e os parâmetros de comando simultaneamente. Você está limitado a um total de parâmetros de consulta de 2100, e isso limita o número total de linhas que podem ser processados dessa maneira.

Os resultados de teste de ad-hoc a seguir mostram o desempenho desse tipo de instrução insert em milissegundos.

| Operações | Parâmetros com valor de tabela (ms) | Única instrução INSERT (ms) |
|---|---|---|
| 1 | 32 | 20 |
| 10 | 30 | 25 |
| 100 | 33 | 51 |

>[AZURE.NOTE] Resultados não são avaliações. Consulte a [Observação sobre resultados de intervalo neste tópico](#note-about-timing-results-in-this-topic).

Essa abordagem pode ser um pouco mais rápida para lotes que são menos de 100 linhas. Embora o aperfeiçoamento seja pequeno, essa técnica é outra opção que pode funcionar bem no seu cenário de aplicativo específico.

### <a name="dataadapter"></a>DataAdapter
A classe de **DataAdapter** permite que você modifique um objeto de **conjunto de dados** e enviar as alterações como inserir, atualizar e excluir operações. Se você estiver usando o **DataAdapter** dessa maneira, é importante observar que são feitas chamadas separadas para cada operação distinta. Para melhorar o desempenho, use a propriedade **UpdateBatchSize** para o número de operações que devem ser em lote por vez. Para obter mais informações, consulte [Executar operações do lote usando DataAdapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Estrutura de entidades
Estrutura de entidade não suportamos processamento em lotes. Diferentes desenvolvedores na comunidade tentou demonstram soluções alternativas, como substituir o método **SaveChanges** . Mas as soluções geralmente são complexos e personalizado para o aplicativo e o modelo de dados. O projeto de codeplex entidade Framework atualmente tem uma página de discussão nessa solicitação de recurso. Para exibir essa discussão, consulte as [Anotações da reunião de Design – 2 de agosto de 2012](http://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML
Para ser completo, nós sabemos que é importante falar sobre XML como uma estratégia de lote. No entanto, o uso de XML tem nenhum vantagens sobre outros métodos e várias desvantagens. A abordagem é semelhante aos parâmetros com valor de tabela, mas um arquivo XML ou cadeia de caracteres é passada para um procedimento armazenado em vez de uma tabela definida pelo usuário. O procedimento armazenado analisa os comandos no procedimento armazenado.

Há várias desvantagens dessa abordagem:

- Trabalhando com XML pode ser complicado e propensa.
- Analisar o XML no banco de dados pode ser de CPU intenso.
- Na maioria dos casos, esse método é mais lento que parâmetros com valor de tabela.

Por esses motivos, o uso de XML para consultas de lote não é recomendável.

## <a name="batching-considerations"></a>Considerações de lotes
As seções a seguir fornecem mais orientação para o uso de processamento em lotes em aplicativos de banco de dados SQL.

### <a name="tradeoffs"></a>Compensações
Dependendo da sua arquitetura, o processamento em lotes pode envolver uma relação entre desempenho e resiliência. Por exemplo, considere o cenário onde sua função inesperadamente diminui. Se você perder uma linha de dados, o impacto é menor do que o impacto de perder um lote grande de linhas de quadros. Há um risco maior quando você buffer linhas antes de enviá-los para o banco de dados em uma janela de tempo especificado.

Devido a essa relação, avalie os tipos de operações que em lote. Lote mais ativamente (lotes maiores e janelas de tempo maiores) com dados menos críticos.

### <a name="batch-size"></a>Tamanho do lote
Em nossos testes, normalmente não houve nenhuma vantagem para dividir lotes grandes em partes menores. Na verdade, essa subdivisão muitas vezes resultou em desempenho mais lento que enviar um único lote grande. Por exemplo, considere um cenário onde você deseja inserir linhas de 1000. A tabela a seguir mostra quanto tempo leva usar parâmetros com valor de tabela para inserir linhas de 1000 quando dividido em lotes menores.

| Tamanho do lote | Iterações | Parâmetros com valor de tabela (ms) |
| -------- | --- | --- |
| 1000 | 1 | 347 |
| 500 | 2 | 355 |
| 100 | 10 | 465 |
| 50 | 20 | 630 |

>[AZURE.NOTE] Resultados não são avaliações. Consulte a [Observação sobre resultados de intervalo neste tópico](#note-about-timing-results-in-this-topic).

Você pode ver que o melhor desempenho para linhas de 1000 é enviá-los ao mesmo tempo. Em outros testes (não mostrados aqui) Houve um ganho de desempenho pequeno para dividir um lote de 10000 linha em dois lotes de 5000. Mas o esquema de tabela para esses testes é relativamente simple, portanto, você deve executar testes em seus dados específicos e tamanhos de lote para verificar essas descobertas.

Outro fator a considerar é que se o lote total ficar muito grande, banco de dados SQL podem controlar e recusar confirmar o lote. Para obter melhores resultados, teste seu cenário específico para determinar se há um tamanho de lote ideal. Tornar o tamanho do lote configurável em tempo de execução para habilitar ajustes rápidos com base no desempenho ou erros.

Por fim, saldo o tamanho do lote com os riscos associados ao processamento em lotes. Se houver erros temporárias ou a função falha, considere as consequências de repetindo a operação ou de perder os dados no lote.

### <a name="parallel-processing"></a>Processamento paralelo
E se você fez a abordagem da redução do tamanho do lote mas usado vários threads para executar o trabalho? Novamente, nossos testes mostraram que vários lotes menores multithread normalmente realizada pior do que um único lote maior. O seguinte teste tenta inserir linhas de 1000 em um ou mais lotes paralelos. Este teste mostra como mais lotes simultâneos realmente redução de desempenho.

| Tamanho do lote [iterações] | Dois threads (ms) | Quatro segmentos (ms) | Seis threads (ms) |
| -------- | --- | --- | --- |
| 1000 [1] | 277 | 315 | 266 |
| 500 [2] | 548 | 278 | 256 |
| 250 [4] | 405 | 329 | 265 |
| 100 [10] | 488 | 439 | 391 |

>[AZURE.NOTE] Resultados não são avaliações. Consulte a [Observação sobre resultados de intervalo neste tópico](#note-about-timing-results-in-this-topic).

Há vários motivos possíveis para a degradação no desempenho devido a paralelismo:

- Há várias chamadas de rede simultâneas em vez de um.
- Várias operações em relação a uma única tabela podem resultar em disputa e bloqueio.
- Existem custos gerais indiretos associados multithread.
- As despesas de abrindo várias conexões supera o benefício de processamento paralelo.

Se você selecionar diferentes tabelas ou bancos de dados, é possível ver alguns desempenho ganho com essa estratégia. Fragmentação de banco de dados ou agrupamentos seria um cenário para essa abordagem. Fragmentação usa vários bancos de dados e roteia dados diferentes para cada banco de dados. Se cada em pequenos lotes for um banco de dados diferentes, em seguida, executar as operações em paralelo pode ser mais eficiente. No entanto, o ganho de desempenho não é significativo o suficiente para usar como base para uma decisão para usar fragmentação de banco de dados em sua solução.

Em alguns designs, execução paralela de lotes menores pode resultar em produtividade aprimorada de solicitações em um sistema sob carga. Nesse caso, embora seja mais rápido processar um único lote maior, processamento vários lotes em paralelo pode ser mais eficiente.

Se você usar execução paralela, considere a possibilidade de controlar o número máximo de segmentos de trabalho. Um número menor pode resultar em menos disputa e um tempo de execução mais rápido. Além disso, considere a carga adicional que isso coloca o banco de dados de destino nas conexões e transações.

### <a name="related-performance-factors"></a>Fatores de desempenho relacionados
Típica orientações sobre desempenho de banco de dados também afeta o processamento em lotes. Por exemplo, insira o desempenho é reduzido para tabelas que possuem uma chave primária grande ou muitos índices não agrupados.

Se parâmetros com valor de tabela usam um procedimento armazenado, você pode usar o comando **SET NOCOUNT ON** no início do procedimento. Esta política elimina o retorno da contagem das linhas afetadas no procedimento. No entanto, em nossos testes, o uso de **SET NOCOUNT ON** ou não tinha nenhum efeito ou redução de desempenho. O procedimento de teste armazenado era simple com um único **Inserir** comando do parâmetro com valor de tabela. É possível que procedimentos armazenados mais complexos seriam beneficiar esta política. Mas não suponha que adicionando **SET NOCOUNT ON** a seu procedimento armazenado automaticamente melhora o desempenho. Para compreender o efeito, teste seu procedimento armazenado com e sem a instrução **SET NOCOUNT ON** .

## <a name="batching-scenarios"></a>Cenários de lotes
As seções a seguir descrevem como usar parâmetros com valor de tabela em três cenários de aplicativo. O primeiro cenário mostra como buffer e lotes podem trabalhar juntos. O segundo cenário melhora o desempenho ao executar operações de mestre / detalhes em uma chamada de procedimento armazenado único. O cenário final mostra como usar parâmetros com valor de tabela em uma operação de "UPSERT".

### <a name="buffering"></a>Buffer
Embora existam alguns cenários que são candidatos óbvio para processamento em lotes, há muitos cenários que podem tirar proveito de processamento em lotes pelo processamento atrasado. No entanto, o processamento atrasado também apresenta um risco maior que os dados são perdidos em caso de falha inesperada. É importante entender o risco e considerar as consequências.

Por exemplo, considere um aplicativo web que controla o histórico de navegação de cada usuário. Em cada solicitação de página, o aplicativo pode fazer um banco de dados chamada gravar o modo de exibição de página do usuário. Mas o melhor desempenho e escalabilidade podem ser obtidos por buffer atividades de navegação dos usuários e, em seguida, enviar esses dados para o banco de dados em lotes. Você pode acionar a atualização de banco de dados por tempo decorrido e/ou o tamanho de buffer. Por exemplo, uma regra pode especificar que o lote deve ser processado após 20 segundos ou quando o buffer alcança 1000 itens.

O exemplo de código a seguir usa [Reactive Extensions - Rx](https://msdn.microsoft.com/data/gg577609) para processar buffer eventos gerados por uma classe monitoramento. Quando o buffer preenchido ou um tempo limite é alcançado, o lote de dados do usuário é enviado para o banco de dados com um parâmetro com valor de tabela.

A seguinte classe NavHistoryData modela os detalhes de navegação do usuário. Ele contém informações básicas, como o identificador de usuário, a URL acessado e o tempo de acesso.

    public class NavHistoryData
    {
        public NavHistoryData(int userId, string url, DateTime accessTime)
        { UserId = userId; URL = url; AccessTime = accessTime; }
        public int UserId { get; set; }
        public string URL { get; set; }
        public DateTime AccessTime { get; set; }
    }

A classe NavHistoryDataMonitor é responsável por buffer os dados de navegação do usuário ao banco de dados. Ele contém um método, RecordUserNavigationEntry, que responde ao disparar um evento **OnAdded** . O código a seguir mostra a lógica de construtor que usa Rx para criar uma coleção observável baseada no evento. Em seguida, ele assina a esse conjunto observável com o método de Buffer. A sobrecarga Especifica que o buffer deve ser enviado cada 20 segundos ou entradas de 1000.

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");
    
        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
    }

O manipulador converte todos os itens de buffer em um tipo de valor de tabela e passa esse tipo para um procedimento armazenado que processa o lote. O código a seguir mostra a definição completa para a NavHistoryDataEventArgs e as classes NavHistoryDataMonitor.

    public class NavHistoryDataEventArgs : System.EventArgs
    {
        public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
        public NavHistoryData Data { get; set; }
    }
    
    public class NavHistoryDataMonitor
    {
        public event EventHandler<NavHistoryDataEventArgs> OnAdded;
    
        public NavHistoryDataMonitor()
        {
            var observableData =
                Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");
    
            observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
        }
    
        public void RecordUserNavigationEntry(NavHistoryData data)
        {    
            if (OnAdded != null)
                OnAdded(this, new NavHistoryDataEventArgs(data));
        }
    
        protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
        {
            DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
            navHistoryBatch.Columns.Add("UserId", typeof(int));
            navHistoryBatch.Columns.Add("URL", typeof(string));
            navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
            foreach (EventPattern<NavHistoryDataEventArgs> item in items)
            {
                NavHistoryData data = item.EventArgs.Data;
                navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
            }
    
            using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
            {
                connection.Open();
    
                SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
                cmd.CommandType = CommandType.StoredProcedure;
    
                cmd.Parameters.Add(
                    new SqlParameter()
                    {
                        ParameterName = "@NavHistoryBatch",
                        SqlDbType = SqlDbType.Structured,
                        TypeName = "NavigationHistoryTableType",
                        Value = navHistoryBatch,
                    });
    
                cmd.ExecuteNonQuery();
            }
        }
    }

Para usar essa classe buffer, o aplicativo cria um objeto de NavHistoryDataMonitor estático. Sempre que um usuário acessa uma página, o aplicativo chama o método NavHistoryDataMonitor.RecordUserNavigationEntry. A lógica de buffer continua a cuidam de enviar essas entradas ao banco de dados em lotes.

### <a name="master-detail"></a>Detalhes mestre
Parâmetros com valor de tabela são úteis para cenários de inserir simples. No entanto, pode ser mais difícil inserções de lote que envolvem mais de uma tabela. O cenário de "mestre/detalhes" é um bom exemplo. A tabela mestre identifica a entidade principal. Uma ou mais tabelas de detalhes armazenam mais dados sobre a entidade. Neste cenário, relações de chave estrangeira impõem a relação de detalhes para uma entidade mestre exclusiva. Considere a possibilidade de uma versão simplificada de uma tabela de PurchaseOrder e sua tabela OrderDetail associada. O seguinte Transact-SQL cria a tabela PurchaseOrder com quatro colunas: CódigoDoPedido, OrderDate, CustomerID e Status.

    CREATE TABLE [dbo].[PurchaseOrder](
    [OrderID] [int] IDENTITY(1,1) NOT NULL,
    [OrderDate] [datetime] NOT NULL,
    [CustomerID] [int] NOT NULL,
    [Status] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrder] 
    PRIMARY KEY CLUSTERED ( [OrderID] ASC ))

Cada pedido contém um ou mais compras de produto. Esta informação é capturada na tabela PurchaseOrderDetail. O seguinte Transact-SQL cria a tabela de PurchaseOrderDetail com cinco colunas: CódigoDoPedido, OrderDetailID, CódigoDoProduto, PreçoUnitário e OrderQty.

    CREATE TABLE [dbo].[PurchaseOrderDetail](
    [OrderID] [int] NOT NULL,
    [OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
    [ProductID] [int] NOT NULL,
    [UnitPrice] [money] NULL,
    [OrderQty] [smallint] NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
    ( [OrderID] ASC, [OrderDetailID] ASC ))

A coluna OrderID na tabela PurchaseOrderDetail deve fazer referência a um pedido da tabela PurchaseOrder. A seguinte definição de uma chave estrangeira impõe essa restrição.

    ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
    CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
    REFERENCES [dbo].[PurchaseOrder] ([OrderID])

Para poder usar parâmetros com valor de tabela, você deve ter um tipo de tabela definidos pelo usuário para cada tabela de destino.

    CREATE TYPE PurchaseOrderTableType AS TABLE 
    ( OrderID INT,
      OrderDate DATETIME,
      CustomerID INT,
      Status NVARCHAR(50) );
    GO
    
    CREATE TYPE PurchaseOrderDetailTableType AS TABLE 
    ( OrderID INT,
      ProductID INT,
      UnitPrice MONEY,
      OrderQty SMALLINT );
    GO

Defina um procedimento armazenado que aceita tabelas desses tipos. Este procedimento permite que um aplicativo ao lote localmente um conjunto de pedidos e detalhes do pedido em uma única chamada. O seguinte Transact-SQL fornece a declaração de procedimento armazenado completo para este exemplo de ordem de compra.

    CREATE PROCEDURE sp_InsertOrdersBatch (
    @orders as PurchaseOrderTableType READONLY,
    @details as PurchaseOrderDetailTableType READONLY )
    AS
    SET NOCOUNT ON;
    
    -- Table that connects the order identifiers in the @orders
    -- table with the actual order identifiers in the PurchaseOrder table
    DECLARE @IdentityLink AS TABLE ( 
    SubmittedKey int, 
    ActualKey int, 
    RowNumber int identity(1,1)
    );
     
          -- Add new orders to the PurchaseOrder table, storing the actual
    -- order identifiers in the @IdentityLink table   
    INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
    OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
    SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;
    
    -- Match the passed-in order identifiers with the actual identifiers
    -- and complete the @IdentityLink table for use with inserting the details
    WITH OrderedRows As (
    SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber 
    FROM @orders
    )
    UPDATE @IdentityLink SET SubmittedKey = M.OrderID
    FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;
    
    -- Insert the order details into the PurchaseOrderDetail table, 
          -- using the actual order identifiers of the master table, PurchaseOrder
    INSERT INTO PurchaseOrderDetail (
    [OrderID],
    [ProductID],
    [UnitPrice],
    [OrderQty] )
    SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
    FROM @details D
    JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
    GO

Neste exemplo, definidos localmente @IdentityLink tabela armazena os valores de OrderID reais das linhas recém-inseridas. Esses identificadores de ordem são diferentes dos valores CódigoDoPedido temporários na @orders e @details parâmetros com valor de tabela. Por esse motivo, a @IdentityLink tabela se conecta os valores de OrderID da @orders parâmetro para os valores de OrderID reais para as novas linhas na tabela PurchaseOrder. Após essa etapa, o @IdentityLink tabela pode facilitar a inserção de detalhes do pedido com o OrderID real que satisfaz a restrição de chave estrangeira.

Esse procedimento armazenado pode ser usado do código ou de outras chamadas de Transact-SQL. Consulte a seção de parâmetros com valor de tabela deste artigo para obter um exemplo de código. O Transact-SQL seguinte mostra como chamar o sp_InsertOrdersBatch.

    declare @orders as PurchaseOrderTableType
    declare @details as PurchaseOrderDetailTableType
    
    INSERT @orders 
    ([OrderID], [OrderDate], [CustomerID], [Status])
    VALUES(1, '1/1/2013', 1125, 'Complete'),
    (2, '1/13/2013', 348, 'Processing'),
    (3, '1/12/2013', 2504, 'Shipped')
    
    INSERT @details
    ([OrderID], [ProductID], [UnitPrice], [OrderQty])
    VALUES(1, 10, $11.50, 1),
    (1, 12, $1.58, 1),
    (2, 23, $2.57, 2),
    (3, 4, $10.00, 1)
    
    exec sp_InsertOrdersBatch @orders, @details

Essa solução permite cada lote usar um conjunto de valores de OrderID que começam com 1. Os valores de OrderID temporários descrevem as relações no lote, mas os valores de OrderID reais são determinados no momento da operação insert. Você pode executar as mesmas instruções no exemplo anterior repetidamente e gerar ordens exclusivas no banco de dados. Por esse motivo, considere adicionar mais lógica de código ou banco de dados que impede que pedidos duplicados ao usar este lote técnica.

Este exemplo demonstra que ainda mais complexas operações de banco de dados, como operações de mestre / detalhes, em lote usando parâmetros com valor de tabela.

### <a name="upsert"></a>UPSERT
Outro cenário lote envolve simultaneamente atualizando inserindo novas linhas e linhas existentes. Essa operação às vezes é conhecida como uma operação de "UPSERT" (atualização + insert). Em vez de fazer chamadas separadas para inserir e atualizar, a instrução de mesclagem é ideal para essa tarefa. A instrução de mesclagem pode executar ambas as insert e atualizar operações em uma única chamada.

Com valor de tabela parâmetros podem ser usados com a instrução de mala direta para realizar atualizações e inserções. Por exemplo, considere uma tabela de funcionários simplificada que contém as seguintes colunas: EmployeeID, nome, sobrenome, NúmeroDoINPS:

    CREATE TABLE [dbo].[Employee](
    [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [SocialSecurityNumber] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
    ([EmployeeID] ASC ))
 
Neste exemplo, você pode usar o fato de que o NúmeroDoINPS sejam exclusivo para executar uma mesclagem de vários funcionários. Primeiro, crie o tipo de tabela definidos pelo usuário:

    CREATE TYPE EmployeeTableType AS TABLE 
    ( Employee_ID INT,
      FirstName NVARCHAR(50),
      LastName NVARCHAR(50),
      SocialSecurityNumber NVARCHAR(50) );
    GO

Em seguida, crie um procedimento armazenado ou escrever código que usa a declaração de mala direta para executar a atualização e inserir. O exemplo a seguir usa a declaração de mesclagem em um parâmetro com valor de tabela, @employees, do tipo EmployeeTableType. O conteúdo do @employees tabela não são mostradas aqui.

    MERGE Employee AS target
    USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees) 
    AS source ([FirstName], [LastName], [SocialSecurityNumber])
    ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
    WHEN MATCHED THEN 
    UPDATE SET
    target.FirstName = source.FirstName, 
    target.LastName = source.LastName
    WHEN NOT MATCHED THEN
       INSERT ([FirstName], [LastName], [SocialSecurityNumber])
       VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);

Para obter mais informações, consulte a documentação e exemplos para a política de mala direta. Embora o mesmo trabalho poderia ser executado em uma etapa múltiplo armazenada chamada de procedimento com separe inserir e operações de atualização, a instrução de mesclagem é mais eficiente. Código de banco de dados também pode construir chamadas de Transact-SQL que usam a política de mesclagem diretamente sem a necessidade de duas chamadas de banco de dados para inserir e atualização.

## <a name="recommendation-summary"></a>Recomendação de resumo

A lista a seguir fornece um resumo das recomendações lotes abordados neste tópico:

- Use buffer e processamento em lotes para aumentar o desempenho e escalabilidade de aplicativos de banco de dados SQL.
- Compreenda as compensações entre lotes/buffer e resiliência. Durante uma falha de função, o risco de perder um lote não processado dos dados essenciais pode superam o benefício de desempenho de processamento em lotes.
- Tente manter todas as chamadas para o banco de dados dentro de um único data center para reduzir a latência.
- Se você escolher uma técnica de lote única, com valor de tabela parâmetros oferecem o melhor desempenho e flexibilidade.
- Para o desempenho de inserir mais rápido, siga estas orientações gerais mas testar seu cenário:
    - < 100 linhas, use um único comando Inserir parametrizado.
    - < 1000 linhas, use parâmetros com valor de tabela.
    - Para > = 1000 linhas, use SqlBulkCopy.
- Para atualizar e excluir operações, usar parâmetros com valor de tabela com lógica de procedimento armazenado que determina a operação correta em cada linha no parâmetro tabela.
- Diretrizes de tamanho de lote:
    - Use os tamanhos de lote maior que faz sentido para seu aplicativo e necessidades de negócios.
    - Saldo o ganho de desempenho de lotes grandes com os riscos de falhas temporários ou graves. Qual é a consequência de tentativas ou perda dos dados no lote? 
    - Teste o maior tamanho de lote para confirmar que o banco de dados do SQL não rejeitá-la.
    - Crie definições de configuração desse lotes de controle, como o tamanho do lote ou a janela de tempo de buffer. Estas configurações fornecem flexibilidade. Você pode alterar o comportamento do lote em produção sem reimplantar o serviço de nuvem.
- Evite a execução paralela de lotes que operam em uma única tabela em um banco de dados. Se você optar por dividir um único lote entre vários threads de trabalho, execute testes para determinar o número ideal de threads. Após um limite especificado, mais threads irá diminuir o desempenho em vez de aumentá-la.
- Considere a possibilidade de buffer em tamanho e a hora como uma maneira de implementar o processamento em lotes de mais cenários.

## <a name="next-steps"></a>Próximas etapas

Este artigo voltadas como o design de banco de dados e técnicas relacionadas ao lote de codificação podem melhorar o desempenho do aplicativo e a escalabilidade. Mas isso é apenas um fator em sua estratégia geral. Para outras maneiras de melhorar o desempenho e escalabilidade, consulte [diretrizes de desempenho do Azure SQL Database para bancos de dados único](sql-database-performance-guidance.md) e [Considerações de preço e desempenho para um pool de elástica banco de dados](sql-database-elastic-pool-guidance.md).
