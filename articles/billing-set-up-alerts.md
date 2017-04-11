<properties
    pageTitle="Configurar alertas para suas assinaturas do Microsoft Azure de cobrança | Microsoft Azure"
    description="Descreve como você pode configurar alertas na fatura Azure para que você pode evitar surpresas cobranças."
    services=""
    documentationCenter=""
    authors="vikdesai"
    manager="mbaldwin"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/18/2016"
    ms.author="vikdesai"/>

# <a name="set-up-billing-alerts-for-your-microsoft-azure-subscriptions"></a>Configurar alertas de cobranças para suas assinaturas do Microsoft Azure

Você está preocupado quanto você está gastando cada mês para sua assinatura do Azure? Se você for o administrador de conta para uma assinatura do Azure, você pode usar o serviço de alerta de cobrança do Azure para criar personalizado alertas de cobranças que ajudam você monitorar e gerenciar a atividade de cobrança para suas contas do Azure.

Esse serviço é um serviço de visualização, portanto, a primeira coisa que você precisa fazer é entrada para cima para ele. Visite [a página de visualização de recursos](https://account.windowsazure.com/PreviewFeatures) no portal de gerenciamento de conta do Azure para ativar esse recurso.

## <a name="set-the-alert-threshold-and-email-recipients"></a>Definir os destinatários de email e de limite de alerta

Depois de receber email de confirmação de que o serviço de faturamento é ativado para sua assinatura, visite [a página assinaturas](https://account.windowsazure.com/Subscriptions) no portal de conta. Clique na assinatura que você deseja monitorar e, em seguida, clique em **alertas**.

![][Image1]

Em seguida, clique em **Adicionar alerta** para criar seu primeiro - você pode configurar um total de cinco alertas cobranças por assinatura, com um limite diferente e até dois destinatários de email para cada alerta.

![][Image2]

Quando você adiciona um alerta, você dê um nome exclusivo, escolha um limite de gasto e escolha os endereços de email onde alertas serão enviados. Ao configurar o limite, você pode escolher um **Total de cobrança** ou um **Crédito monetários** na lista de **Alerta para** . Para um total de cobrança, um alerta é enviado quando gastos de assinatura excede o limite. Para um crédito monetário, um alerta é enviado quando créditos monetários solte abaixo do limite. Créditos monetários geralmente se aplicam a avaliações gratuitas e assinaturas associadas às contas do MSDN.

![][Image3]

Azure suporta qualquer endereço de email, mas não verifica que o funcionamento de endereço de email, portanto verificar ortografia.

## <a name="check-on-your-alerts"></a>Verificar os alertas

Depois de configurar alertas, o Centro de conta lista-los e mostra quantas mais você pode configurar. Para cada alerta, você pode ver a data e hora que foi enviada, seja ele um alerta para cobrança Total ou crédito monetárias e o limite de que configurar. O formato de data e hora é coordenar de tempo Universal de 24 horas (UTC) e a data é o formato aaaa-mm-dd. Clique no sinal de mais para receber um alerta na lista para editá-lo, ou clique na Lixeira para excluí-lo.

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png
