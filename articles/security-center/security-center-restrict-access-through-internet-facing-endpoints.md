<properties
   pageTitle="Restringir o acesso por meio de pontos de extremidade de voltado para a Internet no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar a recomendação de Central de segurança do Azure **restringir o acesso por meio de ponto de extremidade oposto de Internet**."
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

# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Restringir o acesso por meio de pontos de extremidade de voltado para a Internet no Centro de segurança do Azure

O Centro de segurança do Azure será recomendável que você restrinja o acesso por meio de pontos de extremidade de voltado para a Internet se qualquer um dos seus grupos de segurança de rede (NSGs) tem uma ou mais regras de entrada que permitem o acesso de "qualquer" endereço IP de origem. Abrir o acesso a "qualquer" pode permitir ataques acessar seus recursos. Centro de segurança será recomendável que você editar estas regras de entrada para restringir o acesso a endereços IP de origem que realmente precisam de acesso.

Essa recomendação é gerada para qualquer porta não web que tenha "qualquer" como fonte.

> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo. Isso não é um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na **lâmina recomendações**, selecione **restringir o acesso por meio de ponto de extremidade oposto de Internet**.
![Restringir o acesso por meio de ponto de extremidade de com a Internet][1]

2. Isso abre a lâmina **restringir o acesso por meio de ponto de extremidade oposto de Internet**. Este blade listas máquinas virtuais (VMs) com as regras de entrada que cria um possível problema de segurança. Selecione uma máquina virtual.
![Selecione uma máquina virtual][2]

3. A lâmina **NSG** exibe informações de grupo de segurança de rede, as regras de entrada relacionadas e a máquina virtual associada. Selecione **Editar regras de entrada** para continuar com a edição de uma regra de entrada.
![Blade de grupo de segurança de rede][3]

4. Na lâmina **regras de segurança de entrada** , selecione a regra de entrada para editar. Neste exemplo, vamos selecionar **AllowWeb**.
![Regras de segurança de entrada][4]

  Observe, você também pode selecionar **regras padrão** para ver o conjunto de regras padrão contido pelo NSGs todos. As regras padrão não podem ser excluídas, mas, pois elas são atribuídas a uma prioridade mais baixa, eles podem ser substituídos por regras que você criar. Saiba mais sobre [regras padrão](../virtual-network/virtual-networks-nsg.md#default-rules).
![Regras padrão][5]

5. Na lâmina **AllowWeb** , edite as propriedades da regra de entrada para que a **origem** é um endereço IP ou um bloco de endereços IP. Para saber mais sobre as propriedades da regra de entrada, consulte [regras NSG](../virtual-network/virtual-networks-nsg.md#nsg-rules).

  ![Editar regra de entrada][6]

## <a name="see-also"></a>Consulte também

Este artigo de mostrar como implementar a recomendação de Central de segurança "Restringir o acesso por meio de ponto de extremidade oposto Internet". Para saber mais sobre como habilitar NSGs e regras, consulte o seguinte:

- [O que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Como gerenciar NSGs usando o portal do Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md)– Saiba como configurar políticas de segurança para assinaturas do Azure e grupos de recursos.
- [Gerenciando recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md)– Saiba como recomendações ajudam a proteger seus recursos Azure.
- [Monitoramento na Central de segurança do Azure de integridade de segurança](security-center-monitoring.md)– Saiba como monitorar a integridade dos seus recursos Azure.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md)– Saiba como gerenciar e responder a alertas de segurança.
- [Soluções de parceiros de monitoramento com o Centro de segurança do Azure](security-center-partner-solutions.md) , Aprenda a monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md)– localizar perguntas frequentes sobre como usar o serviço.
- [Blog de segurança do azure](http://blogs.msdn.com/b/azuresecurity/)- Obtenha as últimas notícias de segurança do Azure e informações.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
