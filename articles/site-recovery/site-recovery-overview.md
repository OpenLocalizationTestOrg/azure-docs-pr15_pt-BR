<properties
    pageTitle="O que é recuperação de Site? | Microsoft Azure"
    description="Fornece uma visão geral do serviço de recuperação de Site do Azure e resume cenários de implantação."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/13/2016"
    ms.author="raynew"/>

#  <a name="what-is-site-recovery"></a>O que é recuperação de Site?

Bem-vindo à recuperação de Site Azure! Este artigo fornece uma rápida visão geral do serviço de recuperação de Site e como ele contribui para a sua empresa.

Sua organização precisa de uma estratégia de recuperação (BCDR) de desastres que mantém dados, cargas de trabalho e aplicativos confiáveis e disponível durante o tempo de inatividade planejado e e continuidade de negócios e recupera a condições de trabalho normal assim que possível. Recuperação de site é um serviço Azure que contribui para essa estratégia.

Recuperação de site organiza replicação de cargas de trabalho servidores físicos locais e máquinas virtuais. Você pode replicar VMs e servidores de um data center principal para a nuvem (Azure), ou para um data center secundário. Quando ocorrem interrupções no local principal, você não sobre o site secundário para manter aplicativos e cargas de trabalho acessíveis e disponíveis. Você não volta para seu local principal quando ele retorna as operações normais.

## <a name="site-recovery-in-the-azure-portal"></a>Recuperação de site no portal do Azure

Azure tem dois diferentes [modelos de implantação](../resource-manager-deployment-model.md) para criar e trabalhar com recursos. O modelo do Gerenciador de recursos do Azure e o modelo de gerenciamento de serviços clássico. Azure também tem dois portais – o [Azure portal clássico](https://manage.windowsazure.com/) que suporta o modelo clássico de implantação e o [portal do Azure](https://portal.azure.com) com suporte para o clássico e modelos do Gerenciador de recursos.

- Recuperação de site está disponível no portal do clássico e o portal do Azure.
- No portal do Azure clássico, você pode oferecer suporte a recuperação de Site com o modelo de gerenciamento de serviços clássico.
- No portal do Azure, você pode oferecer suporte o modelo clássico ou implantações do Gerenciador de recursos. 

As informações neste artigo se aplica ao clássicas e Azure implantações portal. Diferenças são indicadas onde aplicável.


## <a name="why-deploy-site-recovery"></a>Por que implantar recuperação de Site?

Veja aqui o que pode fazer a recuperação de Site para sua empresa:

- **Simplificar BCDR**— você pode manipular replicação, failover e recuperação de várias cargas de trabalho em um único local no portal do Azure. Recuperação de site organiza replicação e failover, mas não interceptar seus dados de aplicativo ou tiver qualquer informação sobre ele.
- **Fornecem replicação flexível**— usando a recuperação de Site, você pode replicar cargas de trabalho em execução no compatíveis VMs Hyper-V, VMs VMware e servidores físicos Windows/Linux.
- **Replicação de fácil executar testes**— recuperação de Site fornece failovers de teste para oferecer suporte a produtos de recuperação de desastres, sem afetar os ambientes de produção.
- **Falha ao longo e recuperar**— você pode executar failovers planejados para interrupções esperadas com uma perda de dados do zero ou failovers planejadas com perda de dados mínima (dependendo da frequência de replicação) para desastres inesperados. Após o failover, você pode failback aos seus sites principais. Recuperação de site oferece planos de recuperação que podem incluir scripts e pastas de trabalho do Azure automação, para que você possa personalizar failover e recuperação de aplicativos de vários níveis.
- **Eliminar um data center secundário**— você pode replicar cargas de trabalho para o Azure, em vez de em um local secundário. Isso elimina o custo e a complexidade mantendo um data center secundário. Dados replicados armazenados em armazenamento Azure, com a resiliência que fornece. VMs são criadas com os dados replicados quando houver falha.
- **Integrar com tecnologias BCDR existentes**— recuperação de Site integra-se com outros recursos BCDR. Por exemplo, você pode usar a recuperação de Site para proteger o back-end do SQL Server de cargas de trabalho corporativos, incluindo suporte nativo SQL Server AlwaysOn, para gerenciar o failover dos grupos de disponibilidade.

## <a name="what-can-i-replicate"></a>O que pode replicar?

Aqui está um resumo dos quais você pode replicar usando a recuperação do Site.

![Local para o local](./media/site-recovery-overview/asr-overview-graphic.png)

**DUPLICAR** | **DUPLICAR PARA** 
---|---
Cargas de trabalho em execução no VMs VMware local | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Site secundário](site-recovery-vmware-to-vmware.md)
Cargas de trabalho em execução no local Hyper-V VMs gerenciado no nuvens do VMM  | [Azure](site-recovery-vmm-to-azure.md)<br/><br/> [Site secundário](site-recovery-vmm-to-vmm.md) 
Carga de trabalho em execução no local Hyper-V VMs gerenciado no nuvens VMM, com o armazenamento SAN|  [Site secundário](site-recovery-vmm-san.md)
Cargas de trabalho em execução no VMs Hyper-V local, sem VMM | [Azure](site-recovery-hyper-v-site-to-azure.md)
Cargas de trabalho executados em servidores do Windows/Linux físicos local | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Site secundário](site-recovery-vmware-to-vmware.md)


## <a name="what-workloads-can-i-protect"></a>Quais cargas de trabalho pode proteger?

Recuperação de site permite BCDR reconhecimento de aplicativos, de modo que cargas de trabalho e aplicativos continuam sendo executados de maneira consistente quando ocorrem interrupções. Fornece a recuperação do site:

- **Instantâneos consistentes com o aplicativo**— máquinas replicam usando instantâneos consistentes com o aplicativo, para aplicativos únicos ou várias camadas. Além de capturar dados de disco, instantâneos consistentes com o aplicativo captura captura todos os dados na memória e todas as transações no processo.
- **Replicação perto síncrono**— recuperação de Site fornece a frequência de replicação é baixa como 30 segundos para Hyper-V e replicação contínua para VMware.
- **Planos de recuperação flexível**— você pode criar e personalizar planos de recuperação com scripts externos e ações manuais. Integração com a automação do Azure runbooks permitem que você recuperar uma pilha de todo o aplicativo com um único clique.
- **Integração com o SQL Server AlwaysOn**— você pode gerenciar o failover dos grupos de disponibilidade de planos de recuperação de recuperação do Site.
- **Biblioteca de automação**— uma biblioteca de automação do Azure avançada fornece scripts pronto para produção, específicos do aplicativo que podem ser baixados e integrados com recuperação de Site.
- **Gerenciamento de rede simples**— gerenciamento avançado de rede na recuperação de Site e Azure simplifica os requisitos de rede do aplicativo, incluindo reserva de endereços IP, configurando balanceadores de carga e a integração do Gerenciador de tráfego do Azure para switchovers de rede eficiente.


## <a name="next-steps"></a>Próximas etapas

- Leia mais em [quais cargas de trabalho recuperação do Site pode proteger?](site-recovery-workload.md)
- Saiba mais sobre arquitetura de recuperação de Site em [como funciona a recuperação de Site?](site-recovery-components.md)
 
