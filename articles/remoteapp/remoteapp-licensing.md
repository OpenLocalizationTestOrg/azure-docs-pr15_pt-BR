<properties
    pageTitle="Licenciamento de RemoteApp Azure | Microsoft Azure"
    description="Saiba como funciona o licenciamento no Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="how-does-licensing-work-in-azure-remoteapp"></a>Como o licenciamento funciona no Azure RemoteApp?

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Para que você configurou seu serviço Azure RemoteApp, criado seus modelos e estiver pronto para publicar aplicativos para seus usuários. Mas ainda há uma última coisa descobrir: licenciamento. Apenas como funciona o licenciamento para RemoteApp e para os aplicativos que você compartilha em RemoteApp?

RemoteApp não exige qualquer licenças do Windows ou CALs de área de trabalho remota. Sua assinatura cuida do lado RemoteApp em si. (Confira os detalhes dos [planos de preço](https://azure.microsoft.com/pricing/details/remoteapp)).

Se você usa uma das imagens que está incluído em sua assinatura, você pode compartilhar qualquer um dos aplicativos instalados no imagem sem precisar de uma licença separada. Por exemplo, se você usar a imagem de modelo do Windows Server 2012 R2 para criar seu conjunto, você pode compartilhar proteção de ponto de extremidade do sistema central com seus usuários. As únicas exceções a essa regra são o Office 365 ProPlus, que exige uma assinatura separada, e Office 2013, que não podem ser compartilhados em um conjunto de produção.

Se você quiser usar a imagem de modelo do Office 365 que vem com o RemoteApp, você deve ter um plano *existente* do Office 365 ProPlus. O mesmo é verdadeiro para qualquer aplicativo do Office 365 que você publicar usando um modelo personalizado. Você precisa ativar os aplicativos com sua própria assinatura. Isso é verdadeiro para assinaturas de avaliação e pagas. Se você quiser usar a imagem de modelo do Office 365 durante a avaliação, *e você ainda não tiver uma assinatura*, vá para a página do Office 365 para [inscrever-se](https://go.microsoft.com/fwlink/p/?LinkID=403802) para uma assinatura de avaliação. Consulte [como RemoteApp e Office funcionam juntos?](remoteapp-o365.md) para obter mais informações.

Se, durante o período de avaliação, você não deseja obter uma assinatura de avaliação do Office 365, use o Office 2013 Professional Plus modelo de imagem que vem com o RemoteApp. Esta imagem de modelo só pode ser usada por 30 dias e não pode ser convertida em um conjunto de pago.

Para outros aplicativos, você precisa certificar-se de que você tem a licença para compartilhar o aplicativo.

Que faz sentido, certo? Você pode publicar qualquer aplicativo que você está legalmente qualificado para compartilhar. E você precisa para certificar-se de que você realmente está qualificada para compartilhar seus programas.

Observe que você não pode usar um contrato de licença de Volume ou CAL em um conjunto de nuvem. Você *pode* usar um contrato de licença de Volume para ativar aplicativos em seu conjunto de híbrido (exceto para o Office). Você precisa instalá-los na sua imagem de modelo a partir da mídia de licença de Volume. Siga as informações do fornecedor do aplicativo para instalar licenças em um ambiente de área de trabalho remota.
