<properties 
    pageTitle="Casos de uso comuns DocumentDB | Microsoft Azure" 
    description="Saiba mais sobre o início cinco casos de uso para DocumentDB: conteúdo, log de eventos, dados de catálogo, dados de preferências do usuário e Internet das coisas (IoT) gerado pelo usuário." 
    services="documentdb" 
    authors="h0n" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="hawong"/>

# <a name="common-documentdb-use-cases"></a>Casos comuns de uso de DocumentDB
Este artigo fornece uma visão geral de vários casos comuns de uso para DocumentDB.  As recomendações neste artigo servem como ponto de partida à medida que você desenvolve seu aplicativo com DocumentDB.   

Após ler este artigo, você poderá responder às seguintes perguntas: 
 
- Quais são os casos comuns de uso para DocumentDB?
- Quais são os benefícios de usar DocumentDB como um usuário gerada repositório de conteúdo?
- Quais são os benefícios de usar DocumentDB como um dados de catálogo armazena?
- Quais são os benefícios de usar DocumentDB como um log de eventos armazena?
- Quais são os benefícios de usar DocumentDB como uma usuário preferências de dados armazenados?
- Quais são os benefícios de usar DocumentDB como armazenamento de dados para sistemas de Internet das coisas (IoT)?

## <a name="common-use-cases-for-documentdb"></a>Casos comuns de uso para DocumentDB
DocumentDB Azure é um banco de dados de NoSQL de finalidade geral que é usado em uma ampla variedade de aplicativos e casos de uso. É uma boa opção para qualquer aplicativo que precisa tempos de resposta de ordem de milissegundos baixa e precisa ser dimensionar rapidamente. A seguir estão alguns atributos do DocumentDB que tornam mais adequado para aplicativos de alto desempenho.

- DocumentDB nativamente partições seus dados para alta disponibilidade e escalabilidade.
- DocumentDB tem armazenamento SSD feito com tempos de resposta de ordem de milissegundos de latência baixa.
- Suporte do DocumentDB para níveis de consistência como eventual, sessão e envelhecimento limitada permite baixo custo a desempenho-proporção. 
- DocumentDB tem um modelo de preços de dados amigável flexível que metros produtividade e armazenamento de maneira independente.
- Modelo de produtividade reservadas do DocumentDB permite que você pensar em termos de número de leituras/gravações em vez de memória/CPU/IOPs do hardware subjacente.
- Design permite que do DocumentDB você dimensionar para volumes grandes solicitação na ordem bilhões de solicitações por dia.

Esses atributos são particularmente úteis quando se trata de web, móvel, jogos e aplicativos de IoT que precisam baixo tempo de resposta e precisam lidar com grandes quantidades de leituras e gravações. 

## <a name="user-generated-content"></a>Conteúdo gerado por usuários
Um caso de uso comum para DocumentDB é para armazenar e conteúdo (UGC) gerado pelo usuário de consulta para web e aplicativos móveis, aplicativos de mídia social particularmente.  Alguns exemplos de UGC são sessões de bate-papo, tweets, postagens de blog, classificações e comentários.  Muitas vezes, o UGC em aplicativos de mídia social é uma mistura de texto de forma livre, propriedades, marcas e relações que não são limitadas pela estrutura rígida.   

Conteúdo como chats, comentários e postagens podem ser armazenados em DocumentDB sem a necessidade de transformações ou objeto complexo a camadas de mapeamento relacional.  Propriedades de dados podem ser adicionadas ou modificadas facilmente para atender aos requisitos como desenvolvedores itera código do aplicativo, portanto, promover desenvolvimento rápido.  

Aplicativos que se integram ao várias redes sociais devem responder a alteração esquemas dessas redes.  Como dados é automaticamente indexados por padrão em DocumentDB, dados estão prontos para ser consultados a qualquer momento.  Portanto, esses aplicativos têm flexibilidade para recuperar projeções de acordo com as suas respectivas necessidades.       

Muitos dos aplicativos sociais executar em escala global e podem apresentar padrões de uso imprevisíveis.  Flexibilidade em dimensionamento o armazenamento de dados é essencial como escalas de camada de aplicativo para corresponder ao demanda de uso.  Você pode dimensionar adicionando partições de dados adicionais em uma conta de DocumentDB.  Além disso, você também pode criar contas adicionais de DocumentDB entre várias regiões. Para disponibilidade de região do serviço de DocumentDB, consulte [Regiões do Azure](https://azure.microsoft.com/regions/#services).   

## <a name="catalog-data"></a>Dados de catálogo
Cenários de uso de dados de catálogo envolvem armazenar e consultando um conjunto de atributos para entidades como pessoas, lugares e produtos.  Alguns exemplos de dados de catálogo são contas de usuário, catálogos de produtos, registros de dispositivo para IoT e bola de sistemas de materiais.  Atributos para esses dados podem variar e podem mudar ao longo do tempo para acomodar requisitos do aplicativo.  

Considere um exemplo de um catálogo de produtos para um fornecedor de peças automotivas. Cada parte pode ter seus próprio atributos além dos atributos comuns que compartilham de todas as partes.  Além disso, os atributos de uma parte específica podem alterar do ano seguinte quando um novo modelo é lançado.  Como um armazenamento de documentos JSON, DocumentDB suporta esquemas flexíveis e permite representar dados com propriedades aninhadas e, portanto, ele é adequado para armazenar dados de catálogo de produtos.       

## <a name="logging-and-time-series-data"></a>Registro em log e dados de série temporal
Log de aplicativo geralmente é emitida em grandes volumes e podem ter atributos diversos baseado na versão de aplicativo implantado ou os eventos de log de componente.  Dados do log não estão limitados por relações complexas ou estruturas rígidas. Cada vez mais, os dados de log são mantidos no formato JSON como JSON é leve e fácil para as pessoas a leitura.
   
Normalmente, há dois casos de uso principais relacionados aos dados do log de eventos.  O primeiro caso de uso é executar consultas ad-hoc sobre um subconjunto de dados para solução de problemas.  Durante a solução de problemas, um subconjunto de dados será recuperado pela primeira vez os logs, normalmente por série temporal.  Em seguida, um drill down é realizado filtrando o dataset com níveis de erro ou mensagens de erro. Este é o local onde armazenar logs de eventos em DocumentDB é uma vantagem. Dados de log armazenados em DocumentDB será automaticamente indexados por padrão e, portanto, ele está pronto para ser consultados a qualquer momento. Além disso, os dados do log podem ser persistentes entre partições de dados, como uma série de tempo. Logs mais antigos podem ser implementados em armazenamento resfriado por sua política de retenção.          

O segundo caso de uso envolve a execução prolongadas trabalhos de análise de dados offline realizados em um grande volume de dados de log.  Análise de disponibilidade do servidor, análise de erro de aplicativo e análise de dados de sequência de cliques são exemplos deste caso de uso.  Normalmente, Hadoop é usado para executar estes tipos de análises.  Com o conector de Hadoop para DocumentDB, bancos de dados de DocumentDB funcionam como fontes de dados e receptores para trabalhos porco, seção e mapa/reduzir. Para obter detalhes sobre o conector de Hadoop para DocumentDB, consulte [executar um trabalho de Hadoop com DocumentDB e HDInsight](documentdb-run-hadoop-with-hdinsight.md).      

## <a name="gaming"></a>Jogos
A camada de banco de dados é um componente fundamental de aplicativos de jogos. Jogos modernos executam processamento gráfico em clientes do console/mobile, mas dependem na nuvem para fornecer conteúdo personalizados como estatísticas do jogo, integração com mídias sociais e pontuações tabelas. Jogos exigem muito menos latências para leituras e gravações para fornecer um envolventes experiência jogo e a camada de banco de dados precisa manipular altos e baixos em taxas de solicitação lançamentos de jogos novos e atualizações de recursos.

DocumentDB é usado por jogos de escala de massa como [a movimentação inativo: Terra sem homem](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) pela [Próxima jogos](http://www.nextgames.com/), e [Halo 5: responsáveis](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Em ambos os casos de uso, as principais vantagens dos DocumentDB foram o seguinte:

- DocumentDB permite desempenho será dimensionada para cima ou para baixo consumidor. Isso permite jogos lidar com o perfil e as estatísticas de dezenas de milhões de manipuladores simultâneos atualização fazendo uma chamada de API única.
- DocumentDB suporta milissegundos leituras e gravações ajudar a evitar qualquer lento durante o jogo.
- A indexação automática do DocumentDB permite filtragem contra várias propriedades diferentes em tempo real, por exemplo, localize players por seu player interno IDs, ou seus GameCenter, Facebook, IDs do Google ou consulta com base na participação de player em um guild. Isso é possível sem a criação de indexação complexos ou infraestrutura de fragmentação.
- Recursos sociais, incluindo mensagens de chat do jogo, associações de guild player, desafios concluídos, pontuações tabelas e gráficos sociais são mais fáceis de implementar com um esquema flexível.
- DocumentDB como um gerenciado plataforma-como um serviço (PaaS) necessário gerenciamento configuração mínima funcionam para permitir a iteração rápida e reduzir o tempo de mercado.


## <a name="user-preferences-data"></a>Dados de preferências do usuário
Atualmente, mais moderna aplicativos web e móveis vêm com experiências e modos de exibição complexos. Esses modos de exibição e experiências são geralmente dinâmicas, buffet para preferências do usuário ou humor e necessidades de identidade visual.  Portanto, aplicativos devem ser capaz de recuperar configurações personalizadas de forma eficaz para renderizar experiências e elementos de interface do usuário rapidamente. 

JSON é um formato eficaz para representar os dados de layout de interface do usuário como não é apenas leve, mas também pode ser facilmente interpretado pelo JavaScript.  DocumentDB oferece níveis de consistência ajustáveis que permitem leituras rápidas com gravações de baixa latência. Portanto, o armazenamento de dados de layout de interface do usuário, incluindo configurações personalizadas como documentos JSON em DocumentDB é um meio eficaz de obter esses dados pela conexão.

## <a name="iot-and-device-sensor-data"></a>Dados de sensor IoT e dispositivo
Casos de uso de IoT comumente compartilham alguns padrões em como eles inclusão, processam e armazenam dados.  Primeiro, esses sistemas permitem entrada de dados que pode acomodar picos de dados de sensores de dispositivo de vários locais.  Em seguida, esses sistemas processam e analisar dados streaming para obter ideias de tempo real. E último mas não menos importante, a maioria se não todos os dados serão eventualmente será levado em um repositório de dados para análise de consulta e offline adhoc.    

Microsoft Azure oferece serviços sofisticados que podem ser utilizados para IoT casos de uso.  Os serviços do Azure IoT são um conjunto de serviços, incluindo Hubs de evento do Azure, DocumentDB do Azure, a análise de fluxo Azure, Hub de notificação do Azure, aprendizado de máquina do Azure, Azurehdinsight e PowerBI. 

Picos de dados podem ser incluídos por Hubs de evento do Azure que oferece inclusão de dados de alta produtividade com baixa latência. Dados incluídos que precisam ser processada para uma visão de tempo real podem ser encaminhados a análise de fluxo de Azure para análise de tempo real. Dados podem ser carregados no DocumentDB para consultar adhoc. Depois que os dados são carregados em DocumentDB, os dados estão prontos para ser consultados.  Os dados em DocumentDB podem ser usados como dados de referência como parte da análise de tempo real. Além disso, os dados ainda mais podem ser refinados e processados pela conexão de dados de DocumentDB ao HDInsight para trabalhos porco, seção ou mapa/reduzir.  Dados refinados são carregados, em seguida, voltar ao DocumentDB por relatar.   

Para um exemplo de solução de IoT usando DocumentDB, EventHubs e tempestade, consulte o [repositório de hdinsight-tempestade-exemplos no GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Para obter mais informações sobre ofertas Azure para IoT, consulte [criar Internet das suas coisas](http://www.microsoft.com/en-us/server-cloud/internet-of-things.aspx).

## <a name="next-steps"></a>Próximas etapas
 
Para começar a usar DocumentDB, você pode criar uma [conta](https://azure.microsoft.com/pricing/free-trial/) e siga nosso [caminho de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para saber mais sobre DocumentDB e encontrar as informações que necessárias. 

Ou, se você quiser ler mais sobre clientes usando DocumentDB, as seguintes histórias de clientes estão disponíveis:

- [Jogos próximos](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). Mortos Walking: jogo sem homem de terra soars para 1 # suportados pelo DocumentDB do Azure.
- [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Como Halo 5 implementado social jogos usando DocumentDB do Azure.
- [Galeria de análise de Cortana](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Galeria de análise de Cortana - um site de comunidade scalable criado no Azure DocumentDB.
- [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Levam integrador oferece uma visão Global de empresas multinacionais em minutos com tecnologias de nuvem flexível.
- [República de notícias](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Adicionando inteligência às notícias para fornecer informações com finalidade para cidadãos envolvidos. 
- [SGS internacional](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Cor consistente em todo o mundo, principais marcas Descubra SGS. E SGS vira Azure.
- [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Líder global Telenor usa a nuvem para mover-se com a velocidade de uma inicialização. 
- [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). O armazenamento do futuro executa em pesquisa rápida e o fácil fluxo de dados.
