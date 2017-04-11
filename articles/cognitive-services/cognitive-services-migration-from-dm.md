
<properties
    pageTitle="Migrar para o Azure serviços cognitivas recomendações API das recomendações DataMarket API | Microsoft Azure"
    description="Recomendações – migrando para o serviço cognitivas recomendações de aprendizado de máquina Azure"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="luisca"/>


# <a name="migrate-to-azure-cognitive-services-recommendations-api-from-the-datamarket-recommendations-api"></a>Migrar para o Azure serviços cognitivas recomendações API das recomendações DataMarket API
Este artigo mostra como migrar da [API do Microsoft DataMarket recomendações](https://datamarket.azure.com/dataset/amla/recommendations) para a [API de recomendações de serviços cognitivas do Microsoft Azure](https://www.microsoft.com/cognitive-services/en-us/recommendations-api).

A API de recomendações DataMarket deixará de aceitar novos clientes 31 de dezembro de 2016 e será substituída 28 de fevereiro de 2017.

## <a name="how-do-i-start-using-the-azure-cognitive-services-recommendations-api"></a>Como faço para começar usando a API do Azure cognitivas serviços recomendações?

Para migrar para a API de recomendações cognitivas Services, faça o seguinte:

1.  Se você ainda não tiver uma assinatura do Azure, [Inscreva-se](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) para um. 

1.  Obter instruções passo a passo do [Guia de início rápido](cognitive-services-recommendations-quick-start.md) inscrever-se para a API de recomendações serviços cognitivas e usá-lo por programação. 

1.  Vá para a [página inicial do cognitivas API de recomendações de serviços](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) para saber mais sobre o serviço e encontrar documentação.

## <a name="i-used-the-recommendations-ui-to-build-my-models-is-there-a-similar-tool-for-the-cognitive-services-recommendations-api"></a>Usei a IU recomendações para criar meus modelos. Existe uma ferramenta semelhante a cognitivas API de recomendações de serviços?

Absolutamente! A URL para a nova [Interface de usuário de recomendações](http://recommendations-portal.azurewebsites.net/) é http://recommendations-portal.azurewebsites.net. 

>[AZURE.NOTE] Suas credenciais de DataMarket não funcionam aqui. Inscrever-se para uma assinatura no Portal do Azure e obtenha a chave de conta necessário para usar a nova [Interface de usuário de recomendações](http://recommendations-portal.azurewebsites.net/).
Se você não tiver uma tecla de conta, consulte tarefa 1 do [Guia de início rápido](cognitive-services-recommendations-quick-start.md).

##<a name="is-the-new-api-format-the-same-as-the-datamarket-recommendations-api"></a>O novo formato de API é a mesma que a API de recomendações DataMarket?

A API suporta os mesmos cenários e fluxos de processo como esses cenários suportados na versão DataMarket, mas a interface API real foi atualizada de acordo com as [Diretrizes de API REST Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). As APIs são mais consistentes e trabalhar melhor com ferramentas que suportam Swagger.

Para entender cada uma das APIs, confira o [Explorador de API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3db).
Use *tente* -botão para testar uma chamada API. O formato dos arquivos consumida por API recomendações (arquivos de catálogo e o uso) não foi alterado, para que você pode continuar usando o mesmo arquivos e/ou infraestrutura que você criou para gerar esses arquivos.

##<a name="what-are-some-new-features-in-the-cognitive-services-recommendations-api"></a>Quais são alguns recursos novos na cognitivas API recomendações serviços?

Nos últimos dois meses, podemos ter lançado novos recursos para a API de recomendações cognitivas serviços:
-   Recomendações de interface do usuário para treinamento e teste modelos sem precisar escrever uma única linha de código
-   Lote pontuação para fornecer milhares de recomendações ao mesmo tempo
-   Métricas de apoio a consulta a qualidade dos modelos de recomendações
-   Suporte para regras de negócios
-   Capacidade de enumerar e baixar arquivos de catálogo e uso
-   Suporte de compilação de classificação para consultar a qualidade dos recursos de item em um modelo de recomendações
-   Capacidade adicionada para procurar um produto no catálogo

## <a name="when-does-microsoft-stop-supporting-the-datamarket-recommendations-api"></a>Quando o Microsoft parar a API DataMarket recomendações de suporte?

[API de recomendações no DataMarket](https://datamarket.azure.com/dataset/amla/recommendations) interrompe a aceitar novos clientes após 31 de dezembro de 2016 e será substituída completamente por 28 de fevereiro de 2017. 

## <a name="what-if-i-dont-have-the-data-that-i-need-to-recreate-my-models-in-the-cognitive-services-recommendations-api"></a>E se eu não tiver os dados que preciso recriar Meus modelos na cognitivas API recomendações serviços?

Queremos tornar essa transição tão fácil quanto possível para você. Podemos ajudá-lo a mover seus modelos antigos de sua conta do DataMarket para sua nova assinatura de API do Azure cognitivas Services recomendações. Fale conosco em [mlapi@microsoft.com](mailto://mlapi@microsoft.com). Pedimos que você forneça seu DataMarket ID da assinatura e seu ID de assinatura de serviços cognitivas, antes de nós associar os modelos a sua nova conta.
