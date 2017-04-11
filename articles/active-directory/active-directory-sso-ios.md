<properties
    pageTitle="Como habilitar o SSO entre aplicativo IOS usando ADAL | Microsoft Azure"
    description="Como usar os recursos do SDK ADAL para habilitar o logon único em seus aplicativos. "
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>


# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Como habilitar o SSO entre aplicativo IOS usando ADAL


Fornecendo Single Sign-On (SSO) para que os usuários somente precisarão digitar suas credenciais uma vez e ter as credenciais automaticamente trabalhar em aplicativos agora é esperada pelo clientes. A dificuldade em inserir seu nome de usuário e senha em uma tela pequena, geralmente vezes combinados com um fator adicional (2FA) como uma chamada telefônica ou um código texted, os resultados em insatisfação rápida se um usuário tem que fazer isso mais de uma vez para o seu produto.

Além disso, se você utilizar uma plataforma de identidade que outros aplicativos podem usar como Accounts da Microsoft ou uma conta de trabalho do Office 365, os clientes esperam que as credenciais para estar disponível para uso em todos os seus aplicativos não importa o fornecedor.

A plataforma do Microsoft Identity, juntamente com nossos SDKs de identidade da Microsoft, faz todo esse trabalho para você e fornece a capacidade de agradar seus clientes com SSO dentro de seu próprio conjunto de aplicativos ou, como com nossa capacidade de agente autenticador aplicativos e, em todo o dispositivo.

Este passo a passo mostra como configurar nosso SDK dentro de seu aplicativo para fornecer esse benefício aos seus clientes.

Este passo a passo se aplica a:

* Active Directory do Azure
* B2C do Active Directory do Azure
* B2B do Active Directory do Azure
* Acesso condicional do Active Directory do Azure


Observe que o documento abaixo supõe que você tem conhecimento de como [provisionar aplicativos no portal do legados do Azure Active Directory](./develop/active-directory-how-to-integrate.md) , bem como foram integrados seu aplicativo com o [Microsoft identidade iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Conceitos SSO na plataforma de identidade da Microsoft

### <a name="microsoft-identity-brokers"></a>Agentes de identidade da Microsoft

Microsoft fornece aplicativos para cada plataforma móvel que permitem a ponte de credenciais nos aplicativos de fornecedores diferentes bem como permite recursos aprimorados especiais que exigem um único lugar seguro de onde validar credenciais. Chamamos esses **agentes**. No iOS e Android, eles são fornecidos por meio de aplicativos para download que clientes instalar independentemente ou podem ser enviados ao dispositivo por uma empresa que gerencia alguns ou todos do dispositivo para seus funcionários. Esses agentes de suportam o gerenciamento de segurança apenas para alguns aplicativos ou todo o dispositivo com base no que os administradores de TI desejam. No Windows, essa funcionalidade é fornecida por um seletor de conta criada para o sistema operacional, conhecido tecnicamente como o agente de autenticação da Web.

Para entender como podemos usar esses agentes e como seus clientes poderão vê-los no seu fluxo de logon para a plataforma do Microsoft Identity continue lendo para obter mais informações.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Padrões para efetuar login em dispositivos móveis

Acesso a credenciais em dispositivos execute dois padrões básicas para a plataforma do Microsoft Identity:

* Agente de não assistido logon
* Atuar como intermediário de logon assistido

#### <a name="non-broker-assisted-logins"></a>Agente de não assistido logon

Agente de não assistido logon é experiências de logon que acontecem alinhado com o aplicativo e usar o armazenamento local no dispositivo para esse aplicativo. Esse armazenamento pode ser compartilhado entre aplicativos, mas as credenciais são rigorosamente para o aplicativo ou o pacote de aplicativos usando essa credencial. Esta é a experiência que provavelmente já passou em muitos aplicativos móveis onde você insere um nome de usuário e senha dentro do próprio aplicativo.

Estes inícios de sessão têm os seguintes benefícios:

-  Experiência do usuário existe inteiramente dentro do aplicativo.
-  Credenciais podem ser compartilhadas em aplicativos que são assinados pelo mesmo certificado, fornecendo uma experiência de logon único no seu pacote de aplicativos.
-  Controle ao redor de experiência de registro em log no é fornecido para o aplicativo antes e depois de entrar.

Estes inícios de sessão têm as seguintes desvantagens:

- Não é possível experiência de usuário logon único em entre todos os aplicativos que usam um Microsoft Identity, somente entre esses Identities Microsoft seu aplicativo possui e configurou.
- Seu aplicativo não pode ser usado com recursos mais avançados de negócios como acesso condicional ou use o pacote InTune de produtos.
- Seu aplicativo não oferece suporte a autenticação de certificado baseado para usuários de negócios.

Aqui está uma representação de como o SDK de identidade do Microsoft funcionam com o armazenamento de seus aplicativos para habilitar SSO compartilhado:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| Azure SDK  | | Azure SDK  |  | Azure SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Atuar como intermediário de logon assistido

Logon assistido agente é experiências de logon que ocorrem dentro do aplicativo de agente e usam o armazenamento e a segurança do agente para compartilhar credenciais em todos os aplicativos do dispositivo que utilizam a plataforma do Microsoft Identity. Isso significa que seus aplicativos depender o corretor para entrar usuários. No iOS e Android, eles são fornecidos por meio de aplicativos para download que clientes instalar independentemente ou podem ser enviados ao dispositivo por uma empresa que gerencia o dispositivo para seus usuários. Um exemplo desse tipo de aplicativo é o aplicativo do Azure autenticador IOS. No Windows, essa funcionalidade é fornecida por um seletor de conta criada para o sistema operacional, conhecido tecnicamente como o agente de autenticação da Web.
A experiência varia de plataforma e às vezes, pode ser prejudicial aos usuários se não gerenciado corretamente. Você provavelmente está mais familiarizado com esse padrão se você tiver o aplicativo do Facebook instalado e usa o recurso de logon do Facebook no outro aplicativo. A plataforma do Microsoft Identity utiliza o mesmo padrão.

Para iOS que isso leva a uma transição de"" animação onde seu aplicativo é enviado para o plano de fundo enquanto os aplicativos do Azure autenticador vem em primeiro plano para o usuário selecionar a conta que gostaria de entrar.  

Para o Android e Windows o seletor de conta é exibido na parte superior de seu aplicativo que é menos interrupções ao usuário.

#### <a name="how-the-broker-gets-invoked"></a>Como o corretor obtém chamado

Se um agente compatível estiver instalado no dispositivo, como o aplicativo do Azure autenticador, o SDK de identidade do Microsoft fará automaticamente o trabalho de chamar o corretor para você quando um usuário indica que desejam faça logon usando qualquer conta da plataforma do Microsoft Identity. Isso pode ser uma Account da Microsoft, um trabalho ou conta de escola, ou uma conta que você fornecer e host no Azure usando nossos produtos B2C e B2B. Usando criptografia e algoritmos altamente seguros garantimos que as credenciais estão for solicitadas e entregue para o seu aplicativo de maneira segura. Os detalhes técnicos exato desses mecanismos não foi publicado, mas foram desenvolvidos com colaboração Apple e Google.

**O desenvolvedor tem a opção de se o SDK de identidade do Microsoft chama o corretor ou usa o fluxo de assistido não agente.** No entanto se o desenvolvedor optar por não usar o fluxo assistido agente eles perdem a vantagem de aproveitar credenciais SSO que o usuário já tenha adicionado no dispositivo, bem como impede que o aplicativo está sendo usada com recursos de business que Microsoft oferece a seus clientes como o Access condicional, recursos de gerenciamento de Intune, e autenticação baseada em certificado.

Estes inícios de sessão têm os seguintes benefícios:

-  Usuário experiências SSO todos os seus aplicativos não importa o fornecedor.
-  Seu aplicativo pode aproveitar os recursos mais avançados de negócios como acesso condicional ou usar o pacote de InTune de produtos.
-  Seu aplicativo pode oferecer suporte a autenticação de certificado baseado para usuários de negócios.
- Experiência de entrada muito mais segura como a identidade do aplicativo e o usuário são verificados pelo aplicativo agente com algoritmos de segurança adicional e criptografia.

Estes inícios de sessão têm as seguintes desvantagens:

- No iOS o usuário é que fizeram a transição fora experiência do seu aplicativo enquanto credenciais são escolhidas.
- Perda da capacidade de gerenciar a experiência de logon para seus clientes dentro de seu aplicativo.



Aqui está uma representação de como o SDK de identidade do Microsoft funcionam com os aplicativos do agente para habilitar o SSO:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

Contando com essas informações de plano de fundo que você deve ser capaz de entender melhor e implementar SSO dentro de seu aplicativo usando a plataforma do Microsoft Identity e SDKs.


## <a name="enabling-cross-app-sso-using-adal"></a>Habilitando o SSO entre-app usando ADAL

Aqui, usaremos o iOS ADAL SDK para:

- Ativar o agente de não assistido SSO para seu pacote de aplicativos
- Ativar o suporte para SSO assistido agente

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Ativando o SSO do agente de não assistido SSO

Para não agente assistido SSO entre aplicativos SDK de identidade do Microsoft gerenciar muito da complexidade do SSO para você. Isso inclui localizando o usuário correto no cache e manter uma lista de usuários conectados para consultar.

Para habilitar o SSO entre aplicativos pertence a que você precisa fazer o seguinte:

1. Garantir que todos os seus usuários de aplicativos mesmo ID do cliente ou ID do aplicativo.
* Certifique-se de que todos os aplicativos compartilham o mesmo certificado de assinatura da Apple para que você possa compartilhar keychains
* Solicite a mesma qualificação de chaves para cada um dos seus aplicativos.
* Informe SDK de identidade do Microsoft sobre o repositório de chaves compartilhado que deseja usar.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Usando a mesma identificação de cliente / ID de aplicativo para todos os aplicativos em seu pacote de aplicativos

Ordem da plataforma do Microsoft Identity saber que ela tem permissão para compartilhar tokens entre seus aplicativos, cada um dos seus aplicativos precisam compartilhar a mesma ID do cliente ou ID do aplicativo. Este é o identificador exclusivo que foi fornecido a você quando você registrou seu primeiro aplicativo no portal.

Você pode estar se perguntando como você vai identificar diferentes aplicativos para o serviço do Microsoft Identity se ele usa a mesma ID do aplicativo. A resposta é com os **URIs redirecionar**. Cada aplicativo pode ter vários URIs redirecionar registrado no portal de integração. Cada aplicativo no seu pacote terá um URI de redirecionamento diferentes. Um exemplo de como isso parece é abaixo:

Redirecione App1 URI:`x-msauth-mytestiosapp://com.myapp.mytestapp`

Redirecione App2 URI:`x-msauth-mytestiosapp://com.myapp.mytestapp2`

O URI de redirecionamento de App3:`x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Estes são aninhadas a mesma identificação de cliente / ID do aplicativo e pesquisada com base em redirecionar URI retornar em sua configuração de SDK.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```


*Observe que o formato desses URIs redirecionar são explicados abaixo. Você pode usar qualquer URI redirecionar, a menos que você deseja dar suporte o corretor, caso em que eles devem ter uma aparência semelhante acima*



#### <a name="create-keychain-sharing-between-applications"></a>Criar compartilhamento entre aplicativos do repositório de chaves

Ativar o compartilhamento de chaves está além do escopo deste documento e cobertas pela Apple no seu documento [Adicionando recursos](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). O que é importante é que você decide o que suas chaves a ser chamado e adicionar esse recurso em todos os seus aplicativos.

Quando você tem direitos configurar corretamente, você devem ver um arquivo no seu diretório de projeto intitulado `entitlements.plist` que contiver algo parecido com o seguinte:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Depois que você tiver o direito de chaves habilitado em cada um dos seus aplicativos, e você está pronto para usar o SSO, informar o SDK de identidade da Microsoft sobre suas chaves, usando a seguinte configuração no seu `ADAuthenticationSettings` com a seguinte configuração:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [AZURE.WARNING]
Quando você compartilha um repositório de chaves em seus aplicativos de qualquer aplicativo pode excluir usuários ou pior excluir todos os tokens através do aplicativo. Isso é particularmente desastroso se você tiver aplicativos que dependem os tokens de trabalho de plano de fundo. Compartilhando um repositório de chaves significa que você deve ser muito cuidado em todos e quaisquer remove operações através do SDK de identidade do Microsoft.

Pronto! O SDK do Microsoft identidade agora irá compartilhar credenciais em todos os seus aplicativos. A lista de usuários também será compartilhada nas instâncias do aplicativo.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Ativando o SSO do agente assistido SSO

A capacidade de um aplicativo para usar qualquer corretor que está instalado no dispositivo está **desativado por padrão**. Para poder usar o seu aplicativo com o agente, você deve fazer algumas configurações adicionais e adicionar algum código ao seu aplicativo.

As etapas a seguir são:

1. Habilite o modo de agente em chamada do código do seu aplicativo para o SDK do MS.
2. Estabelecer um novo URI de redirecionamento e forneça que para o aplicativo e o seu registro de aplicativo.
3. Registrando um esquema de URL.
4. Suporte de iOS9: adicionar uma permissão para o seu arquivo de info.plist.


#### <a name="step-1-enable-broker-mode-in-your-application"></a>Etapa 1: Habilitar o modo de agente em seu aplicativo
A capacidade de seu aplicativo para usar o corretor é ativada quando você cria o "contexto" ou a configuração inicial do seu objeto de autenticação. Você pode fazer isso definindo seu tipo de credenciais no seu código:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
O `AD_CREDENTIALS_AUTO` configuração permitirá o SDK de identidade do Microsoft tentar chamar a atenção para o corretor, `AD_CREDENTIALS_EMBEDDED` impedirá que o SDK de identidade do Microsoft está ligando para o corretor.

#### <a name="step-2-registering-a-url-scheme"></a>Etapa 2: Registrar um esquema de URL
A plataforma do Microsoft Identity usa URLs para chamar o corretor e devolver o controle ao seu aplicativo. Para concluir essa viagem, é necessário um esquema de URL registrado para seu aplicativo que a plataforma do Microsoft Identity saberá sobre. Isso pode ser além de quaisquer outros esquemas de aplicativo que você pode ter anteriormente registrado com seu aplicativo.

> [AZURE.WARNING]
É recomendável fazer o esquema URL ímpar para minimizar as chances de outro aplicativo usando o mesmo esquema de URL. Apple não aplicar a exclusividade dos esquemas de URL que são registrados na loja de aplicativos.

Abaixo está um exemplo de como isso é exibido na configuração do projeto. Você pode também fazer isso no XCode assim:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Etapa 3: Estabeleça um novo redirecionamento URI com o seu esquema de URL

Para garantir que estamos sempre retornar os tokens de credencial para o aplicativo correto, precisamos certificar-se de que chamamos volta ao seu aplicativo de forma que o sistema operacional de iOS pode verificar. O sistema operacional de iOS relatórios para os aplicativos de agente Microsoft a ID de pacote do aplicativo chamando-lo. Isso não pode ser falsos por um aplicativo autorizado. Portanto, podemos aproveitar isso junto com o URI do nosso aplicativo agente para garantir que os tokens são retornados para o aplicativo correto. Solicitamos que você estabelecer esse redirecionamento exclusivo URI ambas as em seu aplicativo e definir como um URI redirecionar no nosso portal do desenvolvedor.

O URI de redirecionamento deve ser no formato correto de:

`<app-scheme>://<your.bundle.id>`

ex: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Esse URI redirecionar precisa ser especificado em seu registro de aplicativo usando o [portal de clássico Azure](https://manage.windowsazure.com/). Para obter mais informações sobre o registro de aplicativo do Azure AD, consulte [integrando com o Active Directory do Azure](./develop/active-directory-how-to-integrate.md).


##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Etapa 3a: adicionar um URI de redirecionamento em seu portal de aplicativo e desenvolvimento para oferecer suporte à autenticação de certificado baseado

Para dar suporte ao certificado autenticação com base em que um segundo "msauth" precisa ser registrado no seu aplicativo e o [Azure portal clássico](https://manage.windowsazure.com/) para lidar com a autenticação de certificado se quiser adicionar esse suporte em seu aplicativo.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

ex: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*


#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>Etapa 4: iOS9: adicionar um parâmetro de configuração para o seu aplicativo

ADAL usa – canOpenURL: para verificar se o agente está instalado no dispositivo. No iOS Apple 9 bloqueado para podem consultar esquemas de um aplicativo. Você precisará adicionar "msauth" para a seção LSApplicationQueriesSchemes da sua `info.plist file`.

<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>

### <a name="youve-configured-sso"></a>Você configurou o SSO!

Agora o SDK de identidade do Microsoft automaticamente tanto compartilhar credenciais em seus aplicativos e invocar o corretor se ele estiver presente em seu dispositivo.
