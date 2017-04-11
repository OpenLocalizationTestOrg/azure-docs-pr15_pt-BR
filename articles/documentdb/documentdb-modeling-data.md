<properties 
    pageTitle="Modelagem de dados no Azure DocumentDB | Microsoft Azure" 
    description="Saiba mais sobre modelagem de dados para DocumentDB, um banco de dados de documentos NoSQL." 
    keywords="modelagem de dados"
    services="documentdb" 
    authors="kiratp" 
    manager="jhubbard" 
    editor="mimig1" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/05/2016" 
    ms.author="kipandya"/>

#<a name="modeling-data-in-documentdb"></a>Dados de modelagem em DocumentDB#
Enquanto bancos de dados livres de esquema, como DocumentDB do Azure, torná-lo super fácil adotar alterações em seu modelo de dados você deve ainda gastar algum tempo pensando sobre seus dados. 

Como dados vai ser armazenados? Como seu aplicativo será recuperar e consultar dados? É o seu aplicativo leia espesso ou gravação pesado? 

Após ler este artigo, você poderá responder às seguintes perguntas:

- Como deve pensar sobre um documento em um banco de dados do documento?
- O que é modelagem de dados e por que devo me preocupar? 
- Modelagem de dados em um banco de dados do documento é diferente para um banco de dados relacional?
- Como posso express relacionamentos de dados em um banco de dados não-relacionais?
- Quando incorporar dados e quando vincular a dados?

##<a name="embedding-data"></a>Inserção de dados##
Quando você inicia a modelagem de dados em um armazenamento de documentos, como DocumentDB, tente tratar suas entidades como **documentos independentes** representados em JSON.

Antes de mergulhar muito muito mais, vamos voltar algumas etapas e dar uma olhada como podemos pode modelar algo em um banco de dados relacional, um assunto que muitos de nós já estiverem familiarizados com. O exemplo a seguir mostra como uma pessoa pode estar armazenada em um banco de dados relacional. 

![Modelo de banco de dados relacional](./media/documentdb-modeling-data/relational-data-model.png)

Ao trabalhar com bancos de dados relacionais, podemos tiver sido ministrados por anos normalizar, normalizar, normalizar.

Normalizando seus dados geralmente envolve fazendo uma entidade, como uma pessoa e dividi-la partes discreto de dados. No exemplo acima, uma pessoa pode ter vários registros de detalhes de contato, além de vários registros de endereço. Mesmo ir além e divida detalhes do contato extraindo mais comuns campos, como um tipo. Mesmo endereço, cada registro aqui tem um tipo como *Home* ou *Business* 

O orientando local quando normalização dos dados é **evitar armazenar dados redundantes** em cada registro e preferir fazer referência aos dados. Neste exemplo, para ler uma pessoa, com todos os seus detalhes de contato e endereços, é necessário usar junções efetivamente agregar os dados em tempo de execução.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Atualizando uma única pessoa com seus detalhes de contato e endereços requer operações de gravação em várias tabelas individuais. 

Agora vamos dar uma olhada em como podemos seria modelar os mesmos dados como uma entidade independente em um banco de dados do documento.
        
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "addresses": [
            {            
                "line1": "100 Some Street",
                "line2": "Unit 1",
                "city": "Seattle",
                "state": "WA",
                "zip": 98012
            }
        ],
        "contactDetails": [
            {"email: "thomas@andersen.com"},
            {"phone": "+1 555 555-5555", "extension": 5555}
        ] 
    }

Usando a abordagem acima temos agora **desordenados** pessoa gravar onde estamos **inserido** todas as informações relacionadas a essa pessoa, como seus detalhes de contato e endereços, na um único documento JSON.
Além disso, porque nós não estiver restrito a um esquema fixo temos a flexibilidade para fazer coisas como tendo detalhes de contato de diferentes formas completamente. 

Recuperar um registro de pessoa completa do banco de dados agora é uma única operação em relação a um único conjunto e para um único documento de leitura. Atualizando um registro de pessoa, com seus detalhes de contato e endereços, também é uma operação de gravação única em relação a um único documento.

Por Desnormalizando dados, talvez seja necessário seu aplicativo menos consultas e atualizações para concluir operações comuns de problemas. 

###<a name="when-to-embed"></a>Incorporar

Em geral, use dados incorporados modela quando:

- Há **contém** relações entre entidades.
- Há **um-para-algumas** relações entre entidades.
- Não há dados incorporados que **são alterados com frequência**.
- Lá é incorporado dados não crescem **sem limite**.
- Não há dados incorporados que **integral** aos dados em um documento.

> [AZURE.NOTE] Normalmente, modelos de dados desordenados fornecem melhor desempenho de **leitura** .

###<a name="when-not-to-embed"></a>Quando não incorporar

Embora a regra geral em um banco de dados do documento seja desnormalizar tudo e inserir todos os dados em um único documento, isso pode levar a algumas situações que devem-se evitar.

Leve esse trecho JSON.

    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "comments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            …
            {"id": 100001, "author": "jane", "comment": "and on we go ..."},
            …
            {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
            …
            {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
        ]
    }

Isso pode ser como uma entidade de postagem com comentários incorporados ficaria se nós foram modelagem um blog típico ou CMS, sistema. O problema com este exemplo é que a matriz de comentários é **ilimitado**, indicando que não há nenhum limite (prático) para o número de comentários pode ter qualquer postagem única. Isso tornará um problema como o tamanho do documento pode aumentar significativamente.

> [AZURE.TIP] Documentos em DocumentDB têm um tamanho máximo. Para obter mais informações sobre isso consulte [limites de DocumentDB](documentdb-limits.md).

À medida que o tamanho do documento cresce a capacidade de transmitir os dados sobre os fios bem como lendo e atualizando o documento, em escala, será afetada.

Nesse caso seria melhor levar em consideração o seguinte modelo.
        
    Post document:
    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "recentComments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            {"id": 3, "author": "jane", "comment": "....."}
        ]
    }

    Comment documents:
    {
        "postId": "1"
        "comments": [
            {"id": 4, "author": "anon", "comment": "more goodness"},
            {"id": 5, "author": "bob", "comment": "tails from the field"},
            ...
            {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
        ]
    },
    {
        "postId": "1"
        "comments": [
            {"id": 100, "author": "anon", "comment": "yet more"},
            ...
            {"id": 199, "author": "bored", "comment": "will this ever end?"}
        ]
    }

Este modelo possui os três mais recentes comentários incorporados da postagem em si, que é uma matriz fixa vinculado neste momento. Os outros comentários são agrupados em lotes de 100 comentários e armazenados em documentos separados. O tamanho do lote foi escolhido como 100 porque o nosso aplicativo fictício permite ao usuário carregar 100 comentários por vez.  

Outro caso onde a incorporação de dados não são uma boa ideia é quando os dados incorporados são usados com frequência em documentos e alterará com frequência. 

Leve esse trecho JSON.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            {
                "numberHeld": 100,
                "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
            },
            {
                "numberHeld": 50,
                "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
            }
        ]
    }

Isso pode representar portfólio de ações de uma pessoa. Escolhemos incorporar as informações de ações em cada documento de portfólio. Em um ambiente onde os dados relacionados está mudando com frequência, como um aplicativo de negociação, a inserção de dados que são alterados com frequência de estoque será significa que você está atualizando constantemente cada documento de portfólio sempre que uma ação é trocada.

Ações *zaza* podem ser trocadas centenas de horas em um único dia e milhares de usuários podem ter *zaza* em seu portfólio. Com um modelo de dados como o anterior, temos atualizar milhares de documentos de portfólio muitas vezes todos os dias que levam a um sistema que não dimensionar muito bem. 

##<a id="Refer"></a>Referência de dados##

Portanto, a inserção de dados funciona perfeitamente para muitos casos, mas é claro que existem cenários quando desnormalização de seus dados causará mais problemas do vale a pena. Portanto, o que fazemos agora? 

Bancos de dados relacionais não são o único lugar onde você pode criar relações entre entidades. Em um banco de dados do documento, você pode ter informações em um documento que realmente se relacionam aos dados em outros documentos. Agora, eu não estou defendendo por até um minuto que criamos sistemas que poderiam ser mais adequados para um banco de dados relacional DocumentDB ou qualquer outro banco de dados do documento, mas relações simples são finas e podem ser muito útil. 

No JSON abaixo escolhemos para usar o exemplo de um portfólio de ações de versões anteriores, mas desta vez estamos consultem o item de estoque sobre o portfólio em vez de incorporá-lo. Dessa forma, quando o item de ações alterado frequentemente durante o dia todo o documento somente que precisa ser atualizado é o único documento de ações. 

    Person document:
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            { "numberHeld":  100, "stockId": 1},
            { "numberHeld":  50, "stockId": 2}
        ]
    }
    
    Stock documents:
    {
        "id": "1",
        "symbol": "zaza",
        "open": 1,
        "high": 2,
        "low": 0.5,
        "vol": 11970000,
        "mkt-cap": 42000000,
        "pe": 5.89
    },
    {
        "id": "2",
        "symbol": "xcxc",
        "open": 89,
        "high": 93.24,
        "low": 88.87,
        "vol": 2970200,
        "mkt-cap": 1005000,
        "pe": 75.82
    }
    

Uma desvantagem imediata essa abordagem é apesar se seu aplicativo é necessário para mostrar informações sobre cada ação que é mantida ao exibir portfólio de uma pessoa; Nesse caso, você precisaria fazer várias viagens ao banco de dados para carregar as informações para cada documento de ações. Aqui fizemos uma decisão para aumentar a eficiência das operações de gravação, o que acontece frequentemente ao longo do dia, mas alternadamente comprometido em operações de leitura que potencialmente têm menos impacto sobre o desempenho do sistema específico.

> [AZURE.NOTE] Normalizado dados modelos **pode exigir mais idas** para o servidor.

### <a name="what-about-foreign-keys"></a>O que são chaves estrangeiras?
Porque não há nenhum conceito de uma restrição, chave estrangeira ou caso contrário, qualquer relacionamento entre documentos que você tem em documentos é efetivamente "links fracos" e não será verificado pela próprio banco de dados. Se você quiser garantir que os dados de que um documento é fazendo referência a realmente existe, você precisa fazer isso em seu aplicativo, ou por meio do uso disparadores do lado do servidor ou procedimentos armazenados em DocumentDB.

###<a name="when-to-reference"></a>Quando fazer referência
Em geral, use dados normalizados modela quando:

- Representando relações de **um-para-muitos** .
- Representando relações de **muitos-para-muitos** .
- Relacionadas de dados **forem alterados com frequência**.
- Dados referenciados podem ser **não vinculado**.

> [AZURE.NOTE] Normalmente normalizando oferece o melhor desempenho de **gravação** .

###<a name="where-do-i-put-the-relationship"></a>Onde coloquei o relacionamento?
O crescimento da relação ajudará a determinar quais documento para armazenar a referência.

Se olharmos para o JSON que modela livros e editores.

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press",
        "books": [ 1, 2, 3, ..., 100, ..., 1000]
    }

    Book documents:
    {"id": "1", "name": "DocumentDB 101" }
    {"id": "2", "name": "DocumentDB for RDBMS Users" }
    {"id": "3", "name": "Taking over the world one JSON doc at a time" }
    ...
    {"id": "100", "name": "Learn about Azure DocumentDB" }
    ...
    {"id": "1000", "name": "Deep Dive in to DocumentDB" }

Se o número de livros por publisher for pequeno com crescimento limitado, armazenando a referência de catálogo dentro do documento do publisher pode ser úteis. No entanto, se o número de livros por publisher for ilimitado, esse modelo de dados levaria a matrizes mutáveis, crescentes, como o documento de fornecedor de exemplo acima. 

Migrando coisas em torno de um pouco resultaria em um modelo que ainda representa os mesmos dados, mas agora evita essas coleções mutáveis grandes.

    Publisher document: 
    {
        "id": "mspress",
        "name": "Microsoft Press"
    }
    
    Book documents: 
    {"id": "1","name": "DocumentDB 101", "pub-id": "mspress"}
    {"id": "2","name": "DocumentDB for RDBMS Users", "pub-id": "mspress"}
    {"id": "3","name": "Taking over the world one JSON doc at a time"}
    ...
    {"id": "100","name": "Learn about Azure DocumentDB", "pub-id": "mspress"}
    ...
    {"id": "1000","name": "Deep Dive in to DocumentDB", "pub-id": "mspress"}

No exemplo acima, podemos ter descartados coleção ilimitada no documento publisher. Em vez disso, vamos apenas tiver uma uma referência para o publisher em cada documento de catálogo.

###<a name="how-do-i-model-manymany-relationships"></a>Como posso modelar relações muitos: muitos?
Em um banco de dados relacional relações *muitos: muitos* geralmente são modeladas com tabelas de junção, que apenas unir registros de outras tabelas. 

![Associar tabelas](./media/documentdb-modeling-data/join-table.png)

Talvez seja tentado a replicar a mesma coisa usando documentos e gerar um modelo de dados que seja semelhante à seguinte.

    Author documents: 
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }
    
    Book documents:
    {"id": "b1", "name": "DocumentDB 101" }
    {"id": "b2", "name": "DocumentDB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure DocumentDB" }
    {"id": "b5", "name": "Deep Dive in to DocumentDB" }
    
    Joining documents: 
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

Isso funcionaria. Carregar qualquer um autor com seus livros ou carregar um livro com seu autor, no entanto, sempre exijam pelo menos duas consultas adicionais no banco de dados. Uma consulta para o documento de ingresso e, em seguida, outra consulta para buscar o documento real serem Unido. 

Se tudo o que está fazendo esta tabela de junção é colando juntos duas partes de dados, por que não solte-completamente?
Considere o seguinte.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}
    
    Book documents: 
    {"id": "b1", "name": "DocumentDB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "DocumentDB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure DocumentDB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive in to DocumentDB", "authors": ["a2"]}

Agora, se eu tinha um autor, imediatamente saber quais livros gravado e, ao contrário, se eu tinha um documento de livro carregado seria saber as identificações da author(s). Isso salva essa consulta intermediária contra a tabela de junção reduzindo o número de servidor arredondar viagens seu aplicativo tem a fazer. 

##<a id="WrapUp"></a>Modelos de dados híbrido##
Agora encontramos incorporação (ou Desnormalizando) e dados de referência (ou normalizando), cada ter seus upsides e cada ter compromete como temos visto. 

Sempre não precisa ser ou não ser medo misturar um pouco coisas para cima. 

Com base em padrões de uso específico e cargas de trabalho pode haver casos onde misturar incorporado de seu aplicativo e dados referenciados faz sentido e poderia liderar a lógica do aplicativo mais simples com menos de servidor arredondar viagens mantendo um bom nível de desempenho.

Considere o seguinte JSON. 

    Author documents: 
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",     
        "countOfBooks": 3,
        "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "http://....png"}
            {"profile": "http://....png"}
            {"large": "http://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "http://....png"}
        ]
    }
    
    Book documents:
    {
        "id": "b1",
        "name": "DocumentDB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "http://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "DocumentDB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
        ]
    }

Aqui nós (principalmente) seguiu o modelo incorporado, onde os dados de outras entidades são inseridos no documento de nível superior, mas outros dados são referenciados. 

Se você examinar o documento de catálogo, podemos ver algumas interessantes campos quando olharmos para a matriz de autores. Há um campo de *identificação* que é o campo que usamos para voltar a um documento de autor, prática padrão em um modelo normalizado, mas depois também temos *nome* e *thumbnailUrl*. Nós poderia apenas preso com a *id* e o aplicativo para obter informações adicionais, que ela necessária de documento autor respectivos usando o "link" para a esquerda, mas porque o nosso aplicativo exibe o nome do autor e uma imagem em miniatura com cada catálogo exibido nós pode salvar uma viagem no servidor por catálogo em uma lista Desnormalizando **alguns** dados do autor.

Claro, se o nome do autor alterado ou eles querem atualizar sua foto que temos para ir uma atualização cada livro eles nunca de publicado mas em nosso aplicativo, baseado na suposição de que os autores não alterem seus nomes frequentemente, essa é uma decisão de design aceitável.  

No exemplo há valores **calculados previamente agregados** , para salvar o processamento dispendioso em uma operação de leitura. No exemplo, alguns dos dados inseridos no documento autor é dados calculados em tempo de execução. Sempre que um novo livro é publicado, um documento de catálogo é criado **e** que o campo countOfBooks é definido como um valor calculado com base no número de documentos do livro existentes para um determinado autor. Essa otimização seria bom em leitura sistemas pesados onde estamos pode pagar fazer cálculos em gravações para otimizar leituras.

A capacidade de ter um modelo com campos calculados previamente é feita possível porque DocumentDB dá suporte a **transações de vários documentos**. Muitas lojas NoSQL não podem fazer transações em documentos e, portanto, Assistente decisões de design, como "sempre incorporar tudo", devido a essa limitação. Com DocumentDB, você pode usar disparadores do lado do servidor ou procedimentos armazenados, que inserir livros e atualizar autores tudo isso dentro de uma transação ACID. Agora você não **tiver** incorporar tudo em um único documento apenas para Certifique-se de que seus dados permaneçam consistentes.

##<a name="NextSteps"></a>Próximas etapas

Os argumentos maiores deste artigo é entender que modelagem em um mundo livre de esquema de dados são tão importantes quanto nunca. 

Assim como não há nenhuma maneira única para representar um pedaço de dados em uma tela, não há nenhuma maneira única de seus dados de modelo. Você precisa compreender seu aplicativo e como ele produzirá, consumir e processar os dados. Em seguida, aplicando algumas das diretrizes apresentadas aqui você pode definir sobre a criação de um modelo que atende às necessidades imediatas de seu aplicativo. Quando seus aplicativos precisarem mudar, você pode aproveitar a flexibilidade de um livre de esquema de banco de dados adotar mudar e evoluir facilmente o seu modelo de dados. 

Para saber mais sobre DocumentDB do Azure, consulte a página de [documentação](https://azure.microsoft.com/documentation/services/documentdb/) do serviço. 

Para saber mais sobre índices de ajustes no Azure DocumentDB, consulte o artigo [políticas](documentdb-indexing-policies.md)de indexação.

Para entender como para fragmentar seus dados em várias partições, consulte [Dados partição em DocumentDB](documentdb-partition-data.md). 

E finalmente, para obter orientações sobre dados de modelagem e fragmentação para aplicativos de vários locatários, consulte [dimensionamento de um aplicativo de vários locatários com DocumentDB do Azure](http://blogs.msdn.com/b/documentdb/archive/2014/12/03/scaling-a-multi-tenant-application-with-azure-documentdb.aspx).
 
