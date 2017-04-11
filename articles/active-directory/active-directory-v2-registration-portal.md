<properties
    pageTitle="Tópicos de ajuda de Portal de registro de aplicativo | Microsoft Azure"
    description="Uma descrição de vários recursos no portal de registro do aplicativo da Microsoft."
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

# <a name="app-registration-reference"></a>Referência de registro de aplicativo
Este documento fornece contexto e descrições de vários recursos encontrados no Portal de registro do aplicativo Microsoft [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).

## <a name="my-applications"></a>Meus aplicativos
Esta lista contém todos os aplicativos registrados para uso com o ponto de extremidade do Azure AD v 2.0.  Esses aplicativos têm a capacidade de entrar usuários com ambas as contas pessoais da conta da Microsoft e trabalho/escola contas do Active Directory do Azure.  Para saber mais sobre o ponto de extremidade do Azure AD v 2.0, consulte nosso [Visão geral de v 2.0](active-directory-appmodel-v2-overview.md).  Esses aplicativos também podem ser usados para integrar o ponto de extremidade de autenticação conta Microsoft, `https://login.live.com`.

## <a name="live-sdk-applications"></a>Aplicativos de SDK ao vivo
Esta lista contém todos os aplicativos registrados para ser usado somente com a conta da Microsoft.  Eles não estão ativados para uso com o Active Directory do Azure qualquer.  Isso é onde você encontrará todos os aplicativos que tinham anteriormente registrados com o portal do desenvolvedor MSA em `https://account.live.com/developers/applications`.  Todas as funções que você realizou anteriormente no `https://account.live.com/developers/applications` agora podem ser executadas neste novo portal, `https://apps.dev.microsoft.com`.  Se você tiver mais dúvidas sobre seus aplicativos de conta da Microsoft, entre em contato conosco.

## <a name="application-secrets"></a>Segredos de aplicativo
Segredos do aplicativo são credenciais que permitem ao aplicativo executar confiável [autenticação do cliente](http://tools.ietf.org/html/rfc6749#section-2.3) com o Azure AD.  Em OAuth & OpenID conectar, segredos um aplicativo é comumente conhecido como uma `client_secret`.  No protocolo v 2.0, qualquer aplicativo que recebe um token de segurança em um local de endereçamento da web (usando um `https` esquema) deve usar um segredo de aplicativo para identificar-se ao Azure AD após resgate que de token de segurança.  Além disso, qualquer cliente nativo que recebe tokens em um dispositivo será ser proibido de usando um segredo de aplicativo para executar a autenticação de cliente, para evitar o armazenamento de segredos em ambientes inseguras.

Cada aplicativo pode conter dois segredos de aplicativo válido em qualquer ponto no tempo.  Mantendo dois segredos, você tem o ablilty realizem sobreposição de chave periódica em todo o ambiente do seu aplicativo.  Depois de migrar na íntegra do seu aplicativo para um novo segredo, você pode excluir o segredo antigo e provisionar um novo.

No momento, apenas dois tipos de segredos do aplicativo são permitidos no portal de registro do aplicativo.  Escolher **Gerar nova senha** gerará e armazenar um segredo compartilhado no repositório de dados respectivos, que você pode usar em seu aplicativo.  Escolher **Gerar novo par de chaves** criará um novo par de chaves de público/particular que pode ser baixado e usado para autenticação de cliente no Azure AD.

## <a name="profile"></a>Perfil
A seção de perfil do portal de registro do aplicativo pode ser usada para personalizar a página para seu aplicativo entrar.  Neste momento, você pode alterar de entrada do logotipo do aplicativo da página, termos de URL do serviço e a declaração de privacidade.  O logotipo deve ser uma imagem transparente de pixel de 48x48 ou 50 x 50 em um arquivo GIF, PNG ou JPEG 15 KB ou menores.  Tente alterando os valores e exibindo o sinal resultante na página!

## <a name="live-sdk-support"></a>Suporte ao vivo SDK
Quando você habilita a "Live SDK suporte", qualquer segredos de aplicativo que você criar serão configurados para o Azure AD e armazenamento de dados de Account da Microsoft.  Isso permitirá que seu aplicativo para integrar diretamente com o serviço de Account da Microsoft (login.live.com).  Se desejar criar um aplicativo usando Account da Microsoft diretamente (em vez de usar o ponto de extremidade do Azure AD v 2.0), você deverá garantir que o suporte de SDK Live está ativado.

Desabilitar o suporte do Live SDK garante que o segredo de aplicativo sejam gravado somente com os dados do Azure AD armazenar.  Os dados do Azure AD store incorpora regulamentos de nível empresarial que permitem atender a certos padrões, como conformidade FISMA.  Se você habilitar o suporte ao vivo SDK, seu aplicativo não pode alcançar a conformidade com alguns desses padrões.

Se você só planejar usar o ponto de extremidade do Azure AD v 2.0, você pode desativar com segurança suporte SDK Live.

