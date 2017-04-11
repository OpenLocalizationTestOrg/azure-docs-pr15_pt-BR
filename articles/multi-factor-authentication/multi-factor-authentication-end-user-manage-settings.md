<properties
    pageTitle="Gerenciar suas configurações de verificação em duas etapas | Microsoft Azure"
    description="Gerencie como usar o Azure multi-Factor Authentication, inclusive alterar suas informações de contato ou configurando seus dispositivos."
    services="multi-factor-authentication"
    keywords = "cliente de autenticação multifator, problemas de autenticação, ID de correlação"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="manage-your-settings-for-two-step-verification"></a>Gerenciar suas configurações de verificação em duas etapas

Este artigo responde perguntas sobre como atualizar as configurações de autenticação de verificação ou multifator de duas etapas. Se você estiver tendo problemas para entrar em sua conta, consulte [está tendo problemas com a verificação](multi-factor-authentication-end-user-troubleshoot.md) para ajuda de solução de problemas.


## <a name="where-to-find-the-settings-page"></a>Onde encontrar a página de configurações
Dependendo de como sua empresa configurado Azure multi-Factor Authentication, há alguns lugares onde você pode alterar as configurações como o número do seu telefone.

Se seu administrador de TI enviado uma URL específica ou etapas para gerenciar a verificação, siga as instruções. Caso contrário, as instruções a seguir devem funcionar para todas as outras pessoas. Se você seguir essas etapas, mas não vê as mesmas opções, isso significa que seu trabalho ou escola personalizou seu próprios portal. Peça ao seu administrador para o link para o seu portal de autenticação multifator de Azure.


1. Entrar no [https://myapps.microsoft.com](https://myapps.microsoft.com)  
2. Na parte superior, selecione o **perfil**.  
3. Selecione **verificação de segurança adicional**.  

    ![Meus aplicativos](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. A página de verificação de segurança adicional carrega com suas configurações.

    ![Proofup](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)


## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Quero alterar meu número de telefone ou adicionar um número secundário

É importante configurar um número de telefone de autenticação secundária.  Como seu número de telefone primário e seu aplicativo móvel são provavelmente no mesmo telefone, o número de telefone secundário é a única maneira que você poderá voltar para sua conta se seu telefone é perdido ou roubado.

> [AZURE.NOTE]
> Se você não tem acesso a seu número de telefone primário e precisar de ajuda sua conta, consulte nossos tópicos de ajuda na [está tendo problemas com a verificação](multi-factor-authentication-end-user-troubleshoot.md).

**Para alterar o número de telefone principal:**  

1. Na página de verificação de segurança adicional, marque a caixa de texto com seu número de telefone atual e editá-lo com seu novo número de telefone.  
2. Selecione **Salvar**.  
3. Se este é o número que você usa para sua opção de verificação preferencial, você precisa verificar o número da nova antes de você pode salvá-lo.  


**Para adicionar um número de telefone secundário:**  

1. Na página de verificação de segurança adicional, marque a caixa ao lado **telefone de autenticação alternativo.**  
2. Insira seu número de telefone secundário na caixa de texto.  
3. Selecione **Salvar** e suas alterações são concluídas.  


## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>Como limpar Microsoft Authenticator em meu dispositivo antigo e mover para um novo?
Quando você desinstalar o aplicativo do seu dispositivo ou redefine o dispositivo, ele não remove a ativação no back-end. Você deve usar as etapas descritas em [Mover para um novo dispositivo](multi-factor-authentication-microsoft-authenticator.md#how-to-move-to-the-new-microsoft-authenticator-app).

## <a name="next-steps"></a>Próximas etapas
- Obtenha dicas de solução de problemas e ajuda sobre [está tendo problemas com a verificação](multi-factor-authentication-end-user-troubleshoot.md)
- Configure [senhas de aplicativos](multi-factor-authentication-end-user-app-passwords.md) para os aplicativos que não há suporte para verificação em duas etapas.
