<properties
    pageTitle="Preparar o mapeamento de rede para proteção de máquina virtual do Hyper-V com VMM em recuperação de Site do Azure | Microsoft Azure"
    description="Configure mapeamento de rede para Hyper-V máquina virtual replicação de um data center local para Azure, ou para um site secundário."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>


# <a name="prepare-network-mapping-for-hyper-v-virtual-machine-protection-with-vmm-in-azure-site-recovery"></a>Preparar o mapeamento de rede para proteção de máquina virtual do Hyper-V com VMM em recuperação de Site do Azure

Recuperação de Site Azure contribui para sua estratégia de recuperação (BCDR) de desastre e continuidade de negócios por coordenação replicação, failover e recuperação de máquinas virtuais e servidores físicos.

Este artigo descreve o mapeamento de rede, que ajuda a definir configurações de rede ideal quando estiver usando o Site de recuperação para replicar máquinas virtuais Hyper-V localizada na nuvens do VMM entre dois centros de dados locais ou entre um data center local e o Azure. Observe que se você estiver replicar VMs Hyper-V sem uma nuvem VMM ou replicar VMs VMware ou servidores físicos, este artigo não é relevante.

Poste quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Visão geral

Mapeamento de rede é usado quando a recuperação de Site Azure é implantada para replicar máquinas virtuais Hyper-V Azure ou um data center secundário, usando replicação Hyper-V Replica ou SAN.

- **Máquinas virtuais Hyper-V replicar nuvens do VMM entre dois data centers local**— rede mapas de mapeamento entre redes de máquina virtual em um servidor VMM de origem e de máquina virtual em um servidor VMM de destino para fazer o seguinte:

    - **Máquinas virtuais de conectar após failover**— garante que máquinas virtuais serão conectadas ao redes apropriados após failover. Máquina virtual réplica será conectada à rede de destino que é mapeada para a rede de origem.
    - **Colocar réplica virtual máquinas em servidores host**— ideal colocar máquinas virtuais de réplica em servidores de host Hyper-V. Máquinas virtuais de réplica serão colocadas em hosts que podem acessar as redes de máquina virtual mapeadas.
    - **Nenhum mapeamento de rede**— se você não configurar mapeamento de rede, replicadas máquinas virtuais não estiver conectadas a quaisquer redes de máquina virtual após failover.

- **Nuvem de máquinas virtuais de replicação Hyper-V no VMM local para o Azure**— rede mapas de mapeamento entre redes de máquina virtual no servidor VMM de origem e destino redes Azure para fazer o seguinte:
    - **Conectar as máquinas virtuais após failover**— todas as máquinas que failover na mesma rede pode se conectar uns aos outros, independentemente de qual plano de recuperação estiverem em.
    - **Gateway de rede**— se um gateway de rede está configurado no destino rede Azure, máquinas virtuais podem se conectar a outras máquinas virtuais de local.
    - **Nenhum mapeamento de rede**— se você não configurar mapeamento de rede, somente as máquinas virtuais que o failover no mesmo plano de recuperação poderão conectem uns aos outros após Falha ao longo do Azure.


## <a name="network-mapping-example"></a>Exemplo de mapeamento de rede

Mapeamento de rede pode ser configurado entre redes de máquina virtual em dois servidores VMM ou em um único servidor VMM se dois sites são gerenciadas pelo mesmo servidor. Quando o mapeamento está configurado corretamente e replicação está habilitada, uma máquina virtual no local principal será conectada a uma rede e sua réplica no local de destino será conectada à sua rede mapeada.

Se redes tiveram sido configurados corretamente no VMM, quando você selecionar uma rede de máquina virtual de destino durante o mapeamento de rede, as nuvens de origem VMM que usam a rede de máquina virtual de origem serão exibidas, junto com as redes de máquina virtual de destino disponível nas nuvens de destino que são usadas para proteção.

Aqui está um exemplo para ilustrar esse mecanismo. Vamos dar uma organização com dois locais em Nova York e Chicago.

**Local** | **Servidor VMM** | **Redes de máquina virtual** | **Mapeadas**
---|---|---|---
Nova York | VMM Nova_york| VMNetwork1-Nova_york | Mapeados para Chicago VMNetwork1
 |  | VMNetwork2-Nova_york | Não mapeado
Chicago | Chicago VMM| VMNetwork1-Chicago | Mapeadas Nova_york VMNetwork1
 | | VMNetwork2-Chicago | Não mapeado

Com este exemplo:

- Quando uma máquina virtual de réplica é criada para qualquer máquina virtual que está conectada à VMNetwork1 Nova_york, ele será conectado à VMNetwork1 Chicago.
- Quando uma máquina virtual de réplica é criada para Nova_york VMNetwork2 ou VMNetwork2-Chicago, ele não será conectado a uma rede.

Veja aqui como nuvens do VMM estão configurados no nosso exemplo de organização e as redes lógicas associadas as nuvens.

### <a name="cloud-protection-settings"></a>Configurações de proteção de nuvem

**Nuvem protegida** | **Protegendo a nuvem** | **Rede lógica (Nova York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>DISP</p><p></p> | <p>LogicalNetwork1-Nova_york</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>DISP</p><p></p> | <p>LogicalNetwork1-Nova_york</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Configurações de rede lógica e máquina virtual

**Local** | **Rede lógica** | **Rede de máquina virtual associada**
---|---|---
Nova York | LogicalNetwork1-Nova_york | VMNetwork1-Nova_york
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-networks"></a>Redes de destino

Com base nessas configurações, quando você selecionar a rede de máquina virtual de destino, a tabela a seguir mostra as opções que estarão disponíveis.

**Selecione** | **Nuvem protegida** | **Protegendo a nuvem** | **Rede de destino disponível**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Disponível
 | GoldCloud1 | GoldCloud2 | Disponível
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Não disponível
 | GoldCloud1 | GoldCloud2 | Disponível



## <a name="multiple-subnets"></a>Várias sub-redes

Se a rede de destino tem várias sub-redes e uma dessas sub-redes tem o mesmo nome da sub-rede no qual a máquina virtual de origem está localizada, em seguida, na máquina virtual de réplica será conectada para aquela sub-rede de destino após failover. Se não houver nenhuma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.


### <a name="failback"></a>Failback

Para ver o que acontece no caso de failback (replicação inversa), vamos supor que Nova_york VMNetwork1 é mapeado para VMNetwork1-Chicago, com as seguintes configurações.


**Máquina virtual** | **Conectado à rede de máquina virtual**
---|---
VM1 | VMNetwork1-rede
VM2 (réplica do VM1) | VMNetwork1-Chicago

Com essas configurações, vamos revisar o que acontece em alguns cenários possíveis.

**Cenário** | **Resultado**
---|---
Nenhuma alteração nas propriedades de rede da máquina virtual-2 após failover. | Máquina virtual-1 permanece conectado com a rede de origem.
Propriedades de rede da máquina virtual-2 são alterados após failover e está desconectada. | Máquina virtual-1 é desconectado.
Propriedades de rede da máquina virtual-2 são alterados após failover e está conectado à VMNetwork2 Chicago. | Se não estiver mapeado VMNetwork2 Chicago, serão desconectados máquina virtual-1.
Mapeamento de rede de Chicago VMNetwork1 é alterado. | Máquina virtual-1 será conectado à rede agora mapeada para VMNetwork1-Chicago.


## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma melhor compreensão de mapeamento de rede, [começar com as implantações de recuperação do Site](site-recovery-best-practices.md).
