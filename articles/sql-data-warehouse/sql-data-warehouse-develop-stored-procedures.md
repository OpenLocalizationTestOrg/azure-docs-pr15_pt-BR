<properties
   pageTitle="Procedimentos armazenados no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para implementar procedimentos armazenados no armazém de dados do SQL Azure para desenvolvimento de soluções."
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
   ms.date="06/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="stored-procedures-in-sql-data-warehouse"></a>Procedimentos armazenados no SQL Data Warehouse

SQL Data Warehouse compatível com muitos dos recursos Transact-SQL encontrados no SQL Server. Há mais importante escala os recursos específicos que serão queremos aproveitar para maximizar o desempenho de sua solução.

No entanto, para manter a escala e o desempenho do SQL Data Warehouse lá também são alguns dos recursos e funcionalidades que têm diferenças de comportamento e outras pessoas que não são suportados.

Este artigo explica como implementar procedimentos armazenados no depósito de dados do SQL.

## <a name="introducing-stored-procedures"></a>Apresentando o procedimentos armazenados
Procedimentos armazenados são uma ótima maneira para encapsular seu código SQL; armazenando-próxima seus dados no data warehouse. Encapsulando o código em unidades gerenciáveis procedimentos armazenados ajudam os desenvolvedores modularizar suas soluções; facilitando maior reutilização de código. Cada procedimento armazenado também pode aceitar parâmetros para facilitar ainda mais flexível.

SQL Data Warehouse fornece uma implementação de procedimento armazenado simplificado e simplificada. A maior diferença em comparação com o SQL Server é o procedimento armazenado não código previamente compilado. Em depósitos de dados estamos geralmente menos preocupados com o tempo de compilação. É mais importante do que o código do procedimento armazenado é otimizado corretamente quando operando contra grandes volumes de dados. O objetivo é salvar horas, minutos e segundos não milissegundos. Portanto, é mais útil pensar procedimentos armazenados como contêineres para lógica SQL.     

Quando o SQL Data Warehouse executa o procedimento armazenado as instruções SQL são analisadas, traduzido e otimizada em tempo de execução. Durante esse processo cada instrução é convertida em consultas distribuídas. O código SQL que é realmente executado com os dados é diferente para a consulta enviada.

## <a name="nesting-stored-procedures"></a>Procedimentos armazenado de aninhamento
Quando os procedimentos armazenados chamam outros procedimentos armazenados ou executar sql dinâmico e em seguida, a chamada de procedimento ou código armazenada interna é considerada aninhado.

SQL Data Warehouse oferece suporte a um máximo de níveis de aninhamento de 8. Este é um pouco diferente para o SQL Server. O nível de aninhamento no SQL Server é 32.

A chamada de procedimento armazenado de nível superior equivale para aninhar nível 1

```sql
EXEC prc_nesting
```
Se o procedimento armazenado também faz executivo outra chamada, em seguida, isso aumentará o nível de aninhamento para 2
```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Se o segundo procedimento executa alguns sql dinâmico, em seguida, isso aumentará o nível de aninhamento para 3
```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Observação SQL Data Warehouse não suportamos @@NESTLEVEL. Você precisará manter uma trilha de seu nível de aninhamento por conta própria. É provável que você atingirá o limite de nível de 8 aninhamento, mas se você não fizer você precisará trabalhar novamente seu código e "Nivelar"-la para que caiba dentro esse limite.

## <a name="insertexecute"></a>INSERIR.. EXECUTAR
SQL Data Warehouse não permite que você para consumir o conjunto de resultados de um procedimento armazenado com uma instrução INSERT. No entanto, há uma abordagem alternativa, que você pode usar.

Consulte o artigo a seguir nas [tabelas temporárias] para obter um exemplo sobre como fazer isso.

## <a name="limitations"></a>Limitações

Existem alguns aspectos dos procedimentos armazenado Transact-SQL que não são implementados em SQL Data Warehouse.

Eles são:

- procedimentos armazenados temporários
- procedimentos armazenados numerados
- procedimentos armazenados estendidos
- Procedimentos armazenados CLR
- opção de criptografia
- opção de replicação
- parâmetros com valor de tabela
- parâmetros de somente leitura
- parâmetros padrão
- contextos de execução
- instrução Return

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento][].

<!--Image references-->

<!--Article references-->
[tabelas temporárias]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Visão geral de desenvolvimento]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/library/ms187371.aspx

<!--Other Web references-->
