<properties 
    pageTitle="DocumentDB modelo hierárquico recursos e conceitos | Microsoft Azure" 
    description="Aprenda sobre modelo hierárquico do DocumentDB de bancos de dados, conjuntos, função definida pelo usuário (UDF), documentos, permissões para gerenciar recursos e muito mais."
    keywords="Modelo hierárquico, documentdb, azure, Microsoft azure"   
    services="documentdb" 
    documentationCenter="" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="anhoh"/>

# <a name="documentdb-hierarchical-resource-model-and-concepts"></a>Modelo de recursos hierárquica de DocumentDB e conceitos

As entidades de banco de dados que DocumentDB gerencia são chamadas de **recursos**. Cada recurso é identificado exclusivamente por um URI lógico. Você pode interagir com os recursos usando verbos HTTP padrão, cabeçalhos de solicitação/resposta e códigos de status. 

Lendo este artigo, você poderá responder às seguintes perguntas:

- O que é o modelo de recursos do DocumentDB?
- O que são sistema definido de recursos em vez de recursos definidos pelo usuário?
- Como faço para endereçar um recurso?
- Como faço para trabalhar com coleções?
- Como faço para trabalhar com procedimentos armazenados, disparadores e funções definidas pelo usuário (UDFs)?

## <a name="hierarchical-resource-model"></a>Modelo de recursos hierárquica
Como o diagrama a seguir ilustra, hierárquica de DocumentDB **modelo de recursos** consiste em conjuntos de recursos em uma conta de banco de dados, cada endereçamento por meio de um URI lógico e estável. Um conjunto de recursos será chamado como um **feed** neste artigo. 

>[AZURE.NOTE] DocumentDB oferece um protocolo TCP altamente eficiente que também está RESTful no seu modelo de comunicação, disponível por meio do [cliente do .NET SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

![Modelo de recursos hierárquica de DocumentDB][1]  
**Modelo de recursos hierárquica**   

Para começar a trabalhar com os recursos, você deve [criar uma conta de banco de dados DocumentDB](documentdb-create-account.md) usando sua assinatura do Azure. Uma conta de banco de dados pode ser compostos de um conjunto de **bancos de dados**, cada um contendo vários **conjuntos**, cada qual por sua vez contêm de **procedimentos, armazenados aciona, UDFs, documentos** e relacionados **anexos** (recurso de visualização). Um banco de dados também possui associados **usuários**, cada um com um conjunto de **permissões** para acessar conjuntos, procedimentos armazenados, disparadores, UDFs, documentos ou anexos. Enquanto bancos de dados, usuários, permissões e coleções são definidas pelo sistema recursos com esquemas conhecidos, documentos e anexos contêm aleatório, conteúdo JSON definidas pelo usuário.  

|Recurso   |Descrição
|-----------|-----------
|Conta de banco de dados   |Uma conta de banco de dados está associada um conjunto de bancos de dados e um valor fixo de armazenamento de blob para anexos (recurso de visualização). Você pode criar uma ou mais contas de banco de dados usando a sua assinatura do Azure. Para obter mais informações, visite nossa [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).
|Banco de dados   |Um banco de dados é um contêiner lógico de particionado entre conjuntos de armazenamento de documentos. Também é um contêiner de usuários.
|Usuário   |O namespace lógico para definir o escopo de permissões. 
|Permissão |Um símbolo de autorização associado com um usuário para obter acesso a um recurso específico.
|Coleção |Uma coleção é um contêiner de documentos JSON e a lógica do aplicativo JavaScript associada. Uma coleção é uma entidade faturável, onde o [custo](documentdb-performance-levels.md) é determinada pelo nível de desempenho associado a coleção. Coleções podem abranger um ou mais partições/servidores e podem dimensionar para lidar com volumes praticamente ilimitados de armazenamento ou a produtividade.
|Procedimento armazenado   |Lógica de aplicativo escrita em JavaScript que é registrado com um conjunto e transacionalmente executado dentro do mecanismo de banco de dados.
|Disparadores    |Lógica de aplicativo escrita em JavaScript executado antes ou depois de uma inserção, substituir ou operação de exclusão.
|UDF    |Lógica de aplicativo escrita em JavaScript. UDFs habilitar modelar um operador de consulta personalizada e, portanto, estender o núcleo DocumentDB linguagem de consulta.
|Documento   |Conteúdo JSON (aleatório) definidos pelo usuário. Por padrão, nenhum esquema precisa ser definido nem índices secundários precisa ser fornecido para todos os documentos adicionados a uma coleção.
|(Visualização) Anexo   |Um anexo é um documento especial que contém referências e metadados associados para blob/mídia externa. O desenvolvedor pode optar por ter o blob gerenciado pelo DocumentDB ou armazená-la com um provedor de serviços de blob externos como o OneDrive, Dropbox, etc. 


## <a name="system-vs-user-defined-resources"></a>Sistema versus recursos definidos pelo usuário
Recursos como contas do banco de dados, bancos de dados, conjuntos, usuários, permissões, procedimentos armazenados, disparadores e UDFs - todos têm um esquema fixo e são chamados de recursos do sistema. Em contraste, recursos como documentos e anexos não têm restrições sobre o esquema e são exemplos de recursos definidos pelo usuário. Em DocumentDB, sistema e definidas pelo usuário recursos são representados e gerenciados como JSON compatíveis com o padrão. Todos os recursos, sistema ou usuário definido, tem as seguintes propriedades comuns.

> [AZURE.NOTE] Observe que todos os gerada pelo sistema propriedades em um recurso são precedidos com um sublinhado (_) em sua representação JSON.

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Propriedade</strong></p></td>
            <td valign="top"><p><strong>Usuário configurável ou gerado pelo sistema?</strong></p></td>
            <td valign="top"><p><strong>Finalidade</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>Gerado pelo sistema</p></td>
            <td valign="top"><p>Gerado pelo sistema, identificador exclusivo e hierárquico do recurso</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Gerado pelo sistema</p></td>
            <td valign="top"><p>ETag do recurso necessário para o controle de concorrência otimista</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>Gerado pelo sistema</p></td>
            <td valign="top"><p>Último atualização carimbo de hora do recurso</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Self</p></td>
            <td valign="top"><p>Gerado pelo sistema</p></td>
            <td valign="top"><p>URI de endereçamento exclusivo do recurso</p></td>
        </tr>
        <tr>
            <td valign="top"><p>ID</p></td>
            <td valign="top"><p>Gerado pelo sistema</p></td>
            <td valign="top"><p>Nome exclusivo do recurso (com o valor de chave de partição mesmo) definidos pelo usuário. Se o usuário não especificar um id, uma id será gerado pelo sistema</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Representação de fios de recursos
DocumentDB não exigem quaisquer extensões de proprietários para codificações JSON padrão ou especiais; ele funciona com documentos JSON compatível com padrão.  
 
### <a name="addressing-a-resource"></a>Um recurso de endereçamento
Todos os recursos estão URI endereçamento. O valor da propriedade **Self** de um recurso representa o URI relativo do recurso. O formato do URI consiste o /\<feed\>/ segmentos de caminho de {_rid}:  

|Valor da Self |Descrição
|-------------------|-----------
|/DBS   |Feed de bancos de dados em uma conta de banco de dados
|/DBS/ {dbName}  |Banco de dados com uma identificação correspondente ao valor {dbName}
|/colls/ /DBS/ {dbName}   |Feed de coleções em um banco de dados
|/colls/ /DBS/ {dbName} {collName} |Coleção com uma id correspondente ao valor {collName}
|/colls/ /DBS/ {dbName} {collName} / docs    |Feed de documentos em um conjunto
|/docs/ de /colls/ {collName} /DBS/ {dbName} {docId}    |Documento com uma id correspondente ao valor {doc}
|/ usuários / /DBS/ {dbName}   |Feed de usuários em um banco de dados
|/DBS/ {dbName} / usuários / {ID}   |Usuário com uma id correspondente ao valor {usuário}
|/DBS/ {dbName} / usuários / {ID} / permissões   |Feed de permissões em um usuário
|/DBS/ {dbName} / usuários / {ID} /permissions/ {permissionId}    |Permissão com uma identificação correspondente ao valor {permissão}
  
Cada recurso tem um nome de usuário exclusiva definido exposto por meio da propriedade de id. Observação: para documentos, se o usuário não especificar uma identificação, nossos SDKs aceitos gerará automaticamente uma identificação exclusiva para o documento. A identificação é uma cadeia de caracteres definidas pelo usuário, de até 256 caracteres exclusivo dentro do contexto de um recurso de pai específico. 

Cada recurso também tem um identificador de recurso hierárquica gerados pelo sistema (também conhecido como um RID), que está disponível por meio da propriedade _rid. O RID codifica toda a hierarquia de um determinado recurso e é uma representação interna conveniente usada para impor a integridade referencial de maneira distribuída. O RID é exclusivo dentro de uma conta de banco de dados e ele internamente é usado por DocumentDB para roteamento eficiente sem a necessidade de pesquisas de partição cruzada. Os valores das propriedades _rid e o Self são representações alternativas e canônicas de um recurso. 

As APIs REST de DocumentDB suporte endereçamento de recursos e roteamento de solicitações, a identificação e as propriedades de _rid.

## <a name="database-accounts"></a>Contas de banco de dados
Você pode provisionar uma ou mais contas de banco de dados de DocumentDB usando sua assinatura do Azure.

Você pode [criar e gerenciar contas de banco de dados de DocumentDB](documentdb-create-account.md) por meio do Portal do Azure em [http://portal.azure.com/](https://portal.azure.com/). Criar e gerenciar uma conta de banco de dados requerem acesso administrativo e só podem ser executada em sua assinatura do Azure. 

### <a name="database-account-properties"></a>Propriedades de conta de banco de dados
Como parte de provisionar e gerenciar uma conta de banco de dados, você pode configurar e leia as seguintes propriedades:  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Nome da propriedade</strong></p></td>
            <td valign="top"><p><strong>Descrição</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Política de consistência</p></td>
            <td valign="top"><p>Defina esta propriedade para configurar o nível de consistência padrão para todas as coleções em sua conta de banco de dados. Você pode substituir o nível de consistência em uma base por solicitação usando o cabeçalho de solicitação de [x-ms--nível de consistência]. <p><p>Observe que essa propriedade só se aplica a <i>recursos definidos pelo usuário</i>. Sistema de todos os recursos definidos são configurados para oferecer suporte a leituras/consultas com consistência forte.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Teclas de autorização</p></td>
            <td valign="top"><p>Estas são as chaves primárias e secundárias mestre e somente leitura que fornecem acesso administrativo a todos os recursos sob a conta de banco de dados.</p></td>
        </tr>
    </tbody>
</table>

Observe que além de provisionamento, configurando e gerenciando sua conta de banco de dados a partir do Portal do Azure, você pode criar e gerenciar contas de banco de dados de DocumentDB usando o [Azure DocumentDB restante APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx) bem como [cliente SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx)também programaticamente.  

## <a name="databases"></a>Bancos de dados
Um banco de dados de DocumentDB é um contêiner lógico de um ou mais conjuntos e usuários, conforme mostrado no diagrama a seguir. Você pode criar qualquer número de bancos de dados em uma conta de banco de dados DocumentDB sujeitos a oferta limites.  

![Modelo de hierárquica conta e conjuntos de banco de dados][2]  
**Um banco de dados é um contêiner lógico de usuários e conjuntos**

Um banco de dados pode conter armazenamento de documentos de praticamente ilimitado particionado por conjuntos, que formam os domínios de transação para os documentos contidos neles. 

### <a name="elastic-scale-of-a-documentdb-database"></a>Dimensionamento Elástico um banco de dados de DocumentDB
Um banco de dados de DocumentDB é Elástico por padrão – desde alguns GB até petabytes de armazenamento de documentos SSD feito e produtividade provisionada. 

Ao contrário de um banco de dados em RDBMS tradicional, um banco de dados DocumentDB não é delimitado para um único computador. Com DocumentDB, como a escala do aplicativo precisa crescer, você pode criar mais conjuntos, bancos de dados ou ambos. Na verdade, vários aplicativos de terceiros primeiro no Microsoft tiveram sido usando DocumentDB em uma escala de consumidor Criando bancos de dados muito grandes DocumentDB cada contendo milhares de coleções com terabytes de armazenamento de documentos. Você pode aumentar ou diminuir um banco de dados adicionando ou removendo coleções para atender aos requisitos de escala do seu aplicativo. 

Você pode criar qualquer número de conjuntos de dentro de um banco de dados sujeito a oferta. Cada coleção tem SSD feito o armazenamento e produtividade provisionada para você, dependendo do nível de desempenho selecionado.

Um banco de dados de DocumentDB também é um contêiner de usuários. Um usuário, em vez, é um namespace lógico para um conjunto de permissões que fornece acesso a conjuntos, documentos e anexos e autorização refinada.  
 
Como com outros recursos no modelo de recurso DocumentDB, bancos de dados podem ser criados, substituídos, excluídos, ler ou enumerado facilmente usando [APIs REST do Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou qualquer um dos [SDKs de cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB garante consistência forte para leitura ou consultar os metadados de um recurso de banco de dados. Excluir um banco de dados automaticamente garante que você não pode acessar qualquer uma das coleções ou usuários contidos nela.   

## <a name="collections"></a>Coleções
Uma coleção de DocumentDB é um contêiner para os seus documentos JSON. Um conjunto também é uma unidade de escala para transações e consultas. 

### <a name="elastic-ssd-backed-document-storage"></a>SSD elástica feito o armazenamento de documentos
Um conjunto é intrinsecamente Elástico - -aumenta e diminui à medida que você adicionar ou remove documentos automaticamente. Coleções são recursos lógicos e podem abranger uma ou mais partições físicas ou servidores. O número de partições dentro de uma coleção é determinado pelo DocumentDB de acordo com o tamanho do armazenamento e a produtividade provisionada do seu conjunto. Todas as partições no DocumentDB tem um valor fixo de armazenamento reserva SSD associado a ele e é replicada para alta disponibilidade. Gerenciamento de partição totalmente é gerenciado pela DocumentDB do Azure e você não precisa escrever código complexo ou gerenciar suas partições. Conjuntos de DocumentDB são **praticamente ilimitado** em termos de produtividade e de armazenamento. 

### <a name="automatic-indexing-of-collections"></a>A indexação automática de conjuntos de
DocumentDB é um sistema de banco de dados livres de esquema true. Ele não presumem ou exigir qualquer esquema para os documentos JSON. Conforme você adicionar documentos a uma coleção, DocumentDB automaticamente índices-las e elas estão disponíveis para sua consulta. A indexação automática de documentos sem a necessidade de esquema ou índices secundários é um recurso chave do DocumentDB e é habilitada por técnicas de manutenção de índice otimizada para gravação, sem bloqueio e estruturados de log. DocumentDB oferece suporte contínuo volume de gravações muito rápidas enquanto ainda atendendo a consultas consistentes. Armazenamento de documento e índice são usadas para calcular o armazenamento consumido por cada conjunto. Você pode controlar os armazenamento e desempenho concessões associados indexação Configurando a política de indexação para um conjunto. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Configurando a política de indexação de um conjunto
A política de indexação de cada coleção permite que você faça desempenho e armazenamento concessões associados a indexação. As opções a seguir estão disponíveis como parte da configuração de indexação:  

-   Escolha se o conjunto automaticamente índices todos os documentos ou não. Por padrão, todos os documentos são automaticamente indexados. Você pode optar por desativar a indexação automática e adicionar seletivamente apenas os documentos específicos ao índice. Por outro lado, você pode seletivamente optar por excluir apenas os documentos específicos. Você pode obter isso definindo a propriedade automática para ser verdadeiro ou falso na indexingPolicy de um conjunto e usando o cabeçalho de solicitação de [x-ms-indexingdirective] ao inserir, substituir ou excluir um documento.  
-   Escolha se deseja incluir ou excluir caminhos específicos ou padrões em seus documentos do índice. Você pode obter isso por includedPaths de configuração e excludedPaths sobre o indexingPolicy de um conjunto respectivamente. Você também pode configurar os armazenamento e desempenho concessões para consultas de intervalo e hash para padrões de caminho específico. 
-   Escolha entre síncrono (consistente) e atualizações de índice (preguiça) assíncrona. Por padrão, o índice é atualizado sincronia em cada inserir, substituir ou excluir de um documento para o conjunto. Isso permite que as consultas aceitar o mesmo nível de consistência que as leituras de documento. Enquanto DocumentDB é otimizada de gravação e oferece suporte contínuo volumes de gravações de documento junto com a manutenção do índice síncrono e atendendo a consultas consistentes, você pode configurar certos conjuntos para atualizar seu índice lentamente. A indexação lenta melhora o desempenho de gravação e é ideal para cenários de inclusão de massa para conjuntos de leitura pesado principalmente.

A política de indexação pode ser alterada executando uma COLOCAÇÃO na coleção. Isso pode ser obtido por meio do [cliente SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx), o [Portal do Azure](https://portal.azure.com) ou as [APIs REST do Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

### <a name="querying-a-collection"></a>Consultar uma coleção
Os documentos em um conjunto podem ter esquemas aleatório e você pode consultar documentos dentro de uma coleção sem fornecer qualquer esquema ou antecipadamente índices secundários. Você pode consultar a coleção usando a [sintaxe SQL de DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx), que fornece rich hierárquicos, relacionais e operadores espaciais e extensibilidade via UDFs baseada em JavaScript. Gramática JSON permite para documentos JSON como árvores com rótulos como nós da árvore de modelagem. Isso é explorar tanto por técnicas de indexação automáticas do DocumentDB além dialeto do SQL do DocumentDB. A linguagem de consulta DocumentDB consiste em três aspectos principais:   

1.  Um conjunto pequeno de operações de consulta que mapear naturalmente a estrutura de árvore, incluindo projeções e consultas hierárquicas. 
2.  Um subconjunto de operações relacionais incluindo composição, filtro, projeções, agregações e junções auto. 
3.  JavaScript puro com base em UDFs que funcionam com (1) e (2).  

O modelo de consulta DocumentDB tenta obter um equilíbrio entre funcionalidade, eficiência e simplicidade. O mecanismo de banco de dados DocumentDB nativamente compila e executa as instruções de consulta SQL. Você pode consultar uma coleção usando as [APIs REST do Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou qualquer do [cliente SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx). O SDK do .NET vem com um provedor LINQ.

> [AZURE.TIP] Você pode experimentar DocumentDB e executar consultas SQL em relação a nossa dataset no [Espaço de consulta](https://www.documentdb.com/sql/demo).

### <a name="multi-document-transactions"></a>Transações de vários documentos
Transações de banco de dados fornecem um modelo de programação confiáveis e previsível para lidar com alterações simultâneas aos dados. Em RDBMS, a maneira tradicional de escrever lógica comercial é escrever **procedimentos armazenados** e/ou **disparadores** e enviar para o servidor de banco de dados para execução transações. Em RDBMS, o programador de aplicativo é necessária para lidar com duas linguagens de programação diferentes: 

- O aplicativo (não-transacional) idioma (por exemplo, JavaScript, Python, c#, Java, etc.) de programação
- T-SQL, a linguagem de programação transações que nativamente é executada pelo banco de dados

Devido ao seu compromisso profunda com JavaScript e JSON diretamente no mecanismo de banco de dados, o DocumentDB fornece um modelo de programação intuitivo para executar lógica de aplicativo JavaScript com base diretamente nas coleções de arquivos em termos de procedimentos armazenados e disparadores. Isso permite para ambos os procedimentos a seguir:

- Controlar a implementação eficiente de concorrência, recuperação, automática de indexação dos gráficos de objeto JSON diretamente no mecanismo de banco de dados
- Expressando naturalmente fluxo de controle, variável escopo, atribuição e integração de primitivos com transações de banco de dados diretamente em termos a linguagem de programação do JavaScript de manipulação de exceção

A lógica de JavaScript registrada em um nível de conjunto, em seguida, pode executar operações de banco de dados nos documentos do conjunto de determinado. DocumentDB quebra implicitamente o JavaScript com base em procedimentos armazenados e disparadores dentro de um ambiente transações ACID com isolamento de instantâneo em documentos em um conjunto. Durante o curso de sua execução, se o JavaScript gera uma exceção, em seguida, a transação inteira é anulada. O modelo de programação resultante é um muito simples porém poderoso. Os desenvolvedores de JavaScript obtêm um modelo de programação "durável" enquanto estiver usando suas construções de linguagem familiares e primitivos de biblioteca.   

A capacidade de executar JavaScript diretamente no mecanismo de banco de dados no mesmo espaço de endereço do pool de buffer permite desempenho e transações execução das operações de banco de dados contra os documentos de um conjunto. Além disso, o mecanismo de banco de dados de DocumentDB compromete profundamente para o JSON e JavaScript elimina qualquer incompatibilidade de impedância entre os sistemas de tipo de aplicativo e o banco de dados.   

Depois de criar um conjunto, você pode registrar procedimentos armazenados, disparadores e UDFs com um conjunto usando as [APIs REST do Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou qualquer do [cliente SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx). Depois do registro, você pode fazer referência e executá-los. Considere o seguinte procedimento armazenado escrito inteiramente em JavaScript, o código a seguir leva dois argumentos (nome do catálogo e nome do autor) e cria um novo documento, consultas para um documento e atualiza – tudo dentro de uma transação ACID implícita. A qualquer momento durante a execução, se for lançada uma exceção de JavaScript, toda a transação será anulada.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();        
        var collectionLink = collectionManager.getSelfLink()
            
        // create a new document.
        collectionManager.createDocument(collectionLink,
            {id: name, author: author},
            function(err, documentCreated) {
                if(err) throw new Error(err.message);
                
                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function(err, matchingDocuments) {
                        if(err) throw new Error(err.message);
                        
                        context.getResponse().setBody(matchingDocuments.length);
                       
                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don’t need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);   
                        }
                    })
            })
    };

O cliente pode "entregar" a lógica de JavaScript acima para o banco de dados para execução transações via HTTP POST. Para obter mais informações sobre como usar métodos HTTP, consulte [RESTful interações com os recursos de DocumentDB](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

    client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
       .then(function(createdStoredProcedure) {
            return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
                "NoSQL Distilled",
                "Martin Fowler");
        })
        .then(function(result) {
            console.log(result);
        },
        function(error) {
            console.log(error);
        });


Observe que porque o banco de dados nativamente entende JSON e JavaScript, há nenhuma incompatibilidade de tipos de sistema, sem "Ou mapeamento" ou mágico de geração de código obrigatório.   

Procedimentos armazenados e disparadores interagem com um conjunto e os documentos em um conjunto por meio de um modelo de objeto bem definido, que expõe o contexto atual do conjunto.  

Coleções no DocumentDB podem ser criadas, excluídos, ler ou enumerado facilmente usando as [APIs REST do Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou qualquer do [cliente SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB sempre fornece consistência forte para leitura ou consultar os metadados de um conjunto. Excluir um conjunto automaticamente garante que você não pode acessar qualquer de documentos, anexos, procedimentos armazenados, disparadores e UDFs contém nele.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Procedimentos armazenados, disparadores e funções definidas pelo usuário (UDF)
Conforme descrito na seção anterior, você pode escrever lógica de aplicativo para executar diretamente dentro de uma transação dentro do mecanismo de banco de dados. A lógica do aplicativo pode ser escrita inteiramente em JavaScript e pode ser modelada como um procedimento armazenado, disparador ou uma UDF. O código JavaScript dentro de um procedimento armazenado ou um disparador pode inserir, substituir, excluir, documentos de leitura ou consulta em um conjunto. Por outro lado, o JavaScript dentro de uma UDF não é possível inserir, substituir ou excluir documentos. UDFs enumerar os documentos do conjunto de resultados de uma consulta e produzem outro conjunto de resultados. Para multilocação DocumentDB impõe uma governança de recursos de reserva estrito com base. Cada procedimento armazenado, disparador ou uma UDF obtém um quantum fixo de recursos do sistema operacional para fazer seu trabalho. Além disso, procedimentos armazenados, disparadores ou UDFs não é possível vincular contra bibliotecas JavaScript externas e estão na lista negra se eles excederem os orçamentos de recursos alocados para eles. Você pode registrar, cancelar o registro de procedimentos armazenados, disparadores ou UDFs com um conjunto usando as APIs REST.  Após o registro um procedimento armazenado, um disparador ou uma UDF é previamente compilada e armazenada como código de byte que é executado mais tarde. A seção a seguir ilustram como você pode usar o SDK do JavaScript DocumentDB para registrar, executar e cancelar o registro de um procedimento armazenado, disparadores e uma UDF. O SDK do JavaScript é uma embalagem simple sobre as [APIs REST de DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx). 

### <a name="registering-a-stored-procedure"></a>Registrar um procedimento armazenado
Registro de um procedimento armazenado cria um novo recurso de procedimento armazenado em um conjunto via HTTP POST.  

    var storedProc = {
        id: "validateAndCreate",
        body: function (documentToCreate) {
            documentToCreate.id = documentToCreate.id.toUpperCase();
            
            var collectionManager = getContext().getCollection();
            collectionManager.createDocument(collectionManager.getSelfLink(),
                documentToCreate,
                function(err, documentCreated) {
                    if(err) throw new Error('Error while creating document: ' + err.message;
                    getContext().getResponse().setBody('success - created ' + 
                            documentCreated.name);
                });
        }
    };
    
    client.createStoredProcedureAsync(collection._self, storedProc)
        .then(function (createdStoredProcedure) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-stored-procedure"></a>Executar um procedimento armazenado
Execução de um procedimento armazenado é feita por meio de um HTTP POST em relação a um recurso de procedimento armazenado existente passando parâmetros para o procedimento no corpo da solicitação.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>Cancelando o registro de um procedimento armazenado
Cancelando o registro de um procedimento armazenado simplesmente é feito por meio de um HTTP DELETE em relação a um recurso de procedimento armazenado existente.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Registrar um gatilho de pré-lançamento
Registro de um disparador é feito criando um novo recurso de disparador em um conjunto via HTTP POST. Você pode especificar se o disparador é pré ou um disparador de postagem e o tipo de operação podem ser associada a (por exemplo, criar, substituir, excluir ou todos).   

    var preTrigger = {
        id: "upperCaseId",
        body: function() {
                var item = getContext().getRequest().getBody();
                item.id = item.id.toUpperCase();
                getContext().getRequest().setBody(item);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.All
    }
    
    client.createTriggerAsync(collection._self, preTrigger)
        .then(function (createdPreTrigger) {
            console.log("Successfully created trigger");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-pre-trigger"></a>Executar um pré-disparador
Execução de um disparador é feita especificando o nome de um disparador existente no momento da solicitação de POST/colocar/excluir de um recurso de documento por meio do cabeçalho de solicitação de emissão.  
 
    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>Cancelando o registro de um pré-disparador
Cancelando o registro de um disparador simplesmente é feito por meio de emissão um HTTP DELETE em relação a um recurso de gatilho existente.  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>Registrar uma UDF
Registro de um UDF é feito criando um novo recurso UDF em um conjunto via HTTP POST.  

    var udf = { 
        id: "mathSqrt",
        body: function(number) {
                return Math.sqrt(number);
        },
    };
    client.createUserDefinedFunctionAsync(collection._self, udf)
        .then(function (createdUdf) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-udf-as-part-of-the-query"></a>Executar uma UDF como parte da consulta
Uma UDF pode ser especificada como parte da consulta SQL e é usada como uma maneira de estender o núcleo [linguagem de consulta SQL do DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx).

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>Cancelando o registro de uma UDF 
Cancelando o registro de uma UDF simplesmente é feito por meio de um HTTP DELETE em relação a um recurso UDF existente.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

Embora os trechos acima mostraram o registro (POST), o cancelamento de registro (colocar), a leitura/lista (GET) e a execução (POST) via o [SDK do JavaScript DocumentDB](https://github.com/Azure/azure-documentdb-js), você também pode usar as [APIs REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou outro [cliente SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx). 

## <a name="documents"></a>Documentos
Você pode inserir, substituir, excluir, ler, enumerar e consultar documentos JSON aleatório em um conjunto. DocumentDB não exigem qualquer esquema e não exige índices secundários para dar suporte à consulta sobre os documentos em um conjunto.   

Sendo um serviço de banco de dados verdadeiramente aberto, DocumentDB não estoque quaisquer tipos de dados especializados (por exemplo, data hora) ou codificações específicas para documentos JSON. Observe que DocumentDB não exige quaisquer convenções JSON especiais para codificar as relações entre vários documentos; a sintaxe SQL do DocumentDB fornece poderosa consulta relacional e hierárquica diferenciados de operadores de consulta e project documentos sem anotações especiais ou necessidade de codificar relações entre documentos usando propriedades.  
 
Como com todos os outros recursos, documentos podem ser criados, substituídos, excluídos, leia, enumerado e consultado facilmente usando APIs REST ou qualquer um dos [SDKs de cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx). Exclusão de um documento instantaneamente libera a cota correspondente a todos os anexos aninhados. O nível de consistência de leitura de documentos segue a política de consistência na conta do banco de dados. Essa política pode ser substituída em uma base por solicitação dependendo requisitos de consistência de dados do seu aplicativo. Ao consultar documentos, a consistência leitura segue o modo de indexação definir na coleção. Para "consistente", isso segue política de consistência da conta. 

## <a name="attachments-and-media"></a>Anexos e mídia
>[AZURE.NOTE] Recursos de anexo e mídia são recursos de visualização.
 
DocumentDB permite armazenar blobs binárias/mídia com DocumentDB ou seu próprio repositório de mídia remoto. Ele também permite que você representar os metadados de uma mídia em termos de um documento especial, chamado de anexo. Anexo no DocumentDB é um documento de (JSON) especial que faz referência o mídia/blob armazenado em outro local. Um anexo é simplesmente um documento especial que captura os metadados (por exemplo, local, autor etc.) de uma mídia armazenado em um armazenamento de mídia remoto. 

Considere a possibilidade de um aplicativo de leitura social que usa DocumentDB para armazenar anotações à tinta e metadados incluindo comentários, destaca, marcadores, classificações, curtidas/preferências pessoais etc associados para um livro eletrônico de um determinado usuário.   

-   O conteúdo do catálogo propriamente dito está armazenado no armazenamento de mídia ou disponível como parte da conta de banco de dados de DocumentDB ou um repositório de mídia remoto. 
-   Um aplicativo pode armazenar metadados de cada usuário como um documento distinto – por exemplo, os metadados de José para Pasta1 é armazenado em um documento referenciado pelo /colls/joe/docs/book1. 
-   Anexos apontando para as páginas de conteúdo de um determinado livro de um usuário são armazenados sob o documento correspondente, por exemplo, /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2 etc. 

Observe que os exemplos listados acima usam ids amigáveis para transmitir a hierarquia de recursos. Recursos são acessados por meio das APIs REST por meio de ids de recurso exclusivo. 

Para a mídia é gerenciada pela DocumentDB, a propriedade _media do anexo fará referência a mídia por seu URI. DocumentDB garantirá para lixo coletar a mídia quando todas as referências pendentes são desconectadas. DocumentDB automaticamente gera o anexo quando você carrega a nova mídia e preenche o _media para apontar para a mídia recém-adicionado. Se você optar por armazenar a mídia em um armazenamento de blob remoto gerenciado por você (por exemplo, o OneDrive, o armazenamento do Azure, DropBox etc), você ainda pode usar anexos para fazer referência a mídia. Nesse caso, você mesmo cria o anexo e preencher sua propriedade _media.   

Como com todos os outros recursos, anexos podem ser criados, substituídos, excluídos, ler ou enumerado facilmente usando APIs REST ou em qualquer do cliente SDKs. Como com documentos, o nível de consistência de leitura de anexos segue a política de consistência na conta do banco de dados. Essa política pode ser substituída em uma base por solicitação dependendo requisitos de consistência de dados do seu aplicativo. Ao consultar anexos, a consistência leitura segue o modo de indexação definir na coleção. Para "consistente", isso segue política de consistência da conta. 
 
## <a name="users"></a>Usuários
Um usuário DocumentDB representa um namespace lógico para agrupar permissões. Um usuário DocumentDB pode corresponder a um usuário em um sistema de gerenciamento de identidade ou uma função de aplicativos predefinidos. Para DocumentDB, um usuário simplesmente representa uma abstração para agrupar um conjunto de permissões em um banco de dados.   

Para implementar multilocação em seu aplicativo, você pode criar usuários no DocumentDB que corresponde a seus usuários reais ou os locatários do seu aplicativo. Você pode criar permissões para um determinado usuário que correspondem ao controle de acesso sobre vários conjuntos de documentos, anexos, etc.   

Como seus aplicativos precisam crescer o usuário, você pode adotar várias maneiras de fragmentar seus dados. Você pode modelar cada um dos usuários da seguinte maneira:   

-   Cada usuário mapeia para um banco de dados.
-   Cada usuário mapeia para um conjunto. 
-   Documentos correspondente a vários usuários ir para um conjunto dedicado. 
-   Documentos correspondente a vários usuários ir para um conjunto de coleções.   

Independentemente da estratégia de fragmentação específicos que você escolher, você pode modelar usuários reais como usuários no banco de dados de DocumentDB e associar permissões refinadas fino para cada usuário.  

![Conjuntos de usuário][3]  
**Estratégias de fragmentação e modelagem de usuários**

Como todos os outros recursos, usuários em DocumentDB podem ser criados, substituídos, excluídos, ler ou enumerado facilmente usando APIs REST ou em qualquer do cliente SDKs. DocumentDB sempre fornece consistência forte para leitura ou consultar os metadados de um recurso de usuário. Vale a pena destacar que excluindo um usuário automaticamente garante que você não pode acessar qualquer uma das permissões contidas nele. Embora o DocumentDB recupera a cota das permissões como parte do usuário excluído no plano de fundo, as permissões excluídas está disponível instantaneamente novamente para ser usado.  

## <a name="permissions"></a>Permissões
De uma perspectiva de controle de acesso, recursos como contas do banco de dados, bancos de dados, usuários e permissões são considerados recursos *administrativos* desde que eles exigem permissões administrativas. Por outro lado, recursos, inclusive as coleções, documentos, anexos, procedimentos armazenados, disparadores e UDFs são escopo em um banco de dados determinado e considerados *recursos do aplicativo*. Correspondente para os dois tipos de recursos e as funções que acessá-los (ou seja o administrador e o usuário), o modelo de autorização define dois tipos de *teclas de acesso*: *chave mestre* e *chave de recurso*. A chave mestre é uma parte da conta do banco de dados e é fornecida para o desenvolvedor (ou administrador) que é o provisionamento a conta de banco de dados. Esta chave mestre tem semântica de administrador, que pode ser usado para autorizar o acesso aos recursos administrativos e de aplicativos. Em contraste, uma chave de recurso é uma chave de acesso granular que permita acesso a um recurso de aplicativo *específico* . Portanto, ele captura a relação entre o usuário de um banco de dados e as permissões do usuário para um recurso específico (por exemplo, conjunto, documento, anexo, procedimento armazenado, disparador ou UDF).   

A única maneira de obter uma chave de recurso é criando um recurso de permissão em um determinado usuário. Observe que, para criar ou recuperar uma permissão, uma chave mestre devem ser apresentada no cabeçalho de autorização. Um recurso de permissão liga o recurso, seu acesso e o usuário. Depois de criar um recurso de permissão, o usuário só precisa apresentar a chave de recurso associado para acessar o recurso relevante. Portanto, uma chave de recurso pode ser exibida como uma representação lógica e compacta do recurso permissão.  

Como com todos os outros recursos, permissões no DocumentDB podem ser criadas, substituídos, excluídos, ler ou enumerado facilmente usando APIs REST ou em qualquer do cliente SDKs. DocumentDB sempre fornece consistência forte para leitura ou consultar os metadados de uma permissão. 

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como trabalhar com recursos usando os comandos HTTP [RESTful](https://msdn.microsoft.com/library/azure/mt622086.aspx)interações com os recursos de DocumentDB.


[1]: media/documentdb-resources/resources1.png
[2]: media/documentdb-resources/resources2.png
[3]: media/documentdb-resources/resources3.png

