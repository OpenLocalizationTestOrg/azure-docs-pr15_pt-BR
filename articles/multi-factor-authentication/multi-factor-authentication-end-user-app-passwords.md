<properties
    pageTitle="O que são senhas de aplicativo no Azure MFA?"
    description="Esta página ajudará os usuários a entender o que são senhas de aplicativos e que elas são usadas com relação a MFA do Azure."
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
    ms.date="08/04/2016"
    ms.author="kgremban"/>



# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>O que são senhas de aplicativo no Azure multi-Factor Authentication?

Determinados aplicativos não-localizador, como o cliente de email nativo de Apple que usa o Exchange Active Sync, atualmente não dão suporte a autenticação multifator. Autenticação multifator é habilitada por usuário. Isso significa que se um usuário tiver sido habilitado para autenticação multifator e tentar usar os aplicativos de navegador não, eles não poderão fazer isso. Uma senha de aplicativo permite que isso ocorra.

>[AZURE.NOTE] Autenticação moderna para os clientes do Office 2013
>
> Agora, os clientes do Office 2013 (incluindo Outlook) novos protocolos de autenticação de suporte e podem ser habilitados para dar suporte a autenticação multifator.  Isso significa que, quando ativada, senhas de aplicativos não são necessárias para uso com clientes do Office 2013.  Para obter mais informações, consulte [demonstração de pública de autenticação moderno do Office 2013 anunciada](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

## <a name="how-to-use-app-passwords"></a>Como usar senhas de aplicativos

A seguir estão alguns itens a lembrar sobre como usar senhas de aplicativos.

- A senha real é gerada automaticamente e não for fornecida pelo usuário. Isso ocorre porque a senha gerada automaticamente, é mais difícil para um invasor a descoberta e é mais seguro.
- Atualmente não há um limite de 40 senhas por usuário. Se você tentar criar uma após você atingiu o limite, você será solicitado para excluir uma de suas senhas de aplicativo existente para criar um novo.
- É recomendável que as senhas de aplicativo criado por dispositivo e não por aplicativo. Por exemplo, você pode criar uma senha de aplicativo para o seu laptop e usar essa senha de aplicativo para todos os aplicativos em que laptop.
- Você recebe uma senha de aplicativo na primeira vez que você entrar.  Se você precisar aquelas adicionais, você pode criá-los.

![Configuração](./media/multi-factor-authentication-end-user-app-passwords/app.png)

Quando você tiver uma senha de aplicativo, você pode usar esse no lugar de sua senha original com esses aplicativos não-localizador.  Portanto, por exemplo, se você estiver usando autenticação multifator e o cliente de email nativo do Apple no seu telefone.  Use a senha de aplicativo para que ele possa ignorar autenticação multifator e continuar a trabalhar.

## <a name="creating-and-deleting-app-passwords"></a>Criando e excluindo senhas de aplicativos
Durante sua entrar inicial recebem uma senha de aplicativo que você pode usar.  Além disso você também pode criar e excluir senhas de aplicativo mais tarde.  Como fazer isso depende de como você usa autenticação multifator.  Escolha o que mais se aplica a você.

Como usar autenticação multifator|Descrição
:------------- | :------------- |
[Usá-lo com o Office 365](#creating-and-deleting-app-passwords-with-office-365)|  Isso significa que você deseja criar senhas de aplicativo por meio do portal do Office 365.
[Eu não sei](#creating-and-deleting-app-passwords-with-myapps-portal)|Isso significa que você irá querer criar senhas de aplicativo por meio de [https://myapps.microsoft.com](https://myapps.microsoft.com)
[Usá-lo com o Microsoft Azure](#create-app-passwords-in-the-azure-portal)| Isso significa que você desejará criar senhas de aplicativo por meio do portal Azure.

## <a name="creating-and-deleting-app-passwords-with-office-365"></a>Criando e excluindo senhas de aplicativos com o Office 365

Se você usa autenticação multifator com o Office 365, você desejará criar e excluir senhas de aplicativos por meio do portal do Office 365.

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Criar senhas de aplicativos no portal do Office 365
--------------------------------------------------------------------------------

1. Faça logon no [portal do Office 365](https://login.microsoftonline.com/).
2. No canto superior direito selecione o widget e escolha configurações do Office 365.
3. Clique em verificação de segurança adicional.
4. À direita, clique no link que diz **Atualizar meus números de telefone usados para segurança da conta.** 
 ![Instalação](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Você será levado para a página que permitirá que você alterar suas configurações.
![Nuvem](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Na parte superior, ao lado de verificação de segurança adicionais, clique em **senhas de aplicativos.**
7. Clique em **criar**.
![Nuvem](./media/multi-factor-authentication-end-user-app-passwords-create-o365/apppass.png)
8. Insira um nome para a senha do aplicativo e clique em **Avançar**.
![Criar uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
9. Copiar a senha de aplicativo na área de transferência e colá-lo em seu aplicativo.
![Criar uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/create2.png)


### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Para excluir senhas de aplicativo usando o portal do Office 365
--------------------------------------------------------------------------------


1. Faça logon no [portal do Office 365](https://login.microsoftonline.com/).
2. No canto superior direito selecione o widget e escolha configurações do Office 365.
3. Clique em verificação de segurança adicional.
4. À direita, clique no link que diz **Atualizar meus números de telefone usados para segurança da conta.** 
 ![Instalação](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Você será levado para a página que permitirá que você alterar suas configurações.
![Nuvem](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Na parte superior, ao lado de verificação de segurança adicionais, clique em **senhas de aplicativos.**
7. Ao lado da senha do aplicativo que você deseja excluir, clique em **Excluir**.
![Excluir uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
8. Confirme a exclusão clicando em **Sim**.
![Confirmar a exclusão](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
9. Depois que a senha de aplicativo será excluída clique em **Fechar**.
![Fechar](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## <a name="creating-and-deleting-app-passwords-with-myapps-portal"></a>Criando e excluindo senhas de aplicativos com o portal de meus aplicativos.
Se você não tiver certeza de como usar autenticação multifator, em seguida, você pode sempre criar e excluir senhas de aplicativo por meio do portal de meus aplicativos.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Para criar uma senha de aplicativo usando o portal de meus aplicativos

1. Entrar no [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Na parte superior, selecione o perfil.
3. Selecione verificação de segurança adicional.
![Nuvem](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Você será levado para a página que permitirá que você alterar suas configurações.
![Configuração](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. Na parte superior, ao lado de verificação de segurança adicionais, clique em **senhas de aplicativos.**
6. Clique em **criar**.
![Criar uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/create3.png)
7. Insira um nome para a senha do aplicativo e clique em **Avançar**.
![Criar uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
8. Copiar a senha de aplicativo na área de transferência e colá-lo em seu aplicativo.
![Criar uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Para excluir uma senha de aplicativo usando o portal de meus aplicativos

1. Entrar no [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Na parte superior, selecione o perfil.
3. Selecione verificação de segurança adicional.
![Nuvem](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Você será levado para a página que permitirá que você alterar suas configurações.
![Configuração](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. Na parte superior, ao lado de verificação de segurança adicionais, clique em **senhas de aplicativos.**
6. Ao lado da senha do aplicativo que você deseja excluir, clique em **Excluir**.
![Excluir uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
7. Confirme a exclusão clicando em **Sim**.
![Confirmar a exclusão](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
8. Depois que a senha de aplicativo será excluída clique em **Fechar**.
![Fechar](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## <a name="create-app-passwords-in-the-azure-portal"></a>Criar senhas de aplicativos no portal do Azure

Se você usa autenticação multifator com Azure deseja criar senhas de aplicativos por meio do portal Azure.

### <a name="to-create-app-passwords-in-the-azure-portal"></a>Criar senhas de aplicativo no portal do Azure

1. Entrar no portal de gerenciamento do Azure.
2. Na parte superior, clique com botão direito no seu nome de usuário e selecione verificação de segurança adicional.
3. Na página proofup, na parte superior, selecione senhas de aplicativos
4. Clique em **criar**.
5. Insira um nome para a senha do aplicativo e clique em **Avançar**
6. Copiar a senha de aplicativo na área de transferência e colá-lo em seu aplicativo.
![Nuvem](./media/multi-factor-authentication-end-user-app-passwords-create-azure/app2.png)

### <a name="to-delete-app-passwords-in-the-azure-portal"></a>Para excluir senhas de aplicativos no portal do Azure

1. Entrar no portal de gerenciamento do Azure.
2. Na parte superior, clique com botão direito no seu nome de usuário e selecione verificação de segurança adicional.
3. Na parte superior, ao lado de verificação de segurança adicionais, clique em **senhas de aplicativos.**
4. Ao lado da senha do aplicativo que você deseja excluir, clique em **Excluir**.
5. Confirme a exclusão clicando em **Sim**.
6. Depois que a senha de aplicativo será excluída clique em **Fechar**.
![Fechar](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)
