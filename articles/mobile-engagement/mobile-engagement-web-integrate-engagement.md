<properties
    pageTitle="Integração com o SDK do Mobile contrato Web Azure | Microsoft Azure"
    description="As últimas atualizações e procedimentos para o SDK do Azure Mobile contrato da Web"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="02/29/2016"
    ms.author="piyushjo" />

#<a name="integrate-azure-mobile-engagement-in-a-web-application"></a>Integrar o contrato do Azure Mobile em um aplicativo web

> [AZURE.SELECTOR]
- [Universal do Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Os procedimentos neste artigo descrevem a maneira mais simples para ativar a análise e funções de monitoramento em contrato do Azure Mobile em seu aplicativo web.

Siga as etapas para ativar os relatórios de log que são necessários para calcular todas as estatísticas sobre usuários, sessões, atividades, falhas e technicals. Para estatísticas dependem de aplicativos, como eventos, erros e trabalhos, você deve ativar relatórios de log manualmente usando a API de contrato do Azure Mobile. Para obter mais informações, Aprenda [a usar o contrato de celular avançado marcação API em um aplicativo web](mobile-engagement-web-use-engagement-api.md).

## <a name="introduction"></a>Introdução

[Baixar Web contrato móvel Azure SDK](http://aka.ms/P7b453).
O SDK do Mobile contrato Web é fornecido como um único arquivo JavaScript, azure-engagement.js, o que você precisa incluir em cada página do seu site ou aplicativo da web.

> [AZURE.IMPORTANT] Antes de executar este script, você deve executar um trecho de script ou código que você escreve para configurar o contrato de Mobile para seu aplicativo.

## <a name="browser-compatibility"></a>Compatibilidade de navegador

O SDK do Mobile contrato Web usa JSON nativo codificar e decodificar, além de solicitações de AJAX entre domínios (depender a especificação W3C CORS). É compatível com os seguintes navegadores:

* Microsoft Edge 12 +
* Internet Explorer 10 +
* Firefox 3.5 +
* Chrome 4 e posteriores
* Safari 6 +
* Opera 12 +

## <a name="configure-mobile-engagement"></a>Configurar contrato móvel

Escrever um script que cria um global `azureEngagement` objeto JavaScript, como no exemplo a seguir. Porque seu site pode ter páginas de múltiplos, este exemplo supõe que esse script está incluído em cada página. Neste exemplo, o objeto JavaScript é nomeado `azure-engagement-conf.js`.

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

O `connectionString` valor para seu aplicativo é exibido no portal do Azure.

> [AZURE.NOTE] `appVersionName`e `appVersionCode` são opcionais. No entanto, recomendamos que você configurá-los para que a análise possa processar informações de versão.

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>Incluir scripts de contrato Mobile em suas páginas
Adicione scripts de contrato de celular às suas páginas de uma das seguintes maneiras:

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

Ou esta:

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias

Após o script Mobile contrato Web SDK é carregado, ele cria o alias de **contrato** para acessar as APIs SDK. Você não pode usar este alias para definir a configuração de SDK. Este alias é usado como uma referência nesta documentação.

Observe que se o alias padrão em conflito com outra variável global da sua página, você pode redefini-lo na configuração da seguinte maneira antes de carregar o SDK do Mobile contrato da Web:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>Relatórios básicos

Relatórios básicos em Mobile contrato abrange estatísticas de nível de sessão, como estatísticas sobre usuários, sessões, atividades e travamentos.

### <a name="session-tracking"></a>Controle de sessão

Uma sessão de contrato Mobile é dividida em uma sequência de atividades, cada um identificado por um nome.

Em um site clássico, recomendamos que você declare uma atividade diferente em cada página do seu site. Para um aplicativo web ou site em que a página atual nunca muda, talvez você queira controlar as atividades em uma escala menor, como dentro da página.

De qualquer forma, para iniciar ou alterar a atividade de usuário atual, chame o `engagement.agent.startActivity` função. Por exemplo:

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

O servidor de contrato Mobile automaticamente termina uma sessão aberta em três minutos após a página do aplicativo estiver fechada.

Como alternativa, você pode encerrar uma sessão manualmente chamando `engagement.agent.endActivity`. Isso define a atividade de usuário atual para 'Ocioso'.  A sessão terminará 10 segundos mais tarde, a menos que uma nova chamada para `engagement.agent.startActivity` continua a sessão.

Você pode configurar o atraso de 10 segundos no objeto de contrato global, da seguinte maneira:

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [AZURE.NOTE] Não é possível usar `engagement.agent.endActivity` no `onunload` retorno de chamada porque você não pode fazer chamadas de AJAX neste estágio.

## <a name="advanced-reporting"></a>Relatórios avançados

Opcionalmente, se você quiser relatar trabalhos, erros e eventos específicos do aplicativo, você precisa usar a API de contrato Mobile. Você acessa a API de contrato Mobile por meio do `engagement.agent` objeto.

Você pode acessar todos os recursos avançados no contrato de celular na API do contrato Mobile. A API é detalhada no artigo [como usar o contrato de celular avançado marcação API em um aplicativo web](mobile-engagement-web-use-engagement-api.md).

## <a name="customize-the-urls-used-for-ajax-calls"></a>Personalizar as URLs usadas para chamadas de AJAX

Você pode personalizar URLs que usa o SDK do Mobile contrato da Web. Por exemplo, para redefinir a URL de log (o SDK ponto de extremidade para o registro em log), você pode substituir a configuração como esta:

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

Se suas funções de URL retornaram uma cadeia de caracteres que começa com `/`, `//`, `http://`, ou `https://`, o esquema padrão não é usado. Por padrão, o `https://` esquema é usado para esses URLs. Se você deseja personalizar o esquema padrão, substitua a configuração, assim:

    window.azureEngagement = {
      ...
      urls: {
        ...      
        scheme: '//'
      }
    };
