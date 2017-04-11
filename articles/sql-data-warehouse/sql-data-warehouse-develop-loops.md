<properties
   pageTitle="Loops em SQL Data Warehouse | Microsoft Azure"
   description="Dicas para substituir cursores no armazém de dados do SQL Azure para desenvolver soluções e loops de Transact-SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="loops-in-sql-data-warehouse"></a>Loops em SQL Data Warehouse
SQL Data Warehouse é compatível com o loop [ENQUANTO][] para executar repetidamente blocos de instrução. Isso continuará para desde que as condições especificadas forem verdadeiras, ou até que o código especificamente termina o loop usando o `BREAK` palavra-chave. Loops são particularmente úteis para a substituição cursores definidos no código SQL. Felizmente, quase todos os cursores escritas em código SQL são do encaminhamento rápido, leia apenas variedade. Portanto loops [ENQUANTO] são uma ótima alternativa se você precisar substituir um.

## <a name="leveraging-loops-and-replacing-cursors-in-sql-data-warehouse"></a>Aproveitando loops e substituindo cursores em SQL Data Warehouse
No entanto, antes de mergulhar em cabeça primeiro você deve se perguntar a seguinte pergunta: "Pôde desse cursor ser novamente gravado usar operações de conjunto baseado?". Em muitos casos a resposta será Sim e geralmente é a melhor abordagem. Uma operação de conjunto baseado geralmente executa significativamente mais rápido do que uma abordagem iterativa, linha por linha.

Avanço cursores de somente leitura podem ser substituídos facilmente com uma construção de loop. Abaixo está um exemplo simples. Este exemplo de código atualiza as estatísticas para cada tabela no banco de dados. Fazendo a iteração sobre as tabelas no loop estamos capazes de executar cada comando na sequência.

Primeiro, crie uma tabela temporária que contém um número de linha exclusiva usado para identificar as instruções individuais:

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Segundo, inicialize as variáveis necessárias para executar o loop:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Agora loop sobre instruções executá-los um por vez:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Finalmente descartar a tabela temporária criada na primeira etapa

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Visão geral de desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[TEMPO]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->
