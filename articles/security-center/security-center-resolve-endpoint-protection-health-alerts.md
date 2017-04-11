<properties
   pageTitle="Resolver alertas de integridade de proteção de ponto de extremidade no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar a recomendação de Central de segurança do Azure **alertas de integridade de resolver proteção de ponto de extremidade**."
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
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="resolve-endpoint-protection-health-alerts-in-azure-security-center"></a>Resolver alertas de integridade de proteção de ponto de extremidade no Centro de segurança do Azure

O Centro de segurança do Azure será recomendável que você resolver alertas de integridade de proteção de ponto de extremidade detectado.  Central de segurança permite que você veja quais máquinas virtuais (VMs) têm falhas de proteção de ponto de extremidade e quantas.

> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo. Isso não é um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na **lâmina recomendações**, selecione **alertas de integridade de resolver proteção de ponto de extremidade**.
![Resolver alertas de integridade de proteção de ponto de extremidade][1]

2. Isso abre a lâmina **Falha de proteção de ponto de extremidade** que lista VMs com falhas e o número de falhas para cada máquina virtual. Selecione uma máquina virtual na lista.
![Falha de proteção de ponto de extremidade][2]

3. Uma **Lista de falhas** lâmina abre para a máquina virtual selecionada, exibindo uma lista de falhas. Selecione uma falha na lista para saber mais. Isso abre uma lâmina com informações sobre a falha selecionada.
![Lista de falhas][3]
  ![evento de falha][4]

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
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png
