<properties 
    pageTitle="Programação de DocumentDB: procedimentos armazenados, disparadores de banco de dados e UDFs | Microsoft Azure" 
    description="Saiba como usar DocumentDB para gravar procedimentos armazenados, disparadores de banco de dados e funções definidas pelo usuário (UDFs) em JavaScript. Obtenha dicas de programação de banco de dados e muito mais." 
    keywords="Gatilhos de banco de dados, procedimento armazenado, procedimento armazenado, programa de banco de dados, sproc, documentdb, azure, do Microsoft azure"
    services="documentdb" 
    documentationCenter="" 
    authors="aliuy" 
    manager="jhubbard" 
    editor="mimig"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="andrl"/>

# <a name="documentdb-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Programação de servidor de DocumentDB: procedimentos armazenados, disparadores de banco de dados e UDFs

Saiba como o idioma do Azure DocumentDB integrado, transações execução do JavaScript permite que os desenvolvedores escrever **procedimentos armazenados**, **disparadores** e **funções (UDFs) definidas pelo usuário** nativamente em JavaScript. Isso permite que você escrever lógica de aplicativo do programa de banco de dados que pode ser enviada e executada diretamente nas partições de armazenamento de banco de dados 

Recomendamos Introdução assistindo vídeo a seguir, onde Andrew Liu fornece uma breve introdução ao modelo de programação de banco de dados do lado do servidor do DocumentDB. 

> [AZURE.VIDEO azure-demo-a-quick-intro-to-azure-documentdbs-server-side-javascript]

Em seguida, retorne neste artigo, onde você vai aprender as respostas para as seguintes perguntas:  

- Como escrever um um procedimento armazenado, um disparador ou UDF usando JavaScript?
- Como o DocumentDB garante ACID?
- Como funcionam as transações na DocumentDB?
- O que são previamente aciona e posteriores à aciona e como escrever um?
- Como registrar e executar um procedimento armazenado, um disparador ou UDF de maneira RESTful usando HTTP?
- O que DocumentDB SDKs estão disponíveis para criar e executar procedimentos armazenados, disparadores e UDFs?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Introdução ao procedimento armazenado e programação de UDF

Essa abordagem de *"JavaScript como um dia moderno T-SQL"* libera os desenvolvedores de aplicativos das complexidades de diferenças de sistema de tipo e tecnologias de mapeamento relacional de objeto. Ele também possui um número de vantagens intrínsecas que podem ser usados para criar aplicativos sofisticados:  

-   **Lógica de procedimento:** JavaScript como um alto nível de linguagem de programação, fornece uma interface avançada e familiar para expressar lógica comercial. Você pode executar as sequências complexas de operações aproximar os dados.

-   **Transações atômicas:** DocumentDB garante que operações de banco de dados executadas dentro de um único procedimento armazenado ou disparador atômica. Isso permite que um aplicativo combine operações relacionadas em um único lote para que todos eles êxito ou nenhuma delas tenha êxito. 

-   **Desempenho:** O fato JSON intrinsecamente é mapeado para o sistema de tipo de idioma do Javascript e também é a unidade básica de armazenamento no DocumentDB permite que um número de otimizações como preguiça materialização de documentos JSON no pool de buffer e torná-los disponível sob demanda ao código em execução. Há mais benefícios de desempenho associados à remessa lógica de negócios para o banco de dados:
    -   Processamento em lotes – os desenvolvedores podem agrupar operações como inserções e enviá-los em massa. A latência de tráfego de rede de custo e a sobrecarga de armazenamento para criar transações separadas estão reduzidos significativamente. 
    -   Pré-compilação – DocumentDB pré-compila procedimentos armazenados, disparadores e funções definidas pelo usuário (UDFs) para evitar o custo de compilação de JavaScript para cada chamada. A sobrecarga de criar o código de bytes para a lógica de procedimento é amortizada ao valor mínimo.
    -   Sequência – vários precisam de operações um lado efeito ("disparadores") que envolve potencialmente fazendo uma ou várias operações de repositório secundário. Com exceção de atomicidade, isso é mais alto desempenho quando movidos para o servidor. 
-   **Encapsulamento:** Procedimentos armazenados podem ser usados para agrupar lógica de negócios em um só lugar. Isso tem duas vantagens:
    -   Ele adiciona uma camada de abstração sobre os dados processados, que permite que arquitetos de dados a evoluir seus aplicativos independentemente dos dados. Isso é particularmente vantajoso quando os dados são sem esquema, devido as suposições frágil que talvez precise ser implantadas no aplicativo se tiverem que lidar com dados diretamente.  
    -   Essa abstração permite empresas a manter seus dados seguros simplificando o acesso a partir dos scripts.  

A criação e a execução de disparadores de banco de dados, procedimento armazenado e operadores de consulta personalizada é suportado através do [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx), [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)e [cliente SDKs](documentdb-sdk-dotnet.md) em várias plataformas, incluindo .NET, node e JavaScript.

**Que neste tutorial usa o [SDK do Node com promessas de Q](http://azure.github.io/azure-documentdb-node-q/) ** para ilustrar a sintaxe e o uso de procedimentos armazenados, disparadores e UDFs.   

## <a name="stored-procedures"></a>Procedimentos armazenados

### <a name="example-write-a-simple-stored-procedure"></a>Exemplo: Escrever um procedimento armazenado simples 
Vamos começar com um procedimento armazenado simple que retorna uma resposta de "Olá, mundo".

    var helloWorldStoredProc = {
        id: "helloWorld",
        body: function () {
            var context = getContext();
            var response = context.getResponse();
    
            response.setBody("Hello, World");
        }
    }


Procedimentos armazenados são registrados por conjunto e podem operar em qualquer documento e anexo presente nessa coleção. O trecho a seguir mostra como registrar o procedimento helloWorld armazenado com um conjunto. 

    // register the stored procedure
    var createdStoredProcedure;
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
        .then(function (response) {
            createdStoredProcedure = response.resource;
            console.log("Successfully created stored procedure");
        }, function (error) {
            console.log("Error", error);
        });


Quando o procedimento armazenado estiver registrado, podemos executá-lo em relação à coleção e ler os resultados de volta no cliente. 

    // execute the stored procedure
    client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
        .then(function (response) {
            console.log(response.result); // "Hello, World"
        }, function (err) {
            console.log("Error", error);
        });


O objeto de contexto fornece acesso a todas as operações que podem ser realizadas em DocumentDB armazenamento, bem como o acesso aos objetos da solicitação e resposta. Nesse caso, usamos o objeto de resposta para definir o corpo de resposta que foi enviada para o cliente. Para obter mais detalhes, consulte o [servidor de DocumentDB JavaScript documentação SDK](http://azure.github.io/azure-documentdb-js-server/).  

Vamos expandir este exemplo e adicione que mais funcionalidade relacionada ao procedimento armazenado de banco de dados. Procedimentos armazenados podem criar, atualizar, leia, consultar e excluir documentos e anexos dentro do conjunto.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Exemplo: Escrever um procedimento armazenado para criar um documento 
O próximo trecho mostra como usar o objeto de contexto para interagir com os recursos de DocumentDB.

    var createDocumentStoredProc = {
        id: "createMyDocument",
        body: function createMyDocument(documentToCreate) {
            var context = getContext();
            var collection = context.getCollection();

            var accepted = collection.createDocument(collection.getSelfLink(),
                  documentToCreate,
                  function (err, documentCreated) {
                      if (err) throw new Error('Error' + err.message);
                      context.getResponse().setBody(documentCreated.id)
                  });
            if (!accepted) return;
        }
    }


Esse procedimento armazenado aceita como entrada documentToCreate, o corpo de um documento a ser criado na coleção atual. Todas essas operações são assíncronas e dependem retornos de chamada de função JavaScript. A função de retorno de chamada tem dois parâmetros, um para o objeto de erro caso que a operação falhará e uma para o objeto criado. Dentro de retorno de chamada, os usuários podem tratar a exceção ou acionar um erro. No caso de um retorno de chamada não for fornecido e há um erro, o tempo de execução de DocumentDB gera um erro.   

No exemplo acima, o retorno de chamada gera um erro se a operação falhou. Caso contrário, ele define o id do documento criado como o corpo da resposta para o cliente. Veja aqui como esse procedimento armazenado é executado com parâmetros de entrada.

    // register the stored procedure
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', createDocumentStoredProc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
    
            // run stored procedure to create a document
            var docToCreate = {
                id: "DocFromSproc",
                book: "The Hitchhiker’s Guide to the Galaxy",
                author: "Douglas Adams"
            };
    
            return client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/createMyDocument',
                  docToCreate);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response); // "DocFromSproc"
    }, function (error) {
        console.log("Error", error);
    });

    
Observe que este procedimento armazenado pode ser modificado para tirar uma matriz de corpos de documento como entrada e criá-los na mesma execução do procedimento armazenado em vez de várias solicitações de rede para criar cada um deles individualmente. Isso pode ser usado para implementar um importador de massa eficiente para DocumentDB (discutida posteriormente neste tutorial).   

O exemplo descrito demonstrou como usar procedimentos armazenados. Abordaremos disparadores e funções definidas pelo usuário (UDFs) posteriormente no tutorial.

## <a name="database-program-transactions"></a>Transações do programa de banco de dados
Transação em um banco de dados típica pode ser definida como uma sequência de operações executadas como uma única unidade lógica de trabalho. Cada transação fornece **ACID garante**. ACID é um acrônimo conhecido que significa quatro propriedades - atomicidade, consistência, isolamento e durabilidade.  

Resumidamente, atomicidade garante que todo o trabalho feito dentro de uma transação será tratado como uma unidade única onde qualquer tudo isso está comprometida ou nenhum. Consistência garante que os dados sejam sempre em bom estado interno em transações. Isolamento garante que nenhum duas transações interferem uns com os outros – geralmente, sistemas mais comerciais oferecem vários níveis de isolamento que podem ser usados com base nas necessidades aplicativo. Durabilidade garante que qualquer alteração confirmada no banco de dados sempre esteja presente.   

Em DocumentDB, JavaScript está hospedado no mesmo espaço de memória do banco de dados. Portanto, solicitações feitas em procedimentos armazenados e disparadores executam no mesmo escopo de uma sessão de banco de dados. Isso permite DocumentDB garantir ÁCIDO para todas as operações que fazem parte de um único armazenado procedimento/disparador. Considere a seguinte definição de procedimento armazenado:

    // JavaScript source code
    var exchangeItemsSproc = {
        name: "exchangeItems",
        body: function (playerId1, playerId2) {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();
    
            var player1Document, player2Document;
    
            // query for players
            var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
                function (err, documents, responseOptions) {
                    if (err) throw new Error("Error" + err.message);
    
                    if (documents.length != 1) throw "Unable to find both names";
                    player1Document = documents[0];
    
                    var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
                    var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
                        function (err2, documents2, responseOptions2) {
                            if (err2) throw new Error("Error" + err2.message);
                            if (documents2.length != 1) throw "Unable to find both names";
                            player2Document = documents2[0];
                            swapItems(player1Document, player2Document);
                            return;
                        });
                    if (!accept2) throw "Unable to read player details, abort ";
                });
    
            if (!accept) throw "Unable to read player details, abort ";
    
            // swap the two players’ items
            function swapItems(player1, player2) {
                var player1ItemSave = player1.item;
                player1.item = player2.item;
                player2.item = player1ItemSave;
    
                var accept = collection.replaceDocument(player1._self, player1,
                    function (err, docReplaced) {
                        if (err) throw "Unable to update player 1, abort ";
    
                        var accept2 = collection.replaceDocument(player2._self, player2,
                            function (err2, docReplaced2) {
                                if (err) throw "Unable to update player 2, abort"
                            });
    
                        if (!accept2) throw "Unable to update player 2, abort";
                    });
    
                if (!accept) throw "Unable to update player 1, abort";
            }
        }
    }
    
    // register the stored procedure in Node.js client
    client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
        }
    );

Esse procedimento armazenado usa transações dentro de um aplicativo de jogos para itens de comércio entre dois jogadores em uma única operação. O procedimento armazenado tenta ler dois documentos cada correspondente ao IDs de player passado como um argumento. Se os dois documentos player forem encontrados, o procedimento armazenado atualiza os documentos, trocando seus itens. Se os erros forem encontrados durante o percurso, ele gera uma exceção de JavaScript que implicitamente anula a transação.

Se o conjunto de procedimento armazenado está registrado contra é uma coleção de partição única, a transação é delimitada para todos os docuemnts dentro do conjunto. Se a coleção estiver particionada, procedimentos armazenados são executados no escopo de transação de uma chave de partição única. Cada uma execução do procedimento deverá incluir um valor de chave de partição correspondente ao escopo a transação deve ser executado em armazenada. Para obter mais detalhes, consulte [DocumentDB partição](documentdb-partition-data.md).

### <a name="commit-and-rollback"></a>Confirmação e reversão
Transações são profundamente e nativamente integradas modelo de programação do DocumentDB JavaScript. Dentro de uma função de JavaScript, todas as operações automaticamente dispostas em uma única transação. Se o JavaScript for concluída sem qualquer exceção, as operações no banco de dados são confirmadas. Na verdade, as instruções de "Transação de começar" e "Confirmar transação" em bancos de dados relacionais são implícitas no DocumentDB.  
 
Se houver qualquer exceção que é propagada do script, tempo de execução de JavaScript do DocumentDB reverterá a transação inteira. Como mostrado no exemplo anterior, lançar uma exceção é efetivamente equivalente a uma "transação de REVERSÃO" em DocumentDB.
 
### <a name="data-consistency"></a>Consistência de dados
Procedimentos armazenados e disparadores sempre são executados na réplica primária da coleção DocumentDB. Isso garante que leituras de dentro armazenados procedimentos oferta forte consistência. Consultas usando funções definidas pelo usuário podem ser executadas no principal ou qualquer réplica secundária, mas podemos garantir para atender o nível de consistência solicitada, escolhendo a réplica apropriada.

## <a name="bounded-execution"></a>Execução limitada
Todas as operações de DocumentDB devem ser concluídas dentro do servidor especificado solicitar a duração do tempo limite. Essa restrição também se aplica às funções de JavaScript (procedimentos armazenados, disparadores e funções definidas pelo usuário). Se uma operação não é concluído com esse limite de tempo, a transação será revertida. Funções JavaScript devem terminar dentro do limite de tempo ou implementar um modelo de continuação com base para execução de lote/currículo.  

Para simplificar o desenvolvimento de procedimentos armazenados e disparadores para lidar com limites de tempo, todas as funções sob o objeto da coleção (para criar, ler, substituir e excluir de documentos e anexos) retornam um valor booliano que indica se a operação será concluída. Se esse valor for falso, é uma indicação de que o limite de tempo estiver prestes a expirar e que o procedimento deve resumir execução.  Há garantia concluir se o procedimento armazenado conclui em tempo e não na fila mais solicitações de operações enfileirada prévia para a primeira operação de armazenamento não aceito.  

Funções JavaScript também são limitadas no consumo de recursos. DocumentDB reserva produtividade por conjunto baseado no tamanho provisionado de uma conta de banco de dados. Produtividade é expressa em termos de uma unidade normalizada de CPU, memória e consumo de IO chamada unidades de solicitação ou RUs. Funções JavaScript potencialmente podem utilizar um grande número de RUs dentro de uma hora abreviada e podem receber taxa limitada se limite da coleção for atingido. Procedimentos armazenados intenso do recurso também podem colocar em quarentena para garantir a disponibilidade das operações de banco de dados primitivo.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Exemplo: Em massa importando dados para um programa de banco de dados
Abaixo está um exemplo de um procedimento armazenado que é escrito para importação em massa-documentos em um conjunto. Observe como o procedimento armazenado lida com a execução limitada, verificando o booliano valor de retorno de createDocument e usa a contagem de documentos inseridos em cada invocação do procedimento armazenado para controlar e retomar andamento em lotes.

    function bulkImport(docs) {
        var collection = getContext().getCollection();
        var collectionLink = collection.getSelfLink();
    
        // The count of imported docs, also used as current doc index.
        var count = 0;
    
        // Validate input.
        if (!docs) throw new Error("The array is undefined or null.");
    
        var docsLength = docs.length;
        if (docsLength == 0) {
            getContext().getResponse().setBody(0);
        }
    
        // Call the create API to create a document.
        tryCreate(docs[count], callback);
    
        // Note that there are 2 exit conditions:
        // 1) The createDocument request was not accepted. 
        //    In this case the callback will not be called, we just call setBody and we are done.
        // 2) The callback was called docs.length times.
        //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
        function tryCreate(doc, callback) {
            var isAccepted = collection.createDocument(collectionLink, doc, callback);
    
            // If the request was accepted, callback will be called.
            // Otherwise report current count back to the client, 
            // which will call the script again with remaining set of docs.
            if (!isAccepted) getContext().getResponse().setBody(count);
        }
    
        // This is called when collection.createDocument is done in order to process the result.
        function callback(err, doc, options) {
            if (err) throw err;
    
            // One more document has been inserted, increment the count.
            count++;
    
            if (count >= docsLength) {
                // If we created all documents, we are done. Just set the response.
                getContext().getResponse().setBody(count);
            } else {
                // Create next document.
                tryCreate(docs[count], callback);
            }
        }
    }

## <a id="trigger"></a>Gatilhos de banco de dados
### <a name="database-pre-triggers"></a>Gatilhos de pré-lançamento do banco de dados
DocumentDB fornece disparadores que são executados ou acionados por uma operação em um documento. Por exemplo, você pode especificar um pré-disparador quando você estiver criando um documento – esse pré-disparador serão executadas antes do documento é criado. Este é um exemplo de como pré-disparadores podem ser usados para validar as propriedades de um documento que está sendo criada:

    var validateDocumentContentsTrigger = {
        name: "validateDocumentContents",
        body: function validate() {
            var context = getContext();
            var request = context.getRequest();
    
            // document to be created in the current operation
            var documentToCreate = request.getBody();
    
            // validate properties
            if (!("timestamp" in documentToCreate)) {
                var ts = new Date();
                documentToCreate["my timestamp"] = ts.getTime();
            }
    
            // update the document that will be created
            request.setBody(documentToCreate);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.Create
    }


E o código de registro de cliente Node correspondente para o disparador:

    // register pre-trigger
    client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
        .then(function (response) {
            console.log("Created", response.resource);
            var docToCreate = {
                id: "DocWithTrigger",
                event: "Error",
                source: "Network outage"
            };
    
            // run trigger while creating above document 
            var options = { preTriggerInclude: "validateDocumentContents" };
    
            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response.resource); // document with timestamp property added
    }, function (error) {
        console.log("Error", error);
    });


Pré-disparadores não podem ter os parâmetros de entrada. O objeto de solicitação pode ser usado para manipular a mensagem de solicitação associada a operação. Aqui, o pré-disparador está sendo executado com a criação de um documento, e o corpo da mensagem de solicitação contém o documento a ser criado no formato JSON.   

Quando disparadores são registrados, os usuários podem especificar as operações que ele pode ser executado com. Esse disparador foi criado com TriggerOperation.Create, o que significa que a seguir não é permitida.

    var options = { preTriggerInclude: "validateDocumentContents" };
    
    client.replaceDocumentAsync(docToReplace.self,
                  newDocBody, options)
    .then(function (response) {
        console.log(response.resource);
    }, function (error) {
        console.log("Error", error);
    });
    
    // Fails, can’t use a create trigger in a replace operation

### <a name="database-post-triggers"></a>Gatilhos de POST de banco de dados
POST disparadores, como pré-disparadores, estão associados a uma operação em um documento e não levam os parâmetros de entrada. Eles executado **após** que a conclusão da operação e tem acesso à mensagem de resposta que é enviada para o cliente.   

O exemplo a seguir mostra disparadores POST em ação:

    var updateMetadataTrigger = {
        name: "updateMetadata",
        body: function updateMetadata() {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();
    
            // document that was created
            var createdDocument = response.getBody();
    
            // query for metadata document
            var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
                updateMetadataCallback);
            if(!accept) throw "Unable to update metadata, abort";
     
            function updateMetadataCallback(err, documents, responseOptions) {
                if(err) throw new Error("Error" + err.message);
                         if(documents.length != 1) throw 'Unable to find metadata document';
                         
                         var metadataDocument = documents[0];
                         
                         // update metadata
                         metadataDocument.createdDocuments += 1;
                         metadataDocument.createdNames += " " + createdDocument.id;
                         var accept = collection.replaceDocument(metadataDocument._self,
                               metadataDocument, function(err, docReplaced) {
                                      if(err) throw "Unable to update metadata, abort";
                               });
                         if(!accept) throw "Unable to update metadata, abort";
                         return;                    
            }                                                                                           
        },
        triggerType: TriggerType.Post,
        triggerOperation: TriggerOperation.All
    }


O disparador pode ser registrado, conforme mostrado no exemplo a seguir.

    // register post-trigger
    client.createTriggerAsync('dbs/testdb/colls/testColl', updateMetadataTrigger)
        .then(function(createdTrigger) { 
            var docToCreate = { 
                name: "artist_profile_1023",
                artist: "The Band",
                albums: ["Hellujah", "Rotators", "Spinning Top"]
            };
    
            // run trigger while creating above document 
            var options = { postTriggerInclude: "updateMetadata" };
        
            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });


Esse disparador consulta para o documento de metadados e atualiza com detalhes sobre o documento recém-criado.  

Uma coisa que é importante observar é a execução de **transações** de gatilhos no DocumentDB. Esse disparador POST é executado como parte da mesma transação a criação do documento original. Portanto, se podemos lançar uma exceção do disparador POST (digamos se não é possível atualizar o documento de metadados), toda a transação falhará e ser revertida. Nenhum documento será criado e uma exceção será retornada.  

##<a id="udf"></a>Funções definidas pelo usuário
Funções definidas pelo usuário (UDFs) são usadas para estender a gramática de linguagem de consulta DocumentDB SQL e implementar a lógica comercial personalizada. Só pode ser chamados de dentro de consultas. Eles não têm acesso ao objeto de contexto e destinam-se a ser usado como JavaScript somente computação. Portanto, UDFs podem ser executados em réplicas secundárias do serviço DocumentDB.  
 
O exemplo a seguir cria um UDF para calcular o imposto de renda com base em taxas para vários suportes de receita e usa dentro de uma consulta para localizar todas as pessoas que paga mais de US $20.000 em impostos.

    var taxUdf = {
        name: "tax",
        body: function tax(income) {
    
            if(income == undefined) 
                throw 'no input';
    
            if (income < 1000) 
                return income * 0.1;
            else if (income < 10000) 
                return income * 0.2;
            else
                return income * 0.4;
        }
    }


O UDF subsequentemente pode ser usado em consultas como no exemplo a seguir:

    // register UDF
    client.createUserDefinedFunctionAsync('dbs/testdb/colls/testColl', taxUdf)
        .then(function(response) { 
            console.log("Created", response.resource);
    
            var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
            return client.queryDocuments('dbs/testdb/colls/testColl',
                   query).toArrayAsync();
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        var documents = response.feed;
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });

## <a name="javascript-language-integrated-query-api"></a>Consulta integrada à linguagem JavaScript API
Além de emitir consultas usando gramática SQL do DocumentDB, o SDK do servidor permite executar consultas otimizadas usando uma interface fluent do JavaScript sem qualquer conhecimento de SQL. A consulta de JavaScript API permite que você programaticamente criar consultas passando funções predicadas em função chainable chama, com uma sintaxe familiar para elementos internos de matriz e bibliotecas populares do JavaScript como lodash do ECMAScript5. Consultas estão analisadas pelo runtime JavaScript a ser executada com eficiência usando índices do DocumentDB.

> [AZURE.NOTE]`__` (sublinhado duplo) é um alias para `getContext().getCollection()`.
> <br/>
> Em outras palavras, você pode usar `__` ou `getContext().getCollection()` para acessar a API de consulta do JavaScript.

Funções com suporte incluem:
<ul>
<li>
<b>Chain().... valor ([retorno de chamada] [, opções])</b>
<ul>
<li>
Inicia uma chamada encadeada que deve ser terminada com Value ().
</li>
</ul>
</li>
<li>
<b>filtro (predicateFunction [, opções] [, retorno de chamada])</b>
<ul>
<li>
Filtra a entrada usando uma função de predicado que retorna verdadeiro/falso para filtrar dentro/fora documentos de entrada para o conjunto resultante. Isso se comporta semelhante a uma cláusula WHERE no SQL.
</li>
</ul>
</li>
<li>
<b>mapa (transformationFunction [, opções] [, retorno de chamada])</b>
<ul>
<li>
Aplica uma projeção fornecida uma função de transformação que mapeia cada item de entrada para um objeto JavaScript ou valor. Isso se comporta semelhante a uma cláusula SELECT em SQL.
</li>
</ul>
</li>
<li>
<b>Pluck ([propertyName] [, opções] [, retorno de chamada])</b>
<ul>
<li>
Este é um atalho para um mapa que extrai o valor de uma única propriedade de cada item de entrada.
</li>
</ul>
</li>
<li>
<b>Nivelar ([isShallow] [, opções] [, retorno de chamada])</b>
<ul>
<li>
Combina e nivela matrizes de cada item entrada a uma única matriz. Isso se comporta semelhante ao SelectMany em LINQ.
</li>
</ul>
</li>
<li>
<b>sortBy ([predicado] [, opções] [, retorno de chamada])</b>
<ul>
<li>
Produza um novo conjunto de documentos classificando os documentos no fluxo de documento de entrada em ordem usando o predicado determinado crescente. Isso se comporta semelhante a uma cláusula ORDER BY no SQL.
</li>
</ul>
</li>
<li>
<b>sortByDescending ([predicado] [, opções] [, retorno de chamada])</b>
<ul>
<li>
Produza um novo conjunto de documentos classificando os documentos no fluxo de documento de entrada em ordem usando o predicado determinado decrescente. Isso se comporta semelhante a uma cláusula ORDER BY x DESC no SQL.
</li>
</ul>
</li>
</ul>


Quando incluído dentro predicado e/ou o seletor de funções, as seguintes construções de JavaScript automaticamente obtém otimizadas para executar diretamente em índices de DocumentDB:

* Operadores simples: = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Literais, incluindo o objeto literal: {}
* var, retorno

As seguintes construções de JavaScript não obter otimizadas para índices de DocumentDB:

* Fluxo de controle (por exemplo, se, enquanto)
* Chamadas de função

Para obter mais informações, consulte nosso [JSDocs de servidor](http://azure.github.io/azure-documentdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Exemplo: Escrever um procedimento armazenado usando a API de consulta do JavaScript

O exemplo de código a seguir é um exemplo de como a API de consulta JavaScript pode ser usada no contexto de um procedimento armazenado. O procedimento armazenado insere um documento, fornecido por um parâmetro de entrada e atualiza um metadados do documento, usando o `__.filter()` método, com minSize, maxSize e totalSize com base na propriedade de tamanho de entrada do documento.

    /**
     * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
     */
    function insertDocumentAndUpdateMetadata(doc) {
      // HTTP error codes sent to our callback funciton by DocDB server.
      var ErrorCode = {
        RETRY_WITH: 449,
      }

      var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
        if (err) throw err;

        // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
        if (!doc.isMetadata && doc.size > 0) {
          // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
          var result = __.filter(function(x) {
            return x.isMetadata === true
          }, function(err, feed, options) {
            if (err) throw err;

            // We assume that metadata doc was pre-created and must exist when this script is called.
            if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

            // The metadata document.
            var metaDoc = feed[0];

            // Update metaDoc.minSize:
            // for 1st document use doc.Size, for all the rest see if it's less than last min.
            if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
            else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

            // Update metaDoc.maxSize.
            metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

            // Update metaDoc.totalSize.
            metaDoc.totalSize += doc.size;

            // Update/replace the metadata document in the store.
            var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
              if (err) throw err;
              // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
              //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
              //       We have to take care of that on the client side.
            });
            if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
          });
          if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
        }
      });
      if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
    }

## <a name="sql-to-javascript-cheat-sheet"></a>SQL a planilha de enganar Javascript
A tabela a seguir apresenta várias consultas SQL e as consultas de JavaScript correspondentes.

Como com consultas SQL, chaves de propriedade do documento (por exemplo, `doc.id`) diferenciam maiusculas de minúsculas.

<br/>
<table border="1" width="100%">
<colgroup>
<col span="1" style="width: 40%;">
<col span="1" style="width: 40%;">
<col span="1" style="width: 20%;">
</colgroup>
<tbody>
<tr>
<th>SQL</th>
<th>API de consulta de JavaScript</th>
<th>Detalhes</th>
</tr>
<tr>
<td>
<pre>
Selecionar * de documentos
</pre>
</td>
<td>
<pre>
__.map(Function(doc) {retorno doc;});
</pre>
</td>
<td>Resultados em todos os documentos (paginados com token de continuação) como está.</td>
</tr>
<tr>
<td>
<pre>
Selecione docs.id, docs.message como msg, docs.actions de documentos
</pre>
</td>
<td>
<pre>
__.map(Function(doc) {retornar {id: doc.id, msg: doc.message, ações: doc.actions};});
</pre>
</td>
<td>O id, uma mensagem (alias de msg) e uma ação de todos os documentos de projetos.</td>
</tr>
<tr>
<td>
<pre>
Selecionar * de documentos onde docs.id="X998_Y998"
</pre>
</td>
<td>
<pre>
__.Filter(Function(doc) {retornar doc.id === "X998_Y998;"});
</pre>
</td>
<td>Consultas para documentos com o predicado: id = "X998_Y998".</td>
</tr>
<tr>
<td>
<pre>
Selecionar * de documentos onde ARRAY_CONTAINS(docs. Marcas, 123)
</pre>
</td>
<td>
<pre>
__.Filter(Function(x) {retornar x.Tags & & x.Tags.indexOf(123) > -1;});
</pre>
</td>
<td>Consultas para documentos que têm uma propriedade de marcas e marcas é uma matriz que contém o valor 123.</td>
</tr>
<tr>
<td>
<pre>
Selecione docs.id, docs.message como documentos FROM msg onde docs.id="X998_Y998"
</pre>
</td>
<td>
<pre>
__.Chain() .filter(function(doc) {retornar doc.id === "X998_Y998;"}) .map(function(doc) {retornar {id: doc.id, msg: doc.message};}) .value();
</pre>
</td>
<td>Consultas para documentos com um predicado, id = "X998_Y998" e projetos, em seguida, a id e a mensagem (alias de msg).</td>
</tr>
<tr>
<td>
<pre>
Documentos do Selecionar valor marca de junção marca em documentos. Marcas ORDER BY docs._ts
</pre>
</td>
<td>
<pre>
__.Chain() .filter(function(doc) {doc retorno. Marcas & & Array.isArray (doc. Marcas); .sortBy(function(doc)}) {retorno doc._ts;}) .pluck("tags") .flatten() .value()
</pre>
</td>
<td>Filtros para documentos que têm uma propriedade de matriz, marcas, e classifica os documentos resultantes a propriedade do sistema de carimbo de hora _ts, e em seguida projetos + nivela a matriz de marcas.</td>
</tr>
</tbody>
</table>

## <a name="runtime-support"></a>Suporte de tempo de execução
[Servidor DocumentDB JavaScript SDK](http://azure.github.io/azure-documentdb-js-server/) fornece suporte para a maioria dos recursos do idioma base JavaScript como padronizado por [ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Segurança
JavaScript procedimentos armazenados e disparadores são área restrita para que os efeitos de um script não perda para a outra sem percorrer o isolamento de transação instantâneo no nível do banco de dados. Os ambientes de tempo de execução são agrupados, mas limpo do contexto após cada execução. Portanto, eles são oferecem garantia de segurança de quaisquer efeitos lado não intencionais uns dos outros.

### <a name="pre-compilation"></a>Pré-compilação
Procedimentos armazenados, disparadores e UDFs são implicitamente pré-compilados para o formato de código de bytes para evitar o custo de compilação no momento de cada chamada de script. Isso garante chamadas de procedimentos armazenados são rápidas e tem um espaço baixo.

## <a name="client-sdk-support"></a>Suporte SDK do cliente
Além do cliente [Node](documentdb-sdk-node.md) , DocumentDB compatível com [.NET](documentdb-sdk-dotnet.md), [Java](documentdb-sdk-java.md), [JavaScript](http://azure.github.io/azure-documentdb-js/)e [SDKs Python](documentdb-sdk-python.md). Procedimentos armazenados, disparadores e UDFs podem ser criados e executados usando qualquer um destes SDKs também. O exemplo a seguir mostra como criar e executar um procedimento armazenado usando o cliente .NET. Observe como os tipos de .NET são passados para o procedimento armazenado como JSON e repetidos.

    var markAntiquesSproc = new StoredProcedure
    {
        Id = "ValidateDocumentAge",
        Body = @"
                function(docToCreate, antiqueYear) {
                    var collection = getContext().getCollection();    
                    var response = getContext().getResponse();    
    
                    if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
                        docToCreate.antique = true;
                    }
    
                    collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
                        function(err, docCreated, options) { 
                            if(err) throw new Error('Error while creating document: ' + err.message);                              
                            if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
                            response.setBody(docCreated);
                    });
            }"
    };
    
    // register stored procedure
    StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), markAntiquesSproc);
    dynamic document = new Document() { Id = "Borges_112" };
    document.Title = "Aleph";
    document.Year = 1949;
    
    // execute stored procedure
    Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(UriFactory.CreateStoredProcedureUri("db", "coll", "sproc"), document, 1920);


Este exemplo mostra como usar o [SDK do .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx) para criar um disparador pré e criar um documento com o disparador habilitado. 

    Trigger preTrigger = new Trigger()
    {
        Id = "CapitalizeName",
        Body = @"function() {
            var item = getContext().getRequest().getBody();
            item.id = item.id.toUpperCase();
            getContext().getRequest().setBody(item);
        }",
        TriggerOperation = TriggerOperation.Create,
        TriggerType = TriggerType.Pre
    };
    
    Document createdItem = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), new Document { Id = "documentdb" },
        new RequestOptions
        {
            PreTriggerInclude = new List<string> { "CapitalizeName" },
        });


E o exemplo a seguir mostra como criar uma função definida pelo usuário (UDF) e usá-la em uma [consulta SQL DocumentDB](documentdb-sql-query.md).

    UserDefinedFunction function = new UserDefinedFunction()
    {
        Id = "LOWER",
        Body = @"function(input) 
        {
            return input.toLowerCase();
        }"
    };
    
    foreach (Book book in client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
    {
        Console.WriteLine("Read {0} from query", book);
    }

## <a name="rest-api"></a>API REST
Todas as operações de DocumentDB podem ser executadas de maneira RESTful. Procedimentos armazenados, disparadores e funções definidas pelo usuário podem ser registradas em um conjunto usando HTTP POST. Este é um exemplo de como registrar um procedimento armazenado:

    POST https://<url>/sprocs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    
    
    var x = {
      "name": "createAndAddProperty",
      "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
                var collectionManager = getContext().getCollection();
                collectionManager.createDocument(
                    collectionManager.getSelfLink(),
                    docToCreate,
                    function(err, docCreated) {
                      if(err) throw new Error('Error:  ' + err.message);
                      docCreated[addedPropertyName] = addedPropertyValue;
                      getContext().getResponse().setBody(docCreated);
                    });
            }
    }


O procedimento armazenado está registrado executando uma solicitação POST contra o URI bancos de dados/testdb/colls/testColl/sprocs com o corpo que contém o procedimento armazenado para criar. Disparadores e UDFs podem ser registrados da mesma forma pela emissão uma POSTAGEM contra /disparadores e /udfs respectivamente.
Isso armazenado pode procedimento e ser executado pela emissão uma solicitação POST contra seu link de recurso:

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT
    
    
    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


Aqui, a entrada para o procedimento armazenado é passada no corpo da solicitação. Observe que a entrada é passada como uma matriz JSON de parâmetros de entrada. O procedimento armazenado leva a primeira entrada como um documento que é um corpo de resposta. A resposta que recebemos é da seguinte maneira:

    HTTP/1.1 200 OK
     
    { 
      name: 'TestDocument',
      book: ‘Autumn of the Patriarch’,
      id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
      ts: 1407830727,
      self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
      etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
      attachments: ‘attachments/’,
      Price: 200
    }


Disparadores, ao contrário dos procedimentos armazenados, não podem ser executados diretamente. Em vez disso, eles são executados como parte de uma operação em um documento. Podemos especificar os disparadores para ser executado com uma solicitação usando cabeçalhos HTTP. A seguir está solicitação para criar um documento.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger
    
    
    {
       "name": "newDocument",
       “title”: “The Wizard of Oz”,
       “author”: “Frank Baum”,
       “pages”: 92
    }


Veja a ser executado com a solicitação do pré-disparador é especificado no cabeçalho da x-ms-documentdb-pre-trigger-include. Forma correspondente, disparadores POST recebem no cabeçalho da x-ms-documentdb-post-trigger-include. Observe que tanto pré e pós-disparadores podem ser especificados para uma determinada solicitação.

## <a name="sample-code"></a>Código de exemplo

Você pode encontrar mais exemplos de código do lado do servidor (incluindo a [exclusão em massa](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/bulkDelete.js)e [atualize](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/update.js)) em nosso [repositório Github](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples).

Deseja compartilhar seu procedimento armazenado a impressionantes? Envie uma solicitação de recepção! 

## <a name="next-steps"></a>Próximas etapas

Quando você tiver um ou mais procedimentos armazenados, disparadores e funções definidas pelo usuário criadas, você pode carregá-los e exibi-los no Portal do Azure usando o Script Explorer. Para obter mais informações, consulte [Exibir procedimentos armazenados, disparadores e funções definidas pelo usuário usando o Explorador de Script DocumentDB](documentdb-view-scripts.md).

Você também pode encontrar as seguintes referências e recursos úteis para seu caminho para saber mais sobre a programação de servidor de DocumentDB:

- [DocumentDB Azure SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx)
- [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)
- [JSON](http://www.json.org/) 
- [JavaScript ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
- [JavaScript – sistema de tipo JSON](http://www.json.org/js.html) 
- [Banco de dados seguro e portátil extensibilidade](http://dl.acm.org/citation.cfm?id=276339) 
- [Serviço orientado a arquitetura de banco de dados](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
- [Hospedar o Runtime .NET no Microsoft SQL server](http://dl.acm.org/citation.cfm?id=1007669)
