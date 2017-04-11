<properties
   pageTitle="Protegendo sua rede na Central de segurança do Azure | Microsoft Azure"
   description="Este endereços de documento recomendações na Central de segurança do Azure que ajudarão a proteger sua rede do Azure e permanecer em conformidade com políticas de segurança."
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

# <a name="protecting-your-network-in-azure-security-center"></a>Protegendo sua rede na Central de segurança do Azure

O Centro de segurança do Azure analisa o estado de segurança de seus recursos Azure. Quando o Centro de segurança identifica possíveis vulnerabilidades de segurança, ele cria recomendações que orientação você durante o processo de configuração controles necessários.  Recomendações se aplicam a tipos de recursos Azure: VMs (máquinas virtuais), rede, SQL e aplicativos.

Este artigo aborda recomendações que se aplicam à sua rede.  Centro de recomendações de rede ao redor de próxima firewalls de geração, grupos de segurança de rede, configurando regras de tráfego de entrada e muito mais.  Use a tabela a seguir como uma referência para ajudá-lo a compreender as recomendações de rede disponíveis e o que cada uma delas fará se aplicá-lo.

## <a name="available-network-recommendations"></a>Recomendações de rede disponíveis

|Recomendação|Descrição|
|-----|-----|
|[Adicione um Firewall de geração próximo](security-center-add-next-generation-firewall.md)|Recomenda-se de que você adicione um Firewall de geração próximo (NGFW) de um parceiro da Microsoft para aumentar sua proteção de segurança.|
|[Rotear o tráfego através de NGFW apenas](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|Recomenda-se de que você configure regras de grupo (NSG) de segurança de rede que forçarem o tráfego de entrada para sua máquina virtual por meio de seu NGFW.|
|[Habilitar grupos de segurança de rede em sub-redes ou máquinas virtuais](security-center-enable-network-security-groups.md)|Recomenda-se de que você habilite NSGs em sub-redes ou VMs.|
|[Restringir o acesso por meio de ponto de extremidade de com a Internet](security-center-restrict-access-through-internet-facing-endpoints.md)|Recomenda-se de que você configure regras de tráfego de entrada para NSGs.|

## <a name="see-also"></a>Consulte também

Para saber mais sobre as recomendações que se aplicam a outros tipos de recurso Azure, consulte o seguinte:

- [Protegendo suas máquinas virtuais na Central de segurança do Azure](security-center-virtual-machine-recommendations.md)
- [Protegendo seus aplicativos na Central de segurança do Azure](security-center-application-recommendations.md)
- [Protegendo seu serviço SQL Azure na Central de segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para assinaturas do Azure e grupos de recursos.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerenciar e responder a alertas de segurança.
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md) – localizar perguntas frequentes sobre como usar o serviço.
