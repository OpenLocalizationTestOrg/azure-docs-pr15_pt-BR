<properties
    pageTitle="Azure B2C diretório ativo: Token de sessão e configuração de logon único | Microsoft Azure"
    description="Configuração de logon única no Azure Active Directory B2C, sessão e token"
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

# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure B2C diretório ativo: Token de sessão e configuração de logon único

Esse recurso oferece um controle refinado, um [por políticas](active-directory-b2c-reference-policies.md), de:
 
1. Vida útil de token de segurança emitido por B2C Azure Active Directory (AD Azure).
2. Vida útil de sessões dos aplicativos web gerenciadas pelo Azure AD B2C.
3. Comportamento de logon único (SSO) em vários aplicativos e políticas no seu locatário B2C.

Você pode usar esse recurso em seu locatário B2C da seguinte maneira:

1. Siga estas etapas para [navegar até a lâmina de recursos B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. Clique em **políticas de entrada**. *Observação: você pode usar esse recurso em qualquer tipo de política, não apenas em* *Políticas de entrada** *.
3. Abra uma política clicando nela. Por exemplo, clique em **B2C_1_SiIn**.
4. Clique em **Editar** na parte superior da lâmina.
5. Clique em **símbolo, sessão e configuração de logon única**.
6. Faça as alterações desejadas. Saiba mais sobre as propriedades disponíveis nas seções subsequentes.
7. Clique em **Okey**.
8. Clique em **Salvar** na parte superior a lâmina.

![Captura de tela de token, sessão e configuração de logon única](./media/active-directory-b2c-token-session-sso/token-session-sso.png)

## <a name="token-lifetimes-configuration"></a>Configuração de vida útil token

Azure AD B2C suporta o [protocolo de autorização de OAuth 2.0](active-directory-b2c-reference-protocols.md) para habilitar o acesso seguro a recursos protegidos. Para implementar esse suporte, o Azure AD B2C emite vários [tokens de segurança](active-directory-b2c-reference-tokens.md). Estas são as propriedades que você pode usar para gerenciar o tempo de vida de token de segurança emitido por Azure AD B2C:

- **Acesso & identificação de vida útil token (minutos)**: A vida útil do token de portador OAuth 2.0 usada para acessar um recurso protegido. Azure AD B2C emite tokens de ID somente neste momento. Esse valor seria aplicar aos tokens de acesso também, quando adicionamos suporte para eles.
   - Padrão = 60 minutos.
   - Mínimo (inclusive) = 5 minutos.
   - Máximo (inclusive) = 1440 minutos.
- **Vida útil do token atualização (dias)**: O período de tempo máximo antes da qual um token de atualização pode ser usado para adquirir um token de ID ou novo acesso (e, opcionalmente, um nova atualização token, se seu aplicativo tivesse sido concedido a `offline_access` escopo).
   - Padrão = 14 dias.
   - Mínimo (inclusive) = 1 dia.
   - Máximo (inclusive) = 90 dias.
- **Token de atualização deslizando o tempo de vida de janela (dias)**: após esse período de tempo decorrido do usuário é forçado a autenticar novamente, independentemente o período de validade da mais recente atualizar token adquirido pelo aplicativo. Só pode ser fornecido se a opção estiver definida para **Bounded**. Ele precisa ser maior ou igual a **vida útil do token atualização (dias)** valor. Se a opção estiver definida para **Unbounded**, você não pode fornecer um valor específico.
   - Padrão = 90 dias.
   - Mínimo (inclusive) = 1 dia.
   - Máximo (inclusive) = 365 dias.

Estes são alguns casos de uso que você pode habilitar o uso dessas propriedades:

- Permitir que um usuário Permaneço conectado em um aplicativo móvel indefinidamente, desde que ele ou ela está continuamente ativa no aplicativo. Você pode fazer isso definindo a **atualização deslizante janela vida útil do token (dias)** mudar **Unbounded** no sua política de entrada.
- Atender do setor requisitos de segurança e conformidade, definindo os tempos de vida de token de acesso apropriado.

## <a name="session-configuration"></a>Configuração de sessão

Azure AD B2C suporta o [protocolo de autenticação de OpenID conectar](active-directory-b2c-reference-oidc.md) para habilitar entrar seguro aos aplicativos da web. Estas são as propriedades que você pode usar para gerenciar as sessões do aplicativo web:

- **Aplicativo Web vida útil da sessão (minutos)**: A vida útil do cookie de sessão do Azure AD B2C armazenado no navegador do usuário durante a autenticação bem-sucedida.
   - Padrão = 1440 minutos.
   - Mínimo (inclusive) = 15 minutos.
   - Máximo (inclusive) = 1440 minutos.
- **Tempo limite de sessão de aplicativo Web**: se essa opção estiver definida para **absoluta**, o usuário é forçado a autenticar novamente após o período de tempo especificado por ter decorrido **Web app vida útil da sessão (minutos)** . Se essa opção estiver definida como **sem interrupção** (a configuração padrão), o usuário permanecerá conectado, desde que o usuário esteja continuamente ativo no seu aplicativo web.

Estes são alguns casos de uso que você pode habilitar o uso dessas propriedades:

- Atender a requisitos de segurança e conformidade do setor definindo a sessão do aplicativo da web apropriado vidas úteis.
- Força nova autenticação após um período de tempo definido durante a interação do usuário com uma parte de alto nível de segurança do seu aplicativo web. 

## <a name="single-sign-on-sso-configuration"></a>Configuração de logon único (SSO)

Se você tiver vários aplicativos e políticas em seu locatário B2C, você pode gerenciar interações do usuário entre eles usando a propriedade de **configuração de logon único** . Você pode definir a propriedade para uma das configurações a seguir:

- **Locatário**: esta é a configuração padrão. Usando essa configuração permite vários aplicativos e políticas em seu locatário B2C para compartilhar a mesma sessão de usuário. Por exemplo, quando um usuário entra em um aplicativo, as compras de Contoso, ele ou ela pode também perfeitamente entrar no outro um, farmácia Contoso, ao acessá-lo.
- **Aplicativo**: Isso permite manter uma sessão de usuário exclusivamente para um aplicativo, independentemente de outros aplicativos. Por exemplo, se você quiser que o usuário para entrar no Contoso farmácia (com as mesmas credenciais), mesmo que ele ou ela já está conectada ao Contoso compras, outro aplicativo no mesmo B2C locatário. 
- **Diretiva**: Isso permite manter uma sessão de usuário exclusivamente para uma política, independente dos aplicativos usá-lo. Por exemplo, se o usuário já conectado e concluir uma etapa de autenticação (MFA) de fator de multi, ele ou ela pode ser fornecida acesso a partes de maior segurança de vários aplicativos desde que a sessão vinculada à política não expire.
- **Desativado**: Isso força o usuário a percorrer a jornada de usuário inteiro em cada execução da política. Por exemplo, isso permitirá que vários usuários para se inscrever para o seu aplicativo (em um cenário de área de trabalho compartilhada), permanece mesmo durante um único usuário conectado durante o tempo todo.
