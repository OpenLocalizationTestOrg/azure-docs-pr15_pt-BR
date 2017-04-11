<properties
    pageTitle="O que fazer em caso de um Azure serviço interrupção que afeta os serviços de nuvem do Azure | Microsoft Azure"
    description="Saiba o que fazer em caso de uma interrupção de serviço Azure que afeta os serviços de nuvem do Azure."
    services="cloud-services"
    documentationCenter=""
    authors="kmouss"
    manager="drewm"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="cloud-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="kmouss;aglick"/>

#<a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>O que fazer em caso de um Azure serviço interrupção que afeta os serviços de nuvem do Azure

Na Microsoft, podemos faz trabalhar irreversível para garantir que nossos serviços estejam sempre disponíveis para você quando você precisa delas. Força além do nosso controle às vezes afeta conosco de maneiras que causam interrupções planejadas de serviço.

A Microsoft fornece um contrato de nível de serviço (SLA) de seus serviços como um compromisso para atividade e conectividade. O SLA para serviços Azure individuais pode ser encontrado em [Contratos de nível de serviço do Azure](https://azure.microsoft.com/support/legal/sla/).

Azure já tem muitos recursos de plataforma interna que oferecem suporte a aplicativos altamente disponíveis. Para obter mais informações sobre esses serviços, leia a [recuperação de dados e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Este artigo aborda um cenário de recuperação de desastres true, quando uma região inteira sofrer uma interrupção devido a desastre natural principal ou interrupção do serviço amplamente. Estas são ocorrências raras, mas você deve preparar para a possibilidade de que não há uma interrupção de toda a região. Se toda a região sofrer uma interrupção de serviço, as cópias localmente redundantes dos seus dados temporariamente seria indisponíveis. Se você habilitou replicação geográfica, três cópias adicionais de armazenamento do Azure blobs e tabelas são armazenadas em uma região diferente. Em caso de falta de regionais concluída ou um desastre em que a região primária não é recuperável, o Azure remapeia todas as entradas DNS para a região geográfica replicado.

>[AZURE.NOTE]Lembre-se de que você não tem qualquer controle sobre esse processo e só ocorrerá para interrupções de serviço de toda a data center. Por isso, você também deve confiar em outras estratégias de backup específicas do aplicativo para atingir o nível mais alto de disponibilidade. Para obter mais informações, consulte a seção sobre [estratégias de dados para recuperação de dados](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md#DSDR). Se você quiser poderá afetar seu próprios failover, talvez você queira considerar o uso de [armazenamento geográfica redundante de acesso de leitura (ar-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage), que cria uma cópia somente leitura de seus dados em outra região.

Para ajudá-lo a lidar com essas ocorrências raras, fornecemos as seguintes diretrizes para Azure máquinas virtuais (VMs) no caso de uma interrupção do serviço de toda a região onde seu aplicativo de máquina virtual do Azure é implantado.

##<a name="option-1-wait-for-recovery"></a>Opção 1: Espere a recuperação
Nesse caso, nenhuma ação de sua parte é necessária. Sabe que equipes Azure estão trabalhando cuidadosamente para restaurar a disponibilidade do serviço. Você pode ver o status atual do serviço no nosso [Painel de integridade de serviço do Azure](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Esta é a melhor opção se um cliente não configurou a recuperação de Site do Azure ou tem uma implantação secundária em uma região diferente.

Para clientes que desejam acesso imediato aos seus serviços de nuvem implantado, as seguintes opções estão disponíveis.

>[AZURE.NOTE]Lembre-se de que essas opções tenham a possibilidade de perda de dados.     

##<a name="option-2-re-deploy-your-cloud-service-configuration-to-a-new-region"></a>Opção 2: Implantar novamente a configuração do serviço de nuvem para uma nova região

Se você tiver seu código original, você pode simplesmente reimplantar o aplicativo, configuração associada e recursos associados a um novo serviço de nuvem em uma nova região.  

Para obter mais detalhes sobre como criar e implantar um aplicativo de serviço de nuvem, consulte [como criar e implantar um serviço de nuvem](./cloud-services-how-to-create-deploy-portal.md).

Dependendo das suas fontes de dados do aplicativo, você talvez precise verificar os procedimentos de recuperação de sua fonte de dados do aplicativo.
  * Para fontes de dados de armazenamento do Azure, consulte [replicação de armazenamento do Azure](../storage/storage-redundancy.md#read-access-geo-redundant-storage) para verificar as opções que estão disponíveis com base no modelo de replicação escolhido para o seu aplicativo.
  * Para fontes de banco de dados SQL, leia [Visão geral: nuvem business banco de dados e continuidade de recuperação de dados com o banco de dados SQL](../sql-database/sql-database-business-continuity.md) para verificar as opções disponíveis baseiam no modelo de replicação escolhido para o seu aplicativo.

##<a name="option-3-use-a-backup-deployment-through-azure-traffic-manager"></a>Opção 3: Usar uma implantação de backup através do Gerenciador de tráfego do Azure
Essa opção pressupõe que você já tiver criado sua solução de aplicativo com recuperação de dados regionais em mente. Você pode usar esta opção se você já tiver uma implantação de aplicativo de serviços de nuvem secundário que é executado em uma região diferente e conectado através de um canal de Gerenciador de tráfego. Nesse caso, verifique a integridade da implantação secundária. Se for eficaz, você pode redirecionar o tráfego para ele através do Gerenciador de tráfego do Azure. Com essa estratégia, você pode aproveitar o método de roteamento de tráfego e configurações de ordem de failover no Gerenciador de tráfego do Azure. Para obter mais informações, consulte [como configurar o Gerenciador de tráfego](../traffic-manager/traffic-manager-overview.md#how-to-configure-traffic-manager-settings).

![Balanceamento de serviços de nuvem do Azure em regiões com o Gerenciador de tráfego do Azure](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

##<a name="next-steps"></a>Próximas etapas

Para saber mais sobre como implementar uma estratégia de alta disponibilidade e a recuperação de dados, consulte [recuperação de dados e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Para desenvolver uma compreensão técnica detalhada dos recursos de uma plataforma de nuvem, consulte [orientações técnicas resiliência Azure](../resiliency/resiliency-technical-guidance.md).

Se as instruções são não limpar ou se quiser que a Microsoft para fazer as operações em seu nome entre em contato com o [Suporte ao cliente](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
