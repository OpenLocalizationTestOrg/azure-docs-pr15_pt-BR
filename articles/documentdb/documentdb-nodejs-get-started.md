<properties
  pageTitle="Tutorial NoSQL Node para DocumentDB | Microsoft Azure"
  description="Um tutorial NoSQL Node que cria um aplicativo de banco de dados e console de nó usando o SDK do Node DocumentDB. DocumentDB é um banco de dados NoSQL JSON."
    keywords="node tutorial, banco de dados de nó"
  services="documentdb"
  documentationCenter="node.js"
  authors="AndrewHoh"
  manager="jhubbard"
  editor="monicar"/>

<tags
  ms.service="documentdb"
  ms.workload="data-services"
  ms.tgt_pltfrm="na"
  ms.devlang="node"
  ms.topic="hero-article"
  ms.date="08/11/2016"
  ms.author="anhoh"/>

# <a name="nosql-nodejs-tutorial-documentdb-nodejs-console-application"></a>Tutorial do NoSQL node: aplicativo de console DocumentDB Node  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node](documentdb-nodejs-get-started.md)

Bem-vindo ao tutorial Node para o SDK do Azure DocumentDB Node! Após seguir este tutorial, você terá um aplicativo de console que cria e consultas DocumentDB recursos, incluindo um banco de dados de nó.

Vamos abordar:

- Criando e conexão para uma conta de DocumentDB
- Configurar o aplicativo
- Criando um banco de dados de nó
- Criando uma coleção
- Criando documentos JSON
- Consultar a coleção
- Substituindo um documento
- Exclusão de um documento
- Excluir o banco de dados de nó

Não tem tempo? Não se preocupe! A solução completa está disponível no [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). Consulte [obter a solução completa](#GetSolution) para obter instruções rápidas.

Após concluir o tutorial Node, use os botões de votação na parte superior e inferior desta página para nos enviar comentários. Se você quiser que contatemos você diretamente, fique à vontade para incluir seu endereço de email em seus comentários.

Agora vamos começar!

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Pré-requisitos para o tutorial Node

Verifique se que você tem o seguinte:

- Uma conta do Azure active. Se você não tiver um, você pode inscrever-se para uma [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- [Node](https://nodejs.org/) versão v0.10.29 ou superior.

## <a name="step-1-create-a-documentdb-account"></a>Etapa 1: Criar uma conta de DocumentDB

Vamos criar uma conta de DocumentDB. Se você já tiver uma conta que você deseja usar, você pode saltar para [configurar seu aplicativo Node](#SetupNode).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupNode"></a>Etapa 2: Configurar o seu aplicativo Node

1. Abra o terminal favorito.
2. Localize a pasta onde você deseja salvar seu aplicativo Node ou pasta.
3. Crie dois arquivos JavaScript vazios com os seguintes comandos:
  - Windows:
      * ```fsutil file createnew app.js 0```
        * ```fsutil file createnew config.js 0```
  - Linux/SO x:
      * ```touch app.js```
        * ```touch config.js```
4. Instale o módulo documentdb via npm. Use o seguinte comando:
    * ```npm install documentdb --save```

Legal! Agora que você terminar de configurar, vamos começar a escrever algum código.

## <a id="Config"></a>Etapa 3: Definir as configurações do seu aplicativo

Abrir ```config.js``` no editor de texto favorito.

Em seguida, copiar e colar o trecho de código abaixo e definir propriedades ```config.endpoint``` e ```config.primaryKey``` para seu uri do ponto de extremidade de DocumentDB e a chave primária. Ambas as essas configurações podem ser encontradas no [Portal do Azure](https://portal.azure.com).

![Tutorial Node - captura de tela do Portal do Azure, mostrando uma conta de DocumentDB, com o hub ativo realçado, o botão de chaves realçado na lâmina DocumentDB conta e os valores URI, chave primária e chave SECUNDÁRIA realçados na lâmina teclas - banco de dados de nó][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

Copiar e colar a ```database id```, ```collection id```, e ```JSON documents``` à sua ```config``` objeto abaixo de onde você definir sua ```config.endpoint``` e ```config.authKey``` propriedades. Se você já tiver dados que você gostaria de armazenar no seu banco de dados, você pode usar [ferramenta de migração de dados](documentdb-import-data.md) em da DocumentDB vez de adicionar as definições de documento.

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };


O banco de dados, o conjunto e definições de documento irá atuar como seu DocumentDB ```database id```, ```collection id```e os dados dos documentos.

Finalmente, exportar seu ```config``` objeto, de modo que você pode fazer referência a ela dentro do ```app.js``` arquivo.

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

##<a id="Connect"></a>Etapa 4: Conectar a uma conta de DocumentDB

Abra seu vazio ```app.js``` arquivo no editor de texto. Copie e cole o código abaixo para importar os ```documentdb``` módulo e seu recém-criado ```config``` módulo.

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Copie e cole o código para usar a versão salva anteriormente ```config.endpoint``` e ```config.primaryKey``` para criar um novo DocumentClient.

    var config = require("./config");
    var url = require('url');

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

Agora que você tem o código para inicializar o cliente documentdb, vamos dar uma olhada em trabalhando com recursos de DocumentDB.

## <a name="step-5-create-a-node-database"></a>Etapa 5: Criar um banco de dados de nó
Copie e cole o código a seguir para definir o status HTTP para não encontrado, a url do banco de dados e a url do conjunto. Essas urls são como o cliente DocumentDB encontrará o banco de dados à direita e a coleção.

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

Um [banco de dados](documentdb-resources.md#databases) pode ser criados usando a função [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) da classe **DocumentClient** . Um banco de dados é o contêiner lógico de particionado entre conjuntos de armazenamento de documentos.

Copiar e colar a função **getDatabase** para a criação de seu novo banco de dados no arquivo app.js com a ```id``` especificado na ```config``` objeto. A função verificará se o banco de dados com o mesmo ```FamilyRegistry``` id já não existe. Se ele existir, podemos vai retornar esse banco de dados em vez de criar um novo.

    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    // ADD THIS PART TO YOUR CODE
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Copie e cole o código abaixo de onde você definir a função **getDatabase** para adicionar o auxiliar função **Sair** que irá imprimir a mensagem de saída e a chamada para a função **getDatabase** .

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No terminal, localize seu ```app.js``` de arquivo e execute o comando:```node app.js```

Parabéns! Você criou com êxito um banco de dados de DocumentDB.

##<a id="CreateColl"></a>Etapa 6: Criar uma coleção  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** irá criar um novo conjunto, o que tem implicações de preços. Para obter mais detalhes, visite nossa [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).

Uma [coleção](documentdb-resources.md#collections) pode ser criados usando a função [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) da classe **DocumentClient** . Uma coleção é um contêiner de documentos JSON e lógica de aplicativo JavaScript associada.

Copiar e colar a função **getCollection** sob a função de **getDatabase** para criar sua nova coleção com o ```id``` especificado na ```config``` objeto. Novamente, vamos verificar se para certificar de que um conjunto com o mesmo ```FamilyCollection``` id já não existe. Se ele existir, podemos vai retornar esse conjunto em vez de criar um novo.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Copie e cole o código abaixo a chamada para **getDatabase** para executar a função **getCollection** .

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No terminal, localize seu ```app.js``` de arquivo e execute o comando:```node app.js```

Parabéns! Você criou um conjunto de DocumentDB com êxito.

##<a id="CreateDoc"></a>Etapa 7: Criar um documento
Um [documento](documentdb-resources.md#documents) pode ser criado usando a função [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) da classe **DocumentClient** . Os documentos são (aleatório) conteúdo JSON definidas pelo usuário. Agora você pode inserir um documento em DocumentDB.

Copiar e colar a função **getFamilyDocument** sob a função de **getCollection** para criar os documentos que contém os dados JSON salvos no ```config``` objeto. Novamente, veremos para certificar-se de que um documento com a mesma id já não existe.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Copie e cole o código abaixo a chamada para **getCollection** para executar a função **getFamilyDocument** .

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No terminal, localize seu ```app.js``` de arquivo e execute o comando:```node app.js```

Parabéns! Você criou com êxito um documentos DocumentDB.

![Banco de dados do nó - diagrama ilustrando a relação hierárquica entre a conta, o banco de dados, o conjunto e os documentos - tutorial Node](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>Etapa 8: Recursos de consulta DocumentDB

DocumentDB oferece suporte a [consultas avançadas](documentdb-sql-query.md) em JSON documentos armazenados em cada conjunto. O código de exemplo a seguir mostra uma consulta que você pode executar contra os documentos em seu conjunto.

Copie e cole a função **queryCollection** sob a função **getFamilyDocument** . DocumentDB suporta consultas SQL, conforme mostrado abaixo. Para obter mais informações sobre a criação de consultas complexas, confira o [Espaço de consulta](https://www.documentdb.com/sql/demo) e a [documentação da consulta](documentdb-sql-query.md).

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };


O diagrama a seguir ilustra como a sintaxe de consulta SQL DocumentDB é chamada em relação à coleção que você criou.

![Banco de dados do nó - diagrama ilustrando o escopo e significado da consulta - tutorial Node](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

A palavra-chave [FROM](documentdb-sql-query.md#from-clause) é opcional na consulta, pois já têm como escopo DocumentDB consultas para um único conjunto. Portanto, "De famílias f" pode ser trocado por "De raiz r" ou qualquer outra variável nome que você escolher. DocumentDB irá inferir que famílias, raiz ou o nome da variável escolhido, referência à coleção atual por padrão.

Copie e cole o código abaixo a chamada para **getFamilyDocument** para executar a função **queryCollection** .

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No terminal, localize seu ```app.js``` de arquivo e execute o comando:```node app.js```

Parabéns! Você tiver consultado com êxito DocumentDB documentos.

##<a id="ReplaceDocument"></a>Etapa 9: Substituir um documento
DocumentDB oferece suporte a substituição JSON documentos.

Copie e cole a função **replaceDocument** sob a função **queryCollection** .

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Replacing document:\n${document.id}\n`);
        document.children[0].grade = 6;

        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copie e cole o código abaixo a chamada para **queryCollection** para executar a função **replaceDocument** . Além disso, adicione o código para chamar **queryCollection** novamente para confirmar que o documento foi alterado com êxito.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No terminal, localize seu ```app.js``` de arquivo e execute o comando:```node app.js```

Parabéns! Você tiver substituído com êxito um documento DocumentDB.

##<a id="DeleteDocument"></a>Etapa 10: Excluir um documento
DocumentDB suporta excluir documentos JSON.

Copie e cole a função **deleteDocument** sob a função **replaceDocument** .

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Deleting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copie e cole o código abaixo a chamada para a segunda **queryCollection** para executar a função **deleteDocument** .

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No terminal, localize seu ```app.js``` de arquivo e execute o comando:```node app.js```

Parabéns! Você excluiu com êxito um documento DocumentDB.

##<a id="DeleteDatabase"></a>Etapa 11: Excluir o banco de dados de nó

Excluir o banco de dados criado removerá o banco de dados e todos os recursos de filhos (coleções, documentos, etc.).

Copie e cole o seguinte trecho de código (função de **Limpeza**) para remover o banco de dados e todos os recursos de filhos.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

Copie e cole o código abaixo a chamada para **deleteDocument** para executar a função de **Limpeza** .

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

##<a id="Run"></a>Etapa 12: Executar seu aplicativo Node todos junto!

Completamente, a sequência para chamar as funções deve ficar assim:

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No terminal, localize seu ```app.js``` de arquivo e execute o comando:```node app.js```

Você deve ver a saída de seu aplicativo iniciado get. A saída deve coincidir com o texto de exemplo abaixo.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

Parabéns! Você criou você concluiu o tutorial node e ter seu aplicativo de console DocumentDB primeiro!

## <a id="GetSolution"></a>Obtenha a solução de tutorial Node completa
Para criar a solução de GetStarted que contém todos os exemplos deste artigo, você precisará do seguinte:

-   [Conta de DocumentDB][documentdb-create-account].
-   A solução de [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) disponível no GitHub.

Instale o módulo **documentdb** via npm. Use o seguinte comando:
* ```npm install documentdb --save```

Avançar, na ```config.js``` de arquivos, atualize os valores config.endpoint e config.authKey, conforme descrito na [etapa 3: definir as configurações do seu aplicativo](#Config).

## <a name="next-steps"></a>Próximas etapas

-   Quer uma amostra Node mais complexa? Consulte [criar um aplicativo web do Node usando DocumentDB](documentdb-nodejs-application.md).
-  Saiba como [monitorar uma conta de DocumentDB](documentdb-monitor-accounts.md).
-  Execute consultas em nosso conjunto de dados de exemplo no [Espaço de consulta](https://www.documentdb.com/sql/demo).
-  Saiba mais sobre o modelo de programação na seção desenvolver da [página de documentação DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png
