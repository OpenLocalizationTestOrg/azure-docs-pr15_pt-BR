<properties
    pageTitle="Azure B2C diretório ativo: Crie um locatário do Azure Active Directory B2C | Microsoft Azure"
    description="Um tópico sobre como criar um locatário do Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.topic="article"
    ms.devlang="na"
    ms.date="08/30/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-create-an-azure-ad-b2c-tenant"></a>Azure B2C diretório ativo: Crie um locatário do Azure AD B2C

Para começar a usar o Microsoft Azure Active Directory (AD Azure) B2C, siga as três etapas descritas neste artigo.

## <a name="step-1-sign-up-for-an-azure-subscription"></a>Etapa 1: Inscrever-se para uma assinatura do Azure

Se você já tiver uma assinatura do Azure, pule esta etapa. Se esse não for o caso, inscreva-se em uma [assinatura do Azure](../active-directory/sign-up-organization.md) e obter acesso a Azure AD B2C.

## <a name="step-2-create-an-azure-ad-b2c-tenant"></a>Etapa 2: Criar um locatário do Azure AD B2C

Use as seguintes etapas para criar um novo locatário Azure AD B2C. Atualmente B2C recursos não podem ser ativados no seu locatários existentes.

1. Entrar no [portal clássico Azure](https://manage.windowsazure.com/) como o administrador de assinatura. Este é o mesmo trabalho ou conta de escola ou a mesma conta da Microsoft que você usou para se inscrever no Azure.
2. Clique em **novo** > **os serviços de aplicativo** > **do Active Directory** > **diretório** > **Criar personalizada**.

    ![Captura de tela da inicialização criar um locatário](./media/active-directory-b2c-get-started/new-directory.png)

3. Escolha o **nome**, **Nome de domínio** e **país ou região** do seu locatário.
4. Marque a opção que diz que **Este é um diretório B2C**.
5. Clique na marca de seleção para concluir a ação.

    ![Captura de tela da marca de seleção para criar um diretório de B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. Seu locatário agora é criado e aparecerão na extensão do Active Directory. Você também é feitas um Administrador Global do locatário. Você pode adicionar outros administradores globais conforme necessário.

    > [AZURE.IMPORTANT]
    Se você estiver planejando usar um locatário B2C para um aplicativo de produção, leia o artigo em [escala de produção versus locatários visualização B2C](active-directory-b2c-reference-tenant-type.md). Observe que há conhecidos problemas quando você excluir um locatário existente do B2C e recriá-la com o mesmo nome de domínio. Você precisa criar um locatário B2C com um nome de domínio diferente.

## <a name="step-3-navigate-to-the-b2c-features-blade-on-the-azure-portal"></a>Etapa 3: Navegue até a lâmina de recursos B2C no portal do Azure

1. Navegue até a extensão do Active Directory na barra de navegação no lado esquerdo.
2. Encontre seu locatário sob a guia de **diretório** e clique nele.
3. Clique na guia **Configurar** .
4. Clique no link **Gerenciar B2C configurações** na seção **Administração B2C** .

    ![Captura de tela da configuração de diretório para B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

5. Portal do Azure com a lâmina de recursos B2C mostrando será aberto em uma janela ou uma nova guia do navegador.

    > [AZURE.IMPORTANT]
    Pode levar até 2 a 3 minutos para seu locatário estejam acessíveis no portal do Azure. Repetindo essas etapas após alguns instantes serão corrigir o problema. Caso contrário, contate o suporte.

6. Fixe este blade seu Startboard para facilitar o acesso. (A ferramenta de Pin é marcada em vermelho no canto superior direito da lâmina recursos.)

    ![Captura de tela da lâmina B2C recursos](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    Você pode gerenciar usuários e grupos, configuração de redefinição de senha de autoatendimento e recursos de identidade visual de empresa do seu locatário no [Azure portal clássico](https://manage.windowsazure.com/).

## <a name="easy-access-to-the-b2c-features-blade-on-the-azure-portal"></a>Acesso fácil a lâmina B2C recursos no portal do Azure

Para melhorar a capacidade de descoberta, adicionamos um atalho lâmina B2C recursos no portal do Azure.

1. Entrar no portal do Azure como Administrador Global do seu locatário B2C. Se você já estiver conectado em um locatário diferente, alterne locatários (no canto superior direito).
2. Na barra de navegação à esquerda, clique em **Procurar** .
3. Clique em **Azure AD B2C** para acessar a lâmina de recursos B2C.

    ![Captura de tela de procurar B2C lâmina de recursos](./media/active-directory-b2c-get-started/b2c-browse.png)

## <a name="next-steps"></a>Próximas etapas

Aprenda a registrar um aplicativo com o Azure AD B2C e criar um aplicativo de início rápido lendo [Azure Active Directory B2C: registrar seu aplicativo](active-directory-b2c-app-registration.md).
