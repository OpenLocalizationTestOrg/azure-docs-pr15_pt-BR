<properties
   pageTitle="Protegendo seus aplicativos na Central de segurança do Azure | Microsoft Azure"
   description="Este endereços de documento recomendações na Central de segurança do Azure que ajudarão a protegem seus aplicativos do Azure e permanecer em conformidade com políticas de segurança."
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
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# <a name="protecting-your-applications-in-azure-security-center"></a>Protegendo seus aplicativos na Central de segurança do Azure

O Centro de segurança do Azure analisa o estado de segurança de seus recursos Azure. Quando o Centro de segurança identifica possíveis vulnerabilidades de segurança, ele cria recomendações que orientação você durante o processo de configuração controles necessários.  Recomendações se aplicam a tipos de recursos Azure: VMs (máquinas virtuais), rede, SQL e aplicativos.

Este artigo aborda recomendações que se aplicam aos aplicativos.  Centro de recomendações do aplicativo em torno de implantação de um firewall de aplicativo web.  Use a tabela a seguir como uma referência para ajudá-lo a compreender as recomendações de aplicativo disponíveis e o que cada uma delas fará se aplicá-lo.

## <a name="available-application-recommendations"></a>Recomendações de aplicativo disponíveis

|Recomendação|Descrição|
|-----|-----|
|[Adicione um firewall de aplicativo web](security-center-add-web-application-firewall.md)|Recomenda-se de que você implante um firewall de aplicativo da web (WAF) para pontos de extremidade da web. Você pode proteger vários aplicativos da web no Centro de segurança, adicionando esses aplicativos para as implantações de WAF existentes. Dispositivos de WAF (criados com o modelo de implantação do Gerenciador de recursos) precisam ser implantado em uma rede virtual separada. Dispositivos de WAF (criados usando o modelo de implantação clássico) são restritos a usar um grupo de segurança de rede. Esse suporte será estendido a uma implantação totalmente personalizada de um dispositivo de WAF (clássico) no futuro.|
|[Finalizar a proteção do aplicativo](security-center-add-web-application-firewall.md#finalize-application-protection)|Para concluir a configuração de um WAF, o tráfego deve ser reencaminhado para o aparelho de WAF. Seguir essa recomendação concluirá as alterações de configuração necessárias.|

## <a name="see-also"></a>Consulte também

Para saber mais sobre as recomendações que se aplicam a outros tipos de recurso Azure, consulte o seguinte:

- [Protegendo suas máquinas virtuais na Central de segurança do Azure](security-center-virtual-machine-recommendations.md)
- [Protegendo sua rede na Central de segurança do Azure](security-center-network-recommendations.md)
- [Protegendo seu serviço SQL Azure na Central de segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para assinaturas do Azure e grupos de recursos.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerenciar e responder a alertas de segurança.
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md) – localizar perguntas frequentes sobre como usar o serviço.
