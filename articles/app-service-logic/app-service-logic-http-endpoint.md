<properties
   pageTitle="Aplicativos de lógica como pontos de extremidade acessível"
   description="Como criar e configurar pontos de extremidade do disparador e usá-los em um aplicativo de lógica no serviço de aplicativo do Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>


# <a name="logic-apps-as-callable-endpoints"></a>Aplicativos de lógica como pontos de extremidade acessível

Lógica de aplicativos nativamente pode expor um ponto de extremidade HTTP síncrono como um disparador.  Você também pode usar o padrão de pontos de extremidade acessível para invocar aplicativos lógica como um fluxo de trabalho aninhado através da ação "fluxo de trabalho" em um aplicativo de lógica.

Existem 3 tipos de gatilhos que podem receber solicitações:

* Solicitação
* ApiConnectionWebhook
* HttpWebhook

Para o restante do artigo, usaremos **solicitação** como exemplo, mas todos os princípios se aplicam idêntico a outros 2 tipos de disparadores.

## <a name="adding-a-trigger-to-your-definition"></a>Adicionando um disparador a sua definição
A primeira etapa é adicionar um disparador a sua definição de aplicativo de lógica que possa receber solicitações de entrada.  Você pode pesquisar no designer para "Solicitação HTTP" adicionar o cartão de disparador. Você pode definir o corpo de uma solicitação JSON esquema e o designer gerará tokens para ajudá-lo a analisar e passar dados de disparador manual no fluxo de trabalho.  É recomendável usando uma ferramenta como [jsonschema.net](http://jsonschema.net) para gerar um esquema JSON de uma carga de corpo de amostra.

![Cartão de gatilho de solicitação][2]

Depois de salvar sua definição de aplicativo de lógica, uma URL de retorno será gerada semelhante a esta:
 
``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
```

Essa URL contém uma chave SAS nos parâmetros de consulta usado para autenticação.

Você também pode chegar a esse ponto de extremidade no portal do Azure:

![][1]

Ou, ligando para:

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

### <a name="security-for-the-trigger-url"></a>Segurança para a URL de disparadores

URLs de retorno de chamada de aplicativo de lógica são gerados com segurança usando uma assinatura de acesso compartilhado.  A assinatura é passada como um parâmetro de consulta e deve ser validada antes do aplicativo de lógica será acionada.  Ele é gerado por meio de uma combinação exclusiva de uma chave secreta por aplicativo lógica, o nome do disparador e a operação que está sendo executada.  A menos que alguém tenha acesso à chave secreta lógica aplicativo, eles não poderão gerar uma assinatura válida.

## <a name="calling-the-logic-app-triggers-endpoint"></a>Chamar o ponto de extremidade do disparador aplicativo lógica

Depois que você criou o ponto de extremidade para o disparador, você poderá dispará-la por meio de um `POST` para a URL completa. Você pode incluir cabeçalhos adicionais e qualquer conteúdo no corpo.

Se o tipo de conteúdo for `application/json` e em seguida, você será capaz de propriedades de referência de dentro da solicitação. Caso contrário, ele será tratado como uma única unidade binária que pode ser passada para outras APIs, mas não pode ser referenciada dentro do fluxo de trabalho sem converter o conteúdo.  Por exemplo, se você passar `application/xml` conteúdo que você poderia usar `@xpath()` fazer uma extração xpath, ou `@json()` para converter de XML para JSON.  Mais informações sobre como trabalhar com conteúdo tipos [podem ser encontradas aqui](app-service-logic-content-type.md)

Além disso, você pode especificar um esquema JSON na definição. Isso faz com que o designer para gerar tokens que você pode passar, em seguida, em etapas.  Por exemplo a seguir se tornará um `title` e `name` disponíveis no designer de token:

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## <a name="referencing-the-content-of-the-incoming-request"></a>Referenciando o conteúdo da solicitação de entrada

O `@triggerOutputs()` função produzirá o conteúdo da solicitação de entrada. Por exemplo, ele teria o seguinte:

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

Você pode usar o `@triggerBody()` atalho para acessar o `body` propriedade especificamente. 

## <a name="responding-to-the-request"></a>Responder à solicitação

Para algumas solicitações de iniciar um aplicativo de lógica, talvez você queira responder com algum conteúdo para o chamador. Há um novo tipo de ação chamado **resposta** que podem ser usados para construir o código de status, corpo e os cabeçalhos de resposta. Observe que se nenhuma forma de **resposta** estiver presente, o ponto de extremidade de aplicativo de lógica será *imediatamente* responde com **202 aceitos**.

![Ação de resposta HTTP][3]

``` json
"Response": {
            "conditions": [],
            "inputs": {
                "body": {
                    "name": "@{triggerBody()['name']}",
                    "title": "@{triggerBody()['title']}"
                },
                "headers": {
                    "content-type": "application/json"
                },
                "statusCode": 200
            },
            "type": "Response"
        }
```

Respostas tem o seguinte:

| Propriedade | Descrição |
| -------- | ----------- |
| statusCode | O código de status HTTP para responder à solicitação de entrada. Pode ser qualquer código de status válido que começa com 2xx, 4xx ou 5xx. códigos de status de 3xx não são permitidos. | 
| corpo | Um objeto de corpo que pode ser uma cadeia de caracteres, um objeto JSON ou conteúdo mesmo binário referenciados a partir de uma etapa anterior. | 
| cabeçalhos | Você pode definir qualquer número de cabeçalhos a ser incluído na resposta | 

Todas as etapas no aplicativo lógica que são necessárias para a resposta devem ser concluído em *60 segundos* para a solicitação original receber a resposta **a menos que o fluxo de trabalho está sendo chamado como um aplicativo de lógica aninhadas**. Se nenhuma ação de resposta é alcançada dentro de 60 segundos, em seguida, a solicitação de entrada irá expirar e recebe uma resposta de **tempo limite de cliente 408** HTTP.  Para aplicativos de lógica aninhados, o pai lógica aplicativo vão continuar a esperar por uma resposta até conclusão, independentemente da quantidade de tempo demora.

## <a name="advanced-endpoint-configuration"></a>Configuração avançada de ponto de extremidade

Aplicativos de lógica tem suporte para o ponto de extremidade de acesso direto embutido e sempre usar o `POST` método para iniciar uma execução do aplicativo lógica. O aplicativo **Ouvinte HTTP** API anteriormente também suporte para alteração segmentos da URL e o método HTTP. Você pode até mesmo configurar segurança adicional ou um domínio personalizado adicionando-o para o host de aplicativo API (o aplicativo da Web que hospedado o aplicativo API). 

Esta funcionalidade está disponível por meio do **gerenciamento de API**:
* [Alterar o método da solicitação](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [Alterar os segmentos de URL da solicitação](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* Configurar seus domínios de gerenciamento de API na guia **Configurar** no portal do Azure clássico
* Configurar política para verificar se há autenticação básica (**link necessário**)

## <a name="summary-of-migration-from-2014-12-01-preview"></a>Resumo de migração de 2014-12-01-visualização

|  2014-12-01-visualização | 2016-06-01 |
|---------------------|--------------------|
| Clique em aplicativo **Ouvinte HTTP** API | Clique em **gatilho Manual** (nenhuma API app obrigatório) |
| Ouvinte HTTP configuração "*envia resposta automaticamente*" | Seja incluir uma ação de **resposta** ou não na definição de fluxo de trabalho |
| Configurar autenticação básica ou OAuth | por meio de gerenciamento de API |
| Configurar o método HTTP | por meio de gerenciamento de API |
| Configurar o caminho relativo | por meio de gerenciamento de API |
| Referência do corpo recebido via`@triggerOutputs().body.Content` | Referência via`@triggerOutputs().body` |
| Ação de **resposta HTTP enviar** no ouvinte HTTP | Clique em **responder a solicitação HTTP** (nenhuma API app obrigatório)


[1]: ./media/app-service-logic-http-endpoint/manualtriggerurl.png
[2]: ./media/app-service-logic-http-endpoint/manualtrigger.png
[3]: ./media/app-service-logic-http-endpoint/response.png
