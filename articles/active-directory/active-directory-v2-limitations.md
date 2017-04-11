<properties
    pageTitle="Limitações de ponto de extremidade de v 2.0 & restrições | Microsoft Azure"
    description="Uma lista das limitações & restrições com o ponto de extremidade do Azure AD v 2.0."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="should-i-use-the-v20-endpoint"></a>Devo usar o ponto de extremidade de v 2.0?

Ao criar aplicativos que se integram ao Azure Active Directory, você precisará decidir se os protocolos de autenticação e de ponto de extremidade de v 2.0 atendem às suas necessidades.  O ponto de extremidade do Azure AD original é ainda totalmente suportado e em alguns aspectos, é mais avançada do recurso que v 2.0.  No entanto, o v 2.0 ponto de extremidade [apresenta benefícios significativos](active-directory-v2-compare.md) para os desenvolvedores que talvez atraia a usar o novo modelo de programação.

Neste momento, nossa recomendação no uso do ponto de extremidade v 2.0 é a seguinte:

- Se você quiser suportar contas pessoais do Microsoft em seu aplicativo, você deve usar o ponto de extremidade de v 2.0.  Mas, certifique-se de compreender as limitações listadas neste artigo, especialmente aqueles relativos especificamente para trabalhar & estudante contas.
- Se seu aplicativo requer apenas trabalho suporte & estudante, você deve usar [os pontos de extremidade do Azure AD originais](active-directory-developers-guide.md).

Ao longo do tempo, o ponto de extremidade de v 2.0 crescerá para eliminar as restrições listadas aqui, para que você só precisará usar o ponto de extremidade de v 2.0.  Enquanto isso, este artigo destina-se para ajudá-lo a determinar se o ponto de extremidade de v 2.0 é para você.  Podemos continuará a atualização deste artigo ao longo do tempo para refletir o estado atual do ponto de extremidade v 2.0, verifique novamente reavalie seus requisitos com os recursos de v 2.0.

Se você tiver um aplicativo existente com o Azure AD que não use o ponto de extremidade de v 2.0, não é necessário começar do zero.  No futuro, podemos fornecerá uma maneira para você ativar seus aplicativos existentes do Azure AD para uso com o ponto de extremidade de v 2.0.

## <a name="restrictions-on-apps"></a>Restrições em aplicativos
Atualmente, os seguintes tipos de aplicativos não são suportados pelo ponto de extremidade v 2.0.  Para obter uma descrição dos tipos de aplicativos com suporte, consulte [Este artigo](active-directory-v2-flows.md).

##### <a name="standalone-web-apis"></a>APIs de Web autônomo
Com o ponto de extremidade de v 2.0, você tem a capacidade de [criar uma API de Web que é protegido usando OAuth 2.0](active-directory-v2-flows.md#web-apis).  No entanto, essa API Web só poderão receber tokens de um aplicativo que compartilha a mesma ID de aplicativo.  Não há suporte para a criação de uma web API acessado a partir de um cliente com uma Id de aplicativo diferente.  Cliente não poderão solicitar ou obter permissões à sua web API.

Para ver como criar uma API de Web que aceita tokens de um cliente com a mesma Id de aplicativo, consulte os exemplos de Web API do ponto de extremidade de v 2.0 na [Introdução](active-directory-appmodel-v2-overview.md#getting-started).

##### <a name="web-api-on-behalf-of-flow"></a>API em nome de fluxo de Web
Arquiteturas de muitos incluem uma API de Web que precisa chamar outra API Web downstream, ambas protegidos pelo ponto de extremidade v 2.0.  Este cenário é comum em clientes nativos que têm um API Web back-end, que por sua vez chama um serviço online da Microsoft ou outro personalizadas incorporada API da web que ofereça suporte Azure AD.

Este cenário pode ser compatíveis com usando a concessão de credencial de portador OAuth 2.0 Jwt, também conhecida como o fluxo de On-Behalf-Of.  No entanto, o fluxo de nome diante de atualmente não há suporte para o ponto de extremidade de v 2.0.  Para ver como esse fluxo funciona no Azure AD disponibilizado serviço, fazer check-out [em nome de código de exemplo no GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## <a name="restrictions-on-app-registrations"></a>Restrições nos registros de aplicativo
Neste momento, todos os aplicativos que deseja integrar com o ponto de extremidade de v 2.0 devem criar um novo registro de aplicativo em [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Qualquer Azure AD existente ou aplicativos do Microsoft Account não será compatíveis com o ponto de extremidade de v 2.0, nem será registrados no qualquer portal além do novo Portal de registro do aplicativo de aplicativos.  Pretendemos oferece uma maneira de habilitar aplicativos existentes para uso como um aplicativo de v 2.0. Neste momento no entanto, há um caminho de migração de um aplicativo para o ponto de extremidade de v 2.0.

Da mesma forma, aplicativos registrados no novo Portal de registro do aplicativo não funcionará contra o ponto de extremidade de autenticação Azure AD original.  Entretanto, você pode usar aplicativos criados no Portal de registro do aplicativo a integração com êxito com o ponto de extremidade de autenticação conta Microsoft, `https://login.live.com`.

Além disso, os registros de aplicativo criados em [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) tem as seguintes condições:

- Não há suporte para a propriedade da **home page** , também conhecido como a **URL de logon** .  Sem uma home page, esses aplicativos não serão exibidos no painel de meus aplicativos do Office.
- Somente dois segredos do aplicativo são permitidos por aplicativo Id neste momento.
- Um registro de aplicativo só pode ser visualizado e gerenciado por uma conta de desenvolvedor única.  Ela não pode ser compartilhada entre vários desenvolvedores.
- Há várias restrições sobre o formato de redirect_uri permitidos.  Consulte a seção a seguir para obter mais detalhes.

## <a name="restrictions-on-redirect-uris"></a>Restrições sobre URIs de redirecionamento
Aplicativos que são registrados no novo Portal de registro de aplicativo estão atualmente restritos a um conjunto limitado de valores de redirect_uri.  O redirect_uri para aplicativos web e serviços deve começar com o esquema de `https`, e todos os valores de redirect_uri devem compartilhar um único domínio DNS.  Por exemplo, não é possível registrar um aplicativo web que tem redirect_uris:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

O sistema de registro compara o nome inteiro do DNS da redirect_uri existente com o nome DNS da redirect_uri que você está adicionando. A solicitação para adicionar o nome DNS falhará se qualquer uma das seguintes condições forem atendida:  

- Se o nome DNS inteiro do redirect_uri novo não coincidir com o nome DNS da redirect_uri existente
- Se o nome DNS inteiro do novo redirect_uri não é um subdomínio do redirect_uri existentes

Por exemplo, se o aplicativo tem atualmente redirect_uri:

`https://login.contoso.com`

Em seguida, é possível adicionar:

`https://login.contoso.com/new`

que corresponda exatamente ao nome DNS, ou:

`https://new.login.contoso.com`

o que é um subdomínio DNS do login.contoso.com.  Se você quiser ter um aplicativo que tem login east.contoso.com e login-west.contoso.com como redirect_uris, em seguida, você deve adicionar o redirect_uris a seguir na ordem:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Os dois últimos podem ser adicionados porque eles são subdomínios de redirect_uri o primeiro, contoso.com. Essa limitação será removida em uma versão futura.

Para saber como registrar um aplicativo no novo Portal de registro do aplicativo, consulte [Este artigo](active-directory-v2-app-registration.md).

## <a name="restrictions-on-services--apis"></a>Restrições sobre APIs & serviços
O ponto de extremidade de v 2.0 atualmente suporta entrar para qualquer aplicativo registrado no novo Portal de registro de aplicativo, desde que ele cai na lista de [fluxos de autenticação com suporte](active-directory-v2-flows.md).  No entanto, esses aplicativos só poderão adquirir tokens de acesso OAuth 2.0 para um conjunto muito limitado de recursos.  O ponto de extremidade de v 2.0 apenas irá emitir access_tokens para:

- O aplicativo que solicitado o token.  Um aplicativo pode adquirir uma access_token para si mesmo, se o aplicativo lógico é composto por vários componentes diferentes ou níveis.  Para ver esse cenário em ação, Confira nossos tutoriais de [Introdução](active-directory-appmodel-v2-overview.md#getting-started) .
- O email do Outlook, calendário e contatos restante APIs, todas elas estão localizadas em https://outlook.office.com.  Para saber como escrever um aplicativo que acessa essas APIs, consulte estes tutoriais do [Guia de Introdução do Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) .
- As APIs do Microsoft Graph.  Para saber mais sobre o Microsoft Graph e todos os dados que está disponíveis, visite [https://graph.microsoft.io](https://graph.microsoft.io).

Nenhum outro serviço é suportado no momento.  Mais serviços Online da Microsoft serão adicionados no futuro, além de suporte para seu próprio personalizado APIs Web criadas e serviços.

## <a name="restrictions-on-libraries--sdks"></a>Restrições em bibliotecas e SDKs
Suporte de biblioteca para o ponto de extremidade de v 2.0 é bastante limitado neste momento.  Se você quiser usar o ponto de extremidade de v 2.0 em um aplicativo de produção, você tem as seguintes opções:

- Se você estiver criando um aplicativo da web, você pode usar com segurança nossa middleware servidor estará disponível para realizar entrar e token de validação.  Eles incluem o middleware OWIN abrir ID se conectar para ASP.NET e nosso plug-in NodeJS Passport.  Exemplos de código usando nosso middleware estão disponíveis em nossa seção de [Introdução](active-directory-appmodel-v2-overview.md#getting-started) .
- Para outras plataformas e aplicativos nativos e móveis, você também pode integrar com o ponto de extremidade de v 2.0 por diretamente enviando e recebendo mensagens de protocolo no seu código do aplicativo.  O v 2.0 OpenID conectar e OAuth protocolos [explicitamente documentadas](active-directory-v2-protocols.md) para ajudá-lo a realizar essa uma integração.
- Finalmente, você pode usar bibliotecas de abrir ID se conectar e OAuth de abrir origem a integração com o ponto de extremidade de v 2.0.  O protocolo v 2.0 deve ser compatível com muitas bibliotecas de protocolo Abrir origem sem alterações importantes.  A disponibilidade de tais bibliotecas varia por idioma e plataforma e os sites [Abertos ID conectar](http://openid.net/connect/) e [OAuth 2.0](http://oauth.net/2/) mantém uma lista de implementações populares. Consulte [bibliotecas de autenticação e v 2.0 do Azure Active Directory (AD)](active-directory-v2-libraries.md) para obter mais detalhes e a lista de bibliotecas de cliente Abrir origem e amostras que foram testadas com o ponto de extremidade de v 2.0.

Nós também ter lançado apenas uma visualização inicial da [Biblioteca de autenticação da Microsoft (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) para .NET.  Você está bem-vindo ao experimentar essa biblioteca em aplicativos cliente e servidor do .NET, mas como uma biblioteca de visualização que não será acompanhado por qualidade GA suporte.

## <a name="restrictions-on-protocols"></a>Restrições sobre protocolos
O ponto de extremidade de v 2.0 suporta apenas abrir ID conectar & OAuth 2.0.  No entanto, nem todos os recursos e as funções de cada protocolo foram incorporadas o ponto de extremidade de v 2.0, incluindo:

- Conectar a OpenID `end_session_endpoint`, que permite que um aplicativo encerrar a sessão do usuário com o ponto de extremidade de v 2.0.
- id_tokens emitido pelo ponto de extremidade de v 2.0 conter apenas um identificador de dupla do usuário.  Isso significa que os dois aplicativos diferentes receberão IDs diferentes para o mesmo usuário.  Observe que, consultando o Microsoft Graph `/me` ponto de extremidade, você poderá obter um correlatable ID do usuário que podem ser usados em aplicativos.
- id_tokens emitido pelo ponto de extremidade de v 2.0 não contêm um `email` reivindicar do usuário neste momento, mesmo se você adquirir a permissão do usuário para exibir seus emails.
- O ponto de extremidade de OpenID conectar informações do usuário. O ponto de extremidade de informações do usuário não está implementado no ponto de extremidade v 2.0 neste momento.  No entanto, todos os dados de perfil de usuário, você deve receber potencialmente neste ponto de extremidade está disponível na Microsoft Graph `/me` ponto de extremidade.
- Função & declarações de grupo.  No momento, o ponto de extremidade de v 2.0 não suporta declarações de função ou grupo emissão em id_tokens.

Para entender melhor o escopo da funcionalidade do protocolo compatível com o ponto de extremidade de v 2.0, leia nosso [OpenID conectar & referência de protocolo OAuth 2.0](active-directory-v2-protocols.md).

## <a name="restrictions-for-work--school-accounts"></a>Restrições para contas de estudante & trabalhar
Há alguns recursos específicos para os usuários de negócios/organização da Microsoft que ainda não são suportados pelo ponto de extremidade v 2.0.

##### <a name="device-based-conditional-access-native-and-mobile-apps-and-the-microsoft-graph"></a>Acesso condicional baseado no dispositivo, aplicativos móveis e nativos e o Microsoft Graph
O ponto de extremidade de v 2.0 ainda não oferece suporte a autenticação do dispositivo para aplicativos móveis e nativos, como aplicativos nativos em execução no iOS ou Android.  Isto pode bloquear seu aplicativo nativo de chamar o Microsoft Graph para determinadas organizações.  Autenticação do dispositivo é necessária quando um administrador define uma política de acesso condicional baseado no dispositivo em um aplicativo.  O ponto de extremidade de v 2.0, o cenário mais provável para acesso condicional baseado no dispositivo é um administrador definindo uma política em um recurso no Microsoft Graph, como a API do Outlook.  Se um administrador define essa política e seu aplicativo nativo solicita um token para o Microsoft Graph, a solicitação basicamente falhará porque a autenticação de dispositivo ainda não é suportada.  Aplicativos da Web que solicitam tokens para o Microsoft Graph, no entanto, há suporte para quando políticas baseadas no dispositivo são configuradas.  Na web autenticação de dispositivo de cenário de aplicativo é realizada por meio do navegador da web do usuário.

Como um desenvolvedor, você provavelmente não tem controle sobre quando diretivas estão definidas em recursos do Microsoft Graph, ou até mesmo ciente quando acontece.  Se você estiver criando um aplicativo para usuários de trabalho e escolar, você deve usar [o ponto de extremidade do Azure AD original](active-directory-developers-guide.md) até que o ponto de extremidade de v 2.0 dá suporte a autenticação do dispositivo.  Para obter mais informações sobre acesso condicional baseado no dispositivo, confira [neste artigo](active-directory-conditional-access.md#device-based-conditional-access).

##### <a name="windows-integrated-authentication-for-federated-tenants"></a>Autenticação integrada do Windows para locatários federados
Se você já utilizou ADAL (e, portanto, o ponto de extremidade do Azure AD original) em aplicativos do Windows, você deve ter deixado aproveitar o que é conhecido como a concessão de declaração SAML.  Este grant permite que usuários do Azure AD federado locatários silenciosamente se autenticar com sua instância do Active Directory no local sem precisar digitar suas credenciais.  A concessão de declaração SAML não é suportada atualmente no ponto de extremidade v 2.0.
