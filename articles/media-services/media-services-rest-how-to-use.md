<properties 
    pageTitle="Visão geral da API de REST de serviços de mídia | Microsoft Azure" 
    description="Visão geral da API de REST de serviços de mídia" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="juliako"/>


# <a name="media-services-rest-api-overview"></a>Visão geral da API de REST de serviços de mídia 

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Serviços de mídia do Microsoft Azure é um serviço que aceita solicitações HTTP baseada em OData e pode responder novamente no detalhado JSON ou atom + pub. Como os serviços de mídia está em conformidade com diretrizes de design Azure, há um conjunto de cabeçalhos HTTP necessários que cada cliente deverá usar ao se conectar aos serviços de mídia, bem como um conjunto de cabeçalhos opcionais que podem ser usadas. As seções a seguir descrevem os cabeçalhos e verbos HTTP, você pode usar quando criar solicitações e receber respostas de serviços de mídia.

##<a name="considerations"></a>Considerações 

As seguintes considerações se aplicam ao usar o restante.

- Ao consultar entidades, há um limite de 1000 entidades retornadas de uma vez porque público v2 restante limita os resultados de consulta aos resultados de 1000. Você precisa usar **Ignorar** e **levar** (.NET) / **superior** (REST), como descrito [neste exemplo .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [neste exemplo API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 

- Quando usando JSON e especificando para usar a palavra-chave **__metadata** na solicitação (por exemplo, para referencia um objeto vinculado) que você deve definir o cabeçalho de **Aceitar** formato [JSON detalhado](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (consulte o exemplo a seguir). OData não entende a propriedade **__metadata** na solicitação, a menos que você defina-detalhado.  

        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.11
        Authorization: Bearer <token> 
        Host: media.windows.net
        
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 
        

## <a name="standard-http-request-headers-supported-by-media-services"></a>Cabeçalhos de solicitação HTTP padrão suportados pelos serviços de mídia

Para cada chamada que você transformar em serviços de mídia, há um conjunto de cabeçalhos necessários, que você deve incluir em sua solicitação e também um conjunto de cabeçalhos opcionais que você talvez queira incluir. A tabela a seguir lista os cabeçalhos necessários:


Cabeçalho|Tipo|Valor
---|---|---
Autorização|Portador|Portador é o mecanismo de autorização aceita somente. O valor também deve incluir o símbolo de acesso fornecido pelo ACS.
x-ms-version|Decimal|2.11
DataServiceVersion|Decimal|3.0
MaxDataServiceVersion|Decimal|3.0



>[AZURE.NOTE] Como os serviços de mídia usa OData para expor seu repositório de metadados de ativos subjacente através de APIs REST, os cabeçalhos DataServiceVersion e MaxDataServiceVersion devem ser incluídos em qualquer solicitação; No entanto, se não estiverem, em seguida, atualmente serviços de mídia assume que o valor de DataServiceVersion em uso é 3.0.

Este é um conjunto de cabeçalhos opcionais:

Cabeçalho|Tipo|Valor
---|---|---
Data|Data de RFC 1123|Carimbo de hora da solicitação
Aceitar|Tipo de conteúdo|O tipo de conteúdo solicitado para a resposta como as seguintes:<p> -aplicativo/json; odata = detalhado<p> -aplicativo/atom + xml<p> Respostas podem ter um tipo de conteúdo diferentes, como uma busca de blob, onde uma resposta bem-sucedida conterá o fluxo de blob como a carga.
Codificação de aceitar|Gzip, deflate|GZIP e DEFLATE codificação, quando aplicável. Observação: Para obter recursos grandes, serviços de mídia pode ignorar esse cabeçalho e retornar dados não compactados.
Idioma aceitar|"pt", "es" e assim por diante.|Especifica o idioma preferencial para a resposta.
Conjunto de caracteres aceitar|Tipo de conjunto de caracteres como "UTF-8"|O padrão é UTF-8.
Método de HTTP de X|Método HTTP|Permite que clientes ou firewalls que não dão suporte a métodos HTTP como colocar ou excluir usar esses métodos, encapsulados por meio de uma chamada GET.
Tipo de conteúdo|Tipo de conteúdo|Solicitações de tipo de conteúdo do corpo da solicitação em colocar ou POSTAGEM.
Identificação da solicitação de cliente|Cadeia de caracteres|Um valor definido pelo chamador que identifica a solicitação fornecida. Se especificado, esse valor será incluído na mensagem de resposta como uma forma para mapear a solicitação. <p><p>**Importante**<p>Valores devem ser cobertas em 2096b (2k).

## <a name="standard-http-response-headers-supported-by-media-services"></a>Cabeçalhos de resposta HTTP padrão suportados pelos serviços de mídia

O que vem a seguir é um conjunto de cabeçalhos que pode ser retornado para você, dependendo do recurso que você estava solicitando e a ação que você pretende executar.


Cabeçalho|Tipo|Valor
---|---|---
id da solicitação|Cadeia de caracteres|Um identificador exclusivo para a operação atual, serviço gerado.
Identificação da solicitação de cliente|Cadeia de caracteres|Um identificador especificado pelo chamador na solicitação de original, se estiver presente.
Data|Data de RFC 1123|A data em que a solicitação foi processada.
Tipo de conteúdo|Varia|O tipo de conteúdo do corpo da resposta.
Codificação de conteúdo|Varia|Gzip ou deflate, conforme apropriado.


## <a name="standard-http-verbs-supported-by-media-services"></a>Verbos HTTP padrão suportados pelos serviços de mídia

A seguir é uma lista completa de verbos HTTP que podem ser usadas ao fazer HTTP solicitações:


Verbo|Descrição
---|---
Obter|Retorna o valor atual de um objeto.
Postar|Cria um objeto com base em dados fornecidos ou envia um comando.
COLOCAR|Substitui um objeto ou cria um objeto nomeado (quando aplicável).
EXCLUIR|Exclui um objeto.
MALA DIRETA|Atualiza um objeto existente com as alterações de propriedade nomeada.
CABEÇA|Retorna os metadados de um objeto por uma resposta de obter.

##<a name="limitation"></a>Limitação

Ao consultar entidades, há um limite de 1000 entidades retornadas de uma vez porque público v2 restante limita os resultados de consulta aos resultados de 1000. Você precisa usar **Ignorar** e **levar** (.NET) / **superior** (REST), como descrito [neste exemplo .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [neste exemplo API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 


## <a name="discovering-media-services-model"></a>Descobrindo o modelo de serviços de mídia

Para tornar entidades de serviços de mídia mais detectáveis, a operação de $metadata pode ser usada. Ele permite que você recuperar todos os tipos de entidade válida, propriedades de entidade, associações, funções, ações e assim por diante. O exemplo a seguir mostra como construir o URI: https://media.windows.net/API/$ metadados.

Você deve acrescentar "? version=2.x api" até o final do URI se você deseja exibir os metadados em um navegador ou não inclua o cabeçalho x-ms-version em sua solicitação.



##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





 
