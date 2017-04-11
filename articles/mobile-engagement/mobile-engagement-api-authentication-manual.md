<properties 
    pageTitle="Autenticar com APIs REST do contrato Mobile - configuração manual"
    description="Descreve como configurar manualmente a autenticação para celular contrato restante APIs" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="08/19/2016"
    ms.author="piyushjo"/>

# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Autenticar com APIs REST do contrato Mobile - configuração manual

Esta é uma documentação de apêndice autenticar [com Mobile contrato restante APIs](mobile-engagement-api-authentication.md). Verifique se que você lê-lo primeiro para obter o contexto. Descreve uma maneira alternativa de fazer a configuração única para configurar a autenticação para as APIs de restante de contrato Mobile usando o Portal do Azure. 

>[AZURE.NOTE] As instruções abaixo são com base neste [Guia do Active Directory](../resource-group-create-service-principal-portal.md) e personalizadas para o que é necessário para autenticação para APIs de contrato de celular. Portanto consultá-lo se desejar entender as etapas abaixo em detalhes. 

1. Faça login à sua conta do Azure por meio do [portal clássico](https://manage.windowsazure.com/).

2. Selecione o **Active Directory** no painel esquerdo.

     ![Selecione Active Directory][1]

3. Escolha o **Padrão Active Directory** em seu portal Azure. 

     ![Escolher diretório][2]

    >[AZURE.IMPORTANT] Essa abordagem funciona somente quando você estiver trabalhando no Active Directory da sua conta padrão e não funcionará se você estiver fazendo isso em um Active Directory que você criou na sua conta. 

4. Para exibir os aplicativos no seu diretório, clique em **aplicativos**.

     ![Exibir aplicativos][3]

5. Clique em **Adicionar**. 

     ![Adicionar aplicativo][4]

6. Clique em **Adicionar um aplicativo minha organização está desenvolvendo**

     ![novo aplicativo][5]

6. Preencha o nome do aplicativo e selecione o tipo de aplicativo como **WEB APPLICATION e/ou WEB API** e clique no botão Avançar.

     ![aplicativo de nome][6]

7. Você pode fornecer quaisquer URLs fictícios para **URL de SIGN-ON** e **URI de ID do aplicativo**. Eles não são usados para o nosso cenário e as URLs próprios não são validadas.  

     ![Propriedades de aplicativo][7]

8. No final deste, você terá um aplicativo AAD com o nome fornecido anteriormente como a seguir. Este é seu **AD\_aplicativo\_nome** e tome nota dele.  

     ![nome do aplicativo][8]

9. Clique no nome do aplicativo e clique em **Configurar**.

     ![Configurar o aplicativo][9]

10. Anote a ID do cliente que será usado como **cliente\_ID** para sua API chamadas. 

     ![Configurar o aplicativo][10]

11. Role até a seção de **chaves** e adicionar uma chave com preferência duração de 2 anos (expiração) e clique em **Salvar**. 

     ![Configurar o aplicativo][11]


12. Copie o valor que é mostrado para a chave como ele só é exibido agora e não está armazenado para que elas não serão exibidas nunca novamente imediatamente. Se você esquecê-la, em seguida, será necessário gerar uma nova chave. Este será o **CLIENT_SECRET** para as chamadas de API. 

     ![Configurar o aplicativo][12]

    >[AZURE.IMPORTANT] Esta tecla expirará no final da duração especificada para que verifique se você renová-la quando chegar a hora caso contrário, a autenticação de API não funcione mais. Também é possível excluir e recriar essa chave se você acha que ele foi comprometido.
 
13. Clique no botão de **Pontos de EXTREMIDADE do modo de exibição** agora que abrirá a caixa de diálogo de **Pontos de extremidade do aplicativo** . 

    ![][13]

14. Na caixa de diálogo de pontos de extremidade do aplicativo, copie o **Ponto de EXTREMIDADE TOKEN OAUTH 2.0**. 

    ![][14]

15. Neste ponto de extremidade será da seguinte forma onde o GUID na URL é seu **TENANT_ID** portanto anote-la: 

        https://login.microsoftonline.com/<GUID>/oauth2/token

16. Agora, podemos continuará para configurar as permissões neste aplicativo. Para isso, você terá que abrir o [portal do Azure](https://portal.azure.com). 

17. Clique em **Grupos de recursos** e localizar o grupo de recursos do **Contrato de celular** .  

    ![][15]

18. Clique no grupo de recursos do **Contrato de celular** e navegue até a lâmina **configurações** aqui. 

    ![][16]

19. Clique em **usuários** na lâmina configurações e clique em **Adicionar** para adicionar um usuário. 

    ![][17]

20. Clique em **Selecionar uma função**

    ![][18]

21. Clique em **proprietário**

    ![][19]

22. Procure o nome do seu aplicativo **AD\_aplicativo\_nome** na caixa de pesquisa. Você não verá isto por padrão aqui. Quando você encontrá-la, selecioná-la e clique em **Selecione** na parte inferior da lâmina. 

    ![][20]

23. Na lâmina **Adicionar acesso** , ele aparecerá como **1 usuário, grupos de 0**. Clique em **Okey** neste blade para confirmar a alteração. 

    ![][21]

Agora você concluiu a configuração de AAD necessária e você está tudo pronto para chamar as APIs. 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



