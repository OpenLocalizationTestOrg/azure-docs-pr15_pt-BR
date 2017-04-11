<properties
    pageTitle="Azure B2C ativa do diretório: Configuração do Facebook | Microsoft Azure"
    description="Forneça inscrição e entrar para consumidores com contas do Facebook em seus aplicativos que são protegidos por Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure B2C diretório ativo: Fornecer inscrição e entrar para consumidores com contas do Facebook

## <a name="create-a-facebook-application"></a>Criar um aplicativo do Facebook

Para usar o Facebook como um provedor de identidade no B2C Azure Active Directory (AD Azure), você precisa criar um aplicativo do Facebook e fornecer os parâmetros à direita. Você precisa de uma conta do Facebook para fazer isso. Se você não tiver um, você pode obtê-lo em [https://www.facebook.com/](https://www.facebook.com/).

1. Acesse o site do [Facebook para os desenvolvedores](https://developers.facebook.com/) e entre com suas credenciais de conta do Facebook.
2. Se você ainda não fez isso, você precisa registrar como um desenvolvedor do Facebook. Para fazer isso, clique em **registrar** (no canto superior direito da página), aceitar políticas do Facebook e conclua as etapas de registro.
3. Clique em **Meus aplicativos** e clique em **Adicionar um novo aplicativo**. Escolha **site** como a plataforma e clique em **Ignorar e criar ID de aplicativo**.

    ![Facebook - adicionar um novo aplicativo](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![Facebook - adicionar um novo aplicativo - site](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![Facebook - criar ID de aplicativo](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. No formulário, forneça um **Nome para exibição**, um **Email de contato**de válido, uma **categoria**de apropriada e clique em **Criar ID de aplicativo**. Isso requer que você aceitar políticas de plataforma do Facebook e concluir uma verificação de segurança on-line.

    ![Facebook - criar uma nova ID de aplicativo](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. No painel de navegação esquerdo, clique em **configurações** .
6. Clique em **+ Adicionar plataforma** e selecione **site**.

    ![Facebook - configurações](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![Site do Facebook - configurações-](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. Digite [https://login.microsoftonline.com/](https://login.microsoftonline.com/) no campo **URL do Site** e clique em **Salvar alterações**.

    ![Facebook - URL do Site](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

8. Copie o valor da **ID de aplicativo**. Clique em **Mostrar** e copie o valor de **Segredo do aplicativo**. Você precisará ambos para configurar o Facebook como um provedor de identidade em seu locatário. **Aplicativo segredo** é uma credencial de segurança importantes.

    ![Facebook - ID de aplicativo & segredo de aplicativo](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)

9. Clique em **+ Adicionar produto** no painel de navegação esquerdo e o botão de **Introdução** ao lado de **Logon do Facebook**.

    ![Facebook - logon no Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)

10. Insira `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **válido OAuth redirecionar URIs** na seção **Configurações de OAuth do cliente** . Substitua **{locatário}** pelo nome do seu locatário (por exemplo, contosob2c.onmicrosoft.com). Clique em **Salvar alterações** na parte inferior da página.

    ![Facebook - URI de redirecionamento OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

11. Para tornar seu aplicativo do Facebook utilizáveis por Azure AD B2C, você precisa disponibilizá-la. Você pode fazer isso clicando em **Revisão de aplicativo** no painel de navegação esquerdo e desativando a opção na parte superior da página para **Sim** e clicando em **Confirmar**.

    ![Facebook - público de aplicativo](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Configurar o Facebook como um provedor de identidade em seu locatário

1. Siga estas etapas para [navegar até a lâmina de recursos B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. Na lâmina B2C recursos, clique em **provedores de identidade**.
3. Clique em **+ Adicionar** na parte superior da lâmina.
4. Fornece um **nome** de amigável para a configuração do provedor de identidade. Por exemplo, insira "Disponibilidade".
5. Clique em **tipo de provedor de identidade**, selecione **Facebook**e clique em **Okey**.
6. Clique em **configurar esse provedor de identidade** e insira a ID de aplicativo e segredo de aplicativo (do aplicativo Facebook que você criou anteriormente) no **ID do cliente** e o **cliente segredo** campos respectivamente.
7. Clique em **Okey**e, em seguida, clique em **criar** para salvar sua configuração do Facebook.
