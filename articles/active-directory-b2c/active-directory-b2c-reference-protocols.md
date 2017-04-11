<properties
    pageTitle="Active Directory do Azure B2C | Microsoft Azure"
    description="Como criar aplicativos diretamente, usando os protocolos suportados pelo Azure Active Directory B2C."
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-authentication-protocols"></a>Azure B2C do AD: Protocolos de autenticação

Azure Active Directory (AD Azure) B2C fornece identidade como um serviço para os aplicativos por dois protocolos padrão do setor de suporte: OpenID se conectar e OAuth 2.0. O serviço é compatível com os padrões, mas qualquer duas implementações desses protocolos podem ter diferenças sutis.  As informações neste guia será útil se você escrever seu código diretamente enviando e lidar com solicitações de HTTP, em vez de usar uma biblioteca de código aberto. Recomendamos que você leia esta página antes de começar os detalhes de cada protocolo específico. Mas se você já estiver familiarizado com o Azure AD B2C, você pode ir direto para [as guias de referência de protocolo](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Noções básicas
Cada aplicativo que usa o Azure AD B2C deve ser registrado no diretório B2C no [portal do Azure](https://portal.azure.com). O processo de registro de aplicativo coleta e atribui alguns valores para o seu aplicativo:

- Uma **ID de aplicativo** que identifica exclusivamente o seu aplicativo.
- Um **URI redirecionar** ou um **identificador de pacote** que podem ser usados para direcionar respostas de volta para seu aplicativo.
- Alguns outros valores de cenário específico. Para saber mais, saiba [como registrar seu aplicativo](active-directory-b2c-app-registration.md).

Depois que você registrar seu aplicativo, ele se comunica com o Azure AD enviando solicitações para o ponto de extremidade de v 2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Em praticamente todos os fluxos de OAuth e OpenID se conectar, quatro partes estão envolvidas no exchange:

![Funções de OAuth 2.0](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

- O **servidor de autorização** é o ponto de extremidade do Azure AD v 2.0. Com segurança trata que nada relacionados ao acesso e informações do usuário. Também trata as relações de confiança entre as partes em um fluxo. Ele é responsável por verificar a identidade do usuário, conceder e revogar o acesso aos recursos e tokens de emissão. Ele também é conhecido como o provedor de identidade.
- O **proprietário do recurso** normalmente é o usuário final. É o participante que possui os dados, e ela tem a capacidade de terceiros acessar esses dados ou o recurso de permitir.
- O **cliente de OAuth** é seu aplicativo. Ele é identificado por sua ID de aplicativo. Geralmente é a pessoa que usuários finais interagem com. Ele também solicita tokens do servidor autorização. O proprietário do recurso deve conceder a permissão de cliente para acessar o recurso.
- O **servidor de recurso** é onde os dados ou o recurso residem. Ele confia no servidor de autorização para autenticar e autorizar o cliente OAuth de forma segura. Ele também usa os tokens de acesso do portador para garantir que o acesso a um recurso pode ser concedido.

## <a name="policies"></a>Políticas
Possivelmente, políticas do Azure AD B2C são os recursos mais importantes do serviço. Azure AD B2C estende os protocolos OAuth 2.0 e OpenID conectar padrão introduzindo políticas. Elas permitem Azure AD B2C realizar muito mais do que simples autenticação e a autorização. Políticas de totalmente descrevem consumidor identidade experiências, incluindo inscrição, entrar e edição de perfil. Políticas de podem ser definidas em uma interface de usuário administrativo. Eles podem ser executados usando um parâmetro de consulta especial em solicitações de autenticação de HTTP. Políticas não são recursos padrão de OAuth 2.0 e OpenID conectar, então você deve levar o tempo para entendê-los. Para obter mais informações, consulte o [guia de referência do Azure AD B2C política](active-directory-b2c-reference-policies.md).

## <a name="tokens"></a>Tokens
A implementação do Azure AD B2C de OAuth 2.0 e OpenID conectar faz amplo uso de tokens de portador, incluindo tokens de portador que são representados como tokens de web JSON (JWTs). Um token de portador é um token de segurança leve que concede acesso à "portador" para um recurso protegido. Transportador é qualquer pessoa que pode apresentar o token. Azure AD deve primeiro autenticar uma festa antes que ele possa receber um token de portador. Mas se as etapas necessárias não são levadas para proteger o token de transmissão e armazenamento, ele pode ser interceptado e usado por uma pessoa não intencionais.

Alguns tokens de segurança tem um mecanismo interno que impede que autorizadas usá-los, mas tokens de portador não tem esse mecanismo. Eles devem ser transportados em um canal seguro, como segurança da camada de transporte (HTTPS). Se um token de portador for transmitido fora de um canal seguro, um terceiro mal-intencionado pode usar um ataque no meio para adquirir o token e usá-lo para obter acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam quando tokens de portador são armazenados ou em cache para uso posterior. Sempre certifique-se de que seu aplicativo transmite e armazena tokens de portador de maneira segura.

Para considerações de segurança token de portador adicionais, consulte [RFC 6750 seção 5](http://tools.ietf.org/html/rfc6750).

Mais detalhes sobre os diferentes tipos de tokens usados no Azure AD B2C estão disponíveis na [referência de token do Azure AD](active-directory-b2c-reference-tokens.md).

## <a name="protocols"></a>Protocolos

Quando você estiver pronto para analisar algumas solicitações de exemplo, você pode começar com um dos seguintes tutoriais. Cada um deles corresponde a um cenário de autenticação específico. Se precisar de ajuda para determinar qual fluxo é adequado para você, confira [os tipos de aplicativos que você pode criar usando o Azure AD B2C](active-directory-b2c-apps.md).

- [Criar aplicativos móveis e nativos usando OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
- [Criar aplicativos web usando a conexão de OpenID](active-directory-b2c-reference-oidc.md)
