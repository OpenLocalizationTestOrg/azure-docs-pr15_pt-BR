<properties 
    pageTitle="Perguntas de banco de dados de DocumentDB - perguntas frequentes | Microsoft Azure" 
    description="Obtenha respostas para perguntas frequentes sobre Azure DocumentDB um serviço de banco de dados de documentos NoSQL para JSON. Banco de dados de perguntas sobre capacidade, níveis de desempenho e dimensionamento." 
    keywords="Perguntas de banco de dados, perguntas frequentes perguntas, documentdb, azure, do Microsoft azure"
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
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="mimig"/>


#<a name="frequently-asked-questions-about-documentdb"></a>Perguntas frequentes sobre DocumentDB

## <a name="database-questions-about-microsoft-azure-documentdb-fundamentals"></a>Perguntas de banco de dados sobre os conceitos básicos da Microsoft Azure DocumentDB

### <a name="what-is-microsoft-azure-documentdb"></a>O que é o Microsoft Azure DocumentDB? 
Microsoft Azure DocumentDB é um mais rápido, escala planeta NoSQL documento banco de dados-como um serviço que oferece a consulta avançada sobre dados sem esquema, ajuda a oferecer desempenho configurável e confiável e permite rápido desenvolvimento, tudo por meio de uma plataforma gerenciada feito por potência e atinja do Microsoft Azure. DocumentDB é a melhor solução para jogos móvel, da web, e aplicativos de IoT quando produtividade previsível, disponibilidade alta, baixa latência e um modelo de dados sem esquema são requisitos chave. DocumentDB fornece flexibilidade de esquema e rich indexação por meio de um modelo de dados nativo JSON e inclui vários documento suporte transações com JavaScript integrado.  
  
Para mais perguntas de banco de dados, respostas e instruções sobre como implantar e usando esse serviço, consulte a [página de documentação DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

### <a name="what-kind-of-database-is-documentdb"></a>Que tipo de banco de dados é DocumentDB?
DocumentDB é um documento orientado banco de dados NoSQL que armazena dados em formato JSON.  DocumentDB oferece suporte para estruturas aninhadas, auto-contained-dados que podem ser consultadas por meio de uma DocumentDB rich [gramática de consulta SQL](documentdb-sql-query.md). DocumentDB fornece alto desempenho transações de processamento do lado do servidor JavaScript por meio de [procedimentos armazenados, disparadores e funções definidas pelo usuário](documentdb-programming.md). O banco de dados também suporta níveis de consistência ajustáveis do desenvolvedor com associados [níveis de desempenho](documentdb-performance-levels.md).
 
### <a name="do-documentdb-databases-have-tables-like-a-relational-database-rdbms"></a>Bancos de dados de DocumentDB tem tabelas como um banco de dados relacional (RDBMS)?
Não, DocumentDB armazena os dados em conjuntos de documentos JSON.  Para obter informações sobre recursos de DocumentDB, consulte [conceitos e modelo de DocumentDB de recursos](documentdb-resources.md). Para obter mais informações sobre como as soluções de NoSQL como DocumentDB diferem das soluções relacionais, consulte [NoSQL vs SQL](documentdb-nosql-vs-sql.md).

### <a name="do-documentdb-databases-support-schema-free-data"></a>Bancos de dados de DocumentDB oferece suporte a dados sem esquema?
Sim, o DocumentDB permite que aplicativos armazenar documentos JSON aleatório sem definição de esquema ou dicas. Dados estão imediatamente disponíveis para consulta por meio da interface de consulta DocumentDB SQL.   

### <a name="does-documentdb-support-acid-transactions"></a>DocumentDB suporta transações ACID?
Sim, DocumentDB suporta transações de documentos cruzados expressas como procedimentos de JavaScript armazenado e disparadores. Transações estão com escopo de uma única partição dentro de cada coleção e executadas com semântica ACID como tudo ou nada isolada de outras solicitações simultaneamente execução de código e usuário.  Se forem geradas exceções por meio da execução do lado do servidor JavaScript do código do aplicativo, a transação inteira é revertida. Para obter mais informações sobre transações, consulte [transações de programa de banco de dados](documentdb-programming.md#database-program-transactions).

### <a name="what-are-the-typical-use-cases-for-documentdb"></a>Quais são os casos de uso típico de DocumentDB?  
DocumentDB é uma boa opção para novos jogos móveis, de web, e aplicativos IoT onde escala automática, desempenho previsível, rápido ordem dos tempos de resposta de milissegundos e a capacidade de consulta sobre os dados sem esquema é importante. DocumentDB permitem rápido desenvolvimento e suporte a iteração contínua de modelos de dados de aplicativo. Aplicativos que gerenciar conteúdo gerado por usuários e os dados são [casos comuns de uso para DocumentDB](documentdb-use-cases.md).  

### <a name="how-does-documentdb-offer-predictable-performance"></a>Como o DocumentDB oferta previsíveis de desempenho?
Uma [unidade de solicitação](documentdb-request-units.md) é a medida da taxa de transferência em DocumentDB. 1 RU corresponde à produtividade do GET de um documento de 1KB. Cada operação em DocumentDB, incluindo leituras, gravações, consultas SQL e execuções de procedimento armazenado tem um valor de RU determinante com base na produtividade necessária para concluir a operação. Em vez de pensar sobre CPU, IO e memória e como eles cada afetarem a taxa de transferência do aplicativo, você pode pensar em termos de uma única medida RU.

Cada conjunto de DocumentDB pode ser reservado com provisionado produtividade em termos de RUs de produtividade por segundo. Para aplicativos de qualquer escala, você pode padrão de referência solicitações individuais para medir seus valores de RU e conjuntos de provisionar lidar com a soma total de unidades de solicitação em todas as solicitações. Também pode expandir ou dimensionar para baixo produtividade da sua coleção conforme as necessidades dos seus evolve de aplicativo. Para obter mais informações sobre unidades de solicitação e para ajudar a determinar seu conjunto precisa, leia [Gerenciar desempenho e capacidade](documentdb-manage.md) e tente a [Calculadora de produtividade](https://www.documentdb.com/capacityplanner). 

### <a name="is-documentdb-hipaa-compliant"></a>DocumentDB HIPAA é compatível?
Sim, DocumentDB é compatível com HIPAA. HIPAA estabelece requisitos para o uso, divulgação e a proteção de informações sobre a saúde individualmente identificáveis. Para obter mais informações, consulte a [Central de confiabilidade do Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-documentdb"></a>Quais são os limites de armazenamento do DocumentDB? 
Não há nenhum limite teórico para a quantidade total de dados que um conjunto pode armazenar em DocumentDB. Se você quiser armazenar mais de 250 GB de dados em um único conjunto, faça a [contatar o suporte](documentdb-increase-limits.md) para ter cota da sua conta aumentada. 

### <a name="what-are-the-throughput-limits-of-documentdb"></a>Quais são os limites de produtividade do DocumentDB? 
Não há nenhum limite teórico para a quantidade total da taxa de transferência que pode oferecer suporte a um conjunto em DocumentDB, se sua carga de trabalho pode ser distribuída aproximadamente uniformemente entre um número grande o suficiente de chaves de partição. Se desejar exceda solicitação 250.000 unidades/segundo por conjunto ou conta, faça a [contatar o suporte](documentdb-increase-limits.md) para ter cota da sua conta aumentada. 

### <a name="how-much-does-microsoft-azure-documentdb-cost"></a>Quanto custa o Microsoft Azure DocumentDB?
Consulte a página de [detalhes de preços DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/) para obter detalhes. Encargos de uso de DocumentDB são determinados pelo número de coleções em uso, o número de horas as coleções estavam online, e o armazenamento consumido e a produtividade provisionada para cada conjunto. 

### <a name="is-there-a-free-account-available"></a>Existe uma conta gratuita disponível?
Se você estiver começando a usar o Azure, você pode inscrever para uma [conta gratuita do Azure](https://azure.microsoft.com/free/), que lhe 30 dias e r $200 tentar todos os serviços do Azure. Ou, se você tiver uma assinatura do Visual Studio, você está qualificado para [r $150 no livres créditos Azure por mês](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) usar em qualquer serviço Azure.  

### <a name="how-can-i-get-additional-help-with-documentdb"></a>Como posso obter ajuda adicional com DocumentDB?
Se precisar de qualquer ajuda, verifique comunique-se em [Estouro de pilha](http://stackoverflow.com/questions/tagged/azure-documentdb), os [Fóruns de desenvolvedor do Azure DocumentDB MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB), ou agendar um [chat de 1:1 com a equipe de engenharia de DocumentDB](http://www.askdocdb.com/). Para se manter atualizado sobre as últimas notícias de DocumentDB e recursos, siga-no [Twitter](https://twitter.com/DocumentDB).

## <a name="set-up-microsoft-azure-documentdb"></a>Configurar o Microsoft Azure DocumentDB

### <a name="how-do-i-sign-up-for-microsoft-azure-documentdb"></a>Como faço inscrição do Microsoft Azure DocumentDB?
Microsoft Azure DocumentDB está disponível no [Portal do Azure][azure-portal].  Primeiro você deve inscrever para uma assinatura do Microsoft Azure.  Depois que você se inscrever para uma assinatura do Microsoft Azure, você pode adicionar uma conta de DocumentDB à sua assinatura do Azure. Para obter instruções sobre como adicionar uma conta de DocumentDB, consulte [criar uma conta de banco de dados DocumentDB](documentdb-create-account.md).   

### <a name="what-is-a-master-key"></a>O que é uma chave mestre?
Uma chave mestre é um token de segurança para acessar todos os recursos para uma conta. Indivíduos com a chave têm leitura e gravação acesso a todos os recursos na conta de banco de dados. Tenha cuidado ao distribuir chaves mestre. A chave primária de mestre e chave mestre secundária estão disponíveis na lâmina **teclas **do [Portal do Azure][azure-portal]. Para obter mais informações sobre chaves, consulte [Exibir, copiar e as teclas de acesso gerar](documentdb-manage-account.md#keys).

### <a name="how-do-i-create-a-database"></a>Como posso criar um banco de dados?
Você pode criar bancos de dados usando o [Portal do Azure]() como descrito em [criar um banco de dados de DocumentDB](documentdb-create-database.md), um dos [SDKs DocumentDB](documentdb-sdk-dotnet.md)ou por meio das [APIs REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).  

### <a name="what-is-a-collection"></a>O que é uma coleção?
Uma coleção é um contêiner de documentos JSON e a lógica do aplicativo JavaScript associada. Uma coleção é uma entidade faturável, onde o [custo](documentdb-performance-levels.md) é determinada pela produtividade e storaged usado. Coleções podem abranger um ou mais partições/servidores e podem dimensionar para lidar com volumes praticamente ilimitados de armazenamento ou a produtividade.

Coleções também são as entidades cobranças para DocumentDB. Cada coleção é cobrada por hora com base na produtividade provisionada e o espaço de armazenamento usado. Para obter mais informações, consulte [DocumentDB preços](https://azure.microsoft.com/pricing/details/documentdb/).  

### <a name="how-do-i-set-up-users-and-permissions"></a>Como posso configurar usuários e permissões?
Você pode criar usuários e permissões usando um dos [SDKs DocumentDB](documentdb-sdk-dotnet.md) ou por meio das [APIs REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).   

## <a name="database-questions-about-developing-against-microsoft-azure-documentdb"></a>Perguntas de banco de dados sobre como desenvolver contra DocumentDB do Microsoft Azure

### <a name="how-to-do-i-start-developing-against-documentdb"></a>Como fazer para começar a desenvolver contra DocumentDB?
[SDKs](documentdb-sdk-dotnet.md) estão disponíveis para .NET, Python, Node, JavaScript e Java.  Os desenvolvedores também podem aproveitar as [APIs HTTP RESTful](https://msdn.microsoft.com/library/azure/dn781481.aspx) para interagir com os recursos de DocumentDB de uma variedade de plataformas e idiomas. 

Exemplos para o DocumentDB [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node](documentdb-nodejs-samples.md)e [Python](documentdb-python-samples.md) SDKs estão disponíveis no GitHub.

### <a name="does-documentdb-support-sql"></a>DocumentDB suporta SQL?
A linguagem de consulta DocumentDB SQL é um subconjunto aprimorado a funcionalidade de consulta compatível com SQL. A linguagem de consulta DocumentDB SQL fornece rich hierárquicos e operadores relacionais e extensibilidade via JavaScript com base em usuário definidos funções (UDFs). Gramática JSON permite a modelagem documentos JSON como árvores com rótulos como os nós de árvore, que é usado pelo técnicas de indexação automáticas DocumentDB, bem como o dialeto de consulta SQL do DocumentDB.  Para obter detalhes sobre como usar a gramática SQL, consulte a [Consulta DocumentDB] [ query] artigo.

### <a name="what-are-the-data-types-supported-by-documentdb"></a>Quais são os tipos de dados compatíveis com DocumentDB?
Os tipos de dados primitivos com suporte no DocumentDB são os mesmos como JSON. JSON tem um sistema de tipo simples que consiste em cadeias de caracteres, números (IEEE754 dupla precisão) e boolianos - verdadeiros, falso e valores nulos.  Tipos de dados mais complexos como DateTime, Guid, Int64 e geometria podem ser representados no JSON e DocumentDB por meio da criação de objetos aninhados usando o operador de {} e matrizes usando o operador []. 

### <a name="how-does-documentdb-provide-concurrency"></a>Como o DocumentDB enviar concorrência?
DocumentDB suporta controle de concorrência otimista (OCC) através de marcas de entidade HTTP ou etags. Cada recurso DocumentDB tem um etag e a etag está definida no servidor sempre que um documento for atualizado. O cabeçalho etag e o valor atual são incluídos em todas as mensagens de resposta. ETags pode ser usadas com o cabeçalho If-correspondência para permitir que o servidor decidir se um recurso deve ser atualizado. O valor de correspondência se é o valor de etag para ser verificado no. Se o valor de etag corresponde o valor de etag do servidor, o recurso será atualizado. Se a etag não estiver mais atual, o servidor rejeita a operação com um "HTTP 412 Falha de pré-condição" código de resposta. O cliente precisará buscar novamente o recurso para adquirir o valor de etag atual para o recurso. Além disso, etags pode ser usadas com cabeçalho If-nenhum-correspondência para determinar se um buscar novamente de um recurso é necessária. 

Para usar concorrência otimista em .NET, use a classe [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Para uma amostra de .NET, consulte [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) na amostra DocumentManagement no github.

### <a name="how-do-i-perform-transactions-in-documentdb"></a>Como executar transações DocumentDB?
DocumentDB suporta integrada à linguagem transações via procedimentos de JavaScript armazenado e disparadores. Todas as operações de banco de dados dentro de scripts são executadas em isolamento de instantâneo como escopo o conjunto se é um conjunto de partição única ou documentos com o mesmo valor de chave de partição dentro de uma coleção, se a coleção estiver particionada. Um instantâneo das versões do documento (ETags) é feito no início da transação e confirmado somente se o script é bem sucedida. Se o JavaScript gera um erro, a transação será revertida. Para obter mais detalhes, consulte [programação do lado do servidor DocumentDB](documentdb-programming.md) .

### <a name="how-can-i-bulk-insert-documents-into-documentdb"></a>Como posso massa inserir documentos em DocumentDB? 
Há três maneiras de massa inserir documentos em DocumentDB:

- A ferramenta de migração dados, conforme descrito em [Importar dados para DocumentDB](documentdb-import-data.md).
- Explorador de documento no Portal do Azure, conforme descrito em [adição em massa de documentos com o Explorador de documento](documentdb-view-json-document-explorer.md#BulkAdd).
- Armazenados procedimentos, conforme descrito na [programação do lado do servidor DocumentDB](documentdb-programming.md).

### <a name="does-documentdb-support-resource-link-caching"></a>DocumentDB dá suporte a cache do recurso link?
Sim, porque DocumentDB é um serviço RESTful, links de recurso são imutáveis e podem ser armazenados em cache. Clientes de DocumentDB podem especificar um cabeçalho de "If-nenhum-correspondência" para leituras contra qualquer recurso como o documento ou conjunto e atualizar sua local copia somente quando a versão do servidor foi alterado. 

### <a name="is-a-local-instance-of-documentdb-available"></a>Uma instância local do DocumentDB está disponível?
Uma instância local do DocumentDB não está disponível neste momento. Você pode controlar o status de um emulador local e voto para ele no [Fórum de comentários](https://feedback.azure.com/forums/263030-documentdb/suggestions/6328798-standalone-local-instance).


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 
