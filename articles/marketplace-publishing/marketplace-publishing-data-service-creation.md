<properties
   pageTitle="Guia para a criação de um serviço de dados para o Marketplace | Microsoft Azure"
   description="Instruções detalhadas de como criar, certificar e implantar um serviço de dados para compra no Azure Marketplace."
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
      ms.date="08/26/2016"
      ms.author="hascipio; avikova" />

# <a name="data-service-publishing-guide-for-the-azure-marketplace"></a>Guia do Azure Marketplace de publicação de serviço de dados

>[AZURE.IMPORTANT] **Neste momento, não são mais integração qualquer novas fornecedores de serviço de dados. Novo dataservices não irá obter aprovado para listagem.** Se você tiver um aplicativo de negócios SaaS que você gostaria de publicar no AppSource você pode encontrar mais informações [aqui](https://appsource.microsoft.com/partners). Se você tiver um aplicativos IaaS ou serviço de desenvolvedor que você gostaria de publicar no Azure Marketplace, você pode encontrar mais informações [aqui](https://azure.microsoft.com/marketplace/programs/certified/).

Após concluir a etapa 1, a [criação de conta e registro](marketplace-publishing-accounts-creation-registration.md), podemos orientado você pela [Geral abrangem](marketplace-publishing-pre-requisites.md) e [Requisitos técnicos](marketplace-publishing-data-service-creation-prerequisites.md) de uma oferta de serviço de dados no Azure Marketplace. Agora podemos apresentará as etapas para criar uma oferta de serviço de dados no [Portal de publicação] [ link-pubportal] do Azure Marketplace.

## <a name="1---login-to-the-publishing-portal"></a>1. login publicação Portal.

Vá para [https://publish.windowsazure.com](https://publish.windowsazure.com. )

**Para fazer login tempo primeiro no Portal de publicação, use a mesma conta com os quais vendedor perfil sua empresa foi registrado no Developer Center.**  (Posteriormente você pode adicionar qualquer funcionário da sua empresa como co-administrador no Portal de publicação).

Clique no bloco **publicar um serviços de dados** se esse for o primeiro logon para o portal de publicação.

## <a name="2---choose-data-services-in-the-navigation-menu-on-the-left-side"></a>2. Escolha **Os serviços de dados** no menu de navegação no lado esquerdo.

  ![desenho](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## <a name="3---create-a-new-data-service"></a>3. criar um novo serviço de dados

Preencha o título para sua nova oferta de serviço de dados e clique em "+" à direita.

  ![desenho](media/marketplace-publishing-data-service-creation/step-3.png)

## <a name="4---review-the-sub-menu-under-the-newly-created-data-service-in-the-navigation-menu"></a>4. examine o submenu sob o serviço de dados recém-criado no menu de navegação.

Clique na guia **passo a passo** e examine todas as etapas necessárias necessárias para publicar corretamente o serviço de dados no Azure Marketplace.

> [AZURE.TIP] Você sempre pode clique nos links na página "Passo a passo" ou use as guias no submenu da oferta de serviço de dados no lado esquerdo.

## <a name="5---create-a-new-plan"></a>5. Crie um novo plano.

### <a name="offers-plans-transactions"></a>Oferece, planos, transações.

Cada oferta pode ter vários planos, mas deve ter pelo menos um (1) plano. Quando os usuários finais assinar sua oferta eles inscrever-se para um plano da oferta. Cada plano define como os usuários finais serão capazes de usar o serviço.

Azure Marketplace suportamos somente mensal assinatura transação modelo com base em serviços de dados, ou seja, os usuários finais pagarão taxa mensal acordo com o preço do plano específico que eles assinado em poderão consumir cada número do mês da transação definido pelo plano.

Cada transação normalmente definida como número de registros que seu serviço de dados retornarão com base na consulta enviada ao serviço. O padrão é 100. Número de transações retornado para cada consulta será número de registros divididos por 100 e arredondado para cima até o inteiro mais próximo.

É responsabilidade de camada de serviço do Azure Marketplace para monitorar o número de (medidor) de transações consumida por cada consulta.

> [AZURE.IMPORTANT] Os usuários finais que atingiu o limite de transação durante o mês serão bloqueados de continuar a usar o serviço até o final do seu ciclo de assinatura mensal.

> O plano ou um dos planos pode (mas não deve) incluem número ilimitado de transações.

### <a name="create-a-plan"></a>Crie um plano.
1. Clique em **"+"** ao lado de "Adicionar um novo plano".

2. Escolha uma das opções: uso **ilimitado** ou **limitado** para este plano.  Se limitado fornecer o número de transação o plano permitirá consumir em um mês.

    ![desenho](media/marketplace-publishing-data-service-creation/step-5.1.png)  

    Portal de publicação também irá sugerir "Identificador plano", que será usado para se comunicar com os usuários finais, o nome do plano na interface de usuário e também usado pelo serviço de mercado para identificar o plano. Se desejar, você pode alterar o "identificador de planejar".

    > [AZURE.NOTE] O "identificador de planejar" deve ser exclusivo dentro do escopo de cada oferta. Como muitos outros identificadores usado no identificador de plano de Portal de publicação será bloqueado após a primeira publicação para produção e você não poderá alterar esse identificador.

3. Clique para aceitar sua escolha.

4. Em seguida, você será solicitado algumas perguntas adicionais sobre seu plano recém-criado.

    ![desenho](media/marketplace-publishing-data-service-creation/step-5.2.png)


|Pergunta|Significância|
|----|----|
|**Esse plano é gratuito e disponível em todo o mundo?**|Você pode criar um plano de completamente livre de custos. Se for o plano somente para esta oferta – significa que você está publicando "Oferecer livre" no mercado. Se for apenas para um (de poucos) plano, a it oferece uma opção para oferecer os usuários finais para saber mais sobre o seu serviço com um número relativamente pequeno de transações por mês.  Se a resposta for "Sim", então não há mais perguntas serão solicitadas.|

> [AZURE.NOTE] Usuários finais sempre pode atualizar para os planos pagos.

|Pergunta|Significância|
|----|----|
|**Avaliação gratuita está disponível?**|Você pode escolher entre "Sem avaliação" todo ou dar uma opção para usar seu plano para "Um mês". Fornecedores como usar essa opção para fornecer aos usuários finais a possibilidade para compreender os benefícios da oferta gratuitamente um mês.|

> [AZURE.IMPORTANT] Os usuários finais só poderá comprar uma avaliação gratuita se eles tem estabelecido instrumentos de pagamento por exemplo, cartão de crédito, contrato enterprise.

> Depois de um mês após a avaliação gratuita, Azure Marketplace iniciará charging clientes o preço a partir da data da assinatura, a menos que o cliente iniciou o cancelamento de assinatura. Nenhuma notificação especial será fornecida para os usuários finais.

|Pergunta|Significância|
|----|----|
|**Este plano requer um código de promoção para adquirir?**| Os publicadores têm uma opção para limitar o acesso a seus planos de serviço, fornecendo um código especial, chamado de "R Promocode" clientes específicos. Somente os usuários finais que terão esta Promocode será capazes de assinar o plano. Se você escolher "Não", você concorda que todos da região onde a oferta está disponível (consulte [Guia de conteúdo de Marketing do Marketplace](marketplace-publishing-push-to-staging.md) para obter mais detalhes) será capaz de assinar este plano. Não há mais perguntas serão solicitadas.|
|**Também ocultar esse plano de qualquer pessoa que não tem um código de promoção válido?**|Se a resposta à pergunta anterior é "Sim" o Publisher tem uma opção para remover completamente esse plano apareça na interface de usuário do Marketplace. Isso significa, clientes não verá esse plano na página de detalhes da oferta. Os usuários finais que receberão um promocode para comprar, poderá assinar usando este promocode.|

## <a name="6---create-your-marketplace-marketing-content"></a>6. criar seu Marketplace conteúdo de marketing
Como fornecer informações necessárias nas guias de **Marketing, preços, suporte e categorias** , visite [O guia de conteúdo Marketing Marketplace](marketplace-publishing-push-to-staging.md) comuns a todos os artefatos publicadas em do Azure Marketplace.  

## <a name="7---connect-your-offer-to-your-service-sql-azure-based-or-web-service-based"></a>7. Conecte sua oferta ao seu serviço (SQL Azure com base ou serviço Web baseado).

Clique no menu sub de **Serviços de dados** .

Na metade superior da página que você será solicitado a fornecer a oferta **Namespace**.  

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.png)

A seguir pergunta definirá como o Publisher será oferta expõem recém-criado ao Azure Marketplace. (Para obter mais detalhes, consulte o [Guia de pré-requisito técnico de serviços de dados](marketplace-publishing-data-service-creation-prerequisites.md)).

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.2.png)

**O serviço de banco de dados com base de publicação**

Clique em **banco de dados**. A seguinte página aparecerão:

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.3.png)

Para criar um mapeamento de CSDL para o conjunto de dados com base no banco de dados do SQL Azure:

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.4.png)

E, em seguida, para cada tabela

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.6.png)

Se o serviço da Web

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [AZURE.IMPORTANT] Leia o [mapeamento de um serviço web existente ao OData por meio de CSDL](marketplace-publishing-data-service-creation-odata-mapping.md) para obter instruções detalhadas e exemplos para a criação de um serviço da Web CSDL.

## <a name="next-steps"></a>Próximas etapas
Agora que você criou sua oferta de serviço de dados, certifique-se que você conclua as instruções no [Guia de conteúdo de Marketing do Marketplace](marketplace-publishing-push-to-staging.md) antes de você avança para [testar seu serviço de dados no teste](marketplace-publishing-data-service-test-in-staging.md).

## <a name="see-also"></a>Consulte também
- [Introdução: Como publicar uma oferta do Azure Marketplace](marketplace-publishing-getting-started.md)
- Se você estiver interessado em entender o processo de mapeamento de OData e a finalidade geral, leia este artigo [Mapeamento de OData de serviço de dados](marketplace-publishing-data-service-creation-odata-mapping.md) para revisar definições, estruturas e instruções.
- Se você estiver interessado em aprender Compreendendo os nós específicos e seus parâmetros, leia este artigo [Nós de mapeamento do dados serviço OData](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) para definições e explicações, exemplos e usar contexto maiusculas.
- Se você estiver interessado em revisando exemplos, leia este artigo [Exemplos de mapeamento do dados serviço OData](marketplace-publishing-data-service-creation-odata-mapping-examples.md) vir o código de amostra e entender contexto e sintaxe do código.


[link-pubportal]:https://publish.windowsazure.com
