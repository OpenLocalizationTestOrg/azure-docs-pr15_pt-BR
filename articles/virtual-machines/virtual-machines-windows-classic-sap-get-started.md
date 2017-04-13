<properties
   pageTitle="Usando o SAP em máquinas virtuais do Windows | Microsoft Azure"
   description="Limpar sobre como usar o SAP em máquinas virtuais do Windows (VMs) no Microsoft Azure"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="10/04/2016"
   ms.author="sedusch"/>

# <a name="using-sap-on-windows-virtual-machines-in-azure"></a>Usando o SAP em máquinas virtuais do Windows no Azure

Computação em nuvem é um termo amplamente usado que está obtendo mais importância no setor de TI, de pequenas empresas até multinacionais e grandes corporações. Microsoft Azure é a plataforma de serviços de nuvem da Microsoft que oferece uma ampla variedade de novas possibilidades. Agora os clientes são capazes de provisionar e eliminação provisionar aplicativos como serviços de nuvem, para que eles não são limitados às restrições técnicas ou orçamentos rapidamente. Em vez de investir tempo e orçamento em infraestrutura de hardware, empresas podem focalizar o aplicativo, processos de negócios e seus benefícios para os clientes e usuários.

Com máquinas virtuais de Microsoft Azure, a Microsoft oferece uma infraestrutura abrangente como uma plataforma de serviço (IaaS). Aplicativos do SAP NetWeaver com base são suportados em máquinas virtuais do Azure (IaaS). Os artigos a seguintes descrevem como planejar e implementar aplicativos SAP NetWeaver com base em máquinas virtuais do Windows no Azure. Você também pode implementar aplicativos SAP NetWeaver com base em [máquinas virtuais Linux](virtual-machines-linux-classic-sap-get-started.md).

[AZURE.INCLUDE [virtual-machines-common-classic-sap-get-started](../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure---ha"></a>SAP NetWeaver no Azure - HA

Título: SAP NetWeaver no Azure - cluster ASCS/SCS instâncias do SAP usando Cluster de Failover do Windows Server no Azure com SIOS DataKeeper

Resumo: ' Este documento descreve como usar SIOS DataKeeper para configurar uma configuração de SAP ASCS/SCS altamente disponível no Azure. SAP protege seu ponto único de componentes de falha como SAP ASCS/SCS ou serviços de replicação de fila com configurações de Cluster de Failover do Windows Server que exigem discos compartilhados. Esses componentes SAP são essenciais para a funcionalidade de um sistema SAP. Portanto, a funcionalidade de alta disponibilidade precisa ser colocado no lugar para garantir que esses componentes possam sustentar uma falha de um servidor ou uma máquina virtual como concluído com configurações de Cluster do Windows para ambientes de Hyper-V e depender. A partir de agosto de 2015 Azure sozinha não pode fornecer discos compartilhados que seriam necessários para o Windows com base em configurações altamente disponíveis necessárias para esses componentes essenciais do SAP. No entanto com a Ajuda do produto DataKeeper por SIOS, configurações de Cluster de Failover do Windows Server conforme necessário para SAP ASCS/SCS podem ser criadas na plataforma IaaS do Azure. Este artigo descreve em uma abordagem de etapa para como instalar uma configuração de Cluster de Failover do Windows Server com disco compartilhado fornecido pelo Datakeeper SIOS no Azure. Papel mostrará detalhes em configurações do lado do Azure, Windows e SAP que fazer com que a configuração de alta disponibilidade funcionam de maneira ideal. Papel complementa a documentação de instalação do SAP e anotações de SAP que representam os recursos principais para implantações do software SAP e instalações em determinada plataformas.

Atualização: Agosto de 2015

[Baixe este guia agora](http://go.microsoft.com/fwlink/?LinkId=613056)
