<properties
   pageTitle="Forneça detalhes de contato de segurança na Central de segurança do Azure | Microsoft Azure"
   description="Este documento mostra como fornecer detalhes de contato de segurança na Central de segurança do Azure."
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

# <a name="provide-security-contact-details-in-azure-security-center"></a>Forneça detalhes de contato de segurança na Central de segurança do Azure

O Centro de segurança do Azure será recomendável que você forneça detalhes de contato de segurança para sua assinatura do Azure se você ainda não fez isso. Essas informações serão usadas pela Microsoft para contatá-lo a se o Centro de resposta de segurança da Microsoft (MSRC) descobre que seus dados de cliente foi acessados por uma pessoa não autorizada ou ilegal. MSRC executa selecione segurança monitoramento da rede Azure e infraestrutura e recebe reclamações de inteligência e abuse ameaças de terceiros.

Uma notificação por email é enviada na primeira ocorrência diária de um alerta e apenas para alertas de alta gravidade. Preferências de email só podem ser configuradas para políticas de assinatura. Grupos de recursos dentro de uma assinatura herdarão essas configurações.

> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo.  Isso não é um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na lâmina **recomendações** , selecione **segurança de fornecer detalhes de contato**.
![Fornecer contato de segurança][1]

2. Isso abre a lâmina **fornecer detalhes de contato de segurança**. Selecione a assinatura Azure para fornecer informações de contato em.
![Forneça detalhes de contato de segurança][2]

3. Abre uma segunda lâmina **fornecer detalhes de contato de segurança** .

  - Insira o endereço de email do contato de segurança ou endereços separados por vírgulas. Não há um limite quanto ao número de endereços de email que você pode inserir.
  - Insira um número de telefone internacional contato de segurança.
  - Para receber emails sobre alertas de alta gravidade, ative a opção **Enviar-me emails sobre alertas**.
  - No futuro, você terá a opção de enviar notificações por email aos proprietários de assinatura. Essa opção fica acinzentada no momento.
  - Selecione **Okey** para aplicar as informações de contato de segurança à sua assinatura.

## <a name="see-also"></a>Consulte também

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para assinaturas do Azure e grupos de recursos.
- [Gerenciando recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger seus recursos Azure.
- [Monitoramento de integridade de segurança na Central de segurança do Azure](security-center-monitoring.md) , Aprenda a monitorar a integridade dos seus recursos Azure.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerenciar e responder a alertas de segurança.
- [Soluções de parceiros de monitoramento com o Centro de segurança do Azure](security-center-partner-solutions.md) , Aprenda a monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md) – localizar perguntas frequentes sobre como usar o serviço.
- [Blog de segurança do azure](http://blogs.msdn.com/b/azuresecurity/) - Obtenha as últimas notícias de segurança do Azure e informações.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
