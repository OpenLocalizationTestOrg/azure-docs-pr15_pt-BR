<properties
   pageTitle="Transações em SQL Data Warehouse | Microsoft Azure"
   description="Dicas para implementar transações depósito de dados do SQL Azure para desenvolver soluções."
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
   ms.date="07/31/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="transactions-in-sql-data-warehouse"></a>Transações em SQL Data Warehouse

Como você poderia esperar, o SQL Data Warehouse suporta transações como parte da carga de trabalho de depósito de dados. No entanto, para garantir que o desempenho do SQL Data Warehouse é mantido em escala alguns recursos limitam quando comparadas a SQL Server. Este artigo destaca as diferenças e lista os outros. 

## <a name="transaction-isolation-levels"></a>Níveis de isolamento de transação
SQL Data Warehouse implementa transações ACID. No entanto, o isolamento do suporte a transações está limitado a `READ UNCOMMITTED` e isso não pode ser alterado. Você pode implementar um número de codificação métodos para impedir que dirty leituras de dados se esta for importante para você. Os métodos mais populares utilizam CTAS e a mudança de partição de tabela (geralmente conhecida como o padrão de janela deslizante) para impedir que usuários consultando dados que ainda está sendo preparados. Modos de exibição que filtram previamente os dados também é uma abordagem popular.  

## <a name="transaction-size"></a>Tamanho da transação
Uma transação de modificação de dados único é limitada em tamanho. Hoje, o limite é aplicado "por distribuição". Portanto, a alocação total pode ser calculada multiplicando o limite pela contagem de distribuição. Para aproximada o número máximo de linhas na transação divida o limite de distribuição pelo tamanho total de cada linha. Para colunas de comprimento variável considere tomar um comprimento médio de coluna em vez de usar o tamanho máximo.

Na tabela abaixo as seguintes suposições foram feitas:

* Ocorreu uma distribuição uniforme de dados 
* O comprimento de linha média é 250 bytes

| [DWU][]    | Capitular por distribuição (Chaveta) | Número de distribuições | Tamanho máximo de transação (Chaveta) | # Linhas por distribuição | Máximo de linhas por transação |
| ------ | -------------------------- | ----------------------- | -------------------------- | ----------------------- | ------------------------ |
| DW100  |  1                         | 60                      |   60                       |   4,000,000             |    240,000,000           |
| DW200  |  1,5                       | 60                      |   90                       |   6,000,000             |    360,000,000           |
| DW300  |  2,25                      | 60                      |  135                       |   9,000,000             |    540,000,000           |
| DW400  |  3                         | 60                      |  180                       |  12,000,000             |    720,000,000           |
| DW500  |  3,75                      | 60                      |  225                       |  15.000.000             |    900,000,000           |
| DW600  |  4,5                       | 60                      |  270                       |  18,000,000             |  1,080,000,000           |
| DW1000 |  7.5                       | 60                      |  450                       |  30.000.000             |  1,800,000,000           |
| DW1200 |  9                         | 60                      |  540                       |  36,000,000             |  2,160,000,000           |
| DW1500 | 11,25                      | 60                      |  675                       |  45,000,000             |  2,700,000,000           |
| DW2000 | 15                         | 60                      |  900                       |  60.000.000             |  3,600,000,000           |
| DW3000 | 22.5                       | 60                      |  1,350                     |  90,000,000             |  5,400,000,000           |
| DW6000 | 45                         | 60                      |  2.700                     | 180,000,000             | 10,800,000,000           |

O limite de tamanho de transação é aplicado por transação ou operação. Ela não é aplicada em todas as transações simultâneas. Portanto, cada transação tem permissão para gravar essa quantidade de dados no log. 

Para otimizar e minimizar a quantidade de dados gravados no log, consulte o artigo [transações práticas recomendadas][] .

> [AZURE.WARNING] O tamanho máximo da transação só pode ser obtido para HASH ou mesmo de tabelas ROUND_ROBIN distribuído onde está a disseminação dos dados. Se a transação está gravando dados em um modo de inclinada as distribuições o limite é provavelmente a ser alcançado antes do tamanho máximo da transação.
<!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Estado de transação
SQL Data Warehouse usa a função XACT_STATE() para relatar uma transação que falhou usando o valor -2. Isso significa que a transação falhou e está marcada para reversão somente

> [AZURE.NOTE] O uso de -2 pela função XACT_STATE para indicar uma transação que falhou representa o comportamento diferente para o SQL Server. SQL Server usa o valor -1 para representar uma transação sem committable. SQL Server pode suportar alguns erros dentro de uma transação sem ele precisar ser marcadas como não committable. Por exemplo `SELECT 1/0` seria causar um erro, mas não forçar uma transação em um estado não committable. SQL Server também permite leituras na transação não committable. No entanto, o SQL Data Warehouse não permitem que você faça isso. Se ocorrer um erro dentro de uma transação de SQL Data Warehouse inserirá automaticamente o estado de-2 e não será capaz de fazer qualquer instruções select ainda mais até que a política foi revertida. Portanto, é importante verificar que o código do seu aplicativo para ver se ele usa XACT_STATE() como você talvez precise fazer modificações no código.

Por exemplo, no SQL Server, você talvez veja uma transação que tem esta aparência:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
        
        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Se você deixar o código que ele seja acima e em seguida, você receberá a seguinte mensagem de erro:

Msg 111233, nível 16, estado 1, linha 1 111233; A transação atual foi anulada e quaisquer alterações pendentes tiverem sido revertidas. Causa: Uma transação em um estado somente reversão não foi explicitamente revertida antes de uma DDL, DML ou instrução SELECT.

Você também não terá a saída das funções ERROR_ *.

Em SQL Data Warehouse o código precisa ser ligeiramente alterada:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();
        
        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Agora é observado o comportamento esperado. O erro na transação é gerenciado e as funções ERROR_ * fornecem os valores conforme esperado.

Tudo o que mudou é que o `ROLLBACK` da transação tinha acontecer antes da leitura das informações de erro na `CATCH` bloco.

## <a name="errorline-function"></a>Função Error_Line()
Também é importante observar que SQL Data Warehouse não implementar ou a função ERROR_LINE() de suporte. Se você tiver isso no seu código, você precisará removê-lo para ser compatível com SQL Data Warehouse. Use rótulos de consulta em seu código para implementar a funcionalidade equivalente. Consulte o artigo de [etiqueta][] para obter mais detalhes sobre esse recurso.

## <a name="using-throw-and-raiserror"></a>Usando lançar e RAISERROR
Lançar é a implementação mais moderna para disparar exceções em SQL Data Warehouse, mas também é compatível com RAISERROR. Há algumas diferenças que vale a pena prestar atenção para Entretanto.

- Mensagens de erro não podem ser números no intervalo 100,000 150.000 para lançar definidas pelo usuário
- Mensagens de erro RAISERROR são fixos em 50.000
- Não há suporte para o uso de sys.messages

## <a name="limitiations"></a>Limitiations
SQL Data Warehouse tem algumas outras restrições relacionadas a transações.

Eles são da seguinte maneira:

- Não há transações distribuídas
- Não há transações aninhadas permitidas
- Não salvar pontos permitidos
- Não há transações nomeadas
- Não há transações marcadas
- Não há suporte para DDL como `CREATE TABLE` dentro de um usuário definido transação

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como otimizar as transações, consulte [práticas recomendadas de transações][].  Para saber mais sobre outras práticas recomendadas de depósito de dados do SQL, consulte [práticas recomendadas do SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[development overview]: ./sql-data-warehouse-overview-develop.md
[Práticas recomendadas de transações]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Práticas recomendadas de depósito de dados do SQL]: ./sql-data-warehouse-best-practices.md
[RÓTULO]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->

<!--Other Web references-->
