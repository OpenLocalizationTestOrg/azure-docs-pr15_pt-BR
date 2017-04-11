<properties
   pageTitle="Pré-requisitos técnicos para a criação de uma oferta do Azure Marketplace | Microsoft Azure"
   description="Compreenda os requisitos para criar e implantar uma oferta para o Azure Marketplace para que outras pessoas para compra."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="08/18/2016"
  ms.author="hascipio"/>

# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Pré-requisitos gerais para a criação de uma oferta do Azure Marketplace
Compreenda os pré-requisitos gerais, centrados em processos de negócios que são necessárias para avançar com o processo de criação de oferta.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Certifique-se de que você está registrado como um vendedor com a Microsoft
Para obter instruções detalhadas sobre como registrar uma conta de vendedor com a Microsoft, vá para o [registro e criação de conta](marketplace-publishing-accounts-creation-registration.md).

- **Se sua empresa já está registrada como um vendedor no Centro de desenvolvimento e você quiser criar uma nova oferta,** em seguida, logon para publicação portal com a mesma id de email com o qual Centro de desenvolvimento do registro é feito. Essa etapa é necessária para que o portal de publicação e Centro de desenvolvimento do estão vinculados uns com os outros.
- **Se sua empresa já está registrada como um vendedor no Centro de desenvolvimento e você quiser editar uma oferta existente,** em seguida, login para publicação portal com a conta de administrador ou com uma conta que é adicionada como um administrador de co na publicação portal. Etapas para adicionar uma conta de administrador do co são fornecidos abaixo.

## <a name="steps-to-add-a-co-admin-in-the-publishing-portal"></a>Etapas para adicionar um co-administrador no Portal de publicação
Administradores da publicação portal pode adicionar os outros membros da empresa, que estão trabalhando no aplicativo, como um administrador de co na publicação portal. **Presumindo que você for o administrador,** fornecidas abaixo são as etapas para adicionar um co-administrador.

>[AZURE.NOTE] Para novos usuários, antes de adicionar um administrador de co na publicação portal, certifique-se de que você tenha criado pelo menos um aplicativo na publicação portal. Isso é necessário como a guia **fornecedores** aparecem somente depois de criar pelo menos um aplicativo na publicação portal.

1. Certifique-se de que a id de email co-admin é uma account(MSA) da Microsoft. Caso contrário, registrado como uma MSA usando esse [link](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Verifique se há pelo menos um aplicativo sob a conta de administrador antes de tentar adicionar um co-administrador.
3. Depois de concluir as etapas acima, login publicação portal com a id de email do co-administração e log-out.
4. Agora login publicação portal com a id de email do administrador.
5. Navegue até fornecedores -> selecione sua conta -> administradores -> Adicionar co-administrador (captura de tela fornecida abaixo)

    ![desenho](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)

6. Certifique-se de que as ids de email fornecidos em vários estágios do processo de publicação (por exemplo, centro de desenvolvimento, portal de publicação) são monitorados para qualquer comunicação da Microsoft.
7. Para o registro do Centro de desenvolvimento, evite usar uma conta associada a uma única pessoa. Isso é sugerido para Removendo dependência de um indivíduo.
8. Se você enfrentar problemas com o registro do Centro de desenvolvimento, eleve um tíquete usando esse [link](https://developer.microsoft.com/en-us/windows/support).

## <a name="steps-to-delete-a-co-admin-in-the-publishing-portal"></a>Etapas para excluir um administrador de co na publicação portal
**Presumindo que você for o administrador,** fornecidas abaixo são as etapas para excluir um co-administrador.

1. Fazer login no publicação portal com a id de email do administrador.
2. Navegue até **fornecedores** -> selecione sua conta -> **administradores** -> **Co-administradores**.
3. Clique no botão **X** lado co-administrador que desejar excluir tot (captura de tela abaixo).

    ![desenho](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [AZURE.IMPORTANT] Você não precisa concluir as informações de imposto e bancárias de empresa se você estiver planejando publicar ofertas só gratuitas (ou trazer seu próprio licença).

> O registro de empresa deve ser concluído para começar. No entanto, enquanto sua empresa trabalha as informações de imposto e bancária na conta do Microsoft Developer, os desenvolvedores podem começar a trabalhar em criar a imagem de máquina virtual no [Portal de publicação](https://publish.windowsazure.com), obtendo-certified e testá-lo no ambiente de teste do Azure. Você precisará a aprovação de conta do vendedor concluída somente para a etapa final do publicando sua oferta do Azure Marketplace.

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Adquirir uma assinatura "flexível" Azure
Esta é a assinatura que você usará para criar suas imagens de máquina virtual e passar as imagens para o [Azure Marketplace](https://azure.microsoft.com/marketplace/). Se você não tiver uma assinatura existente, em seguida, inscreva-se em https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## <a name="sell-from-countries"></a>"Vender-de" países/regiões
> [AZURE.WARNING]
Para vender seus serviços no Azure Marketplace, você deve garantir que sua entidade registrada é de um dos países aprovado "vender-de". Essa restrição é por motivos de pagamento e tributação. Estamos ativamente procurando para expandir esta lista de países no futuro próximo, portanto, fique atento. Para uma lista completa, consulte seção 1b das [políticas de participação do Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).

## <a name="next-steps"></a>Próximas etapas
Depois que os pré-requisitos abrangem são preenchidos, Avançar é a oferta pré-requisitos técnicos específicos. Clique no link para o artigo para o tipo de oferta respectivos que você gostaria de criar do Azure Marketplace.

- [Pré-requisitos de máquina virtual técnicos](marketplace-publishing-vm-image-creation-prerequisites.md)
- [Solução modelo técnicos pré-requisitos](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>Consulte também
- [Introdução: como publicar uma oferta do Azure Marketplace](marketplace-publishing-getting-started.md)
