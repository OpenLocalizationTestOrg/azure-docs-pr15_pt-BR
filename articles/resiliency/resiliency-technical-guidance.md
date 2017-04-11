<properties
   pageTitle="Índice de orientações técnicas de resiliência | Microsoft Azure"
   description="Índice de artigos técnicos sobre compreensão e Projetando flexível e altamente disponível, aplicativos tolerância, bem como planejamento para continuidade de negócios e a recuperação de desastres"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance"></a>Orientações técnicas resiliência Azure

##<a name="introduction"></a>Introdução

Requisitos de recuperação de desastres e alta disponibilidade de reunião requer dois tipos de dados de Conhecimento:

- Técnicas detalhadas sobre os recursos de uma plataforma de nuvem
- Conhecimento de como projetar corretamente um serviço distribuído

Esta série de artigos aborda o primeiro: os recursos e limitações da plataforma Windows Azure relacionadas com resiliência (às vezes chamada de continuidade de negócios). Se você estiver interessado no último, consulte a série de artigos voltada para [recuperação de dados e alta disponibilidade para aplicativos do Azure](https://aka.ms/drtechguide). Embora esta série de artigos toca em padrões de arquitetura e design, que não é o foco da série. Para obter orientação de design, você pode consultar o material na seção [recursos adicionais](#additional-resources) .

As informações são organizadas nos seguintes artigos:

- [Recuperação de falhas locais](resiliency-technical-guidance-recovery-local-failures.md).
Hardware físico (por exemplo, unidades, servidores e dispositivos de rede) pode falhar. Recursos podem ser espaço termine durante picos de carga. Este artigo descreve os recursos que o Azure fornece para manter a alta disponibilidade nessas condições.

- [Recuperação a partir de uma interrupção do serviço do Azure toda a região](resiliency-technical-guidance-recovery-loss-azure-region.md).
Falhas de ampla são raras, mas eles são teoricamente possíveis. Regiões inteiras podem ficar isoladas devido a falhas de rede ou pode estar danificados física de desastres naturais. Este artigo explica como usar o Azure para criar aplicativos que se estendem por regiões geograficamente diversos.

- [Recuperação do local para o Azure](resiliency-technical-guidance-recovery-on-premises-azure.md).
Nuvem significativamente altera a economia de recuperação de dados, permitindo que as organizações usem Azure para estabelecer um segundo local para recuperação. Você pode fazer isso em uma fração do custo de construção e a manutenção de um data center secundário. Este artigo explica os recursos que Azure fornece para estender uma análise de locais na nuvem.

- [A recuperação de corrupção de dados ou exclusões acidentais](resiliency-technical-guidance-recovery-data-corruption.md).
Aplicativos podem ter bugs dados corrompidos. Operadores incorretamente podem excluir dados importantes. Este artigo explica o que Azure fornece para fazer backup de dados e restaurar a um ponto anterior-tempo.

##<a name="additional-resources"></a>Recursos adicionais

- [Recuperação de dados e alta disponibilidade em aplicativos criados no Microsoft Azure](resiliency-disaster-recovery-high-availability-azure-applications.md).
Este artigo é uma visão geral detalhada de disponibilidade e recuperação de dados. Ele cubra o desafio de replicação manual para referência e dados de transações. As seções finais fornecem resumos de diferentes tipos de topologias de recuperação de desastres que se estendem por regiões Azure para o nível mais alto de disponibilidade.

- [Lista de verificação de alta disponibilidade](resiliency-high-availability-checklist.md).
Este artigo é uma lista de recursos, serviços, e designs que podem ajudar a aumentam a resiliência e a disponibilidade de seu aplicativo.

- [Orientação de resiliência de serviço do Microsoft Azure](resiliency-service-guidance-index.md).
Este artigo é um índice de serviços do Azure e fornece links para orientações de recuperação de desastres e a orientação de design.

- [Visão geral: nuvem business banco de dados e continuidade de recuperação de dados com o banco de dados do SQL](../sql-database/sql-database-business-continuity.md).
Este artigo fornece técnicas de banco de dados do Azure SQL para disponibilidade. Ele centraliza principalmente em estratégias de backup e restauração. Se você usar o banco de dados do SQL Azure em seu serviço de nuvem, você deve examinar este documento e seus recursos relacionados.

- [Alta disponibilidade e recuperação de dados para o SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).
Este artigo discute opções de disponibilidade que você pode explorar quando você usa infraestrutura como um serviço (IaaS) para hospedar seus serviços de banco de dados. Ele aborda os grupos de disponibilidade sempre ativado, espelhamento de banco de dados, envio de log e backup e restauração. Vários tutoriais mostram como usar essas técnicas.

- [Práticas recomendadas para criar serviços em grande escala em serviços de nuvem do Azure](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/).
Este artigo se concentra nos desenvolvendo arquiteturas de nuvem altamente escaláveis. Muitas das técnicas que utilizam para melhorar a escalabilidade também melhoram a disponibilidade. Além disso, se seu aplicativo não consegue dimensionar em aumento de carga, escalabilidade se torna um problema de disponibilidade.

- [Backup e restauração para o SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).
Este artigo fornece orientações técnicas sobre como fazer backup e restaurar o Microsoft SQL Server em máquinas virtuais do Azure em execução.

- [Failsafe: orientação para arquiteturas de nuvem flexível](https://channel9.msdn.com/Series/FailSafe).
Este artigo fornece orientação para a criação de arquiteturas de nuvem flexível, orientação para a implementação essas arquiteturas em tecnologias Microsoft e receitas para implementar essas arquiteturas para cenários específicos.

- [Estudo de caso técnico: usando tecnologias de nuvem para melhorar a recuperação de dados](https://www.microsoft.com/itshowcase/Article/Content/737/Using-cloud-technologies-to-improve-disaster-recovery).
Este estudo de caso mostra como o Microsoft IT usado Azure para melhorar a recuperação de dados.

##<a name="next-steps"></a>Próximas etapas

Este artigo é parte de uma série voltada para orientações técnicas para resiliência Azure. Se você estiver interessado em outros artigos desta série de leitura, você pode começar com [a recuperação de falhas locais](resiliency-technical-guidance-recovery-local-failures.md).
