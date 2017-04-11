<properties
    pageTitle="Selecionar linhas a serem migradas usando uma função de filtro (Alongar Database) | Microsoft Azure"
    description="Saiba como selecionar linhas a serem migradas usando uma função de filtro."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="douglasl"/>

# <a name="select-rows-to-migrate-by-using-a-filter-function-stretch-database"></a>Selecionar linhas a serem migradas usando uma função de filtro (Alongar Database)

Se você armazenar dados frio em uma tabela separada, você pode configurar o banco de dados de Alongar para migrar a tabela inteira. Se sua tabela contém dados quente / frio, por outro lado, você pode especificar uma função de filtro para selecionar as linhas a serem migradas. O predicado de filtro é uma tabela embutida\-com o valor de função. Este tópico descreve como escrever uma tabela embutida\-com o valor de função para selecionar linhas a serem migradas.

>   [AZURE.NOTE] Se você fornecer uma função de filtro que realiza mal, migração de dados também executa mal. Banco de dados Alongar aplica a função de filtro à tabela usando o operador CROSS APPLY.

Se você não especificar uma função de filtro, a tabela inteira é migrada.

Quando você executa o banco de dados habilitar para alongar assistente, você pode migrar uma tabela inteira ou você pode especificar uma função de filtro simples no assistente. Se você quiser usar um tipo diferente de função de filtro para selecionar linhas a serem migradas, siga um destes procedimentos.

-   Sair do assistente e executar a instrução ALTER TABLE para habilitar Alongar para a tabela e especificar uma função de filtro.

-   Execute a instrução ALTER TABLE para especificar uma função de filtro após você sair do assistente.

A sintaxe ALTER TABLE para adicionar uma função é descrita neste tópico.

## <a name="basic-requirements-for-the-filter-function"></a>Requisitos básicos para a função de filtro
A tabela embutida\-função de valor necessária para um predicado de filtro do banco de dados de alongar se parece com o exemplo a seguir.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datatype1, @column2 datatype2 [, ...n])
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE <predicate>
```
Os parâmetros para a função precisam ser identificadores de colunas da tabela.

Vinculação de esquema é necessária para evitar que as colunas que são usadas pela função filtro sejam descartados ou alterados.

### <a name="return-value"></a>Valor de retorno
Se a função retornará uma não\-resultado vazio, a linha é elegível a serem migradas. Caso contrário, \- ou seja, se a função não retornar um resultado \- a linha não está qualificada a serem migradas.

### <a name="conditions"></a>Condições
O &lt; *predicado* &gt; podem ser compostos de uma condição ou de várias condições associadas com o operador lógico AND.

```
<predicate> ::= <condition> [ AND <condition> ] [ ...n ]
```
Cada condição alternadamente pode ser compostos de uma condição primitiva ou de várias condições primitivas associadas com o operador lógico OR.

```
<condition> ::= <primitive_condition> [ OR <primitive_condition> ] [ ...n ]
```

### <a name="primitive-conditions"></a>Condições primitivas
Uma condição primitiva pode seguir um das comparações a seguintes.

```
<primitive_condition> ::=
{
<function_parameter> <comparison_operator> constant
| <function_parameter> { IS NULL | IS NOT NULL }
| <function_parameter> IN ( constant [ ,...n ] )
}
```

-   Compare um parâmetro de função em uma expressão constante. Por exemplo, `@column1 < 1000`.

    Aqui está um exemplo que verifica se o valor de uma coluna de *Data* é &lt; 1/1/2016.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
    GO

    ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   Aplica o operador é nulo ou não é nulo para um parâmetro de função.

-   Use o operador IN para comparar um parâmetro de função a uma lista de valores constantes.

    Aqui está um exemplo que verifica se o valor de uma *remessa\_status* coluna é `IN (N'Completed', N'Returned', N'Cancelled')`.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(shipment_status),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

### <a name="comparison-operators"></a>Operadores de comparação
Há suporte para os seguintes operadores de comparação.

`<, <=, >, >=, =, <>, !=, !<, !>`

```
<comparison_operator> ::= { < | <= | > | >= | = | <> | != | !< | !> }
```

### <a name="constant-expressions"></a>Expressões constantes
As constantes que você usa em uma função de filtro podem ser qualquer expressão determinante que pode ser avaliada quando você define a função. Expressões constantes podem conter os seguintes procedimentos.

-   Literais. Por exemplo, `N’abc’, 123`.

-   Expressões algébricas. Por exemplo, `123 + 456`.

-   Funções determinantes. Por exemplo, `SQRT(900)`.

-   Determinantes conversões que usam CAST ou CONVERT. Por exemplo, `CONVERT(datetime, '1/1/2016', 101)`.

### <a name="other-expressions"></a>Outras expressões
Você pode usar a entre e não os operadores entre se a função resultante está de acordo com as regras descritas aqui depois de você substituir o entre e não entre operadores com o equivalente e e ou expressões.

Não é possível usar subconsultas ou não\-determinantes funções como aleatório () ou GETDATE().

## <a name="add-a-filter-function-to-a-table"></a>Adicionar uma função de filtro a uma tabela
Adicionar uma função de filtro a uma tabela, executando a instrução **ALTER TABLE** e especificando uma tabela existente do embutida\-com o valor de função como o valor da **filtro\_PREDICADO** parâmetro. Por exemplo:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
) )
```
Depois de vincular a função à tabela como um predicado, as seguintes ações são verdadeiras.

-   A próxima migração de dados de tempo ocorre, apenas as linhas para o qual a função retorna um não\-valor vazio são migradas.

-   As colunas usadas pela função são vinculado ao esquema. Você não pode alterar essas colunas como uma tabela está usando a função como seu predicado de filtro.

Você não pode soltar a tabela embutida\-com o valor de função como uma tabela está usando a função como seu predicado de filtro.

>   [AZURE.NOTE] Para melhorar o desempenho da função filtro, crie um índice nas colunas usadas pela função.

### <a name="passing-column-names-to-the-filter-function"></a>Passando nomes de coluna para a função de filtro
Quando você atribui uma função de filtro a uma tabela, especifique os nomes de coluna passaram para a função de filtro com um nome de parte única. Se você especificar um nome de três partes quando você passa a coluna nomes, consultas subsequentes a Alongar\-tabela enabled falhará.

Por exemplo, se você especificar um nome de coluna de três partes, conforme mostrado no exemplo a seguir, a instrução será executada com êxito, mas consultas subsequentes em tabela falhará.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(dbo.SensorTelemetry.ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

Em vez disso, especifique a função de filtro com um nome de parte única coluna, conforme mostrado no exemplo a seguir.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON  (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

## <a name="addafterwiz"></a>Adicionar uma função de filtro após executar o Assistente  

Se você quiser usar uma função que você não pode criar no Assistente de **Habilitar o banco de dados para alongar** , você pode executar a instrução ALTER TABLE para especificar uma função após você sair do assistente. Antes de aplicar uma função, no entanto, você precisa interromper a migração de dados que já está em andamento e trazer dados migrados de volta. (Para obter mais informações sobre por que isso é necessário, consulte [Substituir uma função de filtro existente](#replacePredicate).  

1. Reverter a direção de migração e trazer de volta que os dados já migrados. Você não pode cancelar esta operação depois que ele for iniciado. Você também provoca custos no Azure para transferências de dados de saída \(egresso\). Para obter mais informações, consulte [como Azure preços funciona](https://azure.microsoft.com/pricing/details/data-transfers/).  

    ```tsql  
    ALTER TABLE <table name>  
         SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;   
    ```  

2. Aguarde a migração concluir. Você pode verificar o status no **Alongar Monitor de banco de dados** do SQL Server Management Studio, ou você pode consultar o modo de exibição de **sys.dm_db_rda_migration_status** . Para obter mais informações, consulte [Monitor e solucionar problemas de migração de dados](sql-server-stretch-database-monitor.md) ou [sys.dm_db_rda_migration_status](https://msdn.microsoft.com/library/dn935017.aspx).  

3. Crie a função de filtro que você deseja aplicar à tabela.  

4. Adicione a função à tabela e reinicie a migração de dados no Azure.  

    ```tsql  
    ALTER TABLE <table name>  
        SET ( REMOTE_DATA_ARCHIVE  
            (           
                FILTER_PREDICATE = <predicate>,  
                MIGRATION_STATE = OUTBOUND  
            )  
        );   
    ```  

## <a name="filter-rows-by-date"></a>Filtrar linhas por data
O exemplo a seguir migra linhas onde a coluna **Data** contém um valor anterior a 1 de janeiro de 2016.

```tsql
-- Filter by date
--
CREATE FUNCTION dbo.fn_stretch_by_date(@date datetime2)
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @date < CONVERT(datetime2, '1/1/2016', 101)
GO
```

## <a name="filter-rows-by-the-value-in-a-status-column"></a>Filtrar linhas pelo valor em uma coluna de status
O exemplo a seguir migra linhas onde a coluna **status** contém um dos valores especificados.

```tsql
-- Filter by status column
--
CREATE FUNCTION dbo.fn_stretch_by_status(@status nvarchar(128))
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @status IN (N'Completed', N'Returned', N'Cancelled')
GO
```

## <a name="filter-rows-by-using-a-sliding-window"></a>Filtrar linhas usando uma janela deslizante
Para filtrar linhas usando uma janela deslizante, tenha em mente os seguintes requisitos para a função de filtro.

-   A função deve ser determinante. Portanto, você não pode criar uma função que recalcula automaticamente a janela deslizante passar do tempo.

-   A função usa vinculação de esquema. Portanto você não pode simplesmente atualizar a função "in-loco" diariamente chamando **Alterar função** para mover a janela deslizante.

Comece com uma função de filtro como o exemplo a seguir, que migra linhas onde a coluna **systemEndTime** contém um valor anterior a 1 de janeiro de 2016.

```tsql
CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160101(@systemEndTime datetime2)
RETURNS TABLE
WITH SCHEMABINDING  
AS  
RETURN SELECT 1 AS is_eligible
  WHERE @systemEndTime < CONVERT(datetime2, '2016-01-01T00:00:00', 101) ;
```

Aplica a função filter à tabela.

```tsql
ALTER TABLE <table name>
SET (
        REMOTE_DATA_ARCHIVE = ON
                (
                        FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160101 (SysEndTime)
                                , MIGRATION_STATE = OUTBOUND
                )
        )
;
```

Quando você deseja atualizar a janela deslizante, faça o seguinte.

1.  Crie uma nova função que especifica a nova janela deslizante. O exemplo a seguir seleciona datas anteriores a 2 de janeiro de 2016, em vez de 1 de janeiro de 2016.

2.  Substitua a função de filtro anterior uma nova chamada **ALTER TABLE**, conforme mostrado no exemplo a seguir.

3. Opcionalmente, solte a anterior função filter que já não esteja sendo usado pela **Função SOLTAR**de chamada. (Esta etapa não é mostrada no exemplo.)

```tsql
BEGIN TRAN
GO
        /*(1) Create new predicate function definition */
        CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160102(@systemEndTime datetime2)
        RETURNS TABLE
        WITH SCHEMABINDING
        AS
        RETURN SELECT 1 AS is_eligible
               WHERE @systemEndTime < CONVERT(datetime2,'2016-01-02T00:00:00', 101)
        GO

        /*(2) Set the new function as the filter predicate */
        ALTER TABLE <table name>
        SET
        (
               REMOTE_DATA_ARCHIVE = ON
               (
                       FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160102(SysEndTime),
                       MIGRATION_STATE = OUTBOUND
               )
        )
COMMIT ;
```

## <a name="more-examples-of-valid-filter-functions"></a>Mais exemplos de funções de filtro válido

-   O exemplo a seguir combina duas condições primitivas usando o operador lógico e.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate((@column1 datetime, @column2 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
      WHERE @column1 < N'20150101' AND @column2 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date, shipment_status),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   O exemplo a seguir usa várias condições e uma conversão determinante com converter.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example1(@column1 datetime, @column2 int, @column3 nvarchar)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)AND (@column2 < -100 OR @column2 > 100 OR @column2 IS NULL)AND @column3 IN (N'Completed', N'Returned', N'Cancelled')
    GO
    ```

-   O exemplo a seguir usa funções e operadores matemáticos.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example2(@column1 float)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < SQRT(400) + 10
    GO
    ```

-   O exemplo a seguir usa o entre e não os operadores entre. Esse uso é válido, porque a função resultante está de acordo com as regras descritas aqui depois de você substituir o entre e não entre operadores com o equivalente e e ou expressões.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example3(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 BETWEEN 0 AND 100
                AND (@column2 NOT BETWEEN 200 AND 300 OR @column1 = 50)
    GO
    ```
    A função anterior é equivalente à função seguinte após substituir o não os operadores de entre e entre com o equivalente e e ou expressões.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example4(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 >= 0 AND @column1 <= 100AND (@column2 < 200 OR @column2 > 300 OR @column1 = 50)
    GO
    ```

## <a name="examples-of-filter-functions-that-arent-valid"></a>Exemplos de funções de filtro que não são válidos

-   A função a seguir não é válida porque ela contém um não\-conversão determinante.

    ```tsql
    CREATE FUNCTION dbo.fn_example5(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < CONVERT(datetime, '1/1/2016')
    GO
    ```

-   A função a seguir não é válida porque ela contém um não\-chamada de função determinante.

    ```tsql
    CREATE FUNCTION dbo.fn_example6(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < DATEADD(day, -60, GETDATE())
    GO
    ```

-   A função a seguir não é válida porque ela contém uma subconsulta.

    ```tsql
    CREATE FUNCTION dbo.fn_example7(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 IN (SELECT SupplierID FROM Supplier WHERE Status = 'Defunct'))
    GO
    ```

-   As funções a seguir não são válidas porque expressões que usam operadores algébricas ou criado\-em funções devem ser avaliados como uma constante quando você define a função. Você não pode incluir referências de coluna em expressões algébricas ou chamadas de função.

    ```tsql
    CREATE FUNCTION dbo.fn_example8(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 % 2 =  0
    GO

    CREATE FUNCTION dbo.fn_example9(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE SQRT(@column1) = 30
    GO
    ```

-   A função a seguir não é válida porque ele viola as regras descritas aqui depois de você substituir o operador BETWEEN com a expressão e equivalente.

    ```tsql
    CREATE FUNCTION dbo.fn_example10(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE (@column1 BETWEEN 1 AND 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```
    A função anterior é equivalente à função seguinte após substituir o operador BETWEEN com a expressão e equivalente. Esta função não é válida porque condições primitivas só podem usar o operador lógico OR.

    ```tsql
    CREATE FUNCTION dbo.fn_example11(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE (@column1 >= 1 AND @column1 <= 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```

## <a name="how-stretch-database-applies-the-filter-function"></a>Como o banco de dados de alongar aplica a função de filtro
Banco de dados Alongar aplica a função de filtro à tabela e determina linhas qualificadas usando o operador CROSS APPLY. Por exemplo:

```tsql
SELECT * FROM stretch_table_name CROSS APPLY fn_stretchpredicate(column1, column2)
```
Se a função retornará uma não\-resultado vazio para a linha, a linha é elegível a serem migradas.

## <a name="replacePredicate"></a>Substituir uma função de filtro existente
Você pode substituir uma função de filtro especificado anteriormente, executando a instrução **ALTER TABLE** novamente e especificando um novo valor para o **filtro\_PREDICADO** parâmetro. Por exemplo:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate2(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
```
A nova tabela embutida\-função de valor tem os seguintes requisitos.

-   A nova função deve ser menos restritivo do que a função anterior.

-   Todos os operadores existentes na função antiga devem constar a nova função.

-   A nova função não pode conter operadores que não existem na função antiga.

-   Não é possível alterar a ordem dos argumentos de operador.

-   Somente os valores constantes que fazem parte de um `<, <=, >, >=` comparação pode ser alterada de uma maneira que faz a função menos restritivo.

### <a name="example-of-a-valid-replacement"></a>Exemplo de uma substituição válido
Suponha que a função a seguir é o predicado de filtro atual.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_old (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
A função a seguir é uma substituição válida porque a constante de nova data (que especifica uma data de fechamento mais tarde) faz com que a função menos restritivo.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_new (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '2/1/2016', 101)
            AND (@column2 < -50 OR @column2 > 50)
GO
```

### <a name="examples-of-replacements-that-arent-valid"></a>Exemplos de substituições que não são válidos
A função a seguir não substitui válido porque a constante de nova data (que especifica uma data de fechamento anteriormente) não faz a função menos restritivo.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_1 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
A função a seguir não substitui válido porque um dos operadores de comparação foi removido.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_2 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -50)
GO
```
A função a seguir não substitui válido porque uma nova condição foi adicionada com o operador lógico AND.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_3 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
            AND (@column2 <> 0)
GO
```

## <a name="remove-a-filter-function-from-a-table"></a>Remover uma função de filtro de uma tabela
Para migrar a tabela inteira em vez de linhas selecionadas, remova a função existente definindo **filtro\_PREDICADO** para null. Por exemplo:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = NULL,
    MIGRATION_STATE = <desired_migration_state>
) )
```
Depois de remover a função de filtro, todas as linhas da tabela são qualificadas para a migração. Como resultado, você não pode especificar uma função de filtro para a mesma tabela posteriormente, a menos que você trazer de volta todos os dados remotos para a tabela do Azure primeiro. Essa restrição existe para evitar a situação onde linhas que não são qualificadas para migração quando você fornece uma nova função de filtro já foram migradas para o Azure.

## <a name="check-the-filter-function-applied-to-a-table"></a>Verifique a função de filtro aplicada a uma tabela
Para verificar a função de filtro aplicada a uma tabela, abra o modo de exibição de catálogo **sys.remote\_dados\_arquivamento\_tabelas** e verificar o valor da **filtro\_predicado** coluna. Se o valor for nulo, a tabela inteira está qualificada para arquivamento. Para obter mais informações, consulte [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="security-notes-for-filter-functions"></a>Notas de segurança para funções de filtro  
Uma conta com privilégios de db_owner comprometida pode fazer o seguinte.  

-   Criar e aplicar uma função com valor de tabela que consome grandes quantidades de recursos do servidor ou aguarda por um longo período que resulta em uma negação de serviço.  

-   Criar e aplicar uma função com valor de tabela que possibilita inferir o conteúdo de uma tabela para a qual o usuário foi explicitamente negado acesso de leitura.  

## <a name="see-also"></a>Consulte também

[Alterar tabela (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
