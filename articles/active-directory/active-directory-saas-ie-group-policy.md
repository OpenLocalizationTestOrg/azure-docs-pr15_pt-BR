<properties
    pageTitle="Como implantar a extensão do painel de acesso do Internet Explorer usando a política de grupo | Microsoft Azure"
    description="Como usar a política de grupo para implantar o complemento do Internet Explorer para o portal de meus aplicativos."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/16/2016"
    ms.author="markvi"/>

#<a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Como implantar a extensão do painel de acesso do Internet Explorer usando a política de grupo

Este tutorial mostra como usar a política de grupo para instalar remotamente a extensão do painel de acesso para o Internet Explorer em máquinas dos usuários. Essa extensão é necessária para os usuários do Internet Explorer que precisam entrar em aplicativos que são configurados usando [baseada em senha logon único](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

É recomendável que os administradores automatizam a implantação da extensão. Caso contrário, usuários terão baixar e instalar a extensão sozinhos, que está sujeita a erro de usuário e requer permissões de administrador. Este tutorial apresenta um método para automatizar implantações de software usando a política de grupo. [Saiba mais sobre a política de grupo.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

A extensão do painel de acesso também está disponível para o [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) e [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), nenhum dos quais exigir permissões de administrador para instalar.

##<a name="prerequisites"></a>Pré-requisitos

- Configurar [Serviços de domínio Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)e você ingressou máquinas dos usuários para o seu domínio.
- Você deve ter a permissão de "Editar configurações" para editar objetos de política de grupo (GPOs). Por padrão, os membros dos grupos de segurança a seguir têm essa permissão: administradores de domínio, administradores de empresa e proprietários de criadores de diretiva de grupo. [Saiba Mais.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

##<a name="step-1-create-the-distribution-point"></a>Etapa 1: Criar o ponto de distribuição

Primeiro, você deve colocar o pacote de instalação em um local de rede que possa ser acessado de todas as máquinas que você deseja instalar a extensão remotamente em. Para fazer isso, siga estas etapas:

1. Faça logon no servidor como um administrador

2. Na janela **Gerenciador de servidor** , vá para **arquivos e serviços de armazenamento**.

    ![Abrir arquivos e serviços de armazenamento](./media/active-directory-saas-ie-group-policy/files-services.png)

3. Vá para a guia de **compartilhamentos** . Em seguida, clique em **tarefas** > **Novo compartilhamento …**

    ![Abrir arquivos e serviços de armazenamento](./media/active-directory-saas-ie-group-policy/shares.png)

4. Conclua o **Assistente de compartilhamento** e defina permissões para garantir que ela pode ser acessada de máquinas dos usuários. [Saiba mais sobre compartilhamentos.](https://technet.microsoft.com/library/cc753175.aspx)

5. Baixe o pacote do Microsoft Windows Installer (arquivo msi) seguinte: [Extension.msi do painel de acesso](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Panel Extension.msi)

6. Copie o pacote de instalação para o local desejado no compartilhamento.

    ![Copiar o arquivo. msi a você é o compartilhamento.](./media/active-directory-saas-ie-group-policy/copy-package.png)

8. Verifique se suas máquinas cliente são capazes de acessar o pacote de instalação do compartilhamento. 

##<a name="step-2-create-the-group-policy-object"></a>Etapa 2: Criar o objeto de política de grupo

1. Faça logon no servidor que hospeda sua instalação de serviços de domínio Active Directory (AD DS).

2. No Gerenciador do servidor, vá para **Ferramentas** > **Gerenciamento de política de grupo**.

    ![Vá para Ferramentas > Gerenciamento de política de grupo](./media/active-directory-saas-ie-group-policy/tools-gpm.png)

3. No painel esquerdo da janela de **Gerenciamento de política de grupo** , exiba sua hierarquia de unidade organizacional (OU) e determinar quais escopo que você gostaria de aplicar a política de grupo. Por exemplo, você pode decidir escolher uma pequena OU para implantar em alguns usuários de teste, ou você pode escolher uma OU de nível superior para implantar em toda a sua organização.

    > [AZURE.NOTE] Se você gostaria de criar ou editar sua organização (unidades organizacionais), alterne de volta para o Gerenciador de servidores e vá para **Ferramentas** > **e computadores do Active Directory Users**.

4. Assim que você tiver selecionado uma unidade Organizacional, clique com botão direito nele e selecione **criar um GPO neste domínio e vinculá-lo aqui...**

    ![Criar um novo GPO](./media/active-directory-saas-ie-group-policy/create-gpo.png)

5. No prompt de **Novo GPO** , digite um nome para o novo objeto de política de grupo.

    ![Nomeie o novo GPO](./media/active-directory-saas-ie-group-policy/name-gpo.png)

6. Clique com botão direito no objeto de política de grupo que você acabou de criar e, em seguida, selecione **Editar**.

    ![Editar o novo GPO](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

##<a name="step-3-assign-the-installation-package"></a>Etapa 3: Atribuir o pacote de instalação

1. Determine se você gostaria de implantar a extensão com base na **Configuração do computador** ou **Configuração do usuário**. Ao usar a [configuração do computador](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), a extensão será instalada no computador, independentemente de quais usuários se conectem--lo. Por outro lado, com a [configuração do usuário](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), os usuários terão a extensão instalada para eles, independentemente de quais computadores fazem logon.

2. No painel esquerdo da janela do **Editor de gerenciamento de política de grupo** , vá para qualquer um dos seguintes caminhos de pasta, dependendo do tipo de configuração que você escolheu:
    - `Computer Configuration/Policies/Software Settings/`
    - `User Configuration/Policies/Software Settings/`

3. Clique com botão direito na **instalação do Software**, em seguida, selecione **novo** > **pacote...**

    ![Criar um novo pacote de instalação do software](./media/active-directory-saas-ie-group-policy/new-package.png)

4. Vá para a pasta compartilhada que contém o pacote de instalação do [etapa 1: criar o ponto de distribuição](#step-1-create-the-distribution-point), selecione o arquivo. msi e clique em **Abrir**.

    > [AZURE.IMPORTANT] Se o compartilhamento está localizado no mesmo servidor, verifique se que você está acessando o arquivo. msi através o caminho do arquivo de rede, em vez do caminho do arquivo local.

    ![Selecione o pacote de instalação da pasta compartilhada.](./media/active-directory-saas-ie-group-policy/select-package.png)

5. No prompt de **Implantar Software** , selecione **atribuído** para seu método de implantação. Clique em **Okey**.

    ![Selecione atribuído, clique em Okey.](./media/active-directory-saas-ie-group-policy/deployment-method.png)

A extensão agora é implantada para a unidade Organizacional que você selecionou. [Saiba mais sobre a instalação do Software de política de grupo.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

##<a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Etapa 4: Ativar automática a extensão para o Internet Explorer 

Além de executar o instalador, todas as extensões para o Internet Explorer devem ser habilitada explicitamente antes que ela possa ser usada. Siga as etapas abaixo para habilitar a extensão do painel de acesso usando a política de grupo:

1. Na janela do **Editor de gerenciamento de política de grupo** , vá para qualquer um dos seguintes caminhos, dependendo do tipo de configuração que você escolheu na [etapa 3: atribuir o pacote de instalação](#step-3-assign-the-installation-package):
    - `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

2. Clique com botão direito na **Lista de complementos**e selecione **Editar**.
    ![Edite lista de complementos.](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)

3. Na janela de **Lista de complementos** , selecione **habilitado**. Em seguida, na seção **Opções** , clique em **Mostrar...**.

    ![Clique em Habilitar, clique em Mostrar...](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)

4. Na janela **Mostrar conteúdo** , execute as seguintes etapas:

    1. Para a primeira coluna (o campo **Nome do valor** ), copie e cole a seguinte identificação de classe:`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`

    2. Na segunda coluna (campo de **valor** ), digite o seguinte valor:`1`

    3. Clique em **Okey** para fechar a janela **Mostrar conteúdo** .

    ![Preencha os valores especificados acima.](./media/active-directory-saas-ie-group-policy/show-contents.png)

5. Clique em **Okey** para aplicar as alterações e feche a janela de **Lista de complementos** .

A extensão agora deve estar habilitada para as máquinas da unidade organizacional selecionada. [Saiba mais sobre como usar a política de grupo para habilitar ou desabilitar complementos do Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

##<a name="step-5-optional-disable-remember-password-prompt"></a>Etapa 5 (opcional): desativar o aviso de "Lembrar senha"

Quando os usuários entrar para sites que usam a extensão do painel de acesso, o Internet Explorer pode mostrar a seguinte solicitação perguntando "Você gostaria de armazenar sua senha?"

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

Se você quiser impedir que os usuários vendo este prompt, em seguida, siga as etapas abaixo para impedir que o preenchimento automático de lembrar senhas:

1. Na janela do **Editor de gerenciamento de política de grupo** , vá para o caminho listado abaixo. Observe que esta configuração só está disponível em **Configuração do usuário**.
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`

2. Localize a configuração denominada **Ativar o recurso Preenchimento automático para nomes de usuário e senhas em formulários**.

    > [AZURE.NOTE] Versões anteriores do Active Directory podem listar essa configuração com o nome **não permitir preenchimento automático para salvar senhas**. A configuração para essa configuração é diferente da configuração descrita neste tutorial.

    ![Lembre-se procure por isso em configurações de usuário.](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)

3. Clique com o botão direito na configuração acima e selecione **Editar**.

4. Na janela intitulada **Ative o recurso Preenchimento automático para nomes de usuário e senhas em formulários**, selecione **desabilitado**.

    ![Selecione Desativar](./media/active-directory-saas-ie-group-policy/disable-passwords.png)

5. Clique em **Okey** para aplicar essas alterações e feche a janela.

Os usuários não poderão armazenar suas credenciais ou use preenchimento automático para acessar as credenciais armazenadas anteriormente. No entanto, essa política permite aos usuários continuar a usar o preenchimento automático para outros tipos de campos de formulário, como campos de pesquisa.

> [AZURE.WARNING] Se esta política estiver habilitada depois que os usuários tem escolhido para armazenar algumas credenciais, esta será de política *não* limpar as credenciais que já foram armazenadas.

##<a name="step-6-testing-the-deployment"></a>Etapa 6: Testar a implantação

Siga as etapas abaixo para verificar se a implantação de extensão foi bem-sucedida:

1. Se você implantou usando a **Configuração do computador**, entrar em um computador cliente que pertence à OU que você selecionou no [etapa 2: criar o objeto de política de grupo](#step-2-create-the-group-policy-object). Se você implantou usando a **Configuração do usuário**, verifique se entrar como um usuário que pertence a essa OU.

2. Pode levar alguns entrada ins para a política de grupo é alterado para totalmente atualizar com esta máquina. Para forçar a atualização, abra uma janela do **Prompt de comando** e execute o seguinte comando:`gpupdate /force`

3. Você precisará reiniciar o computador para a instalação ocorra. Inicialização pode levar muito mais tempo do que instala costume durante a extensão.

4. Depois de reiniciar, abra o **Internet Explorer**. No canto superior direito da janela, clique em **Ferramentas** (o ícone de engrenagem) e, em seguida, selecione **Gerenciar complementos**.

    ![Vá para Ferramentas > Gerenciar complementos](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)

5. Na janela **Gerenciar complementos** , verifique se a **Extensão do painel de acesso** foi instalado e que seu **Status** foi definido como **ativado**.

    ![Verifique se a extensão do painel de acesso é instalada e ativada.](./media/active-directory-saas-ie-group-policy/verify-install.png)

## <a name="related-articles"></a>Artigos relacionados

- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
- [Acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md)
- [A extensão do painel de acesso de solução de problemas para o Internet Explorer](active-directory-saas-ie-troubleshooting.md)
