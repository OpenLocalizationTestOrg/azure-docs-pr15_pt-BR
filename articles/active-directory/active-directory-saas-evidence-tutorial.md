<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Evidence.com | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e Evidence.com."
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
    ms.date="02/23/2016"
    ms.author="asmalser"/>


# <a name="tutorial-azure-active-directory-integration-with-evidencecom"></a>Tutorial: Integração com o Active Directory do Azure com Evidence.com

O objetivo deste tutorial é mostrar como configurar o logon único entre Azure Active Directory (AAD) e Evidence.com. O cenário descrito neste tutorial supõe que você já tem os seguintes itens:
    
* Uma assinatura válida do Microsoft Azure
* Uma assinatura de Evidence.com com logon único habilitado (email earlyaccess@evidence.com se baseada em SAML logon único não estiver habilitado)

Depois de concluir este tutorial, os usuários AAD a quem você atribuiu acesso Evidence.com poderão logon único para o aplicativo usando o painel de acesso AAD.

## <a name="add-evidencecom-to-your-directory"></a>Adicionar Evidence.com ao seu diretório

Esta seção descreve como adicionar Evidence.com como um aplicativo integrado do Azure Active Directory.

**Para habilitar a integração de aplicativo de evidências:**

1.  No [portal do Azure clássico](https://manage.windowsazure.com), no painel de navegação esquerdo, clique em **Active Directory**.

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

4.  Para abrir a Galeria de aplicativo, clique em **Adicionar**e, em seguida, clique em **Adicionar um aplicativo da Galeria**.

5.  Na caixa de pesquisa, digite **Evidence.com**.

6.  No painel de resultados, selecione **Evidence.com**e clique em **concluído** para adicionar o aplicativo.


## <a name="configuring-single-sign-on"></a>Configurando o logon único

Esta seção descreve como habilitar usuários autenticar Evidence.com com sua conta do Azure Active Directory, usando a federação com base no protocolo SAML.

**Para configurar o logon único, execute as seguintes etapas:**

1.  Depois de adicionar Evidence.com no portal de clássico do Azure, clique em **Configurar Single Sign-On**. 
 
2.  Na tela seguinte, selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

3.  Na tela Configurar aplicativo URL, digite a URL onde os usuários serão entrar em sua URL de locatário Evidence.com (exemplo: https://yourtenant.evidence.com e, em seguida, clique em **Avançar**. 

4.  Clique no link **Baixar o certificado** e salvá-lo em sua unidade local. Este certificado e as URLs de metadados (ID de entidade, SSO URL de entrada e sinal de Check-Out URL) serão usadas para configurar o SSO no site Evidence.com. 

5.  Na janela do navegador da web separado, fazer login no seu Evidence.com como um administrador de locatários e navegue até o guia de **Administração**
      
6.  Clique em **agência logon único**
 
7.  Selecione **SAML baseia o logon único**
 
8.  Copie a **URL do emissor**, valores de **Logon único** e **Sair único** exibidos no portal de clássico do Azure e para os campos correspondentes em Evidence.com.

9.  Abrir o certificado baixado na etapa 4 usando um editor de texto como Notepad.exe e copiar e colar o conteúdo na caixa de **Certificado de segurança** . 

10. Salve a configuração na Evidence.com.
 
11. No portal do Azure clássico, marque **Confirmar que você configurou logon único conforme descrito acima**. Verificar isso permitirá que o certificado atual para começar a trabalhar para esta caixa de seleção do aplicativo.
 
12. Na página Confirmação de logon único, clique em **Concluir**.  


## <a name="creating-an-evidencecom-test-user"></a>Criação de um usuário de teste Evidence.com

Para usuários do Azure AD conseguir entrar, ele devem ser provisionados para acesso dentro do aplicativo Evidence.com. Esta seção descreve como criar contas de usuário do Azure AD dentro Evidence.com

**Configurar uma conta de usuário no Evidence.com:**

1.  Na janela do navegador da web, faça logon em seu site de empresa Evidence.com como administrador.

2.  Navegue até o guia de **Administração** .

3.  Clique em **Adicionar usuário**.

4.  Clique no botão **Adicionar** .

5.  O **Endereço de Email** do usuário adicionado deve corresponder o nome de usuário dos usuários no Azure AD quem você deseja conceder acesso. Se o nome de usuário e endereço de email não é o mesmo valor em sua organização, você pode usar o **Evidence.com > atributos > Single Sign-On** seção do portal de clássico do Azure para alterar o nameidenitifer enviada ao Evidence.com para ser o endereço de email.


## <a name="assigning-users-to-evidencecom"></a>Atribuir usuários a Evidence.com

Para provisionado AAD os usuários possam ver Evidence.com no seu painel de acesso, eles devem ser atribuídos acesso dentro do Azure portal clássico.

**Para atribuir usuários a Evidence.com:**

1.  Na página início rápido para Evidence.com no portal de clássico do Azure, clique em **atribuir aos usuários Evidence.com**.
 
2.  No menu **Mostrar** , selecione se você deseja atribuir um usuário ou grupo para Evidence.com e clique no botão de marca de seleção.
 
3.  Na lista de **usuários** , selecione os usuários do grupo ao qual você deseja atribuir Evidence.com.
 
4.  No rodapé da página, clique no botão **atribuir** .

