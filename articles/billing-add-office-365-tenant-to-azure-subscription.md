<properties
    pageTitle="Usar um locatário do Office 365 com uma assinatura do Azure | Microsoft Azure"
    description="Saiba como adicionar um diretório do Office 365 (Locatário) uma assinatura do Azure para fazer a associação."
    services=""
    documentationCenter=""
    authors="JiangChen79"
    manager="mbaldwin"
    editor=""
    tags="billing,top-support-issue"/>

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="cjiang"/>

# <a name="associate-an-office-365-tenant-with-an-azure-subscription"></a>Associar um locatário do Office 365 com uma assinatura do Azure
Se você tiver adquirido assinaturas do Azure e o Office 365 separadamente no passado e agora você deseja poder acessar o locatário do Office 365 da assinatura Azure, é fácil fazer isso. Este artigo mostra como fazer isso.

> [AZURE.NOTE] Este artigo não se aplica aos clientes Enterprise Agreement (EA).

## <a name="quick-guidance"></a>Guia rápido
Para associar seu locatário do Office 365 com sua assinatura do Azure, use sua conta do Azure para adicionar seu locatário do Office 365 e associar sua assinatura do Azure o locatário do Office 365.

## <a name="detailed-steps"></a>Etapas detalhadas
Neste cenário, Kelley Wall é um usuário que tenha uma assinatura do Azure sob a conta kelley.wall@outlook.com. Kelley também tem uma assinatura do Office 365 sob a conta kelley.wall@contoso.onmicrosoft.com. Kelley quiser acessar o locatário do Office 365 com a assinatura do Azure.

![Status de captura de tela do Active Directory do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![Usuários ativos de centro de administração de captura de tela do Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

### <a name="prerequisites"></a>Pré-requisitos
Para que a associação funcione corretamente, os seguintes pré-requisitos são necessários:

- Você precisa as credenciais de administrador do serviço da assinatura do Azure. Coadministradores não é possível executar um subconjunto das etapas.
- Você precisa as credenciais de um administrador global do Office 365 locatário.
- O endereço de email do administrador do serviço não deve estar contido no locatário Office 365.
- O endereço de email do administrador do serviço não deve corresponder de qualquer administrador global do Office 365 locatário.
- Se você estiver usando um endereço de email que é uma conta da Microsoft e uma conta organizacional, altere temporariamente o administrador de serviço da sua assinatura do Azure para usar outra conta da Microsoft. Você pode criar uma nova conta do Microsoft na [página de inscrição de conta Microsoft](https://signup.live.com/).


Para alterar o administrador do serviço, siga estas etapas:

1. Entrar no [portal de gerenciamento de conta](https://account.windowsazure.com/subscriptions).
2. Selecione a assinatura que você deseja alterar.
3. Selecione **Editar detalhes da assinatura**.

    ![Informações de assinatura de captura de tela do Azure, com "Editar detalhes da assinatura" realçado](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)

4. Na caixa **Administrador de serviço** , insira o endereço de email do administrador do serviço novo.

    ![Captura de tela da caixa de diálogo "Editar a sua assinatura"](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

### <a name="associate-the-office-365-tenant-with-the-azure-subscription"></a>Associar o locatário do Office 365 com a assinatura do Azure
Para associar a assinatura do Azure o locatário do Office 365, siga estas etapas:

1.  Entrar no [portal de gerenciamento de conta](https://account.windowsazure.com/subscriptions) com as credenciais de administrador de serviço.
2.  No painel esquerdo, selecione **ACTIVE DIRECTORY**.

    ![Captura de tela de entrada do Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

    > [AZURE.NOTE] Você não verá o locatário do Office 365. Se você vir, ignore a próxima etapa.

    ![Captura de tela do diretório padrão do Active Directory do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)

3. Adicione o locatário do Office 365 à sua assinatura do Azure.

    a. Selecione **novo** > **diretório** > **Criar personalizada**.

    ![Criar personalizada de captura de tela do Active Directory do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)

    b. Na página **Adicionar diretório** , em **diretório**, selecione **usar o diretório existente**. Selecione **estou pronto para ser assinado agora**e selecione **completa** ![ícone concluir](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

    ![Captura de tela de "Usar o diretório existente"](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)

    c. Depois que você está desconectado, entre com as credenciais do administrador global do seu locatário do Office 365.

    ![Captura de tela do Office 365 administrador global entrar](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)

    d. Selecione **continuar**.

    ![Captura de tela de verificação](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)

    e. Selecione **sair agora**.

    ![Captura de tela de saída](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)

    f. Entrar no [portal de gerenciamento de conta](https://account.windowsazure.com/subscriptions) com as credenciais de administrador de serviço.

    ![Captura de tela do Azure entrar](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

    g. Você deve ver seu locatário do Office 365 no painel de controle.

    ![Captura de tela do painel de controle](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

4. Altere o diretório associado a assinatura do Azure.

    a. Selecione **configurações**.

    ![Ícone de configurações do portal clássico de captura de tela do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)

    b. Selecione a sua assinatura do Azure e selecione **Editar diretório**.
    ![Diretório de edição de inscrição de captura de tela do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)

    c. Selecione **Avançar** ![ícone Avançar](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).

    ![Captura de tela de "Alterar diretório associado"](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)

    > [AZURE.WARNING] Você receberá um aviso de que todos os administradores de colegas serão removidos.

    ![Azure-confirme--mapeamento de diretório](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)

    >[AZURE.WARNING] Além disso, todos os usuários de [controle de acesso baseado em função (RBAC)](./active-directory/role-based-access-control-configure.md) com acesso atribuído nos grupos de recursos existentes também serão removidos. No entanto, o aviso recebido apenas menciona a remoção da coadministradores.

    ![atribuídos a usuários-removidos--grupos de recursos](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)

    d. Selecione **completa** ![ícone concluir](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

5. Agora você pode adicionar suas contas organizacionais do Office 365 como coadministradores locatários Azure Active Directory.

    a. Selecione a guia **administradores** e selecione **Adicionar**.

    ![Guia de administradores de configurações do portal clássico de captura de tela do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)

    b. Insira uma conta organizacional do seu locatário do Office 365, selecione a assinatura do Azure e, em seguida, selecione **completa** ![ícone concluir](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

    ![Caixa de diálogo de administrador colegas de adicionar a captura de tela do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)

    c. Volte para a guia de **administradores** . Você deve ver a conta organizacional exibida como administrador colegas.

    ![Captura de tela da guia de administradores](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)

6. Em seguida você pode testar o acesso com o administrador de colegas.

    a. Saia do portal de gerenciamento de conta.

    b. Abra o [portal de gerenciamento de conta](https://account.windowsazure.com/subscriptions) ou o [portal do Azure](https://portal.azure.com/).

    c. Se a página de entrada Azure tem um link de **entrar com sua conta de organização**, selecione o link. Caso contrário, pule esta etapa.

    ![Página de entrada captura de tela do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)

    d. Insira as credenciais do administrador colegas e selecione **entrar**.

    ![Página de entrada captura de tela do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="next-steps"></a>Próximas etapas
Cenários relacionados incluem:

- Você já tiver uma assinatura do Office 365 e está pronto para uma assinatura do Azure, mas que você deseja usar as contas de usuário existentes do Office 365 para sua assinatura do Azure.
- Você é um assinante do Azure e deseja obter uma assinatura do Office 365 para os usuários na sua instância do Active Directory do Azure existente.

Para aprender a realizar essas tarefas, consulte [usar de conta existente do Office 365 com sua assinatura do Azure, ou vice-versa](billing-use-existing-office-365-account-azure-subscription.md).
