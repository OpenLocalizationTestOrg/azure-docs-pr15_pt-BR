##<a name="create-client"></a>Criar uma Conexão de cliente

Criar uma conexão de cliente, criando um `WindowsAzure.MobileServiceClient` objeto.  Substituir `appUrl` com a URL para o seu aplicativo móvel.

```
var client = WindowsAzure.MobileServiceClient(appUrl);
```

##<a name="table-reference"></a>Trabalhar com tabelas

A dados do access ou atualização, criar uma referência para a tabela de back-end. Substituir `tableName` com o nome da tabela

```
var table = client.getTable(tableName);
```

Quando você tem uma referência de tabela, você poderá trabalhar melhor com sua tabela:

* [Uma tabela de consulta](#querying)
  * [Filtragem de dados](#table-filter)
  * [Paginação através de dados](#table-paging)
  * [Classificar dados](#sorting-data)
* [Inserindo dados](#inserting)
* [Modificando dados](#modifying)
* [Exclusão de dados](#deleting)

###<a name="querying"></a>Como: uma referência de tabela de consulta

Quando você tem uma referência de tabela, você poderá usá-lo para consultar dados no servidor.  As consultas são feitas em um idioma de "Gostar LINQ".
Para retornar todos os dados da tabela, use o seguinte:

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

A função de sucesso denomina-se com os resultados.   Não use `for (var i in results)` o sucesso funcionar como que será iteramos sobre as informações que estão incluídas nos resultados quando outras funções de consulta (como `.includeTotalCount()`) são usadas.

Para obter mais informações sobre a sintaxe de consulta, consulte a [documentação do objeto de consulta].

####<a name="table-filter"></a>Filtragem de dados no servidor

Você pode usar um `where` cláusula na referência de tabela:

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

Você também pode usar uma função que filtra o objeto.  Nesse caso a `this` variável é atribuída ao objeto atual sendo filtrado.  A seguir há funcionalidade equivalente ao exemplo anterior:

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

####<a name="table-paging"></a>Paginação através de dados

Utilize os métodos take() e skip().  Por exemplo, se você deseja dividir a tabela em registros de linha 100:

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

O `.includeTotalCount()` método é usado para adicionar um campo totalCount para o objeto de resultados.  O campo totalCount é preenchido com o número total de registros que seria retornado se nenhum paginação for usada.

Você pode usar a variável de páginas e alguns botões de interface do usuário para fornecer uma lista de página; Use loadPage() para carregar os novos registros para cada página.  Você deve implementar algum tipo de cache para o acesso de velocidade registros que já foram carregados.


####<a name="sorting-data"></a>Como: retornar dados classificados

Use os métodos de consulta .orderBy() ou .orderByDescending():

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Para obter mais informações sobre o objeto de consulta, consulte a [documentação do objeto de consulta].

###<a name="inserting"></a>Como: inserir dados

Crie um objeto JavaScript com a data adequada e chame table.insert() assíncrona:

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

Inserção bem-sucedida, o item inserido é retornado com os campos adicionais que são necessários para operações de sincronização.  Você deve atualizar seu próprio cache com essas informações para atualizações posteriores.

Observe que o SDK do Azure Mobile aplicativos Node Server oferece suporte a esquema dinâmica para fins de desenvolvimento.
No caso de esquema dinâmica, o esquema da tabela é atualizado dinamicamente, permitindo que você adicione colunas à tabela apenas especificando-os em uma inserção ou operação de atualização.  Recomendamos que você desative esquema dinâmica antes de mover seu aplicativo de produção.

###<a name="modifying"></a>Como: modificar dados

Semelhante ao método .insert(), você deve criar um objeto de atualização e, em seguida, chame .update().  O objeto de atualização deve conter a identificação do registro a ser atualizado - isso é obtido quando o registro de leitura ou ao chamar .insert().

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

###<a name="deleting"></a>Como: excluir dados

Chame o método .del() para excluir um registro.  Passe o ID em uma referência de objeto:

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```
