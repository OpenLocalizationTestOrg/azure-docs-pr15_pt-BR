<properties
   pageTitle="Introdução à integração de log do Microsoft Azure | Microsoft Azure"
   description="Saiba mais sobre a integração de log Azure, seus recursos principais e como ele funciona."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# <a name="introduction-to-microsoft-azure-log-integration-preview"></a>Introdução à integração de log do Microsoft Azure (visualização)

Saiba mais sobre a integração de log Azure, seus recursos principais e como ele funciona.

## <a name="overview"></a>Visão geral

Plataforma como um serviço (PaaS) e infraestrutura como um serviço (IaaS) hospedado no Azure gerar uma grande quantidade de dados em logs de segurança. Esses registros contêm informações vitais que podem fornecer inteligência e ideias sofisticadas para violações de políticas, ameaças internas e externas, problemas de conformidade regulamentar e anomalias na rede, host e atividades do usuário.

Integração do Azure log permite integrar brutos logs de seus recursos Azure seus sistemas de gerenciamento de evento (SIEM) e informações de segurança do local. Integração do Azure log coletará diagnóstico do Azure do Windows *(WAD)* máquinas virtuais, bem como diagnósticos de soluções de parceiros como um Firewall de aplicativo de Web (WAF). Essa integração oferece um painel unificado para todos os seus ativos, local ou na nuvem, para que você pode agregar, correlação, analisar e alertar eventos de segurança.

![Integração do Azure log][1]

## <a name="what-logs-can-i-integrate"></a>Quais registros pode integrar?

Azure produz log extensivo para cada serviço Azure. Esses logs são categorizados por dois tipos principais:

- **Logs de gerenciamento do controle**, que dão visibilidade as operações Azure Gerenciador de recursos de criar, atualizar e excluir. Logs de auditoria Azure é um exemplo desse tipo de log.
- **Logs de plano de dados**, que dão visibilidade as eventos gerados como parte do uso de um recurso Azure. Exemplos desse tipo de registro são os eventos do Windows sistema, segurança, e logs de aplicativo em uma máquina virtual.

Integração de log Azure atualmente oferece suporte a integração de Logs de auditoria do Azure, logs de máquina virtual e o Centro de segurança do Azure alertas.

Se você tiver dúvidas sobre a integração de Log do Azure, envie um email para [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Próximas etapas

Neste documento, você introduzidas integração do Azure log. Para saber mais sobre a integração do registro Azure e os tipos de logs com suporte, consulte o seguinte:

- [Integração de Log do Microsoft Azure para logs Azure (visualização)](https://www.microsoft.com/download/details.aspx?id=53324) – Centro de Download para obter detalhes, requisitos do sistema e instruções de instalação a integração de log Azure.
- [Introdução ao integração do Azure log](security-azure-log-integration-get-started.md) - este tutorial orienta instalação de integração de log Azure e integração de logs do armazenamento do Azure, Logs de auditoria do Azure e alertas de segurança central.
- [Etapas de configuração de parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – esta postagem de blog mostra como configurar a integração de log Azure para trabalhar com soluções de parceiros Splunk, HP ArcSight e IBM QRadar.
- [Log azure integração perguntas frequentes (FAQ)](security-azure-log-integration-faq.md) - perguntas frequentes sobre este responde perguntas sobre a integração de log Azure.
- [Alertas do Centro de segurança integrando com o Azure logon integração](../security-center/security-center-integrating-alerts-with-log-integration.md) – este documento mostra como sincronizar os alertas do Centro de segurança, juntamente com coletados pelo diagnóstico do Azure e Logs de auditoria do Azure, com sua solução SIEM ou a análise de log de eventos de segurança de máquina virtual.
- [Novos recursos para diagnóstico do Azure e Logs de auditoria do Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – esta postagem de blog apresenta os Logs de auditoria do Azure e outros recursos que ajudam você obtém ideias para as operações de seus recursos Azure.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
