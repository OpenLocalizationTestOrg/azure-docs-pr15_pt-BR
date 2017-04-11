<properties
    pageTitle="Serviços de domínio do Azure AD: Ativar os serviços de domínio do Azure AD | Microsoft Azure"
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
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="enable-azure-ad-domain-services"></a>Ativar os serviços de domínio do Azure AD

## <a name="task-3-enable-azure-ad-domain-services"></a>Tarefa 3: Ativar os serviços de domínio do Azure AD
Nesta tarefa, você habilitar serviços de domínio do Azure AD para seu diretório. Execute as seguintes etapas de configuração para habilitar serviços de domínio do Azure AD para seu diretório.

1. Navegue até o **portal clássico Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Selecione o nó do **Active Directory** no painel esquerdo.

3. Selecione o locatário do Azure AD (diretório) para o qual você gostaria de ativar os serviços de domínio do Azure AD.

    ![Selecione Directory do Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Clique na guia **Configurar** .

    ![Configurar o guia do diretório](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Role para baixo até uma seção intitulada **Serviços de domínio**.

    ![Seção de configuração de serviços de domínio](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. Alterne a opção intitulada **habilitar serviços de domínio para este diretório** como **Sim**. Você perceber alguns mais opções de configuração para os serviços de domínio do Azure AD aparecem na página.

    ![Ativar os serviços de domínio](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

    > [AZURE.NOTE] Quando você habilita os serviços de domínio do Azure AD do seu locatário, Azure AD gera e armazena o hash de credencial Kerberos e NTLM necessários para autenticar usuários.

7. Especifique o **nome de domínio DNS dos serviços de domínio**.

   - O nome de domínio padrão do diretório (ou seja, que terminam com o **. onmicrosoft.com** sufixo de domínio) está selecionada por padrão.

   - A lista contém todos os domínios que foram configurados para seu diretório Azure AD – incluindo verificado bem como não verificados domínios que você configurar na guia 'Domínios'.

   - Além disso, você também pode digitar um nome de domínio personalizado. Neste exemplo, podemos digitou um nome de domínio personalizado 'contoso100.com'.

     > [AZURE.WARNING] Certifique-se de que o prefixo de domínio do nome do domínio que você especificar (por exemplo, 'contoso100' no nome de domínio 'contoso100.com') é menos de 15 caracteres. Você não pode criar um domínio de serviços de domínio do Azure AD com um prefixo de domínio mais de 15 caracteres.

8. Certifique-se de que o nome de domínio DNS que você escolheu para o domínio gerenciado não já existe na rede virtual. Especificamente, verifique se:

   - Você já tem um domínio com o mesmo nome de domínio DNS em uma rede virtual.

   - a rede virtual que você selecionou tiver uma conexão de VPN com a rede local e você tiver um domínio com o mesmo nome de domínio DNS na sua rede local.

   - Você tem um serviço de nuvem existente com esse nome em uma rede virtual.

9. A próxima etapa é selecionar uma rede virtual na qual você gostaria de serviços de domínio do Azure AD esteja disponível. Selecione a rede virtual e sub-rede dedicada criado na lista suspensa intitulada **Serviços de domínio de conectar a esta rede virtual**.

   - Certifique-se de que a rede virtual que você especificou pertence a uma região Azure suportada pelos serviços de domínio do Azure AD. Consulte a página de [Serviços Azure por região](https://azure.microsoft.com/regions/#services/) conheça as regiões Azure em que os serviços de domínio do Azure AD está disponível.

   - Redes virtuais pertencentes a uma região onde não há suporte para os serviços de domínio do Azure AD não aparecem na lista suspensa.
   
   - Use uma sub-rede dedicada dentro da rede virtual para serviços de domínio do Azure AD. Certifique-se de que você não selecionar a sub-rede de gateway. Consulte [Considerações de rede](active-directory-ds-networking.md). 

   - Da mesma forma, redes virtuais que foram criadas usando o Gerenciador de recursos do Azure não aparecem na lista suspensa. Redes virtuais baseados em Gerenciador de recursos não são suportadas pelos serviços de domínio do Azure AD.

10. Para habilitar serviços de domínio do Azure AD, clique em **Salvar** no painel de tarefas na parte inferior da página.

11. A página exibe um ' pendente... ' estado, enquanto os serviços de domínio do Azure AD está sendo habilitado para seu diretório.

    ![Ativar os serviços de domínio - pendente](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [AZURE.NOTE] Serviços de domínio do AD Azure fornece alta disponibilidade do seu domínio gerenciado. Depois de habilitar os serviços de domínio do Azure AD, observe os endereços IP em que os serviços de domínio estão disponíveis em aparecem na rede virtual um por vez. O segundo endereço IP é exibido em breve, como assim o serviço permite alta disponibilidade do seu domínio. Quando alta disponibilidade está configurado e ativa do seu domínio, você deve ver dois endereços IP na seção de **Serviços de domínio** da guia **Configurar** .

12. Depois cerca de 20 a 30 minutos, você verá o primeiro endereço IP em que os serviços de domínio está disponível em sua rede virtual no campo **endereço IP** na página **Definir** .

    ![Serviços de domínio habilitados - IP primeiro provisionado](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

13. Quando alta disponibilidade estiver operacional do seu domínio, você verá dois endereços IP exibidos na página. Seu domínio gerenciado está disponível na sua rede virtual selecionado nesses dois endereços IP. Anote os endereços IP para que você possa atualizar as configurações de DNS para sua rede virtual. Esta etapa permite máquinas virtuais em uma rede virtual para conectar-se para o domínio para operações como participar de domínio.

    ![Serviços de domínio habilitados - ambos os IPs provisionado](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [AZURE.NOTE] Dependendo do tamanho do seu locatário do Azure AD (número de usuários, grupos etc), a sincronização para seu domínio gerenciado leva algum tempo. Esse processo de sincronização ocorre no plano de fundo. Para grandes locatários com milhares de objetos, pode levar um ou dois dias para todos os usuários, as associações de grupo e credenciais a ser sincronizado.

<br>

## <a name="task-4---update-dns-settings-for-the-azure-virtual-network"></a>Tarefa 4 - atualizar configurações de DNS para a rede virtual Azure
A próxima tarefa de configuração é para [atualizar as configurações de DNS para a rede virtual Azure](active-directory-ds-getting-started-dns.md).
