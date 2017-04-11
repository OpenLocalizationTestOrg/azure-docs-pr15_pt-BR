<properties 
    pageTitle="Configurar e usar a API de recomendações de aprendizado de máquina | Microsoft Azure" 
    description="API de recomendações Microsoft construídas com perguntas Frequentes de aprendizado de máquina do Azure" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/> 

#<a name="setting-up-and-using-machine-learning-recommendations-api-faq"></a>Configurar e usar as perguntas Frequentes do API recomendações de aprendizado de máquina


**O que é recomendações?**

>[AZURE.NOTE]Você deve começar a usar o serviço de cognitivas API recomendações em vez de nesta versão. O serviço de cognitivas recomendações será substituído por esse serviço e todos os novos recursos serão desenvolvidos lá. Ela tem novos recursos como processamento em lotes de suporte, uma melhor API do Explorer, uma experiência de inscrição/cobrança mais consistente, superfície de API limpeza, etc.
> Saiba mais sobre a [migração para o novo serviço cognitivas](http://aka.ms/recomigrate)

Para organizações e empresas que dependem de venda produtos e serviços aos seus clientes e recomendações para cruzadas, recomendações de aprendizado de máquina do Azure oferece um mecanismo de autoatendimento recomendações. É uma implementação de filtragem colaborativos que usa fatoração de matriz como algoritmo de núcleo. Os desenvolvedores de aplicativo podem acessar recomendações usando APIs REST. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**O que posso fazer com recomendações?**

RECOMENDAÇÕES leva como entrada um item ou um conjunto de itens e retorna uma lista de recomendações relevantes. Por exemplo: um cliente de um revendedor on-line clica em um produto. O revendedor online envia produto como entrada para recomendações, obtém uma lista de produtos em retorno e decide qual desses produtos será mostrada para o cliente. Talvez você queira usar recomendações para otimizar sua loja online ou até mesmo informar seu interior Centro de chamada ou departamento de vendas.

**Existem quaisquer limitações de uso?**

Recomendações tem as seguintes limitações de uso:
* Número máximo de modelos por assinatura: 10
* Número máximo de itens que pode conter um catálogo: 100,000
* O número máximo de pontos de uso que são mantidos é ~ 5.000.000. O mais antigo será excluído se novos serão carregados ou relatados.
* Tamanho máximo de dados que podem ser enviados por email (por exemplo, importar dados de catálogo, importar dados de uso) é 200 MB
* Número de transações por segundo (TPS) para uma compilação de modelo de recomendações que não esteja ativa é TPS ~ 2. Uma compilação de modelo de recomendações que está ativa pode conter até 20 TPS.

##<a name="purchase-and-billing"></a>Compra e cobrança 


**Quanto o custo de recomendações durante o período de lançamento?**

Recomendações é um serviço baseado em assinatura. Charging baseia-se em volume de transações por mês. Você pode verificar [página oferecer] (https://datamarket.azure.com/dataset/amla/recommendations) no Microsoft Azure Marketplace para informações sobre preços.

**Existem quaisquer custos associados tendo recomendações controlar e armazenam atividade do usuário para mim?**

Não no momento.

**Recomendações tem uma avaliação gratuita?**

Não há uma trilha livre, que é restrita a 10.000 transações por mês.

**Quando será eu cobrado para recomendações?**

Uma assinatura paga é qualquer assinatura para o qual há uma taxa mensal. Quando você compra uma assinatura paga, imediatamente cobrado para uso do primeiro mês. Cobrado o valor que está associado a oferta na página assinatura (mais impostos aplicáveis). Este custo mensal é feito a cada mês na mesma data calendário como sua compra original até que você cancele a assinatura. 

**Como atualizar para um serviço de nível superior?**

Você pode comprar ou atualizar sua assinatura do [página oferecer] página (https://datamarket.azure.com/dataset/amla/recommendations) no Microsoft Azure Marketplace.

Quando você atualizar uma assinatura:

* Transações que são restante em sua assinatura antiga não são adicionadas à sua nova assinatura. 
* Você paga o preço total para a nova assinatura, mesmo que você tenha transações não utilizadas em sua assinatura antiga.

Processo de atualização de uma assinatura:

* Nevigate para [página oferecer] (https://datamarket.azure.com/dataset/amla/recommendations).
* Entrar no mercado se você já não estiver conectado.
* No painel à direita, todos os planos disponíveis são listados. Clique no botão de rádio para o plano que você deseja atualizar para.
* Se você deseja atualizar, clique em **Okey**. Se você não deseja atualizar, clique em **Cancelar**.

**Importante** Leia atentamente a caixa de diálogo antes de atualizar porque há implicações de cobrança e uso.

**Quando minha assinatura às recomendações vai terminar?**

Sua assinatura terminará quando você cancelá-la. Se você gostaria de cancelar suas assinaturas, consulte as instruções a seguir.

**Como faço para cancelar minha assinatura recomendações?**

Para cancelar sua assinatura, use as etapas a seguir. Se sua assinatura atual for uma assinatura paga, sua assinatura continua em vigor até o final do período de faturamento atual. Se você precisar o cancelamento em vigor imediatamente, fale conosco em [Suporte da Microsoft](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

**Observação** Nenhum reembolso é dado se você cancelar antes do final de um período de cobrança ou para as transações não utilizadas em um período de cobrança.

* Navegue até [página oferecer] (https://datamarket.azure.com/dataset/amla/recommendations).
* Entrar no mercado se você já não estiver conectado.
* Clique em **Cancelar** à direita do nome do conjunto de dados e status. Você pode usar esta assinatura até o final do período de faturamento atual ou seu limite de transação é alcançado (o que ocorrer primeiro).

Se você gostaria de cancelar sua assinatura imediatamente assim você pode adquirir uma nova assinatura, arquivo um tíquete em [Suporte da Microsoft](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

##<a name="getting-started-with-recommendations"></a>Introdução ao recomendações

**É recomendações para mim?** 

Recomendações de aprendizado de máquina é para as organizações e empresas que contam com recomendações para cruzadas e vendas de produtos ou serviços aos seus clientes. Se você tiver um site voltado ao cliente, uma equipe de vendas, um interior força de vendas, ou um call center, e se você oferecer um catálogo de mais de algumas dezenas de produtos ou serviços, a linha inferior pode beneficiar usando recomendações. 

Experiências com recomendações foi projetado para ser bem simples. A versão atual baseada em API requer conhecimentos básicos de programação. Se precisar de assistência, contate o fornecedor que desenvolveu seu site. Se você tiver um departamento de TI interno ou desenvolvedor interno, eles poderão obter recomendações para trabalhar para você. 

**Quais são os pré-requisitos para configurar recomendações?**

Recomendações requer que você tenha um registro de opções do usuário enquanto ele está relacionado ao seu catálogo. Se você não t tem como um log e você tiver um site de opostas do cliente, recomendações podem coletar atividade do usuário para você. 

Recomendações também requer um catálogo de produtos ou serviços. Se você não t tem o catálogo, recomendações podem usar os dados de uso do cliente real e criar um catálogo. Um catálogo implícito não incluirá itens que não foram relatados como parte das transações de usuário.

**Como configurar recomendações pela primeira vez?**

Após [assinar] (https://datamarket.azure.com/dataset/amla/recommendations) para recomendações, você deve usar a documentação da API do [Azure máquina aprendizagem recomendações guia de início rápido](machine-learning-recommendation-api-quick-start-guide.md) para configurar o serviço.

**Onde posso encontrar documentação da API?** 

A documentação da API é [Azure máquina aprendizagem recomendações Quick Start Guide](machine-learning-recommendation-api-quick-start-guide.md).

**Quais são as opções que tenho carregar dados de uso e de catálogo para recomendações?**

Você tem duas opções para seus dados de catálogo e o uso de carregamento: você pode exportar os dados do seu sistema CRM ou outros logs e carregue-o para recomendações, ou você pode adicionar marcas a seu site que irá controlar as atividades do usuário. Se você usar o último método, os dados serão armazenados no Azure.

##<a name="maintenance-and-support"></a>Manutenção e suporte

**Como grande meu conjunto de dados é possível?**

Cada conjunto de dados pode conter até 100.000 itens de catálogo e até 2048 MB de dados de uso.
Além disso, uma assinatura pode conter até 10 conjuntos de dados (modelos).

**Onde posso obter suporte técnico para recomendações?**

Suporte técnico está disponível no site de [Suporte do Microsoft Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) .

**Onde posso encontrar os termos de uso?**

[Recomendações API termos de serviço de aprendizado de máquina de Microsoft Azure](https://datamarket.azure.com/dataset/amla/recommendations#terms).



 
