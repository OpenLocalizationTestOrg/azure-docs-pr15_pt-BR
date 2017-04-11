<properties
    pageTitle="Azure AD Cordova Introdução | Microsoft Azure"
    description="Como criar um aplicativo de Cordova que se integra ao Azure AD para entrar e chama Azure AD protegido APIs usando OAuth."
    services="active-directory"
    documentationCenter=""
    authors="vibronet"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="vittorib"/>

# <a name="integrate-azure-ad-with-an-apache-cordova-app"></a>Integrar o Azure AD com um Apache Cordova aplicativo

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Apache Cordova possibilita para desenvolver aplicativos HTML5/JavaScript que podem ser executados em dispositivos móveis como aplicativos nativos completos.
Com o Azure AD, você pode adicionar recursos de autenticação de nível empresarial aos seus aplicativos de Cordova. Graças a um plug-in Cordova disposição SDKs nativos do Azure AD IOS, Android, Windows Store e do Windows Phone, você pode aprimorar seu aplicativo suporta sinal com contas do AD de seus usuários, obter acesso ao Office 365 e API do Azure e até mesmo proteger chamadas para seu próprio API Web personalizado.

Neste tutorial, usaremos o plug-in Apache Cordova para o Active Directory autenticação biblioteca (ADAL) para melhorar a um aplicativo simple com os seguintes recursos:

-   Com apenas algumas linhas de código, autenticar um usuário do AD e obter um símbolo para chamar a API do Azure AD Graph.
-   Usar esse token para chamar a API do gráfico para consultar o diretório e exibir os resultados  
-   Aproveite o cache de token ADAL para minimizar as solicitações de autenticação do usuário.

Para fazer isso, você precisará:

2. Registrar um aplicativo com o Azure AD
2. Adicionar código ao seu aplicativo para solicitar tokens
3. Adicionar código para usar o token para consultar a API do gráfico e exibir os resultados.
4. Crie o projeto de implantação Cordova com todas as plataformas que você deseja destinar e o plug-in Cordova ADAL e testar a solução no emuladores.

## <a name="0--prerequisites"></a>*0. pré-requisitos de*

Para concluir este tutorial, que você precisará:

- Um locatário do Azure AD em que você tiver uma conta com direitos de desenvolvimento de aplicativo
- Um ambiente de desenvolvimento configurado para usar Apache Cordova  

Se você já tiver ambos configurar, prossiga diretamente para a etapa 1.

Se você não tiver um locatário do Azure AD, você pode encontrar [instruções sobre como obter uma aqui](active-directory-howto-tenant.md).

Se você não tiver Apache Cordova configurar em seu computador, instale o seguinte:

- [Gito](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [NodeJS](https://nodejs.org/download/)
- [Cordova CLI](https://cordova.apache.org/) (pode ser facilmente instalado via Gerenciador de pacote NPM: `npm install -g cordova`)

Observe que aqueles devem funcionar no PC e no Mac.

Cada plataforma de destino tem pré-requisitos diferentes.

- Para criar e executar a versão de aplicativo de telefone ou Tablet/PC com Windows
    - [Visual Studio de 2013 para Windows com atualização 2 ou posterior](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express ou outra versão).
- Para criar e executar para iOS
    -   Xcode 5. x ou superior. Baixe-o no http://developer.apple.com/downloads ou o [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12)
    -   [ios-sim](https://www.npmjs.org/package/ios-sim) – permite que você iniciar aplicativos do iOS iOS Simulator da linha de comando (pode ser facilmente instalado via terminal: `npm install -g ios-sim`)

- Para criar e executar o aplicativo para Android
    - Instale o [Kit de desenvolvimento Java (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou posterior. Verifique se `JAVA_HOME` (variável de ambiente) está definida corretamente de acordo com o caminho de instalação do JDK (por exemplo C:\Program Files\Java\jdk1.7.0_75).
    - Instalar o [SDK do Android](http://developer.android.com/sdk/installing/index.html?pkg=tools) e adicionar `<android-sdk-location>\tools` local (por exemplo, C:\tools\Android\android-sdk\tools) para sua `PATH` variável de ambiente.
    - Abrir o Gerenciador de SDK do Android (por exemplo, via terminal: `android`) e instalar
    - SDK da plataforma *5.0.1 Android (API 21)*
    - *Ferramentas de compilação do android SDK* versão 19.1.0 ou superior
    - *Repositório de suporte Android* (Extras)

  Android sdk não oferece qualquer instância de emulador padrão. Criar um novo executando `android avd` do terminal e, em seguida, selecionando *criar …* se desejar executar o aplicativo do Android no emulador. Recomendado *Nível Api* é 19 ou superior, consulte [AVD Manager] (http://developer.android.com/tools/help/avd-manager.html) para obter mais informações sobre as opções de criação e emulador Android.


## <a name="1--register-an-application-with-azure-ad"></a>*1. registrar um aplicativo com o Azure AD*

Observação: esse __passo é opcional__. O tutorial fornecido previamente provisionados valores que permitirá que você veja a amostra em ação sem fazer qualquer provisionamento no seu próprio locatário. No entanto é recomendável que você executar esta etapa e familiarize-se com o processo, como ele será necessário quando você irá criar seus próprios aplicativos.

Azure AD só será emitir tokens para aplicativos conhecidos. Antes de poder usar Azure AD de seu aplicativo, você precisa criar uma entrada para ele no seu locatário.  Para registrar um novo aplicativo em seu locatário,

-   Entrar no [Portal de gerenciamento do Azure](https://manage.windowsazure.com)
-   No painel de navegação à esquerda, clique no **Active Directory**
-   Selecione o locatário onde você deseja registrar o aplicativo.
-   Clique na guia **aplicativos** e clique em **Adicionar** na parte inferior registradora.
-   Siga os avisos e criar um novo **Aplicativo cliente nativo** (apesar do fato de que Cordova aplicativos são baseado em HTML, estamos criando aplicativo de cliente nativo aqui tão `Native Client Application` opção deve ser selecionada; caso contrário, o aplicativo não funciona).
    -   O **nome** do aplicativo descreverá seu aplicativo para usuários finais
    -   O **URI redirecionar** é o URI usado para retornar tokens para seu aplicativo. Insira `http://MyDirectorySearcherApp`.

Após concluir registro, AAD atribuirá seu aplicativo um identificador de cliente exclusivo.  Você precisará desse valor nas próximas seções: você pode encontrá-lo na guia **Configurar** do aplicativo recém-criado.

Para executar `DirSearchClient Sample`, conceder a permissão de aplicativo recém-criado para consultar a _API do Azure AD gráfico_:
-   Na guia **Configurar** , localize a seção "Permissões para outros aplicativos".  Para o aplicativo "Azure Active Directory", adicione a permissão de **acesso do diretório como o usuário conectado** em **Delegada permissões**.  Isso permitirá que seu aplicativo para consultar a API de gráfico para os usuários.

## <a name="2-clone-the-sample-app-repository-required-for-the-tutorial"></a>*2. clonar o repositório de aplicativo de amostra necessário para o tutorial*

Do shell ou linha de comando, digite o seguinte comando:

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="3-create-the-cordova-app"></a>*3. criar o aplicativo de Cordova*

Há várias maneiras de criar aplicativos de Cordova. Neste tutorial, usaremos a Cordova comando interface de linha ().
Do shell ou linha de comando, digite o seguinte comando:


     cordova create DirSearchClient --copy-from="NativeClient-MultiTarget-Cordova/DirSearchClient"

Que criará a estrutura da pasta e estrutura para o projeto Cordova, copie o conteúdo do projeto starter na subpasta www.
Mover para a nova pasta de DirSearchClient.

    cd .\DirSearchClient

Adicione o plug-in branca, necessário para chamar a API do gráfico.

     cordova plugin add cordova-plugin-whitelist

Em seguida, adicione todas as plataformas que você deseja dar suporte. Para ter uma amostra de trabalho, você precisará executar pelo menos um dos comandos a seguir. Observe que você não será capaz de emular iOS no Windows ou Windows/Windows Phone em um Mac.

    cordova platform add android
    cordova platform add ios
    cordova platform add windows

Por fim, você pode adicionar o ADAL para plug-in Cordova ao seu projeto.

    cordova plugin add cordova-plugin-ms-adal

## <a name="3-add-code-to-authenticate-users-and-obtain-tokens-from-aad"></a>*3. adicionar código para autenticar usuários e obter tokens de AAD*

O aplicativo que você está desenvolvendo neste tutorial fornecerá um recurso de pesquisa de diretório bone vazio, onde o usuário final pode digitar o alias do qualquer usuário no diretório e visualizar alguns atributos básicos.  O projeto starter contém a definição da interface do usuário básica do aplicativo (em www/index.html) e a estrutura que fios evento de aplicativo básico ciclos, ligações da interface de usuário e resultados lógica de exibição (em www/js/index.js). A única coisa check-out para você é adicionar a lógica de implementação de tarefas de identidade.

A primeira coisa que você precisa fazer é apresentar no seu código os valores de protocolo que são usados pelo AAD para identificar seu aplicativo e os recursos que você direcione. Esses valores serão usados para construir as solicitações de token posteriormente. Inserir o trecho abaixo na parte superior do arquivo js.

```javascript
    var authority = "https://login.windows.net/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

O `redirectUri` e `clientId` valores devem corresponder aos valores descrevendo seu aplicativo no AAD. Você pode encontrar os da guia Configurar no portal do Azure, conforme descrito na etapa 1 anteriormente neste tutorial.
Observação: se você tiver optado por não registrar um novo aplicativo em seu próprio locatário, você pode simplesmente colar os valores pré-configurado acima como está - que permitirá que você veja a execução de amostra, embora você sempre deve criar sua própria entrada para seus aplicativos são destinados a produção.

Em seguida, precisamos adicionar o código de solicitação de token real. Inserir o trecho a seguir entre o `search `e `renderdata `definições.

```javascript
    // Shows user authentication dialog if required.
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Vamos examinar essa função dividindo-lo em suas duas partes principais.
Este exemplo é projetado para funcionar com qualquer locatário, ao contrário para ser vinculados a uma determinada. Ele usa da empresa "/ comuns", que permite ao usuário inserir qualquer conta no momento da autenticação e direciona a solicitação para o locatário pertence.
Nesta primeira parte do método analisa o cache de ADAL para ver se já existe um token armazenado - e, se houver, ele usa os locatários que ele veio para reinicializar em ADAL. Isso é necessário para evitar prompts extras, como o uso de "/ comuns" sempre resulta em solicitando que o usuário insira uma nova conta.
```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
A segunda parte do método executa a solicitação de tokewn adequada.
O `acquireTokenSilentAsync` método pede para ADAL para retornar um símbolo para o recurso especificado sem mostrar qualquer UX. Que pode acontecer se o cache já tem um token de acesso adequado armazenado, ou se houver um token de atualização que pode ser usado para obter um novo token de acesso sem shwoing qualquer prompt.
Se essa tentativa falhar, vamos voltar `acquireTokenAsync` -qual visivelmente solicitará que o usuário se autenticar.
```javascript
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
Agora que temos o token, podemos finalmente invocar a API de gráfico e executar a consulta de pesquisa que queremos. Inserir o trecho a seguir logo abaixo do `authenticate` definição.

```javascript
// Makes Api call to receive user list.
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
Os arquivos de ponto de partida fornecido um barebone UX para inserir o alias de um usuário em uma caixa de texto. Este método usa esse valor para construir uma consulta, combine com o token de acesso, envie-o para o gráfico e analisar os resultados. O método renderData, já presente no arquivo de ponto de partida, cuida visualizar os resultados.

## <a name="4-run"></a>*4. executar*
O aplicativo está finalmente pronto para executar! Operar-é muito simple: depois que o aplicativo for iniciado, digite na caixa de texto o alias do usuário que você deseja pesquisar - e clique no botão. Você será solicitado para autenticação. Durante a autenticação bem-sucedida e pesquisa bem-sucedida, os atributos do usuário pesquisado serão exibidos. Executa subsequentes executará a pesquisa sem mostrar qualquer prompt, graças à presença no cache do token adquirido anteriormente.
As etapas de concretas para executando o aplicativo variam de plataforma.

####<a name="windows-10"></a>Windows 10:

   Tablet PC:`cordova run windows --archs=x64 -- --appx=uap`

   Celular (requer o dispositivo móvel Windows10 conectado ao PC):`cordova run windows --archs=arm -- --appx=uap --phone`

   __Observação__: durante a primeira execução, você pode ser solicitado a entrar para uma licença de desenvolvedor. Consulte [licença de desenvolvedor](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx) para obter mais detalhes.

####<a name="windows-81-tabletpc"></a>Tablet/PC com Windows 8.1:

   `cordova run windows`

   __Observação__: durante a primeira execução, você pode ser solicitado a entrar para uma licença de desenvolvedor. Consulte [licença de desenvolvedor](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx) para obter mais detalhes.

####<a name="windows-phone-81"></a>Windows Phone 8.1:

   Para executar em dispositivo conectado:`cordova run windows --device -- --phone`

   Para executar em emulador padrão:`cordova emulate windows -- --phone`

   Use `cordova run windows --list -- --phone` para ver todos os destinos disponíveis e `cordova run windows --target=<target_name> -- --phone` para executar o aplicativo no dispositivo específico ou emulador (por exemplo, `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).

####<a name="android"></a>Android:

   Para executar em dispositivo conectado:`cordova run android --device`

   Para executar em emulador padrão:`cordova emulate android`

   __Observação__: Verifique se você criou instância emulador usando o *Gerenciador de AVD* como ele é mostrado na seção *pré-requisitos* .

   Use `cordova run android --list` para ver todos os destinos disponíveis e `cordova run android --target=<target_name>` para executar o aplicativo no dispositivo específico ou emulador (por exemplo, `cordova run android --target="Nexus4_emulator"`).

####<a name="ios"></a>iOS:

   Para executar em dispositivo conectado:`cordova run ios --device`

   Para executar em emulador padrão:`cordova emulate ios`

   __Observação__: Verifique se você tem `ios-sim` pacote instalado para ser executado no emulador. Consulte a seção *pré-requisitos* para obter mais detalhes.

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run application on specific device or emulator (for example,  `cordova run android --target="iPhone-6"`).

Use `cordova run --help` para ver a compilação adicional e opções de execução.

Para referência, o exemplo concluído (sem os valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient).  Agora você pode mover-se em cenários mais avançados de (okey e mais interessante).  Talvez você queira experimentar:

[Proteger uma Web Node API com Azure AD >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
