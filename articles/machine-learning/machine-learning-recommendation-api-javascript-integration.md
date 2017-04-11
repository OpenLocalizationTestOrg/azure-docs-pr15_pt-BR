<properties 
    pageTitle="Recomendações de aprendizado de máquina: Integração de JavaScript | Microsoft Azure" 
    description="Documentação de recomendações de aprendizado de máquina - JavaScript integração - Azure" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="javascript" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/>

# <a name="azure-machine-learning-recommendations---javascript-integration"></a>Recomendações - JavaScript integração de aprendizado de máquina Azure

>[AZURE.NOTE]Você deve começar a usar o serviço de cognitivas API recomendações em vez de nesta versão. O serviço de cognitivas recomendações será substituído por esse serviço e todos os novos recursos serão desenvolvidos lá. Ela tem novos recursos como processamento em lotes de suporte, uma melhor API do Explorer, uma experiência de inscrição/cobrança mais consistente, superfície de API limpeza, etc.
> Saiba mais sobre a [migração para o novo serviço cognitivas](http://aka.ms/recomigrate)


Este documento descrevem como integrar seu site usando JavaScript. O JavaScript permite que você enviar eventos de aquisição de dados e consumir recomendações depois de criar um modelo de recomendação. Todas as operações feitas via JS também podem ser feitas do lado do servidor.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="1-general-overview"></a>1. visão geral de
Integração de seu site com o Azure ML recomendações consistem em 2 fases:

1.  Envie eventos em ML Azure recomendações. Isso permitirá para criar um modelo de recomendação.
2.  Consuma as recomendações. Depois que o modelo foi criado, você pode consumir as recomendações. (Este documento não explica como criar um modelo, leia o guia de início rápido para obter mais informações sobre como).


<ins>Fase I</ins>

Na primeira fase você insere nas suas páginas html uma pequena biblioteca JavaScript que permite a página para enviar eventos conforme eles ocorrem na página html em servidores do Azure ML recomendações (via dados de mercado):

![Drawing1][1]

<ins>Fase II</ins>

Na segunda fase quando você deseja mostrar as recomendações na página que você selecione uma das seguintes opções:

1. seu servidor (em fase de renderização da página) chama Azure ML recomendações Server (via dados de mercado) para obter recomendações. Os resultados incluem uma lista de identificação de itens. Seu servidor precisa aprimorar os resultados com os itens de dados de Meta (por exemplo, imagens, descrição) e enviar a página criada para o navegador.

![Drawing2][2]

2. a outra opção é usar o arquivo JavaScript pequeno da fase um para obter uma lista simples de itens recomendados. Os dados recebidos aqui serão mais enxutos na primeira opção.

![Drawing3][3]

##<a name="2-prerequisites"></a>2. pré-requisitos de

1. Crie um novo modelo usando as APIs. Consulte o guia de início rápido sobre como fazer isso.
2. Codificar seu &lt;dataMarketUser&gt;:&lt;dataMarketKey&gt; na Base 64. (Isso será usado para a autenticação básica para ativar o código JS chamar as APIs).


##<a name="3-send-data-acquisition-events-using-javascript"></a>3. enviar eventos de aquisição de dados usando JavaScript
As etapas a seguir facilitam o envio de eventos:

1.  Inclua JQuery biblioteca em seu código. Você pode baixá-lo do nuget na URL a seguir.

        http://www.nuget.org/packages/jQuery/1.8.2
2.  Incluir biblioteca recomendações Java Script da seguinte URL: http://aka.ms/RecoJSLib1

3.  Inicialize a biblioteca do Azure ML recomendações com os parâmetros apropriados.

        <script>
            AzureMLRecommendationsStart("<base64encoding of username:key>",
            "<model_id>");
        </script>

4.  Envie o evento apropriado. Consulte a seção detalhada abaixo em todos os tipos de eventos (exemplo de evento click)

        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") {      
                        AzureMLRecommendationsEvent = [];
                    }
            AzureMLRecommendationsEvent.push({ event: "click", item: "18321116" });
        </script>


###<a name="31-limitations-and-browser-support"></a>3.1. Suporte ao navegador e limitações
Esta é uma implementação de referência e ele é fornecido como está. Ele deve oferecer suporte a todos os principais navegadores.

###<a name="32-type-of-events"></a>3,2. Tipo de eventos
Há 5 tipos de evento que ofereça suporte a biblioteca: clique em, clique em recomendação, adicionar ao carrinho de compras, remova o carrinho de compras e compra. Não há um evento adicional que é usado para definir o contexto de usuário chamado Login.

####<a name="321-click-event"></a>3.2.1. Clique em evento
Este evento deve ser usado sempre que um usuário clicado em um item. Geralmente quando o usuário clica em um item uma nova página é aberta com os detalhes do item; nesta página este evento deve ser disparado.

Parâmetros:
- evento (cadeia, obrigatório) - "clique"
- item (cadeia, obrigatória) - Identificador exclusivo do item
- nome do item (cadeia, opcional) - o nome do item
- itemDescription (cadeia, opcional) - a descrição do item
- itemCategory (cadeia, opcional) - a categoria do item
        
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "click", item: "3111718" });
        </script>

Ou com dados opcionais:

        <script>
            if (typeof AzureMLRecommendationsEvent === "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "click", item: "3111718", itemName: "Plane", itemDescription: "It is a big plane", itemCategory: "Aviation"});
        </script>


####<a name="322-recommendation-click-event"></a>3.2.2. Recomendação em evento
Este evento deve ser usado sempre que um usuário clicado em um item que foi recebido do Azure ML recomendações como um item recomendado. Geralmente quando o usuário clica em um item uma nova página é aberta com os detalhes do item; nesta página este evento deve ser disparado.

Parâmetros:
- evento (cadeia, obrigatório) - "recommendationclick"
- item (cadeia, obrigatória) - Identificador exclusivo do item
- nome do item (cadeia, opcional) - o nome do item
- itemDescription (cadeia, opcional) - a descrição do item
- itemCategory (cadeia, opcional) - a categoria do item
- propaga (cadeia matriz, opcional) - as sementes que gerou a consulta de recomendação.
- recoList (cadeia matriz, opcional) - o resultado da solicitação de recomendação que gerou o item que foi clicado.
        
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "recommendationclick", item: "18899918" });
        </script>

Ou com dados opcionais:

        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: eventName, item: "198", itemName: "Plane2", itemDescription: "It is a big plane2", itemCategory: "Default2", seeds: ["Seed1", "Seed2"], recoList: ["199", "198", "197"]               });
        </script>


####<a name="323-add-shopping-cart-event"></a>3.2.3. Adicionar evento de carrinho de compras
Este evento deve ser usado quando o usuário adiciona um item ao carrinho de compras.
Parâmetros:
* evento (cadeia, obrigatório) - "addshopcart"
* item (cadeia, obrigatória) - Identificador exclusivo do item
* nome do item (cadeia, opcional) - o nome do item
* itemDescription (cadeia, opcional) - a descrição do item
* itemCategory (cadeia, opcional) - a categoria do item
        
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "addshopcart", item: "13221118" });
        </script>

####<a name="324-remove-shopping-cart-event"></a>3.2.4. Remover o evento de carrinho de compras
Este evento deve ser usado quando o usuário remove um item ao carrinho de compras.

Parâmetros:
* evento (cadeia, obrigatório) - "removeshopcart"
* item (cadeia, obrigatória) - Identificador exclusivo do item
* nome do item (cadeia, opcional) - o nome do item
* itemDescription (cadeia, opcional) - a descrição do item
* itemCategory (cadeia, opcional) - a categoria do item
        
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "removeshopcart", item: "111118" });
        </script>

####<a name="325-purchase-event"></a>3.2.5. Evento de compra
Este evento deve ser usado quando o usuário comprou seu carrinho de compras.

Parâmetros:
* evento (cadeia) - "adquirir"
* itens (adquiridos []) - matriz mantém uma entrada para cada item comprado.<br><br>
Formato comprado:
    * item (cadeia) - Identificador exclusivo do item.
    * contagem (int ou cadeia de caracteres) - número de itens que foram compradas.
    * preço (flutuação ou cadeia de caracteres) - campo opcional - o preço do item.

O exemplo a seguir mostra a compra de 3 itens (33, 34, 35), dois com todos os campos preenchidos (item, contagem, preço) e um (item 34) sem um preço.

        <script>
            if ( typeof AzureMLRecommendationsEvent == "undefined"){ AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "purchase", items: [{ item: "33", count: "1", price: "10" }, { item: "34", count: "2" }, { item: "35", count: "1", price: "210" }] });
        </script>

####<a name="326-user-login-event"></a>3.2.6. Evento de logon do usuário
Biblioteca de ML recomendações evento Azure cria e usar um cookie para identificar os eventos que vieram do mesmo navegador. Para melhorar os resultados de modelo Azure ML recomendações permite definir uma identificação exclusiva do usuário que substituirá o uso de cookies.

Este evento deve ser usado após o logon do usuário para o seu site.

Parâmetros:
* evento (cadeia) - "userlogin"
* usuário (cadeia) - identificação exclusiva do usuário.
        <script>
  Se (typeof AzureMLRecommendationsEvent = = "indefinido") {AzureMLRecommendationsEvent = [];} AzureMLRecommendationsEvent.push ({evento: "userlogin", usuário: "ABCD10AA"});      </script>

##<a name="4-consume-recommendations-via-javascript"></a>4. consumir recomendações por meio de JavaScript
O código que consome a recomendação é disparado por algum evento JavaScript por página da Web do cliente. A resposta de recomendação inclui as Ids de itens recomendados, seus nomes e suas classificações. É melhor usar esta opção apenas para uma exibição de lista dos itens Recomendados - manipulação mais complexo (como a adição de metadados do item) deve ser feita na integração de lado do servidor.

###<a name="41-consume-recommendations"></a>4.1 consumir recomendações
Para consumir recomendações, que você precisa incluem as bibliotecas JavaScript necessárias na sua página e para chamar AzureMLRecommendationsStart. Consulte a seção 2.

Consumir recomendações para um ou mais itens que você precisa chamar um método chamado: AzureMLRecommendationsGetI2IRecommendation.

Parâmetros:
* itens (matriz de cadeias de caracteres) - um ou mais itens para obter recomendações para. Se você consumir uma compilação de Fbt, em seguida, você pode definir aqui somente um item.
* numberOfResults (int) - número de resultados necessários.
* includeMetadata (booliano, opcional) - se definida como 'true' indica que o campo de metadados deve ser preenchido no resultado.
* Função - uma função que irá manipular as recomendações de processamento retornado. Os dados são retornados como uma matriz de:
    * Id exclusiva item - item
    * Name - item nome (se existir no catálogo)
    * classificação - classificação de recomendação
    * metadados - uma cadeia de caracteres que representa os metadados do item

Exemplo: O código a seguir solicita 8 recomendações para item "64f6eb0d-947a-4c18-a16c-888da9e228ba" (e não especificando includeMetadata - implicitamente diz que não há metadados são necessário), ele depois concatenar os resultados em um buffer.

        <script>
            var reco = AzureMLRecommendationsGetI2IRecommendation(["64f6eb0d-947a-4c18-a16c-888da9e228ba"], 8, false, function (reco) {
                var buff = "";
                for (var ii = 0; ii < reco.length; ii++) {
                    buff += reco[ii].item + "," + reco[ii].name + "," + reco[ii].rating + "\n";
                }
                alert(buff);
            });
        </script>


[1]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing1.png
[2]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing2.png
[3]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing3.png
 
