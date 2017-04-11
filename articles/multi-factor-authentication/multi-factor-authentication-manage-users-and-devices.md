<properties 
    pageTitle="Relatórios do Azure autenticação multifator"
    description="Descreve como alterar as configurações de usuário como forçar os usuários façam o processo de prova novamente."
    documentationCenter=""
    services="multi-factor-authentication"
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gerenciando configurações de usuário com o Azure multi-Factor Authentication na nuvem

Como administrador, você pode gerenciar as configurações de usuários e dispositivos a seguir.  

- [Exigir que os usuários selecionados fornecer métodos de contato novamente](#require-selected-users-to-provide-contact-methods-again)
- [Excluir usuários existentes senhas de aplicativos](#delete-users-existing-app-passwords)
- [Restaurar MFA em todos os dispositivos suspensos para um usuário](#restore-mfa-on-all-suspended-devices-for-a-user)






Isso é útil se um computador ou dispositivo seja perdido ou roubado ou que você precisa remover um acesso de usuários.


## <a name="require-selected-users-to-provide-contact-methods-again"></a>Exigir que os usuários selecionados fornecer métodos de contato novamente

Esta configuração será força o usuário para concluir o processo de registro novamente quando ele ou ela entra. Lembre-se de que os aplicativos de navegador não vai continuar a funcionar se o usuário tiver senhas de aplicativos para eles.  Você pode excluir as senhas de aplicativo de usuários selecionando também **Excluir todas as senhas de aplicativo existente geradas por usuários selecionados**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Como exigir que os usuários fornecer métodos de contato novamente




1. Entrar no portal de clássico do Azure.
2. À esquerda, clique em Active Directory.
3. Em, diretório clique no diretório para o usuário que você deseja exigir para fornecer o seu método de contato novamente.
4. Na parte superior, clique em usuários.
5. Na parte inferior da página, clique em Gerenciar multifator autenticação Isso abrirá a página de autenticação multifator.
6. Localize o usuário que você deseja gerenciar e coloque uma seleção na caixa localizada ao lado do nome. Talvez você precise alterar o modo de exibição na parte superior.
7. Isso abrirá o link **Gerenciar configurações de usuário** à direita. Clique nele.
8. Coloque uma verificação em **exigir que os usuários selecionados fornecer métodos de contato novamente**.
![Fornecer métodos de contato](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
10. Clique em Salvar.
11. Clique em Fechar

## <a name="delete-users-existing-app-passwords"></a>Excluir usuários existentes senhas de aplicativos

Exclui todas as senhas de aplicativo que o usuário tiver criado. Aplicativos de navegador não que estavam associados essas senhas de aplicativo deixará trabalhar até uma nova senha de aplicativo é criada.

### <a name="how-to-delete-users-existing-app-passwords"></a>Como excluir usuários existentes senhas de aplicativos

1. Entrar no portal de clássico do Azure.
2. À esquerda, clique em Active Directory.
3. Em, diretório clique no diretório para o usuário que você deseja excluir senhas de aplicativo para.
4. Na parte superior, clique em usuários.
5. Na parte inferior da página, clique em Gerenciar multifator autenticação Isso abrirá a página de autenticação multifator.
6. Localize o usuário que você deseja gerenciar e coloque uma seleção na caixa localizada ao lado do nome. Talvez você precise alterar o modo de exibição na parte superior.
7. Isso abrirá o link **Gerenciar configurações de usuário** à direita. Clique nele.
8. Coloque uma verificação em **Excluir todas as senhas de aplicativo existente geradas por usuários selecionados**.
![Excluir senhas de aplicativo](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
10. Clique em Salvar.
10. Clique em Fechar.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Restaurar MFA em todos os dispositivos lembrados para um usuário

Os administradores têm a capacidade de restaurar autenticação multifator em navegadores e dispositivos de usuários. Ao fazer isso, isso removerá a lembrar MFA todos os dispositivos do usuário e navegadores e o usuário serão necessária para usar MFA ao assinar na próxima vez.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Como restaurar MFA em todos os dispositivos suspensos para um usuário

1. Entrar no portal de clássico do Azure.
2. À esquerda, clique em Active Directory.
3. Em, diretório clique no diretório para o usuário que você deseja restaurar mfa em.
4. Na parte superior, clique em usuários.
5. Na parte inferior da página, clique em Gerenciar multifator autenticação Isso abrirá a página de autenticação multifator.
6. Localize o usuário que você deseja gerenciar e coloque uma seleção na caixa localizada ao lado do nome. Talvez você precise alterar o modo de exibição na parte superior.
7. Isso abrirá o link **Gerenciar configurações de usuário** à direita. Clique nele.
8. Colocar uma verificação em **restaurar a autenticação multifator em todos os dispositivos lembradas**
![excluir senhas de aplicativo](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. Clique em Salvar.
10. Clique em Fechar.
