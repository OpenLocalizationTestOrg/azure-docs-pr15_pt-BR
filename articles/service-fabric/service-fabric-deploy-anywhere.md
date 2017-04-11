<properties
   pageTitle="Criar clusters de estrutura de serviço do Azure no Windows Server e Linux | Microsoft Azure"
   description="Clusters de serviço tecidos executados no Windows Server e Linux, que significa que você poderá implantar e aplicativos de serviço tecidos de host é possível executar o Windows Server ou Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="Chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="chackdan"/>

# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Criar clusters de estrutura de serviço no Windows Server ou no Linux

Azure tecidos de serviço permite a criação de clusters de serviço tecidos em qualquer VMs ou computadores que executam o Windows Server ou Linux. Isso significa que você é capaz de implantar e executar aplicativos de serviço tecidos em qualquer ambiente em que você tiver um conjunto de computadores Windows Server ou Linux interconectados, sejam locais, Microsoft Azure ou com qualquer provedor de nuvem.

##<a name="create-service-fabric-clusters-on-azure"></a>Criar clusters de serviço tecidos no Azure

Criando um cluster no Azure é feito por meio de um modelo de modelo de recursos ou o portal do Azure. Para mais informações, leia [criar um cluster de estrutura de serviço usando um modelo do Gerenciador de recursos](service-fabric-cluster-creation-via-arm.md) ou [criar um cluster de estrutura de serviço do portal do Azure](service-fabric-cluster-creation-via-portal.md) .

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Sistemas operacionais suportados para clusters no Azure

É possível criar clusters em VMs executando esses sistemas operacionais:

* Windows Server 2012 R2
* Windows Server 2016 (depois que ele é anunciado como estará disponível)
* Linux Ubuntu 16.04 (no modo de visualização público) 


##<a name="create-service-fabric-standalone-clusters-on-premise-or-with-any-cloud-provider"></a>Criar estrutura de serviço autônomo clusters no local ou em qualquer provedor de nuvem

Serviço tecidos fornece um pacote de instalação para você criar autônomo serviço tecidos clusters local ou em qualquer provedor de nuvem

Para obter mais informações sobre como configurar clusters de estrutura de serviço autônomo no Windows Server, leia [criação do cluster tecidos de serviço para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>Qualquer implantações de nuvem versus implantações locais
O processo para criar um cluster de serviço tecidos local é semelhante ao processo de criação de um cluster em qualquer nuvem de sua preferência com um conjunto de VMs. As etapas iniciais para provisionar VMs são controladas pelo provedor de nuvem ou ambiente local que você está usando. Quando você tiver um conjunto de VMs com conectividade de rede habilitada entre elas, em seguida, as etapas para configurar o pacote de serviço tecidos, edite as configurações de cluster e execute a criação de cluster e scripts de gerenciamento são idênticos. Isso garante que seu conhecimento e a experiência de operação e gerenciar clusters de serviço tecidos é transferível quando você escolhe para novos ambientes de host de destino.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Benefícios da criação de clusters de estrutura de serviço autônomo
* Você está livre para escolher qualquer provedor de nuvem para hospedar seu cluster.
* Aplicativos de serviço tecidos, uma vez escritos, podem ser executados em vários ambientes de hospedagem com mínimos para nenhuma alteração.
* Conhecimento da criação de aplicativos de serviço tecidos traz de um ambiente de hospedagem para outro.
* Experiência operacional da execução e gerenciando o serviço tecidos clusters realiza através de um ambiente para outro.
* Cliente amplo alcance é ilimitado por restrições de ambiente de hospedagem.
* Uma camada extra de confiabilidade e proteção contra interrupções amplamente existe porque você pode mover os serviços sobre para outro ambiente de implantação se um provedor de dados do centro ou nuvem tem um escurecimento.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Sistemas operacionais suportados para clusters autônomo
É possível criar clusters em computadores que executam esses sistemas operacionais ou VMs:

* Windows Server 2012 R2
* Windows Server 2016 (depois que ele é anunciado como estará disponível)
* Linux (em breve)

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Vantagens do serviço tecidos clusters no Azure sobre autônomo que serviço tecidos clusters criada no local

Executar o serviço tecidos clusters no Azure fornece vantagens sobre o local de opção, isso se você não tiver necessidades específicas de onde você executa sua clusters, em seguida, sugerimos que você executá-los no Azure. No Azure, fornecemos integração com outros recursos do Azure e serviços, que torna operações e gerenciamento do cluster mais fácil e confiável.

* **Portal azure:** Portal do Azure facilita a criar e gerenciar clusters.

* **Gerenciador de recursos de azure:** Uso do Gerenciador de recursos do Azure permite o fácil gerenciamento de todos os recursos usados pelo cluster como uma unidade e simplifica acompanhamento de custo e cobrança.
* **Cluster de estrutura de serviço como um recurso do Azure** Um cluster de estrutura de serviço é um recurso ARM, para que você pode modelá-lo como faz outros recursos ARM no Azure.
* **Integração com o Azure infraestrutura** Serviço tecidos coordenadas com a infraestrutura Azure subjacente para o sistema operacional, rede e outras atualizações melhorar a disponibilidade e confiabilidade de seus aplicativos.  
* **Diagnóstico:** No Azure, fornecemos integração com o diagnóstico do Azure e a análise de Log.
* **Escala automática:** Para clusters no Azure, fornecemos funcionalidade interna escala automática devido a conjuntos de escala de máquina Virtual. No local e outros ambientes de nuvem, você precisa criar sua própria recurso ou escala manualmente usando as APIs que serviço tecidos expõe para dimensionamento clusters de escala automática.

## <a name="next-steps"></a>Próximas etapas
Criar um cluster em computadores executando o Windows Server ou VMs: [criação de cluster tecidos de serviço para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)

Criar um cluster em computadores executando Linux ou VMs: [Tecidos de serviço no Linux](service-fabric-linux-overview.md)
