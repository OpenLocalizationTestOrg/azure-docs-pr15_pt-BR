<properties
   pageTitle="Criar serviço principal no portal | Microsoft Azure"
   description="Descreve como criar um novo aplicativo do Active Directory e o serviço principal que pode ser usado com o controle de acesso baseado em função no Gerenciador de recursos do Azure para gerenciar o acesso aos recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/07/2016"
   ms.author="tomfitz"/>

# <a name="use-portal-to-create-active-directory-application-and-service-principal-that-can-access-resources"></a>Usar o portal para criar o aplicativo do Active Directory e capital de serviço que pode acessar recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)


Quando você tiver um aplicativo que precise acessar ou modificar recursos, você deve configurar um aplicativo do Active Directory (AD) e atribuir as permissões necessárias para ela. Este tópico mostra como executar essas etapas por meio do portal. Atualmente, você deve usar o portal clássico para criar um novo aplicativo do Active Directory e, em seguida, alterne para o portal do Azure para atribuir uma função para o aplicativo. 

> [AZURE.NOTE] As etapas neste tópico aplicam-se apenas ao usar o **portal clássico** para criar o aplicativo do AD. **Se você usar o portal do Azure para criar o aplicativo do AD, essas etapas não funcionará.** 
>
> Talvez seja mais fácil de configurar o seu aplicativo do AD e o serviço principal através de [PowerShell](resource-group-authenticate-service-principal.md) ou [CLI do Azure](resource-group-authenticate-service-principal-cli.md), especialmente se você quiser usar um certificado para autenticação. Este tópico não mostra como usar um certificado.

Para obter uma explicação dos conceitos do Active Directory, consulte [objetos de aplicativo e Principal do serviço](./active-directory/active-directory-application-objects.md). Para obter mais informações sobre autenticação do Active Directory, consulte [Cenários de autenticação do Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Para obter etapas detalhadas sobre como integrar um aplicativo do Azure para gerenciar recursos, consulte [o guia do desenvolvedor autorização com a API do Gerenciador de recursos do Azure](resource-manager-api-authentication.md).

## <a name="create-an-active-directory-application"></a>Criar um aplicativo do Active Directory

1. Faça logon em sua conta do Azure por meio do [portal clássico](https://manage.windowsazure.com/). 

2. Verifique se que você sabe o padrão do Active Directory para sua assinatura. Você só pode conceder acesso para aplicativos no mesmo diretório como sua assinatura. Selecione **configurações** e procure o nome de diretórios associado a sua assinatura.  Para obter mais informações, consulte [como Azure assinaturas são associadas com o Active Directory do Azure](./active-directory/active-directory-how-subscriptions-associated-directory.md).
   
     ![Localize o diretório padrão](./media/resource-group-create-service-principal-portal/show-default-directory.png)

2. Selecione o **Active Directory** no painel esquerdo.

     ![Selecione Active Directory](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. Selecione o Active Directory que você deseja usar para criar o aplicativo. Se você tiver mais de um Active Directory, criar o aplicativo no diretório padrão para sua assinatura.   

     ![Escolher diretório](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
3. Para exibir os aplicativos em seu diretório, selecione **aplicativos**.

     ![Exibir aplicativos](./media/resource-group-create-service-principal-portal/view-applications.png)

4. Se você ainda não criou um aplicativo no diretório antes, você verá algo semelhante à seguinte imagem. Selecione **Adicionar um aplicativo**

     ![Adicionar aplicativo](./media/resource-group-create-service-principal-portal/create-application.png)

     Ou, clique em **Adicionar** no painel inferior.

     ![Adicionar](./media/resource-group-create-service-principal-portal/add-icon.png)

5. Selecione o tipo de aplicativo que você gostaria de criar. Para este tutorial, selecione **Adicionar um aplicativo minha organização está desenvolvendo**. 

     ![novo aplicativo](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. Forneça um nome para o aplicativo e selecione o tipo de aplicativo que você deseja criar. Para este tutorial, crie uma **WEB APPLICATION e/ou WEB API** e clique no botão Avançar. Se você selecionar **Aplicativo nativo do cliente**, as etapas restantes deste artigo não coincidirão sua experiência.

     ![aplicativo de nome](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. Preencha as propriedades para o aplicativo. Para **URL de SIGN-ON**, forneça o URI a um site que descreva o seu aplicativo. A existência do site da web não é validada. Para **URI de ID do aplicativo**, forneça a URI que identifica seu aplicativo.

     ![Propriedades de aplicativo](./media/resource-group-create-service-principal-portal/app-properties.png)

Você criou seu aplicativo.

## <a name="get-client-id-and-authentication-key"></a>Obter chave de id e a autenticação de cliente

Quando o registro em log programaticamente no, você precisa da identificação do aplicativo. Se o aplicativo é executado em seus próprio credenciais, você também precisa de uma chave de autenticação.

1. Selecione a guia **Configurar** para configurar senha do seu aplicativo.

     ![Configurar aplicativo](./media/resource-group-create-service-principal-portal/application-configure.png)

2. Copie o **código do cliente**.
  
     ![id do cliente](./media/resource-group-create-service-principal-portal/client-id.png)

3. Se o aplicativo é executado em seus próprio credenciais, role até a seção de **chaves** e selecione por quanto tempo você gostaria que sua senha seja válida.

     ![chaves](./media/resource-group-create-service-principal-portal/create-key.png)

4. Selecione **Salvar** para criar sua chave.

     ![Salvar](./media/resource-group-create-service-principal-portal/save-icon.png)

     A chave salva é exibida e você poderá copiá-lo. Não é possível recuperar a chave mais tarde então copiá-lo agora.

     ![salvo chave](./media/resource-group-create-service-principal-portal/save-key.png)

## <a name="get-tenant-id"></a>Obter identificação de locatário

Quando o registro em log programaticamente no, você precisa passar o id de locatário com sua solicitação de autenticação. Para aplicativos Web e aplicativos de API da Web, você pode recuperar a identificação de locatário selecionando **pontos de extremidade do modo de exibição** na parte inferior da tela e recuperar a identificação conforme mostrado na imagem a seguir.  

   ![id do locatário](./media/resource-group-create-service-principal-portal/save-tenant.png)

Você também pode recuperar a identificação de locatário através do PowerShell:

    Get-AzureRmSubscription

Ou, CLI Azure:

    azure account show --json

## <a name="set-delegated-permissions"></a>Definir delegada permissões

Se o aplicativo acessa recursos em nome de um usuário conectado, você deve conceder ao seu aplicativo a permissão delegada para acessar outros aplicativos. Você concede acesso a na seção **permissões para outros aplicativos** da guia **Configurar** . Por padrão, uma permissão delegada já está habilitada para o Azure Active Directory. Deixe esta permissão delegada inalterado.

1. Selecione **Adicionar aplicativo**.

2. Na lista, selecione a **API de gerenciamento de serviço do Windows Azure**. Em seguida, selecione o ícone de concluído.

      ![Selecione o aplicativo](./media/resource-group-create-service-principal-portal/select-app.png)

3. Na lista suspensa permissões delegados, selecione **Gerenciamento de serviços do Access Azure como organização**.

      ![Selecione permissão](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. Salve a alteração.

## <a name="assign-application-to-role"></a>Atribuir o aplicativo à função

Se seu aplicativo é executado em seus próprio credenciais, você deve atribuir o aplicativo a uma função. Decida qual função representa as permissões corretas para o aplicativo. Para saber mais sobre as funções disponíveis, consulte [RBAC: integrado funções](./active-directory/role-based-access-built-in-roles.md). 

Para atribuir uma função a um aplicativo, você deve ter as permissões corretas. Especificamente, você deve ter `Microsoft.Authorization/*/Write` acesso concedido por meio a função de [proprietário](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrador de acesso do usuário](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) . A função Colaborador não tem o acesso correto.

Você pode definir o escopo no nível da assinatura, grupo de recursos ou recurso. As permissões são herdadas de níveis inferiores de escopo. Por exemplo, adicionando um aplicativo à função Leitor de um grupo de recursos significa que ele pode ler o grupo de recursos e os recursos que ela contém.

1. Para atribuir o aplicativo a uma função, alterne do portal clássico do [portal do Azure](https://portal.azure.com).

1. Verifique suas permissões para certificar-se de que você pode atribuir a entidade de serviço a uma função. Selecione **Minhas permissões** para a sua conta.

    ![Selecione minhas permissões](./media/resource-group-create-service-principal-portal/my-permissions.png)

1. Exiba as permissões atribuídas para a sua conta. Conforme mencionado anteriormente, você deve pertencer às funções proprietário ou administrador de acesso do usuário ou ter uma função personalizada que concede acesso de gravação para Microsoft.Authorization. A imagem a seguir mostra uma conta que é atribuída à função Colaborador de assinatura, que não é permissões adequadas para atribuir um aplicativo a uma função.

    ![Mostrar minhas permissões](./media/resource-group-create-service-principal-portal/show-permissions.png)

     Se você não tem as permissões corretas para conceder acesso a um aplicativo, que você deve tanto solicitar que o administrador de assinatura que você adiciona à função de administrador de acesso do usuário ou solicitar que um administrador concede acesso ao aplicativo.

1. Navegue até o nível de escopo que você deseja atribuir o aplicativo. Para atribuir uma função no escopo assinatura, selecione **assinaturas**.

     ![Selecione assinatura](./media/resource-group-create-service-principal-portal/select-subscription.png)

     Selecione a assinatura específica para atribuir o aplicativo.

     ![Selecione a assinatura de atribuição](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

     Selecione o ícone de **acesso** no canto superior direito.

     ![Selecione acesso](./media/resource-group-create-service-principal-portal/select-access.png)
     
     Ou, para atribuir uma função no escopo do grupo de recursos, navegue até um grupo de recursos. Da lâmina de grupo do recurso, selecione o **controle de acesso**.

     ![Selecione usuários](./media/resource-group-create-service-principal-portal/select-users.png)

     As etapas a seguir são as mesmas para qualquer escopo.

2. Selecione **Adicionar**.

     ![Selecione Adicionar](./media/resource-group-create-service-principal-portal/select-add.png)

3. Selecione a função de **leitor** (ou qualquer função que você deseja atribuir o aplicativo).

     ![Selecionar função](./media/resource-group-create-service-principal-portal/select-role.png)

4. Quando vir a lista de usuários que você pode adicionar à função primeiro, você não verá aplicativos. Você só verá grupo e usuários.

     ![Mostrar os usuários](./media/resource-group-create-service-principal-portal/show-users.png)

5. Para localizar seu aplicativo, você deve procurá-lo. Comece a digitar o nome do seu aplicativo e a lista das opções disponíveis será alterada. Selecione seu aplicativo quando você vê-lo na lista.

     ![atribuir a função](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. Selecione **Okey** para concluir a atribuição de função. Agora você deve ver seu aplicativo na lista de usos atribuído a uma função para o grupo de recursos.


Para obter mais informações sobre a atribuição de usuários e aplicativos às funções por meio do portal, consulte [Usar atribuições de função para gerenciar o acesso aos recursos da sua assinatura Azure](role-based-access-control-configure.md#manage-access-using-the-azure-management-portal).

## <a name="sample-applications"></a>Aplicativos de exemplo

Os aplicativos de exemplo a seguir mostram como efetuar login como o capital de serviço.

**.NET**

- [Implantar um SSH habilitado máquina virtual com um modelo com .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Gerenciar recursos do Azure e grupos de recursos com .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Introdução aos recursos - implantar usando o modelo do Gerenciador de recursos do Azure - em Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Introdução aos recursos - gerenciar o grupo de recursos - em Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Implantar um SSH habilitado máquina virtual com um modelo em Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Gerenciando o recurso Azure e grupos de recursos com Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node**

- [Implantar um SSH habilitado máquina virtual com um modelo no Node](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Gerenciar recursos do Azure e grupos de recursos com Node](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Implantar um SSH habilitado máquina virtual com um modelo em Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Gerenciando o recurso Azure e grupos de recursos com Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## <a name="next-steps"></a>Próximas etapas

- Para saber sobre como especificar políticas de segurança, consulte [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md).  
- Para uma demonstração em vídeo destas etapas, consulte [Habilitando-se o gerenciamento de programação de um recurso do Azure com o Active Directory do Azure](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).

