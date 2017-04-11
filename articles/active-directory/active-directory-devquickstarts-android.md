<properties
    pageTitle="Azure AD Android Introdução | Microsoft Azure"
    description="Como criar um aplicativo do Android que se integra ao Azure AD para entrar e chama Azure AD protegido APIs usando OAuth."
    services="active-directory"
    documentationCenter="android"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="integrate-azure-ad-into-an-android-app"></a>Integrar o Azure AD em um aplicativo do Android

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)] 

Se você está desenvolvendo um aplicativo de desktop, Azure AD torna mais simples e direto para você autenticar os usuários usando suas contas do Active Directory.  Ele também permite que seu aplicativo com segurança consumir qualquer web API protegido por Azure AD, como as APIs do Office 365 ou a API do Azure.

Para clientes Android que precisam acessar recursos protegidos, Azure AD fornece a biblioteca de autenticação do Active Directory ou ADAL.  Única finalidade do ADAL na vida é tornar mais fácil para o aplicativo obter tokens de acesso.  Para demonstrar apenas como é fácil, aqui será criamos um aplicativo de lista de tarefas pendentes Android que:

-   Obtém acesso tokens para chamar uma API de lista de tarefas pendentes usando o [protocolo de autenticação de OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Obtém a lista de tarefas pendentes de um usuário
-   Usuários de sinais check-out.

Para começar, você precisará de um locatário do Azure AD na qual você pode criar usuários e registrar um aplicativo.  Se você ainda não tiver um locatário, [Saiba como obter uma](active-directory-howto-tenant.md).

> [AZURE.TIP] Experimente a visualização do nosso novo [portal do desenvolvedor](https://identity.microsoft.com/Docs/Android) que ajudarão você a começar a trabalhar com o Active Directory do Azure em poucos minutos!  O portal do desenvolvedor o orientará durante o processo de registrar um aplicativo e integrar Azure AD seu código.  Quando tiver terminado, você terá um aplicativo simples que pode autenticar os usuários em seu locatário e um back-end que podem aceitar tokens e executar a validação. 

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>Etapa 1: Baixar e executar o servidor de amostra Node REST API TODO

Este exemplo é escrito especificamente para trabalhar em relação a nosso exemplo existente para criar um único locatário API REST de tarefas pendentes para o Microsoft Azure Active Directory. Este é um pré-requisito para o início rápido.

Para obter informações sobre como configurar isto, visite nossos exemplos existentes aqui:

* [Serviço de API do restante do Microsoft Azure Active Directory amostra para Node](active-directory-devquickstarts-webapi-nodejs.md)

## <a name="step-2-register-your-web-api-with-your-microsoft-azure-ad-tenant"></a>Etapa 2: Registrar sua Web API com seu locatário do AD do Microsoft Azure

**O que estou fazendo?**

*Microsoft Active Directory dá suporte à adição de dois tipos de aplicativos. Web APIs que oferecem serviços para usuários e aplicativos (ou na web ou um aplicativo em execução em um dispositivo) que acessar essas APIs da Web. Nesta etapa, você está registrando a API Web estiver executando localmente para este exemplo de teste. Normalmente essa API Web seria um serviço REST que está oferecendo funcionalidade que você quiser um aplicativo para acessar. Microsoft Azure Active Directory pode proteger qualquer ponto de extremidade!*

*Aqui estamos supondo estiver registrando API REST TODO mencionado acima, mas isso funciona para qualquer API de Web que você desejaria Azure Active Directory para proteger.*

Etapas para registrar uma API Web com o Microsoft Azure AD

1. Entrar no [portal de gerenciamento do Azure](https://manage.windowsazure.com).
2. Clique em Active Directory na navegação à esquerda
3. Clique em do locatário de diretório onde você deseja registrar o aplicativo de amostra.
4. Clique na guia aplicativos.
5. Na registradora, clique em Adicionar.
6. Clique em "Adicionar um aplicativo minha organização está desenvolvendo".
7. Insira um nome amigável para selecionar aplicativo, por exemplo, "TodoListService", "API Web e/ou de aplicativo da Web" e clique em Avançar.
8. Para a URL de logon, insira a URL base para o exemplo, por padrão `https://localhost:8080`.
9. Para o URI de ID do aplicativo, insira `https://<your_tenant_name>/TodoListService`, substituir `<your_tenant_name>` com o nome do seu locatário do Azure AD.  Clique em Okey para concluir o registro.
10. Enquanto ainda estiver no portal do Azure, clique na guia Configurar do seu aplicativo.
11. **Localizar o valor de ID do cliente e copiá-lo lado**, você precisará isso posteriormente ao configurar seu aplicativo.

## <a name="step-3-register-the-sample-android-native-client-application"></a>Etapa 3: Registrar o aplicativo do Android Native Client de amostra

Registrar seu aplicativo web é a primeira etapa. Em seguida, você precisará informar Azure Active Directory sobre seu aplicativo também. Isso permite que seu aplicativo para se comunicar com a API Web apenas registrado

**O que estou fazendo?**  

*Conforme mencionado acima, Microsoft Azure Active Directory dá suporte à adição de dois tipos de aplicativos. Web APIs que oferecem serviços para usuários e aplicativos (ou na web ou um aplicativo em execução em um dispositivo) que acessar essas APIs da Web. Nesta etapa, você está registrando o aplicativo neste exemplo. Você deve fazer isso em ordem para este aplicativo sejam capazes de solicitação de acesso a API da Web que você acabou de ser registrado. Active Directory do Azure irá se recusar a mesmo permitir que o aplicativo solicitar entrada, a menos que ele está registrado! Que faz parte da segurança do modelo.*

*Aqui estamos supondo estiver registrando este aplicativo de exemplo mencionado acima, mas isso funciona para qualquer aplicativo que você está desenvolvendo.*

**Por que eu estou API Web e um aplicativo em um locatário?**

*Como você poderia esperar, você pode criar um aplicativo que acessa um API externa que está registrado no Active Directory do Azure de outro locatário. Se você fizer isso, seus clientes serão solicitados a consentimento para o uso da API no aplicativo. A parte interessante é, biblioteca de autenticação do Active Directory para iOS cuida desse consentimento para você! Como podemos acessar recursos mais avançados, você verá que isto é uma parte importante do trabalho necessário para acessar o pacote do Microsoft APIs do Azure e Office, bem como qualquer outro provedor de serviço. Por agora, porque você registrou seu Web API e o aplicativo em mesmo locatário, você não verá avisos consentimento. Isso geralmente é o caso, se você está desenvolvendo um aplicativo apenas para sua própria empresa usar.*

1. Entrar no [portal de gerenciamento do Azure](https://manage.windowsazure.com).
2. Clique em Active Directory na navegação à esquerda
3. Clique em do locatário de diretório onde você deseja registrar o aplicativo de amostra.
4. Clique na guia aplicativos.
5. Na registradora, clique em Adicionar.
6. Clique em "Adicionar um aplicativo minha organização está desenvolvendo".
7. Insira um nome amigável para o aplicativo, por exemplo "TodoListClient Android", selecione "cliente aplicativo nativo" e clique em Avançar.
8. Para o URI redirecionar, insira `http://TodoListClient`.  Clique em Concluir.
9. Clique na guia Configurar do aplicativo.
10. Localizar o valor de ID do cliente e copiá-lo à parte, você precisará isso mais tarde quando configurar seu aplicativo.
11. Em "Permissões para outros aplicativos", clique em "Adicionar aplicativo".  Selecione "Outros" no menu suspenso "Mostrar" e clique na marca de seleção superior.  Localize e clique no TodoListService e clique na marca de seleção inferior para adicionar o aplicativo.  Selecione "TodoListService de acesso" no menu suspenso "Delegada permissões" e salvar a configuração.



Para criar com Maven, você pode usar o pom.xml no nível superior

  * Clone este repo em um diretório de sua escolha:

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`  

  * Siga as etapas da [seção de Prerequests configurar sua maven para android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)
  * Configuração emulator com SDK 19
  * Vá para a pasta raiz onde você clonar o repo
  * Execute o comando: instalar o mvn limpa
  * Altere o diretório para a amostra de início rápido: cd samples\hello
  * Execute o comando: mvn android: implantar android: executar
  * Você deve ver iniciando o aplicativo
  * Insira as credenciais de usuário de teste para experimentar!

Pacotes de JAR também serão enviados ao lado do pacote de aar.

### <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>Etapa 4: Baixar o Android ADAL e adicioná-lo ao seu espaço de trabalho do Eclipse

Tornamos mais fácil para você tem várias opções para usar esta biblioteca em seu projeto Android:

* Você pode usar o código-fonte para importar esta biblioteca para Eclipse e o link para o seu aplicativo.
* Se usando Studio Android, você pode usar o formato de pacote *aar* e referenciar os binários.

####<a name="option-1-source-zip"></a>Opção 1: Origem Zip

Para baixar uma cópia do código-fonte, clique em "Baixar ZIP" no lado direito da página ou clique [aqui](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz).

####<a name="option-2-source-via-git"></a>Opção 2: Fonte via gito

Para obter o código-fonte do SDK via gito digite:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

####<a name="option-3-binaries-via-gradle"></a>Opção 3: Binários via Gradle

Você pode acessar os binários de repo central Maven. Pacote de AAR pode ser incluído em seu projeto no AndroidStudio da seguinte maneira:

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

####<a name="option-4-aar-via-maven"></a>Opção 4: aar via Maven

Se você estiver usando o plug-in m2e no Eclipse, você pode especificar a dependência no seu arquivo de pom.xml:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


####<a name="option-5-jar-package-inside-libs-folder"></a>Opção 5: jar pacote dentro de pasta de bibliotecas
Você pode obter o arquivo jar do maven a repo e solte na pasta de *bibliotecas* em seu projeto. Você precisa copiar os recursos necessários para seu projeto, bem como os pacotes de jar não incluam-los.


### <a name="step-5-add-references-to-android-adal-to-your-project"></a>Etapa 5: Adicionar referências para Android ADAL ao seu projeto


2. Adicionar uma referência ao seu projeto e especificá-lo como uma biblioteca Android. Se você não tiver certeza como fazer isso, [Clique aqui para obter mais informações] (http://developer.android.com/tools/projects/projects-eclipse.html)

3. Adicionar a dependência de projeto para depuração em suas configurações de projeto

4. Atualize o arquivo de AndroidManifest.xml do seu projeto para incluir:

    ```Java
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
      ....
      <application/>
    ```

7. Crie uma instância de AuthenticationContext em sua atividade principal. Os detalhes desta chamada estão além do escopo deste arquivo Leiame, mas você pode obter um bom início examinando a [Amostra de cliente nativo Android](https://github.com/AzureADSamples/NativeClient-Android). Abaixo está um exemplo:

    ```Java
    // Authority is in the form of https://login.windows.net/yourtenant.onmicrosoft.com
    mContext = new AuthenticationContext(MainActivity.this, authority, true); // This will use SharedPreferences as            default cache
    ```
  * Observação: mContext é um campo em sua atividade

8. Copie esse bloco de código para manipular o final da AuthenticationActivity depois que o usuário insere as credenciais e recebe o código de autorização:

    ```Java
     @Override
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
         super.onActivityResult(requestCode, resultCode, data);
         if (mContext != null) {
             mContext.onActivityResult(requestCode, resultCode, data);
         }
     }
    ```

9. Para solicitar um token, você define um retorno de chamada

    ```Java
    private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };
    ```
10. Por fim, solicite um token usando o retorno de chamada:

    ```Java
     mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                    callback);
    ```

Explicação dos parâmetros:

  * Recurso é necessário e o recurso que você está tentando acessar.
  * ClientID é necessária e vem a partir do Portal de AzureAD.
  * Você pode configurar redirectUri como seu packagename. Não é necessário para ser fornecido a chamada acquireToken.
  * PromptBehavior ajuda a solicitar credenciais ignorar o cache e cookies.
  * Retorno de chamada será chamado depois que o código de autorização é trocado para um token.

  O retorno de chamada terá um objeto de AuthenticationResult que tem accesstoken, data expirado e informações de idtoken.

Opcional: **acquireTokenSilent**

Você pode chamar **acquireTokenSilent** tratar cache e atualização de token. Ele fornece também a versão de sincronização. Ele aceita ID de usuário como parâmetro.

    ```java
     mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

11. **Agente**: aplicativo de portal do Microsoft Intune empresa fornecerá o componente de agente. ADAL será usar a conta do agente, se houver uma conta de usuário é criada neste autenticador e desenvolvedor optar por não ignorá-la. Desenvolvedor pode ignorar o usuário agente com:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```

 Desenvolvedor precisa registrar redirectUri especial para uso do agente. RedirectUri está no formato de msauth://packagename/Base64UrlencodedSignature. Você pode obter seu redirecturi para seu aplicativo usando o script "brokerRedirectPrint.ps1" ou usar a API chamada mContext.getBrokerRedirectUri. Assinatura está relacionada ao seu certificados de assinatura.

 Modelo do agente atual é de um usuário. AuthenticationContext fornece método de API para obter o usuário do agente.

 ```java
 String brokerAccount =  mContext.getBrokerUser();
 ```
 Usuário do agente será retornado se conta é válida.

 Seu manifesto de aplicativo deve ter permissões para usar contas AccountManager: http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


Usando este passo a passo, você deve ter o que você precisa integrar com êxito ao Azure Active Directory. Para obter mais exemplos disso funcionar, visite o AzureADSamples / repositório no GitHub.

## <a name="important-information"></a>Informações importantes

### <a name="customization"></a>Personalização

Recursos de projeto de biblioteca podem ser substituídos por seus recursos de aplicativo. Isso acontece quando seu aplicativo está criando. Por esse motivo, você pode personalizar layout de atividade de autenticação da maneira desejada. Você precisa certificar-se de manter a identificação dos controles que uses(Webview) ADAL.

### <a name="broker"></a>Agente

Componente do agente serão entregues com o aplicativo de portal do Microsoft Intune da empresa. Conta será criada no Gerenciador de conta. Tipo de conta é "com.microsoft.workaccount". Ele permite apenas única conta SSO. Ele criará cookie SSO para este usuário após concluir o desafio de dispositivo para um dos aplicativos do.

### <a name="authority-url-and-adfs"></a>ADFS e a Url de autoridade

ADFS não é reconhecido como produção STS, então você precisa desativar a descoberta de instância e passar falso no construtor de AuthenticationContext.

Url de autoridade precisa STS instância e o nome do locatário: https://login.windows.net/yourtenant.onmicrosoft.com

### <a name="querying-cache-items"></a>Consultando itens do cache

ADAL fornece cache padrão em SharedPreferences com alguns simple cache funções de consulta. Você pode obter o cache atual da AuthenticationContext com:
```Java
 ITokenCacheStore cache = mContext.getCache();
```
Você também pode fornecer a implementação de cache, se desejar personalizá-lo.
```Java
mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);
```

### <a name="promptbehavior"></a>PromptBehavior

ADAL fornece a opção para especificar o comportamento de aviso. PromptBehavior.Auto surgirá UI se token de atualização é inválido e credenciais de usuário são necessárias. PromptBehavior.Always ignorará o uso de cache e sempre mostrar interface do usuário.

### <a name="silent-token-request-from-cache-and-refresh"></a>Solicitação de token silenciosa do cache e atualização

Esse método não usa pop de interface do usuário para cima e não exige uma atividade. Ela retornará token do cache, se disponível. Se token tiver expirado, ele tentará atualizá-lo. Se o token de atualização está expirado ou falha, ela retornará AuthenticationException.

    ```Java
    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

Você também pode fazer com que o sync chamadas com este método. Você pode definir null para retorno de chamada ou usar acquireTokenSilentSync.

### <a name="diagnostics"></a>Diagnósticos

Estes são as principais fontes de informações para diagnosticar problemas:

+ Exceções
+ Logs
+ Rastreamentos de rede

Além disso, observe que IDs de correlação centrais o diagnóstico na biblioteca. Você pode definir seu correlação IDs em uma base por solicitação se quiser correlação um ADAL solicitação com outras operações em seu código. Se você não definir um id de correlações e ADAL gerará um aleatório um and todas as mensagens de log e chamadas de rede serão marcadas com a id de correlação. O auto gerado alterações de identificação em cada solicitação.

#### <a name="exceptions"></a>Exceções

Obviamente, isso é o diagnóstico primeiro. Tentamos fornecer mensagens de erro úteis. Se você achar que não é útil registre um problema e fale conosco. Forneça também informações de dispositivo como modelo e SDK #.

#### <a name="logs"></a>Logs

Você pode configurar a biblioteca para gerar mensagens de registro que você pode usar para ajudar a diagnosticar problemas. Configurar o log, fazendo a chamada a seguir para configurar um retorno de chamada que ADAL usará para entregar desativar cada mensagem de registro como ele é gerado.


 ```Java
 Logger.getInstance().setExternalLogger(new ILogger() {
     @Override
     public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
      ...
      // You can write this to logfile depending on level or errorcode.
      writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
     }
 }
 ```
Mensagens possam ser gravadas em um arquivo de log personalizado, conforme mostrado abaixo. Infelizmente, não há nenhuma maneira padrão de obter registros em um dispositivo. Há alguns serviços que podem ajudá-lo com isso. Você pode também crie seu próprio, como enviar o arquivo para um servidor.

```Java
private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
}
```

##### <a name="logging-levels"></a>Níveis de log

+ Error(Exceptions)
+ Warn(Warning)
+ Informações (fins)
+ Detalhado (mais detalhes)

Definir o nível de log assim:
```Java
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);
 ```

 Todas as mensagens de log são enviadas para logcat além de qualquer retornos de log personalizado.
Você pode obter o log de logcat de formulário um arquivo como mostrado belog:

 ```
  adb logcat > "C:\logmsg\logfile.txt"
 ```
 Mais exemplos sobre adb comandos: https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat

#### <a name="network-traces"></a>Rastreamentos de rede

Você pode usar várias ferramentas para capturar o tráfego HTTP que gera ADAL.  Isso é mais útil se você estiver familiarizado com o protocolo OAuth ou se você precisar fornecer informações de diagnóstico para Microsoft ou outros canais de suporte.

Fiddler é a ferramenta de rastreamento de HTTP mais fácil.  Use os links a seguir para configurá-lo para cima até o tráfego de rede ADAL registro corretamente.  Para ser útil é necessário configurar fiddler ou qualquer outra ferramenta como Carlos, gravar tráfego SSL não criptografado.  Observação: Rastreamentos gerados desta forma podem conter informações altamente privilegiadas como tokens de acesso, nomes de usuário e senhas.  Se você estiver usando contas de produção, não compartilhe esses rastreamentos com partes 3º.  Se você precisar fornecer um rastreamento para alguém para obter suporte, reproduza o problema com uma conta temporária com nomes de usuário e senhas que você não se importa em compartilhamento.

+ [Configurando Fiddler para Android](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
+ [Configurar regras de Fiddler para ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)


### <a name="dialog-mode"></a>Modo de caixa de diálogo
método de acquireToken sem atividade suporta prompt de diálogo.

### <a name="encryption"></a>Criptografia

ADAL criptografa os tokens e a loja no SharedPreferences por padrão. Você pode examinar a classe StorageHelper para ver os detalhes. Android introduzido AndroidKeyStore para 4.3(API18) armazenamento seguro de chaves particulares. ADAL que usa para API18 e acima. Se você deseja usar ADAL para versões anteriores do SDK, você precisará fornecer chave secreta em AuthenticationSettings.INSTANCE.setSecretKey

### <a name="oauth2-bearer-challenge"></a>Desafio de portador de Oauth2

AuthenticationParameters classe fornece funcionalidade para acessar o authorization_uri de desafio de portador de Oauth2.

### <a name="session-cookies-in-webview"></a>Cookies de sessão na exibição da Web

Exibição da Web Android não limpar cookies de sessão depois que o aplicativo estiver fechado. Você pode manipular isso com código de exemplo abaixo:
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
Mais sobre cookies: http://developer.android.com/reference/android/webkit/CookieSyncManager.html

### <a name="resource-overrides"></a>Substituições de recurso

A biblioteca ADAL inclui cadeias de caracteres em inglês para as seguintes mensagens de ProgressDialog duas.

Seu aplicativo deve substituí-las se cadeias de caracteres localizadas são desejadas.

```Java
<string name="app_loading">Loading...</string>
<string name="broker_processing">Broker is processing</string>
<string name="http_auth_dialog_username">Username</string>
<string name="http_auth_dialog_password">Password</string>
<string name="http_auth_dialog_title">Sign In</string>
<string name="http_auth_dialog_login">Login</string>
<string name="http_auth_dialog_cancel">Cancel</string>
```

=======

### <a name="ntlm-dialog"></a>Caixa de diálogo NTLM
Versão adal 1.1.0 dá suporte a caixa de diálogo NTLM que é processada por meio de evento de onReceivedHttpAuthRequest de WebViewClient. Cadeias de caracteres e layout de diálogo podem ser personalizadas.

### <a name="cross-app-sso"></a>Aplicativo de cruz SSO
Saiba [como habilitar SSO entre-app no Android usando ADAL](active-directory-sso-android.md)  


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
