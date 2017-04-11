
<properties
    pageTitle="Recuperação de Site Azure: Replicar Hyper-V virtual máquinas em um único servidor VMM | Microsoft Azure"
    description="Este artigo descreve como replicar máquinas virtuais Hyper-V quando tiver apenas um único servidor VMM."
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
    ms.workload="backup-recovery"
    ms.date="08/24/2016"
    ms.author="raynew"/>

#  <a name="replicate-hyper-v-virtual-machines-on-a-single-vmm-server"></a>Replicar máquinas virtuais de Hyper-V em um único servidor VMM

Leia este artigo para saber como replicar máquinas virtuais de Hyper-V localizadas em um servidor de host Hyper-V em uma nuvem VMM quando tiver apenas um único servidor VMM na sua implantação.

Azure tem dois diferentes [modelos de implantação](../resource-manager-deployment-model.md) para criar e trabalhar com recursos: Gerenciador de recursos do Azure e clássico. Azure também tem dois portais – o portal de clássico Azure que suporta o modelo clássico de implantação e o portal do Azure com suporte para ambos os modelos de implantação. Este artigo contém instruções para configurar a replicação no portal do Azure.


Se você tiver dúvidas após ler este artigo, publique-os nos comentários Disqus na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Visão geral

Se você deseja duplicar VMs Hyper-V localizados em hosts Hyper-V no VMM e tiver apenas um único servidor VMM, você pode [replicar para Azure](site-recovery-vmm-to-azure.md), ou entre nuvens no servidor VMM único.

Recomendamos que você replica no Azure porque failover e recuperação não são perfeitas ao replicar entre nuvens e um número de etapas manuais é necessárias. Se você quiser replicar usando somente o servidor VMM, você pode fazer o seguinte:

- Duplicar com um servidor VMM autônomo único
- Duplicar com um único servidor VMM implantado em um cluster de Windows ampliado


## <a name="replicate-across-sites-with-a-single-standalone-vmm-server"></a>Duplicar em sites com um servidor VMM autônomo único

![Servidor virtual de VMM autônomo](./media/site-recovery-single-vmm/single-vmm-standalone.png)

Neste cenário você implantar o servidor VMM único como uma máquina virtual no local principal e replica essa VM para um site secundário usando Hyper-V Replica e recuperação de Site.

1. Configure o VMM em uma VM Hyper-V. Sugerimos que você instale instância do SQL Server usada pelo VMM na mesma VM para economizar tempo. Se você quiser usar uma instância remota do SQL Server e uma interrupção ocorre, você precisa recuperar essa instância antes que você pode recuperar VMM.
2. Certifique-se de que o servidor do VMM tem pelo menos duas nuvens configurados. Uma nuvem contém as VMs deseja duplicar e outra nuvem serve como local secundário. Na nuvem que contém as VMs que você deseja proteger deve ter:

    - Um ou mais VMM hospede grupos que contém um ou mais servidores de host Hyper-V em cada grupo de host.
    - Pelo menos uma máquina virtual do Hyper-V em cada servidor de host Hyper-V.

3. Criar um cofre de serviços de recuperação, gerar baixar uma chave de registro do cofre e registrar o servidor VMM no cofre. Durante o registro que você instale o provedor de recuperação de Site do Azure do servidor VMM.
4. Configurar um ou mais nuvens na VM VMM e adicione os hosts Hyper-V nessas nuvens.
3. Defina configurações de proteção para as nuvens. Você pode especificar o nome do servidor VMM único como locais de origem e destino. Para configurar mapeamento de rede, mapeie a rede de máquina virtual para a nuvem com as VMs que você deseja proteger, para a rede de máquina virtual para a nuvem de replicação.
4. Habilite replicação inicial para VMs que você deseja proteger na rede porque os dois nuvens encontram-se no mesmo servidor.
4. No console Gerenciador Hyper-V, habilitar Hyper-V Replica no host Hyper-V que contém a VM VMM e habilitar a replicação na máquina virtual. Verifique se que você não adicionar VMM VM para qualquer nuvens protegidos por recuperação do Site. Isso garante que as configurações de Hyper-V Replica não são substituídas por recuperação do Site.
5. Se você deseja criar planos de recuperação, especifique o mesmo servidor VMM para origem e destino.

Siga as instruções [deste artigo](site-recovery-vmm-to-vmm.md) para criar um cofre, registrar o servidor e configurar a proteção.

### <a name="what-to-do-in-an-outage"></a>O que fazer em uma interrupção

Se ocorre uma interrupção concluída e você precisa operar do local secundário, faça o seguinte:

1.  No console Gerenciador Hyper-V no site secundário, execute um failover não planejado falha sobre o VMM VM do principal para site secundário.
2.  Verifique se a VM VMM está em execução no site secundário.
3.  No cofre de serviços de recuperação, execute um failover não planejado falha sobre a carga de trabalho VMs do principal para nuvens secundários. Para concluir o failover planejado das VMs, confirme o failover ou selecionar um ponto de recuperação diferentes conforme necessário.
4.  Após o failover planejado for concluído, os usuários podem acessar recursos de carga de trabalho no site secundário.

Quando o local principal estiver operando normalmente novamente, faça o seguinte:

1.  No console Gerenciador Hyper-V, habilite replicação inversa para VM VMM, para iniciar a replicação de secundário para primário.
2.  No console Gerenciador Hyper-V, execute um failover planejado para falhar novamente a VM VMM para o site principal. Confirme o failover para concluí-la. Em seguida, habilite replicação reversa iniciar a replicação o VMM do principal para o secundário.
3.  No cofre de serviços de recuperação, habilite replicação inversa para a carga de trabalho VMs, para iniciar replicá-los de secundário para primário.
4.  No cofre de serviços de recuperação, execute um failover planejado para falhar novamente a carga de trabalho VMs ao local principal. Confirme o failover para concluí-la. Em seguida, habilite replicação inversa iniciar a replicação a carga de trabalho VMs do principal para o secundário.



## <a name="replicate-across-sites-with-a-single-vmm-server-in-a-stretched-cluster"></a>Duplicar em sites com um único servidor VMM em um cluster ampliado

![Agrupadas servidor virtual do VMM](./media/site-recovery-single-vmm/single-vmm-cluster.png)

Em vez de Implantando um servidor VMM autônomo como uma máquina virtual que replica para um site secundário, você pode disponibilizar VMM altamente Implantando-o como uma máquina virtual em um cluster de failover do Windows. Isso fornece resiliência de carga de trabalho e proteção contra falha de hardware. Para implantar com recuperação de Site VM VMM deve ser implantado em um cluster estendido entre sites separados geograficamente. Para fazer isso:

1. Instalar o VMM em uma máquina virtual em um cluster de failover do Windows e selecione a opção para executar o servidor como altamente disponível durante a instalação.
2. Instância do SQL Server que é usada pelo VMM deve ser replicada com grupos de disponibilidade do SQL Server AlwaysOn para que haja uma réplica do banco de dados no local secundário.
3. Siga as instruções [deste artigo](site-recovery-vmm-to-vmm.md) para criar um cofre, registrar o servidor e configurar a proteção. Você precisa registrar cada servidor VMM no cluster no cofre de serviços de recuperação. Para fazer isso, você deve instala o provedor em um nó ativo e registrar o servidor VMM. Em seguida, instalar o provedor em outros nós.

### <a name="what-to-do-in-an-outage"></a>O que fazer em uma interrupção

Quando ocorre uma interrupção, servidor do VMM e seu banco de dados do SQL Server correspondente são falhou e acessados a partir do site secundário.


## <a name="next-steps"></a>Próximas etapas

[Saiba mais](site-recovery-vmm-to-vmm.md) sobre implantação detalhado de recuperação do Site do VMM replicação VMM.
