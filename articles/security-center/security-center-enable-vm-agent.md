<properties
   pageTitle="Habilitar o agente de máquina virtual na Central de segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar a recomendação de Central de segurança do Azure **Habilitar agente de máquina virtual**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/17/2016"
   ms.author="terrylan"/>

# <a name="enable-vm-agent-in-azure-security-center"></a>Habilitar o agente de máquina virtual na Central de segurança do Azure

O agente de máquina virtual deve ser instalado em máquinas virtuais (VMs) em ordem para [Habilitar coleta de dados](security-center-enable-data-collection.md).  O Centro de segurança do Azure permite que você veja quais VMs exigem o agente de máquina virtual e serão recomendável habilitar o agente de máquina virtual nessas VMs.

O agente de máquina virtual é instalado por padrão para VMs que são implantadas do Azure Marketplace. O artigo [agente de máquina virtual e extensões – parte 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o agente de máquina virtual.


> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo. Isso não é um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na **lâmina recomendações**, selecione **Habilitar o agente de máquina virtual**.
![Habilitar o agente de máquina virtual][1]

2. Isso abre a lâmina **Máquina virtual agente ausente ou não está respondendo**. Este blade lista VMs que exigem o agente de máquina virtual. Siga as instruções na lâmina para instalar o agente de máquina virtual.
![Agente de máquina virtual está ausente][2]

## <a name="see-also"></a>Consulte também

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md)– Saiba como configurar políticas de segurança para assinaturas do Azure e grupos de recursos.
- [Gerenciando recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md)– Saiba como recomendações ajudam a proteger seus recursos Azure.
- [Monitoramento na Central de segurança do Azure de integridade de segurança](security-center-monitoring.md)– Saiba como monitorar a integridade dos seus recursos Azure.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md)– Saiba como gerenciar e responder a alertas de segurança.
- [Soluções de parceiros de monitoramento com o Centro de segurança do Azure](security-center-partner-solutions.md) , Aprenda a monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md)– localizar perguntas frequentes sobre como usar o serviço.
- [Blog de segurança do azure](http://blogs.msdn.com/b/azuresecurity/)- Obtenha as últimas notícias de segurança do Azure e informações.

<!--Image references-->
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png
