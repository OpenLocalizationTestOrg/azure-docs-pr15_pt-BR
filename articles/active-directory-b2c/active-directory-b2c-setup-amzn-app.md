<properties
    pageTitle="Azure B2C diretório ativo: Configuração Amazon | Microsoft Azure"
    description="Forneça inscrição e entrar para consumidores com contas de Amazon em seus aplicativos que são protegidos por Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure B2C diretório ativo: Fornecer inscrição e entrar para consumidores com contas de Amazon

## <a name="create-an-amazon-application"></a>Criar um aplicativo de Amazon

Para usar Amazon como um provedor de identidade no B2C Azure Active Directory (AD Azure), você precisa criar um aplicativo de Amazon e fornece-lo com os parâmetros à direita. É necessário uma conta Amazon para fazer isso. Se você não tiver um, você pode obtê-lo em [http://www.amazon.com/](http://www.amazon.com/).

1. Vá para a [Central de desenvolvedores do Amazon](https://login.amazon.com/) e entre com suas credenciais de conta Amazon.
2. Se você ainda não o fez, clique em **Inscrever-se**, siga as etapas de registro do desenvolvedor e aceitar a política.
3. Clique em **registrar o novo aplicativo**.

    ![Registrar um novo aplicativo no site da Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)

4. Fornecer informações de aplicativo (**nome**, **Descrição**e **URL de aviso de privacidade**) e clique em **Salvar**.

    ![Fornecendo informações de aplicativo para registrar um novo aplicativo na Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)

5. Na seção **Configurações da Web** , copie os valores de **ID do cliente** e **Segredo de cliente**. (É necessário clicar no botão **Mostrar secreta** para ver como isso.) Você precisa de ambos configurar Amazon como um provedor de identidade em seu locatário. Clique em **Editar** na parte inferior da seção. **Segredo do cliente** é uma credencial de segurança importantes.

    ![Fornecendo ID do cliente e segredo cliente para seu novo aplicativo na Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. Insira `https://login.microsoftonline.com` no campo **Permitido origens de JavaScript** e `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **Permitidos URLs de retorno** . Substitua **{locatário}** pelo nome do seu locatário (por exemplo, contoso.onmicrosoft.com). Clique em **Salvar**. O valor de **{locatário}** diferencia maiusculas de minúsculas.

    ![Fornecendo origens de JavaScript e retornar URLs para seu novo aplicativo na Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>Configurar o Amazon como um provedor de identidade em seu locatário

1. Siga estas etapas para [navegar até a lâmina de recursos B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. Na lâmina B2C recursos, clique em **provedores de identidade**.
3. Clique em **+ Adicionar** na parte superior da lâmina.
4. Fornece um **nome** de amigável para a configuração do provedor de identidade. Por exemplo, insira "Amzn".
5. Clique em **tipo de provedor de identidade**, selecione **Amazon**e clique em **Okey**.
6. Clique em **configurar esse provedor de identidade** e insira a ID do cliente e o segredo de cliente do aplicativo Amazon que você criou anteriormente.
7. Clique em **Okey** e, em seguida, clique em **criar** para salvar sua configuração Amazon.
