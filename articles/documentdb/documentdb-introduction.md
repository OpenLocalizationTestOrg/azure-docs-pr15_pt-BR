<properties 
    pageTitle="Introdução ao DocumentDB, um banco de dados JSON | Microsoft Azure" 
    description="Saiba mais sobre Azure DocumentDB, um banco de dados NoSQL JSON. Este banco de dados do documento é criado para dados grandes, escalabilidade elástica e alta disponibilidade." 
    keywords="banco de dados de JSON, banco de dados de documento"
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="09/13/2016" 
    ms.author="mimig"/>

# <a name="introduction-to-documentdb-a-nosql-json-database"></a>Introdução ao DocumentDB: um banco de dados NoSQL JSON

##<a name="what-is-documentdb"></a>O que é DocumentDB?

DocumentDB é um serviço de banco de dados NoSQL totalmente gerenciado criado para desempenho rápido e previsível, alta disponibilidade, dimensionamento Elástico, distribuição global e facilidade de desenvolvimento. Como um esquema livre NoSQL banco de dados DocumentDB fornece recursos de consulta SQL sofisticados e familiares com menos latências consistentes em dados JSON - garantindo que 99% de seu leituras são atendidos em 10 milissegundos e 99% de suas gravações são atendidos em 15 milissegundos. Verifique estes benefícios exclusivos DocumentDB um ótimo ajustá-la para web, móvel, jogos e IoT e muitos outros aplicativos que precisam de escala perfeita e replicação global.

## <a name="how-can-i-learn-about-documentdb"></a>Como posso saber mais sobre DocumentDB? 

Uma maneira rápida de saber mais sobre DocumentDB e veja em ação é siga estas três etapas: 

1. Assista o minuto duas [o que é DocumentDB?](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/) vídeo, que apresenta os benefícios de usar DocumentDB.
2. Assista o três minutos [Criar DocumentDB no Azure](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) vídeo, que destaca como começar com DocumentDB usando o Portal do Azure.
3. Visite o [Espaço de consulta](http://www.documentdb.com/sql/demo), onde você pode percorrer diferentes atividades para saber mais sobre a funcionalidade de consulta avançada disponível no DocumentDB. Em seguida, vá direto para a guia de área restrita e executar suas própria consultas SQL personalizadas e experimentar DocumentDB.

Em seguida, retorne neste artigo, onde nos aprofundaremos mais.  

## <a name="what-capabilities-and-key-features-does-documentdb-offer"></a>Quais recursos e chave DocumentDB oferta?  

DocumentDB Azure oferece os seguintes recursos principais e benefícios:

-   **Consumidor scalable produtividade e armazenamento:** Expandir ou dimensionar para baixo do seu banco de dados de DocumentDB JSON para atender às suas necessidades de aplicativo com facilidade. Seus dados são armazenados em discos de estado sólido (SSD) para baixos latências previsíveis. DocumentDB suporta contêineres para armazenar dados JSON chamados coleções que podem chegar a produtividade provisionada e tamanhos de armazenamento praticamente ilimitado. Você pode consumidor dimensionar DocumentDB com desempenho previsível perfeitamente à medida que seu aplicativo cresce. 

-   **Replicação de várias regiões:** DocumentDB transparente replica seus dados para todas as regiões associados a sua conta de DocumentDB, permitindo que você desenvolver aplicativos que exigem acesso global a dados fornecendo compensações entre consistência, disponibilidade e desempenho, todos com garantias correspondentes. DocumentDB fornece failover regional transparente com APIs múltiplas home pages e a capacidade de dimensionar consumidor produtividade e armazenamento em todo o mundo. Saiba mais em o [distribuir dados globalmente com DocumentDB](documentdb-distribute-data-globally.md).

-   **Consultas Ad hoc com sintaxe SQL familiar:** Armazenar documentos JSON heterogêneos dentro DocumentDB e consultar esses documentos por meio de uma sintaxe SQL familiar. DocumentDB utiliza um bloqueio altamente simultâneo, gratuito, tecnologia de indexação para indexar automaticamente todo o conteúdo de documento estruturado de log. Isso permite consultas avançadas em tempo real sem a necessidade de especificar dicas de esquema, índices secundários ou modos de exibição. Saiba mais em [DocumentDB de consulta](documentdb-sql-query.md). 

-   **Execução do JavaScript no banco de dados:** Expresse lógica do aplicativo como procedimentos armazenados, disparadores e funções definidas pelo usuário (UDFs) usando JavaScript padrão. Isso permite que a lógica do aplicativo operar sobre os dados sem se preocupar as incompatibilidades entre o aplicativo e o esquema de banco de dados. DocumentDB fornece completo transações execução da lógica de aplicativo JavaScript diretamente dentro do mecanismo de banco de dados. A integração profunda do JavaScript permite a execução de inserir, substituir, excluir e selecionar operações de dentro de um programa de JavaScript como uma transação isolada. Saiba mais em [programação de servidor de DocumentDB](documentdb-programming.md).

-   **Níveis de consistência ajustáveis:** Selecione quatro níveis de consistência bem definidas para alcançar troca ideal entre consistência e desempenho. Para consultas e as operações de leitura, DocumentDB oferece quatro níveis de consistência distintas: sessão forte, envelhecimento limitada e eventual. Esses níveis de consistência granular e bem definidas permitem que você faça concessões som entre consistência, disponibilidade e latência. Saiba mais em [usando níveis de consistência para maximizar a disponibilidade e desempenho em DocumentDB](documentdb-consistency-levels.md).

-   **Totalmente gerenciados:** Elimine a necessidade de gerenciar recursos de banco de dados e de máquina. Como um serviço do Microsoft Azure totalmente gerenciado, você não precisa gerenciar máquinas virtuais, implantar e configurar o software, gerenciar dimensionamento ou lidar com as atualizações de camada de dados complexos. Cada banco de dados é feito automaticamente e protegido contra falhas regionais. Adicionar uma conta de DocumentDB e facilmente provisionar capacidade conforme necessário, permitindo que você se concentre em seu aplicativo em vez de operando e gerenciando seu banco de dados. 

-   **Aberto por design:** Começar rapidamente usando ferramentas e habilidades existentes. Programação contra DocumentDB é simples, acessível e não exige que você adotar novas ferramentas ou adicional extensões personalizadas para JSON ou JavaScript. Você pode acessar todas as funcionalidades de banco de dados, incluindo CRUD, consulta e JavaScript processamento em uma interface de HTTP RESTful simples. DocumentDB adota padrões, idiomas e formatos existentes, oferecendo valor alto recursos de banco de dados na parte superior-los.

-   **a indexação automática:** Por padrão, DocumentDB [índices automaticamente](documentdb-indexing.md) todos os documentos do banco de dados e não esperar ou exigir qualquer esquema ou a criação de índices secundários. Não quer indexar tudo? Não se preocupe, você pode [optar por cancelar caminhos nos seus arquivos JSON](documentdb-indexing-policies.md) muito.

##<a name="data-management"></a>Como o DocumentDB gerenciar dados?

DocumentDB Azure gerencia dados JSON por meio de recursos de banco de dados bem definido. Esses recursos são replicados para alta disponibilidade e são exclusivamente endereçamento por seu URI lógico. DocumentDB oferece um HTTP simple com base em modelo de programação RESTful para todos os recursos. 

A conta de banco de dados de DocumentDB é um namespace exclusivo que lhe dá acesso a DocumentDB do Azure. Antes de criar uma conta de banco de dados, você deve ter uma assinatura do Azure, que lhe dá acesso a uma variedade de serviços do Azure. 

Todos os recursos dentro DocumentDB são modelados e armazenados como JSON documentos. Os recursos são gerenciados como itens, que são JSON documentos que contém metadados e como feeds que são conjuntos de itens. Conjuntos de itens estão contidos em seus respectivos feeds.

A imagem abaixo mostra as relações entre os recursos de DocumentDB:

![A relação hierárquica entre recursos em DocumentDB, um banco de dados NoSQL JSON][1] 

Uma conta de banco de dados consiste em um conjunto de bancos de dados, cada um contendo vários conjuntos, cada um deles pode conter procedimentos armazenados, disparadores, UDFs, documentos e anexos relacionados. Um banco de dados também possui associados usuários, cada um com um conjunto de permissões para acessar vários outros conjuntos, procedimentos armazenados, disparadores, UDFs, documentos ou anexos. Enquanto bancos de dados, os usuários, permissões e coleções são definidas pelo sistema recursos com esquemas conhecidos, documentos, procedimentos armazenados, disparadores, UDFs e anexos contêm aleatório, definidos pelo usuário conteúdo JSON.  

##<a name="develop"></a>Como posso desenvolver aplicativos com DocumentDB?

Azure DocumentDB expõe recursos por meio de uma API REST que pode ser chamado por qualquer linguagem capaz de fazer solicitações HTTP/HTTPS. Além disso, DocumentDB oferece bibliotecas de programação para vários idiomas populares. Essas bibliotecas simplificam vários aspectos de trabalho com o Azure DocumentDB manipulando detalhes como endereço cache, gerenciamento de exceções, tentativas automáticas e assim por diante. Bibliotecas estão disponíveis atualmente para as plataformas e os idiomas a seguir:  

Baixar | Documentação
--- | ---
[.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) | [Biblioteca .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)
[Node SDK](http://go.microsoft.com/fwlink/?LinkID=402990) | [Biblioteca de Node](http://azure.github.io/azure-documentdb-node/)
[Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) | [Biblioteca Java](http://azure.github.io/azure-documentdb-java/)
[SDK de JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991) | [Biblioteca JavaScript](http://azure.github.io/azure-documentdb-js/)
n/d | [SDK de JavaScript do lado do servidor](http://azure.github.io/azure-documentdb-js-server/)
[Python SDK](https://pypi.python.org/pypi/pydocumentdb) | [Biblioteca de Python](http://azure.github.io/azure-documentdb-python/)

Além básica criar, ler, atualizar e excluir operações, DocumentDB fornece uma interface de consulta avançada do SQL para recuperar documentos JSON e suporte ao servidor para execução transações JavaScript da lógica de aplicativo. As interfaces de execução de consulta e script estão disponíveis por meio de todas as bibliotecas de plataforma, bem como as APIs REST. 

### <a name="sql-query"></a>Consulta SQL
Azure suporta DocumentDB consultando documentos usando uma linguagem SQL, com raiz no sistema de tipo JavaScript e expressões com suporte para consultas relacionais, hierárquicas e espaciais. A linguagem de consulta DocumentDB é uma interface simple, porém poderosa para documentos JSON de consulta. O idioma suporta um subconjunto de gramática ANSI SQL e adiciona ampla integração do objeto JavaScript, matrizes, construção de objeto e invocação de função. DocumentDB fornece seu modelo de consulta sem qualquer esquema explícita ou dicas de indexação do desenvolvedor.

Funções definidas pelo usuário (UDFs) pode ser registradas com DocumentDB e referenciadas como parte de uma consulta SQL, estendendo assim a gramática para oferecer suporte a lógica de aplicativo personalizado. Estas UDFs são gravados como JavaScript programas e executados no banco de dados. 

Para desenvolvedores .NET, DocumentDB também oferece um provedor de consulta LINQ como parte do [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx). 

### <a name="transactions-and-javascript-execution"></a>Transações e a execução de JavaScript
DocumentDB permite que você escrever lógica de aplicativo como programas nomeados escritos inteiramente em JavaScript. Esses programas são registrados para um conjunto e podem executar operações de banco de dados nos documentos em um determinado conjunto. JavaScript pode ser registrado para execução como disparador, procedimento armazenado ou função definida pelo usuário. Procedimentos armazenados e disparadores podem criar, ler, atualizar e excluir documentos enquanto funções definidas pelo usuário executar como parte da lógica de execução de consulta sem acesso de gravação para o conjunto.

Execução de JavaScript dentro DocumentDB é modelada após os conceitos compatíveis com sistemas de banco de dados relacional, com JavaScript como substituto moderno para Transact-SQL. Toda a lógica de JavaScript é executada dentro de uma transação ACID ambiente com isolamento de instantâneo. Durante o curso de sua execução, se o JavaScript gera uma exceção, em seguida, a transação inteira é anulada.

## <a name="next-steps"></a>Próximas etapas
Já tem uma conta do Microsoft Azure? Em seguida, você possa começar com DocumentDB no [Portal do Azure](https://portal.azure.com/#gallery/Microsoft.DocumentDB) criando [uma conta de banco de dados DocumentDB](documentdb-create-account.md).

Não tiver uma conta do Microsoft Azure? É possível:

- Inscreva-se para uma [avaliação gratuita Azure](https://azure.microsoft.com/free/), que lhe 30 dias e r $200 tentar todos os serviços do Azure. 
- Se você tiver uma assinatura do MSDN, você está qualificado para [r $150 no livres créditos Azure por mês](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) usar em qualquer serviço Azure. 

Em seguida, quando você estiver pronto para saber mais, visite o nosso [caminho de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para navegar até a todos os recursos de aprendizado disponíveis para você. 


[1]: ./media/documentdb-introduction/json-database-resources1.png
 
