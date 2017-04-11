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

# <a name="understanding-the-nodes-schema-for-mapping-an-existing-web-service-to-odata-through-csdl"></a>Noções básicas sobre o esquema de nós para mapear um serviço web existente para OData por meio de CSDL

>[AZURE.IMPORTANT] **Neste momento, não são mais integração qualquer novas fornecedores de serviço de dados. Novo dataservices não irá obter aprovado para listagem.** Se você tiver um aplicativo de negócios SaaS que você gostaria de publicar no AppSource você pode encontrar mais informações [aqui](https://appsource.microsoft.com/partners). Se você tiver um aplicativos IaaS ou serviço de desenvolvedor que você gostaria de publicar no Azure Marketplace, você pode encontrar mais informações [aqui](https://azure.microsoft.com/marketplace/programs/certified/).

Este documento ajudará a esclarecer a estrutura de nó para mapear um protocolo OData para CSDL. É importante observar que a estrutura de nó é bem formado XML. Portanto esquema raiz, pai e filho é aplicável ao criar seu mapeamento de OData.

## <a name="ignored-elements"></a>Elementos ignorados
Estes são os elementos CSDL alto níveis (nós XML) que não vai ser usado pelo back-end Azure Marketplace durante a importação dos metadados do serviço web. Eles podem estar presentes, mas serão ignorados.

| Elemento | Escopo |
|----|----|
| Usando o elemento | O nó, nós sub e todos os atributos |
| Elemento de documentação | O nó, nós sub e todos os atributos |
| Tipo complexo | O nó, nós sub e todos os atributos |
| Elemento de associação | O nó, nós sub e todos os atributos |
| Propriedade estendida | O nó, nós sub e todos os atributos |
| EntityContainer | Somente os atributos a seguir são ignorados: *estende* e *AssociationSet* |
| Esquema | Somente os atributos a seguir são ignorados: *Namespace* |
| FunctionImport | Somente os atributos a seguir são ignorados: *modo* (o valor padrão de ln é adotada) |
| EntityType | Somente os seguintes nós sub são ignorados: *chave* e *PropertyRef* |

A seguir descreve as alterações (adicionado e ignorados elementos) para diversos nós CSDL XML em detalhes.

## <a name="functionimport-node"></a>Nó FunctionImport
Um nó FunctionImport representa uma URL (ponto de entrada) que expõe um serviço para o usuário final. O nó permite que descreve como a URL endereçada, quais parâmetros estão disponíveis para o usuário final e como esses parâmetros são fornecidos.

Detalhes sobre este nó são encontrados no [aqui] [MSDNFunctionImportLink]

[MSDNFunctionImportLink]: (https://msdn.microsoft.com/library/cc716710 (v=vs.100).aspx)

Estes são os atributos adicionais (ou adições atributos) que são expostas pelo nó FunctionImport:

**d:BaseUri** -
modelo o URI para o recurso REST exposto ao Marketplace. Marketplace usa o modelo para construir consultas contra o serviço web REST. O modelo URI contém espaços reservados para os parâmetros na forma de {nome do parâmetro}, onde o nome do parâmetro é o nome do parâmetro. Ex. apiVersion = {apiVersion}.
Parâmetros são permitidos para aparecer como parâmetros URI ou como parte do caminho do URI. No caso da aparência no caminho sempre são obrigatórias (não podem ser marcadas como anulável). *Exemplo:*`d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**Nome** - o nome da função importado.  Não podem ser iguais a outros nomes definidos no CSDL.  Ex. Nome = "GetModelUsageFile"

**EntitySet** *(opcional)* - se a função retornará uma coleção de tipos de entidade, o valor do **EntitySet** deve ser a entidade definir ao qual pertence o conjunto. Caso contrário, o atributo **EntitySet** não deve ser usado. *Exemplo:*`EntitySet="GetUsageStatisticsEntitySet"`

**ReturnType** *(Opcional)* - Especifica o tipo de elementos retornados pelo URI.  Não use esse atributo se a função não retorna um valor. Estes são os tipos suportados:

 - **Conjunto (<Entity type name>)**: especifica uma coleção de tipos de entidade definido. O nome está presente no atributo Name do nó EntityType. Um exemplo é conjunto (WXC. HourlyResult).
 - **Matéria (<mime type>)**: especifica um documento/blob bruto que é retornado para o usuário. Um exemplo é Raw(image/jpeg) outros exemplos:

  - ReturnType="Raw(text/plain)"
  - ReturnType = "conjunto (sálvia. DeleteAllUsageFilesEntity) "*

**d:Paging** - Especifica como paginação é tratada pelo recurso restante. Os valores de parâmetro são usados em curvas braches, por exemplo, página = {$page} & itemsperpage = {$size} as opções disponíveis são:

- **Nenhum:** sem paginação está disponível
- **Ignorar:** paginação é expressa por meio de uma lógica "Ignorar" e "leve" (superior). Ignorar os saltos sobre elementos de M e leve retorna os próximos elementos de N. Valor do parâmetro: $skip
- **Levar:** Leve retorna os próximos elementos de N. Valor do parâmetro: $take
- **PageSize:** paginação é expressa por meio de uma página lógica e o tamanho (itens por página). Página representa a página atual que é retornada. Valor do parâmetro: $page
- **Tamanho:** tamanho representa o número de itens retornados para cada página. Valor do parâmetro: $size

**d:AllowedHttpMethods** *(Opcional)* - Especifica quais verbo é tratado pelo recurso restante. Além disso, restringe verbo aceito para o valor especificado.  Padrão = POST.  *Exemplo:* `d:AllowedHttpMethods="GET"` As opções disponíveis são:

- **Obter:** geralmente usado para retornar dados
- **POST:** geralmente usado para inserir novos dados
- **Colocar:** geralmente é usada para atualizar dados
- **Excluir:** usado para excluir dados

Nós filhos adicionais (não abordadas pela documentação CSDL) dentro do nó FunctionImport são:

**d:RequestBody** *(Opcional)* - o corpo da solicitação é usado para indicar que a solicitação de espera um corpo sejam enviadas. Parâmetros podem ser fornecidos no corpo da solicitação. Eles são expressas colchetes curvas, por exemplo, {nome do parâmetro}. Esses parâmetros são mapeados da entrada do usuário no corpo que é transferido para o serviço do provedor de conteúdo. O elemento requestBody tem um atributo com nome httpMethod. O atributo permite que os dois valores:

- **POST:** Usado se a solicitação é uma POSTAGEM de HTTP
- **Obter:** Usado se a solicitação é um HTTP GET

    Exemplo:

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:namespaces** e **d:Namespace** - este nó descreve os namespaces são definidos no XML que é retornado pela importação de função (ponto de extremidade URI). O XML retornado pelo serviço de back-end pode conter qualquer número de namespaces para diferenciar o conteúdo que será retornado. **Todos esses namespaces, se usado em consultas de XPath d:Map ou d:Match precisam ser listados.** O nó d:Namespaces contém uma conjunto/lista de nós de d:Namespace. Cada um deles lista um namespace usado na resposta do serviço de back-end. Estes são o atributo do nó d:Namespace:

-   **d:Prefix:** O prefixo para o namespace, conforme visto nos resultados XML retornados pelo serviço, por exemplo, f:FirstName, f:LastName, onde f é o prefixo.
- **d:Uri:** O URI completo do namespace usado no documento resultado. Ele representa o valor que o prefixo é resolvido para no tempo de execução.

**d:ErrorHandling** *(Opcional)* - este nó contém condições para tratamento de erros. Cada uma das condições é validada contra o resultado é retornado pelo serviço do provedor de conteúdo. Se uma condição corresponde ao código de erro HTTP sugerido uma mensagem de erro será retornada para o usuário final.

**d:ErrorHandling** *(Opcional)* e **d:Condition** *(opcional)* - um nó de condição contém uma condição que está marcada no resultado retornado pelo serviço do provedor de conteúdo. Estes são os atributos **necessários** :

- **d:Match:** Uma expressão XPath valida se um determinado nó/valor está presente na saída do provedor de conteúdo XML. O XPath é executado em relação a saída e deve retornar true se a condição for uma correspondência ou FALSO caso contrário.
- **d:HttpStatusCode:** O código de status HTTP que deve ser retornado por Marketplace no caso a condição corresponde. Marketplace signalizes erros ao usuário através de códigos de status HTTP. Uma lista de códigos de status HTTP estão disponíveis em http://en.wikipedia.org/wiki/HTTP_status_code
- **d:ErrorMessage:** A mensagem de erro retornado – com o código de status HTTP – para o usuário final. Isso deve ser uma mensagem de erro amigável que não contenha qualquer segredos.

**d:Title** *(Opcional)* - permite descrevendo o título da função. O valor para o título é proveniente

- O atributo de mapa opcional (um xpath) que especifica onde encontrar o título na resposta retornada da solicitação de serviço.
- - Ou - o título especificado como valor do nó.

**d:Rights** *(Opcional)* - os (por exemplo, direitos autorais) de direitos associados a função. O valor para os direitos é proveniente:

- O atributo de mapa opcional (um xpath) que especifica onde localizar os direitos a resposta retornada da solicitação de serviço.
-   - Ou - os direitos especificados como valor do nó.

**d:Description** *(Opcional)* - uma breve descrição para a função. O valor para a descrição é proveniente

- O atributo de mapa opcional (um xpath) que especifica onde encontrar a descrição na resposta retornada da solicitação de serviço.
- - Ou – a descrição especificado como valor do nó.

**d:EmitSelfLink** - *Consulte exemplo acima "FunctionImport paginação através de retornou dados"*

**d:EncodeParameterValue** - extensão opcional ao OData

**d:QueryResourceCost** - extensão opcional ao OData

**d:Map** - extensão opcional ao OData

**d:Headers** - extensão opcional ao OData

**d:Headers** - extensão opcional ao OData

**d:Value** - extensão opcional ao OData

**d:HttpStatusCode** - extensão opcional ao OData

**d:ErrorMessage** - extensão opcional ao OData

## <a name="parameter-node"></a>Nó do parâmetro

Este nó representa um parâmetro exposto como parte do modelo de URI / solicitar corpo que foi especificado no nó FunctionImport.

Uma página de documento muito útil detalhes sobre o nó de "Elemento de parâmetro" é encontrada no [aqui](http://msdn.microsoft.com/library/ee473431.aspx) (Use na lista suspensa de **Versão outros** para selecionar uma versão diferente, se necessário, para exibir a documentação). *Exemplo:*`<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| Atributo de parâmetro | É necessário | Valor |
|----|----|----|
| Nome | Sim | O nome do parâmetro. Diferenciar maiusculas de minúsculas!  Coincidir maiusculas e minúsculas BaseUri. **Exemplo:**`<Property Name="IsDormant" Type="Byte" />` |
| Tipo | Sim | O tipo de parâmetro. O valor deve ser um **EDMSimpleType** ou um tipo complexo que está dentro do escopo do modelo. Para obter mais informações, consulte "tipos de propriedade/parâmetro suportados 6".  (Diferencia maiusculas de minúsculas! Primeiro caractere é maiusculo, rest letras minúsculas.)  Consulte também, [conceitual modelo tipos CSDL ()] [MSDNParameterLink]. **Exemplo:**`<Property Name="LimitedPartnershipID " Type="Int32" />` |
| Modo | Não | **Em**, ou entrada/saída dependendo se o parâmetro é uma entrada, saída ou parâmetro de entrada/saída. (Somente "Em" está disponível no Azure Marketplace.) **Exemplo:**`<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| MaxLength | Não | O comprimento máximo permitido do parâmetro. **Exemplo:**`<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| Precisão | Não | A precisão do parâmetro. **Exemplo:**`<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| Escala | Não | A escala do parâmetro. **Exemplo:**`<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

[MSDNParameterLink]: (http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx)

Estes são os atributos que foram adicionados a especificação de CSDL:

| Atributo de parâmetro | Descrição |
|----|----|
| **d:Regex** *(Opcional)* | Uma instrução de regex usada para validar o valor de entrada para o parâmetro. Se o valor de entrada não corresponder a instrução o valor será rejeitado. Isso permite para especificar também um conjunto de valores possíveis, por exemplo, ^ [0-9] +? $ para permitir apenas números. **Exemplo:** ' < nome do parâmetro = "nome" modo = "Em" tipo = "Cadeia de caracteres" d: anulável = "false" d:Regex = "^ [a-zA-Z] * $" d:Description = "um nome que não pode conter espaços ou caracteres de inglês não alfabéticos" d:SampleValues = "Jorge|John|Santos|James "/ >' |
| **d:Enum** *(Opcional)* | Uma barra vertical separados lista de valores válidos para o parâmetro. O tipo de valores precisa coincidir com o tipo do parâmetro definido. Exemplo: ' inglês|métrica|bruto`. Enum will display as a selectable dropdown list of parameters in the UI (service explorer). **Example:** `< nome do parâmetro = "Duração" tipo = "Cadeia de caracteres" Mode = "Em" Nullable = "true" d:Enum = "1 ano|5years|10years "/ >' |
| **d: anulável** *(Opcional)* | Permite definir se um parâmetro pode ser nulo. O padrão é: true. No entanto, os parâmetros que são expostos como parte do caminho no modelo de URI não podem ser nulos. Quando o atributo é definido como false para esses parâmetros – a entrada do usuário é substituída. **Exemplo:**`<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *(Opcional)* | Um valor de amostra para exibir como uma anotação para o cliente na interface de usuário.  É possível adicionar vários valores usando uma lista de barra vertical separado, ou seja ' um|b|c` **Example:** `< nome do parâmetro = "BikeOwner" Type = "Cadeia de caracteres" Mode = "Em" d:SampleValues = "Jorge|John|Santos|James "/ >' |

## <a name="entitytype-node"></a>Nó de EntityType

Este nó representa um dos tipos de retornadas do Marketplace para o usuário final. Ele também contém o mapeamento da saída retornado pelo serviço do provedor de conteúdo para os valores que são retornados para o usuário final.

Detalhes sobre este nó são encontrados no [aqui](http://msdn.microsoft.com/library/bb399206.aspx) (Use na lista suspensa de **Versão outros** para selecionar uma versão diferente, se necessário, para exibir a documentação).

| Nome do atributo | É necessário | Valor |
|----|----|----|
| Nome | Sim | O nome do tipo da entidade. **Exemplo:**`<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType | Não | O nome do outro tipo de entidade que é o tipo base do tipo de entidade que está sendo definido. **Exemplo:**`<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

Estes são os atributos que foram adicionados a especificação de CSDL:

**d:Map** - uma expressão XPath executada a saída de serviço. O pressuposto é de que a saída de serviço contém um conjunto de elementos que se repetem, como um ATOM feed onde não há um conjunto de nós de entrada que se repetem. Cada um de nós de repetição contém um registro. O XPath é então especificado para apontar para o nó de repetição individual no resultado de serviço do provedor de conteúdo que contém os valores para um registro individual. Exemplo de saída do serviço:

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

A expressão XPath deve ser /foo/barra porque cada da barra de nó é o nó de repetição na saída e contém o conteúdo real que é retornado para o usuário final.

**Chave** - esse atributo será ignorado pelo Marketplace. RESTANTE com base em serviços web, em geral não expõem uma chave primária.


## <a name="property-node"></a>Nó de propriedade

Este nó contém uma propriedade do registro.

Detalhes sobre este nó são encontrados no [http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx) (Use na lista suspensa de **Versão outros** para selecionar uma versão diferente, se necessário, para exibir a documentação). *Exemplo:*
        `<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name"   Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
        ...
        </EntityType>`

| AttributeName | Necessário | Valor |
|----|----|----|
| Nome | Sim | O nome da propriedade. |
| Tipo | Sim | O tipo do valor da propriedade. O tipo de valor de propriedade deve ser um **EDMSimpleType** ou um tipo complexo (indicado por um nome totalmente qualificado) que se encontra no escopo do modelo. Para obter mais informações, consulte tipos de modelo conceitual (CSDL). |
| Anulável | Não | **True** (o valor padrão) ou **Falso** dependendo se a propriedade pode ter um valor nulo. Observação: A versão do CSDL indicado pelo namespace [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm) , uma propriedade de tipo complexo deve ter Nullable = "False". |
| DefaultValue | Não | O valor padrão da propriedade. |
|MaxLength | Não | O comprimento máximo do valor da propriedade. |
| FixedLength | Não | **Verdadeiro** ou **Falso** dependendo se o valor da propriedade será armazenado como uma cadeia de comprimento de fiexed. |
| Precisão | Não | Refere-se ao número máximo de dígitos para reter no valor numérico. |
| Escala | Não | Número máximo de casas decimais para manter o valor numérico. |
| Unicode | Não | **Verdadeiro** ou **Falso** dependendo se o valor da propriedade ser armazenado como uma cadeia de caracteres Unicode. |
| Agrupamento | Não | Uma cadeia de caracteres que especifica a sequência de agrupamento a ser usado na fonte de dados. |
| ConcurrencyMode | Não | **Nenhum** (o valor padrão) ou **fixo**. Se o valor for definido como **fixo**, o valor da propriedade será usado em verificações de concorrência otimista. |

Estes são os atributos adicionais que foram adicionados a especificação de CSDL:

**d:Map** - a expressão XPath executada no serviço de saída e extrai uma propriedade de saída. O XPath especificado é relativo o nó repetitivo que foi selecionado no XPath do nó EntityType. Também é possível especificar um XPath absoluto para permitir incluindo um recurso estático em cada um de nós de saída, como por exemplo uma declaração de copyright que é encontrada apenas uma vez no serviço original de saída, mas deve estar presente em cada uma das linhas na saída do OData. Exemplo do serviço:

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

A expressão XPath aqui seria ./bar/baz0 para obter o nó baz0 de serviço do provedor de conteúdo.

**d:CharMaxLength** - tipo de cadeia de caracteres, você pode especificar o comprimento máximo. Consulte exemplo DataService CSDL

**d:IsPrimaryKey** - indica se a coluna é a chave primária na tabela/exibição. Consulte exemplo CSDL DataService.

**d:isExposed** - determina se o esquema de tabela é exposto (geralmente verdadeiro). Consulte exemplo DataService CSDL

**d:IsView** *(Opcional)* - true se baseia-se em um modo de exibição em vez de uma tabela.  Consulte exemplo DataService CSDL

**d:Tableschema** - consulte DataService CSDL exemplo

**d:ColumnName** - é o nome da coluna na tabela/exibição.  Consulte exemplo DataService CSDL

**d:IsReturned** - é o booliano que determina se o serviço expõe esse valor para o cliente.  Consulte exemplo DataService CSDL

**d:IsQueryable** - é o booliano que determina se a coluna pode ser usada em uma consulta de banco de dados.   Consulte exemplo DataService CSDL

**d:OrdinalPosition** - é a posição da coluna numérica da aparência, x, na tabela ou modo de exibição, onde x é de 1 até o número de colunas na tabela.  Consulte exemplo DataService CSDL

**d:DatabaseDataType** - é o tipo de dados da coluna no banco de dados, ou seja, o tipo de dados do SQL. Consulte exemplo DataService CSDL

## <a name="supported-parametersproperty-types"></a>Tipos de propriedade/parâmetros com suporte
A seguir estão os tipos suportados para parâmetros e propriedades. (Diferencia maiusculas de minúsculas)

| Tipos primitivos | Descrição |
|----|----|
| Nulo | Representa a ausência de um valor |
| Booliano | Representa o conceito de matemático da lógica com valor binário|
| Byte | Valor inteiro de 8 bits não assinado|
|DateTime| Representa a data e hora com valores desde meia-noite de 12:00:00, 1 de janeiro de 1753 A.D. por meio de 11:59:59 p. m, dezembro de 9999 A.D.|
|Decimal | Representa valores numéricos com precisão e escala fixas. Esse tipo pode descrever um valor numérico que varia de 10 negativo ^ 255 + 1 a 10 positivo ^-255 1|
| Duplo | Representa um número com precisão de 15 dígitos que pode representar valores com intervalo aproximado de ± 2, 23E-308 até ± 1, 79E de ponto flutuante +308. **Use Decimal devido a problema de exportação Exel**|
| Único | Representa um número com precisão de 7 dígitos que pode representar valores com intervalo aproximado de ± 1, 18E-38 até ± 3, 40E de ponto flutuante +38|
|GUID |Representa um valor de identificador exclusivo do 16 bytes (128 bits) |
|Int16|Representa um valor inteiro de 16 bits |
|Int32|Representa um valor inteiro de 32 bits |
|Int64|Representa um valor inteiro de 64 bits |
|Cadeia de caracteres | Representa dados de caractere fixa ou variável-comprimento |


## <a name="see-also"></a>Consulte também
- Se você estiver interessado em entender o processo de mapeamento de OData e a finalidade geral, leia este artigo [Mapeamento de OData de serviço de dados](marketplace-publishing-data-service-creation-odata-mapping.md) para revisar definições, estruturas e instruções.
- Se você estiver interessado em revisando exemplos, leia este artigo [Exemplos de mapeamento do dados serviço OData](marketplace-publishing-data-service-creation-odata-mapping-examples.md) vir o código de amostra e entender contexto e sintaxe do código.
- Para retornar ao caminho indicado para publicação de um serviço de dados do Azure Marketplace, leia este artigo [Guia de publicação de serviço de dados](marketplace-publishing-data-service-creation.md).
