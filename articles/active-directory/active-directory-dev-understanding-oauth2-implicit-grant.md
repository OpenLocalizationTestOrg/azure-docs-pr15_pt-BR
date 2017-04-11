<properties
   pageTitle="Noções básicas sobre o OAuth2 implícito conceder fluxo no Active Directory do Azure | Microsoft Azure"
   description="Saiba mais sobre a implementação do Azure Active Directory do OAuth2 implícitos conceder fluxo, e se ele é adequado para seu aplicativo."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="vibronet"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="vittorib;bryanla"/>

# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Compreensão do fluxo de conceder implícitos OAuth2 no Azure Active Directory (AD)

A concessão de implícito OAuth2 é muito comum sendo a concessão com a lista mais longa de questões de segurança na especificação OAuth2. E ainda, que é a abordagem implementada pelo JS ADAL e o que recomendamos ao escrever aplicativos SPA. O que acontece? É tudo uma questão de compensações: e acontece, a concessão implícita é a melhor abordagem que você pode tomar para aplicativos que consumam uma API Web por meio de JavaScript em um navegador.

## <a name="what-is-the-oauth2-implicit-grant"></a>O que é a concessão implícita OAuth2?

A melhor [conceder de código de autorização de OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) é a concessão de autorização que usa dois pontos de extremidade separados. O ponto de extremidade de autorização é usado para a fase de interação do usuário, que resulta em um código de autorização. O ponto de extremidade de token é usado pelo cliente para trocar o código para um token de acesso e geralmente também um token de atualização. Aplicativos da Web são necessários para apresentar seus próprios credenciais do aplicativo para o ponto de extremidade token, para que o servidor de autorização pode autenticar o cliente.

O [OAuth2 implícito conceder](https://tools.ietf.org/html/rfc6749#section-1.3.2) é uma variante permitindo um cliente obter um token de acesso (e id_token, no caso de [OpenId conectar](http://openid.net/specs/openid-connect-core-1_0.html)) diretamente do ponto de extremidade autorização, sem entrar em contato com o ponto de extremidade token nem autenticar o aplicativo cliente. Essa variante foi projetada especificamente para aplicativos executados em um navegador da Web baseados em JavaScript: na especificação OAuth2 original, tokens são retornados em um fragmento URI. Que disponibiliza os bits de token para o código JavaScript no cliente, mas ela garante que eles não serão incluídos na redirecionamentos em direção no servidor. Retornar tokens via navegador redireciona diretamente do ponto de extremidade de autorização. Ele também tem a vantagem de eliminar qualquer requisitos para entre chamadas de origem, que são necessários se o aplicativo de JavaScript é necessário entrar em contato com o ponto de extremidade de token.

Uma característica importante da concessão implícita OAuth2 é o fato de que tal flua tokens de atualização nunca retornar para o cliente. Como veremos na próxima seção, que não é realmente necessária e na verdade seria um problema de segurança.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Cenários adequados para a concessão implícito OAuth2

Como declara a especificação de OAuth2 em si, a concessão implícita tenha foi desenvolvida para permitir que aplicativos de agente de usuário – isto é dizer que aplicativos de JavaScript em execução em um navegador. A característica definindo desses aplicativos é que o código JavaScript é usado para acessar recursos do servidor (geralmente uma API Web) e para atualizar o aplicativo UX adequadamente. Pense em aplicativos como Gmail ou Outlook Web Access: quando você seleciona uma mensagem da sua caixa de entrada, somente o painel de visualização da mensagem alterada para exibir a nova seleção, enquanto o resto da página permanece inalterado. Isso é diferentemente tradicional baseado em redirecionar Web apps, onde cada interação de usuário resulta em uma postagem de página inteira e um processamento de página inteira da resposta do servidor novo.

Aplicativos que tomar a abordagem JavaScript com base para seu extremo são chamados de aplicativos de página única ou SPAs: a ideia é que esses aplicativos servem apenas uma página inicial de HTML e JavaScript associado, com todas as interações subsequentes sendo orientada por chamadas Web API realizadas via JavaScript. No entanto, abordagens de implantação híbrida, onde o aplicativo é principalmente orientado por postagem, mas efetua chamadas JS ocasionalmente, não são incomuns – a discussão sobre o uso de fluxo implícito é relevante para aqueles que também.

Aplicativos baseados em redirecionar normalmente protegem suas solicitações por meio de cookies, no entanto, abordagem não funciona bem para aplicativos de JavaScript. Cookies só funcionam em relação ao domínio que elas foram geradas para, enquanto chamadas JavaScript podem ser direcionadas para outros domínios. Na verdade, que frequentemente será o caso: pense invocar Microsoft Graph, API do Office, Azure API de – todos os que reside fora do domínio de onde o aplicativo é atendido de aplicativos. Uma tendência de crescimento para aplicativos de JavaScript é não ter nenhum back-end, contando 100% em festa 3º APIs Web implementar sua função de negócios.

Atualmente, o método preferencial de proteger chamadas para uma API Web é usar a abordagem de token de portador de OAuth2, onde cada chamada é acompanhada por um token de acesso de OAuth2. A API Web examina o token de acesso de entrada e, se encontra nos escopos necessários, ele conceder acesso para a operação solicitada. O fluxo implícito fornece um mecanismo conveniente para aplicativos de JavaScript obter tokens de acesso para uma API de Web, oferecendo várias vantagens em relação às cookies:

- Tokens podem ser obtidos confiavelmente sem necessidade de entre chamadas de origem – registro obrigatório do redirecionamento URI aos quais tokens são retorno garante que os tokens não sejam substituídos
- Aplicativos de JavaScript podem obter tantas tokens de acesso conforme necessário, para quantos APIs Web eles destinam – com nenhuma restrição de domínios
- Recursos de HTML5 como sessão ou armazenamento local conceder controle total sobre o cache de token e gerenciamento de vida útil, enquanto o gerenciamento de cookies é opaco ao aplicativo
- Tokens de acesso não estão sujeitos a ataques de falsificação (CSRF) de solicitação entre sites

O fluxo de conceder implícitos não emite tokens de atualização, principalmente por razões de segurança. Um token de atualização como especificamente não está com escopo definido como tokens de acesso, concedendo muito mais energia, portanto, inflicting muito mais danos caso ele é perdido check-out. No fluxo de implícitos, tokens são entregues na URL, portanto, o risco de interceptação é maior do que na concessão de autorização de código.

Entretanto, observe que um aplicativo de JavaScript tem outro mecanismo à sua disposição para renovar tokens de acesso sem aviso repetidamente credenciais do usuário. O aplicativo pode usar um iframe oculto para executar novas solicitações de token contra o ponto de extremidade de autorização do Azure AD: desde o navegador ainda tem uma sessão ativa (leia: tem um cookie de sessão) em relação ao domínio AD Azure, a solicitação de autenticação pode ocorrer com êxito sem a necessidade de interação do usuário. 

Este modelo concede o aplicativo JavaScript a capacidade de maneira independente Renovar tokens de acesso e até mesmo adquirir novas para uma nova API (desde que o usuário anteriormente consentiu para eles. Isso evita a carga adicional de adquirir, manter e proteger um artefato de valor alto como um token de atualização. O artefato que torna a renovação silenciosa possíveis, o cookie de sessão do Azure AD, é gerenciado fora do aplicativo. Outra vantagem dessa abordagem é que um usuário pode sair do Azure AD, usando qualquer um dos aplicativos conectados ao Azure AD, executando em qualquer uma das guias de navegador. Isso resulta na exclusão do cookie de sessão do Azure AD e o aplicativo de JavaScript automaticamente perderá a capacidade de renovar tokens para o usuário conectado check-out.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>A concessão implícita é adequado para meu aplicativo?

A concessão implícita apresenta mais riscos que outros concessões. As áreas que você precisa prestar atenção para estão bem documentadas (por exemplo consulte [Uso incorreto de Token de acesso para representar o proprietário do recurso no fluxo implícitos] [ OAuth2-Spec-Implicit-Misuse] e [Considerações de segurança e de modelo de risco OAuth 2.0][OAuth2-Threat-Model-And-Security-Implications]). No entanto, o perfil de risco superior é amplamente devido ao fato de que ele serve para permitir aplicativos que executar código ativo, servido por um recurso remoto para um navegador. Se você estiver planejando uma arquitetura SPA, têm sem componentes de back-end ou pretende invocar uma API Web por meio de JavaScript, é recomendável usar o fluxo implícito token de aquisição.

Se seu aplicativo for um cliente nativo, o fluxo implícito não é uma excelente opção. Ausência do cookie de sessão do Azure AD no contexto de um cliente nativo deprives seu aplicativo das médias de manter uma sessão de vida longa. Que significa que seu aplicativo repetidamente solicitará que o usuário ao obter tokens de acesso para novos recursos.

Se você está desenvolvendo um aplicativo Web que inclui um back-end e consumindo uma API do seu código de back-end, o fluxo implícito também não é uma boa opção. Outros concede possibilitam você muito mais. Por exemplo, a concessão de credenciais de cliente OAuth2 fornece a capacidade de obter tokens que refletem as permissões atribuídas ao próprio aplicativo, em vez de delegações de usuário. Isso significa que o cliente tem a capacidade de manter programação acesso aos recursos mesmo quando um usuário não estiver ativamente envolvido em uma sessão e assim por diante. Não apenas isso, mas tal concede dar maiores garantias de segurança. Por exemplo, tokens de acesso de trânsito nunca através do navegador do usuário, elas não risco sendo salvas no histórico do navegador e assim por diante. O aplicativo cliente também pode executar autenticação sólida ao solicitar um token.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma lista completa de recursos de desenvolvedor, incluindo informações de referência para os protocolos e OAuth2 autorização conceder fluxos suporte por Azure AD, consulte o [Azure AD guia do desenvolvedor][AAD-Developers-Guide]
- Veja [como integrar um aplicativo com o Azure AD]  [ ACOM-How-To-Integrate] para profundidade adicional sobre o processo de integração de aplicativo.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]: active-directory-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16 
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

