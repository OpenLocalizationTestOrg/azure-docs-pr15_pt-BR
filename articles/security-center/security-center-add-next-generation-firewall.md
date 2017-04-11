<properties
   pageTitle="Adicione um firewall de geração próxima na Central de segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar as recomendações do Centro de segurança do Azure **Adicionar um Firewall de geração próximo** e **rotear o tráfego por meio de NGFW somente**."
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
   ms.date="10/26/2016"
   ms.author="terrylan"/>

# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Adicione um Firewall de geração próxima na Central de segurança do Azure

O Centro de segurança do Azure pode recomendar que você adicionar um firewall de geração próximo (NGFW) de um parceiro da Microsoft para aumentar sua proteção de segurança. Este documento orienta você por meio de um exemplo de como fazer isso.

> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo.  Isso não é um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na lâmina **recomendações** , selecione **Adicionar um Firewall de geração próximo**.
![Adicione um Firewall de geração próximo][1]

2. Na lâmina **Adicionar um Firewall de geração próxima** , selecione um ponto de extremidade.
![Selecione um ponto extremo][2]

3. Abre uma segunda lâmina de **Adicionar um Firewall de geração próximo** . Você pode optar por usar uma solução existente, se disponível, ou você pode criar um novo. Neste exemplo existem sem soluções existentes disponíveis para que vamos criar um novo NGFW.
![Criar novo Firewall geração próximo][3]

4. Para criar um novo NGFW, selecione uma solução da lista de parceiros integrados. Neste exemplo, podemos selecionará **Ponto de verificação**.
![Selecionar solução de próxima geração Firewall][4]

5. A lâmina de **Ponto de verificação** abre fornecendo informações sobre a solução de parceiro. Selecione **criar** na lâmina informações.
![Blade de informações de firewall][5]

6. A lâmina de **máquina virtual de criar** é aberta. Aqui você pode digitar as informações necessárias para uma máquina virtual (VM) que executará o NGFW de rotação. Siga as etapas e forneça as informações de NGFW necessárias. Selecione Okey para aplicar.
![Criar máquina virtual para executar NGFW][6]

## <a name="route-traffic-through-ngfw-only"></a>Rotear o tráfego através de NGFW apenas

Retornar para a lâmina **recomendações** . Uma nova entrada foi gerada depois que você adicionou um NGFW por meio do Centro de segurança, chamado **rotear o tráfego através de NGFW somente**. Essa recomendação é criada somente se você instalou seu NGFW por meio do Centro de segurança. Se você tiver pontos de extremidade de voltado para a Internet, o Centro de segurança será recomendável que você configure as regras de grupo de segurança de rede que forçarem o tráfego de entrada para sua máquina virtual por meio de seu NGFW.

1. Na **lâmina recomendações**, selecione **rotear o tráfego através de NGFW somente**.
![Rotear o tráfego através de NGFW apenas][7]

2. Isso abre a lâmina **rotear o tráfego através do NGFW apenas** que lista VMs que você pode direcionar o tráfego para. Selecione uma máquina virtual na lista.
![Selecione uma máquina virtual][8]

3. Um blade para a máquina virtual selecionado é aberta, exibindo as regras de entrada relacionadas. Uma descrição oferece mais informações sobre possíveis etapas. Selecione **Editar regras de entrada** para continuar com a edição de uma regra de entrada. As expectativas é que **fonte** não estiver definida para **qualquer** para os pontos de extremidade voltado para a Internet vinculados com o NGFW. Para saber mais sobre as propriedades da regra de entrada, consulte [regras NSG](../virtual-network/virtual-networks-nsg.md#nsg-rules).
![Configurar regras para limitar o acesso][9]
![Editar regra de entrada][10]

## <a name="see-also"></a>Consulte também

Este documento mostrado como implementar a recomendação de Central de segurança "Adicionar um Firewall de geração próximo". Para saber mais sobre a solução de parceiro do ponto de verificação e NGFWs, consulte o seguinte:

- [Firewall de última geração](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
- [VSEC de ponto de verificação](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança.
- [Gerenciando recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger seus recursos Azure.
- [Monitoramento de integridade de segurança na Central de segurança do Azure](security-center-monitoring.md) , Aprenda a monitorar a integridade dos seus recursos Azure.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerenciar e responder a alertas de segurança.
- [Soluções de parceiros de monitoramento com o Centro de segurança do Azure](security-center-partner-solutions.md) , Aprenda a monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md) – localizar perguntas frequentes sobre como usar o serviço.
- Postagens no [blog de segurança do azure](http://blogs.msdn.com/b/azuresecurity/) – localizar blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png
