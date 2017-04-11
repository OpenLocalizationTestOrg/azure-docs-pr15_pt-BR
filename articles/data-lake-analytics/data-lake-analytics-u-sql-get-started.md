<properties
   pageTitle="Desenvolver scripts U-SQL usando ferramentas de Lucerne de dados para o Visual Studio | Azure"
   description="Saiba como instalar dados Lucerne ferramentas para Visual Studio, como desenvolver e scripts de teste U-SQL. "
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-u-sql-language"></a>Tutorial: Começar a usar o idioma de análise de Lucerne de dados U-SQL Azure

U-SQL é uma linguagem que unifica as vantagens de SQL com a expressive potência de seu próprio código para processar todos os dados em qualquer escala. Capacidade de consulta distribuída scalable do U-SQL permite com eficiência analisar dados da loja e entre lojas relacionais como Azure SQL Database.  Ele permite ao processo dados não estruturados, aplicando esquema em ler, inserir lógica personalizada e do UDF e incluem extensibilidade para habilitar o controle fino refinado sobre como executar em escala. Para saber mais sobre a filosofia de design por trás U-SQL, consulte esta [postagem de blog do Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Há algumas diferenças do ANSI SQL ou T-SQL. Por exemplo, suas palavras-chave como selecionar têm que estar em maiusculas.

Ele é o idioma de sistema e a expressão do tipo dentro cláusulas select, onde estão os predicados etc no c#.
Isso significa que os tipos de dados são os tipos de c# e os tipos de dados usam semântica c# nulo e as operações de comparação dentro de um predicado siga sintaxe c# (por exemplo, um = = "foo").  Isso também significa que, se os valores são objetos de .NET completos, permitindo que você facilmente usar qualquer método para operar no objeto (por exemplo, "f o o o". Split(' ')).

Para obter mais informações, consulte [Referência U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

###<a name="prerequisites"></a>Pré-requisitos

Você deve concluir [Tutorial: desenvolver scripts U-SQL usando ferramentas de Lucerne de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

No tutorial, você executou um trabalho de análise de Lucerne de dados com o seguinte script U-SQL:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();

Esse script não tem quaisquer etapas de transformação. Ele lê a partir do arquivo de origem chamado **SearchLog.tsv**, schematizes-lo e gera o conjunto de linhas novamente em um arquivo denominado **SearchLog-primeiro-u-sql.csv**.

Observe o ponto de interrogação ao lado do tipo de dados do campo Duração. Isso significa que o campo Duração poderia ser nulo.

Alguns conceitos e palavras-chave encontradas no script:

- **Variáveis de conjunto de linhas**: cada expressão de consulta que produz um conjunto de linhas pode ser atribuída a uma variável. U-SQL segue o padrão de nomenclatura variável do T-SQL, por exemplo, **@searchlog** no script.
    Observe que a atribuição não forçar a execução. Ele simplesmente nomes a expressão e oferece a capacidade de acúmulos expressões mais complexas.
- **EXTRAIR** fornece a capacidade de definir um esquema na leitura. O esquema é especificado por um nome de coluna e c# par de nome tipo por coluna. Ele usa um chamados **Extractor**, por exemplo, **Extractors.Tsv()** para extrair arquivos tsv. Você pode desenvolver correspondentes personalizados.
- **Saída** leva um conjunto de linhas e serializa-lo. O Outputters.Csv() um arquivo separado por vírgula de saída para o local especificado. Você também pode desenvolver Outputters personalizados.
- Observe que os dois caminhos são caminhos relativos. Você também pode usar caminhos absolutos.  Por exemplo

        adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Você deve usar o caminho absoluto para acessar os arquivos nas contas armazenamento vinculadas.  A sintaxe para arquivos armazenados em conta vinculada de armazenamento do Azure é:

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Contêiner de Blob Azure com permissões de acesso de contêineres público ou públicos blobs não são suportados no momento.

## <a name="use-scalar-variables"></a>Usar variáveis escalares

Você pode usar variáveis escalares também para facilitar a manutenção do seu script. O script U-SQL anterior também pode ser escrito como o seguinte:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Transformar conjuntos de linhas

Use **Selecione** transformar conjuntos de linhas:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

A cláusula WHERE usa [c# expressão booliana](https://msdn.microsoft.com/library/6a71f45d.aspx). Você pode usar a linguagem de expressão c# fazer suas próprias expressões e funções. Você pode até mesmo executar filtragem mais complexa, combinando-os com conjunções lógicas (e) e disjunctions (ORs).

O script a seguir usa o método de DateTime.Parse() e uma conjunção.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();

Observe que a segunda consulta está funcionando no resultado do primeiro conjunto de linhas e, portanto, o resultado é uma composição dos dois filtros. Você também pode reutilizar um nome de variável e os nomes têm como escopo lexicalmente.

## <a name="aggregate-rowsets"></a>Agregados conjuntos de linhas

U-SQL oferece **a familiar ORDER BY**, **GROUP BY** e agregações.

A consulta a seguir localiza a duração total por região e então apresenta topo 5 durações na ordem.

Conjuntos de linhas U-SQL não preservam sua ordem para a consulta seguinte. Portanto, para solicitar uma saída, você precisa adicionar ORDER BY na instrução de saída, conforme mostrado abaixo:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Cláusula SQL U ORDER BY deve ser combinado com a cláusula de busca em uma expressão de seleção.

Cláusula SQL U tendo pode ser usada para restringir a saída para grupos que satisfaçam a condição HAVING:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

## <a name="join-data"></a>Participar de dados

U-SQL fornece operadores comuns de junção como junção interna, junção externa esquerda/direita/completo, associação de ponto, ingressar não apenas as tabelas, mas qualquer conjuntos de linhas (mesmo aqueles produzidos de arquivos).

O seguinte script ingressa a searchlog com um log de impressão de anúncio e nos dá os anúncios para a cadeia de caracteres de consulta para uma determinada data.

    @adlog =
        EXTRACT UserId int,
                Ad string,
                Clicked int
        FROM "/Samples/Data/AdsLog.tsv"
        USING Extractors.Tsv();

    @join =
        SELECT a.Ad, s.Query, s.Start AS Date
        FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                        ON a.UserId == s.UserId
        WHERE a.Clicked == 1;

    OUTPUT @join   
        TO "/output/Searchlog-join.csv"
        USING Outputters.Csv();


U-SQL só oferece suporte a sintaxe da junção compatível com ANSI: predicado Rowset1 junção Rowset2 ativado. A sintaxe antiga do Rowset1, onde Rowset2 predicado não é suportada.
O predicado em uma junção é uma junção de igualdade e nenhuma expressão. Se você quiser usar uma expressão, adicione-o à cláusula select de um conjunto de anterior linhas. Se você quiser fazer uma comparação diferente, você pode movê-la para a cláusula WHERE.


## <a name="create-databases-table-valued-functions-views-and-tables"></a>Criar tabelas, exibições, funções com valor de tabela e bancos de dados

U-SQL permite que você use dados no contexto de um banco de dados e esquema. Portanto, você não tem sempre ler ou gravar arquivos.

Cada script U-SQL é executado com um banco de dados padrão (mestre) e o esquema de padrão (DBO) como seu contexto padrão. Você pode criar seu próprio banco de dados e/ou esquema. Para alterar o contexto, use a instrução **USE** para alterar o contexto.


### <a name="create-a-table-valued-function-tvf"></a>Criar uma função com valor de tabela (TVF)

No script U-SQL anterior, você repetidas usando EXTRAIR ler do mesmo arquivo de origem. Função com valor de tabela do U-SQL permite que você encapsule os dados para reutilização futura.   

O script a seguir cria um TVF chamado *Searchlog()* no banco de dados padrão e esquema:

    DROP FUNCTION IF EXISTS Searchlog;

    CREATE FUNCTION Searchlog()
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    RETURN;
    END;

O script a seguir mostra como usar o TVF definido no script anterior:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-views"></a>Criar modos de exibição

Se você tiver apenas uma expressão de consulta que você deseja abstratos e não deseja parametrizá-lo, você pode criar um modo de exibição em vez de uma função com valor de tabela.

O script a seguir cria um modo de exibição chamado *SearchlogView* no banco de dados padrão e esquema:

    DROP VIEW IF EXISTS SearchlogView;

    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

O seguinte script demonstra usando o modo de exibição definido:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-tables"></a>Criar tabelas

Semelhante à tabela de banco de dados relacional, U-SQL permite que você crie uma tabela com um esquema predefinido ou criar uma tabela e deduzir o esquema da consulta que preenche a tabela (também conhecido como criar tabela como selecione ou CTAS).

O script a seguir cria um banco de dados e duas tabelas:

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SeachLogDb
    USE DATABASE SearchLogDb;

    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;

    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,

                INDEX sl_idx CLUSTERED (UserId ASC)
                    PARTITIONED BY HASH (UserId)
    );

    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC)
                PARTITIONED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### <a name="query-tables"></a>Tabelas de consulta

Você pode consultar as tabelas (criadas no script anterior) da mesma maneira como você consulta sobre os arquivos de dados. Em vez de criar um conjunto de linhas usando EXTRAIR, você agora pode apenas se referir ao nome da tabela.

O script de transformação que você usou anteriormente é modificado para ler das tabelas:

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Observe que você atualmente não é possível executar uma seleção em uma tabela no mesmo script como o script onde você criar tabela.


##<a name="conclusion"></a>Conclusão

O que é coberto no tutorial é apenas uma pequena parte do U-SQL. Devido o escopo deste tutorial, ele não pode cobrir tudo, tais como:

- Usar CROSS APPLY descompactar partes de cadeias de caracteres, matrizes e mapas em linhas.
- Opere particionados conjuntos de dados (conjuntos de arquivos e tabelas particionadas).
- Desenvolva operadores definidos pelo usuário como correspondentes, outputters, processadores, definidos pelo usuário agregador em c#.
- Use funções de janelas de U-SQL.
- Gerencie o código de U-SQL com modos de exibição, procedimentos armazenados e funções com valor de tabela.
- Execute o código personalizado aleatório em seus nós de processamento.
- Conecte a bancos de dados do SQL Azure e agrupe consultas entre eles e seus dados U-SQL e Lucerne de dados do Azure.

## <a name="see-also"></a>Consulte também

- [Visão geral de análise de Lucerne de dados do Microsoft Azure](data-lake-analytics-overview.md)
- [Desenvolver scripts U-SQL usando ferramentas de Lucerne de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Usando funções de janela U-SQL para trabalhos de análise de Lucerne de dados do Azure](data-lake-analytics-use-window-functions.md)
- [Monitorar e solucionar problemas de trabalhos de análise de Lucerne de dados do Azure usando o Portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="let-us-know-what-you-think"></a>Informe o que você pensa

- [Enviar uma solicitação de recurso](http://aka.ms/adlafeedback)
- [Obtenha ajuda nos fóruns](http://aka.ms/adlaforums)
- [Fornecer comentários sobre U-SQL](http://aka.ms/usqldiscuss)
