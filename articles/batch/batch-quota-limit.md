<properties
    pageTitle="Cotas de serviço e limites de lote | Microsoft Azure"
    description="Saiba mais sobre restrições, limites e cotas de lote Azure padrão e como solicitar cota aumenta"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/10/2016"
    ms.author="marsma"/>

# <a name="quotas-and-limits-for-the-azure-batch-service"></a>Cotas e limites para o serviço de lote do Azure

Como com outros serviços do Azure, há limites em determinados recursos associados ao serviço de lote. Muitos desses limites são cotas padrão aplicadas pelo Azure no nível de conta ou assinatura. Este artigo discute esses padrões e como você pode solicitar cota aumenta.

Se você planeja executar cargas de trabalho de produção em lote, você talvez precise aumentar um ou mais das cotas acima do padrão. Se você quiser aumentar uma cota, você pode abrir um online [solicitação de suporte ao cliente](#increase-a-quota) sem nenhum custo.

>[AZURE.NOTE] Uma cota é um limite de crédito, não uma garantia de capacidade. Se você tem necessidades de capacidade de grande escala, contate o suporte Azure.

## <a name="subscription-quotas"></a>Cotas de assinatura
**Recurso**|**Limite padrão**|**Limite máximo**
---|---|---
Contas de lote por região por assinatura | 1 | 50

## <a name="batch-account-quotas"></a>Cotas de conta do lote
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="other-limits"></a>Outros limites
**Recurso**|**Limite máximo**
---|---
[Tarefas simultâneas](batch-parallel-node-tasks.md) por nó de computação | 4 x número de cores de nó
[Aplicativos](batch-application-packages.md) por conta em lotes        | 20
Pacotes de aplicativos por aplicativo  | 40
Tamanho de pacote de aplicativo (cada)       | Aproximadamente 195GB<sup>1</sup>

Limite de armazenamento do azure <sup>1</sup> para tamanho de blob máximo de bloco

## <a name="view-batch-quotas"></a>Exibir lote cotas

Exibir suas cotas de conta de lote no [portal do Azure][portal].

1. Selecione **contas de lote** no portal e, em seguida, selecione a conta de lote que você está interessado.

2. Selecione **Propriedades** no blade do menu da conta em lotes

3. A lâmina de propriedades exibe as **cotas** atualmente aplicado à conta do lote

    ![Cotas de conta do lote][account_quotas]

## <a name="increase-a-quota"></a>Aumentar cota

Siga as etapas abaixo para solicitar uma cota aumentam usando o [portal do Azure][portal].

1. Selecione o bloco de **Ajuda + suporte** em seu painel portal ou o ponto de interrogação (**?**) no canto superior direito do portal.

2. Selecione **nova solicitação de suporte** > **Noções básicas**.

3. Na lâmina **Noções básicas** :

    a. **Tipo de problema** > **cota**

    b. Selecione sua assinatura.

    c. **Tipo de cota** > **em lotes**

    d. **Plano de suporte** > **suporte de cota - incluído**

    Clique em **Avançar**.

4. Na lâmina **problema** :

    a. Selecione uma **gravidade** de acordo com seu [impacto comercial][support_sev].

    b. Em **detalhes**, especifique cada cota que você deseja alterar, o nome de conta do lote e o novo limite.

    Clique em **Avançar**.

5. Na lâmina **as informações de contato** :

    a. Selecione um **método de contato preferencial**.

    b. Verifique e insira os detalhes de contato necessários.

    Clique em **criar** para enviar a solicitação de suporte.

Depois que você enviou sua solicitação de suporte, Azure suporte entrará em contato com você. Observe que a concluir a solicitação pode levar até 2 dias úteis.

## <a name="related-topics"></a>Tópicos relacionados

* [Crie uma conta de lote do Azure usando o portal do Azure](batch-account-create-portal.md)

* [Visão geral dos recursos do Azure em lotes](batch-api-basics.md)

* [Assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG
