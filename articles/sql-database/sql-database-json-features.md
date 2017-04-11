<properties
    pageTitle="Recursos de JSON de banco de dados do SQL Azure | Microsoft Azure"
    description="Banco de dados do SQL Azure permite análise, consulta e formatar os dados em notação JSON JavaScript Object Notation ()."
    services="sql-database"
    documentationCenter=""
    authors="jovanpop-msft"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/17/2016"
    ms.author="jovanpop"
   ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Introdução aos recursos JSON no banco de dados do SQL Azure

Banco de dados do SQL Azure lhe permite analisar dados representados no formato de JavaScript Object Notation [(JSON)](http://www.json.org/) de consulta e exportar seus dados relacionais como texto JSON.

JSON é um formato de dados popular usado para a troca de dados em web moderna e aplicativos móveis. JSON também é usada para armazenar dados semiestruturados em arquivos de log ou em bancos de dados NoSQL como [DocumentDB do Azure](https://azure.microsoft.com/services/documentdb/). Muitos serviços web do resto resultados retornados formatados como texto JSON ou aceitam dados formatados como JSON. Mais Azure serviços como [Pesquisa do Azure](https://azure.microsoft.com/services/search/), [O armazenamento do Azure](https://azure.microsoft.com/services/storage/)e [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) tem pontos de extremidade restante que retornam ou consumam JSON.

Banco de dados do SQL Azure permite trabalhar com dados JSON facilmente e integrar seu banco de dados com serviços modernos.

## <a name="overview"></a>Visão geral

Banco de dados do SQL Azure fornece as seguintes funções para trabalhar com dados JSON:

![Funções JSON](./media/sql-database-json-features/image_1.png)

Se você tiver texto JSON, você pode extrair dados de JSON ou verificar se JSON está formatado corretamente usando as funções internas [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)e [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). A função [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) permite que você atualize o valor de dentro do texto JSON. Para mais avançados consultando e análise, função [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) pode transformar uma matriz de objetos JSON em um conjunto de linhas. Qualquer consulta SQL pode ser executada no conjunto de resultados retornados. Finalmente, há uma cláusula [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) que permite formatar dados armazenados nas tabelas relacionais como texto JSON.

## <a name="formatting-relational-data-in-json-format"></a>Formatação de dados relacionais no formato JSON
Se você tiver um serviço da web que leva dados do banco de dados de camada e fornece uma resposta em JSON formatar ou cliente estruturas JavaScript ou bibliotecas que aceitam dados formatados como JSON, você pode formatar o conteúdo de banco de dados como JSON diretamente em uma consulta SQL. Você já não precisa escrever código de aplicativo que formata os resultados de banco de dados do Azure SQL como JSON ou incluir algumas biblioteca de serialização JSON para converter os resultados da consulta tabular e, em seguida, serializar objetos em formato JSON. Em vez disso, você pode usar a cláusula JSON para formatar os resultados da consulta SQL como JSON no banco de dados do Azure SQL e usá-lo diretamente em seu aplicativo.

No exemplo a seguir, as linhas da tabela Sales. Customer são formatadas como JSON usando a cláusula JSON para:

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

A cláusula para JSON PATH formata os resultados da consulta como texto JSON. Nomes de coluna são usados como chaves, enquanto os valores de célula são gerados como valores JSON:

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

O conjunto de resultados é formatado como uma matriz JSON onde cada linha é formatada como um objeto JSON separado.

CAMINHO indica que você pode personalizar o formato de saída do seu resultado JSON usando a notação de ponto em aliases de coluna. A seguinte consulta altera o nome da chave de "Nome do cliente" no formato JSON de saída e coloca os números de telefone e fax no objeto sub "Contato":

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

A saída dessa consulta tem esta aparência:

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

Neste exemplo, podemos retornado um único objeto JSON em vez de uma matriz, especificando a opção de [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) . Você pode usar esta opção se você souber que você está retornando um único objeto como resultado de consulta.

O valor principal da cláusula FOR JSON é que ele permite retornar dados hierárquicos complexos de seu banco de dados formatado como objetos JSON aninhados ou matrizes. O exemplo a seguir mostra como incluir pedidos que pertencem ao cliente como uma matriz aninhada de pedidos:

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

Em vez de enviar separar consultas para obter dados do cliente e, em seguida, para buscar uma lista dos pedidos relacionados, você pode obter todos os dados necessários com uma única consulta, conforme mostrado na saída de exemplo a seguir:

```
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
]
}
```

## <a name="working-with-json-data"></a>Trabalhando com dados JSON

Se você não tiver dados estruturados terminantemente, se você tiver dados hierárquicos, matrizes ou objetos sub complexos ou se suas estruturas de dados evoluem ao longo do tempo, o formato JSON pode ajudá-lo para representar qualquer estrutura de dados complexos.

JSON é um formato textual que pode ser usado como qualquer outro tipo de cadeia de caracteres no Azure SQL Database. Enviar ou armazenar dados JSON como um NVARCHAR padrão:

```
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

Os dados JSON usados neste exemplo são representados usando o tipo de nvarchar (max). JSON pode ser inserido nessa tabela ou fornecida como argumento do procedimento armazenado usando sintaxe Transact-SQL padrão, conforme mostrado no exemplo a seguir:

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Qualquer idioma do lado do cliente ou a biblioteca que funciona com dados de cadeia de caracteres no banco de dados do SQL Azure também funcionam com dados JSON. JSON pode ser armazenado em qualquer tabela que suporta o tipo NVARCHAR, como uma tabela de memória otimizada ou uma tabela de sistema versão. JSON não apresenta qualquer restrição no código do lado do cliente ou na camada de banco de dados.

## <a name="querying-json-data"></a>Consultando dados JSON

Se você tiver dados formatados como JSON armazenado nas tabelas do SQL Azure, JSON funções, você pode usar esses dados em qualquer consulta SQL.

Funções JSON que estão disponíveis no deixe de banco de dados do SQL Azure você tratar dados formatados como JSON como qualquer outro tipo de dados do SQL. Você pode extrair valores de texto JSON e usar facilmente dados JSON em qualquer consulta:

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

A função JSON_VALUE extrai um valor de texto JSON armazenado na coluna de dados. Esta função usa um caminho semelhante JavaScript para fazer referência a um valor em texto JSON para extrair. Valor extraído pode ser usado em qualquer parte da consulta SQL.

A função JSON_QUERY é semelhante a JSON_VALUE. Ao contrário de JSON_VALUE, esta função extrai objeto sub complexo como matrizes ou objetos que são colocados em texto JSON.

A função JSON_MODIFY permite especificar o caminho do valor em texto JSON que deve ser atualizado, bem como um novo valor que substituirá o antigo. Dessa maneira você pode atualizar facilmente texto JSON sem reparsing toda a estrutura.

Como JSON é armazenada em um texto padrão, existem não garante que os valores armazenados em colunas de texto estão formatados corretamente. Você pode verificar se o texto armazenado na coluna JSON está formatado corretamente usando a função ISJSON e restrições de verificação de banco de dados do Azure SQL padrão:

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Se o texto de entrada está formatado corretamente JSON, a função ISJSON retornará o valor 1. Em cada insert ou update da coluna JSON, essa restrição verificará que o novo valor de texto não é JSON mal formado.

## <a name="transforming-json-into-tabular-format"></a>Transformar JSON em formato tabular

Banco de dados do SQL Azure também permite transformar coleções de JSON em formato e carga ou consulta JSON os dados tabulares.

OPENJSON é uma função de valor de tabela que analisa texto JSON, localiza uma matriz de objetos JSON, percorre os elementos da matriz e retorna uma linha no resultado da saída para cada elemento da matriz.

![JSON tabular](./media/sql-database-json-features/image_2.png)

No exemplo acima, podemos especificar onde localizar a matriz JSON que deve ser aberta (no $. Caminho de pedidos), quais colunas devem ser retornadas como resultado e onde encontrar os valores JSON que serão retornados como células.

Nós pode transformar uma matriz JSON na @orders variável em um conjunto de linhas, para analisar o conjunto de resultados, ou inserir linhas em uma tabela padrão:

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
A coleção de pedidos formatado como uma matriz JSON e fornecida como um parâmetro para o procedimento armazenado pode ser analisado e inserido na tabela Pedidos.

## <a name="next-steps"></a>Próximas etapas

Para saber como integrar JSON em seu aplicativo, consulte estes recursos:

- [Blog do TechNet](https://blogs.technet.microsoft.com/dataplatforminsider/2016/01/05/json-in-sql-server-2016-part-1-of-4/)
- [Documentação do MSDN](https://msdn.microsoft.com/library/dn921897.aspx)
- [Canal de vídeo de 9](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Para saber sobre os vários cenários para integrar JSON em seu aplicativo, consulte as demonstrações neste [vídeo do canal 9](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) ou encontrar um cenário que corresponde ao seu caso de uso em [postagens no Blog de JSON](http://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).
