<properties
    pageTitle="Mudar sua assinatura do Azure para outra oferta | Microsoft Azure"
    description="Saiba mais sobre como alterar sua assinatura do Azure e alternar para uma oferta de diferentes usando o portal de gerenciamento de assinatura"
    services=""
    documentationCenter=""
    authors="genlin"
    manager="mbaldwin"
    editor=""
    tags="billing,top-support-issue"/>

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="genli"/>

# <a name="switch-your-azure-subscription-to-another-offer"></a>Mudar sua assinatura do Azure para outra oferta

Como um cliente de [pré-pago](https://azure.microsoft.com/offers/ms-azr-0003p/) , você poderá mudar sua assinatura do Azure para outra oferta no [Centro de conta](https://account.windowsazure.com/Subscriptions). Por exemplo, você pode usar esse recurso para aproveitar os [créditos mensais para os assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Se você estiver na [Versão de avaliação gratuita](https://azure.microsoft.com/free/), saiba como [atualizar para pré-pago](billing-upgrade-azure-subscription.md).

#### <a name="whats-supported"></a>O que é suportado:

| De                                                              | Para                                                                                      |
|-------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| [Pré-pago](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Pré-pago desenvolvimento/teste](https://azure.microsoft.com/offers/ms-azr-0023p/)              |
| [Pré-pago](https://azure.microsoft.com/offers/ms-azr-0003p/) | [O Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)          |
| [Pré-pago](https://azure.microsoft.com/offers/ms-azr-0003p/) | [O Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)     |
| [Pré-pago](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)                      |
| [Pré-pago](https://azure.microsoft.com/offers/ms-azr-0003p/) | [O Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)            |
| [Pré-pago](https://azure.microsoft.com/offers/ms-azr-0003p/) | [O Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [AZURE.NOTE] Para outros oferecem alterações, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
    
## <a name="switch-subscription-offer"></a>Oferta de inscrição de mudança

> [AZURE.VIDEO switch-to-a-different-azure-offer]

1.  Entrar no [Centro de conta do Azure](https://account.windowsazure.com/Subscriptions).

2.  Selecione sua assinatura flexível.

3.  Clique em **Alternar para outra oferta**. O botão só estará disponível se você estiver na pré-pago e concluído com seu primeiro período de cobrança.

    ![Observe o botão de oferta de mudança no lado direito da página](./media/billing-how-to-switch-azure-offer/switchbutton.png)
    
4.  **Selecione a oferta desejada** na lista de ofertas de para que sua assinatura pode ser alternada. Esta lista varia de acordo com os membros que está associada à sua conta. Se nada estiver disponível, verifique a [lista de ofertas disponíveis, que você pode mudar](#whats-supported) e verifique se que você tem as associações à direita. 

    ![Selecione uma oferta que você deseja alternar para](./media/billing-how-to-switch-azure-offer/selectoffer.png)

5.  Dependendo da oferta, que você está mudando, você pode ver uma nota sobre o impacto da migração. Percorra esta lista cuidadosamente e siga as instruções antes de continuar.

    ![Examine as anotações](./media/billing-how-to-switch-azure-offer/thingstonote.png)

6.  Você pode renomear sua assinatura. Por padrão, podemos configurá-lo para o novo nome de oferta. Clique em **Alternar oferecer** para concluir o processo.

    ![Clique no botão verde](./media/billing-how-to-switch-azure-offer/confirmpage.png)

7.  Sucesso! Sua assinatura agora é alternada para a nova oferta.

## <a name="why-cant-i-switch-offers"></a>Por que não consigo mudar ofertas?

Talvez você não veja **Alternar para outra oferta** se:

- Você não está em [pré-pago](https://azure.microsoft.com/offers/ms-azr-0003p/). Atualmente somente para inscrições pré-pago poderá ser mudadas para outra oferta.

    - Se você estiver na [Versão de avaliação gratuita](https://azure.microsoft.com/free/), saiba como [atualizar para pré-pago](billing-upgrade-azure-subscription.md).

    - Para alternar a oferta de uma assinatura diferente, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- Você ainda estiver em seu primeiro período de faturamento; Você deve esperar o primeiro período de faturamento encerrar antes de alternar ofertas.

Talvez você veja **não há nenhuma ofertas disponíveis na sua região ou país neste momento** se:

- Você não está qualificado para as opções de oferta. Verifique a [lista de ofertas disponíveis, que você pode mudar](#whats-supported).

## <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>O que significa alternando Azure oferece ao meu serviço e cobrança?

Estes são os detalhes do que acontece quando você alterna Azure planos no Centro de conta.

### <a name="access-to-services"></a>Acesso aos serviços

Não há nenhum tempo de inatividade de serviço para todos os usuários associados a assinatura. No entanto, alternar para a oferta talvez haja restrições. Por exemplo, algumas ofertas proíbem o uso de produção, para que você precisaria mover recursos de produção para outra assinatura.

### <a name="billing"></a>Cobrança

No dia que mudar, uma fatura é gerada para todos os encargos pendentes. Em seguida, sua assinatura é cobrada por termos de preços da oferta de novo. Seu aniversário de cobrança da assinatura é alterado para a data em que você alterou ofertas. Uso e cobrança dados antes da alteração de oferta não é mantida, portanto, é recomendável que você baixe uma cópia antes de trocar.

> [AZURE.NOTE] Opções de oferta devido a restrições de cobrança relacionados, não são possíveis dentro do ciclo de cobrança primeiro depois de criar uma assinatura.

## <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>Pode migrar de pré-pago para o [Provedor de soluções de nuvem](https://partner.microsoft.com/Solutions/cloud-reseller-overview) () ou [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?

Atualmente, não suportamos alternar de oferta para CSP ou EA no Centro de contas. Para mover sua assinatura atual para EA, têm a adicionar sua conta para o EA do seu administrador de inscrição. Em seguida, você recebe um email de convite. Quando você seguir as instruções para aceitar o convite, suas assinaturas são movidas automaticamente sob o contrato Enterprise. Para migrar para o CSP, consulte [Migração de assinatura do Azure para CSP](https://blogs.technet.microsoft.com/hybridcloudbp/2016/08/26/azure-subscription-migration-to-csp/).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar funções de administrador](billing-add-change-azure-subscription-administrator.md) para sua assinatura

- Controlar seu uso baixando [dados de uso e fatura](billing-download-azure-invoice-daily-usage-date.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte.

Se você ainda tiver mais perguntas, por favor, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o problema resolvido rapidamente.
