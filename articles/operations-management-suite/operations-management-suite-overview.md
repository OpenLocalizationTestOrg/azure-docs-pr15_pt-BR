<properties
   pageTitle="Visão geral do pacote de gerenciamento (OMS) operações | Microsoft Azure"
   description="Pacote de gerenciamento de operações da Microsoft (OMS) é na nuvem solução de gerenciamento da Microsoft que ajuda você a gerenciar e proteger seu local e infraestrutura de nuvem.  Este artigo identifica diferentes serviços incluídos no OMS e fornece links para o seu conteúdo detalhado."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="bwren" />

# <a name="what-is-operations-management-suite-oms"></a>O que é o pacote de gerenciamento de operações (OMS)?

Pacote de gerenciamento de operações da Microsoft (OMS) é na nuvem solução de gerenciamento da Microsoft que ajuda você a gerenciar e proteger seu local e infraestrutura de nuvem.  Já que OMS é implementado como um serviço baseado em nuvem, você pode tê-lo em funcionamento rapidamente com investimento mínimo nos serviços de infraestrutura.  Novos recursos são entregues automaticamente, economizando manutenção contínua e atualizar custos.

Além de oferecer serviços valiosos por conta própria, OMS pode integrar com componentes do System Center como System Center Operations Manager para estender os investimentos de gerenciamento na nuvem.  System Center e OMS podem trabalhar juntos para proporcionar uma experiência de gerenciamento de híbrido completo.

As seções a seguir fornecem uma descrição de alto nível das áreas valor diferente de OMS e os serviços que implementam-los.  Você pode consultar a arquitetura do OMS para obter uma visão geral dos diferentes componentes OMS antes de examinar a documentação detalhada para cada um.


## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![Ideias e análise](media/operations-management-suite-overview/icon-insight-analytics.png) Ideias e análise

[Análise de log](http://azure.microsoft.com/documentation/services/log-analytics) ajuda você a coletar, correlação, pesquisar e atuar em log e dados de desempenho gerados pelo sistemas operacionais e aplicativos. Ele fornece ideias operacionais em tempo real usando pesquisa integrada e painéis personalizados prontamente analisar milhões de registros em todas as cargas de trabalho e servidores, independentemente de sua localização física.

Você pode adicionar facilmente soluções para análises de Log que definem dados a serem coletados e a lógica para sua análise.  Soluções podem incluir funcionalidade adicional que é fornecida automaticamente para agentes com pouca ou nenhuma configuração.  Além de usar ferramentas de análise fornecidas pelo soluções individuais, você pode executar pesquisas personalizadas entre o conjunto de dados inteiro para correlação dados entre sistemas e aplicativos.  


## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![Controle e automação](media/operations-management-suite-overview/icon-automation-control.png) Controle e automação

Automação Azure automatiza processos administrativos com [runbooks](../automation/automation-runbook-types.md) que são baseados no PowerShell e executados na nuvem Azure.  Runbooks pode acessar qualquer produto ou serviço que pode ser gerenciado com PowerShell incluindo recursos em outros nuvens como Amazon Web Services (AWS).  Runbooks também podem ser executadas em um servidor no seu centro de dados locais para gerenciar recursos locais.

Automação Azure oferece gerenciamento de configuração com [DSC do PowerShell](../automation/automation-dsc-overview.md).  Você pode criar e gerenciar recursos de DSC hospedados no Azure e aplicá-las aos sistemas de nuvem e local para definir e aplicar automaticamente a sua configuração.


## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![Proteção e recuperação](media/operations-management-suite-overview/icon-protection-recovery.png) Proteção e recuperação de dados

[Backup do Azure](http://azure.microsoft.com/documentation/services/backup) protege seus dados de aplicativo e retém-la por anos sem qualquer investimento de capital e com custos operacionais mínimo.  Ele pode fazer backup de dados de servidores físicos e virtuais do Windows além das cargas de trabalho de aplicativos como o SQL Server e o SharePoint.  Ele também pode ser usado por System Center dados Protection Manager (DPM) para duplicar dados protegidos para Azure para redundância e armazenamento a longo prazo.

[Recuperação de Site do Azure](http://azure.microsoft.com/documentation/services/site-recovery) contribui para continuidade de negócios e uma estratégia de recuperação (BCDR) desastres por coordenação replicação, failover e recuperação de local Hyper-V virtual máquinas, máquinas virtuais VMware e servidores físicos do Windows/Linux. Você pode replicar máquinas para uma central de dados secundária ou estender seu data center por replicá-los no Azure. Recuperação de site também fornece failover simples e recuperação para cargas de trabalho. Ele integra mecanismos de recuperação de desastres como o SQL Server AlwaysOn e fornece os planos de recuperação para fácil failover das cargas de trabalho que são hierárquico em várias máquinas.


## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![OMS segurança e conformidade](media/operations-management-suite-overview/icon-security-compliance.png) Segurança e conformidade
Segurança e conformidade ajuda você a identificar, avaliar e reduzir os riscos de segurança em sua infraestrutura.  Esses recursos do OMS são implementados por meio de várias soluções de análise de Log que analisar dados de log e a configuração de sistemas de agente para ajudá-lo a garantir a segurança em andamento do seu ambiente.

- A [solução de segurança e auditoria](oms-security-getting-started.md ) coleta e analisa eventos de segurança em sistemas gerenciados para identificar atividades suspeitas.
- A [solução Antimalware](log-analytics-malware.md ) relatórios sobre o status de proteção antimalware em sistemas gerenciados.  
- A solução de atualizações do sistema realiza uma análise das atualizações de segurança e outras atualizações em sistemas gerenciados para que você identificar facilmente sistemas exigindo patches.


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [a análise de Log](http://azure.microsoft.com/documentation/services/log-analytics).
- Saiba mais sobre [automação Azure](../automation/automation-intro.md).
- Saiba mais sobre [Backup Azure](http://azure.microsoft.com/documentation/services/backup).
- Saiba mais sobre [a recuperação de Site Azure](http://azure.microsoft.com/documentation/services/site-recovery).
