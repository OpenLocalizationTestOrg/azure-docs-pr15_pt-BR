## <a name="prerequisites"></a>Pré-requisitos

Antes de podemos escrever código de gerenciamento de CDN, precisamos fazer algumas preparação para habilitar o nosso código interagir com o Gerenciador de recursos do Azure.  Para fazer isso, você precisará:

* Criar um grupo de recursos para conter o perfil CDN que criamos neste tutorial
* Configurar o Azure Active Directory para fornecer autenticação de nosso aplicativo
* Aplicar permissões ao grupo de recursos para que somente os usuários autorizados de nosso locatário Azure AD podem interagir com nosso perfil CDN

### <a name="creating-the-resource-group"></a>Criação de grupo de recursos

1. Log no [Portal do Azure](https://portal.azure.com).

2. Clique no botão **novo** no canto superior esquerdo e, em seguida, **gerenciamento**e **Grupo de recursos**.
    
    ![Criando um novo grupo de recursos](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)

3. Ligue para seu grupo de recursos *CdnConsoleTutorial*.  Selecione a assinatura e escolha um local perto de você.  Se desejar, você pode clicar na caixa de seleção **Fixar em dashboard** para fixar o grupo de recursos para o painel no portal.  Isso tornará mais fácil encontrar mais tarde.  Após fazer suas seleções, clique em **criar**.

    ![O grupo de recursos de nomenclatura](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)

4. Após o grupo de recursos é criado, se você não fixá-lo ao seu painel, você pode encontrá-lo clicando em **Procurar**, em seguida, **Grupos de recursos**.  Clique no grupo de recursos para abri-lo.  Anote seu **ID da assinatura**.  Precisaremos-la mais tarde.

    ![O grupo de recursos de nomenclatura](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Criar o aplicativo do Azure AD e aplicação de permissões

Há duas abordagens para a autenticação de aplicativo com o Azure Active Directory: usuários individuais ou uma entidade de segurança do serviço. Um serviço principal é semelhante a uma conta de serviço do Windows.  Em vez de conceder permissões para interagir com os perfis CDN de um usuário específico, em vez disso, podemos conceder as permissões para o serviço principal.  Objetos de serviço geralmente são usados para processos automatizados, não interativo.  Embora este tutorial está gravando um aplicativo de console interativo, nos concentraremos na abordagem principal do serviço.

Criando um principal de serviço consiste em várias etapas, incluindo a criação de um aplicativo do Azure Active Directory.  Para fazer isso, vamos seguir [Este tutorial](../articles/resource-group-create-service-principal-portal.md).

> [AZURE.IMPORTANT] Certifique-se de seguir todas as etapas no [tutorial vinculada](../articles/resource-group-create-service-principal-portal.md).  É *muito importante* que concluí-la exatamente como descrito.  Verifique se observar seu **ID de locatário**, **nome de domínio de locatário** (comumente uma *. onmicrosoft.com* domínio, a menos que você especificou um domínio personalizado), **ID do cliente**e **chave de autenticação de cliente**, como precisaremos mais tarde.  Tenha cuidado para proteger sua **ID do cliente** e a **chave de autenticação de cliente**, como essas credenciais podem ser usadas por qualquer pessoa executar operações como o capital de serviço. 
>   
> Quando chegar na etapa denominado [Configurar vários locatário aplicativo](../articles/resource-group-create-service-principal-portal.md#configure-multi-tenant-application), selecione **não**.
> 
> Quando chegar na etapa [atribuir aplicativo à função](../articles/resource-group-create-service-principal-portal.md#assign-application-to-role), use o grupo de recursos criado anteriormente, *CdnConsoleTutorial*, mas em vez de função **leitor** , atribuir a função **Colaborador de perfil de CDN** .  Depois de atribuir o aplicativo a função **Colaborador de perfil de CDN** no seu grupo de recursos, retorne neste tutorial. 

Uma vez que você criou seu serviço principal e atribuído a função de **Colaborador de perfil de CDN** , a lâmina de **usuários** para seu grupo de recursos deve ser semelhante a esta.

![Blade de usuários](./media/cdn-app-dev-prep/cdn-service-principal-include.png)


### <a name="interactive-user-authentication"></a>Autenticação de usuário interativo

Se, em vez de um objeto de serviço, você preferiria ter autenticação de usuário individual interativos, o processo é muito semelhante para uma entidade de segurança do serviço.  Na verdade, você precisará siga o mesmo procedimento, mas fazer algumas alterações secundárias.

> [AZURE.IMPORTANT] Apenas siga as próximas etapas se você estiver escolhendo usar a autenticação de usuário individual, em vez de um serviço principal.

1. Ao criar seu aplicativo, em vez de **Aplicativo da Web**, escolha o **aplicativo nativo**. 
    
    ![Aplicativo nativo](./media/cdn-app-dev-prep/cdn-native-application-include.png)
    
2. Na próxima página, você será solicitado para um **Redirecionar URI**.  O URI não ser validados, mas lembre-se de que você inseriu.  Você precisará-la mais tarde. 

3. Não é necessário para criar uma **chave de autenticação de cliente**.

4. Em vez de atribuir uma entidade de segurança do serviço na função **Colaborador de perfil de CDN** , vamos atribuir usuários ou grupos individuais.  Neste exemplo, você pode ver que eu atribuiu *CDN demonstração usuário* à função de **Colaborador de perfil de CDN** .  
    
    ![Acesso de usuário individual](./media/cdn-app-dev-prep/cdn-aad-user-include.png)

