<properties
    pageTitle="Serviços de domínio do Azure AD: Criar ou selecionar uma rede virtual | Microsoft Azure"
    description="Introdução ao Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="create-or-select-a-virtual-network-for-azure-ad-domain-services"></a>Criar ou selecionar uma rede virtual para serviços de domínio do Azure AD

## <a name="guidelines-to-select-an-azure-virtual-network"></a>Diretrizes para selecionar uma rede virtual Azure
> [AZURE.NOTE] **Antes de começar**: consulte [Considerações para serviços de domínio do Azure AD de rede](active-directory-ds-networking.md).


## <a name="task-2-create-an-azure-virtual-network"></a>Tarefa 2: Criar uma rede virtual Azure
A próxima tarefa de configuração é criar uma rede virtual Azure e uma sub-rede dentro dela. Você pode habilitar serviços de domínio do Azure AD nesta sub-rede dentro de sua rede virtual. Se você já tiver uma rede virtual existente que você prefere usar, você pode ignorar esta etapa.

> [AZURE.NOTE] Certifique-se de que a rede virtual Azure criar ou optar por usar com os serviços de domínio do Azure AD pertence a uma região Azure que é suportada pelos serviços de domínio do Azure AD. Consulte a página de [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) conheça as regiões Azure em que os serviços de domínio do Azure AD está disponível.

Observe o nome da rede virtual para que você selecionar a rede virtual à direita ao ativar os serviços de domínio do Azure AD em uma etapa de configuração subsequentes.

Execute as seguintes etapas de configuração para criar uma rede virtual Azure na qual você gostaria ativar os serviços de domínio do Azure AD.

1. Navegue até o **portal clássico Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Selecione o nó de **redes** no painel esquerdo.

    ![Nó de redes](./media/active-directory-domain-services-getting-started/networks-node.png)

3. Clique em **novo** no painel de tarefas na parte inferior da página.

    ![Nó de redes virtuais](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. No nó de **Serviços de rede** , selecione **Rede Virtual**.

5. Clique em **Criar rápida** para criar uma rede virtual.

    ![Criar uma rede virtual - rápida](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. Especifique um **nome** para sua rede virtual. Você também pode optar por configurar o **espaço de endereço** ou a **contagem de máquina virtual máximo** para esta rede. Você pode deixar a configuração de **servidor DNS** definida como 'Nenhum' agora. Você pode atualizar o servidor DNS configurando após seu domínio do Azure AD habilitar serviços.

7. Certifique-se de que você selecionar uma região Azure com suporte no menu suspenso de **local** . Consulte a página de [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) conheça as regiões Azure em que os serviços de domínio do Azure AD está disponível.

8. Para criar sua rede virtual, clique no botão **criar uma rede Virtual** .

    ![Crie uma rede virtual para os serviços de domínio do Azure AD.](./media/active-directory-domain-services-getting-started/create-vnet.png)

9. Após a rede virtual é criada, selecione a rede virtual e clique na guia **Configurar** .

    ![Criar uma sub-rede](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)

10. Navegue até a seção de **espaços de endereço de rede virtual** . Clique em **Adicionar sub-rede** e especificar uma sub-rede com o nome **AaddsSubnet**. Clique em **Salvar** para criar a sub-rede.

    ![Crie uma sub-rede para serviços de domínio do Azure AD.](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)


<br>

## <a name="task-3---enable-azure-ad-domain-services"></a>Tarefa 3 - habilitar serviços de domínio do Azure AD
A próxima tarefa de configuração é habilitar [Serviços de domínio do Azure AD](active-directory-ds-getting-started-enableaadds.md).
