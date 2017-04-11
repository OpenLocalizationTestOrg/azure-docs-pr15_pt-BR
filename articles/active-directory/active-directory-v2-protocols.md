<properties
    pageTitle="Azure AD v 2.0 protocolos | Microsoft Azure"
    description="Um guia para protocolos suportados pelo ponto de extremidade do Azure AD v 2.0."
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20--openid-connect"></a>Conectar de protocolos - OAuth 2.0 & OpenID v 2.0

O ponto de extremidade de v 2.0 pode usar Azure AD para identidade-como um serviço com protocolos padrão do setor, OpenID se conectar e OAuth 2.0.  Enquanto o serviço é compatível com padrões, pode haver diferenças sutis entre as duas implementações desses protocolos.  As informações aqui será útil se você optar por escrever seu código enviando diretamente & lidar com solicitações HTTP ou use um 3º festa Abrir biblioteca de fontes, em vez de usar uma das nossas bibliotecas de código-fonte aberto.
<!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]
    Nem todos os cenários do Active Directory do Azure e recursos são compatíveis com o ponto de extremidade de v 2.0.  Para determinar se você deve usar o ponto de extremidade de v 2.0, leia sobre as [limitações da versão 2.0](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Noções básicas
Em quase todos os fluxos de OAuth & OpenID conectar, há quatro partes envolvidas no exchange:

![Funções de OAuth 2.0](../media/active-directory-v2-flows/protocols_roles.png)

- O **Servidor de autorização** é o ponto de extremidade de v 2.0.  Ele é responsável por garantir a identidade do usuário, conceder e revogar o acesso aos recursos e tokens de emissão.  Ele também é conhecido como o provedor de identidade - com segurança trata algo a ver com as informações do usuário, o acesso e as relações de confiança entre partes em um fluxo.
- O **Proprietário do recurso** é geralmente o usuário final.  É o participante que possui os dados e tem a potência para permitir que terceiros acessar esses dados ou recurso.
- O **Cliente de OAuth** é seu aplicativo, identificado por sua ID de aplicativo.  Geralmente é a pessoa que o usuário final interage com e ele solicita tokens do servidor autorização.  O cliente deve ter permissão para acessar o recurso pelo proprietário do recurso.
- O **Servidor de recurso** é onde os dados ou o recurso residem.  Ele confia no servidor de autorização para autenticar com segurança e autorizar o cliente OAuth e usa portador access_tokens para garantir que o acesso a um recurso pode ser concedido.


## <a name="app-registration"></a>Registro de aplicativo
Cada aplicativo que usa o ponto de extremidade de v 2.0 precisa ser registrado no [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) antes que ela pode interagir usando OAuth ou OpenID se conectar.  O processo de registro do aplicativo irá coletar & atribuir alguns valores para o seu aplicativo:

- Uma **Id de aplicativo** que identifica exclusivamente o seu aplicativo
- Um **URI redirecionar** ou um **Identificador de pacote** que podem ser usados para direcionar respostas ao seu aplicativo
- Alguns outros valores de cenário específico.

Para obter mais detalhes, saiba como [registrar um aplicativo](active-directory-v2-app-registration.md).

## <a name="endpoints"></a>Pontos de extremidade
Após registrado, o aplicativo se comunica com o Azure AD enviando solicitações para o ponto de extremidade de v 2.0:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Onde o `{tenant}` pode realizar uma das quatro valores diferentes:

| Valor | Descrição |
| ----------------------- | ------------------------------- |
| `common` | Permite que os usuários com contas de trabalho/escola e de contas pessoais do Microsoft do Azure Active Directory para entrar no aplicativo. |
| `organizations` | Permite que somente os usuários com contas de trabalho/escola do Azure Active Directory para entrar no aplicativo. |
| `consumers` | Permite que somente os usuários com contas pessoais do Microsoft MSA () para entrar no aplicativo. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`ou`contoso.onmicrosoft.com` | Permite que somente os usuários com contas de trabalho/escola de um locatário específico do Active Directory do Azure para entrar no aplicativo.  O nome de domínio amigável do locatário Azure AD ou identificador de guid do locatário pode ser usado.  |

Para obter mais informações sobre como interagir com esses pontos de extremidade, escolha um tipo de aplicativo específico abaixo.

## <a name="tokens"></a>Tokens
A implementação de v 2.0 de OAuth 2.0 e OpenID conectar fizer uso extensivo de tokens de portador, incluindo tokens de portador representados como JWTs. Um token de portador é um token de segurança leve que concede acesso à "portador" para um recurso protegido. Nesse sentido, "portador" é qualquer pessoa que pode apresentar o token. Embora uma festa deve primeiro autenticar com o Azure AD para receber o token de portador, se as etapas necessárias não são levadas para proteger o token de transmissão e armazenamento, podem ser interceptado e usado por uma pessoa não intencionais. Enquanto alguns tokens de segurança tem um mecanismo interno para impedir que autorizadas usá-los, tokens de portador não tem esse mecanismo e devem ser transportados em um canal seguro como segurança da camada de transporte (HTTPS). Se um token de portador é transmitido em Limpar, um homem-ataque de interceptação pode ser usado por um terceiro mal-intencionado adquirir o token e usá-lo para um acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam ao armazenar ou cache tokens de portador para uso posterior. Sempre certifique-se de que seu aplicativo transmite e armazena tokens de portador de maneira segura. Para mais considerações de segurança em tokens de portador, consulte [RFC 6750 seção 5](http://tools.ietf.org/html/rfc6750).

Mais detalhes de diferentes tipos de tokens usados no ponto de extremidade v 2.0 está disponível na [referência de token de ponto de extremidade de v 2.0](active-directory-v2-tokens.md).

## <a name="protocols"></a>Protocolos

Se você estiver pronto para ver algumas solicitações de exemplo, começar com um do abaixo tutoriais.  Cada um deles corresponde a um cenário de autenticação específico.  Se precisar de ajuda para determinar qual é o fluxo de certo para você, confira [os tipos de aplicativos que você pode criar com o v 2.0](active-directory-v2-flows.md).

- [Construir Mobile e o aplicativo nativo com OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
- [Criar Web aplicativos com identificação abrir conectar](active-directory-v2-protocols-oidc.md)
- [Criar aplicativos de única página com o fluxo de implícito OAuth 2.0](active-directory-v2-protocols-implicit.md)
- [Construir Daemons ou processos de lado de servidor com as credenciais do cliente OAuth 2.0 fluxo](active-directory-v2-protocols-oauth-client-creds.md)
- Obter tokens em uma API de Web com OAuth 2.0 em nome de fluxo de (em breve)

<!-- - Get tokens using a username & password with the OAuth 2.0 Resource Owner Password Credentials Flow (coming soon) --> 
