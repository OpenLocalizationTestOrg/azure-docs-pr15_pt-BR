<properties
   pageTitle="Preparar para publicar ou implantar um aplicativo do Azure do Visual Studio | Microsoft Azure"
   description="Saiba os procedimentos para configurar serviços de conta de armazenamento e nuvem e configurar seu aplicativo do Azure."
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

# <a name="prepare-to-publish-or-deploy-an-azure-application-from-visual-studio"></a>Preparar para publicar ou implantar um aplicativo do Azure do Visual Studio

## <a name="overview"></a>Visão geral

Antes de publicar um projeto de serviço de nuvem, você deve configurar os seguintes serviços:

- Um **serviço de nuvem** para executar suas funções no ambiente do Azure

- Uma **conta de armazenamento** que fornece acesso aos serviços Blob, fila e tabela.

Use os procedimentos a seguir para configurar esses serviços e configurar o aplicativo


## <a name="create-a-cloud-service"></a>Criar um serviço de nuvem

Para publicar um serviço de nuvem do Azure, você deve primeiro criar um serviço de nuvem, que executa suas funções no ambiente do Azure. Você pode criar um serviço de nuvem no do [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885), conforme descrito na seção **para criar um serviço de nuvem usando o portal de clássico Azure**, mais adiante neste tópico. Você também pode criar um serviço de nuvem no Visual Studio usando o Assistente de publicação.

### <a name="to-create-a-cloud-service-by-using-visual-studio"></a>Para criar um serviço de nuvem usando o Visual Studio

1. Abra o menu de atalho para o projeto Azure e escolha **Publicar**.

    ![VST_PublishMenu](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/vst-publish-menu.png)

1. Se você ainda não entrou no, entrar com seu nome de usuário e senha da conta da Microsoft ou conta organizacional que está associada a sua assinatura do Azure.

1. Escolha o botão **Avançar** para ir para a página de **configurações** .

    ![Configurações comuns de Assistente para publicação](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/publish-settings-page.png)

1. Na lista de **Serviços de nuvem** , escolha **Criar novo**. A caixa de diálogo **Criar Azure Services** é exibida.

1. Insira o nome do seu serviço de nuvem. O nome da parte da URL para o serviço de formulários e, portanto, deve ser exclusivo. O nome não diferencia maiusculas de minúsculas.

### <a name="to-create-a-cloud-service-by-using-the-azure-classic-portal"></a>Para criar um serviço de nuvem usando o portal de clássico Azure

1. Entrar no [portal clássico Azure](http://go.microsoft.com/fwlink/?LinkId=253103) no site da Microsoft.

1. (opcional) Para exibir uma lista de serviços de nuvem que você já tiver criado, escolha o link de serviços de nuvem no lado esquerdo da página.

1. Escolha o **+** ícone no canto inferior esquerdo de canto e, em seguida, escolha **O serviço de nuvem** no menu que aparece. Outra tela com duas opções, **Criar rápida** e **Criar personalizada**, é exibida. Se você escolher **Criar rápida**, você pode criar um serviço de nuvem apenas, especificando a URL dele e a região onde ele será física hospedado. Se você escolher **Criar personalizada**, você pode publicar imediatamente um serviço na nuvem, especificando um pacote (arquivo .cspkg), um arquivo de configuração (.cscfg) e um certificado. Criar personalizada não é necessário se você pretende publicar seu serviço de nuvem usando o comando **Publicar** em um projeto do Azure. O comando **Publicar** está disponível no menu de atalho para um projeto do Azure.

1. Escolha **Criar rápida** mais tarde publicar seu serviço de nuvem usando o Visual Studio.

1. Especifique um nome para seu serviço de nuvem. A URL completa aparece ao lado do nome.

1. Na lista, escolha a região onde a maioria dos usuários está localizada.

1. Na parte inferior da janela, escolha o link **Criar serviço de nuvem** .

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de armazenamento fornece acesso aos serviços de Blob, fila e tabela. Você pode criar uma conta de armazenamento usando o Visual Studio ou do [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkId=253103).

### <a name="to-create-a-storage-account-by-using-visual-studio"></a>Para criar uma conta de armazenamento usando o Visual Studio

1. No **Solution Explorer**, abra o menu de atalho para o nó de **armazenamento** e escolha **Criar conta de armazenamento**.

    ![Criar uma nova conta de armazenamento do Azure](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/IC744166.png)

1. Selecione ou insira as seguintes informações para a nova conta de armazenamento na caixa de diálogo **Criar conta de armazenamento** .
    - A assinatura do Azure ao qual você deseja adicionar a conta de armazenamento.
    - O nome que você deseja usar para a nova conta de armazenamento.
    - A região ou grupo de afinidade (como Oeste EUA ou da Ásia Oriental).
    - O tipo de replicação que você deseja usar para a conta de armazenamento, como localização geográfica redundantes.

1. Quando terminar, escolha **criar**. A nova conta de armazenamento aparece na lista de **armazenamento** no **Server Explorer**.

### <a name="to-create-a-storage-account-by-using-the-azure-classic-portal"></a>Para criar uma conta de armazenamento usando o portal de clássico Azure

1. Entrar no [portal clássico Azure](http://go.microsoft.com/fwlink/?LinkId=253103) no site da Microsoft.

1. (Opcional) Para exibir suas contas de armazenamento, escolha o link de **armazenamento** no painel no lado esquerdo da página.

1. No canto inferior esquerdo da página, escolha o **+** ícone.

1. No menu que aparece, escolha o **armazenamento**e escolha **Criar rápida**.

1. Dê um nome que resultará em uma url exclusiva para a conta de armazenamento.

1. Dê um nome para seu serviço de nuvem. A URL completa aparece ao lado do nome.

1. Na lista de regiões, escolha uma região onde a maioria dos usuários está localizada.

1. Especifique se deseja habilitar a replicação de localização geográfica. Se você habilitar replicação geográfica, seus dados serão salvos em vários locais físicos para reduzir a possibilidade de perda. Esse recurso torna o armazenamento mais caro, mas você pode reduzir o custo habilitando localização geográfica quando você cria a conta de armazenamento em vez de adicionar o recurso mais tarde. Para obter mais informações, consulte [replicação geográfica](http://go.microsoft.com/fwlink/?LinkId=253108).

1. Na parte inferior da janela, escolha o link **Criar conta de armazenamento** .

Depois de criar sua conta de armazenamento, você verá as URLs que você pode usar para acessar recursos em cada uma dos serviços de armazenamento do Azure e também as teclas de acesso primário e secundário para sua conta. Use estas teclas para autenticar solicitações feitas em relação aos serviços de armazenamento.

>[AZURE.NOTE] A tecla de acesso secundário fornece o mesmo acesso à sua conta de armazenamento que a chave primária de acesso e é gerada como um backup deve ser comprometida sua chave primária de acesso. Além disso, é recomendável que você gerar suas chaves de acesso regularmente. Você pode modificar uma configuração de cadeia de conexão para usar a chave secundária enquanto você gerar novamente a chave primária, então você pode modificá-lo para usar a chave primária gerado novamente enquanto você gerar novamente a chave secundária.

## <a name="configure-your-app-to-use-services-provided-by-the-storage-account"></a>Configurar seu aplicativo para usar os serviços fornecidos pela conta do armazenamento

Você deve configurar qualquer função que acessa os serviços de armazenamento para usar os serviços de armazenamento do Azure que você criou. Para fazer isso, você pode usar várias configurações de serviço para o seu projeto Azure. Por padrão, dois são criados no seu projeto Azure. Usando várias configurações de serviço, você pode usar a mesma cadeia de conexão no seu código, mas tem um valor diferente para uma cadeia de conexão em cada configuração de serviço. Por exemplo, você pode usar uma configuração de serviço para executar e depurar seu aplicativo localmente usando o emulador de armazenamento do Azure e uma configuração de serviço diferente para publicar seu aplicativo no Azure. Para obter mais informações sobre configurações de serviço, consulte [Configurando o Azure projeto usando várias configurações de serviço](vs-azure-tools-multiple-services-project-configurations.md).

### <a name="to-configure-your-application-to-use-services-that-the-storage-account-provides"></a>Para configurar seu aplicativo para usar os serviços que fornece a conta de armazenamento

1. No Visual Studio, abra sua solução Azure. No Solution Explorer, abra o menu de atalho para cada função em seu projeto Azure que acessa os serviços de armazenamento e escolha **Propriedades**. Uma página com o nome da função é exibida no editor do Visual Studio. A página exibe os campos para a guia **configuração** .

1. Nas páginas de propriedade para a função, escolha **configurações**.

1. Na lista de **Configuração do serviço** , escolha o nome da configuração do serviço que você deseja editar. Se você quiser fazer alterações em todas as configurações de serviço para essa função, você pode escolher **Todas as configurações**.  Para obter mais informações sobre como atualizar configurações de serviço, consulte a seção **Gerenciar cadeias de caracteres de Conexão para contas de armazenamento** no tópico [Configurar as funções para um serviço de nuvem do Azure com o Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Para modificar as configurações de cadeia de conexão, escolha o **…** botão ao lado da configuração. A caixa de diálogo **Criar cadeia de Conexão de armazenamento** aparece.

1. Em **conectar-se usando**, escolha a opção de **sua assinatura** .

1. Na lista de **assinatura** , escolha sua assinatura. Se a lista das assinaturas não incluir a opção desejada, escolha o link de **Baixar configurações de publicação** .

1. Na lista **nome da conta** , escolha o nome da sua conta de armazenamento. Ferramentas Azure obtém credenciais de conta de armazenamento automaticamente usando o arquivo. publishsettings. Para especificar suas credenciais de conta de armazenamento manualmente, escolha a opção **inseridos manualmente as credenciais** e, em seguida, continue com este procedimento. Você pode obter o seu nome de conta de armazenamento e a chave primária do [Azure portal clássico](http://go.microsoft.com/fwlink/p/?LinkID=213885). Se você não deseja especificar seu armazenamento de configurações de conta manualmente, escolha o botão de **Okey** para fechar a caixa de diálogo.

1. Escolha o link de credenciais de **conta de armazenamento Enter** .

1. Na caixa **nome da conta** , insira o nome da sua conta de armazenamento.

    >[AZURE.NOTE] Entrar no [portal clássico Azure](http://go.microsoft.com/fwlink/?LinkID=213885)e, em seguida, escolha o botão de **armazenamento** . O portal mostra uma lista de contas de armazenamento. Se você escolher uma conta, abre uma página para ele. Você pode copiar o nome da conta de armazenamento desta página. Se você estiver usando uma versão anterior do portal do clássico, o nome da sua conta de armazenamento aparece no modo de exibição **Contas de armazenamento** . Para copiar esse nome, realçá-la na janela **Propriedades** deste modo de exibição e, em seguida, escolha as teclas Ctrl + C. Cole o nome no Visual Studio, escolha a caixa de texto **nome da conta** e, em seguida, escolha as teclas Ctrl + V.

1. Na caixa **chave da conta** , insira a chave primária, ou copie e cole-a partir do [portal clássico Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
    Para copiar esta chave:

    1. Na parte inferior da página para a conta de armazenamento apropriado, escolha o botão de **Gerenciamento de chaves** .

    1. Na página **Gerenciar o acesso de chaves** , selecione o texto da chave primária de acesso e escolha as teclas Ctrl + C.

    1. Em ferramentas do Azure, cole a chave na caixa **chave da conta** .

    1. Você deve selecionar uma das opções a seguir para determinar como o serviço irá acessar a conta de armazenamento:
        - **Usar HTTP**. Esta é a opção padrão. Por exemplo, `http://<account name>.blob.core.windows.net`.
        - **Usar HTTPS** para uma conexão segura. Por exemplo, `https://<accountname>.blob.core.windows.net`.
        - **Especificar pontos de extremidade personalizados** para cada um dos três serviços. Você pode digitar esses pontos de extremidade para o campo o serviço específico.

        >[AZURE.NOTE] Se você criar pontos de extremidade personalizados, você pode criar uma cadeia de conexão mais complexa. Quando você usa esse formato de cadeia de caracteres, você pode especificar pontos de extremidade de serviço de armazenamento que incluem um nome de domínio personalizado que você registrou para sua conta de armazenamento com o serviço de Blob. Além disso, você pode conceder acesso somente aos recursos de blob em um único contêiner por meio de uma assinatura de acesso compartilhado. Para obter mais informações sobre como criar pontos de extremidade personalizados, consulte [Configurar cadeias de Conexão de armazenamento do Azure](storage-configure-connection-string.md).

1. Para salvar essas alterações de cadeia de conexão, escolha o botão de **Okey** e escolha o botão **Salvar** na barra de ferramentas. Depois de salvar essas alterações, você pode obter o valor dessa cadeia de caracteres de conexão no seu código usando [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx). Quando você publica seu aplicativo no Azure, escolha a configuração do serviço que contém a conta de armazenamento do Azure para a cadeia de conexão. Depois que seu aplicativo é publicado, verifique se o aplicativo funciona como esperado em relação aos serviços de armazenamento do Azure

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a publicação de aplicativos no Azure do Visual Studio, consulte [Publicando um serviço de nuvem usando as ferramentas do Azure](vs-azure-tools-publishing-a-cloud-service.md).
