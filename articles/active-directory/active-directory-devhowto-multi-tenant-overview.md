<properties
   pageTitle="Como criar um aplicativo que pode entrar em qualquer usuário do Active Directory do Azure | Microsoft Azure"
   description="Passo a passo instruções para a criação de um aplicativo que podem entrar em um usuário de qualquer locatário do Active Directory do Azure, também conhecido como um aplicativo de vários locatário."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="skwan;bryanla"/>

# <a name="how-to-sign-in-any-azure-active-directory-ad-user-using-the-multi-tenant-application-pattern"></a>Como entrar em qualquer usuário do Azure Active Directory (AD) usando o padrão de locatário vários aplicativos
Se você oferecer um Software como um aplicativo de serviço para muitas organizações, você pode configurar seu aplicativo para aceitar a entrada suplementos de qualquer locatário Azure AD.  No Azure AD isso é chamado fazer seu locatário vários do aplicativo.  Usuários em qualquer locatário Azure AD poderá entrar no seu aplicativo após concordando para usar sua conta com o aplicativo.  

Se você tiver um aplicativo existente que tem seu próprio sistema de conta ou compatível com outros tipos de entrada de outros provedores de nuvem, adicionar entrar do Azure AD de qualquer locatário é tão simple quanto registrar seu aplicativo, adicionando a entrada no código via OAuth2, OpenID conectar ou SAML e colocar uma entrada com o botão Microsoft no seu aplicativo. Clique no botão abaixo para saber mais sobre a marcação de seu aplicativo.

[! [Entrar no botão] [AAD-entrar]][AAD-App-Branding]


Este artigo pressupõe que você já está familiarizado com a criação de um aplicativo de locatário único para Azure AD.  Se você não, cabeça volta para cima até a [home page do desenvolvedor guia] [ AAD-Dev-Guide] e experimente um dos nossos inícios rápidos!

Há quatro etapas simples para converter seu aplicativo em um aplicativo de vários locatário do Azure AD:

1.  Atualizar seu registro de aplicativo para que ele seja vários locatários
2.  Atualizar seu código para enviar solicitações para o /common ponto de extremidade 
3.  Atualizar seu código para lidar com vários valores de emissor
4.  Entender consentimento de usuário e administrador e fazer alterações de código apropriado

Vamos examinar cada etapa detalhadamente. Você também pode saltar direto para [Esta lista de locatários vários exemplos][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Registro de atualização sejam vários locatários
Por padrão, os registros de aplicativo/API web Azure AD são locatário único.  Você pode fazer seu registro vários locatários encontrando a opção "Aplicativo é vários locatários" na página Configuração de seu registro de aplicativo no [Azure portal clássico] [ AZURE-classic-portal] e defini-lo como "Sim".

Observação: Antes de um aplicativo pode ser feito a vários locatário, o Azure AD requer o URI de ID de aplicativo do aplicativo seja exclusivo. O URI de ID de aplicativo é uma das maneiras a que um aplicativo é identificado em mensagens de protocolo.  Para um aplicativo de locatário única, é suficiente para o URI de ID de aplicativo ser exclusivo dentro de locatário.  Para um aplicativo de vários locatário, ele deve ser exclusivo para que Azure AD possa encontrar o aplicativo em todos os locatários.  Exclusividade global é imposta exigindo o URI de ID de aplicativo tenha um nome de host que corresponde a um domínio verificado do locatário Azure AD.  Por exemplo, se o nome do seu locatário fosse contoso.onmicrosoft.com então válido URI de ID de aplicativo seria `https://contoso.onmicrosoft.com/myapp`.  Se seu locatário tinha um domínio verificado do `contoso.com`, e em seguida, um URI de ID de aplicativo válido também seria `https://contoso.com/myapp`.  A configuração de um aplicativo como vários locatários falhará se o URI de ID de aplicativo não siga esse padrão.

Registros de clientes nativos são vários locatários por padrão.  Você não precisa realizar nenhuma ação para fazer um nativo vários locatários do cliente aplicativo registro.

## <a name="update-your-code-to-send-requests-to-common"></a>Atualizar seu código para enviar solicitações para /common
Em um aplicativo de locatário única, entrar solicitações é enviados para o ponto de extremidade de entrada do locatário.   Por exemplo, para contoso.onmicrosoft.com o ponto de extremidade seria:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

Solicitações enviadas ao ponto de extremidade de um locatário podem entrar em usuários (ou convidados) locatário aos aplicativos no locatário.  Com um aplicativo de vários locatário, o aplicativo não sabe antecipadamente quais locatário o usuário é de, para que você não consegue enviar solicitações de ponto de extremidade de um locatário.  Em vez disso, as solicitações são enviadas com um ponto de extremidade multiplexes entre locatários tudo Azure AD:

    https://login.microsoftonline.com/common

Quando o Azure AD recebe uma solicitação do /common ponto de extremidade, ele sinais do usuário em e como consequência descobre qual locatário o usuário é de.  A/ponto de extremidade comum funciona com todos os protocolos de autenticação compatíveis com o Azure AD: OpenID conectar, OAuth 2.0, SAML 2.0 e Web Services Federation.

A resposta para o aplicativo, em seguida, entrar contém um token que representa o usuário.  O valor de emissor do token informa um aplicativo quais o usuário for proveniente de locatário.  Quando uma resposta retorna do /common ponto de extremidade, o valor de emissor do token corresponderão ao locatário do usuário.  É importante observar a /common ponto de extremidade não é um locatário e não é um emissor, é apenas um multiplexador.  Ao usar /common, a lógica em seu aplicativo para validar tokens precisa ser atualizado para levar isso em conta. 

Conforme mencionado anteriormente, locatários vários aplicativos também devem fornecer uma experiência de entrada consistente para usuários, seguindo o aplicativo do Azure AD diretrizes de identidade visual. Clique no botão abaixo para saber mais sobre a marcação de seu aplicativo.

[! [Entrar no botão] [AAD-entrar]][AAD-App-Branding]

Vamos dar uma olhada o uso do /common ponto de extremidade e a implementação de código mais detalhadamente.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Atualizar seu código para lidar com vários valores de emissor
Aplicativos da Web e web APIs receber e validar tokens do Azure AD.  

> [AZURE.NOTE] Enquanto aplicativos cliente nativo solicitam e recebem tokens do Azure AD, eles fazem-lo para enviá-los para APIs, onde eles são validados.  Aplicativos nativos não validar tokens e devem tratá-los como opaca.

Vamos dar uma olhada em como um aplicativo valida os tokens que recebe do Azure AD.  Um aplicativo de locatário único normalmente terá um valor de ponto de extremidade como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

e usá-lo para construir uma URL de metadados (nesse caso, OpenID conectar) como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

Para fazer o download de duas partes de informações críticas que são usados para validar tokens: locatário de assinatura chaves e o valor de emissor.  Cada locatário Azure AD tem um valor exclusivo emissor do formulário:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

onde o valor GUID é a versão de renomear isenta da identificação do locatário do locatário.  Se você clicar no link metadados acima para `contoso.onmicrosoft.com`, você pode ver esse valor emissor no documento.

Quando um aplicativo de locatário único valida um token, ele verifica a assinatura do token contra as chaves de assinatura do documento de metadados e garante que o valor de emissor do token corresponde ao que foi encontrado no documento de metadados.

Desde o /common ponto de extremidade não correspondem a um locatário e não é um emissor, quando você examinar o valor de emissor nos metadados de/comuns-tem uma URL de modelo em vez de um valor real:

    https://sts.windows.net/{tenantid}/

Portanto, um aplicativo de vários locatário não pode validar tokens apenas combinando o valor de emissor nos metadados com o `issuer` valor no token.  Um aplicativo de vários locatário necessidades lógica para decidir quais valores de emissor são válidos e quais são não, com base no locatário parte de ID do valor emissor.  

Por exemplo, se um aplicativo de vários locatário só permitir entrar de locatários específicos que se inscreveram para o seu serviço, em seguida, ele deve verificar o valor emissor ou o `tid` reivindicar valor no token para certificar-se de locatário está em sua lista de assinantes.  Se um aplicativo de vários locatário apenas lida com indivíduos e não fazer qualquer decisões de acesso com base em locatários, ele pode ignorar o valor de emissor completamente.

Nos exemplos vários locatários que você encontrará na seção [Conteúdo relacionado](#related-content) no final deste artigo, validação de emissor é desabilitada para permitir que qualquer locatário Azure AD entrar.

Agora vamos observar a experiência do usuário para usuários que estão entrando no locatários vários aplicativos.

## <a name="understanding-user-and-admin-consent"></a>Usuário de compreensão e consentimento de administração
Para um usuário entrar em um aplicativo no Azure AD, o aplicativo deve ser representado no locatário do usuário.  Isso permite que a organização fazer algo como aplicar políticas de exclusivas quando os usuários de seu locatário entram no aplicativo.  Para um aplicativo de locatário único esse registro é simple; ele é o que acontece quando você registra o aplicativo no [Azure portal clássico][AZURE-classic-portal].

Para um aplicativo de vários locatário, registro inicial para o aplicativo reside no locatário Azure AD usado pelo desenvolvedor.  Quando um usuário de um locatário diferentes entra para o aplicativo pela primeira vez, o Azure AD solicita que eles consentimento para as permissões solicitadas pelo aplicativo.  Se eles consentimento, em seguida, é criada uma representação do aplicativo chamado um *serviço principal* no locatário do usuário e pode continuar a entrar. Uma delegação também é criada no diretório que registra o consentimento do usuário para o aplicativo. Consulte [objetos de aplicativo e serviço capital] [ AAD-App-SP-Objects] para obter detalhes sobre objetos de aplicativo e ServicePrincipal do aplicativo e como se relacionam entre si.

![Consentimento ao aplicativo de camada única][Consent-Single-Tier] 

Esta experiência de consentimento é afetada pelas permissões solicitadas pelo aplicativo.  Azure AD suporta dois tipos de permissões, delegados e somente aplicativo:

- Uma permissão delegada concede um aplicativo a capacidade para agir como um usuário conectado de um subconjunto dos itens que o usuário pode fazer.  Por exemplo, você pode conceder um aplicativo a permissão delegada para ler calendário do usuário conectado.
- Um aplicativo somente tem permissão diretamente a identidade do aplicativo.  Por exemplo, você pode conceder a permissão de aplicativo somente para ler a lista de usuários em um locatário um aplicativo, e ele poderá fazer isso, independentemente de quem está conectado ao aplicativo.

Algumas permissões podem ser consentiu por um usuário normal, enquanto outras exigem consentimento de um administrador locatário. 

### <a name="admin-consent"></a>Consentimento de administração
Permissões somente aplicativo sempre requerem consentimento de um administrador locatário.  Se seu aplicativo solicita uma permissão somente aplicativo e um usuário normal tenta se conectar ao aplicativo, seu aplicativo obterá uma mensagem de erro dizendo que o usuário não conseguir consentimento.

Determinadas permissões delegadas também exigem consentimento de um administrador locatário.  Por exemplo, a capacidade de write-back para Azure AD como o usuário conectado no requer o consentimento de um administrador locatário.  Como permissões somente aplicativo, se um usuário comum tenta se conectar a um aplicativo que solicita uma permissão delegada que requer o consentimento de administrador, seu aplicativo receberá um erro.  Estando ou não exige uma permissão consentimento de administração é determinado pelo desenvolvedor que o recurso de publicados e pode ser encontradas na documentação do recurso.  Links para tópicos que descrevem as permissões disponíveis para a API do Azure AD Graph e a API do Microsoft Graph estão na seção [Conteúdo relacionado](#related-content) deste artigo.

Se o aplicativo usa as permissões que exigem consentimento de administrador, você precisa ter um gesto em seu aplicativo como um botão ou link onde o administrador pode iniciar a ação.  A solicitação de seu aplicativo envia para esta ação é uma solicitação de autorização de OAuth2/OpenID conectar usual, mas que também inclui a `prompt=admin_consent` parâmetro da sequência de consulta.  Após o administrador aceitou e o capital de serviço é criado no locatário do cliente, solicitações de entrar subsequentes não é necessário o `prompt=admin_consent` parâmetro.   Como o administrador tiver decidido que as permissões solicitadas são aceitáveis, nenhum outro usuário no locatário será solicitado consentimento desse ponto em diante.

O `prompt=admin_consent` parâmetro também pode ser usado por aplicativos que solicitam permissões que não exigem consentimento de administração, mas deseja dar uma experiência onde a administração de locatário "se inscreve" para o aplicativo de uma vez, e nenhum outro usuário for solicitado consentimento desse ponto em.

Se um aplicativo requer o consentimento de administrador, e o administrador se inscreve para o aplicativo, mas o `prompt=admin_consent` parâmetro não é enviado, o administrador poderá consentimento com êxito para o aplicativo, mas eles só serão consentimento para sua conta de usuário.  Usuários regulares ainda não poderão entrar e consentimento para o aplicativo.  Isso é útil se você quiser permite que o administrador locatário explorar seu aplicativo antes de permitir acesso de outros usuários.

Um administrador locatário pode desabilitar a capacidade para os usuários regulares consentimento para aplicativos.  Se esse recurso estiver desabilitado, administração consentimento sempre é necessário para o aplicativo ser configuradas no locatário.  Se você quiser testar seu aplicativo com o usuário regular consentimento desabilitado, você pode encontrar a mudança de configuração no locatário Azure AD seção de configuração do [Azure portal clássico][AZURE-classic-portal].

> [AZURE.NOTE] Alguns aplicativos quer uma experiência onde usuários regulares são capazes de consentimento inicialmente e posteriormente o aplicativo pode envolver as permissões de administrador e solicitar que exigem consentimento de administrador.  Não há nenhuma maneira de fazer isso com o registro de um único aplicativo no Azure AD hoje.  Os próximos o ponto de extremidade do Azure AD v2 permitirá aplicativos para solicitar permissões em tempo de execução, em vez de em tempo de registro, que permitirá que esse cenário.  Para obter mais informações, consulte o [Guia de desenvolvedor do modelo de aplicativo do Azure AD v2][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Aplicativos de consentimento e vários níveis
Seu aplicativo pode ter vários níveis, cada um representado pelo seu próprio registro no Azure AD.  Por exemplo, um aplicativo nativo que chama uma API de web ou um aplicativo web que chame um web API.  Em ambos os casos, o cliente (aplicativo nativo ou web app) solicita permissões para chamar o recurso (web API).  Para o cliente com êxito ser consentiu em locatário de um cliente, todos os recursos aos quais ele solicita permissões já devem existir no locatário do cliente.  Se essa condição não for atendida, o Azure AD retornarão um erro que o recurso deve ser adicionado primeiro.

Isso pode ser um problema se seu aplicativo lógico consiste em dois ou mais registros aplicativo, por exemplo, um cliente separado e recursos.  Como você faz o recurso para o locatário de cliente primeiro?  Azure AD aborda neste caso, permitindo que o cliente e o recurso a ser consentiu em uma única etapa, onde o usuário vê a soma total das permissões solicitado pelo cliente e pelo recurso na página de autorização.  Para habilitar esse comportamento, o registro de aplicativos do recurso deve incluir ID de aplicativo do cliente como um `knownClientApplications` em seu manifesto de aplicativo.  Por exemplo:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Essa propriedade pode ser atualizada por meio do recurso [manifesto do aplicativo][AAD-App-Manifest]e é demonstrou em um cliente nativo de vários nível chamando amostra de web API na seção [Conteúdo relacionado](#related-content) no final deste artigo. O diagrama a seguir fornece uma visão geral de consentimento para um aplicativo de várias camado:

![Consentimento aplicativo cliente conhecidos de vários níveis][Consent-Multi-Tier-Known-Client] 

Um caso semelhante acontece se as diferentes camadas de um aplicativo são registradas no locatários diferentes.  Por exemplo, considere o caso de criação de um aplicativo cliente nativo que chama a API do Office 365 Exchange Online.  Para desenvolver o nativo aplicativo e posterior para o aplicativo nativo executar no locatário de um cliente, o Exchange Online capital de serviço deve estar presente.  Nesse caso, o cliente tem adquirir o Exchange Online para o serviço principal ser criados em seu locatário.  No caso de uma API criada por uma organização diferente da Microsoft, o desenvolvedor da API precisa oferece uma maneira para seus clientes para consentimento seus aplicativos em um locatário do cliente, por exemplo uma página da web que unidades consentimento através dos mecanismos descritos neste artigo.  Após o capital de serviço é criado no locatário, o aplicativo nativo pode obter tokens para API.

O diagrama a seguir fornece uma visão geral de consentimento para um aplicativo de várias camado registrado no locatários diferentes:

![Consentimento ao aplicativo de vários participante de vários nível][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Revogando consentimento
Usuários e administradores podem revogar consentimento para seu aplicativo em qualquer momento:

- Usuários revogam o acesso a aplicativos individuais, removendo-os de seus [Aplicativos do painel de acesso] [ AAD-Access-Panel] lista.
- Os administradores revogam o acesso a aplicativos, removendo-os do Azure AD usando a seção de gerenciamento do Azure AD do [Azure portal clássico][AZURE-classic-portal].

Se um administrador dê o seu consentimento para um aplicativo para todos os usuários em um locatário, os usuários não podem revogar o acesso individualmente.  Somente o administrador pode revogar o acesso e apenas para o aplicativo inteiro.

### <a name="consent-and-protocol-support"></a>Suporte a protocolo e consentimento
Consentimento é compatível com o Azure AD via OAuth, OpenID conectar, Web Services Federation e SAML protocolos.  Os protocolos SAML e Web Services Federation não dão suporte a `prompt=admin_consent` parâmetro, portanto administração consentimento só é possível via OAuth e OpenID se conectar.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Aplicativos de vários locatários e cache Tokens de acesso
Aplicativos de vários locatários também podem chegar tokens de acesso para chamar APIs protegidos por Azure AD.  Um erro comum ao usar a biblioteca de autenticação do Active Directory (ADAL) com um aplicativo de vários locatário é inicialmente solicitar um token de um usuário usando /common, recebe uma resposta e, em seguida, solicitar um token subsequente para esse usuário mesmo também usando /common.  Desde que a resposta do Azure AD vem de um locatário, não/armazena ADAL comum, o token como sendo de locatário. A chamada subsequente para /common para obter um token de acesso do usuário erros de entrada do cache e o usuário será solicitado a entrar novamente.  Para evitar perder o cache, certifique-se de ponto de extremidade do locatário feitas chamadas subsequentes para um usuário já conectado.

## <a name="related-content"></a>Conteúdo relacionado

- [Exemplos de aplicativos de vários locatários][AAD-Samples-MT]
- [Diretrizes de identidade visual para aplicativos][AAD-App-Branding]
- [Guia do desenvolvedor do Azure AD][AAD-Dev-Guide]
- [Objetos de aplicativo e serviço Principal][AAD-App-SP-Objects]
- [Integração de aplicativos com o Active Directory do Azure][AAD-Integrating-Apps]
- [Visão geral do Framework consentimento][AAD-Consent-Overview]
- [Escopos de permissão do Microsoft Graph API][MSFT-Graph-AAD]
- [Escopos de permissão do Azure AD Graph API][AAD-Graph-Perm-Scopes]

Use a seção de comentários de Disqus abaixo para fornecer comentários e ajudar a refinar e nosso conteúdo de forma.

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[MSFT-Graph-AAD]: https://graph.microsoft.io/en-us/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ./active-directory-appmodel-v2-overview.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken














