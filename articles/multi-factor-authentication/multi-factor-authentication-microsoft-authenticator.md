<properties
    pageTitle="Aplicativo Microsoft Authenticator para celulares | Microsoft Azure"
    description="Saiba como atualizar para a versão mais recente do Azure autenticador."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kgremban"/>

# <a name="microsoft-authenticator"></a>Microsoft autenticador

O aplicativo Microsoft Authenticator fornece um nível adicional de segurança em sua conta do Microsoft Azure (por exemplo, bsimon@contoso.onmicrosoft.com), conta corporativa do seu local (por exemplo, bsimon@contoso.com), ou sua conta da Microsoft (por exemplo, bsimon@outlook.com).

O aplicativo funciona de duas maneiras:

- **Notificação**. O aplicativo pode ajudar a impedir o acesso não autorizado às contas e interromper transações fraudulentas enviando uma notificação para seu smartphone ou tablet. Simplesmente exibir a notificação e se ele for legítimo, selecione **Confirmar**. Caso contrário, você pode selecionar **Negar**. Para obter informações sobre a rejeição de notificações, consulte como usar o recurso de fraude de relatório e negar para autenticação multifator.

- **Senha com código de verificação**. O aplicativo pode ser usado como um token de software para gerar um código de verificação de OAuth. Insira o código fornecido pelo aplicativo para a tela de entrada, juntamente com o nome de usuário e senha, quando solicitado. O código de verificação fornece um segundo formulário de autenticação.

Com o lançamento do aplicativo Microsoft Authenticator, o antigo aplicativo autenticador Azure está sendo substituído.  O aplicativo do Azure autenticador continuarão a funcionar, mas se você decidir mover para o novo aplicativo Microsoft Authenticator, este artigo pode ajudá-lo.  

## <a name="install-the-app"></a>Instalar o aplicativo

O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="add-accounts-to-the-app"></a>Adicionar contas ao aplicativo

Para cada conta que você deseja adicionar ao aplicativo do Microsoft Authenticator, use um dos procedimentos a seguir.

### <a name="add-an-account-to-the-app-by-using-the-qr-code-scanner"></a>Adicionar uma conta ao aplicativo usando o scanner de código QR

1. Vá para a tela de configurações de verificação de segurança.  Para obter informações sobre como acessar esta tela, consulte [alterando as configurações de segurança](multi-factor-authentication-end-user-manage-settings.md).

2. Selecione **Configurar**.

    ![O botão Configurar na tela de configurações de verificação de segurança](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

    Isto traz uma tela com um código QR nele.

    ![Tela que fornece o código QR](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Abra o aplicativo Microsoft autenticador. Na tela **contas** , selecione **+**e em seguida, especifique o que você deseja adicionar uma conta corporativa ou escolar.

    ![A tela de contas com sinal de adição](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

    ![Tela para especificar uma conta corporativa ou escolar](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. Use a câmera para examinar o código QR e selecione **Concluir** para fechar a tela de código QR.

    ![Tela de verificação Message](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

    Se sua câmera não está funcionando corretamente, você pode inserir o código QR e a URL manualmente. Para obter mais informações, consulte [Adicionar uma conta ao aplicativo manualmente](#add-an-account-to-the-app-manually).

5. Aguarde enquanto a conta está ativada. Quando concluir a ativação, selecione **contato-me**.  Isso envia uma notificação ou um código de verificação para o seu telefone.  Selecione **Verificar**.

    ![Tela onde você pode selecionar verificar entrar](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

6. Se sua empresa requer um PIN para aprovar a verificação de entrada, insira-o.

    ![Caixa para inserir um PIN](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

7. Após a conclusão da entrada PIN, selecione **Fechar**. Neste ponto, sua verificação deve ser bem-sucedida.
8. Recomendamos que você insira seu número de telefone celular caso perca o acesso ao seu aplicativo. Especifique seu país na lista suspensa e insira seu número de telefone celular na caixa ao lado do nome de país/região. Selecione **Avançar**.
9. Neste ponto, você configurou seu método de contato. Agora é hora de definir senhas de aplicativo para aplicativos não-localizador, como o Outlook 2010 ou mais antigo. Se você não usa esses aplicativos, selecione **concluído**. Caso contrário, prossiga para a próxima etapa.

    ![Tela para a criação de uma senha de aplicativo](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

10. Se você estiver usando aplicativos não-localizador, copie a senha de aplicativo fornecido e cole a senha em seus aplicativos. Para obter etapas sobre aplicativos individuais como o Outlook e o Lync, veja como alterar a senha em seu email para a senha do aplicativo e como alterar a senha em seu aplicativo para a senha do aplicativo.
11. Selecione **concluído**.

Agora você deve ver a nova conta na tela **contas** .

![Tela de contas](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Adicionar uma conta ao aplicativo manualmente

1. Vá para a tela de configurações de verificação de segurança.  Para obter informações sobre como acessar esta tela, consulte [alterando as configurações de segurança](multi-factor-authentication-end-user-manage-settings.md).

2. Selecione **Configurar**.

    ![O botão Configurar na tela de configurações de verificação de segurança](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

    Isto traz uma tela com um código QR nele.  Observe o código e a URL.

    ![Tela que fornece o código QR e a URL](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Abra o aplicativo Microsoft autenticador. Na tela **contas** , selecione **+**e em seguida, especifique o que você deseja adicionar uma conta corporativa ou escolar.

    ![A tela de contas com sinal de adição](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

    ![Tela para especificar uma conta corporativa ou escolar](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. No scanner, selecione **Inserir código manualmente**.

    ![Tela de verificação Message](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

5. Insira o código e a URL nas caixas apropriadas no aplicativo.

    ![Tela para inserir código e URL](./media/multi-factor-authentication-azure-authenticator/manual.png)

    ![Tela para inserir código e URL](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

6. Aguarde enquanto a conta está ativada. Quando concluir a ativação, selecione **contato-me**. Isso envia uma notificação ou um código de verificação para o seu telefone. Selecione **Verificar**.

Agora você deve ver a nova conta na tela **contas** .

![Tela de contas](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### <a name="add-an-account-to-the-app-by-using-touch-id"></a>Adicionar uma conta ao aplicativo usando ID de toque

O aplicativo Microsoft Authenticator IOS dá suporte a ID de toque.  Azure autenticação multifator permite que as organizações exigir um PIN para dispositivos. Com a ID de toque, os usuários do iOS não é necessário digitar um PIN. Em vez disso, eles podem digitalizar sua impressão digital e selecione **Aprovar**.

Configurando a identificação de toque com Microsoft Authenticator é simple. Você concluir um desafio de verificação normal com um PIN. Se seu dispositivo tem suporte para toque ID, Microsoft Authenticator será configurá-lo automaticamente para essa conta.

![Verificação de configuração de ID de toque](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

Desse ponto em diante, quando você estiver necessários para verificar seu entrar, selecione a notificação de push recebido e digitalizar sua impressão digital em vez de inserir seu PIN.

![Notificação de envio](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## <a name="uninstall-the-old-azure-authentication-app"></a>Desinstalar o antigo aplicativo de autenticação do Azure

Depois de adicionar todas as contas para o novo aplicativo, você pode desinstalar o antigo aplicativo em seu telefone.

## <a name="delete-an-account"></a>Excluir uma conta

Para remover uma conta do aplicativo Microsoft Authenticator, selecione a conta e selecione **Excluir**.

![Botão Excluir](./media/multi-factor-authentication-azure-authenticator/remove.png)
