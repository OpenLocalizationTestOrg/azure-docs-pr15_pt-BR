<properties
   pageTitle="Pública e privada DC/SO agente Pools ACS | Microsoft Azure"
   description="Como os pools do agente pública e privada funcionam com um cluster de serviço de contêiner do Azure."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contêineres, Microserviços, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="timlt"/>

# <a name="dcos-agent-pools-for-azure-container-service"></a>DC/SO Pools de agente de serviço de contêiner Azure

Serviço de contêiner do Azure DC/SO divide agentes em pools públicos ou privados. Uma implantação pode ser feita para o pool, afetando acessibilidade entre máquinas no seu serviço de contêiner. As máquinas podem ser expostas na internet (público) ou mantidas interna (privada). Este artigo fornece uma visão geral de por que há um pool público e privado.

### <a name="private-agents"></a>Agentes particulares

Nós de agente particular executar através de uma rede não roteáveis. Esta rede só é acessível a partir da região de administrador ou por meio do roteador de borda de zona pública. Por padrão, o DC/SO inicia aplicativos em nós do agente particular. Consulte a [documentação de DC/SO](https://dcos.io/docs/1.7/administration/securing-your-cluster/) para obter mais informações sobre a segurança de rede.

### <a name="public-agents"></a>Agentes de públicos

Nós de agente público executados DC/OS aplicativos e serviços por meio de uma rede acessível publicamente. Consulte a [documentação de DC/SO](https://dcos.io/docs/1.7/administration/securing-your-cluster/) para obter mais informações sobre a segurança de rede.

## <a name="using-agent-pools"></a>Usando pools do agente

Por padrão, **maratona** implanta qualquer novo aplicativo para nós do agente *particular* . Você precisa explicitamente implantar o aplicativo para o nó *público* durante a criação do aplicativo. Selecione a guia **opcional** e digite **slave_public** para o valor de **Funções de recurso aceitas** . Esse processo é documentado [aqui](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) e na documentação do [DC\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) .

## <a name="next-steps"></a>Próximas etapas

Leia mais informações sobre como [gerenciar seus contêineres de DC/sistema operacional](container-service-mesos-marathon-ui.md).

Saiba como [Abrir o firewall](container-service-enable-public-access.md) fornecido pelo Azure para permitir acesso público à sua contêiner DC/sistema operacional.