<properties
   pageTitle="Adicionar um firewall de aplicativo web na Central de segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar as recomendações do Centro de segurança do Azure **proteção de aplicativo de finalizar**e **Adicionar um firewall de aplicativo web** ."
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

# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Adicionar um firewall de aplicativo web na Central de segurança do Azure

O Centro de segurança do Azure pode recomendar que você adicionar um firewall de aplicativo da web (WAF) de um parceiro da Microsoft para proteger seus aplicativos web. Este documento orienta você por meio de um exemplo de como fazer isso.

> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo.  Isso não é um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na lâmina **recomendações** , selecione **proteger o aplicativo web usando um firewall de aplicativo web**.
![Proteger o aplicativo da web][1]

2. Na lâmina **proteger seus aplicativos da web usando o firewall do aplicativo da web** , selecione um aplicativo web. Abre a lâmina de **Adicionar um Firewall de aplicativo Web** .
![Adicione um firewall de aplicativo web][2]
3. Você pode optar por usar um firewall de aplicativo da web existente, se disponível, ou você pode criar um novo. Neste exemplo existem sem WAFs existentes disponíveis para que vamos criar um novo WAF.

4. Para criar um novo WAF, selecione uma solução da lista de parceiros integrados. Neste exemplo, podemos selecionará **Barracuda Web Application Firewall**.
5. A lâmina de **Firewall de aplicativo Web Barracuda** abre fornecendo informações sobre a solução de parceiro. Selecione **criar** na lâmina informações.
![Blade de informações de firewall][3]

6. A lâmina de **Novo Firewall do aplicativo da Web** é aberta, onde você pode executar as etapas de **Configuração de máquina virtual** e fornecer **Informações de WAF**. Selecione **a configuração de máquina virtual**.

7. Na **Configuração de máquina virtual** lâmina que você insira informações necessárias para a máquina virtual que executará o WAF de rotação.
![Configuração de máquina virtual][4]
8. Retornar para a lâmina **Novo Firewall do aplicativo da Web** e selecione **WAF informações**. Na lâmina **WAF informações** você configurar o WAF em si. Etapa 7 permite que você configure a máquina virtual no qual o WAF será executado e etapa 8 permite provisionar o WAF em si.

## <a name="finalize-application-protection"></a>Finalizar a proteção do aplicativo

1. Retornar para a lâmina **recomendações** . Uma nova entrada foi gerada após criado o WAF, chamado de **proteção do aplicativo finalizar**. Essa entrada permite saber o que você precisa concluir o processo de realmente conectando a WAF dentro da rede Virtual do Azure para que ele pode proteger o aplicativo.
![Finalizar a proteção do aplicativo][5]

2. Selecione **Finalizar aplicativo proteção**. Abre um novo blade. Você pode ver que não há um aplicativo web que precisa ter seu tráfego redirecionado.
3. Selecione o aplicativo web. Um blade abre que fornece etapas para finalizar a configuração de firewall do aplicativo web. Conclua as etapas e selecione **restringir o tráfego**. Centro de segurança, em seguida, fará a fiação-up para você.
![Restringir o tráfego][6]

> [AZURE.NOTE] Você pode proteger vários aplicativos da web no Centro de segurança, adicionando esses aplicativos para as implantações de WAF existentes. Dispositivos de WAF (criados com o modelo de implantação do Gerenciador de recursos) precisam ser implantado em uma rede virtual separada. Dispositivos de WAF (criados usando o modelo de implantação clássico) são restritos a usar um grupo de segurança de rede. Esse suporte será estendido a uma implantação totalmente personalizada de um dispositivo de WAF (clássico) no futuro. Saiba mais sobre o [classic e modelos de implantação do Gerenciador de recursos](../azure-classic-rm.md) para recursos Azure.

Os logs a partir dessa WAF agora são totalmente integrados. Central de segurança pode iniciar automaticamente reunir e analisar os logs de forma que ele pode expor os alertas de segurança importantes para você.

## <a name="see-also"></a>Consulte também

Este documento mostrado como implementar a recomendação de Central de segurança "Adicionar um aplicativo web". Para saber mais sobre como configurar um firewall de aplicativo da web, consulte o seguinte:

- [Configurando um Firewall de aplicativo da Web (WAF) para o ambiente de serviço de aplicativo](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para assinaturas do Azure e grupos de recursos.
- [Monitoramento de integridade de segurança na Central de segurança do Azure](security-center-monitoring.md) , Aprenda a monitorar a integridade dos seus recursos Azure.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerenciar e responder a alertas de segurança.
- [Gerenciando recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger seus recursos Azure.
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md) – localizar perguntas frequentes sobre como usar o serviço.
- Postagens no [blog de segurança do azure](http://blogs.msdn.com/b/azuresecurity/) – localizar blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
