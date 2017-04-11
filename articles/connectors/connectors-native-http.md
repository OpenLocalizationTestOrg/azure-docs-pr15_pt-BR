<properties
    pageTitle="Adicionar a ação de HTTP nos aplicativos de lógica | Microsoft Azure"
    description="Visão geral da ação HTTP com propriedades"
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-http-action"></a>Começar a usar a ação de HTTP

Com a ação de HTTP, você pode estender fluxos de trabalho para a sua organização e se comunicar com qualquer ponto de extremidade sobre HTTP.

É possível:

- Crie fluxos de trabalho de aplicativo que ativar (disparador) quando um site que você gerencia desce uma lógica.
- Comunicar-se a qualquer ponto de extremidade sobre HTTP para estender seus fluxos de trabalho para outros serviços.

Para começar a usar a ação de HTTP em um aplicativo de lógica, consulte [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-http-trigger"></a>Use o disparador HTTP

Um disparador é um evento que pode ser usado para iniciar o fluxo de trabalho que é definido em um aplicativo de lógica. [Saiba mais sobre gatilhos](connectors-overview.md).

Veja aqui uma sequência de exemplo de como configurar o disparador HTTP no Designer de aplicativo de lógica.

1. Adicione o disparador HTTP em seu aplicativo de lógica.
2. Preencha os parâmetros para o ponto de extremidade HTTP que você deseja pesquisar.
3. Modificar o intervalo de recorrência na frequência ele deve pesquisar.
4. O aplicativo de lógica agora aciona com qualquer conteúdo que será retornado durante a verificação de cada.

![Gatilho HTTP](./media/connectors-native-http/using-trigger.png)

### <a name="how-the-http-trigger-works"></a>Como funciona o disparador HTTP

O disparador HTTP fazer uma chamada para um ponto de extremidade HTTP em um intervalo recorrente. Por padrão, qualquer resposta HTTP código menor que 300 resultados em um aplicativo de lógica executar. Você pode adicionar uma condição no modo de exibição de código que irá avaliar após a chamada HTTP para determinar se o aplicativo de lógica deve ser disparada. Aqui está um exemplo de um gatilho HTTP que é acionado sempre que o código de status retornado é maior que ou igual a `400`.

```javascript
"Http":
{
    "conditions": [
        {
            "expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
        }
    ],
    "inputs": {
        "method": "GET",
        "uri": "https://blogs.msdn.microsoft.com/logicapps/",
        "headers": {
            "accept-language": "en"
        }
    },
    "recurrence": {
        "frequency": "Second",
        "interval": 15
    },
    "type": "Http"
}
```

Detalhes completos sobre os parâmetros de gatilho HTTP estão disponíveis no [MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger).

## <a name="use-the-http-action"></a>Usar a ação de HTTP

Uma ação é uma operação que é executada pelo fluxo de trabalho que é definido em um aplicativo de lógica. [Saiba mais sobre ações](connectors-overview.md).

1. Selecione o botão **Nova etapa** .
2. Escolha **Adicionar uma ação**.
3. Na caixa de pesquisa ação, digite **http** para listar a ação de HTTP.

    ![Selecione a ação de HTTP](./media/connectors-native-http/using-action-1.png)

4. Adicione todos os parâmetros que são necessários para a chamada HTTP.

    ![Concluir a ação de HTTP](./media/connectors-native-http/using-action-2.png)

5. Clique no canto superior esquerdo da barra de ferramentas para salvar. Seu aplicativo de lógica será salvar e publicar (Ativar).

## <a name="http-trigger"></a>Gatilho HTTP

Aqui estão os detalhes para o disparador que dá suporte a esse conector. O conector HTTP tem um disparador.

|Disparadores|Descrição|
|---|---|
|HTTP|Fazer uma chamada HTTP e retorna o conteúdo de resposta.|

## <a name="http-action"></a>Ação de HTTP

Aqui estão os detalhes para a ação que suporta esse conector. O conector HTTP tem uma ação possível.

|Ação|Descrição|
|---|---|
|HTTP|Fazer uma chamada HTTP e retorna o conteúdo de resposta.|

## <a name="http-details"></a>Detalhes HTTP

As tabelas a seguir descrevem os campos de entrada obrigatórios e opcionais para a ação e os detalhes de saída correspondentes que estão associados usando a ação.


#### <a name="http-request"></a>Solicitação HTTP
A seguir são campos de entrada para a ação, que faz uma solicitação de saída de HTTP.
R * significa que é um campo obrigatório.

|Nome para exibição|Nome da propriedade|Descrição|
|---|---|---|
|Método *|método|O verbo HTTP usar|
|URI *|URI|O URI para a solicitação HTTP|
|Cabeçalhos|cabeçalhos|Um objeto JSON dos cabeçalhos HTTP para incluir|
|Corpo|corpo|O corpo da solicitação HTTP|
|Autenticação|autenticação|Detalhes na seção [autenticação](#authentication)|
<br>

#### <a name="output-details"></a>Detalhes de saída

Estes são os detalhes de saída para a resposta HTTP.

|Nome da propriedade|Tipo de dados|Descrição|
|---|---|---|
|Cabeçalhos|objeto|Cabeçalhos de resposta|
|Corpo|objeto|Objeto de resposta|
|Código de status|int|Código de status HTTP|

## <a name="authentication"></a>Autenticação

O recurso de lógica aplicativos do serviço de aplicativo do Azure permite que você use diferentes tipos de autenticação contra pontos de extremidade HTTP. Você pode usar essa autenticação com os conectores **HTTP**, **[HTTP + Swagger](./connectors-native-http-swagger.md)**e **[Webhook de HTTP](./connectors-native-webhook.md)** . Os seguintes tipos de autenticação estão configuráveis:

* [Autenticação básica](#basic-authentication)
* [Autenticação de certificado de cliente](#client-certificate-authentication)
* [Autenticação do Active Directory (AD Azure) OAuth Azure](#azure-active-directory-oauth-authentication)

#### <a name="basic-authentication"></a>Autenticação básica

O objeto de autenticação a seguir é necessário para autenticação básica.
R * significa que é um campo obrigatório.

|Nome da propriedade|Tipo de dados|Descrição|
|---|---|---|
|Tipo *|tipo|Tipo de autenticação (deve ser `Basic` para autenticação básica)|
|Nome de usuário *|nome de usuário|Nome de usuário para autenticar|
|Senha *|senha|Senha para autenticação|

>[AZURE.TIP] Se você quiser usar uma senha que não é possível recuperar a definição, use um `securestring` parâmetro e o `@parameters()` [função de definição de fluxo de trabalho](http://aka.ms/logicappdocs).

Portanto, você criaria um objeto assim no campo autenticação:

```javascript
{
    "type": "Basic",
    "username": "user",
    "password": "test"
}
```

#### <a name="client-certificate-authentication"></a>Autenticação de certificado de cliente

O objeto de autenticação a seguir é necessário para autenticação de certificado de cliente. R * significa que é um campo obrigatório.

|Nome da propriedade|Tipo de dados|Descrição|
|---|---|---|
|Tipo *|tipo|O tipo de autenticação (deve ser `ClientCertificate` para os certificados de cliente SSL)|
|PFX *|PFX|O conteúdo codificado na Base 64 do arquivo Personal Information Exchange (PFX)|
|Senha *|senha|A senha para acessar o arquivo PFX|

>[AZURE.TIP] Você pode usar um `securestring` parâmetro e o `@parameters()` a [função de definição de fluxo de trabalho](http://aka.ms/logicappdocs) para usar um parâmetro que não serão legível na definição de depois de salvar o aplicativo de lógica.

Por exemplo:

```javascript
{
    "type": "ClientCertificate",
    "pfx": "aGVsbG8g...d29ybGQ=",
    "password": "@parameters('myPassword')"
}
```

#### <a name="azure-ad-oauth-authentication"></a>Autenticação do Azure AD OAuth

O objeto de autenticação a seguir é necessária para autenticação do Azure AD OAuth. R * significa que é um campo obrigatório.

|Nome da propriedade|Tipo de dados|Descrição|
|---|---|---|
|Tipo *|tipo|O tipo de autenticação (deve ser `ActiveDirectoryOAuth` para Azure AD OAuth)|
|Locatário *|locatário|O identificador de locatário do locatário Azure AD|
|Audiência *|público-alvo|Defina como`https://management.core.windows.net/`|
|Cliente ID *|clientId|O identificador de cliente para o aplicativo do Azure AD|
|Secreta *|segredo|O segredo do cliente que está solicitando o token|

>[AZURE.TIP] Você pode usar um `securestring` parâmetro e o `@parameters()` a [função de definição de fluxo de trabalho](http://aka.ms/logicappdocs) para usar um parâmetro que não serão legível na definição após salvar.

Por exemplo:

```javascript
{
    "type": "ActiveDirectoryOAuth",
    "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "audience": "https://management.core.windows.net/",
    "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
    "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## <a name="next-steps"></a>Próximas etapas

Agora, experimente a plataforma e [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Você pode explorar os outros conectores disponíveis nos aplicativos de lógica examinando nossa [lista APIs](apis-list.md).
