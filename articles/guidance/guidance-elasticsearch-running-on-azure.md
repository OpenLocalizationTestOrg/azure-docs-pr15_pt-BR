<properties
   pageTitle="Executando Elasticsearch no Azure | Microsoft Azure"
   description="Como instalar, configurar e executar Elasticsearch no Azure."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>

# <a name="running-elasticsearch-on-azure"></a>Elasticsearch em execução no Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo é [parte de uma série](guidance-elasticsearch.md). 

## <a name="overview"></a>Visão geral

Este documento fornece uma breve introdução a estrutura geral do Elasticsearch e, em seguida, descreve como você pode implementar um cluster de Elasticsearch uso do Azure. Ele toca em práticas recomendadas para implantar um cluster de Elasticsearch, concentrando-se no desempenho funcional e requisitos de gerenciamento do seu sistema e considerando como seus requisitos devem orientar a configuração e a topologia que você selecionar diversos.

> [AZURE.NOTE] Este guia pressupõe familiaridade básica com [Elasticsearch][].

## <a name="the-structure-of-elasticsearch"></a>A estrutura de Elasticsearch 

Elasticsearch é um banco de dados do documento altamente otimizado para agir como um mecanismo de pesquisa. Documentos são serializados no formato JSON. Dados são mantidos em índices, implementados usando [Apache Lucene][], embora os detalhes são removidos de modo de exibição e não é necessário compreender totalmente Lucene para poder usar Elasticsearch.

### <a name="clusters-nodes-indexes-and-shards"></a>Clusters, nós, índices e fragmentos

Elasticsearch implementa uma arquitetura de cluster que usa fragmentação para distribuir dados em vários nós e replicação para oferecer alta disponibilidade. Documentos são armazenados em índices. O usuário pode especificar quais campos em um documento são usados para identificá-lo exclusivamente dentro de um índice ou o sistema pode gerar um campo de chave e valores automaticamente. O índice é usado para física organizar documentos e é que o principal significa para localizar documentos. 

Um índice contém um conjunto de fragmentos. Documentos são distribuídos uniformemente fragmentos usando um mecanismo de hash com base em valores-chave índice e o número de fragmentos no índice. 

Os índices podem ser replicados. Nesse caso, cada fragmentar no índice é copiada. Elasticsearch garante que cada fragmentar original para um índice (referido como um "fragmentar primária") e sua réplica sempre residem em nós diferentes. Quando um documento é adicionado ou modificado, todas as gravação operações são executadas a fragmentar primária primeiro e, em seguida, em cada réplica. 

A figura a seguir mostra os aspectos essenciais de um cluster de Elasticsearch que contém três nós. Foi criado um índice que consiste em dois fragmentos principais com dois réplicas para cada fragmentar (seis fragmentos em todos).

![](media/guidance-elasticsearch/general-cluster1.png)

*Um cluster de Elasticsearch simples que contém dois nós primários e dois conjuntos de réplicas*

Neste cluster, fragmentar primária 1 e fragmentar primária 2 estão localizados em nós separados para ajudar a saldo a carga entre eles. As réplicas são distribuídas de maneira semelhante. Se um único nó falhar, os nós restantes tem informações suficientes para ativar o sistema continuar funcionando. Se necessário, Elasticsearch será promover uma fragmentar réplica para se tornar um fragmentar primária se a fragmentar primária correspondente não estiver disponível.

### <a name="node-roles"></a>Funções de nó

Os nós em um cluster de Elasticsearch podem executar as seguintes funções:

- Um **nó de dados** que pode conter um ou mais fragmentos que contêm dados de índice.

- Um **nó do cliente** que não armazenam dados de índice, mas que lida com solicitações de entrada feitas por aplicativos de cliente para o nó de dados apropriado.
 
- Um **nó mestre** não contém dados de índice, mas que efetua operações de gerenciamento de cluster, como manter e distribuir informações de roteamento em torno de cluster (a lista dos quais nós contêm quais fragmentos), determinar quais nós estão disponíveis, realocação fragmentos como nós apareçam e desaparecem e coordenar recuperação após falha de nó. Vários nós podem ser configurados como mestres, mas somente um será realmente ser decidiu executar as funções mestres. Se esse nó falhar, outra eleição ocorrerá e um dos outros nós mestres qualificados será ser escolhido e assumir.

> [AZURE.NOTE]O nó mestre lecionado é essencial para o bem-estar do cluster. Outros nós ping-regularmente para garantir que ele ainda está disponível. Se o nó mestre lecionado também está atuando como um nó de dados, há uma chance de que o nó pode se tornar ocupado e falhas responder a estas ping. Nessa situação, o mestre é considerado falha e um dos outros nós mestres é escolhido em seu lugar. 

 A figura a seguir mostra uma topologia contendo uma mistura de nós de dados em um cluster de Elasticsearch, cliente e mestre dedicado.

![](media/guidance-elasticsearch/general-cluster2.png)

*Um cluster de Elasticsearch mostrando diferentes tipos de nós*

### <a name="costs-and-benefits-of-using-client-nodes"></a>Custos e benefícios do uso de nós de cliente

Quando um aplicativo envia uma consulta para um cluster de Elasticsearch, o nó ao qual o aplicativo se conecta é responsável por dirigir o processo de consulta. O nó encaminha a solicitação para cada nó de dados e reúne os resultados, retornando as informações acumuladas para o aplicativo. Se uma consulta envolve agregações e outros cálculos, o nó ao qual o aplicativo se conecta executa as operações necessárias após recuperar os dados de cada um dos outros nós. Esse processo de dispersão/coletar pode consumir recursos de memória e processamento consideráveis.

O uso de nós clientes dedicada para realizar estas tarefas permite nós de dados enfocar gerenciamento e armazenamento de dados. O resultado é que muitos cenários que envolvem agregações e consultas complexas podem se beneficiar usando nós de cliente dedicado. No entanto, o impacto do uso de nós clientes dedicado provavelmente variará dependendo do seu cenário, a carga de trabalho e o tamanho de cluster. 

> [AZURE.NOTE] Consulte [ajuste de agregação de dados e desempenho de consulta para Elasticsearch no Azure][] para obter mais informações sobre o processo de ajuste.

### <a name="connecting-to-a-cluster"></a>Conectando a um cluster

Elasticsearch expõe uma série de APIs REST para criação de aplicativos cliente e enviando solicitações para um cluster. Se você está desenvolvendo aplicativos usando o .NET Framework, dois níveis superiores APIs estão disponíveis — [Elasticsearch.Net & ANINHAR][].

Se você estiver criando aplicativos cliente usando Java, você pode usar a [API do cliente de nó][] criar cliente nós dinamicamente e adicioná-los ao cluster. Criando cliente nós dinamicamente é conveniente se seu sistema usa um número relativamente pequeno de conexões de vida longa. Nós de cliente criados usando a API de nó são fornecidos com o cluster roteamento mapear (os detalhes dos quais nós contêm quais fragmentos) o nó mestre. Essas informações habilita o aplicativo de Java conectar-se diretamente para os nós apropriados quando indexação ou consultando dados, reduzindo o número de saltos que podem ser necessários ao usar outras APIs.

O custo dessa abordagem é a sobrecarga de registrar o nó de cliente em cluster. Se um grande número de nós clientes aparece e desaparecem rapidamente, o impacto da manutenção e distribuir o mapa de roteamento de cluster pode se tornar significativo.

A figura a seguir mostra uma configuração que usa um balanceador de carga para rotear solicitações para um conjunto de nós do cliente, embora a mesma estratégia pode ser usada para conectar-se diretamente para nós de dados se nós de cliente não são usados.

![](media/guidance-elasticsearch/general-clientappinstances.png)

*Instâncias do aplicativo cliente se conectar a um cluster de Elasticsearch por meio do balanceador de carga do Azure*

> [AZURE.NOTE]Você pode usar o [Balanceador de carga do Azure][] para expor o cluster à Internet pública, ou você pode usar um [balanceador de carga interno][] se a aplicativos cliente e o cluster estão contidos inteiramente a rede privada virtual (VNet) mesma.

### <a name="node-discovery"></a>Descoberta de nó

Elasticsearch é baseado em comunicações ponto-a-ponto, portanto descobrir outros nós em um cluster é uma parte importante no ciclo de vida de um nó. Detecção de nó permite que novos nós de dados a serem adicionados dinamicamente a um cluster, que permite o cluster dimensionar transparente. Além disso, se um nó de dados falhar ao responder a solicitações de comunicações de outros nós, um nó mestre pode decidir que o nó de dados falhou e executar as etapas necessárias para realocar os fragmentos que ele foi mantendo a outros nós de dados operacionais.

Descoberta de nó Elasticsearch é tratada usando um módulo de detecção. O módulo de detecção é um plug-in que poderá ser mudado para usar um mecanismo de descoberta diferentes. O módulo de descoberta de padrão ([Zen][]) faz com que um nó emitir solicitações de ping para encontrar outros nós na mesma rede. Se outros nós responderem, eles fofocar para trocar informações. Um nó mestre pode distribuir fragmentos para o novo nó (se for um nó de dados) e rebalancear cluster. O módulo de descoberta Zen também trata o processo de eleição mestre e o protocolo para detectar falha de nó.

Além disso, se você estiver executando Elasticsearch nós como Azure VMs (máquinas virtuais), mensagens de difusão seletiva não é suportada. Por este motivos, você deve configurar a descoberta de Zen para usar o sistema de mensagens de difusão ponto a ponto e forneça uma lista de nós de contato válidos no arquivo de configuração elasticsearch.yml.

Se você estiver hospedando um cluster Elasticsearch dentro de uma rede virtual Azure, você pode especificar que particular IP atribuído pelo DHCP endereçada fornecidas para cada máquina virtual do cluster deve permanecer alocada (estática). Você pode configurar Zen descoberta difusão ponto a ponto mensagens usando esses endereços IP estáticos. Se você estiver usando VMs com endereços IP dinâmicos, tenha em mente que se uma máquina virtual parar e reinicia ela poderia ser alocada um novo endereço IP tornando descoberta mais difícil. Para manipular esse cenário, você pode trocar o módulo de descoberta Zen para o [Plug-in do Azure nuvem][]. Este plug-in usa a API do Azure para implementar o mecanismo de descoberta, que é baseado nas informações de assinatura Azure.

> [AZURE.NOTE]A versão atual do plug-in do Azure nuvem requer que você instale o certificado de gerenciamento para sua assinatura do Azure no armazenamento de chaves Java no nó Elasticsearch e forneça o local e as credenciais para acessar o repositório de chaves no arquivo elasticsearch.yml. Esse arquivo é mantido em texto não criptografado, portanto, é vital importante que você verifique esse arquivo só é acessível pela conta executando o serviço de Elasticsearch. 
> 
> Além disso, essa abordagem pode não ser compatível com implantações do Gerenciador de recursos do Azure. Por esses motivos, é recomendável que você use endereços IP estáticos para nós mestres e usa esses nós para implementar o Zen difusão ponto a ponto descoberta de mensagens no cluster. Na configuração abaixo (obtida do arquivo elasticsearch.yml de um nó de dados de exemplo), os endereços IP de host referência mestra nós no cluster:

```yaml
discovery.zen.ping.multicast.enabled: false  
discovery.zen.ping.unicast.hosts: ["10.0.0.10","10.0.0.11","10.0.0.12"]
```

## <a name="general-system-guidelines"></a>Diretrizes gerais do sistema

Elasticsearch podem ser executados em vários computadores, desde um único laptop para um cluster de servidores de alto nível. No entanto, os recursos mais em termos de memória, computação de alimentação e rápida discos que estão disponível melhor o desempenho. As seções a seguir resumem os requisitos de hardware e software básicos para execução de Elasticsearch.

### <a name="memory-requirements"></a>Requisitos de memória 

Elasticsearch tenta armazenar dados na memória para velocidade. Um servidor de produção hospedagem de um nó para uma corporativo típico ou o tamanho moderado comercial implantação do Azure deve ter entre 14 e 28GB de RAM (D3 ou D4 VMs). **Distribuir a carga entre nós mais em vez de criar nós com mais memória** (Experiências mostram que o uso de nós maiores com mais memória pode causar tempos de recuperação estendida em caso de falha.) No entanto, embora a criação de clusters com um número muito grande de nós pequenos pode aumentar a disponibilidade e produtividade, ele também escala o esforço envolvido no gerenciamento e manutenção de um sistema desse tipo.

**Alocar 50% da memória disponível em um servidor para a pilha de Elasticsearch**. Se você estiver usando o conjunto de Linux a variável de ambiente ES_HEAP_SIZE antes de executar Elasticsearch. Como alternativa, se você estiver usando o Windows ou Linux, você pode especificar tamanho da memória na `Xmx` e `Xms` parâmetros ao iniciar Elasticseach. Defina ambos os parâmetros para o mesmo valor para evitar a máquina Virtual de Java (JVM) redimensionando a pilha em tempo de execução. No entanto, **não alocar mais de 30 GB**. Use a memória restante para o cache de arquivo do sistema operacional.

> [AZURE.NOTE]Elasticsearch utiliza a biblioteca Lucene para criar e gerenciar índices. Estruturas de Lucene usam um formato baseado em disco e essas estruturas no cache do sistema de arquivos de cache irá melhorar o desempenho.

Observe que o tamanho máximo de pilha ideal para Java em uma máquina de 64 bits é logo acima de 30GB. Acima desse tamanho que Java alterna para usando um mecanismo de estendido para objetos na pilha de referência, que aumenta os requisitos de memória para cada objeto e reduz o desempenho. 

O coletor de lixo padrão Java (marcar simultânea e limpar) também pode executar abaixo do ideal se o tamanho da pilha estiver acima de 30 GB. Atualmente não é recomendável alternar para um coletor de lixo diferentes como Elasticsearch e Lucene só foi testada contra o padrão.

Não overcommit a memória conforme trocando memória principal para o disco afetará seriamente o desempenho. Se possível, desabilitar trocando completamente (os detalhes dependem do sistema operacional). Se isso não for possível habilite a configuração de *mlockall* no arquivo de configuração Elasticsearch (elasticsearch.yml) da seguinte maneira:

```yaml
bootstrap.mlockall: true
```

Esta configuração faz com que a JVM bloquear sua memória e impede que ele sendo trocados pelo sistema operacional.

### <a name="disk-and-file-system-requirements"></a>Requisitos de sistema de arquivos e disco

Use discos de dados copiados pelo armazenamento premium para armazenar fragmentos. Discos devem ser dimensionados para manter a quantidade máxima de dados previstos para seus fragmentos, embora seja possível adicionar mais discos posteriormente. Você pode estender um fragmentar em vários discos em um nó.

> [AZURE.NOTE]Elasticsearch compacta os dados de campos armazenados usando o algoritmo de LZ4 e no Elasticsearch 2.0 em diante, você pode alterar o tipo de compactação. Você pode mudar o algoritmo de compactação DEFLATE como usado pelos utilitários *zip* e *gzip* . Essa técnica de compactação pode usar mais recursos, mas você deve considerar a usá-lo para dados de log arquivados. Essa abordagem pode ajudar a reduzir o tamanho do índice.

Não é essencial que todos os nós em um cluster têm o mesmo layout de disco e capacidade. No entanto, um nó com uma capacidade de disco muito grande em comparação com outros nós em um cluster será atrair mais dados e exige capacidade de processamento para lidar com esses dados. Consequentemente o nó pode se tornar "quente" comparado a outros nós e por sua vez, isso pode afetar o desempenho.

Se possível, use RAID 0 (faixas). Outras formas de RAID que implementam paridade e espelhamento são desnecessárias como Elasticsearch fornece sua própria solução de alta disponibilidade no formulário de réplicas.

> [AZURE.NOTE]Antes de Elasticsearch 2.0.0, você também pode implementar distribuição no nível de software, especificando várias pastas na configuração *path.data* configuração. No Elasticsearch 2.0.0, já não há suporte para essa forma de distribuição. Em vez disso, fragmentos diferentes podem ser alocados a caminhos diferentes, mas todos os arquivos em uma única fragmentar serão gravados para o mesmo caminho. Se você precisar de distribuição, você deve distribuição dados no nível de hardware do sistema operacional ou. 

Maximizar a taxa de transferência de armazenamento, cada **máquina virtual deve ter uma conta de armazenamento dedicado premium**.

A biblioteca de Lucene pode usar um grande número de arquivos para armazenar dados de índice e Elasticsearch pode abrir um número significativo de sockets para comunicação entre nós e com clientes. Certifique-se de que o sistema operacional está configurado para oferecer suporte a um número adequado de descritores de arquivo aberto (até 64000 se memória suficiente estiver disponível). Observe que a configuração padrão para muitas distribuições do Linux limita o número de descritores de abrir arquivo para 1024, que é muito muito pequeno.

Elasticsearch usa uma combinação de memória mapeada (mmap) e/s e Java nova e/s (NIO) para otimizar acesso simultâneo aos arquivos de dados e índices. Se você estiver usando o Linux, você deve configurar o sistema operacional para garantir que haja memória virtual suficiente disponível com espaço para áreas de mapa de memória de 256K.

> [AZURE.NOTE]Muitos padrão de distribuições Linux usando o Agendador de (CFQ) fila completamente feira ao organizar para gravar dados em disco. Este Agendador não é otimizada para SSDs. Considere a possibilidade de reconfigurar o sistema operacional para usar o Agendador NOOP ou o Agendador de prazo, que são mais eficazes SSDS.

### <a name="cpu-requirements"></a>Requisitos da CPU

Azure VMs estão disponíveis em uma variedade de configurações de CPU, suporte entre 1 e 32 cores. Para um nó de dados, um bom ponto de partida é uma máquina virtual de série DS padrão e selecione um do DS3 (4 cores) ou D4 (8 cores) SKUs. O DS3 também fornece 14GB de RAM, enquanto o DS4 inclui 28GB. 

O GS séries (para armazenamento de premium) e G-(para armazenamento padrão) usam processadores Xeon E5 V3 que podem ser útil para cargas de trabalho que são muito pesados, como agregações em grande escala. Para obter as informações mais recentes, visite [tamanhos para máquinas virtuais][].

### <a name="network-requirements"></a>Requisitos de rede

Elasticsearch requer uma largura de banda de rede de entre 1 e 10 Gbps, dependendo do tamanho e volatilidade dos clusters que ele implementa. Elasticsearch migra fragmentos entre nós medida mais nós são adicionados a um cluster. Elasticsearch pressupõe que o tempo de comunicação entre todos os nós equivalente e não considera os locais relativos dos fragmentos contidos em nós. Além disso, replicação pode provoca i / de rede significativa entre fragmentos. Por estes motivos, **Evite criar clusters em nós que estão em diferentes regiões**.

### <a name="software-requirements"></a>Requisitos de software

Você pode executar Elasticsearch no Windows ou no Linux. O serviço de Elasticsearch é implantado como uma biblioteca de jar Java e possui dependências em outras bibliotecas Java que estão incluídas no pacote Elasticsearch. Você deve instalar o Java 7 (atualização 55 ou posterior) ou Java 8 (atualização de 20 ou posterior) JVM para executar Elasticsearch.

> [AZURE.NOTE]Diferente dos parâmetros de memória *Xmx* e *Xms* (especificado como opções de linha de comando para o mecanismo de Elasticsearch – consulte [requisitos de memória][]) não modifique as configurações de JVM padrão. Elasticsearch foi criado usando os padrões; transforma pode causar Elasticsearch para se tornar detuned e executar mal.

### <a name="deploying-elasticsearch-on-azure"></a>Implantando Elasticsearch no Azure

Embora não seja difícil implantar uma única instância do Elasticsearch, criando um número de nós e instalando e configurando Elasticsearch em cada uma delas pode ser um processo demorado e propensa. Se você estiver considerando executando Elasticsearch no Azure VMs, você tem três opções que podem ajudar a reduzir as chances de erros.

- Usando o Gerenciador de recursos do Azure [modelo](https://azure.microsoft.com/marketplace/partners/elastic/elasticsearchelasticsearch/) no Azure marketplace. Este modelo é criado pela elástica. Ele permite que você adicione aprimoramentos comerciais como proteção, Marvel, Inspetor e assim por diante.

- Usando o início rápido Azure [modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) para criar o cluster. Este modelo pode criar um cluster com base no Windows Server 2012 ou Ubuntu Linux 14.0.4. Ele permite que você use experimentais recursos como armazenamento de arquivos do Azure. Este modelo é usado para a pesquisa e tarefas de teste neste documento.

- Uso de scripts que podem ser executados de maneira autônoma ou automatizados. Scripts que podem criar e implantar um cluster de Elasticsearch estão disponíveis no [repositório de GitHub][elasticsearch-scripts]

## <a name="cluster-and-node-sizing-and-scalability"></a>Cluster e dimensionamento de nó e escalabilidade 

Elasticsearch permite que um número topologias de implantação, projetados para dar suporte a requisitos diferentes e níveis de escala. Esta seção descreve algumas topologias comuns e descreve as considerações para implementar clusters com base nessas topologias.

### <a name="elasticsearch-topologies"></a>Elasticsearch topologias

A figura a seguir ilustra um ponto de partida para criar uma topologia de Elasticsearch para Azure:

![](media/guidance-elasticsearch/general-startingpoint.png)

*Sugeridos ponto de partida para criar um cluster de Elasticsearch com o Azure*

Essa topologia contém seis nós de dados junto com três nós de clientes e três nós mestres (somente um nó mestre é selecionada, as outras duas estão disponíveis para eleição o mestre lecionado falhar.) Cada nó é implementado como uma máquina virtual separada. Aplicativos web Azure são direcionados para nós do cliente por meio de um balanceador de carga. 

Neste exemplo, todos os nós e os aplicativos web residem na mesma rede virtual que os isola efetivamente do mundo exterior. Se o cluster precisa estar disponíveis externamente (possivelmente como parte de uma solução híbrida incorporando os clientes locais), você pode usar o balanceador de carga do Azure para fornecer um endereço IP público, mas você precisa tomar as precauções de segurança adicionais para impedir o acesso não autorizado ao cluster. 

"Saltar caixa opcional" é uma máquina virtual que está disponível somente para administradores. Esta máquina virtual tem uma conexão de rede para a rede virtual, mas também uma para fora opostas conexão de rede para permitir o logon do administrador de uma rede externa (esse logon deve ser protegido usando uma senha forte ou um certificado). Um administrador pode efetuar logon na caixa saltar e conecte a partir daí diretamente para qualquer um de nós no cluster. 

Abordagens alternativas incluem usando uma VPN to-site entre uma organização e a rede virtual ou circuitos [rota expressa][] para se conectar à rede virtual. Esses mecanismos permitem o acesso administrativo ao cluster sem expor cluster à internet pública.

Para manter a disponibilidade de máquina virtual, os nós de dados são agrupados no mesmo conjunto de disponibilidade Azure. Da mesma forma, os nós de cliente são mantidos em outro conjunto de disponibilidade e os nós mestres são armazenados em um terceiro conjunto de disponibilidade.

Essa topologia é relativamente fácil dimensionar, basta adicionar mais nós do tipo apropriado e verifique se eles estão configurados com o mesmo nome de cluster no arquivo elasticsearch.yml. Nós clientes também precisam ser adicionadas ao pool de back-end para o balanceador de carga Azure.

**Localização geográfica clusters**

**Não distribuídas nós em um cluster em regiões como isso pode afetar o desempenho da comunicação entre nós** (consulte [requisitos de rede][]). Dados de localização geográfica Fechar para usuários em diferentes regiões requerem a criação de vários clusters. Nessa situação, você precisa considerar como (ou até mesmo se) para sincronizar clusters. Soluções possíveis incluem:

[Nós tribo][] são semelhantes a um nó de cliente, exceto que ele pode participar de vários clusters Elasticsearch e visualizá-las todas como um cluster grande. Dados ainda são gerenciados localmente por cada cluster (atualizações não são propagadas limites de cluster), mas todos os dados está visível. Um nó tribo pode consultar, criar e gerenciar documentos em qualquer cluster. 

As restrições principais são que um nó tribo não pode ser usado para criar um novo índice e nomes de índice devem ser exclusivos em todos os clusters. Portanto é importante considerar como índices serão nomeados ao criar clusters deva ser acessado de nós tribo.

Usando este mecanismo, cada cluster pode conter os dados que provavelmente ser acessada por aplicativos cliente local, mas esses clientes ainda poderão acessar e modificar dados remotos embora com possível estendido latência. A figura a seguir mostra um exemplo dessa topologia. O nó tribo em Cluster 1 é realçado; os outros clusters também podem ter nós tribo Embora estes não são mostradas no diagrama:

![](media/guidance-elasticsearch/general-tribenode.png)

*Um aplicativo cliente acessando vários clusters através de um nó tribo*

Neste exemplo, o aplicativo cliente se conecta a nó tribo em Cluster 1 (localizada na mesma região), mas este nó está configurado para poder acessar Cluster 2 e 3 de Cluster, que possam estar localizados em diferentes regiões. O aplicativo cliente pode enviar solicitações que recuperar ou modifiquem dados em qualquer um dos clusters.

> [AZURE.NOTE]Nós tribo requerem descoberta seletiva para se conectar a clusters, que podem apresentar um problema de segurança. Consulte a seção [descoberta de nó][] para obter mais detalhes.

- Implementando replicação geográfica entre clusters. Nesta abordagem, as alterações feitas em cada cluster são propagadas quase em tempo real para clusters localizados em outros centros de dados. Plug-ins de terceiros estão disponíveis para Elasticsearch que oferecem suporte a essa funcionalidade, como o [Plug-in de alterações de PubNub][].

- Usando o [módulo de restauração e Elasticsearch instantâneo][]. Se os dados é muito lenta-movendo e são modificados apenas por um único cluster, você pode considerar o uso de instantâneos tirar uma cópia periódica dos dados e restaurar esses instantâneos em outros clusters (instantâneos podem ser armazenados no armazenamento de Blob do Azure se você instalou o [Plug-in do Azure nuvem][]). No entanto, essa solução não funciona bem para alterar rapidamente os dados ou se os dados podem ser alterados em mais de um cluster.

**Topologias pequena**

Grande escala topologias incluindo clusters de nós dedicada de mestre, cliente e dados podem não ser apropriadas para cada cenário. Se você estiver criando um sistema de desenvolvimento ou produção pequena, considere o cluster de 3 nós mostrado na figura abaixo.

Aplicativos cliente conectam diretamente a qualquer nó de dados disponíveis no cluster. O cluster contém três fragmentos rotulados P1-P3 (para permitir crescimento) mais réplicas rotuladas R1-R3. Usar três nós possibilita Elasticsearch distribuir a fragmentos e réplicas para que se qualquer único nó falhar sem dados serão perdidos.

![](media/guidance-elasticsearch/general-threenodecluster.png)

*Um cluster de 3 nós com 3 fragmentos e réplicas*

Se você estiver executando uma instalação de desenvolvimento em um computador autônomo que você pode configurar um cluster com um único nó que atua como mestre, cliente e armazenamento de dados. Como alternativa, você pode iniciar vários nós executados como um cluster no mesmo computador iniciando mais de uma instância de Elasticsearch. A figura a seguir mostra um exemplo.

![](media/guidance-elasticsearch/general-developmentconfiguration.png)

*Uma configuração de desenvolvimento executando vários nós Elasticsearch na mesma máquina*

Observe que nenhuma dessas configurações autônomo são recomendadas para um ambiente de produção, pois eles podem causar disputa, a menos que sua máquina de desenvolvimento tem uma quantidade significativa de memória e vários discos rápidos. Além disso, eles não fornecem que qualquer alta disponibilidade garante. Se o computador falhar, todos os nós são perdidos.

### <a name="scaling-a-cluster-and-data-nodes"></a>Dimensionamento de um nó de cluster e dados

Elasticsearch pode aumentar em duas dimensões: verticalmente (usando maiores e mais eficazes máquinas) e horizontalmente (disseminação a carga em máquinas).

**Dimensionamento nós de dados Elasticsearch verticalmente**

Se você estiver hospedando um cluster Elasticsearch usando Azure VMs, cada nó pode corresponder a uma máquina virtual. O limite de redimensionamento vertical de um nó amplamente é regulado pelo SKU da máquina virtual e as restrições gerais aplicadas a contas individuais de armazenamento e assinaturas do Azure. 

A página [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md) descreve esses limites em detalhes, mas como diz respeito a criação de um cluster de Elasticsearch, os itens na lista a seguir são as mais pertinentes. 

- Cada conta de armazenamento é restrita para 20.000 IOPS. Cada máquina virtual do cluster deve aproveitar dedicado (preferência premium) conta de armazenamento.

- O número de nós de dados em uma rede virtual. Se você não estiver usando o Gerenciador de recursos do Azure, há um limite de 2048 instâncias de máquina virtual por rede virtual. Enquanto isso deve comprovar suficiente para muitos casos, se você tiver uma configuração muito grande com milhares de nós pode ser uma limitação.

- Número de contas de armazenamento por assinatura por região. Você pode criar até 100 contas de armazenamento por assinatura Azure em cada região. Contas de armazenamento são usadas para manter discos virtuais e cada conta de armazenamento tem um limite de 500TB de espaço.

- Número de cores por assinatura. O limite padrão é 20 cores por assinatura, mas isso pode ser aumentado até 10.000 cores solicitando um aumento de limite por meio de um tíquete. 

- A quantidade de memória por tamanho de máquina virtual. Menor tamanho VMs quantidades limitadas de memória disponível (D1 máquinas ter 3,5 GB e D2 máquinas tiver 7 GB). Essas máquinas talvez não seja adequadas para cenários que exigem Elasticsearch em cache significativas quantidades de dados para obter um bom desempenho (agregar dados ou analisar um grande número de documentos durante a inclusão de dados, por exemplo).

- O número máximo de discos por tamanho de máquina virtual. Essa restrição pode limitar o tamanho e desempenho de um cluster. Menos discos significa que menos dados podem ser mantidos, e o desempenho poderá ser prejudicado por ter menos discos disponíveis para distribuição.

- O número de atualizar domínios / domínios por conjunto de disponibilidade de falhas. Se você criar VMs usando o Gerenciador de recursos do Azure, cada conjunto de disponibilidade pode ser alocado até 3 falhas domínios e atualização de 20. Essa limitação pode afetar a resiliência de um cluster grande que está sujeitas a frequentes atualizações sem interrupção.

Além disso, você provavelmente não considere usar VMs com mais de 64GB de memória. Conforme descrito na seção [requisitos de memória][], você não deve alocar mais de 30 GB de RAM em cada máquina virtual à JVM e permitir que o sistema operacional utilizar a memória restante para o buffer de e/s.

Com essas restrições em mente, você sempre deve difundir os discos virtuais para as VMs em um cluster entre contas de armazenamento para reduzir as chances de otimização de e/s. Em um cluster muito grande, você talvez precise reformular sua infraestrutura de lógica e dividi-la em partições funcionais separadas. Por exemplo, talvez seja necessário dividir cluster em assinaturas, embora esse processo pode levar a mais complicações devido a necessidade de se conectar redes virtuais.

**Dimensionamento um cluster Elasticsearch horizontalmente**

Internamente dentro de Elasticsearch, o limite de escalabilidade horizontal é determinado pelo número de fragmentos definido para cada índice. Inicialmente, muitos fragmentos podem ser alocados para o mesmo nó em um cluster, mas conforme o volume de dados aumenta adicional nós podem ser adicionados e fragmentos podem ser distribuídos entre esses nós. Em teoria, somente quando o número de nós atinge o número de fragmentos o sistema deixará de dimensionar horizontalmente.

Como com dimensionamento vertical, há alguns problemas que você deve considerar ao considerando a implementação de dimensionamento horizontal, incluindo:

- O número máximo de VMs que você pode conectar em uma rede virtual Azure. Isso pode limitar a escalabilidade horizontal para um cluster muito grande. Você pode criar um cluster de nós que abrange mais de uma rede virtual para contornar esse limite, mas essa abordagem pode levar a desempenho reduzido devido a falta de localidade de cada nó com seus colegas.

- O número de discos por tamanho de máquina virtual. Outra série e SKUs suportam diferentes números de discos anexados.
Além disso, você também pode considerar usando o armazenamento efêmero incluído com a máquina virtual para fornecer uma quantidade limitada de armazenamento de dados mais rápida, embora existam implicações resiliência e recuperação que você deve considerar (consulte [Configurando resiliência e recuperação em Elasticsearch no Azure] [ elasticsearch-resilience-recovery] para obter mais informações). A série de D, série DS, Dv2-séries e GS de VMs usam SSDs para armazenamento efêmero.

Você pode considerar o uso de [Conjuntos de escala de máquina Virtual] [ vmss] para iniciar e parar VMs como exige exigida pela. No entanto, essa abordagem pode não ser adequada para um cluster de Elasticsearch pelos seguintes motivos:

- Essa abordagem é mais adequada para VMs sem estado. Sempre que você adicionar ou remove um nó de um cluster de Elasticsearch, fragmentos são realocados de saldo a carga e esse processo pode gerar consideráveis volumes de tráfego de rede e i / disco e pode afetar seriamente taxas de inclusão de dados. Você deve avaliar se essa sobrecarga vale o benefício do processamento adicional e dos recursos de memória que se tornam disponíveis iniciando dinamicamente mais VMs.

- Inicialização de máquina virtual não ocorrer instantaneamente e pode levar alguns minutos antes de VMs adicionais se tornam disponíveis ou que eles são encerrados. Dimensionamento dessa maneira só deve ser usado para lidar com alterações contínua em demanda.

- Depois de dimensionamento check-out, você realmente precisa levar em consideração dimensionamento novamente? Removendo uma máquina virtual de um cluster de Elasticsearch pode ser um processo de muitos recursos que exigem que Elasticsearch recupera os fragmentos e réplicas que estiverem localizadas em que máquina virtual e os recria em um ou mais de nós restantes. Remover várias VMs ao mesmo tempo poderia comprometem a integridade do cluster, dificulta a recuperação. Além disso, muitas implementações Elasticsearch crescerão ao longo do tempo, mas a natureza dos dados é, de forma que ele não precisam diminuir volume. É possível excluir documentos manualmente e documentos também podem ser configurados com um TTL (tempo de vida) depois que eles expirar e obtém removidos, mas na maioria dos casos, é provável que o espaço previamente alocado será reutilizado rapidamente por documentos novos ou modificados. Fragmentação dentro de um índice pode ocorrer quando documentos são removidos ou alterados, caso em que você pode usar a HTTP Elasticsearch [otimizar][] API (Elasticsearch 2.0.0 e anteriores) ou a [Força mesclar][] API (Elasticsearch 2.1.0 e posterior) para executar desfragmentação.

### <a name="determining-the-number-of-shards-for-an-index"></a>Determinando o número de fragmentos para um índice

O número de nós em um cluster pode variar ao longo do tempo, mas o número de fragmentos em um índice é corrigido depois que o índice foi criado. Para adicionar ou remover fragmentos requer a reindexação de dados – um processo de criação de um novo índice com o número necessário de fragmentos e, em seguida, copiando os dados o índice antigo para o novo (você pode usar aliases para isolar usuários do fato de que dados foram reindexados – consulte [agregação de dados de ajuste e desempenho de consulta para Elasticsearch no Azure][] para obter mais detalhes).
Portanto, é importante determinar o número de fragmentos que você provavelmente exigir antes de criar o primeiro índice em seu cluster. Você pode executar as seguintes etapas para estabelecer este número:

- Crie um cluster de nó único usando a mesma configuração de hardware que você pretende implantar na produção.

- Crie um índice que corresponde à estrutura que você planeja usar em produção. Dê deste índice fragmentar um único e nenhum réplicas.

- Adicione uma quantidade específica de dados de produção realista ao índice.

- Executar consultas típicas, agregações e outras cargas de trabalho contra o índice e medir o tempo de produtividade e resposta.

- Se o tempo de resposta e produtividade dentro dos limites aceitáveis, em seguida, repita o processo da etapa 3 (adicionar mais dados).

- Quando parece que você atingiu a capacidade do fragmentar (tempos de resposta e começar a se tornar aceitável de produtividade), anote o volume de documentos.

- Expande a capacidade de um único fragmentar previstas número de documentos em produção para calcular o número necessário de fragmentos (você deve incluir alguma margem de erro nesses cálculos como extrapolação não é uma ciência precisa).

> [AZURE.NOTE]Lembre-se de que cada fragmentar é implementado como um índice de Lucene que consome memória, potência de CPU e identificadores de arquivo. Os fragmentos mais tiver, mais desses recursos que exigem.

Além disso, criar fragmentos mais pode aumentar escalabilidade (dependendo de sua carga de trabalho e cenário) e pode aumentar a produtividade de inclusão de dados, mas ele pode reduzir a eficiência de várias consultas. Por padrão, uma consulta será investigar cada fragmentar usado por um índice (você pode usar [Roteamento personalizado][] para modificar esse comportamento, se você souber quais fragmentos os dados que necessários estão localizados no). 

Seguindo este processo pode gerar apenas uma estimativa do número de fragmentos e o volume de documentos esperado em produção não pode ser conhecido. Nesse caso, você deve determinar o volume inicial (como acima) e a taxa de crescimento previsto. Crie um número apropriado de fragmentos que pode lidar com o crescimento de dados para o período até que você está disposto a reindexar o banco de dados. 

Outras estratégias usado para cenários como gerenciamento de eventos e registro em log incluem o uso de índices sem interrupção.
Criar um novo índice para os dados incluídos cada dia e acessar deste índice por meio de um alias que é alternado diariamente para apontar para o índice mais recente. Essa abordagem permite que você dados mais facilmente idade-out antigos (você pode excluir os índices que contém informações que não é mais necessárias) e mantém o volume de dados gerenciáveis.

Tenha em mente que o número de nós não tenha que corresponde ao número de fragmentos. Por exemplo, se você criar 50 fragmentos, você pode distribui-las por 10 nós inicialmente e, em seguida, adicionar mais nós para dimensionar o sistema de check-out como o volume de trabalho aumenta. Evite a criação de um número muito grande de fragmentos em um pequeno número de nós (1000 fragmentos difundir através de 2 nós, por exemplo). Embora o sistema teoricamente poderia escala para nós de 1000 com essa configuração, executando 500 fragmentos em um riscos de nó único enfraquecer o desempenho do nó.

> [AZURE.NOTE]Para sistemas que são dados inclusão espesso, considere o uso de um número primo de fragmentos. O algoritmo de padrão que Elasticsearch usa para circulação de documentos para fragmentos produz uma mais mesmo distribuída nesse caso.

### <a name="security"></a>Segurança

Por padrão, Elasticsearch implementa segurança mínima e não fornece qualquer meio de autenticação e autorização. Esses aspectos exigem Configurando o sistema operacional e a rede subjacente e usando o plug-ins e utilitários de terceiros. Alguns exemplos incluem [proteção][]e [Protetor de pesquisa][].

> [AZURE.NOTE]Proteção é um plug-in fornecido pelo elástica para autenticação do usuário, criptografia de dados, controle de acesso baseado em função, filtragem IP e auditoria. Talvez seja necessário configurar o sistema operacional subjacente para implementar o medidas de segurança, como criptografia de disco.

Em um sistema de produção, você deve considerar como:

- Impedir o acesso não autorizado ao cluster.
- Identificar e autenticar usuários.
- Autorize as operações que os usuários autenticados podem realizar.
- Proteja o cluster falsos ou operações de danosos.
- Proteger os dados contra acesso não autorizado.
- Atender aos requisitos regulamentares segurança de dados comerciais (se apropriado).

### <a name="securing-access-to-the-cluster"></a>Protegendo o acesso ao cluster

Elasticsearch é um serviço de rede. Os nós em um cluster de Elasticsearch escutam solicitações de entrada cliente usando HTTP e se comunicar com os outros usando um canal TCP. Você deve tomar medidas para impedir que clientes não autorizados ou serviços possam enviar solicitações pela caminhos HTTP tanto o TCP. Considere os seguintes itens. 

- Defina grupos de segurança de rede para limitar o entrada e saída tráfego de rede para uma rede virtual ou máquina virtual para somente portas específicas.

- Altere as portas padrão usadas para o acesso do cliente web (9200) e acesso à rede de programação (9300). Use um firewall para proteger cada nó de tráfego de Internet mal-intencionado.

- Dependendo da localização e a conectividade de clientes, coloque o cluster em uma sub-rede privada sem acesso direto à Internet. Se o cluster deve ser exposto fora da sub-rede, rotear todas as solicitações através de um servidor bastião ou proxy suficientemente robusto para proteger o cluster.

Se você deve fornecer acesso direto a nós, use um servidor de proxy [nginx](http://nginx.org/en/) configurar e autenticação HTTPS.

> [AZURE.NOTE]Usando um servidor proxy como nginx, você pode também restringir o acesso à funcionalidade. Por exemplo, você pode configurar nginx para permitir apenas solicitações para o \_pesquisar ponto extremo Se você precisar impedir a executar outras operações de clientes.

Se você precisar de mais abrangente segurança de acesso de rede, use os plug-ins proteção ou protetor de pesquisa.

### <a name="identifying-and-authenticating-users"></a>Identificar e autenticar usuários

Todas as solicitações feitas por clientes ao cluster devem ser autenticadas. Além disso, você deve impedir que nós não autorizados ingressar no cluster como eles podem fornecer uma porta dos fundos no sistema que ignora autenticação.

Elasticsearch plug-ins estão disponíveis que podem realizar diferentes tipos de autenticação, incluindo:

- **Autenticação básica HTTP**. Nomes de usuário e senhas são incluídas em cada solicitação. Todas as solicitações devem ser criptografadas usando SSL/TLS ou um equivalente nível de proteção.

- **Integração de LDAP e Active Directory**. Essa abordagem requer que clientes são atribuídos a funções em grupos LDAP ou AD.

- **Autenticação nativa**. Usa identidades definidas no próprio cluster Elasticsearch.

- **Autenticação TLS**. Use autenticação TLS dentro de um cluster para autenticar todos os nós.

- **Filtragem de IP**. Use a filtragem IP para impedir que clientes de sub-redes não autorizados de conexão e também impedindo nós destas sub-redes ingressar no cluster.

### <a name="authorizing-client-requests"></a>Autorizar solicitações de cliente

Autorização depende o plug-in Elasticsearch usado para fornecer esse serviço. Por exemplo, um plug-in que fornece autenticação básica normalmente fornece recursos que definem o nível de autenticação, enquanto um plug-in que usa LDAP ou AD será normalmente associar clientes funções e atribuir direitos de acesso a essas funções. Ao usar qualquer plug-in, você deve considerar os seguintes pontos:

- Você precisa restringir as operações que um cliente pode realizar? Por exemplo, um cliente deve ser capaz de monitorar o status do cluster, ou criar e excluir índices?

- O cliente deve ser restrito a índices específicos? Isso é útil em um multitenant: nenhum hífen por estilo guia. >> situação onde locatários podem ser atribuídos a seu próprio conjunto específico de índices e esses índices devem ser inacessíveis aos outros locatários.

- Devo o cliente por possam ler e gravar dados em um índice? Um cliente pode ser capaz de executar pesquisas que recuperar dados usando um índice, mas devem ser impedidas de adicionar ou excluir dados do índice, por exemplo.

Atualmente, a maioria dos plug-ins de segurança escopo operações ao nível cluster ou índice e não para subconjuntos de documentos em índices. Isso é por motivos de eficiência. Portanto, não é fácil de limitar as solicitações de documentos específicos dentro de um único índice. Se você solicitar esse nível de detalhamento, salve documentos em índices separados e use aliases que índices de grupo juntas. 

Por exemplo, em um sistema de pessoal, se o usuário A requer acesso a todos os documentos que contêm informações sobre funcionários no departamento X, o usuário B requer acesso a todos os documentos que contêm informações sobre funcionários no departamento Y e usuário C requer acesso a todos os documentos que contêm informações sobre funcionários em ambos os departamentos, crie dois índices (por departamento X e Y) e um alias que faz referência a ambos os índices. Conceder acesso de leitura de um usuário para o primeiro índice, conceder acesso de leitura do usuário B para o segundo índice e conceder acesso de leitura do usuário C para ambos os índices por meio do alias. Para obter mais informações, consulte [Simular índice por usuário com Aliases][].

### <a name="protecting-the-cluster"></a>Protegendo o cluster

O cluster pode se tornar vulnerável a uso indevido se ele não estiver protegido cuidadosamente. 

**Desabilitar a consulta dinâmica scripts no Elasticsearch** consulta como eles podem levar a vulnerabilidades de segurança. Usar os scripts nativos em preferência de consulta script; um script nativo é um plug-in Elasticsearch escritos em Java e compilado em um arquivo JAR.

Script de consulta dinâmica agora é desabilitado por padrão; não reativá-lo, a menos que você tenha um bom motivo para fazê-lo.

**Evite expor pesquisas de sequência de consulta aos usuários** como este tipo de pesquisa permite aos usuários executar consultas de recursos sem demora. Essas pesquisas seriamente poderá afetar o desempenho do cluster e podem renderizar o sistema aberto a ataques. Além disso, a pesquisa de sequência de consulta pode expor informações potencialmente particulares.

**Impedir operações de consumir muita memória** como esses pode causar exceções de falta de memória, resultando em falha de Elasticsearch em um nó. Operações de intenso de recursos de execução demorada também podem ser usadas para implementar ataques DOS. Alguns exemplos incluem:

Evite solicitações de pesquisa que tentam carregar campos muito grandes na memória (se uma consulta classifica, scripts ou aspectos nesses campos), tais como:

- Pesquisas de vários índices de consulta ao mesmo tempo.

- Pesquisas de recuperar um grande número de campos. Essas pesquisas podem usar memória fazendo uma grande quantidade de dados do campo a ser armazenados em cache. Por padrão, o cache de dados do campo é ilimitado em tamanho, mas você pode definir as propriedades de [indices.fielddata.cache.*](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-fielddata.html) no arquivo de configuração elasticsearch.yml para limitar os recursos disponíveis. Você também pode configurar o [separador de circuito de dados do campo][] para ajudar a impedir que os dados armazenados em cache de um único campo da memória usar todos e o [separador de circuito de solicitação][] para parar de consultas individuais de monopolizando memória. O custo de configuração esses parâmetros é a maior probabilidade de algumas consultas falhando ou o tempo limite.
 
> [AZURE.NOTE]Usando [Valores do documento][] pode reduzir os requisitos de memória de índices salvar dados do campo disco em vez de carregá-lo na memória. Isso pode ajudar a reduzir as chances de esgotamento da memória em um nó, mas com uma redução de velocidade.

> Elasticsearch sempre pressupõe que ele tenha memória suficiente para executar sua carga de trabalho atual. Se esse não for o caso, o serviço de Elasticsearch pode falhar. Elasticsearch fornece pontos de extremidade que retornam informações sobre o uso de recursos (o HTTP [gato APIs][]) e monitore cuidadosamente essas informações.

**Aguardando muito tempo para liberar um segmento de memória em andamento**. Isso pode usar o espaço de buffer na memória.
Se necessário, [configure o translog][] para reduzir os limites em quais dados é limpo em disco.

**Criar índices com grandes quantidades de metadados**. Um índice que contém documentos com uma grande variação nos nomes de campo pode consumir muita memória. Para obter mais informações, consulte [Explosão mapeamento][].
  
A definição de uma operação de intenso de consulta ou demorada é altamente específicas do cenário. A carga de trabalho normalmente esperada por um cluster pode ter um perfil completamente diferente na carga de trabalho em outro. Determinar quais operações são aceitável requer pesquisa significativa e testes de seus aplicativos.

Ser proativos, detectar e interromper atividades mal-intencionados antes que eles causar dano significativo ou perda de dados.
Considere usar um sistema de monitoramento e notificação de segurança que pode detectar rapidamente padrões incomuns de dados acessar e geram alertas quando, por exemplo, solicitações de logon do usuário falham, nós inesperado ingressar ou sair do cluster ou operações estejam demorando mais que o esperado. Ferramentas que podem realizar estas tarefas incluem Elasticearch [Inspetor][].

### <a name="protecting-the-data"></a>Proteção de dados

Você pode proteger dados inflight usando SSL/TLS, mas Elasticsearch não fornece qualquer formulário interno de criptografia de dados para obter informações que estão armazenadas no disco. Lembre-se de que essas informações são mantidas em arquivos do disco comum, e qualquer usuário com acesso a esses arquivos pode ser capaz de comprometer os dados que elas contêm, por exemplo copiando-os para seu próprio cluster. Considere os seguintes pontos:

- Protege os arquivos usados pelo Elasticsearch para manter os dados. Não permita aleatório acesso de leitura ou gravação a identidades diferente do serviço de Elasticsearch.

- Criptografe os dados contidos nesses arquivos usando um sistema de arquivos criptografados.

> [AZURE.NOTE]Azure agora dá suporte a criptografia de disco para Linux e VMs do Windows. Para obter mais informações, consulte [Azure disco criptografia para Windows e Linux IaaS VMs Preview][].

### <a name="meeting-regulatory-requirements"></a>Requisitos regulatórios de reunião

Requisitos regulatórios principalmente se preocupam com operações para manter um histórico de eventos, de auditoria e assegurar a privacidade dessas operações impedi-los sendo monitoradas (repetidas) por uma empresa externa. Em particular, você deve considerar como:

- Acompanhar todas as solicitações (com êxito ou não) e todas as tentativas de acessar o sistema.

- Criptografe comunicações feitas por clientes do cluster bem como comunicações para nós realizada pelo cluster. Você deve implementar o SSL/TLS para todas as comunicações de cluster. Elasticsearch também suporta codificações conectáveis se sua organização tiver requisitos distintos de disponível por meio de SSL/TLS.

- Armazene todos os dados de auditoria com segurança. O volume de informações de auditoria pode crescer muito rapidamente e deve ser protegido verdadeiramente para impedir a adulteração de informações de auditoria.

- Com segurança dados de auditoria de arquivamento.

### <a name="monitoring"></a>Monitoramento

Monitoramento é importante no nível do sistema operacional e do nível de Elasticsearch.

Você pode executar o monitoramento no nível do sistema operacional usando ferramentas específicas do sistema operacional. Em Windows, isso inclui itens como o Monitor de desempenho com os contadores de desempenho apropriado, enquanto no Linux, você pode usar ferramentas como *vmstat*, *iostat*e *superior*. Os principais itens para monitorar no nível do sistema operacional incluem utilização da CPU, volumes de e/s de disco, tempos de espera i / disco e tráfego de rede.
Em um cluster de Elasticsearch bem ajustado, utilização da CPU pelo processo Elasticsearch deverá ser alta e tempos de espera de e/s de disco deve ser mínimo.

No nível do software, você deve monitorar as horas de produtividade e resposta de solicitações, junto com os detalhes de solicitações que falham. Elasticsearch fornece um número de APIs que você pode usar para examinar o desempenho dos diferentes aspectos de um cluster. As duas APIs mais importantes são *_cluster/integridade* e *_nodes/estatísticas*. A *_cluster/integridade* API pode ser usada para fornecer um instantâneo da integridade geral do cluster, bem como fornecendo informações detalhadas para cada índice, conforme mostrado no exemplo a seguir:

`GET _cluster/health?level=indices`

Saída do exemplo mostrada abaixo foi gerada com essa API:

```json
{
    "cluster_name": "elasticsearch",
    "status": "green",
    "timed_out": false,
    "number_of_nodes": 6,
    "number_of_data_nodes": 3,
    "active_primary_shards": 10,
    "active_shards": 20,
    "relocating_shards": 0,
    "initializing_shards": 0,
    "unassigned_shards": 0,
    "delayed_unassigned_shards": 0,
    "number_of_pending_tasks": 0,
    "number_of_in_flight_fetch": 0,
    "indices": {
        "systwo": {
            "status": "green",
            "number_of_shards": 5,
            "number_of_replicas": 1,
            "active_primary_shards": 5,
            "active_shards": 10,
            "relocating_shards": 0,
            "initializing_shards": 0,
            "unassigned_shards": 0
        },
        "sysfour": {
            "status": "green",
            "number_of_shards": 5,
            "number_of_replicas": 1,
            "active_primary_shards": 5,
            "active_shards": 10,
            "relocating_shards": 0,
            "initializing_shards": 0,
            "unassigned_shards": 0
        }
    }
}
```

Este cluster contém dois índices chamado *systwo* e *sysfour*. Principais estatísticas para monitorar para cada índice são o status, active_shards e unassigned_shards. O status deve estar verde, o número de active_shards deve refletir o number_of_shards e number_of_replicas e unassigned_shards deve ser zero. 

Se o status for vermelho, parte do índice está ausente ou foi danificado. Você pode verificar isso se a configuração de *active_shards* é menor que *number_of_shards* - (*number_of_replicas* + 1) e unassigned_shards é diferente de zero. Observe que um status de amarelo indica que um índice está em estado de transição, como o resultado da adição de mais réplicas ou fragmentos realocados. O status deve alternar para verde quando a transição for concluída. 

Se ele permanecer amarelo por um longo período ou alterações em vermelho, você deverá verificar se todos os eventos significativos e/s (como uma falha de disco ou de rede) ocorreu no nível do sistema operacional.

O \_API de nós/estatísticas emite considerável informações sobre cada nó no cluster:

`GET _nodes/stats`

A saída gerada inclui detalhes sobre como índices são armazenados em cada nó (incluindo os tamanhos e números de documentos), o tempo gasto indexação de desempenho, consultando, pesquisando, mesclando, cache, sistema operacional e informações de processo, estatísticas sobre a JVM (incluindo lixo coleção de desempenho) e pools de segmento. Para obter mais informações, consulte [Monitoramento nós individuais][].

Se uma porção significativa das solicitações de Elasticsearch está falhando com mensagens de erro de *EsRejectedExecutionException* , Elasticsearch está falhando acompanhar o trabalho que está sendo enviado. Nessa situação, é necessário identificar o gargalo que está causando Elasticsearch para se situar. Considere os seguintes itens:

- Se o gargalo for devido a uma restrição de recursos, como memória insuficiente alocada para a JVM causando um número excessivo de coleções de lixo, considere a alocação de recursos adicionais (neste caso, configurar a JVM usar mais memória, para cima para 50% de armazenamento disponível no nó –, consulte [requisitos de memória][]).

- Se o cluster estiver aparecendo grande tempos de espera de e/s e as estatísticas de mesclagem reunidas para um índice usando o \_nó/estatísticas API contêm valores grandes e em seguida, o índice será pesado de gravação. Revise os pontos levantados [otimizando recursos para operações de indexação](guidance-elasticsearch-tuning-data-ingestion-performance.md#optimizing-resources-for-indexing-operations) para ajustar o desempenho de indexação.

- Aplicativos cliente que estão executando operações de inclusão de dados de aceleração e determine o efeito que isso tem no desempenho. Se essa abordagem mostra uma melhora significativa, considere a aceleração de retenção, ou dimensionamento distribuindo a carga de gravação pesado índices em mais nós.
Para obter mais informações, consulte [ajuste de desempenho de inclusão de dados para Elasticsearch no Azure][].

- Se as estatísticas de pesquisa para um índice indicam que consultas estão demorando muito, em seguida, considere como as consultas são otimizadas. Observe que você pode usar os valores de *query_time_in_millis* e *query_total* relatados pelas estatísticas da pesquisa para calcular um guia preliminar a eficiência de consulta; a equação *query_time_in_millis* / *query_total* você terá a média de uma vez para cada consulta.

### <a name="tools-for-monitoring-elasticsearch"></a>Ferramentas para o monitoramento Elasticsearch

Uma variedade de ferramentas estão disponíveis para executar o monitoramento diárias do Elasticsearch de produção. Essas ferramentas normalmente usam as APIs Elasticsearch subjacente para coletar informações e apresentar os detalhes de uma maneira mais fácil observar que os dados não processados. Exemplos comuns incluem [Cabeça Elasticsearch][], [Bigdesk][], [Kopf][]e [Marvel][].

Cabeça Elasticsearch, Bigdesk e Kopf executam como plug-ins para o software Elasticsearch. Versões mais recentes do Marvel podem executar de maneira independente, mas exigir [Kibana][] fornecer um ambiente de hospedagem e a captura de dados. A vantagem de usar Marvel com Kibana é que você pode implementar monitoramento em um ambiente separado do cluster Elasticsearch, permitindo que você explorar problemas com Elasticsearch que talvez não seja possível se as ferramentas de monitoramento executam como parte do software Elasticsearch. Por exemplo, se Elasticsearch repetidamente falhar ou está lento muito, ferramentas que executam como Elasticsearch plug-ins, também serão afetadas, tornando mais difícil o monitoramento e diagnóstico.

No nível do sistema operacional, você pode usar ferramentas como o recurso de análise de Log do [Pacote de gerenciamento de operações do Azure][] ou [Diagnóstico do Azure com o Portal do Azure][] para capturar dados de desempenho para VMs Elasticsearch nós de hospedagem. Outra abordagem é usar [Logstash][] para capturar o desempenho e registre os dados, armazenar essas informações em um cluster de Elasticsearch separado (não use o mesmo cluster que você está usando ou seu aplicativo) e depois use Kibana para visualizar os dados. Para obter mais informações, consulte [Diagnóstico do Microsoft Azure com ELK][].

### <a name="tools-for-testing-elasticsearch-performance"></a>Ferramentas de teste de desempenho de Elasticsearch

Outras ferramentas estão disponíveis se você estiver Elasticsearch de avaliações de desempenho ou submeter um cluster para teste de desempenho. Essas ferramentas destinam-se a ser usada em um desenvolvimento ou teste ambiente em vez de produção.
Um exemplo usados é [Apache JMeter][].

JMeter foi usado para executar testes de carga benchmarking e outras descritos em documentos relacionados a este guia. [Criando um ambiente de teste de desempenho para Elasticsearch no Azure][] descreve detalhadamente como JMeter foi configurado e usado.

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Ajuste de desempenho de inclusão de dados para Elasticsearch no Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Criando um ambiente de teste para Elasticsearch no Azure de desempenho]: guidance-elasticsearch-creating-performance-testing-environment.md
[Implementing a JMeter Test Plan for Elasticsearch]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Ajuste de desempenho de consulta para Elasticsearch no Azure e de agregação de dados]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Configuring Resilience and Recovery on Elasticsearch on Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Running the Automated Elasticsearch Resiliency Tests]: guidance-elasticsearch-configuring-resilience-and-recovery

[Apache JMeter]: http://jmeter.apache.org/
[Apache Lucene]: https://lucene.apache.org/
[Criptografia de disco Azure para Windows e Linux IaaS VMs Preview]: ../azure-security-disk-encryption.md
[Balanceador de carga Azure]: ../load-balancer/load-balancer-overview.md
[Rota expressa]: ../expressroute/expressroute-introduction.md
[Balanceador de carga interno]:  ../load-balancer/load-balancer-internal-overview.md
[Tamanhos para máquinas virtuais]: ../virtual-machines/virtual-machines-linux-sizes.md

[Requisitos de memória]: #memory-requirements
[Requisitos de rede]: #network-requirements
[Descoberta de nó]: #node-discovery
[Query Tuning]: #query-tuning

[elasticsearch-scripts]: https://github.com/mspnp/azure-guidance/tree/master/scripts/ps
[A Highly Available Cloud Storage Service with Strong Consistency]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx
[Plug-in do Azure nuvem]: https://www.elastic.co/blog/azure-cloud-plugin-for-elasticsearch
[Diagnóstico do Azure com o Portal do Azure]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[Pacote de gerenciamento de operações Azure]: https://www.microsoft.com/server-cloud/operations-management-suite/overview.aspx
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/
[Bigdesk]: http://bigdesk.org/
[gato APIs]: https://www.elastic.co/guide/en/elasticsearch/reference/1.7/cat.html
[Configurar o translog]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-translog.html
[roteamento personalizado]: https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-routing-field.html
[Valores de documento]: https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html
[Elasticsearch]: https://www.elastic.co/products/elasticsearch
[Cabeça Elasticsearch]: https://mobz.github.io/elasticsearch-head/
[Elasticsearch.Net & ANINHAR]: http://nest.azurewebsites.net/
[elasticsearch-resilience-recovery]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Módulo de restauração e Elasticsearch instantâneo]: https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html
[Simular índice por usuário com Aliases]: https://www.elastic.co/guide/en/elasticsearch/guide/current/faking-it.html
[separador de circuito de dados do campo]: https://www.elastic.co/guide/en/elasticsearch/reference/current/circuit-breaker.html#fielddata-circuit-breaker
[Forçar direta]: https://www.elastic.co/guide/en/elasticsearch/reference/2.1/indices-forcemerge.html
[gossiping]: https://en.wikipedia.org/wiki/Gossip_protocol
[Kibana]: https://www.elastic.co/downloads/kibana
[Kopf]: https://github.com/lmenezes/elasticsearch-kopf
[Logstash]: https://www.elastic.co/products/logstash
[Explosão de mapeamento]: https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion
[Marvel]: https://www.elastic.co/products/marvel
[Diagnóstico do Microsoft Azure com ELK]: http://aka.ms/AzureDiagnosticsElk
[Monitoramento nós individuais]: https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_monitoring_individual_nodes
[nginx]: http://nginx.org/en/
[API do cliente de nó]: https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/client.html
[Otimizar]: https://www.elastic.co/guide/en/elasticsearch/reference/1.7/indices-optimize.html
[Plug-in de alterações de PubNub]: http://www.pubnub.com/blog/quick-start-realtime-geo-replication-for-elasticsearch/
[separador de circuito de solicitação]: https://www.elastic.co/guide/en/elasticsearch/reference/current/circuit-breaker.html#request-circuit-breaker
[Protetor de pesquisa]: https://github.com/floragunncom/search-guard
[Proteção]: https://www.elastic.co/products/shield
[Transport Client API]: https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/transport-client.html
[nós tribo]: https://www.elastic.co/blog/tribe-node
[vmss]: https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/
[Inspetor]: https://www.elastic.co/products/watcher
[Zen]: https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html
