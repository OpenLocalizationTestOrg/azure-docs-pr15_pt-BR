<properties
   pageTitle="Testar sua oferta de máquina virtual do Marketplace | Microsoft Azure"
   description="Compreenda como testar sua imagem de máquina virtual do Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="hascipio" />

# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>Testar sua oferta de máquina virtual do Azure Marketplace no teste

Preparação significa Implantando o SKU em uma "área restrita" onde você pode testar e validar sua funcionalidade antes de implantá-lo para o Marketplace privada. O SKU aparece no teste como faria para um cliente que implantou-lo. Sua imagem de máquina virtual deve ser certificada para ser enviado para preparação.

## <a name="step-1-push-your-offer-to-staging"></a>Etapa 1: Push sua oferta de teste

1. Na guia **Publicar** , clique em **Enviar para teste**.

    ![desenho](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. Se o Portal de publicação o notificará sobre quaisquer erros, corrigi-los.
3.  No **quem pode acessar sua oferta em estágios?** caixa de diálogo, digite a lista de assinaturas do Azure que você usará para visualizar sua oferta no [portal do Azure preview](https://portal.azure.com).

    >[AZURE.NOTE] No caso de solução e máquinas virtuais modelos, por favor **não** branca assinaturas do tipo CSP, DreamSpark ou Azure em Abrir.


    > No caso de máquinas virtuais, quando você clica no botão de **Envio por PUSH para PREPARAÇÃO**, as seguintes etapas são executadas por trás da cena. Você poderá ver o andamento de cada etapa na guia Publicar na publicação portal. Você precisa verificar esta página em intervalo regular (até que o status mostra em ESTÁGIOS) qualquer informação de falha que precisam ser corrigidos do seu final.

    > - A primeira solicitação de preparação vai para a equipe de certificação que validar o vhd. No entanto, se sua solicitação tem somente a alteração de marketing, em seguida, na etapa de certificação será ignorada.
    > - Quando a certificação for concluída, replicação da oferta começar em todo os Azure dos data centers. Geralmente ele leva 24-48hours para a conclusão da replicação, mas pode levar uma semana dependendo do tamanho do vhd. No entanto, se sua solicitação tem somente a alteração de marketing, a replicação é mais rápida.
    > - Quando a replicação estiver concluída, a oferta estará disponível no [portal do Azure](http:/portal.azure.com). AT que o status de horário ficam transferidos na publicação portal. Uma oferta em estágios fica visível no [portal do Azure](http:/portal.azure.com) usando somente as identificações de email associadas com a assinatura com o qual a oferta é transferida.

4. Entrar para o [Azure visualizar portal](https://portal.azure.com) usando uma das assinaturas Azure listadas na etapa anterior.
5. Localize sua oferta e validar seus pontos de imagem de máquina virtual:
  - Certifique-se de que conteúdo de marketing aparece corretamente no mercado.
  - Implantação de ponta a ponta da imagem máquina virtual.

      ![img de mapa de portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [AZURE.IMPORTANT] Sua oferta permanecerão no teste até você notificar a Microsoft através do Portal de publicação [guia**Publicar** > clique no botão **"solicitar aprovação para envio à produção"**] que você está pronto para enviar para produção. Este é o momento ideal para ter todos os membros da sua seleção de equipe sobre tudo em preparação para a sua oferta indo listados.

> A plataforma preparação destina-se para testar a oferta em um modo de visualização pelo editor. Podemos altamente desencorajar usando este platofrm para fins de comercial.

## <a name="next-steps"></a>Próximas etapas
Agora que sua oferta é "preparada" e testar sua funcionalidade e conteúdo de marketing, você pode prosseguir para a fase de publicação final, **etapa 4**: [Implantando sua oferta para o Marketplace](marketplace-publishing-push-to-production.md).

## <a name="see-also"></a>Consulte também
- [Introdução: como publicar uma oferta do Azure Marketplace](marketplace-publishing-getting-started.md)
