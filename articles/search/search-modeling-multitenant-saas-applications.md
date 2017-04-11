<properties
    pageTitle="Modelagem multilocação na pesquisa Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Saiba mais sobre padrões de design comuns para vários locatários aplicativos SaaS durante o uso do Azure pesquisa."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="10/26/2016"
    ms.author="ashmaka"/>

# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Projetar padrões para vários locatários aplicativos SaaS e pesquisa do Azure

Um aplicativo de vários locatários é aquele que fornece os mesmos serviços e recursos para qualquer número de locatários que não consigo ver ou compartilhar os dados de qualquer outro locatário. Este documento discute estratégias de isolamento de locatário para vários locatários aplicativos criados com a pesquisa do Azure.

## <a name="azure-search-concepts"></a>Azure conceitos de pesquisa
Como uma solução de pesquisa-como um serviço, pesquisa Azure permite aos desenvolvedores adicionar experiências de pesquisa avançada para aplicativos sem qualquer infraestrutura de gerenciamento ou se tornar um especialista em pesquisa. Dados são carregados para o serviço e, em seguida, armazenados na nuvem. Usando solicitações simples à API de pesquisa do Azure, os dados podem ser modificados e pesquisados. Uma visão geral do serviço pode ser encontrada [neste](http://aka.ms/whatisazsearch)artigo. Antes de discutir padrões de design, é importante entender alguns conceitos na pesquisa do Azure.

### <a name="search-services-indexes-fields-and-documents"></a>Serviços de pesquisa, índices, campos e documentos
Ao usar a pesquisa do Azure, um se inscreve para um _serviço de pesquisa_. Como dados for carregados para pesquisa do Azure, ela é armazenada em um _índice_ dentro do serviço de pesquisa. Pode haver um número de índices dentro de um único serviço. Para usar os conceitos familiares de bancos de dados, o serviço de pesquisa pode ser comparado a um banco de dados enquanto os índices dentro de um serviço podem ser comparados a tabelas em um banco de dados.

Cada índice dentro de um serviço de pesquisa tem seu próprio esquema, que é definida por um número de _campos_de personalizáveis. Dados são adicionados a um índice de pesquisa do Azure na forma de individuais _documentos_. Cada documento deve ser carregado um índice específico e deve caber esquema desse índice. Ao pesquisar dados usando a pesquisa do Azure, as consultas de pesquisa de texto completo são emitidas em relação a um índice específico.  Para comparar esses conceitos de um banco de dados, campos podem ser comparados a colunas em uma tabela e documentos podem ser comparados a linhas.

### <a name="scalability"></a>Escalabilidade
Qualquer serviço de pesquisa do Azure nos padrão [preços camada](https://azure.microsoft.com/pricing/details/search/) pode aumentar em duas dimensões: armazenamento e disponibilidade.
* _Partições_ podem ser adicionados para aumentar o armazenamento de um serviço de pesquisa.
* _Réplicas_ podem ser adicionados a um serviço para aumentar a produtividade do solicitações que pode lidar com um serviço de pesquisa.

Adicionando e removendo partições e réplicas em permitirá a capacidade do serviço de pesquisa para crescer com a quantidade de dados e as exigências de aplicativo de tráfego. Ordem de um serviço de pesquisa para obter uma leitura [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), ele requer duas réplicas. Ordem de um serviço para alcançar um de leitura e gravação [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), ela requer três réplicas.


### <a name="service-and-index-limits-in-azure-search"></a>Limites de serviço e índice na pesquisa do Azure
Existem em diferentes alguns [níveis de preços](https://azure.microsoft.com/pricing/details/search/) na pesquisa do Azure, cada uma das camadas tem diferentes [cotas e limites](search-limits-quotas-capacity.md). Alguns desses limites são no nível de serviço, alguns estão no nível do índice e alguns estão no nível da partição.


|                                  | Básico     | Standard1   | Standard2   | Standard3   | Standard3 HD  |
|----------------------------------|-----------|-------------|-------------|-------------|---------------|
| Máximos réplicas por serviço     | 3         | 12          | 12          | 12          | 12            |
| Partições máximos por serviço   | 1         | 12          | 12          | 12          | 1             |
| Unidades de pesquisa máximo (réplicas * partições) por serviço | 3         | 36          | 36          | 36          | 36 (máx 3 partições)            |
| Documentos máximos por serviço    | 1 milhão | milhões de 180 | 720 milhões | 1,4 bilhões | 600 milhões   |
| Armazenamento máximo por serviço      | 2 GB      | 300 GB      | 1.2 TB      | 2,4 TB      | 600 GB        |
| Documentos máximos por partição  | 1 milhão | 15 milhões  | 60 milhões  | 120 milhões | 200 milhões   |
| Armazenamento máximo por partição    | 2 GB      | 25 GB       | 100 GB      | 200 GB      | 200 GB        |
| Índices máximos por serviço      | 5         | 50          | 200         | 200         | 3000 (máx 1000 índices/partição)          |


#### <a name="s3-high-density"></a>S3 Alta densidade '
Na camada de preços do Azure da pesquisa S3, há uma opção para o modo de alta densidade (HD) projetado especificamente para vários locatários cenários. Em muitos casos, é necessário oferecer suporte a um grande número de locatários menores em um único serviço para obter os benefícios de eficiência simplicidade e o custo.

S3 HD permite que os índices muitos pequenos ser empacotado sob o gerenciamento de um serviço de pesquisa único por negociação a capacidade de dimensionar índices usando partições para a capacidade para hospedar mais índices em um único serviço.

Concretamente, um serviço de S3 poderia ter entre 1 e 200 índices que juntos podem hospedar até bilhões de 1,4 documentos. Um HD S3 por outro lado seria permitir índices individuais ir apenas até 1 milhão de documentos, mas pode lidar com até 1000 índices por partição (até 3000 por serviço) com uma contagem total do documento de 200 milhões por partição (até 600 milhões por serviço).



## <a name="considerations-for-multitenant-applications"></a>Considerações para vários locatários aplicativos
Aplicativos de vários locatários efetivamente deverá distribuir recursos entre os locatários preservando algum nível de privacidade entre os vários locatários. Há algumas considerações ao criar a arquitetura para esse aplicativo:

* _Isolamento de locatários:_ Desenvolvedores de aplicativo precisam tomar medidas necessárias para garantir que nenhuma locatários tem não autorizado ou indesejado acesso aos dados de outros locatários. Além da perspectiva de privacidade de dados, estratégias de isolamento de locatário exigem gerenciamento eficaz de recursos compartilhados e proteção de ruído vizinhos.
* _Custo de recurso de nuvem:_ Como com qualquer outro aplicativo, soluções de software devem permanecer competitivo como um componente de um aplicativo de vários locatários do custo.
* _Facilidade de operações:_ Ao desenvolver uma arquitetura de vários locatários, o impacto sobre operações e a complexidade do aplicativo é uma consideração importante. Pesquisa Azure tem um [SLA de 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* _Espaço global:_ Aplicativos de vários locatários talvez precise efetivamente servir locatários que são distribuídos em todo o mundo.
* _Escalabilidade:_ Os desenvolvedores de aplicativos precisam considerar como eles reconciliar entre manter um nível suficientemente baixo de complexidade de aplicativos e projetar o aplicativo dimensionar com número de locatários e o tamanho de dados e a carga de trabalho dos locatários.

Pesquisa Azure oferece alguns limites que podem ser usados para isolar dados e a carga de trabalho dos locatários.

## <a name="modeling-multitenancy-with-azure-search"></a>Modelagem multilocação com a pesquisa do Azure
No caso de um cenário de vários locatários, o desenvolvedor do aplicativo consome um ou mais serviços de pesquisa e dividir seus locatários entre os serviços, índices ou ambos. Pesquisa Azure tem alguns padrões comuns ao modelar um cenário com vários locatários:

1. _Índice por locação:_ Cada locatário tem seu próprio índice dentro de um serviço de pesquisa que é compartilhado com outros locatários.
1. _Serviço por locação:_ Cada locatário tem seu próprio serviço de pesquisa do Azure dedicado, oferecendo mais alto nível de separação de dados e a carga de trabalho.
1. _Combinação de ambas:_ Locatários maiores e mais ativos são atribuídos serviços dedicados enquanto locatários menores são atribuídos índices individuais dentro de serviços compartilhados.

## <a name="1-index-per-tenant"></a>1. indexar por locatário
![Um portrayal do modelo de índice por locatário](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

Em um modelo de índice por locatário, vários locatários ocupam um único serviço de pesquisa do Azure onde cada locatário tem seu próprio índice.

Locatários atingir isolamento de dados porque todos os solicitações de pesquisa e operações de documento são emitidas em um nível de índice na pesquisa do Azure. Na camada de aplicativo, há o reconhecimento de necessidade para direcionar o tráfego a vários locatários para os índices PRI enquanto também gerenciar recursos do nível de serviço em todos os locatários.

Um atributo chave de modelo de índice por locatário é a capacidade de desenvolvedor do aplicativo a receber assinaturas demais a capacidade de um serviço de pesquisa entre locatários do aplicativo. Se os locatários tem uma distribuição desigual de carga de trabalho, a combinação ideal de locatários pode ser distribuída entre índices de um serviço de pesquisa para acomodar um número de locatários altamente ativos, que requer muita recurso ao servir simultaneamente uma cauda longa de locatários menos ativos. A desvantagem é a impossibilidade do modelo de lidar com situações em que cada locatário simultaneamente é altamente ativo.

O modelo de índice por locatário fornece a base para um modelo de custo variável, onde um serviço de pesquisa do Azure inteiro é comprou prévio e subsequentemente preenchido com os locatários. Isso permite capacidade não utilizada para ser designado para tentativas e contas gratuitas.

Para aplicativos com um espaço global, o modelo de índice por locatário pode não estar mais eficiente. Se os locatários do aplicativo são distribuídos em todo o mundo, um serviço separado pode ser necessário para cada região que pode duplicar custos em cada um deles.

Pesquisa Azure permite a escala de índices individuais e o número total de índices para crescer. Se um preço apropriado nível é escolhido, partições e réplicas podem ser adicionadas ao serviço de pesquisa de inteiro quando um índice individual dentro do serviço fica muito grande em termos de armazenamento ou tráfego.

Se o número total de índices crescer muito grande para um único serviço, outro serviço deve ser provisionado para acomodar os novo locatários. Se tem índices para serem movidos entre os serviços de pesquisa, conforme são adicionados novos serviços, os dados do índice tem sejam copiados manualmente um índice para outra como pesquisa do Azure não permite que um índice a ser movida.


## <a name="2-service-per-tenant"></a>2. serviço por locatário
![Um portrayal do modelo de serviço por locatário](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

Em uma arquitetura de serviço por locatário, cada locatário tem seu próprio serviço de pesquisa.

Nesse modelo, o aplicativo atinge o nível de isolamento para seus locatários máximo. Cada serviço tem dedicado de armazenamento e produtividade para lidar com a solicitação de pesquisa, além de chaves de API separadas.

Para aplicativos onde cada locatário tem um grande espaço ou a carga de trabalho variabilidade pouco do locatário para locatário, o modelo de serviço por locatário é uma opção adequada como recursos não são compartilhados entre cargas de trabalho dos vários locatários.

Um serviço por modelo de locatário também oferece o benefício de um modelo de custo fixo, previsíveis. Não há nenhum investimento inicial em um serviço de pesquisa toda até que haja um locatário para preenchê-lo, mas o custo por locatário é maior do que um modelo de índice por locatário.

O modelo de serviço por locatário é uma opção eficiente para aplicativos com um espaço global. Com os locatários distribuídos geograficamente, é fácil ter o serviço do cada locatário na região apropriado.

Os desafios em dimensionamento esse padrão surgirem quando locatários individuais mais atender o crescimento seus serviços. Pesquisa Azure não suportamos atualizando o nível de preço de um serviço de pesquisa, para que todos os dados precisaria ser copiados manualmente para um novo serviço.

## <a name="3-mixing-both-models"></a>3. misturar ambos os modelos
Outro padrão para modelagem multilocação é misturar estratégias índice por locatário e serviço por locatário.

Unindo os dois padrões, locatários maior do aplicativo podem ocupar serviços dedicados enquanto cauda longa da locatários menos ativos, menores pode ocupar índices em um serviço compartilhado. Esse modelo garante que os locatários maior tenham consistentemente alto desempenho do serviço e ajudar a proteger os locatários menores de qualquer ruídos vizinhos.

No entanto, a implementação essa estratégia se baseia antecipação prever quais locatários exigem um serviço dedicado versus um índice em um serviço compartilhado. Complexidade de aplicativo aumenta com a necessidade de gerenciar ambos desses modelos multitenancy.

## <a name="achieving-even-finer-granularity"></a>Obtendo mais precisos detalhamento
Os padrões de design acima para modelar cenários vários locatários no Azure pesquisa presumem um escopo uniforme onde cada locatário é uma instância inteira de um aplicativo. No entanto, aplicativos às vezes podem lidar com vários escopos menores.

Se o serviço por locatário e índice por locatário modelos não estão suficientemente pequenos escopos, é possível um índice para alcançar um grau mais precisos de detalhamento de modelo.

Para fazer com que um único índice se comportam de forma diferente de pontos de extremidade do cliente diferente, é possível adicionar um campo a um índice que designa um determinado valor para cada cliente possível. Sempre que um cliente chama pesquisa do Azure para consultar ou modificar um índice, o código do aplicativo cliente especifica o valor apropriado para esse campo usando o recurso de [filtro](https://msdn.microsoft.com/library/azure/dn798921.aspx) do Azure pesquisa em tempo de consulta.

Este método pode ser usado para obter funcionalidade de contas de usuário separadas, níveis de permissão separados e até mesmo completamente separar aplicativos.

## <a name="next-steps"></a>Próximas etapas
Pesquisa Azure é uma escolha atraente para muitos aplicativos, [Leia mais sobre robustos do serviço](http://aka.ms/whatisazsearch). Ao avaliar os diversos padrões de design para aplicativos vários locatários, considere os [vários níveis de preços](https://azure.microsoft.com/pricing/details/search/) e os respectivos [limites de serviço](search-limits-quotas-capacity.md) para adaptar melhor a pesquisa do Azure para ajustá-la cargas de trabalho do aplicativo e arquiteturas de todos os portes.

Perguntas sobre pesquisa do Azure e cenários de vários locatários podem ser direcionadas para azuresearch_contact@microsoft.com.
