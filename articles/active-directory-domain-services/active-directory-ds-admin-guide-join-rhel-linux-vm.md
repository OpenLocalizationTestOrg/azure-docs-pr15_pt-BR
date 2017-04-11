<properties
    pageTitle="Azure domínio serviços do Active Directory: Ingressar em uma VM RHEL de um domínio gerenciado | Microsoft Azure"
    description="Ingressar em uma máquina virtual de Red Hat Enterprise Linux aos serviços de domínio do Azure AD"
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

# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Ingressar em uma máquina virtual de Red Hat Enterprise Linux 7 a um domínio gerenciado
Este artigo mostra como ingressar em uma máquina virtual de Red Hat Enterprise Linux (RHEL) 7 a um domínio de serviços de domínio do Azure AD gerenciado.

## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Provisionar uma máquina virtual de Red Hat Enterprise Linux
Execute as seguintes etapas para provisionar uma máquina virtual do RHEL 7 usando o portal do Azure.

1. Entrar no [portal do Azure](https://portal.azure.com).

    ![Painel de portal Azure](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)

2. Clique em **novo** no painel esquerdo e digite **Red Hat** na barra de pesquisa, conforme mostrado na seguinte captura de tela. Entradas para Red Hat Enterprise Linux aparecem nos resultados da pesquisa. Clique em **Red Hat Enterprise Linux 7.2**.

    ![Selecione RHEL nos resultados](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)

3. Os resultados da pesquisa no painel **tudo** devem listar a imagem Red Hat Enterprise Linux 7.2. Clique em **Red Hat Enterprise Linux 7.2** para exibir mais informações sobre a imagem de máquina virtual.

    ![Selecione RHEL nos resultados](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)

4. No painel **Red Hat Enterprise Linux 7.2** , você deve ver mais informações sobre a imagem de máquina virtual. Na lista suspensa **Selecionar um modelo de implantação** , selecione **clássico**. Em seguida, clique no botão **criar** .

    ![Exibir detalhes de imagem](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)

5. No painel **Máquina virtual criar** , insira o **Nome do Host** para a nova máquina virtual. Também especifica um nome de usuário de administrador local no campo **nome de usuário** e uma **senha**. Você também pode optar por usar uma chave SSH para autenticar o usuário administrador local. Também selecione um **Nível de preços** para a máquina virtual.

    ![Criar máquina virtual - detalhes básicos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)

6. Clique em **configuração opcional**. No painel **config opcional** , clique em **rede**.

    ![Criar máquina virtual - configurar rede virtual](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-configure-vnet.png)

7. Isto traz um painel intitulado **rede**. No painel de **rede** , clique em **Rede Virtual** para selecionar a rede virtual à qual o VM Linux deve ser implantado. Isso abre o painel de **Rede Virtual** . No painel de **Rede Virtual** , escolha a opção **usar uma rede virtual existente** . Selecione a rede virtual no qual os serviços de domínio do Azure AD está disponível. Neste exemplo, podemos escolha rede virtual 'MyPreviewVNet'.

    ![Criar máquina virtual - selecionar uma rede virtual](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)

8. No painel **configuração opcional** , clique no botão **Okey** .

    ![Criar máquina virtual - rede virtual selecionada](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)

9. Agora você está pronto para criar a máquina virtual. No painel de **Máquina virtual criar** , clique no botão **criar** .

    ![Criar máquina virtual - pronto](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm.png)

10. Implantação da nova máquina virtual com base na imagem RHEL 7.2 deve começar.

  ![Criar máquina virtual - implantação iniciada](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)

11. Após alguns minutos, a máquina virtual deve ser implantada com êxito e pronto para uso.

  ![Criar máquina virtual - implantado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)



## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Conectar-se remotamente à máquina virtual Linux recentemente provisionada
Máquina virtual RHEL 7.2 foi configurada no Azure. A próxima tarefa é conectar remotamente na máquina virtual.

**Conectar-se à máquina virtual RHEL 7.2** Siga as instruções no artigo [como fazer logon em uma máquina virtual executando Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).

O restante das etapas pressupõem que você usa o cliente acabamento SSH para se conectar à máquina virtual RHEL. Para obter mais informações, consulte a [página de Download de acabamento](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Abra o programa de acabamento.

2. Insira o **Nome do Host** de máquina virtual RHEL recém-criado. Neste exemplo, nossa máquina virtual tem o nome de host 'contoso-rhel.cloudapp .net'. Se você não tiver certeza do nome do host da sua máquina virtual, consulte o painel de máquina virtual no portal do Azure.

    ![Conectar acabamento](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)

3. Faça logon na máquina virtual usando as credenciais de administrador local especificado quando a máquina virtual foi criada. Neste exemplo, usamos a conta de administrador local "mahesh".

    ![Acabamento de logon](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalar pacotes necessários na máquina virtual Linux
Depois de conectar à máquina virtual, a próxima tarefa é instalar pacotes necessários para o ingresso no domínio na máquina virtual. Execute as seguintes etapas:

1. **Instalar realmd:** O pacote de realmd é usado para participar do domínio. No terminal acabamento, digite o seguinte comando:

    sudo yum instalar realmd

    ![Instalar o realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)

    Após alguns minutos, o pacote de realmd deve obter instalado na máquina virtual.

    ![realmd instalado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)

3. **Instalar sssd:** O pacote de realmd depende sssd para executar operações de junção de domínio. No terminal acabamento, digite o seguinte comando:

    sudo yum instalar sssd

    ![Instalar o sssd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)

    Após alguns minutos, o pacote de sssd deve obter instalado na máquina virtual.

    ![realmd instalado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)

4. **Instalar kerberos:** No terminal acabamento, digite o seguinte comando:

    sudo yum instalar krb5-estação de trabalho krb5-bibliotecas

    ![Instalar o kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)

    Após alguns minutos, o pacote de realmd deve obter instalado na máquina virtual.

    ![Kerberos instalado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Ingressar o computador virtual Linux no domínio gerenciado
Agora que os pacotes necessários são instalados na máquina virtual Linux, a próxima tarefa é ingressar na máquina virtual para o domínio gerenciado.

1. Descubra o domínio gerenciado AAD serviços de domínio. No terminal acabamento, digite o seguinte comando:

    Descubra o território sudo CONTOSO100.COM

    ![Descubra o território](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)

    Se **descobrir território** não consegue encontrar seu domínio gerenciado, certifique-se de que o domínio está acessível na máquina virtual (tente ping). Também, certifique-se de que a máquina virtual realmente foi implantada à mesma rede virtual no qual o domínio gerenciado está disponível.

2. Inicialize kerberos. No terminal acabamento, digite o seguinte comando. Certifique-se de que você especificar um usuário que pertence ao grupo 'Administradores de DC AAD'. Apenas esses usuários podem ingressar computadores ao domínio gerenciado.

    kinitbob@CONTOSO100.COM

    ![Kinit](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)

    Certifique-se de que você especificar o nome de domínio em letras maiusculas, kinit mais falha.

3. Participar da máquina para o domínio. No terminal acabamento, digite o seguinte comando. Especifique o mesmo usuário que você especificou na etapa anterior ('kinit').

    junção de território sudo - CONTOSO100.COM -U detalhado'bob@CONTOSO100.COM'

    ![Junção de território](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

Você deve receber uma mensagem ("registrados com êxito máquina no território") quando a máquina com êxito ingressou no domínio gerenciado.


## <a name="verify-domain-join"></a>Verifique se a associação de domínio
Rapidamente, você pode verificar se o computador ingressou com êxito para o domínio gerenciado. Conectar-se ao recentemente integrado ao domínio RHEL VM usando SSH e uma conta de usuário de domínio e marque para ver se a conta de usuário é resolvida corretamente.

1. No terminal acabamento, digite o seguinte comando para se conectar à recentemente integrado ao domínio máquina virtual RHEL usando SSH. Usar uma conta de domínio que pertence ao domínio gerenciado (por exemplo, 'bob@CONTOSO100.COM' nesse caso.)

    SSH -l bob@CONTOSO100.COM contoso-rhel.cloudapp.net

2. No terminal acabamento, digite o seguinte comando para ver se o diretório inicial foi inicializado corretamente.

    pwd

3. No seu terminal acabamento, digite o seguinte comando para ver se os membros do grupo estão sendo resolvidos corretamente.

    ID

Um exemplo de saída desses comandos a seguir:

![Verifique se a associação de domínio](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)


## <a name="troubleshooting-domain-join"></a>Solução de problemas de associação de domínio
Consulte o artigo de [associação de domínio de solução de problemas](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join) .


## <a name="related-content"></a>Conteúdo relacionado
- [Serviços de domínio Azure AD - guia de Introdução](./active-directory-ds-getting-started.md)

- [Ingressar em uma máquina de virtual do Windows Server em um domínio gerenciado de serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Como fazer logon em uma máquina virtual executando Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).

- [Instalando Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)

- [Red Hat Enterprise Linux 7 - Guia de integração do Windows](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
