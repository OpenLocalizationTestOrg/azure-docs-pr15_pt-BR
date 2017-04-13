<properties
   pageTitle="Publicar um serviço de nuvem usando as ferramentas do Azure | Microsoft Azure"
   description="Saiba mais sobre como publicar projetos de serviço de nuvem Azure usando o Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="publishing-a-cloud-service-using-the-azure-tools"></a>Publicar um serviço de nuvem usando as ferramentas do Azure

Usando as ferramentas do Azure para Microsoft Visual Studio, você pode publicar seu aplicativo do Azure diretamente do Visual Studio. Visual Studio oferece suporte a publicação integrada para o teste ou o ambiente de produção de um serviço de nuvem.

Antes de publicar um aplicativo do Azure, você deve ter uma assinatura do Azure. Você também deve configurar uma conta de armazenamento e serviços de nuvem para ser usado pelo seu aplicativo. Você pode configurar esses no [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885).

>[AZURE.IMPORTANT] Quando você publica, você pode selecionar o ambiente de implantação de seu serviço de nuvem. Você também deve selecionar uma conta de armazenamento que é usada para armazenar o pacote de aplicativo para implantação. Após a implantação, o pacote de aplicativo é removido da conta de armazenamento.

Quando você estiver desenvolvendo e testando um aplicativo do Azure, você pode usar a implantação da Web para publicar as alterações de forma incremental para suas funções da web. Depois de publicar seu aplicativo para um ambiente de implantação, implantar Web permite que você implantar alterações diretamente à máquina virtual que esteja executando a função web. Você não precisa empacotar e publicar seu aplicativo do Azure inteiro sempre que você deseja atualizar sua função web para testar as alterações. Com essa abordagem, você pode ter as alterações de função web disponíveis na nuvem para teste sem aguardando para ter seu aplicativo publicado em um ambiente de implantação.

Use os procedimentos a seguir para publicar seu aplicativo do Azure e atualizar uma função web usando a implantação da Web:

- Publicar ou empacotar um aplicativo do Azure do Visual Studio

- Atualizar uma função web como parte do ciclo de teste e desenvolvimento

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Publicar ou empacotar um aplicativo do Azure do Visual Studio

Quando você publica seu aplicativo do Azure, você pode fazer uma das seguintes tarefas:

- Criar um pacote de serviço: você pode usar este pacote e o arquivo de configuração do serviço para publicar seu aplicativo para um ambiente de implantação do [Portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

- Publicar seu projeto Azure do Visual Studio: para publicar seu aplicativo diretamente para o Azure, você usa o Assistente de publicação. Para obter informações, consulte [Publicar Assistente de aplicativo do Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Para criar um pacote de serviço do Visual Studio

1. Quando você estiver pronto para publicar seu aplicativo, abra o Solution Explorer, abra o menu de atalho para o projeto Azure que contém suas funções e escolha publicar.

1. Para criar um pacote de serviço, siga estas etapas:  

  1. No menu de atalho para o projeto Azure, escolha o **pacote**.

  1. Na caixa de diálogo **Pacote Azure aplicativo** , escolha a configuração do serviço para o qual você deseja criar um pacote e, em seguida, escolha a configuração de compilação.

  1. (opcional) Para ativar área de trabalho remota para o serviço de nuvem após publicá-la, marque a caixa de seleção **Habilitar a área de trabalho remota para todas as funções** e, em seguida, selecione **configurações** para configurar área de trabalho remota. Se você quiser depurar seu serviço de nuvem após publicá-la, ative a depuração remota, selecionando **Ativar depurador remoto para todas as funções**.

      Para obter mais informações, consulte [Usando a área de trabalho remota com funções do Azure](vs-azure-tools-remote-desktop-roles.md).

  1. Para criar o pacote, escolha o link de **pacote** .

      Explorador de arquivos mostra o local do arquivo do pacote recém-criado. Você pode copiar esse local para que você pode usá-lo a partir do [portal clássico Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

  1. Para publicar este pacote em um ambiente de implantação, você deve usar esse local como o local do pacote, quando você cria um serviço na nuvem e implanta este pacote em um ambiente com o [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Opcional) Para cancelar o processo de implantação, no menu de atalho para o item de linha no registro de atividade, escolha **Cancelar e remover**. Isso interrompe o processo de implantação e exclui o ambiente de implantação do Azure.

    >[AZURE.NOTE] Para remover esse ambiente de implantação após ele ter sido implantado, você deve usar o [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Opcional) Depois que sua instâncias de função tenham iniciado, o Visual Studio automaticamente mostra o ambiente de implantação no nó de **Serviços de nuvem** no Server Explorer. A partir daqui, você pode ver o status das instâncias de função individuais. Consulte [recursos de gerenciamento de Azure com o Explorador de nuvem](vs-azure-tools-resources-managing-with-cloud-explorer.md). A ilustração a seguir mostra as instâncias de função enquanto elas ainda estão no estado de inicialização:

    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Atualizar uma função Web como parte do ciclo de teste e desenvolvimento

Se infraestrutura de back-end do seu aplicativo é estável, mas as funções da web precisam de mais frequentes atualizando, você pode usar a implantação da Web para atualizar apenas uma função da web em seu projeto. Isso é útil quando você não deseja recriar e reimplantar as funções de operador de back-end, ou se você tiver várias funções da web e que você deseja atualizar apenas uma das funções da web.

### <a name="requirements"></a>Requisitos

Aqui estão os requisitos para usar a implantação da Web para atualizar sua função da web:

- **Para desenvolvimento e teste fins apenas:** As alterações são feitas diretamente à máquina virtual onde a função web está sendo executado. Se esta máquina virtual precisa ser reciclados, as alterações serão perdidas porque o pacote original que você publicou é usado para recriar a máquina virtual para a função. Você deve republicar seu aplicativo para ver as alterações mais recentes para a função web.

- **Somente funções da web podem ser atualizadas:** Funções de trabalho não podem ser atualizadas. Além disso, você não pode atualizar o RoleEntryPoint no role.cs da web.

- **Só pode oferecer suporte a uma única instância de uma função web:** Você não pode ter várias instâncias de qualquer função web no seu ambiente de implantação. No entanto, há suporte para várias funções da web cada com apenas uma instância.

- **Você deve habilitar conexões de área de trabalho remota:** Isso é necessário para que a implantação da Web pode usar o usuário e a senha para se conectar à máquina virtual para implantar as alterações no servidor que esteja executando serviços de informações da Internet (IIS). Além disso, talvez seja necessário conectar-se na máquina virtual para adicionar um certificado confiável ao IIS nessa máquina. (Isso garante que a conexão remota para IIS usado pela implantação da Web é seguro).

O procedimento a seguir pressupõe que você está usando o Assistente para **Publicar aplicativos do Azure** .

### <a name="to-enable-web-deploy-when-you-publish-your-application"></a>Para habilitar Web implantar quando você publica seu aplicativo

1. Para habilitar a **Habilitar a implantação da Web** para todas as caixa de seleção de funções de web, você deve primeiro configurar conexões de área de trabalho remota. Selecione **Habilitar a área de trabalho remota** para todas as funções e, em seguida, forneça as credenciais que serão usadas para se conectar remotamente na caixa **Configuração de área de trabalho remota** que aparece. Consulte [Usando a área de trabalho remota com funções do Azure](vs-azure-tools-remote-desktop-roles.md) para obter mais informações.

1. Para habilitar a implantação da Web para todas as funções da web em seu aplicativo, selecione **Habilitar Web implantar para todas as funções da web**.

    Um triângulo amarelo de aviso é exibida. Implantar Web usa um certificado não confiável, auto-assinado por padrão, o que não é recomendado para carregar dados confidenciais. Se você precisar proteger esse processo para dados confidenciais, você pode adicionar um certificado SSL a ser usado para conexões de implantação da Web. Este certificado deve ser um certificado confiável. Para obter informações sobre como fazer isso, consulte a seção **Para Web tornar implantar seguro** neste tópico.

1. Escolha **Avançar** para mostrar a tela de **Resumo** e escolha **Publicar** para implantar o serviço de nuvem.

    O serviço de nuvem é publicado. A máquina virtual que é criada tem conexões remotas habilitadas para IIS para que a implantação da Web pode ser usado para atualizar suas funções da web sem republicá-los.

    >[AZURE.NOTE] Se você tiver mais de uma instância configurada para uma função da web, é exibida uma mensagem de aviso informando que cada função web serão limitada a uma instância somente no pacote que é criada para publicar seu aplicativo. Selecione **Okey** para continuar. Conforme mencionado na seção requisitos, você pode ter mais de uma função da web, mas apenas uma instância de cada função.

### <a name="to-update-your-web-role-by-using-web-deploy"></a>Para atualizar sua função Web usando implantação da Web

1. Para usar a implantação da Web, faça alterações no código do projeto para qualquer uma das suas funções da web no Visual Studio que você deseja publicar e este nó de projeto na sua solução de atalho e, em seguida, aponte para **Publicar**. Caixa de diálogo **Publicar Web** é exibida.

1. (Opcional) Se você adicionou um certificado SSL confiável para usar para conexões remotas para IIS, você pode desmarcar a caixa de seleção de **certificado não confiável permitir** . Para obter informações sobre como adicionar um certificado para tornar segura a implantação da Web, consulte a seção **Para tornar Web implantar seguro** mais adiante neste tópico.

1. Para usar a implantação da Web, o mecanismo de publicação necessidades o nome de usuário e senha que você configurar para sua conexão de área de trabalho remota quando você publicou primeiro o pacote.

  1. Em **nome de usuário**, digite o nome de usuário.

  1. Em **senha**, digite a senha.

  1. (Opcional) Se você quiser salvar esta senha neste perfil, escolha **Salvar senha**.

1. Para publicar as alterações em sua função da web, escolha **Publicar**.

    A linha de status exibe **Publicar iniciado**. Quando a publicação estiver concluída, **Publicar bem-sucedida** é exibida. As alterações agora foram implantadas à função web em sua máquina virtual. Agora você pode iniciar seu aplicativo do Azure no ambiente do Azure para testar as alterações.

### <a name="to-make-web-deploy-secure"></a>Para tornar o implantar Web seguro

1. Implantar Web usa um certificado não confiável, auto-assinado por padrão, o que não é recomendado para carregar dados confidenciais. Se você precisar proteger esse processo para dados confidenciais, você pode adicionar um certificado SSL a ser usado para conexões de implantação da Web. Este certificado deve ser um certificado confiável, o que você obtém de uma autoridade de certificação (CA).

    Para tornar Web implantar seguras para cada máquina virtual para cada uma das suas funções da web, você deve carregar o certificado de confiança que você deseja usar para web implantar [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885). Isso garante que o certificado é adicionado à máquina virtual que é criada para a função web quando você publica seu aplicativo.

1. Para adicionar um certificado SSL confiável IIS para usar para conexões remotas, siga estas etapas:

  1. Para se conectar à máquina virtual que esteja executando a função da web, selecione a instância da função web no **Explorador de nuvem** ou **Server Explorer**e, em seguida, escolha o comando **Conectar usando a área de trabalho remota** . Para obter etapas detalhadas sobre como se conectar à máquina virtual, consulte [Usando a área de trabalho remota com funções do Azure](vs-azure-tools-remote-desktop-roles.md).

      Seu navegador solicitará que você baixe um. Arquivo RDP.

  1. Para adicionar um certificado SSL, abra o serviço de gerenciamento no Gerenciador do IIS. No Gerenciador do IIS, habilite o SSL abrindo o link de **ligações** no painel **ação** . A caixa de diálogo **Adicionar vinculação de Site** é exibida. Escolha **Adicionar**e, em seguida, escolha HTTPS na lista suspensa **tipo** . Na lista de **certificado SSL** , escolha o certificado SSL que você tinha assinado por uma autoridade de certificação e que você carregou o [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885). Para obter mais informações, consulte [Definir configurações de Conexão para o serviço de gerenciamento](http://go.microsoft.com/fwlink/?LinkId=215824).

      >[AZURE.NOTE] Se você adicionar um certificado SSL confiável, o triângulo amarelo de aviso não aparece mais no **Assistente de publicação**.

## <a name="include-files-in-the-service-package"></a>Incluir arquivos no pacote de serviço

Talvez seja necessário incluir arquivos específicos no seu pacote de serviço para que elas fiquem disponíveis na máquina virtual que é criada para uma função. Por exemplo, você talvez queira adicionar uma .exe ou um arquivo. msi usado por um script de inicialização ao seu pacote de serviço. Ou talvez você precise adicionar um assembly que requer um projeto de função de função ou colega da web. Para incluir arquivos, eles devem ser adicionados à solução para o seu aplicativo do Azure.

### <a name="to-include-files-in-the-service-package"></a>Para incluir arquivos no pacote de serviço

1. Para adicionar um assembly a um pacote de serviço, use as seguintes etapas:

  1. No **Solution Explorer** , abra o nó do projeto para o projeto que está faltando assembly referenciado.

  1. Para adicionar o assembly ao projeto, abra o menu de atalho para a pasta de **referências** e escolha **Adicionar referência**. A caixa de diálogo Add Reference é exibida.

  1. Escolha a referência que você deseja adicionar e, em seguida, escolha o botão de **Okey** .

      A referência é adicionada à lista sob a pasta de **referências** .

  1. Abrir o menu de atalho para o conjunto que você adicionou e escolha **Propriedades**. A janela de **Propriedades** aparece.

      Para incluir este assembly no pacote do serviço, na **lista cópia Local** escolha **True**.

1. No **Solution Explorer** , abra o nó do projeto para o projeto que está faltando assembly referenciado.

1. Para adicionar o assembly ao projeto, abra o menu de atalho para a pasta de **referências** e escolha **Adicionar referência**. A caixa de diálogo **Add Reference** é exibida.

1. Escolha a referência que você deseja adicionar e, em seguida, escolha o botão de **Okey** .

    A referência é adicionada à lista sob a pasta de **referências** .

1. Abrir o menu de atalho para o conjunto que você adicionou e escolha **Propriedades**. A janela de propriedades aparece.

1. Para incluir este assembly no pacote do serviço, na lista de **Cópia Local** , escolha **True**.

1. Para incluir arquivos no pacote de serviço que foram adicionados ao seu projeto de função web, abra o menu de atalho para o arquivo e escolha **Propriedades**. Na janela de **Propriedades** , escolha **conteúdo** da caixa de listagem **Build Action** .

1. Para incluir arquivos no pacote de serviço que foram adicionados ao seu projeto de função de trabalho, abra o menu de atalho para o arquivo e escolha **Propriedades**. Na janela de **Propriedades** , escolha **Copiar se mais recente** da caixa de listagem **Copiar para diretório de saída** .

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como publicar no Azure do Visual Studio, consulte [Publicar Assistente de aplicativo do Azure](vs-azure-tools-publish-azure-application-wizard.md).
