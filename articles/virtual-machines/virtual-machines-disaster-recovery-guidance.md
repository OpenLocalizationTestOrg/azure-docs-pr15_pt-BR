<properties
    pageTitle="O que fazer se uma interrupção do serviço Azure afeta Azure máquinas virtuais | Microsoft Azure"
    description="Saiba o que fazer se uma interrupção do serviço Azure afeta Azure máquinas virtuais."
    services="virtual-machines"
    documentationCenter=""
    authors="kmouss"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines"
    ms.workload="virtual-machines"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="kmouss;aglick"/>

#<a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-virtual-machines"></a>O que fazer se uma interrupção do serviço Azure afeta Azure máquinas virtuais

Na Microsoft, podemos faz trabalhar irreversível para garantir que nossos serviços estejam sempre disponíveis para você quando você precisa delas. Força além do nosso controle às vezes afeta conosco de maneiras que causam interrupções planejadas de serviço.

A Microsoft fornece um contrato de nível de serviço (SLA) de seus serviços como um compromisso para atividade e conectividade. O SLA para serviços Azure individuais pode ser encontrado em [Contratos de nível de serviço do Azure](https://azure.microsoft.com/support/legal/sla/).

Azure já tem muitos recursos de plataforma interna que oferecem suporte a aplicativos altamente disponíveis. Para obter mais informações sobre esses serviços, leia a [recuperação de dados e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Este artigo aborda um cenário de recuperação de desastres true, quando uma região inteira sofrer uma interrupção devido a desastre natural principal ou interrupção do serviço amplamente. Estas são ocorrências raras, mas você deve preparar para a possibilidade de que não há uma interrupção de toda a região. Se toda a região sofrer uma interrupção de serviço, as cópias localmente redundantes dos seus dados temporariamente seria indisponíveis. Se você habilitou replicação geográfica, três cópias adicionais de armazenamento do Azure blobs e tabelas são armazenadas em uma região diferente. Em caso de falta de regionais concluída ou um desastre em que a região primária não é recuperável, o Azure remapeia todas as entradas DNS para a região geográfica replicado.

>[AZURE.NOTE]Lembre-se de que você não tem qualquer controle sobre esse processo e só ocorrerá para interrupções de serviço de toda a região. Por isso, você também deve confiar em outras estratégias de backup específicas do aplicativo para atingir o nível mais alto de disponibilidade. Para obter mais informações, consulte a seção sobre [estratégias de dados para recuperação de dados](../resiliency/resiliency-disaster-recovery-azure-applications.md#data-strategies-for-disaster-recovery).

Para ajudá-lo a lidar com essas ocorrências raras, fornecemos as seguintes diretrizes para Azure máquinas virtuais no caso de uma interrupção do serviço de toda a região onde seu aplicativo do Azure máquina virtual é implantado.

##<a name="option-1-wait-for-recovery"></a>Opção 1: Espere a recuperação
Nesse caso, nenhuma ação de sua parte é necessária. Sabe que estamos trabalhando cuidadosamente para restaurar a disponibilidade do serviço. Você pode ver o status atual do serviço no nosso [Painel de integridade de serviço do Azure](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Esta é a melhor opção se você não tiver configurado recuperação de Site do Azure, backup de máquinas virtuais, armazenamento de localização geográfica redundantes de acesso de leitura ou armazenamento geográfica redundantes antes da interrupção. Se você configurou armazenamento redundante de localização geográfica ou armazenamento de localização geográfica redundantes de acesso de leitura para a conta de armazenamento onde a unidades de disco rígidas virtuais (VHDs) sua máquina virtual são armazenadas, você pode pesquisar para recuperar a imagem base VHD e tente provisionar uma nova VM dele. Isso não é uma opção preferencial, pois não há nenhuma garantia de sincronização de dados, a menos que o backup de máquina virtual do Azure ou recuperação de Site do Azure são usadas. Consequentemente, esta opção não é garantia de trabalhar.

Para clientes que desejam acesso imediato a máquinas virtuais, as duas opções a seguintes estão disponíveis.  

>[AZURE.NOTE]Lembre-se de que ambas as opções a seguintes têm a possibilidade de perda de dados.     

##<a name="option-2-restore-a-vm-from-a-backup"></a>Opção 2: Restaurar uma máquina virtual de um backup
Para clientes que configuraram um backup de máquina virtual, você pode restaurar a máquina virtual do seu ponto de recuperação e backup.

Para restaurar uma nova VM de Backup do Azure, consulte [restaurar máquinas virtuais no Azure](../backup/backup-azure-restore-vms.md).

Para ajudá-lo a planejar sua infraestrutura de backup do Azure máquinas virtuais, consulte [planejar sua infraestrutura de backup de máquina virtual no Azure](../backup/backup-azure-vms-introduction.md).

##<a name="option-3-initiate-a-failover-by-using-azure-site-recovery"></a>Opção 3: Iniciar um failover usando recuperação de Site do Azure
Se você tiver configurado recuperação de Site do Azure para trabalhar com suas máquinas virtuais Azure afetadas, você pode restaurar suas VMs de suas réplicas. Essas réplicas podem residem no Azure ou no local. Nesse caso, você pode criar uma nova VM da réplica existente. Para restaurar suas VMs de uma réplica de recuperação de Site do Azure, consulte [máquinas virtuais de migrar Azure IaaS entre regiões Azure com recuperação de Site do Azure](../site-recovery/site-recovery-migrate-azure-to-azure.md).

>[AZURE.NOTE]Embora o sistema operacional do Azure máquina virtual e discos de dados serão replicados para um VHD secundário, se eles estiverem em uma conta de armazenamento de localização geográfica redundantes de acesso de leitura ou de armazenamento redundante de localização geográfica, cada VHD é replicado independentemente. Esse nível de replicação não garante a consistência entre os VHDs replicados. Se seu aplicativo e/ou bancos de dados que usam esses discos de dados tem dependências em si, ele não é garantia de que todos os VHDs são replicados como um instantâneo. Ele também não há garantia que a réplica VHD de armazenamento redundante de localização geográfica ou acesso de leitura geográfica-redundantes resultará em um instantâneo consistente de aplicativo inicializar a máquina virtual.

##<a name="next-steps"></a>Próximas etapas
Para saber mais sobre como implementar uma estratégia de alta disponibilidade e a recuperação de dados, consulte [recuperação de dados e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Para desenvolver uma compreensão técnica detalhada dos recursos de uma plataforma de nuvem, consulte [orientações técnicas resiliência Azure](../resiliency/resiliency-technical-guidance.md).

Para saber como fazer backup VMs, consulte [fazer backup Azure máquinas virtuais](../backup/backup-azure-vms.md).

Saiba como usar recuperação de Site do Azure para coordenar e automatizar a proteção de suas físicas (e virtuais) Windows e Linux máquinas que são executados em VMWare e VMs Hyper-V, consulte [Recuperação de Site do Azure](https://azure.microsoft.com/documentation/learning-paths/site-recovery/).

Se as instruções são não limpar ou se quiser que a Microsoft para fazer as operações em seu nome, contate o [Suporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
