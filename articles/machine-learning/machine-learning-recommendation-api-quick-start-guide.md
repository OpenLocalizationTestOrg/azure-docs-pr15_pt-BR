<properties 
    pageTitle="Guia de início rápido: API de recomendações de aprendizado de máquina | Microsoft Azure" 
    description="Azure aprendizado de máquina recomendações - guia de início rápido" 
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

# <a name="quick-start-guide-for-the-machine-learning-recommendations-api"></a>Guia de início rápido para a API de recomendações de aprendizado de máquina

>[AZURE.NOTE]Você deve começar a usar o serviço de cognitivas API recomendações em vez de nesta versão. O serviço de cognitivas recomendações será substituído por esse serviço e todos os novos recursos serão desenvolvidos lá. Ela tem novos recursos como processamento em lotes de suporte, uma melhor API do Explorer, uma experiência de inscrição/cobrança mais consistente, superfície de API limpeza, etc.
> Saiba mais sobre a [migração para o novo serviço cognitivas](http://aka.ms/recomigrate)


Este documento descreve como integrado ao seu serviço ou o aplicativo para usar recomendações de aprendizado de máquina do Microsoft Azure. Você pode encontrar mais detalhes sobre a API de recomendações na [Galeria](http://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="general-overview"></a>Visão geral

Para usar recomendações de aprendizado de máquina do Azure, você precisa executar as seguintes etapas:

* Criar um modelo, um modelo é um contêiner de seus dados de uso, dados do catálogo e o modelo de recomendação.
* Importar dados de catálogo - um catálogo contém informações de metadados sobre os itens. 
* Importar dados de uso - dados de uso podem ser carregados em uma das maneiras de 2 (ou ambas):
    * Carregar um arquivo que contém os dados de uso.
    * Enviando eventos de aquisição de dados.
    Normalmente você carrega um arquivo de uso para poder criar um modelo de recomendação inicial (inicialização) e usá-lo até que o sistema reúne suficiente dados usando o formato de aquisição de dados.
* Criar um modelo de recomendação - esta é uma operação assíncrona no qual o sistema de recomendação leva todos os dados de uso e cria um modelo de recomendação. Essa operação pode levar alguns minutos ou várias horas dependendo do tamanho dos dados e os parâmetros de configuração de compilação. Quando disparo a compilação, você obterá uma ID de compilação. Usá-lo para verificar quando o processo de compilação foi encerrado antes de começar a consumir recomendações.
* Consuma recomendações - recomendações de Get para um item específico ou uma lista de itens.

Todas as etapas acima são feitas por meio da API de recomendações de aprendizado de máquina Azure.  Você pode fazer o download de um aplicativo de exemplo que implementa cada uma dessas etapas do [Galeria bem.](http://1drv.ms/1xeO2F3)

##<a name="limitations"></a>Limitações

* O número máximo de modelos por assinatura é 10.
* O número máximo de itens que pode conter um catálogo é 100,000.
* O número máximo de pontos de uso que são mantidos é ~ 5.000.000. O mais antigo será excluído se novos serão carregados ou relatados.
* O tamanho máximo de dados que podem ser enviados no POST (por exemplo, importar dados de catálogo, importar dados de uso) é 200MB.
* O número de transações por segundo para uma compilação de modelo de recomendação que não esteja ativa é ~ 2TPS. Uma compilação de modelo de recomendação que está ativa pode conter backup para 20TPS.

##<a name="integration"></a>Integração

###<a name="authentication"></a>Autenticação
O Azure Marketplace suporta o Basic ou OAuth método de autenticação. Você pode localizar facilmente as teclas de conta, navegando até as teclas no mercado em [suas configurações de conta](https://datamarket.azure.com/account/keys). 
####<a name="basic-authentication"></a>Autenticação básica
Adicione o cabeçalho de autorização:

    Authorization: Basic <creds>
               
    Where <creds> = ConvertToBase64("AccountKey:" + yourAccountKey);  
    
Converter na Base 64 (c#)

    var bytes = Encoding.UTF8.GetBytes("AccountKey:" + yourAccountKey);
    var creds = Convert.ToBase64String(bytes);
    
Converter na Base 64 (JavaScript)

    var creds = window.btoa("AccountKey" + ":" + yourAccountKey);
    



###<a name="service-uri"></a>URI serviço
A raiz de serviço URI para as APIs de recomendações de aprendizado de máquina Azure é [aqui.](https://api.datamarket.azure.com/amla/recommendations/v2/)

URI de serviço completo é expresso usando elementos da especificação de OData.

###<a name="api-version"></a>Versão da API
Cada chamada de API terá, no final, um parâmetro de consulta chamado apiVersion que deve ser definida como "1.0".

###<a name="ids-are-case-sensitive"></a>IDs diferenciam maiusculas de minúsculas
IDs, retornados por qualquer uma das APIs, diferenciam maiusculas de minúsculas e devem ser usados como tal quando passados como parâmetros em chamadas de API subsequentes. Por exemplo, identificações de modelo e catálogo diferenciam maiusculas de minúsculas.

###<a name="create-a-model"></a>Criar um modelo
Criar uma solicitação "Criar modelo":

| Método HTTP | URI |
|:--------|:--------|
|Postar     |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27`|

|   Nome do parâmetro  |   Valores válidos                        |
|:--------          |:--------                              |
|   modelName   |   Apenas letras (A-Z, a-z), números (0-9), hifens (-) e sublinhado (_) são permitidos.<br>Comprimento máximo: 20 |
|   apiVersion      | 1.0 |
|||
| Corpo da solicitação | NENHUM |


**Resposta**:

Código de HTTP Status: 200

- `feed/entry/content/properties/id`-Contém a ID de modelo.
**Observação**: ID de modelo diferencia maiusculas de minúsculas.

XML de OData

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Create A New Model</subtitle>
      <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T06:35:21Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
      </entry>
    </feed>


###<a name="import-catalog-data"></a>Importar dados de catálogo

Se você carregar vários arquivos de catálogo para o mesmo modelo com várias chamadas, podemos irá inserir somente os novos itens de catálogo. Os itens existentes permanecerão com os valores originais.

| Método HTTP | URI |
|:--------|:--------|
|Postar     |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|   Nome do parâmetro  |   Valores válidos                        |
|:--------          |:--------                              |
|   modelId |   Identificador exclusivo do modelo (diferencia maiusculas de minúscula)  |
| nome do arquivo | Identificador textual do catálogo.<br>Apenas letras (A-Z, a-z), números (0-9), hifens (-) e sublinhado (_) são permitidos.<br>Comprimento máximo: 50 |
|   apiVersion      | 1.0 |
|||
| Corpo da solicitação | Catálogo de dados. Formato:<br>`<Item Id>,<Item Name>,<Item Category>[,<description>]`<br><br><table><tr><th>Nome</th><th>Obrigatório</th><th>Tipo</th><th>Descrição</th></tr><tr><td>Id do item</td><td>Sim</td><td>Comprimento máximo, alfanumérico 50</td><td>Identificador exclusivo de um item</td></tr><tr><td>Nome do item</td><td>Sim</td><td>Comprimento alfanumérico, máximo 255</td><td>Nome do item</td></tr><tr><td>Categoria de item</td><td>Sim</td><td>Comprimento alfanumérico, máximo 255</td><td>Categoria à qual este item pertence (por exemplo, culinária livros, Drama...)</td></tr><tr><td>Descrição</td><td>Não</td><td>Comprimento máximo, alfanumérico 4000</td><td>Descrição deste item</td></tr></table><br>Tamanho máximo de arquivo é 200MB.<br><br>Exemplo:<br><pre>2406e770-769c-4189-89de-1c9283f93a96, Clara Callan, catálogo<br>21bf8088-b6c0-4509-870c-e1c7ac78304a, a sala esquecer: uma ficção (Byzantium livro), do livro<br>Catálogo de 3bb5cb44-d143-4bdd-a55c-443964bf4b23, Spadework,<br>552a1940-21e4-4399-82bb-594b46d7ed54, moderação de feras, catálogo</pre> |


**Resposta**:

Código de HTTP Status: 200

- `Feed\entry\content\properties\LineCount`-O número de linhas aceitas.
- `Feed\entry\content\properties\ErrorCount`-Número de linhas que não foram inseridos devido a um erro.

XML de OData

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
        <subtitle type="text">Import catalog file</subtitle>
        <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">ImportCatalogFileEntity2</title>
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">4</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
      </m:properties>
    </content>
     </entry>
    </feed>


###<a name="import-usage-data"></a>Importar dados de uso

####<a name="uploading-a-file"></a>Ao carregar um arquivo
Esta seção mostra como carregar dados de uso usando um arquivo. Você pode chamar essa API várias vezes com dados de uso. Todos os dados de uso serão salvos para todas as chamadas.

| Método HTTP | URI |
|:--------|:--------|
|Postar     |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|   Nome do parâmetro  |   Valores válidos                        |
|:--------          |:--------                              |
|   modelId |   Identificador exclusivo do modelo (diferencia maiusculas de minúscula) |
| nome do arquivo | Identificador textual do catálogo.<br>Apenas letras (A-Z, a-z), números (0-9), hifens (-) e sublinhado (_) são permitidos.<br>Comprimento máximo: 50 |
|   apiVersion      | 1.0 |
|||
| Corpo da solicitação | Dados de uso. Formato:<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>Nome</th><th>Obrigatório</th><th>Tipo</th><th>Descrição</th></tr><tr><td>Id de usuário</td><td>Sim</td><td>Alfanuméricos</td><td>Identificador exclusivo de um usuário</td></tr><tr><td>Id do item</td><td>Sim</td><td>Comprimento máximo, alfanumérico 50</td><td>Identificador exclusivo de um item</td></tr><tr><td>Tempo</td><td>Não</td><td>Data no formato: DD/MM/ddTHH (por exemplo, 2013/06/20T10:00:00)</td><td>Tempo de dados</td></tr><tr><td>Evento</td><td>Não, se fornecido, também deve colocar a data</td><td>Um destes procedimentos:<br>• Clique em<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Compra</td><td></td></tr></table><br>Tamanho máximo de arquivo é 200MB.<br><br>Exemplo:<br><pre>149452, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951, 1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**Resposta**:

Código de HTTP Status: 200

- `Feed\entry\content\properties\LineCount`-O número de linhas aceitas.
- `Feed\entry\content\properties\ErrorCount`-Número de linhas que não foram inseridos devido a um erro.
- `Feed\entry\content\properties\FileId`-Identificador de arquivo.


XML de OData

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Add bulk usage data (usage file)</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
    </entry>
    </feed>


####<a name="using-data-acquisition"></a>Usando a aquisição de dados
Esta seção mostra como enviar eventos em tempo real para recomendações de aprendizado de máquina do Azure, geralmente de seu site.

| Método HTTP | URI |
|:--------|:--------|
|Postar     |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|   Nome do parâmetro  |   Valores válidos                        |
|:--------          |:--------                              |
|   apiVersion      | 1.0 |
|||
|Corpo da solicitação| Entrada de dados de evento para cada evento que você deseja enviar. Você deve enviar para a mesma sessão do navegador ou usuário a mesma ID no campo ID de sessão. (Consulte a amostra de corpo de evento abaixo.)|


- Exemplo de evento 'Clique':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>Click</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Exemplo de evento 'RecommendationClick':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>RecommendationClick</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Exemplo de evento 'AddShopCart':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>AddShopCart</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Exemplo de evento 'RemoveShopCart':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>RemoveShopCart</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Exemplo de evento 'Compra':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
            <Name>Purchase</Name> 
            <PurchaseItems>
            <PurchaseItems>
                <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
                <Count>3</Count>
            </PurchaseItems>
        </PurchaseItems>
        </EventData>
        </EventData>
        </Event>

- Exemplo enviando 2 eventos, 'Clique' e 'AddShopCart':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>Click</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        <ItemName>itemName</ItemName>
        <ItemDescription>item description</ItemDescription>
        <ItemCategory>category</ItemCategory>
        </EventData>
        <EventData>
        <Name>AddShopCart</Name>
        <ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
        </EventData>
        </EventData>
        </Event>

**Resposta**: código de HTTP Status: 200

###<a name="build-a-recommendation-model"></a>Criar um modelo de recomendação

| Método HTTP | URI |
|:--------|:--------|
|Postar     |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|

|   Nome do parâmetro  |   Valores válidos                        |
|:--------          |:--------                              |
| modelId | Identificador exclusivo do modelo (diferencia maiusculas de minúscula)  |
| userDescription | Identificador textual do catálogo. Observe que se você usar espaços que você deve codificá-lo com 20% em vez disso. Consulte o exemplo acima.<br>Comprimento máximo: 50 |
| apiVersion | 1.0 |
|||
| Corpo da solicitação | NENHUM |

**Resposta**:

Código de HTTP Status: 200

Esta é uma API assíncrona. Você obterá uma ID de compilação como uma resposta. Para saber quando a compilação foi encerrado, você deve chamar a API de "Obter cria Status de um modelo" e localize essa ID de compilação na resposta. Observe que uma compilação pode levar de minutos a horas dependendo do tamanho dos dados.

Você não pode consumir recomendações até a compilação termina.

Status de compilação válidos:

- Criar – modelo foi criado.
- Enfileirados – compilação de modelo foi acionada e ele está na fila.
- Construção – modelo está sendo criada.
- Sucesso – compilação finalizada com êxito.
- Erro – compilação encerrada com falha.
- Compilação cancelada – foi cancelada.
- Cancelando – compilação está sendo cancelada.


Observe que a ID de compilação pode ser encontrada no caminho a seguir:`Feed\entry\content\properties\Id`

XML de OData

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Build a Model with RequestBody</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
    </entry>
    </feed>

###<a name="get-build-status-of-a-model"></a>Obter o status de compilação de um modelo

| Método HTTP | URI |
|:--------|:--------|
|Obter     |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|



|   Nome do parâmetro  |   Valores válidos                        |
|:--------          |:--------                              |
|   modelId         |   Identificador exclusivo do modelo (diferencia maiusculas de minúscula)    |
|   onlyLastBuild   |   Indica se retornar todo o histórico de compilação do modelo ou apenas o status da versão mais recente. |
|   apiVersion      |   1.0                                 |


**Resposta**:

Código de HTTP Status: 200

A resposta inclui uma entrada por compilação. Cada entrada tem os seguintes dados:

- `feed/entry/content/properties/UserName`– Nome do usuário.
- `feed/entry/content/properties/ModelName`– Nome do modelo.
- `feed/entry/content/properties/ModelId`– Modelo identificador exclusivo.
- `feed/entry/content/properties/IsDeployed`– Se a compilação for implantada (também conhecido como Construir ativa).
- `feed/entry/content/properties/BuildId`– Crie identificador exclusivo.
- `feed/entry/content/properties/BuildType`-Tipo de compilação.
- `feed/entry/content/properties/Status`– Status da compilação. Pode ser um dos seguintes: erro, construção, enfileirados, Cancelling, cancelado, sucesso
- `feed/entry/content/properties/StatusMessage`– Mensagem de status detalhado (aplica-se somente a estados específicos).
- `feed/entry/content/properties/Progress`– Crie andamento (%).
- `feed/entry/content/properties/StartTime`– Crie a hora de início.
- `feed/entry/content/properties/EndTime`– Crie a hora de término.
- `feed/entry/content/properties/ExecutionTime`– Crie a duração.
- `feed/entry/content/properties/ProgressStep`– Detalhes sobre o estágio atual que uma compilação em andamento está em.

Status de compilação válidos:
- Entrada de solicitação de compilação criado – foi criada.
- Enfileirados – solicitação de compilação foi acionada e ele está na fila.
- Construção – compilação está em andamento.
- Sucesso – compilação finalizada com êxito.
- Erro – compilação encerrada com falha.
- Compilação cancelada – foi cancelada.
- Cancelando – compilação está sendo cancelada.

Valores válidos para o tipo de compilação:
- Ordem de classificação - compilação. (Para fileira construir detalhes, consulte o documento "Documentação de recomendação de aprendizado de máquina API")
- Recomendação - compilação de recomendação.
- FBT - frequentemente comprado juntos compilação.

XML de OData

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get builds status of a model</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetBuildsStatusEntity</title>
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
        <d:ModelName m:type="Edm.String">ModelName</d:ModelName>
        <d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
        <d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
        <d:BuildId m:type="Edm.String">1000272</d:BuildId>
        <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
        <d:Status m:type="Edm.String">Success</d:Status>
        <d:StatusMessage m:type="Edm.String"></d:StatusMessage>
        <d:Progress m:type="Edm.String">0</d:Progress>
        <d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
        <d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
        <d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
        <d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
        <d:ProgressStep m:type="Edm.String"></d:ProgressStep>
      </m:properties>
     </content>
    </entry>
    </feed>


###<a name="get-recommendations"></a>Obter recomendações

| Método HTTP | URI |
|:--------|:--------|
|Obter     |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|



|   Nome do parâmetro  |   Valores válidos                        |
|:--------          |:--------                              |
| modelId | Identificador exclusivo do modelo (diferencia maiusculas de minúscula) |
| itemIds | Lista separada por vírgulas dos itens recomendar para.<br>Comprimento máximo: 1024 |
| numberOfResults | Número de resultados necessários |
| includeMetatadata | Uso futuro, sempre false |
| apiVersion | 1.0 |

**Resposta:**

Código de HTTP Status: 200

A resposta inclui uma entrada por item recomendado. Cada entrada tem os seguintes dados:

- `Feed\entry\content\properties\Id`-ID do item recomendados.
- `Feed\entry\content\properties\Name`-Nome do item.
- `Feed\entry\content\properties\Rating`-Classificação da recomendação; número mais alto significa confiança maior.
- `Feed\entry\content\properties\Reasoning`-Recomendação raciocínio (por exemplo, explicações recomendação).

XML de OData

A resposta de exemplo abaixo inclui 10 itens recomendados:

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
     <subtitle type="text">Get Recommendation</subtitle>
     <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
    </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
    </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
    </feed>

###<a name="update-model"></a>Modelo de atualização
Você pode atualizar a descrição do modelo ou a ID de compilação ativa.
*ID de compilação active* - cada compilação para cada modelo tem um ID de compilação. A ID de compilação ativa é a primeira compilação bem-sucedida de cada novo modelo. Depois que você tiver uma ID de compilação ativa e você fazer cria adicionais para o mesmo modelo, você precisa defini-la explicitamente como a ID de compilação padrão se você quiser. Quando você utilizar recomendações, se você não especificar a identificação de compilação que você deseja usar, o padrão será usado automaticamente.

Esse mecanismo permite - quando você tiver um modelo de recomendação em produção - criem novos modelos e testá-las antes de promovê-los para produção.

| Método HTTP | URI |
|:--------|:--------|
|COLOCAR     |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|


|   Nome do parâmetro  |   Valores válidos                        |
|:--------          |:--------                              |
| ID | Identificador exclusivo do modelo (diferencia maiusculas de minúscula) |
| apiVersion | 1.0 |
|||
| Corpo da solicitação | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`   <Description>New Description</Description>`<br>`          <ActiveBuildId>-1</ActiveBuildId>`<br>`</ModelUpdateParams>`<br><br>Observe que as marcas XML descrição e ActiveBuildId são opcionais. Se você não deseja definir descrição ou ActiveBuildId, remova a marca inteira. |

**Resposta**:

Código de HTTP Status: 200

XML de OData

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Update an Existing Model</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'</id>
    <rights type="text" />
     <updated>2014-10-05T10:27:17Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'" />
    </feed>

##<a name="legal"></a>Legal
Este documento é fornecido "como-é". Informações e modos de exibição expressos neste documento, incluindo URL e outras referências de site da Internet, podem ser alteradas sem aviso prévio. Alguns exemplos aqui representados são fornecidos somente para ilustração e são fictícios. Nenhuma associação real ou conexão é intencional ou deve ser inferida. Este documento não fornecer nenhum direito legal a qualquer propriedade intelectual de nenhum produto Microsoft. Você pode copiar e usar este documento para seu internos, fins de referência. © Microsoft de 2014. Todos os direitos reservados. 
 
