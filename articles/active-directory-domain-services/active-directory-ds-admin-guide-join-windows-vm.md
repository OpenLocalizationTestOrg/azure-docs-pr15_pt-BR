<properties
    pageTitle="Azure domínio serviços do Active Directory: Ingressar em uma máquina de virtual do Windows Server para um domínio gerenciado | Microsoft Azure"
    description="Ingressar em uma máquina de virtual do Windows Server aos serviços de domínio do Azure AD"
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
    ms.topic="article"
    ms.date="10/02/2016"
    ms.author="maheshu"/>

# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Ingressar em uma máquina de virtual do Windows Server para um domínio gerenciado

> [AZURE.SELECTOR]
- [Portal clássico Azure - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

Este artigo mostra como ingressar em uma máquina virtual executando o Windows Server 2012 R2 para um domínio gerenciado de serviços de domínio do Azure AD, usando o portal de clássico Azure.


## <a name="step-1-create-the-windows-server-virtual-machine"></a>Etapa 1: Criar a máquina virtual do Windows Server
Siga as instruções descritas no tutorial [criar uma máquina virtual executando o Windows no portal de clássico do Azure](../virtual-machines/virtual-machines-windows-classic-tutorial.md) . É importante garantir que esse recém-criado máquina virtual está associado à mesma rede virtual no qual você habilitados serviços de domínio do Azure AD. A opção 'Criar rápida' não permitem que você ingressar na máquina virtual com uma rede virtual. Portanto, você precisa usar a opção 'Da galeria' para criar a máquina virtual.

Execute as seguintes etapas para criar uma máquina virtual do Windows associada à rede virtual no qual você habilitou os serviços de domínio do Azure AD.

1. No portal do clássico Azure, na barra de comandos na parte inferior da janela, clique em **novo**.

2. Em **Calcular**, clique em **Máquina Virtual**, clique em **Galeria de**.

3. Primeira tela permite **Escolher uma imagem** para sua máquina virtual na lista de imagens disponíveis. Escolha a imagem apropriada.

    ![Selecione imagem](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. Segunda tela permite que você escolha um nome de computador, o tamanho e o nome de usuário administrativo e a senha. Use a camada e o tamanho necessário para executar o aplicativo ou a carga de trabalho. O nome de usuário que você escolher aqui é um usuário administrador local na máquina. Não digite credenciais de uma conta usuário de domínio aqui.

    ![Configurar máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-config.png)

5. Na terceira tela permite que você configurar os recursos de rede, armazenamento e disponibilidade. Certifique-se de que selecionar a rede virtual no qual você habilitados serviços de domínio do Azure AD na lista suspensa de **Rede de grupo/Virtual de região/afinidade** . Especifique um **Nome de DNS de serviço de nuvem** conforme apropriado para a máquina virtual.

    ![Selecione uma rede virtual para máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [AZURE.WARNING]
    Certifique-se de que você ingressar na máquina virtual à mesma rede virtual no qual você habilitou os serviços de domínio do Azure AD. Como resultado, a máquina virtual pode 'consulte' o domínio e executar tarefas como ingressar no domínio. Se você optar por criar a máquina virtual em uma rede virtual diferente, conecte dessa rede virtual à rede virtual no qual você habilitou os serviços de domínio do Azure AD.

6. A quarta tela permite instalar o agente de máquina virtual e configurar algumas das extensões disponíveis.

    ![Concluído](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)

7. Depois que a máquina virtual é criada, o portal clássico listas a nova máquina virtual sob o nó de **máquinas virtuais** . Tanto o serviço de nuvem e máquina virtual são iniciadas automaticamente e seu status é listado como **executando**.

    ![Máquina virtual está em execução](./media/active-directory-domain-services-admin-guide/create-windows-vm-running.png)


## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>Etapa 2: Conectar na máquina virtual do Windows Server usando a conta de administrador local
Agora, podemos conectar na máquina recém-criado de virtual do Windows Server, para ingressar no domínio. Use as credenciais de administrador local que você especificou ao criar a máquina virtual, para se conectar a ele.

Execute as seguintes etapas para se conectar à máquina virtual.

1. Navegue até o nó de **máquinas virtuais** no portal do clássico. Selecione a máquina virtual que você criou na etapa 1 e clique em **Conectar** na barra de comandos na parte inferior da janela.

    ![Conectar-se a máquina virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. O portal de clássico solicitará que você abrir ou salvar um arquivo com uma extensão '. rdp', que é usada para conectar-se à máquina virtual. Clique para abrir o arquivo quando o download for concluído.

3. No prompt de logon, insira suas **credenciais de administrador local**, que você especificou ao criar a máquina virtual. Por exemplo, usamos 'localhost\mahesh' neste exemplo.

Neste ponto, você deve estar conectado a máquina virtual Windows recém-criado usando credenciais de administrador locais. A próxima etapa é ingressar na máquina virtual para o domínio.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>Etapa 3: Junção máquina virtual do Windows Server para o domínio gerenciado AAD-DS
Execute as seguintes etapas para ingressar na máquina virtual do Windows Server para o domínio gerenciado AAD-DS.

1. Conecte ao Windows Server, conforme mostrado na etapa 2. Na tela Iniciar, abra o **Gerenciador de servidor**.

2. Clique em **Servidor Local** no painel esquerdo da janela do Gerenciador de servidor.

    ![Inicie o Gerenciador de servidor na máquina virtual](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. Clique em **grupo de trabalho** na seção **Propriedades** . Na página de propriedades de **Propriedades do sistema** , clique em **Alterar** para ingressar no domínio.

    ![Página de propriedades do sistema](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

4. Especifique o nome de domínio dos seus serviços de domínio do Azure AD gerenciado domínio na caixa de texto **domínio** e clique em **Okey**.

    ![Especificar o domínio a ser associado](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

5. Você será solicitado a inserir suas credenciais para ingressar no domínio. Certifique-se que você **Especifique as credenciais de um usuário que pertencem aos administradores DC AAD** grupo. Somente os membros deste grupo tem privilégios para ingressar máquinas no domínio gerenciado.

    ![Especifique as credenciais de junção de domínio](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

6. Você pode especificar credenciais em qualquer uma das seguintes maneiras:

    - Formato UPN: especificar o sufixo UPN para a conta de usuário, conforme configurado no Azure AD. Neste exemplo, o sufixo UPN do usuário 'Paulo' é 'bob@domainservicespreview.onmicrosoft.com'.

    - Formato de SAMAccountName: você pode especificar o nome da conta no formato SAMAccountName. Neste exemplo, o usuário 'Paulo' precisaria inserir 'CONTOSO100\bob'.

        > [AZURE.NOTE] **É recomendável usar o formato UPN para especificar credenciais.** SAMAccountName pode ser gerado automaticamente se o prefixo UPN do usuário é muito longo (por exemplo, 'joereallylongnameuser'). Se vários usuários têm o mesmo prefixo UPN (por exemplo, ' Paulo') em seu locatário do Azure AD, seu formato SAMAccountName pode ser gerado automaticamente pelo serviço. Nesses casos, o formato UPN pode ser usado confiável para efetuar login no domínio.

7. Depois de junção de domínio for bem-sucedido, você verá a seguinte mensagem de boas-vindas para o domínio. Reinicie a máquina virtual para a operação de associação de domínio concluir.

    ![Bem-vindo ao domínio](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## <a name="troubleshooting-domain-join"></a>Solução de problemas de associação de domínio
### <a name="connectivity-issues"></a>Problemas de conectividade
Se a máquina virtual for incapaz de localizar o domínio, consulte as etapas de solução de problemas a seguir:

- Certifique-se de que a máquina virtual está conectada à mesma rede virtual que você habilitou os serviços de domínio no. Caso contrário, a máquina virtual não é possível conectar ao domínio e, portanto, não é possível ingressar no domínio.

- Se a máquina virtual estiver conectada a outra rede virtual, certifique-se de que esta rede virtual está conectado à rede virtual no qual você habilitou os serviços de domínio.

- Tente fazer ping do domínio usando o nome de domínio do domínio gerenciado (por exemplo, ' ping contoso100.com'). Se você não conseguir fazer isso, tente ping nos endereços IP para o domínio exibido na página onde você habilitados serviços de domínio do Azure AD (por exemplo, ' ping 10.0.0.4'). Se você for capaz de executar ping o endereço IP, mas não o domínio, DNS pode estar configurado incorretamente. Talvez você não tenha configurado os endereços IP do domínio como servidores DNS para a rede virtual.

- Tente liberar o cache de resolução DNS na máquina virtual ('ipconfig /flushdns.').

Se você receber a caixa de diálogo que solicita credenciais para ingressar no domínio, você não tem problemas de conectividade.


### <a name="credentials-related-issues"></a>Problemas relacionados à credenciais
Consulte as etapas a seguir se você estiver enfrentando problemas com as credenciais e não conseguem ingressar no domínio.

- Tente usar o formato UPN para especificar credenciais. O SAMAccountName para sua conta pode ser gerado automaticamente se há vários usuários com o mesmo prefixo UPN em seu locatário ou se o prefixo UPN é muito longo. Portanto, o formato de SAMAccountName para sua conta pode ser diferente do que você espera ou usar em seu domínio local.

- Tente usar as credenciais de uma conta de usuário que pertencer ao grupo 'Administradores de DC AAD' ingressar máquinas ao domínio gerenciado.

- Certifique-se de que você tenha [habilitado para sincronização de senha](active-directory-ds-getting-started-password-sync.md) de acordo com as etapas descritas no guia de Introdução.

- Certifique-se de que você use o UPN do usuário conforme configurado no Azure AD (por exemplo, 'bob@domainservicespreview.onmicrosoft.com') para entrar.

- Certifique-se de que você tenha demorou tempo suficiente para sincronização de senha ser concluída conforme especificado no guia de Introdução.


## <a name="related-content"></a>Conteúdo relacionado

- [Serviços de domínio Azure AD - guia de Introdução](./active-directory-ds-getting-started.md)

- [Administrar um domínio gerenciado de serviços de domínio do Azure AD](./active-directory-ds-admin-guide-administer-domain.md)
