<properties
    pageTitle="Configurar a verificação de minha conta corporativa ou escolar"
    description="Quando sua empresa configura autenticação multifator do Azure, você será solicitado a inscrever para verificação em duas etapas. Saiba como configurá-lo. "
    services="multi-factor-authentication"
    keywords="como usar o directory do azure, do active directory na nuvem, tutorial do active directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="pblachar"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="set-up-my-account-for-two-step-verification"></a>Configurar minha conta para verificação em duas etapas

A verificação é uma etapa de segurança adicional que ajuda a proteger sua conta, tornando mais difícil para outras pessoas a quebra na. Se você estiver lendo este artigo, você provavelmente tem um email do seu administrador do trabalho ou da escola sobre autenticação multifator. Ou talvez você tentou entrar e você tem uma mensagem pedindo para configurar a verificação de segurança adicional. Se for esse o caso, **que você não conseguir entrar, até concluir o processo de inscrição automática**.

Este artigo ajuda você a configurar seu **trabalho ou escola conta**. Se você deseja habilitar a verificação de seu próprio, conta pessoal da Microsoft, consulte [sobre verificação em duas etapas](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="determine-how-you-will-use-multi-factor-authentication"></a>Determinar como você usará a autenticação multifator

Verificação em duas etapas funciona solicitando que você para duas partes de identificação quando você entrar. Primeiro, solicitamos seu nome de usuário e senha como de costume. Em seguida, contato um telefone que sabemos pertence a você e você Confirmamos que a tentativa de entrada foi legítima.  

Para começar a usar o processo de instalação, tente entrar sua conta como faz normalmente. Se o administrador configurou sua conta para verificação em duas etapas, você será solicitado para começar o processo de inscrição automática. Comece este processo clicando em **configurá-lo agora.**

![Configuração](./media/multi-factor-authentication-end-user-first-time/first.png)

A primeira pergunta no processo de inscrição é como você deseja entrar em contato com você. Examine as opções na tabela e use os links para ir para as etapas de configuração para cada método.

| Método de contato | Descrição |
| --- | --- |
[Aplicativo móvel](#use-a-mobile-app-as-the-contact-method) | - **Receba notificações para verificação.** Esta opção envia uma notificação para o aplicativo de autenticador em seu smartphone ou tablet. Exibir a notificação e, se ele for legítimo, selecione **autenticar** no aplicativo. Seu trabalho ou escola pode exigir que você insira um PIN antes de você se autenticar.<br>- **Use o código de verificação.** Nesse modo, o aplicativo de autenticador gera um código de verificação que atualiza a cada 30 segundos. Insira o código de verificação mais atual da interface de entrada.<br>O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
[Chamada do telefone celular ou texto](#use-your-mobile-phone-as-the-contact-method) | - **Chamada telefônica** coloca uma chamada de voz automatizada para o número de telefone que você fornecer. Atender a chamada e pressione # o teclado do telefone para autenticar.<br>- **Mensagem de texto** termina uma mensagem de texto que contém um código de verificação. Após o prompt no texto, ou responder a mensagem de texto ou insira o código de verificação fornecido na interface de entrada. |  
[Telefonema do Office](#use-your-office-phone-as-the-contact-method) | Coloca uma chamada de voz automatizada para o número de telefone que você fornecer. Atender a chamada e pressiona # no teclado telefônico para autenticar. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Usar um aplicativo móvel como o método de contato

Usando esse método requer que você instale um aplicativo de autenticador em seu telefone ou tablet. As etapas neste artigo são baseadas no aplicativo do Microsoft Authenticator, que está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

1. Selecione o **aplicativo móvel** na lista suspensa.
2. Selecione **receber notificações para verificação** ou **código de verificação de uso**e, em seguida, selecione **Configurar**.

    ![Tela de verificação de segurança adicional](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

3. Em seu telefone ou tablet, abra o aplicativo e selecione **+** para adicionar uma conta. (Em dispositivos Android, selecione os três pontos, em seguida, **Adicionar conta**).
4. Especifica que você deseja adicionar uma conta corporativa ou escolar. Abre o scanner de código QR em seu telefone. Se sua câmera não está funcionando corretamente, você pode optar por inserir informações da sua empresa manualmente. Para obter mais informações, consulte [Adicionar uma conta manualmente](#add-an-account-manually).  
5. Digitalize a imagem de código QR que aparecia com a tela para configurar o aplicativo móvel.  Selecione **concluído** para fechar a tela de código QR.  

    ![Tela do código QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

6. Quando concluir a ativação por telefone, selecione **contato-me**.  Esta etapa envia uma notificação ou um código de verificação para o seu telefone. Selecione **Verificar**.  
7. Se sua empresa requer um PIN para aprovar a verificação de entrada, insira-o.

    ![Caixa para inserir um PIN](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

8. Após a conclusão da entrada PIN, selecione **Fechar**. Neste ponto, sua verificação deve ser bem-sucedida.
9. Recomendamos que você insira seu número de telefone celular caso perca o acesso ao seu aplicativo móvel. Especifique seu país na lista suspensa e insira seu número de telefone celular na caixa ao lado do nome de país/região. Selecione **Avançar**.
10. Neste ponto, você será solicitado a configurar senhas de aplicativos para os não-localizador aplicativos como o Outlook 2010 ou mais antigo ou o aplicativo de email nativos em dispositivos da Apple. Isso ocorre porque alguns aplicativos não há suporte para verificação em duas etapas. Se você não usa esses aplicativos, clique em **Concluir** e ignore o restante das etapas.
11. Se você estiver usando esses aplicativos, copiar a senha de aplicativo fornecido e colá-lo em seu aplicativo em vez de sua senha regular. Você pode usar a mesma senha de aplicativo para vários aplicativos. Para obter mais informações, [ajuda com senhas de aplicativo].
12. Clique em **concluído**.


### <a name="add-an-account-manually"></a>Adicionar uma conta manualmente
Se você quiser adicionar uma conta ao aplicativo móvel manualmente, em vez de usar o leitor de QR, siga estas etapas.

1. Selecione o botão **Inserir conta manualmente** .  
2. Insira o código e a URL que são fornecidas na mesma página que mostra o código de barras. Essas informações prossegue nas caixas **código** e **URL** o aplicativo móvel.

    ![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

3. Quando tiver concluído a ativação, selecione **contato-me**. Esta etapa envia uma notificação ou um código de verificação para o seu telefone. Selecione **Verificar**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Usar seu telefone celular como o método de contato

1. Selecione o **Telefone de autenticação** na lista suspensa.  

    ![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-phone/phone.png)  

2. Escolha seu país na lista suspensa e insira seu número de telefone celular.
3. Selecione o método que prefere usar com o seu telefone celular - texto ou chamada.
4. Selecione **contato-me** para verificar seu número de telefone. Dependendo do modo selecionado, podemos enviar um texto ou ligue para você. Siga as instruções fornecidas na tela e, em seguida, selecione **Verificar**.
5. Neste ponto, você será solicitado a configurar senhas de aplicativos para os não-localizador aplicativos como o Outlook 2010 ou mais antigo ou o aplicativo de email nativos em dispositivos da Apple. Isso ocorre porque alguns aplicativos não há suporte para verificação em duas etapas. Se você não usa esses aplicativos, clique em **Concluir** e ignore o restante das etapas.
6. Se você estiver usando esses aplicativos, copiar a senha de aplicativo fornecido e colá-lo em seu aplicativo em vez de sua senha regular. Você pode usar a mesma senha de aplicativo para vários aplicativos. Para obter mais informações, [ajuda com senhas de aplicativo].
7. Clique em **concluído**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Usar seu telefone do office como o método de contato

1. Selecione **Office telefone** na lista suspensa  

    ![Configuração](./media/multi-factor-authentication-end-user-first-time-office-phone/office.png)  

2. A caixa de número de telefone é preenchida automaticamente com suas informações de contato da empresa. Se o número estiver incorreto ou ausente, peça ao seu administrador para fazer alterações.
4. Selecionar **contato-me** para verificar seu número de telefone, e podemos chamará seu número. Siga as instruções fornecidas na tela e, em seguida, selecione **Verificar**.
5. Neste ponto, você será solicitado a configurar senhas de aplicativos para os não-localizador aplicativos como o Outlook 2010 ou mais antigo ou o aplicativo de email nativos em dispositivos da Apple. Isso ocorre porque alguns aplicativos não há suporte para verificação em duas etapas. Se você não usa esses aplicativos, clique em **Concluir** e ignore o restante das etapas.
6. Se você estiver usando esses aplicativos, copiar a senha de aplicativo fornecido e colá-lo em seu aplicativo em vez de sua senha regular. Você pode usar a mesma senha de aplicativo para vários aplicativos. Para obter mais informações, consulte [o que são senhas de aplicativos](multi-factor-authentication-end-user-app-passwords.md).
7. Clique em **concluído**.

## <a name="next-steps"></a>Próximas etapas

- Alterar as opções de preferenciais e [Gerenciar suas configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md)
- Configure [senhas de aplicativos](multi-factor-authentication-end-user-app-passwords.md) para aplicativos de dispositivo nativo que não há suporte para verificação em duas etapas.
- Confira o [aplicativo Microsoft autenticador](multi-factor-authentication-microsoft-authenticator.md) para autenticação de forma rápida e segura, mesmo quando você não tiver o serviço de célula.
