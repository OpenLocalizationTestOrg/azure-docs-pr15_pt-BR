<properties
   pageTitle="Atribuir variáveis em SQL Data Warehouse | Microsoft Azure"
   description="Dicas para atribuindo variáveis de Transact-SQL no depósito de dados do SQL Azure para desenvolver soluções."
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

# <a name="assign-variables-in-sql-data-warehouse"></a>Atribuir variáveis em SQL Data Warehouse
Variáveis em SQL Data Warehouse são definidas usando o `DECLARE` instrução ou o `SET` instrução.

Todos os itens a seguir são perfeitamente válidas maneiras de definir o valor de uma variável:

## <a name="setting-variables-with-declare"></a>Variáveis de configuração com DECLARE

Inicializar variáveis com DECLARE é uma das maneiras mais flexíveis para definir um valor de variáveis em SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

Você também pode usar DECLARE para definir variável de mais de uma por vez. Não é possível usar `SELECT` ou `UPDATE` para fazer isso:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Você não pode inicializar e usar uma variável na mesma instrução DECLARE. Para ilustrar o ponto de exemplo a seguir **não** é permitido como @p1 foi inicializado e usado na mesma instrução DECLARE. Isso resultará em um erro.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Definir valores com conjunto
Conjunto é um método muito comum de configuração de uma única variável.

Todos os exemplos abaixo são válidas maneiras de definir uma variável com conjunto:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Você só pode definir uma variável por vez com o conjunto. No entanto, como pode ser visto acima operadores compostos são permissable.

## <a name="limitations"></a>Limitações
Você não pode usar SELECT ou UPDATE para atribuição de variável.


## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Visão geral de desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
