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

# <a name="mapping-an-existing-web-service-to-odata-through-csdl"></a>Mapear um serviço web existente para OData por meio de CSDL

>[AZURE.IMPORTANT] **Neste momento, não são mais integração qualquer novas fornecedores de serviço de dados. Novo dataservices não irá obter aprovado para listagem.** Se você tiver um aplicativo de negócios SaaS que você gostaria de publicar no AppSource você pode encontrar mais informações [aqui](https://appsource.microsoft.com/partners). Se você tiver um aplicativos IaaS ou serviço de desenvolvedor que você gostaria de publicar no Azure Marketplace, você pode encontrar mais informações [aqui](https://azure.microsoft.com/marketplace/programs/certified/).

Este artigo fornece uma visão geral sobre como usar um CSDL mapear um serviço existente para um serviço compatível OData. Explica como para criar o documento de mapeamento (CSDL) que transforma a solicitação de entrada do cliente por meio de uma chamada de serviço e a saída (dados) de volta para o cliente por meio de um compatível de OData feed. Azure Marketplace do Microsoft expõe serviços para os usuários finais usando o protocolo OData. Os serviços que são expostos por provedores de conteúdo (proprietários de dados) são expostos em uma variedade de formulários, como o resto, SOAP, etc.

## <a name="what-is-a-csdl-and-its-structure"></a>O que é um CSDL e sua estrutura?
Um CSDL (linguagem de definição de esquema conceitual) é uma especificação que define como descrever o serviço da web ou serviço de banco de dados em comum argumentação XML para o Azure Marketplace.

Visão geral de Simple do **Solicitar fluxo:**

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

O **fluxo de dados** é na direção oposta:

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**Figura 1** diagramas como um cliente seria obter dados de um provedor de conteúdo (seu serviço) através do Azure Marketplace.  O CSDL é usado pelo componente/transformação de mapeamento para lidar com a solicitação e passam dados entre os serviços do provedor de conteúdo e o cliente solicitando.

*Figura 1: Fluxo detalhado do cliente solicitando para provedor de conteúdo por meio do Azure Marketplace*

  ![desenho](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

Para o plano de fundo em Atom, Atom Pub e o protocolo OData na qual as extensões do Azure Marketplace construir, examine: [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

Trecho acima link:     *"a finalidade do protocolo dados abertos (agora em diante referido como OData) é fornecer um protocolo baseado em REST para operações de estilo CRUD (criar, ler, atualizar e excluir) em relação a recursos expostos como serviços de dados. Um "serviço de dados" é um ponto de extremidade onde não há dados expostos de um ou mais "coleções" cada com zero ou mais "entradas", que consistem digitado pares de valor nomeado. OData é publicado pela Microsoft em padrões OASIS (organização para o avanço dos estruturados padrões informações) para que qualquer pessoa que quer possa criar servidores, clientes ou ferramentas sem royalties ou restrições."*

### <a name="three-critical-pieces-that-have-to-be-defined-by-the-csdl-are"></a>Três partes essenciais que precisam ser definidas por CSDL são:

- O **ponto de extremidade** do serviço provedor The Web endereço URI () do serviço
- Os **parâmetros de dados** que estão sendo passados como entrada para o provedor de serviços as definições dos parâmetros sendo enviado ao serviço do provedor de conteúdo para baixo até o tipo de dados.
- **Esquema** de dados sendo retornados ao serviço solicitando o esquema de dados sejam entregues pelo serviço do provedor de conteúdo, incluindo contêiner, conjuntos/tabelas, variáveis/colunas e tipos de dados.

O diagrama a seguir mostra uma visão geral do fluxo de onde o cliente insere a instrução de OData (chamada ao serviço da web do provedor de conteúdo) para obter os resultados/dados de volta.

  ![desenho](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### <a name="steps"></a>Etapas:

1. Cliente envia a solicitação por meio de chamada de serviço completo com parâmetros de entrada definidos no XML do Azure Marketplace
2. CSDL é usado para validar a chamada de serviço.
    - A chamada de serviço formatado é enviada para o serviço de provedores de conteúdo do Azure Marketplace
3. O Serviçoweb executa e valida ou não a ação do verbo Http (isto é, GET) os dados são retornados ao Azure Marketplace onde os dados solicitados (se houver) são expõe em formato XML para o cliente usando o mapeamento definido no CSDL.
4. O cliente é enviado os dados (se houver) em formato XML ou JSON

## <a name="definitions"></a>Definições

### <a name="odata-atom-pub"></a>Pub ATOM OData

Definir uma extensão para o pub ATOM onde cada entrada representa uma linha de um resultado. A parte do conteúdo da entrada é aprimorada para contêm os valores da linha – como pares de valor de chave. Mais informações é encontradas aqui: [https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### <a name="csdl---conceptual-schema-definition-language"></a>CSDL - linguagem de definição de esquema conceitual

Permite a definição de funções (SPROCs) e entidades que são expostas por meio de um banco de dados. Mais informações encontradas aqui: [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)  

> [AZURE.TIP] Clique na lista suspensa de **outras versões** e selecione uma versão, se você não vir o artigo.

### <a name="edm---entry-data-model"></a>EDM - modelo de dados de entrada
- Visão geral: [http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx][OverviewLink] [OverviewLink]: http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx
- Visualização: [http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx][PreviewLink] [PreviewLink]: http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx
- Tipos de dados: [http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx][DataTypesLink] [DataTypesLink]: http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx

A seguir mostra a detalhadas esquerda para direita flua de onde o cliente insere a instrução de OData (chamada ao serviço da web do provedor de conteúdo) para obter os resultados/dados de volta:

  ![desenho](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)


## <a name="csdl-basics"></a>Noções básicas CSDL

Um CSDL (linguagem de definição de esquema conceitual) é uma especificação que define como descrever o serviço da web ou serviço de banco de dados em comum argumentação XML para o Azure Marketplace. CSDL descreve crítica peças que **possibilita passagem de dados da fonte de dados para o Azure Marketplace.** As partes principais são descritas aqui:

- Informações de interface descrever todas as funções disponíveis publicamente (FunctionImport nó)
- Informações de tipo de dados para todas as mensagens requests(input) e responses(outputs) de mensagem (EntitySet/EntityContainer/EntityType nós)
- Informações sobre o protocolo de transporte sejam de vinculação usado (nó de cabeçalho)
- Informações de endereço para localizar o serviço especificado (BaseURI atributo)

Em resumo, o CSDL representa um contrato independente de plataforma e idioma entre o solicitante de serviço e o provedor de serviço. Usando o CSDL, um cliente pode localizar um serviço de banco de dados/serviço da web e invocar qualquer uma das suas funções disponíveis publicamente.

### <a name="relating-a-csdl-to-a-database-or-a-collection"></a>Relação entre um CSDL de um banco de dados ou uma coleção
**A especificação de CSDL**

CSDL é gramática XML para descrever um serviço web. A especificação em si é dividida em 4 elementos principais: EntitySet, FunctionImport; NameSpace e EntityType.

Para fazer essa abstração mais fácil de compreender permite relacionar um CSDL a uma tabela.

Lembre-se de que;

  CSDL representa um contrato independente de plataforma e idioma entre o **solicitante de serviço** e o **provedor de serviço**. Usando CSDL, um **cliente** pode localizar um **serviço de banco de dados/serviço web** e chamar disponível publicamente **funções.**

Para um serviço de dados as quatro partes de um CSDL podem ser consideradas em termos de um banco de dados, tabela, coluna e procedimento de armazenamento.

Relação entre eles da seguinte maneira para um serviço de dados:

- EntityContainer ~ = banco de dados
- EntitySet ~ = tabela
- EntityType ~ = colunas
- FunctionImport ~ = procedimento armazenado

**Verbos HTTP permitidos**
- OBTER – retorna valores a partir do BD (retorna um conjunto)
- POSTAGEM – usada para passar dados e valores de retorno opcionais de banco de dados (criar uma nova entrada na coleção, id/URI retorno)
- Excluir – exclui dados de banco de dados (exclui um conjunto)
- COLOCAR – atualizar dados em um banco de dados (substituir um conjunto ou crie uma)

## <a name="metadatamapping-document"></a>Documento de metadados/mapeamento

O documento de metadados/mapeamento é usado para mapear um conteúdo existente da web serviços de provedor para que ele pode ser exposto como um serviço da web de OData pelo sistema Azure Marketplace. Ele se baseia CSDL e implementa algumas extensões CSDL para acomodar as necessidades do REST com base em serviços web expostos por meio do Azure Marketplace. As extensões são encontradas no namespace [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04) .

Segue um exemplo do CSDL: (copiar e colar o exemplo CSDL em um editor de XML e alterar para corresponder ao seu serviço abaixo.  Em seguida, cole CSDL mapeando DataService guia ao criar seu serviço no [Portal de publicação do Azure Marketplace](https://publish.windowsazure.com)).

**Termos:** Relação entre os termos CSDL com os termos de interface do usuário (PPUI) do [Portal de publicação](https://publish.windowsazure.com) .
- Oferecer "Título" no PPUI se relaciona a MyWebOffer
- MyCompany na PPUI se relacionam ao **Nome de exibição do Publisher** no [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) interface do usuário
- Sua API se relaciona a uma Web ou um serviço de dados (um plano na PPUI)

**Hierarquia:** 
 uma empresa (provedor de conteúdo) possui ofertas que têm planos, serviços ou seja, qual linha para cima com uma API.

### <a name="webservice-csdl-example"></a>Exemplo de Serviçoweb CSDL

Conecta-se a um serviço que é expor um ponto de extremidade do aplicativo da web (como um aplicativo c#)

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
            <!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

            <!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID" d:IsPrimaryKey="True" Type="Int32"  Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount" Type="Double"   Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"   Type="String"   Nullable="false" d:Map="./City"/>
        <Property Name="State"  Type="String"   Nullable="false" d:Map="./State"/>
        <Property Name="Zip"    Type="Int32"    Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"    Type="DateTime" Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [AZURE.TIP] Visualizar mais exemplos de serviço da Web CSDL no artigo [exemplos de mapeamento de um serviço web existente ao OData por meio de CSDLs](marketplace-publishing-data-service-creation-odata-mapping-examples.md)

###<a name="dataservice-csdl-example"></a>Exemplo de DataService CSDL

Conecta-se a um serviço que é expor uma tabela de banco de dados ou o modo de exibição, como um ponto de extremidade exemplo abaixo mostra duas APIs para o banco de dados com base em API CSDL (pode usar modos de exibição em vez de tabelas).

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
            Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
            Map is the schema.tabel or schema.view
            dals.TableName is the table Name
            Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
            dals:IsExposed determines if the table schema is exposed (generally true).
            dals:IsView (optional) true if this is based on a view rather than a table
            dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
            dals:ColumnName is the name of the column in the table /view.
            Type is the emd.SimpleType
            Nullable determines if NULL is a valid output value
            dals.CharMaxLenght is the maximum length of the output value
            Name is the name of the Property and is exposed to the client facing UI
            dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
            IsQueryable is the Boolean that determines if the column can be used in a database query
            (For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
            dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
            dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## <a name="see-also"></a>Consulte também
- Se você estiver interessado em aprender Compreendendo os nós específicos e seus parâmetros, leia este artigo [Nós de mapeamento do dados serviço OData](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) para definições e explicações, exemplos e usar contexto maiusculas.
- Se você estiver interessado em revisando exemplos, leia este artigo [Exemplos de mapeamento do dados serviço OData](marketplace-publishing-data-service-creation-odata-mapping-examples.md) vir o código de amostra e entender contexto e sintaxe do código.
- Para retornar ao caminho indicado para publicação de um serviço de dados do Azure Marketplace, leia este artigo [Guia de publicação de serviço de dados](marketplace-publishing-data-service-creation.md).
