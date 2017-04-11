<properties
    pageTitle="Azure B2C diretório ativo: Restrições e limitações | Microsoft Azure"
    description="Uma lista de restrições com o Azure Active Directory B2C e limitações"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-limitations-and-restrictions"></a>Azure B2C diretório ativo: Restrições e limitações

Há vários recursos e funcionalidades do Azure Active Directory (AD Azure) B2C que ainda não são aceitas. Muitos desses problemas conhecidos & limitações serão endereçados ao prosseguir, mas você deve estar ciente deles se você estiver criando aplicativos voltados para o consumidor usando o Azure AD B2C.

## <a name="issues-during-the-creation-of-azure-ad-b2c-tenants"></a>Problemas durante a criação do Azure AD B2C locatários

Se você encontrar problemas durante a [criação de um locatário do Azure AD B2C](active-directory-b2c-get-started.md), consulte [criar um locatário do Azure AD ou um locatário do Azure AD B2C – problemas e resoluções](active-directory-b2c-support-create-directory.md) para obter orientação.

Observe que há conhecidos problemas quando você excluir um locatário existente do B2C e recriá-la com o mesmo nome de domínio. Você precisa criar um locatário B2C com um nome de domínio diferente.

## <a name="note-about-b2c-tenant-quotas"></a>Observação sobre cotas de locatário B2C

Por padrão, o número de usuários em um locatário B2C é limitado a 50.000 usuários. Se você precisar aumentar a cota de seu locatário B2C, você deve contatar o suporte.

## <a name="branding-issues-on-verification-email"></a>Problemas de identidade visual no email de confirmação

O email de confirmação padrão contém Microsoft identidade visual. Podemos removerá no futuro. Por agora, você pode removê-lo usando o [recurso de identidade visual da empresa](../active-directory/active-directory-add-company-branding.md).

## <a name="restrictions-on-applications"></a>Restrições em aplicativos

Os seguintes tipos de aplicativos não são aceitos atualmente no Azure AD B2C. Para obter uma descrição dos tipos de aplicativos com suporte, consulte [Azure AD B2C: tipos de aplicativos](active-directory-b2c-apps.md).

### <a name="single-page-applications-javascript"></a>Aplicativos de única página (JavaScript)

Muitos aplicativos modernos tem uma única página aplicativo (SPA) front-end escrito principalmente em JavaScript e geralmente usa uma estrutura SPA como AngularJS, Ember.js, Durandal etc. Esse fluxo ainda não está disponível no Azure AD B2C.

### <a name="daemons--server-side-applications"></a>Daemons / servidor aplicativos

Aplicativos que contêm processos de execução demorada ou que operam sem a presença de um usuário também precisam de uma maneira de acessar recursos protegidos, como APIs da Web. Esses aplicativos podem autenticar e obter tokens usando a identidade do aplicativo (em vez de identidade delegada do consumidor) no [fluxo de credenciais de cliente OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-client-credentials-grant-flow). Esse fluxo ainda não está disponível no Azure AD B2C, para que no momento, aplicativos podem obter tokens somente depois que um fluxo de entrada do consumidor interativo ocorreu.

### <a name="standalone-web-apis"></a>APIs de Web autônomo

No Azure AD B2C, você tem a capacidade de [criar uma API de Web que é protegido usando tokens OAuth 2.0](active-directory-b2c-apps.md#web-apis). No entanto, essa API Web só poderão receber tokens de um cliente que compartilha o mesmo ID do aplicativo. Não há suporte para a criação de uma API de Web que é acessado de vários clientes diferentes.

### <a name="web-api-chains-on-behalf-of"></a>Cadeias de API da Web (em nome de)

Arquiteturas de muitos incluem uma API de Web que precisa chamar outra API Web downstream, ambas protegidos por Azure AD B2C. Este cenário é comum em clientes nativos que têm um API Web back-end, que por sua vez chama um serviço online da Microsoft como a API do Azure AD Graph.

Este cenário de Web API encadeado pode ser suportado usando a concessão de credencial de portador OAuth 2.0 Jwt, também conhecida como o nome diante de fluxo. No entanto, o fluxo de nome diante de atualmente não está implementado no Azure AD B2C.

## <a name="restriction-on-libraries-and-sdks"></a>Restrições em bibliotecas e SDKs

O conjunto de bibliotecas de suportada da Microsoft que funcionam Azure AD B2C é muito limitado neste momento. Temos suporte para .NET com base na web aplicativos e serviços, bem como NodeJS web apps e serviços.  Também temos uma biblioteca de cliente .NET visualização conhecida como MSAL que pode ser usado com o Azure AD B2C em outros aplicativos do .NET e Windows.

Não atualmente temos biblioteca suporte para quaisquer outros idiomas ou plataformas, inclusive iOS e Android.  Se você quiser desenvolver uma plataforma diferente daqueles mencionados acima, é recomendável usar um SDK de código-fonte aberto, referenciando nosso [OAuth 2.0 e referência de protocolo de conectar OpenID](active-directory-b2c-reference-protocols.md) conforme necessário.  Azure AD B2C implementa OAuth & OpenID conectar-se, que torna possível usar uma biblioteca de OAuth ou OpenID conectar genérica para integração.

Nossa iOS e tutoriais de início rápido Android usam bibliotecas de código-fonte aberto que testamos para compatibilidade com o Azure AD B2C.  Todos os nossos tutoriais de início rápido estão disponíveis em nossa seção [Introdução](active-directory-b2c-overview.md#getting-started) .

## <a name="restriction-on-protocols"></a>Restrição em protocolos

Azure AD B2C suporta OpenID conectar e OAuth 2.0. No entanto, nem todos os recursos e as funções de cada protocolo foram implementadas. Para entender melhor o escopo da funcionalidade de protocolo suportado no Azure AD B2C, leia nossos [OpenID se conectar e referência de protocolo OAuth 2.0](active-directory-b2c-reference-protocols.md). Suporte de protocolo SAML e WS-Fed não está disponível.

## <a name="restriction-on-tokens"></a>Restrição em tokens

Muitos dos tokens emitidos por Azure AD B2C são implementados como JSON Web Tokens ou JWTs. Entretanto, nem todas as informações contidas no JWTs (conhecidos como "declarações") são bem como ele deve ser ou está ausente. Alguns exemplos incluem "sub" e as declarações de "preferred_username".  Como os valores, formato ou significado de alteração de declarações ao longo do tempo, tokens para suas políticas existentes permanecerá afetados - você pode confiar nos valores nos aplicativos de produção.  Como alteram valores, podemos você terá a oportunidade de configurar essas alterações para cada uma das suas políticas.  Para entender melhor os tokens atualmente emitidos pelo serviço Azure AD B2C, leia nossa [referência de token](active-directory-b2c-reference-tokens.md).

## <a name="restriction-on-nested-groups"></a>Restrições em grupos aninhados

Associações de grupo aninhado não são suportadas no Azure AD B2C locatários. Não pretendemos adicionar esse recurso.

## <a name="restriction-on-differential-query-feature-on-azure-ad-graph-api"></a>Restrição de recurso de consulta diferencial em API do Azure AD Graph

O [recurso de consulta diferencial em API do Azure AD gráfico](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-differential-query) não são suportados no Azure AD B2C locatários. Este é o nosso roteiro de longo prazo.

## <a name="issues-with-user-management-on-the-azure-classic-portal"></a>Problemas com o gerenciamento de usuários no portal de clássico do Azure

Recursos de B2C são acessíveis no portal do Azure. No entanto, você pode usar o portal de clássico Azure para acessar outros recursos de locatário, incluindo gerenciamento de usuários. Atualmente, há alguns problemas conhecidos com o gerenciamento de usuários (guia de **usuários** ) no portal do clássico Azure:

- Para um usuário de conta local (ou seja, um consumidor quem se inscreve com um endereço de email e senha, ou um nome de usuário e senha), o campo **Nome** do usuário não corresponde ao entrar identificador (endereço de email ou nome de usuário) que foi usado durante a inscrição. Isso ocorre porque o campo exibido no portal do clássico Azure é realmente o usuário nome Principal (UPN), que não é usada em cenários de B2C. Para exibir o identificador de entrada da conta local, localize o objeto de usuário no [Explorador de gráfico](https://graphexplorer.cloudapp.net/). Você encontrará o mesmo problema com uma conta social do usuário (ou seja, um consumidor quem se inscreve com o Facebook, Google +, etc.), mas nesse caso, não há nenhum identificador entrar para falar de.

    ![Conta local - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- Para um usuário de conta local, você irá não é possível editar qualquer um dos campos e salvar alterações na guia **perfil** .

## <a name="issues-with-admin-initiated-password-reset-on-the-azure-classic-portal"></a>Problemas com senha iniciado pelo administrador redefinir no portal de clássico do Azure

Se você redefinir a senha para um local consumidor baseado em conta no portal de clássico Azure (o comando **Redefinir senha** na guia **usuários** ), que o consumidor não poderão alterar sua senha na próxima entrar, se você usar um sinal de ou entrar política e será bloqueada seus aplicativos. Como alternativa, use a [API do Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) para redefinir a senha do consumidor (sem expiração de senha) ou usar uma política em vez de um sinal de entrada para cima ou entrar política.

## <a name="issues-with-creating-a-custom-attribute"></a>Problemas com a criação de um atributo personalizado

Não é criado imediatamente a um [atributo personalizado adicionado no portal do Azure](active-directory-b2c-reference-custom-attr.md) em seu locatário B2C. Você terá que usar o atributo personalizado em pelo menos uma das suas políticas para ele são criados no seu locatário B2C e se tornam disponíveis por meio de API do gráfico.

## <a name="issues-with-verifying-a-domain-on-the-azure-classic-portal"></a>Problemas com a verificação de um domínio no portal de clássico do Azure

No momento você não pode verificar um domínio com êxito no [Azure portal clássico](https://manage.windowsazure.com/).

## <a name="issues-with-sign-in-with-mfa-policy-on-safari-browsers"></a>Problemas de entrada com a política MFA em navegadores Safari

Solicitações para falham de políticas de entrada (com MFA ativada) intermitentemente em navegadores Safari com erros de HTTP 400 (solicitação incorreta). Este é o vencimento limites de tamanho de cookie baixa do Safari. Há algumas soluções alternativas para este problema:

- Use a "ou de inscrição entrar diretiva" em vez da "política entrar".
- Reduza o número de **declarações de aplicativo** solicitado na sua política.
