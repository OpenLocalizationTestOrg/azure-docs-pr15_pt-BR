<properties
    pageTitle="Habilitar o Microsoft Windows Hello para empresas em sua organização | Microsoft Azure"
    description="Instruções de implantação para habilitar o Microsoft Passport em sua organização."
    services="active-directory"
    documentationCenter=""
    keywords="Configurar o Microsoft Passport, Microsoft Windows Hello para implantação de negócios"
    authors="markusvi"
    manager="femila"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="markvi"/>


# <a name="enable-microsoft-windows-hello-for-business-in-your-organization"></a>Habilitar o Microsoft Windows Hello para empresas em sua organização

Depois de [conectar dispositivos de domínio do Windows 10 com o Azure Active Directory](active-directory-azureadjoin-devices-group-policy.md), faça o seguinte para habilitar o Microsoft Windows Hello para empresas em sua organização:

1. Implantar o Gerenciador de configuração do System Center  

2. Definir configurações de política

3. Configurar o perfil do certificado  




## <a name="deploy-system-center-configuration-manager"></a>Implantar o Gerenciador de configuração do System Center 

Para implantar certificados de usuário com base em Windows Hello para chaves comerciais, você precisa do seguinte:

- **Ramificação atual do Gerenciador de configuração do sistema Centro** - você precisa instalar a versão 1606 ou melhor. Para obter mais informações, consulte a [documentação do Gerenciador de configuração do System Center](https://technet.microsoft.com/library/mt346023.aspx) e o [Blog da equipe do System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
- **Infraestrutura de chave pública (PKI)** - habilitar Microsoft Windows Hello para empresas usando certificados de usuário, você deve ter uma PKI no lugar. Se não tiver uma, ou você não quiser usá-lo para certificados de usuário, você pode implantar um novo controlador de domínio com o Windows Server 2016 compilação 10551 (ou melhor) instalado. Siga as etapas para [instalar um controlador de domínio de réplica em um domínio existente](https://technet.microsoft.com/library/jj574134.aspx) ou [instalar uma nova floresta do Active Directory, se você estiver criando um novo ambiente](https://technet.microsoft.com/library/jj574166). (Os ISOs estão disponíveis para download na [Troca de mídia Signiant](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true).)


## <a name="configure-policy-settings"></a>Definir configurações de política

Para configurar o Microsoft Windows Hello para configurações de política de negócios, você tem duas opções:

- Política de grupo no Active Directory 
- O Gerenciador de configuração do System Center 


Política de grupo no Active Directory é o método recomendado para configurar o Microsoft Windows Hello para configurações de política de negócios. 



Usar o Gerenciador de configuração do System Center é o método preferencial quando você usá-lo também para implantar certificados. Neste cenário:

- Garante a compatibilidade com os cenários de implantação mais recentes
- Requer no lado do cliente 1607 de versão do Windows 10 ou superior.

### <a name="configure-microsoft-windows-hello-for-business-via-group-policy-in-active-directory"></a>Configurar o Microsoft Windows Hello para empresas via diretiva de grupo no Active Directory

 
**Etapas**:

1.  Abrir o Gerenciador de servidor e navegue até **Ferramentas** > **Gerenciamento de política de grupo**.
2.  Do gerenciamento de política de grupo, navegue até o nó do domínio que corresponde ao domínio no qual você deseja habilitar Azure AD ingressar.
3.  **Objetos de política de grupo**de atalho e, em seguida, selecione **novo**. Nomeie seu objeto de política de grupo, por exemplo, habilitar Windows Hello para empresas. Clique em **Okey**.
4.  Clique com botão direito seu novo objeto de política de grupo e selecione **Editar**.
5.  Navegue para **configuração do computador** > **políticas** > **modelos administrativos** > **componentes do Windows** > **saudação do Windows para empresas**.
6.  Clique com botão direito **Habilitar Windows Hello para empresas**e selecione **Editar**.
7.  Selecione o botão de opção **ativado** e, em seguida, clique em **Aplicar**. Clique em **Okey**.
8.  Agora você pode vincular o objeto de política de grupo para um local de sua escolha. Para habilitar essa política para todos os dispositivos de Windows 10 domínio em sua organização, vincule a política de grupo para o domínio. Por exemplo:
 - Uma unidade organizacional específica (OU) no Active Directory onde computadores de domínio do Windows 10 ficarão localizados
 - Um grupo de segurança específico que contenha computadores Windows 10 domínio que serão automaticamente registrados com o Azure AD


### <a name="configure-windows-hello-for-business-using-system-center-configuration-manager"></a>Configurar o Windows Hello for Business usando o Gerenciador de configuração do System Center


**Etapas**:


1. Abrir o **Gerenciador de configuração do System Center**e, em seguida, navegue até **ativos e conformidade > configurações de conformidade > acesso ao recurso da empresa > Windows Hello perfis de negócios de**.

    ![Configurar o Windows Hello para empresas](./media/active-directory-azureadjoin-passport-deployment/01.png)


2. Na barra de ferramentas na parte superior, clique em **Criar Windows Hello para empresas perfil**.

    ![Configurar o Windows Hello para empresas](./media/active-directory-azureadjoin-passport-deployment/02.png)

2. Na caixa de diálogo **Geral** , execute as seguintes etapas:

    ![Configurar o Windows Hello para empresas](./media/active-directory-azureadjoin-passport-deployment/03.png)

    a. Na caixa de texto **nome** , digite um nome para seu perfil, por exemplo, **Meu perfil WHfB**.

    b. Clique em **Avançar**.


2. Na caixa de diálogo **Plataformas suportadas** , selecione as plataformas que serão configuradas com este Windows Hello de perfil de negócios e clique em **Avançar**.

    ![Configurar o Windows Hello para empresas](./media/active-directory-azureadjoin-passport-deployment/04.png)


2. Na caixa de diálogo **configurações** , execute as seguintes etapas:

    ![Configurar o Windows Hello para empresas](./media/active-directory-azureadjoin-passport-deployment/05.png)

    a. Como **Configurar o Windows Hello para empresas**, selecione **habilitado**.

    b. Como **usar um módulo de plataforma confiável (TPM)**, selecione **necessários**. 

    c. Como **método de autenticação**, selecione **baseada em certificados**.

    d. Clique em **Avançar**.



2. Na caixa de diálogo **Resumo** , clique em **Avançar**.

2. Na caixa de diálogo de **conclusão** , clique em **Fechar**.


2. Na barra de ferramentas na parte superior, clique em **implantar**.

    ![Configurar o Windows Hello para empresas](./media/active-directory-azureadjoin-passport-deployment/06.png)



## <a name="configure-the-certificate-profile"></a>Configurar o perfil do certificado 

Se você estiver usando a autenticação baseada em certificado para autenticação de locais, é necessário configurar e implantar um perfil de certificado. Esta tarefa requer que você configure um servidor NDES e a função de ponto de registro de certificado de site no Gerenciador de configuração de sistema central. Para obter mais detalhes, consulte os [pré-requisitos para perfis de certificado no Gerenciador de configuração](https://technet.microsoft.com/library/dn261205.aspx).

1. Abrir o **Gerenciador de configuração do System Center**e, em seguida, navegue até **ativos e conformidade > configurações de conformidade > acesso ao recurso da empresa > perfis de certificado**.


2. Selecione um modelo que tem o cartão inteligente entrar uso estendido de chave (EKU).

Na página de **Inscrição de SCEP** do perfil do certificado, você precisa escolher **falham instalar Passport para trabalho caso contrário,** como o **Provedor de armazenamento de chave**.



## <a name="next-steps"></a>Próximas etapas
* [Windows 10 para a empresa: maneiras de usar dispositivos para trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio de participar do Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticação de identidades sem senhas por meio do Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de uso para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos de domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o Azure AD ingressar](active-directory-azureadjoin-setup.md)
