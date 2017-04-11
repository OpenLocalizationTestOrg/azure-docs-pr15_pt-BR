<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com o Facebook no trabalho | Microsoft Azure"
    description="Saiba como configurar o logon único entre Azure Active Directory e do Facebook no trabalho."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/26/2016"
    ms.author="asmalser"/>


# <a name="tutorial-azure-active-directory-integration-with-facebook-at-work"></a>Tutorial: Integração com o Active Directory do Azure com o Facebook no trabalho

O objetivo deste tutorial é mostram como integrar Facebook no trabalho com o Azure Active Directory (AD Azure).

Integração Facebook no trabalho com o Azure AD fornece os seguintes benefícios: 

- Você pode controlar no Azure AD quem tem acesso ao Facebook no trabalho 
- Você pode provisionar automaticamente conta para os usuários que receberam acesso ao Facebook no trabalho
- Você pode habilitar os usuários para automaticamente obter assinado no Facebook no trabalho (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central 

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).


## <a name="prerequisites"></a>Pré-requisitos 

Para configurar a integração do Azure AD com estrelas CS, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Facebook no trabalho com o logon único habilitado

Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/). 


## <a name="adding-facebook-at-work-from-the-gallery"></a>Adicionando Facebook no trabalho da Galeria
Para configurar a integração do Facebook no trabalho no Azure AD, você precisa adicionar Facebook no trabalho da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Facebook no trabalho da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.
    
    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Facebook no trabalho**.

7. No painel de resultados, selecione **Facebook no trabalho**e, em seguida, clique em **concluído** para adicionar o aplicativo.


### <a name="configuring-azure-ad-single-sign-on"></a>Configurando Azure AD Single Sign-On

Esta seção descreve como habilitar usuários autenticar com o Facebook no trabalho com sua conta do Azure Active Directory, usando a federação com base no protocolo SAML.

**Para configurar o logon único Azure AD com o Facebook no trabalho, execute as seguintes etapas:**

1.  Depois de adicionar o Facebook no trabalho no portal de clássico do Azure, clique em **Configurar Single Sign-On**.

2.  Na tela **Configurar aplicativo URL** , digite a URL onde os usuários serão entrar em seu Facebook no aplicativo de trabalho. Este é o Facebook na URL de locatário do trabalho (exemplo: https://example.facebook.com/). Quando terminar, clique em **Avançar**.

3.  Em uma janela de navegador da web diferente, faça logon em sua Facebook no site de empresa de trabalho como um administrador.

4. Siga as instruções na seguinte URL para configurar o Facebook no trabalho usar Azure AD como um provedor de identidade: [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)

5.  Uma vez concluída, retorne para as janelas de navegador mostrando o Azure portal clássico, clique na caixa de seleção para confirmar que você tiver concluído o procedimento, clique em **Avançar** e **Concluir**.


## <a name="automatically-provisioning-users-to-facebook-at-work"></a>Provisionamento automaticamente os usuários com o Facebook no trabalho

Azure AD suporta a capacidade de automaticamente synchonize os detalhes de conta de usuários atribuídos ao Facebook no trabalho. Esta sincronização automática permite Facebook no trabalho para obter os dados necessários para autorizar usuários para acessar, antes que eles tentar entrar pela primeira vez. Ele também eliminação provisiona usuários do Facebook no trabalho quando acesso foi revogado no Azure AD.

Provisionamento automático pode ser configurado clicando em **Configurar conta provisionamento** na janela portal clássica do Azure.

Para obter detalhes adicionais sobre como configurar provisionamento automático, consulte [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)


## <a name="assigning-users-to-facebook-at-work"></a>Atribuir usuários a Facebook no trabalho

Para provisionado AAD os usuários possam ver o Facebook no trabalho em seu painel de acesso, eles devem ser atribuídos acesso dentro do Azure portal clássico.

**Para atribuir usuários a Facebook no trabalho:**

1.  Na página inicial do Facebook no trabalho no portal de clássico do Azure, clique em **atribuir contas**.

2.  No menu **Mostrar** , selecione se deseja atribuir um usuário ou um grupo no Facebook no trabalho e clique no botão de marca de seleção.

3.  Na lista resultante, selecione os usuários ou o grupo ao qual você deseja atribuir o Facebook no trabalho.

4.  No rodapé da página, clique no botão **atribuir** .


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png




