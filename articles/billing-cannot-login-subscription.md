<properties
    pageTitle="Não é possível entrar em assinatura Azure | Microsoft Azure"
    description="Descreve como solucionar alguns problemas comuns de logon do Azure assinatura."
    services=""
    documentationCenter=""
    authors="genlin"
    manager="mbaldwin"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="genli"/>

# <a name="i-cant-sign-in-to-manage-my-azure-subscription"></a>Não consigo entrar Gerenciar minha assinatura do Azure

Este artigo orienta você a alguns dos métodos mais comuns para resolver problemas de login.

## <a name="page-hangs-in-the-loading-status"></a>Página trava no status de carregamento

Se a página do navegador de internet trava, tente cada uma das etapas a seguir até que você pode acessar o [portal do Azure](https://portal.azure.com).

-   Atualize a página.
-   Use um navegador de Internet diferentes.
-   Se você estiver usando o Microsoft Internet Explorer, navegue até o portal do Azure usando o modo de Navegação InPrivate. 

    R.  Clique em **Ferramentas** ![botão ferramentas](./media/billing-cannot-login-subscription/Toolsbutton.png) > **segurança** > **Navegação InPrivate**.

    B.  Navegue até o [portal do Azure](https://portal.azure.com)e, em seguida, entre portal.

## <a name="error-message-no-subscriptions-found"></a>Mensagem de erro "Nenhuma assinaturas encontradas"

Se sua conta não tem permissões suficientes, você poderá ver uma mensagem de erro de **Nenhuma assinatura encontrada** . Somente um administrador de conta pode acessar o [Centro de conta](https://account.windowsazure.com/), não os administradores de serviço (SA) ou coadministradores (CA).

**Cenário 1: Mensagem de erro é recebida no [portal do Azure](https://portal.azure.com)**

Para resolver esse problema, [adicione a função de administrador de colegas ou proprietário](billing-add-change-azure-subscription-administrator.md) da conta.

**Cenário 2: Mensagem de erro é recebida no [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions)**

Verifique se a conta que você usou é o administrador da conta. Para verificar quem é o administrador da conta, siga estas etapas:

1.  Entrar no [portal do Azure](https://portal.azure.com).
2.  No menu Hub, selecione a **assinatura**.
3.  Selecione a assinatura que você deseja verificar e, em seguida, selecione **configurações**.
4.  Selecione **Propriedades**. O administrador de conta da assinatura é exibido na caixa **Conta de administrador** .

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>Você é automaticamente conectado como um usuário diferente

Esse problema pode ocorrer se você estiver usando mais de uma conta de usuário em um navegador da Internet.

Para resolver o problema, tente um dos seguintes métodos:

-   Limpar o cache e excluir cookies da Internet. No Internet Explorer, clique em **Ferramentas** ![botão ferramentas](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Opções da Internet** > **Excluir**. Certifique-se de que as caixas de seleção para arquivos temporários, cookies, senha e histórico de navegação são selecionadas e clique em Excluir.

-   Redefina as configurações do Internet Explorer para reverter quaisquer configurações pessoais que você fez. Clique em **Ferramentas** ![botão ferramentas](./media/billing-cannot-login-subscription/Toolsbutton.png)> **Opções da Internet** > **Avançado** > marque a caixa **Excluir configurações pessoais** > **Redefinir**.

-   Navegue até o portal do Azure no modo de Navegação InPrivate. Clique em **Ferramentas** ![botão ferramentas](./media/billing-cannot-login-subscription/Toolsbutton.png) > **segurança** > **Navegação InPrivate**.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte. 

Se você ainda precisar de Ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente. 