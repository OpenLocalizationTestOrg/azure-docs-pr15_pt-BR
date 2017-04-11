<properties
    pageTitle="Como usar o Fiddler para avaliar e testar APIs de REST de pesquisa do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Use Fiddler para uma abordagem sem código para verificar a disponibilidade de pesquisa do Azure e experimentando as APIs REST."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="10/17/2016"
    ms.author="heidist"/>

# <a name="use-fiddler-to-evaluate-and-test-azure-search-rest-apis"></a>Usar Fiddler para avaliar e testar APIs de REST de pesquisa do Azure
> [AZURE.SELECTOR]
- [Visão geral](search-query-overview.md)
- [Explorador de pesquisa](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [RESTANTE](search-query-rest-api.md)

Este artigo explica como usar o Fiddler, disponível como um [download gratuito a partir Telerik](http://www.telerik.com/fiddler)para problema HTTP solicitações e respostas de modo de exibição usando a API REST de pesquisa de Azure, sem precisar escrever nenhum código. Pesquisa Azure é totalmente gerenciados, hospedado no serviço de pesquisa de nuvem no Microsoft Azure, facilmente programável por meio do .NET e APIs REST. O serviço de pesquisa do Azure APIs REST estão documentadas no [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Nas etapas a seguir, você vai criar um índice, carregar documentos, o índice de consulta e, em seguida, o sistema para informações sobre o serviço de consulta.

Para concluir essas etapas, você precisará de um serviço de pesquisa do Azure e `api-key`. Consulte [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para obter instruções sobre como começar.

## <a name="create-an-index"></a>Criar um índice

1. Inicie o Fiddler. No menu **arquivo** , desative **O tráfego de capturar** para ocultar estranhas atividades HTTP que não está relacionada à tarefa atual.

3. Na guia **compositor** , você vai Formula uma solicitação que se parece com a captura de tela a seguir.

    ![][1]

2. Selecione **colocar**.

3. Insira uma URL que especifica a URL do serviço, atributos de solicitação e a versão da api. Algumas sugestões a ter em mente:
   + Use HTTPS como o prefixo.
   + Atributo de solicitação é "/ índices/hotéis". Isso informa a pesquisa para criar um índice chamado 'hotéis'.
   + Versão da API é minúscula, especificado como "? versão da api = 2015-02-28". As versões de API são importantes porque a pesquisa do Azure implanta atualizações regularmente. Em raros ocasiões, uma atualização do serviço pode apresentar uma alteração significativa à API. Por esse motivo, a pesquisa de Azure requer uma versão de api em cada solicitação para que você está no controle total sobre qual deles é usado.

    A URL completa deve ser semelhante ao seguinte exemplo.

            https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28

4.  Especifica o cabeçalho de solicitação, substituindo o host e a chave de api com valores que são válidos para o serviço.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

5.  No corpo da solicitação, cole nos campos que compõem a definição de índice.
            
             {
            "name": "hotels",  
            "fields": [
              {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
              {"name": "baseRate", "type": "Edm.Double"},
              {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
              {"name": "hotelName", "type": "Edm.String"},
              {"name": "category", "type": "Edm.String"},
              {"name": "tags", "type": "Collection(Edm.String)"},
              {"name": "parkingIncluded", "type": "Edm.Boolean"},
              {"name": "smokingAllowed", "type": "Edm.Boolean"},
              {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
              {"name": "rating", "type": "Edm.Int32"},
              {"name": "location", "type": "Edm.GeographyPoint"}
             ]
            }

6.  Clique em **Executar**.

Em alguns segundos, você deve ver uma resposta de HTTP 201 na lista de sessão, indicando que o índice foi criado com êxito.

Se você obtiver HTTP 504, verifique se que a URL especifica HTTPS. Se você vir HTTP 400 ou 404, verifique o corpo da solicitação para verificar se que não há erros de copiar colar. Normalmente, um HTTP 403 indica um problema com a api-chave (uma chave inválida ou um problema de sintaxe com como o api-chave for especificada).

## <a name="load-documents"></a>Carregar documentos

Na guia **compositor** , sua solicitação para postar documentos aparência a seguir. O corpo da solicitação de contém os dados de pesquisa de 4 hotéis.

   ![][2]

1. Selecione **postar**.

2.  Insira uma URL que começa com HTTPS, seguido por sua URL de serviço, seguida por "/indexes/ <'indexname ' >/docs/índice? versão da api = 2015-02-28". A URL completa deve ser semelhante ao seguinte exemplo.

            https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.  Cabeçalho de solicitação deve ser o mesmo de antes. Lembre-se de que você substituiu o host e a chave de api com valores que são válidos para o serviço.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

4.  Corpo da solicitação contém quatro documentos a ser adicionado ao índice hotéis.

            {
            "value": [
            {
                "@search.action": "upload",
                "hotelId": "1",
                "baseRate": 199.0,
                "description": "Best hotel in town",
                "hotelName": "Fancy Stay",
                "category": "Luxury",
                "tags": ["pool", "view", "wifi", "concierge"],
                "parkingIncluded": false,
                "smokingAllowed": false,
                "lastRenovationDate": "2010-06-27T00:00:00Z",
                "rating": 5,
                "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
              },
              {
                "@search.action": "upload",
                "hotelId": "2",
                "baseRate": 79.99,
                "description": "Cheapest hotel in town",
                "hotelName": "Roach Motel",
                "category": "Budget",
                "tags": ["motel", "budget"],
                "parkingIncluded": true,
                "smokingAllowed": true,
                "lastRenovationDate": "1982-04-28T00:00:00Z",
                "rating": 1,
                "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
              },
              {
                "@search.action": "upload",
                "hotelId": "3",
                "baseRate": 279.99,
                "description": "Surprisingly expensive",
                "hotelName": "Dew Drop Inn",
                "category": "Bed and Breakfast",
                "tags": ["charming", "quaint"],
                "parkingIncluded": true,
                "smokingAllowed": false,
                "lastRenovationDate": null,
                "rating": 4,
                "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
              },
              {
                "@search.action": "upload",
                "hotelId": "4",
                "baseRate": 220.00,
                "description": "This could be the one",
                "hotelName": "A Hotel for Everyone",
                "category": "Basic hotel",
                "tags": ["pool", "wifi"],
                "parkingIncluded": true,
                "smokingAllowed": false,
                "lastRenovationDate": null,
                "rating": 4,
                "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
              }
             ]
            }

8.  Clique em **Executar**.

Em alguns segundos, você deve ver uma resposta HTTP 200 na lista sessão. Isso indica que os documentos foram criados com êxito. Se você receber um 207, pelo menos um documento Falha ao carregar. Se você receber um 404, você tem um erro de sintaxe no cabeçalho ou no corpo da solicitação.

## <a name="query-the-index"></a>O índice da consulta

Agora que um índice e documentos são carregados, você pode emitir consultas em relação a elas.  Na guia **compositor** , um comando **GET** que consulta seu serviço terá aparência semelhante à seguinte imagem capturada.

   ![][3]

1.  Selecione **obter**.

2.  Insira uma URL que começa com HTTPS, seguido por sua URL de serviço, seguida por "/indexes/ <'indexname ' > / docs?", seguido por parâmetros de consulta. Por exemplo, use a URL a seguir, substituindo o nome do host de exemplo com um que seja válido para seu serviço.
    
            https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    Essa consulta pesquisa sobre o termo "bichos" e recupera categorias de faceta para classificações.

3.  Cabeçalho de solicitação deve ser o mesmo de antes. Lembre-se de que você substituiu o host e a chave de api com valores que são válidos para o serviço.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

O código de resposta deve ser 200, e a saída de resposta deve ser semelhante a captura de tela a seguir.

   ![][4]

A consulta de exemplo a seguir é da [operação de índice de pesquisa (API de pesquisa do Azure)](http://msdn.microsoft.com/library/dn798927.aspx) no MSDN. Muitos das consultas de exemplo neste tópico incluem espaços, que não são permitidos no Fiddler. Substituir cada espaço com um caractere + antes de colar na consulta de cadeia de caracteres antes de tentar a consulta na Fiddler.

**Antes de espaços são substituídos:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**Depois de espaços são substituídos por +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28

## <a name="query-the-system"></a>Consultar o sistema

Você também pode consultar o sistema para obter o consumo de armazenamento e contagens de documento. Na guia **compositor** , sua solicitação terá uma aparência semelhante à seguinte e a resposta retornará uma contagem do número de documentos e o espaço utilizado.

 ![][5]

1.  Selecione **obter**.

2.  Insira uma URL que inclui a URL do serviço, seguida por "/ hotéis/índices/estatísticas? versão da api = 2015-02-28":

            https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28

3.  Especifica o cabeçalho de solicitação, substituindo o host e a chave de api com valores que são válidos para o serviço.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

4.  Deixe o corpo da solicitação vazio.

5.  Clique em **Executar**. Você deve ver um código de status HTTP 200 na lista sessão. Selecione a entrada lançada para o comando.

6.  Clique na guia **inspetores** , clique na guia **cabeçalhos** e selecione o formato JSON. Você deve ver o tamanho de armazenamento e a contagem do documento (em KB).

## <a name="next-steps"></a>Próximas etapas

Consulte [Gerenciar o serviço de pesquisa no Azure](search-manage.md) para um sem código abordagem para gerenciar e usar a pesquisa do Azure.


<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
