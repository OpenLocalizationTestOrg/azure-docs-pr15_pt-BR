<properties
    pageTitle="Mapear armazenamento em recuperação de Site do Azure para replicação de máquina virtual do Hyper-V entre locais dos data centers | Microsoft Azure"
    description="Prepare o mapeamento de armazenamento para replicação de máquina virtual do Hyper-V entre dois centros de dados locais com o Azure recuperação do Site."
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
    ms.date="07/06/2016"
    ms.author="raynew"/>


# <a name="prepare-storage-mapping-for-hyper-v-virtual-machine-replication-between-two-on-premises-datacenters-with-azure-site-recovery"></a>Preparar o mapeamento de armazenamento para replicação de máquina virtual do Hyper-V entre dois centros de dados local com recuperação de Site do Azure


Recuperação de Site Azure contribui para sua estratégia de recuperação (BCDR) de desastre e continuidade de negócios por coordenação replicação, failover e recuperação de máquinas virtuais e servidores físicos. Este artigo descreve o mapeamento de armazenamento, que ajuda a fazer ideal usa de armazenamento quando você estiver usando a recuperação de Site para replicar máquinas virtuais Hyper-V entre data centers VMM de dois locais.

Poste quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Visão geral

Mapeamento de armazenamento só é relevante quando você estiver replicar máquinas virtuais Hyper-V que se encontram em nuvens do VMM de um data center principal para um centro de dados secundário usando replicação Hyper-V Replica ou SAN, da seguinte maneira:


- **Local para replicação local com Hyper-V Replica)** — Você configurar o armazenamento de mapeamento por mapeamento classificações de armazenamento em uma fonte e destino servidores VMM para fazer o seguinte:

    - **Identificar o armazenamento de destino para máquinas virtuais de réplica**— máquinas virtuais serão replicados para um destino de armazenamento (SMB compartilhar ou cluster volumes compartilhados (CSVs)) que você escolher.
    - **Posicionamento de máquina virtual réplica**— mapeamento de armazenamento é usado para colocar ideal máquinas virtuais de réplica em servidores de host Hyper-V. Máquinas virtuais de réplica serão colocadas em hosts que podem acessar a classificação de armazenamento mapeada.
    - **Nenhum mapeamento de armazenamento**— se você não configurar mapeamento de armazenamento, máquinas virtuais serão replicadas para o local de armazenamento padrão especificado no servidor host Hyper-V associado a máquina virtual de réplica.

- **Local para replicação local com SAN**— você configurar o armazenamento de mapeamento por mapeamento pools de matrizes de armazenamento em uma fonte e VMM servidores de destino.
    - **Especificar pool**— Especifica qual pool de armazenamento secundário recebe dados de replicação do pool principal.
    - **Identificar pools de armazenamento de destino**— garante que os LUNs em um grupo de replicação de origem são duplicadas ao pool de armazenamento de destino mapeados de sua escolha.

## <a name="set-up-storage-classifications-for-hyper-v-replication"></a>Configurar classificações de armazenamento para replicação de Hyper-V

Quando você estiver usando o Hyper-V Replica replicar com recuperação do Site, você mapear entre classificações de armazenamento em servidores VMM de origem e destino, ou em um único servidor VMM se dois sites são gerenciadas pelo mesmo servidor VMM. Observe que:

- Quando o mapeamento está configurado corretamente e replicação está habilitada, virtual disco de uma máquina virtual no local principal será replicado para armazenamento no local de destino mapeados.
- Classificações de armazenamento devem estar disponíveis para os grupos de host localizados em nuvens de origem e destino.
- Classificações não precisam ter o mesmo tipo de armazenamento. Por exemplo, você pode mapear uma classificação de origem que contém compartilhamentos SMB para uma classificação de destino que contém CSVs.
- Leia mais em [como criar classificações de armazenamento no VMM](https://technet.microsoft.com/library/gg610685.aspx).

## <a name="example"></a>Exemplo

Se classificações estão configuradas corretamente no VMM quando você seleciona de origem e destino servidor VMM durante o mapeamento de armazenamento, as classificações de origem e destino serão exibidas. Aqui está um exemplo de compartilhamentos de arquivos de armazenamento e classificações para uma organização com dois locais em Nova York e Chicago.

**Local** | **Servidor VMM** | **Compartilhamento de arquivo (origem)** | **Classificação (origem)** | **Mapeadas** | **Compartilhamento de arquivo (destino)**
---|---|--- |---|---|---
Nova York | VMM_Source| SourceShare1 | OURO | GOLD_TARGET | TargetShare1
 |  | SourceShare2 | PRATA | SILVER_TARGET | TargetShare2
 | | SourceShare3 | BRONZE | BRONZE_TARGET | TargetShare3
Chicago | VMM_Target |  | GOLD_TARGET | Não mapeado |
| | | SILVER_TARGET | Não mapeado |
 | | | BRONZE_TARGET | Não mapeado

Você deve configurá-los na guia **Armazenamento de servidor** na página **recursos** do portal da recuperação do Site.

![Configurar mapeamento de armazenamento](./media/site-recovery-storage-mapping/storage-mapping1.png)

Com este exemplo:
- Quando uma uma máquina virtual de réplica é criada para qualquer máquina virtual armazenamento de ouro (SourceShare1), ele será replicado em um armazenamento de GOLD_TARGET (TargetShare1).
- Quando uma máquina virtual de réplica é criada para qualquer máquina virtual armazenamento prata (SourceShare2), ele será replicado para um armazenamento SILVER_TARGET (TargetShare2) e assim por diante.

Os compartilhamentos de arquivo real e suas classificações atribuídas no VMM aparecem na próxima captura de tela.

![Classificações de armazenamento no VMM](./media/site-recovery-storage-mapping/storage-mapping2.png)

## <a name="multiple-storage-locations"></a>Vários locais de armazenamento

Se a classificação de destino for atribuída a vários compartilhamentos SMB ou CSVs, o local de armazenamento ideal será selecionado automaticamente quando a máquina virtual está protegida. Se nenhum armazenamento de destino adequado está disponível com a classificação especificada, o local de armazenamento padrão especificado no host Hyper-V é usado para colocá-a réplica virtual.

A tabela a seguir mostram como classificação de armazenamento e volumes compartilhados do cluster estão configurados em nosso exemplo.

**Local** | **Classificação** | **Armazenamento associado**
---|---|---
Nova York | OURO | <p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p>
 | PRATA | <p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p>
Chicago | GOLD_TARGET | <p>C:\ClusterStorage\TargetVolume1</p><p>\\FileServer\TargetShare1</p>
 | SILVER_TARGET| <p>C:\ClusterStorage\TargetVolume2</p><p>\\FileServer\TargetShare2</p>

Esta tabela resume o comportamento quando você ativa a proteção para máquinas virtuais (VM1 - VM5) nesse ambiente de exemplo.

**Máquina virtual** | **Armazenamento de origem** | **Classificação de origem** | **Armazenamento de destino mapeados**
---|---|---|---
VM1 | C:\ClusterStorage\SourceVolume1 | OURO | <p>C:\ClusterStorage\SourceVolume1</p><p>\\\FileServer\SourceShare1</p><p>Os dois GOLD_TARGET</p>
VM2 | \\FileServer\SourceShare1 | OURO | <p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> <p>Os dois GOLD_TARGET</p>
VM3 | C:\ClusterStorage\SourceVolume2 | PRATA | <p>C:\ClusterStorage\SourceVolume2</p><p>\FileServer\SourceShare2</p>
VM4 | \FileServer\SourceShare2 | PRATA |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p><p>Os dois SILVER_TARGET</p>
VM5 | C:\ClusterStorage\SourceVolume3 | N/D | Nenhum mapeamento, o local de armazenamento padrão do host Hyper-V é usado

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma melhor compreensão de mapeamento de armazenamento, [Prepare-se para implantar o Azure recuperação do Site](site-recovery-best-practices.md).
