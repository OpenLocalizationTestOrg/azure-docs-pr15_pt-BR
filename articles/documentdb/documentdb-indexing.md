<properties 
    pageTitle="A indexação automática em DocumentDB | Microsoft Azure" 
    description="Saiba mais sobre como automática funciona indexação no Azure DocumentDB." 
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="mimig" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>
    
# <a name="automatic-indexing-in-azure-documentdb"></a>Automático indexação no Azure DocumentDB

Este artigo é extraído de papel ["esquema independente indexação com Azure DocumentDB"](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) , o que será apresentado na [Conferência interna 41st em muito grandes bancos de dados](http://www.vldb.org/2015/) entre 31 de agosto - 4 de setembro de 2015, e é uma introdução ao como indexação funciona no Azure DocumentDB. 

Após lendo este artigo, você irá responder as seguintes perguntas:

- Como o DocumentDB deduzir o esquema de um documento JSON?
- Como o DocumentDB criar um índice em documentos diferentes?
- Como o DocumentDB realiza a indexação automática em escala?

##<a id="HowDocumentDBIndexingWorks"></a>Como funciona a indexação de DocumentDB

[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) é um objetivo de banco de dados true livre de esquema foram criado para JSON. Ele não esperar ou exigir qualquer esquema ou definições de índice secundário aos dados de índice em escala. Isso permite que você defina rapidamente e iteramos sobre modelos de dados de aplicativo usando DocumentDB. Conforme você adicionar documentos a uma coleção, DocumentDB índices automaticamente todas as propriedades de documento para que eles estão disponíveis para a consulta. A indexação automática permite armazenar documentos pertencentes a esquemas completamente aleatório sem se preocupar em esquemas ou índices secundários.

Com uma meta para eliminar a incompatibilidade de impedância entre o banco de dados e os modelos de programação de aplicativo, DocumentDB explorações a simplicidade do JSON e sua falta de uma especificação de esquema. Ela faz não suposições sobre os documentos e permite que os documentos em um conjunto de DocumentDB para variar no esquema, além dos valores específicos de instância. Em contraste com outros bancos de dados do documento, o mecanismo de banco de dados do DocumentDB opera diretamente no nível de gramática JSON, restante independente o conceito de um esquema de documento e desfoque no limite entre os valores de instância e a estrutura de documentos. Este, em virada, permite que ela automaticamente índice documentos sem a necessidade de esquema ou índices secundários.

A indexação no DocumentDB tira proveito do fato de que gramática JSON permite documentos seja **representado como árvores**. Para um documento JSON seja representado como uma árvore, um nó raiz fictício precisa ser criado que pais o restante de nós reais no documento abaixo. Cada etiqueta incluindo os índices de matriz em um documento JSON torna-se um nó da árvore. A figura a seguir ilustra um exemplo de documento JSON e sua representação de árvore correspondente.

>[AZURE.NOTE] Como o JSON é descrição automática ou seja, cada documento inclui esquema (metadados) e dados, por exemplo, `{"locationId": 5, "city": "Moscow"}` revela que existem duas propriedades `locationId` e `city`, e que tenham um valores de propriedade numéricos e de cadeia de caracteres. DocumentDB é possível deduzir o esquema de documentos e indexá-los quando eles são inseridos ou substituídos, sem que você precisar definir esquemas ou índices secundários.


**Documentos JSON como árvores:**

![Documentos como árvores](media/documentdb-indexing/DocumentsAsTrees.png)

Por exemplo, no exemplo mostrado acima:

- A propriedade JSON `{"headquarters": "Belgium"}` propriedade no exemplo acima corresponde ao caminho/headquarters/Bélgica.
- A matriz JSON `{"exports": [{"city": “Moscow"}`, `{"city": Athens"}]}` corresponde aos caminhos `/exports/[]/city/Moscow` e `/exports/[]/city/Athens`.

Automáticas indexação, (1) cada caminho em uma árvore de documento é indexado (a menos que o desenvolvedor configurou explicitamente a política de indexação para excluir certos padrões de caminho). (2) cada atualização de um documento para um conjunto de DocumentDB leva a atualização da estrutura de índice (ou seja, a adição de causas ou a remoção de nós). Um dos principais requisitos de indexação automática de documentos é para garantir que o custo para indexar e consultar um documento com estrutura profundamente aninhada, diga 10 níveis, é a mesma que de um documento JSON simples consiste em pares chave-valor apenas um nível de profundidade. Portanto, uma representação de caminho normalizado é a base na qual ambos os subsistemas automáticos de indexação e de consulta são criados.

Uma implicação importante de tratamento de ambos os valores de esquema e instância uniformemente em termos de caminhos é que logicamente, basta, como documentos individuais, um índice dos dois documentos mostrado que mantém um mapa entre caminhos e as ids de documento que contém o caminho também podem ser representadas como uma árvore. DocumentDB usa esse fato para construir uma árvore de índice que foi criada fora da união de todas as árvores representando documentos individuais dentro do conjunto. A árvore de índice em conjuntos de DocumentDB aumenta ao longo do tempo conforme novos documentos obtém adicionados ou atualizados para o conjunto.


**Índice de DocumentDB como uma árvore:**

![Índice como uma árvore](media/documentdb-indexing/IndexAsTree.png)

Apesar de ser livre de esquema, SQL e JavaScript do DocumentDB consultar idiomas fornecem projeções relacionais e filtros, navegação hierárquica em documentos, operações espaciais e invocação de UDFs escrito inteiramente em JavaScript. Tempo de execução de consulta do DocumentDB é capaz de suportar essas consultas, desde que ele pode operar diretamente contra esta representação de árvore de índice dos dados.

A política padrão de indexação automaticamente índices todas as propriedades de todos os documentos e fornece consultas consistentes (ou seja, o índice é atualizado em sincronia com a gravação de documento). Como o DocumentDB suporta atualizações consistentes para a árvore de índice em escala? DocumentDB usa gravação otimizada, bloquear gratuito e log estruturados técnicas de manutenção de índice. Isso significa que DocumentDB pode oferecer suporte a um volume contínuo de gravações rápidas enquanto ainda atendendo a consultas consistentes. 

Indexação do DocumentDB foi projetado para eficiência de armazenamento e tratar multilocação. Para eficiência de custo, a sobrecarga de armazenamento em disco do índice é baixa e previsíveis. Atualizações de índice também são executadas dentro do orçamento de recursos do sistema alocados por conjunto de DocumentDB.

##<a name="NextSteps"></a>Próximas etapas
- Baixe ["esquema independente indexação com Azure DocumentDB"](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), a serem apresentados na conferência interna 41st em muito grandes bancos de dados, 31 de agosto - 4 de setembro de 2015.
- [Consulta com DocumentDB SQL](documentdb-sql-query.md)
- Saiba mais sobre como personalizar o índice de DocumentDB [aqui](documentdb-indexing-policies.md)
 
