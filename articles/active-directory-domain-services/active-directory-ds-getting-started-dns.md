<properties
    pageTitle="Serviços de domínio do Azure AD: Configurações de DNS de atualização para a rede virtual Azure | Microsoft Azure"
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
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services---update-dns-settings-for-the-azure-virtual-network"></a>Serviços de domínio Azure AD - configurações de DNS de atualização para a rede virtual Azure

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Tarefa 4: Atualizar as configurações de DNS para a rede virtual Azure
Em anterior tarefas de configuração, você habilitou com êxito os serviços de domínio do Azure AD para seu diretório. A próxima tarefa é garantir que os computadores dentro da rede virtual podem se conectar e consumir esses serviços. Atualize as configurações do servidor DNS para sua rede virtual para apontar para os dois endereços IP em que os serviços de domínio do Azure AD está disponível em uma rede virtual.

> [AZURE.NOTE] Anote os endereços IP para serviços de domínio do Azure AD exibidos na guia **Configurar** do seu diretório, depois que você tiver habilitado o serviços de domínio do Azure AD para o diretório.

Execute as seguintes etapas de configuração para atualizar a configuração do servidor DNS para a rede virtual no qual você habilitou os serviços de domínio do Azure AD.

1. Navegue até o **portal clássico Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Selecione o nó de **redes** no painel esquerdo.

    ![Nó de redes virtuais](./media/active-directory-domain-services-getting-started/virtual-network-select.png)

3. Na guia **Redes virtuais** , selecione a rede virtual no qual você habilitados serviços de domínio do Azure AD exibir suas propriedades.

4. Clique na guia **Configurar** .

    ![Nó de redes virtuais](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)

5. Na seção **servidores DNS** , digite os endereços IP dos serviços de domínio do Azure AD.

6. Certifique-se de que você pode inserir os endereços IP que foram exibidos na seção de **Serviços de domínio** na guia **Configurar** do seu diretório.

7. Para salvar as configurações do servidor DNS para esta rede virtual, clique em **Salvar** no painel de tarefas na parte inferior da página.

   ![Atualize as configurações de servidor DNS para a rede virtual.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [AZURE.NOTE] Depois de atualizar as configurações do servidor DNS para a rede virtual, pode levar alguns instantes para máquinas virtuais na rede para obter a configuração de DNS atualizada. Se uma máquina virtual não for possível se conectar com o domínio, você pode liberar o cache DNS (ex. 'ipconfig /flushdns.') na máquina virtual. Este comando força uma atualização das configurações de DNS na máquina virtual.


## <a name="task-5---enable-password-synchronization-to-azure-ad-domain-services"></a>Tarefa 5 - habilitar a sincronização de senha aos serviços de domínio do Azure AD
A próxima tarefa de configuração é para [Habilitar a sincronização de senha aos serviços de domínio do Azure AD](active-directory-ds-getting-started-password-sync.md).
