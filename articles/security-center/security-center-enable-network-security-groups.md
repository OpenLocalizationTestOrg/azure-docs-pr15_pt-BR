<properties
   pageTitle="Habilitar a grupos de segurança de rede na Central de segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar a recomendação de Central de segurança do Azure **Habilitar grupos de segurança de rede**."
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

# <a name="enable-network-security-groups-in-azure-security-center"></a>Habilitar a grupos de segurança de rede na Central de segurança do Azure

O Centro de segurança do Azure será recomendável habilitar um grupo de segurança de rede (NSG) se uma já não estiver habilitada. NSGs contém uma lista de regras de lista de controle de acesso (ACL) que permitir ou negar o tráfego de rede para suas instâncias de máquina virtual em uma rede Virtual. NSGs podem ser associadas a sub-redes ou instâncias individuais de máquina virtual dentro daquela sub-rede. Quando um NSG está associado uma sub-rede, as regras ACL se aplicam a todas as instâncias de máquina virtual nessa sub-rede. Além disso, o tráfego para um máquina virtual individual pode ser restrito mais associando um NSG diretamente para essa máquina virtual. Para saber mais, consulte [o que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)

Se você não tiver NSGs habilitados, o Centro de segurança apresentará dois recomendações para você: habilitar os grupos de segurança de rede em sub-redes e habilitar grupos de segurança de rede nas máquinas virtuais. Você escolhe qual nível, sub-rede ou máquina virtual, para aplicar NSGs.


> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo.  Isso não é um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na lâmina **recomendações** , selecione **Habilitar grupos de segurança de rede** em sub-redes ou em máquinas virtuais.
![Habilitar a grupos de segurança de rede][1]

2. Isso abre a lâmina **Configurar grupos de segurança de rede ausentes** para sub-redes ou para máquinas virtuais, dependendo a recomendação que você selecionou. Selecione uma sub-rede ou uma máquina virtual para configurar um NSG em.

  ![Configurar NSG para sub-rede][2]

  ![Configurar NSG para máquina virtual][3]
3. No **grupo de segurança de rede escolher** blade selecione uma NSG existente ou selecione Criar um novo NSG.

  ![Escolha o grupo de segurança de rede][4]

Se você criar um novo NSG, siga as etapas em [como gerenciar NSGs usando o portal do Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) para criar um NSG e definir regras de segurança.

## <a name="see-also"></a>Consulte também

Este artigo de mostrar como implementar a recomendação de Central de segurança "Ativar grupos de segurança de rede" para sub-redes ou máquinas virtuais. Para saber mais sobre como habilitar NSGs, consulte o seguinte:

- [O que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Como gerenciar NSGs usando o portal do Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para assinaturas do Azure e grupos de recursos.
- [Gerenciando recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger seus recursos Azure.
- [Monitoramento de integridade de segurança na Central de segurança do Azure](security-center-monitoring.md) , Aprenda a monitorar a integridade dos seus recursos Azure.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerenciar e responder a alertas de segurança.
- [Soluções de parceiros de monitoramento com o Centro de segurança do Azure](security-center-partner-solutions.md) , Aprenda a monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md) – localizar perguntas frequentes sobre como usar o serviço.
- [Blog de segurança do azure](http://blogs.msdn.com/b/azuresecurity/) - Obtenha as últimas notícias de segurança do Azure e informações.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
