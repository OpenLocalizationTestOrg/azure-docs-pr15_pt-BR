<properties
    pageTitle="Configurar ambientes web Apps em um serviço de aplicativo do Azure temporários"
    description="Saiba como usar a publicação em estágios para web apps em um serviço de aplicativo do Azure."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    writer="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/09/2016"
    ms.author="cephalin"/>

# <a name="set-up-staging-environments-for-web-apps-in-azure-app-service"></a>Configurar ambientes web Apps em um serviço de aplicativo do Azure temporários
<a name="Overview"></a>

Quando você implanta o aplicativo web do serviço de [Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714), você pode implantar um slot de implantação separada em vez do slot de produção padrão ao executar no modo de plano de serviço de aplicativo **Premium** ou **padrão** . Implantação slots são aplicativos web realmente ao vivo com seus próprios nomes de host. Elementos de conteúdo e configurações do aplicativo da Web podem ser trocados entre dois slots de implantação, incluindo o slot de produção. Implantando seu aplicativo para um slot de implantação tem os seguintes benefícios:

- Você pode validar alterações de aplicativo da web em um slot de implantação preparação antes trocando-o com o slot de produção.

- Implantando um aplicativo web um slot primeiro e substituindo-os em produção garantem que todas as instâncias do slot são aquecidas antes de ser trocado em produção. Isso elimina o tempo de inatividade quando você implanta seu aplicativo web. O redirecionamento de tráfego é perfeito e sem solicitações são desconectadas como resultado de operações de troca. Este fluxo de trabalho inteiro pode ser automatizado, configurando a [Troca automática](#configure-auto-swap-for-your-web-app) quando a validação de pré-troca de não é necessária.

- Após uma troca, o slot com anteriormente em estágios web app agora tem o aplicativo de web de produção anterior. Se as alterações trocadas no slot produção não conforme o esperado, você pode executar a troca mesma imediatamente para obter seu "último site conhecido" novamente.

Cada modo de plano de serviço de aplicativo é compatível com um número diferente de slots de implantação. Para descobrir o número de slots suportes de modo da seu aplicativo web, consulte [Preços de serviço de aplicativo](/pricing/details/app-service/).

- Quando seu aplicativo web tem vários slots, você não pode alterar o modo.

- Dimensionamento não está disponível para slots não seja de produção.

- Gerenciamento de recursos vinculada não é suportado para slots não seja de produção. No [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715) somente, você pode evitar este impacto potencial em um slot de produção movendo temporariamente o slot não seja de produção para um modo diferente de plano de serviço de aplicativo. Observe que o slot não produção deve novamente compartilhar o mesmo modo com o slot de produção antes de você pode trocar os dois slots.


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="Add"></a>
## <a name="add-a-deployment-slot-to-a-web-app"></a>Adicionar um slot de implantação para um aplicativo web ##

O aplicativo web deve estar executando no modo **padrão** ou **Premium** em ordem para habilitar vários slots de implantação.

1. No [Portal do Azure](https://portal.azure.com/), abra blade do web app.
2. Clique em **configurações**e, em seguida, clique em **slots de implantação**. Na lâmina **slots de implantação** , clique em **Adicionar Slot**.

    ![Adicionar um novo slot de implantação][QGAddNewDeploymentSlot]

    > [AZURE.NOTE]
    > Se o web app não ainda estiver no modo **padrão** ou **Premium** , você receberá uma mensagem indicando os modos suportados para habilitar a publicação em estágios. Neste ponto, você tem a opção para selecionar a **atualização** e navegue até a guia de **escala** de seu aplicativo web antes de continuar.

2. Na lâmina **Adicionar um slot** , dê um nome para o slot e selecione se deseja clonar a configuração do aplicativo web do outro slot de implantação existente. Clique na marca de seleção para continuar.

    ![Fonte de configuração][ConfigurationSource1]

    A primeira vez que você adiciona um slot, só terá duas opções: configuração de clonar do slot padrão em produção ou não.

    Após ter criado vários slots, você poderá clonar a configuração de um slot diferente em produção:

    ![Fontes de configuração][MultipleConfigurationSources]

5. Na lâmina **slots de implantação** , clique em slot implantação para abrir um blade do slot, com um conjunto de métricas e configuração assim como qualquer outro aplicativo web. **Your-Web-App-Name-Deployment-slot-Name** aparecerá na parte superior da lâmina para lembrá-lo que você está exibindo o slot de implantação.

    ![Título de Slot de implantação][StagingTitle]

5. Clique na URL do aplicativo em lâmina do slot. Observe o slot de implantação tem seu próprio nome de host e também é um aplicativo ao vivo. Para limitar o acesso público ao slot de implantação, consulte o [Aplicativo de serviço Web App – bloco web access para slots de implantação de não produção](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Não há nenhum conteúdo após a criação de slot de implantação. Você pode implantar ao slot de uma ramificação de repositório diferente ou um repositório totalmente diferente. Você também pode alterar a configuração do slot. Use as credenciais de perfil ou implantação de publicar associadas ao slot de implantação para atualizações de conteúdo.  Por exemplo, você pode [Publicar este slot com gito](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>
## <a name="configuration-for-deployment-slots"></a>Configuração para slots de implantação ##
Quando você clonar configuração de outro slot de implantação, a configuração clonada é editável. Além disso, alguns elementos de configuração seguirá o conteúdo em uma troca (não slot específico) enquanto outros elementos de configuração permanecerá no mesmo slot após uma troca (slot específico). As listas a seguir mostram a configuração que serão alterados quando você troca slots.

**Configurações que são trocadas**:

- Configurações gerais - como Web sockets do framework versão, 32/64 bits,
- Configurações de aplicativo (pode ser configurado para manter a um slot opção)
- Cadeias de caracteres de Conexão (pode ser configurado para manter a um slot opção)
- Mapeamentos de manipulador
- Configurações de diagnósticos e monitoramento
- WebJobs conteúdo

**Configurações que não são trocadas**:

- Pontos de extremidade de publicação
- Nomes de domínio personalizado
- Ligações e certificados SSL
- Configurações de escala
- WebJobs agendadores

Para configurar uma cadeia de conexão ou configuração do aplicativo ser fiel a um slot (não trocado), acesse a lâmina de **Configurações do aplicativo** para um slot específico, depois marque a caixa de **Configuração de Slot** para os elementos de configuração que deve preferir slot. Observe que um elemento de configuração marcar como slot específico tem o efeito de estabelecer esse elemento como não troca entre todos os slots de implantação associados com o aplicativo web.

![Configurações de slot][SlotSettings]

<a name="Swap"></a>
## <a name="to-swap-deployment-slots"></a>Para trocar slots de implantação ##

>[AZURE.IMPORTANT] Antes de você troca um aplicativo web de um slot de implantação em produção, certifique-se de que todas as configurações específicas de slot não estejam configuradas exatamente como você deseja que ele no destino trocar.

1. Para trocar slots de implantação, clique no botão de **troca** na barra de comandos do aplicativo web ou na barra de comandos de um slot de implantação. Certifique-se de que trocar origem e destino de troca estão definidas corretamente. Geralmente, o destino de troca seria o slot de produção.  

    ![Botão de troca][SwapButtonBar]

3. Clique em **Okey** para concluir a operação. Quando a operação for concluído, os slots de implantação foram trocados.

## <a name="configure-auto-swap-for-your-web-app"></a>Configurar a troca automática para o seu aplicativo web ##

Troca automática simplifica cenários DevOps onde você deseja implantar continuamente o seu aplicativo web com zero início frio e inatividade para clientes finais do aplicativo web. Quando um slot de implantação é configurado para troca automática em produção, sempre que você atualizar seu código por push para esse slot, serviço de aplicativo será automaticamente Troque o web app em produção depois que ele já tem aquecido no slot.

>[AZURE.IMPORTANT] Quando você habilita a troca automática para um slot, verifique se que a configuração do slot é exatamente a configuração destinada para o slot de destino (geralmente o slot de produção).

Configurando a troca automática para um slot é fácil. Siga as etapas abaixo:

1. Na lâmina **Slots de implantação** , selecione um slot não seja de produção, clique em **Todas as configurações** para lâmina nesse slot.  

    ![][Autoswap1]

2. Clique em **configurações do aplicativo**. Selecionar **ativado** para **Troca automática**, selecione o slot de destino desejado no **Slot de troca automática**e clique em **Salvar** na barra de comando. Verifique se a configuração do slot é exatamente a configuração destinada para o slot de destino.

    Guia **notificações** serão flash verde **êxito** quando a operação for concluída.

    ![][Autoswap2]

    >[AZURE.NOTE] Para testar a troca automática para o aplicativo web, primeiro você pode selecionar um slot de destino não seja de produção no **Slot de troca automática** para se familiarizar com o recurso.  

3. Execute um envio de código para esse slot de implantação. Troca automática acontecerá após alguns instantes e a atualização será refletida na URL de seu slot destino.

<a name="Multi-Phase"></a>
## <a name="use-multi-phase-swap-for-your-web-app"></a>Use troca de várias fase para o aplicativo web ##

Troca de várias fase está disponível para simplificar a validação no contexto de elementos de configuração projetado ser fiel a um slot como cadeias de caracteres de conexão. Nesses casos, pode ser útil aplicar esses elementos de configuração do destino troca à fonte de troca e validar antes de trocar realmente entra em vigor. Depois de trocar elementos de configuração de destino são aplicados à fonte de trocar as ações disponíveis são Concluindo a troca ou reversão à configuração original para a fonte de troca que também tem o efeito de cancelamento a troca. Exemplos de cmdlets do PowerShell do Azure disponível para troca de várias fase são incluídos nos cmdlets do PowerShell do Azure para seção de slots de implantação.

<a name="Rollback"></a>
## <a name="to-rollback-a-production-app-after-swap"></a>Para reverter um aplicativo de produção após a troca ##

Se os erros forem identificados em produção após uma troca de slot, reverta os slots para os estados de pré-troca de trocando os mesmos dois slots imediatamente.

<a name="Warm-up"></a>
## <a name="custom-warm-up-before-swap"></a>Aquecimento personalizado antes da troca ##

Alguns aplicativos podem exigir ações de aquecimento personalizado. O elemento de configuração de applicationInitialization em Web. config permite que você especifique ações de inicialização personalizados a serem executadas antes de uma solicitação for recebida. A operação de troca esperará este aquecimento personalizado ser concluída. Aqui está um fragmento de Web. config de exemplo.

    <applicationInitialization>
        <add initializationPage="/" hostName="[web app hostname]" />
        <add initializationPage="/Home/About" hostname="[web app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>
## <a name="to-delete-a-deployment-slot"></a>Para excluir um slot de implantação##

Na lâmina para um slot de implantação, clique em **Excluir** na barra de comando.  

![Excluir um Slot de implantação][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
##Azure cmdlets do PowerShell para slots de implantação

PowerShell Azure é um módulo que fornece cmdlets para gerenciar Azure através do Windows PowerShell, incluindo o suporte para o gerenciamento de slots de implantação do web app em um serviço de aplicativo do Azure.

- Para obter informações sobre instalação e configuração do PowerShell do Azure e autenticar Azure PowerShell com sua assinatura do Azure, veja [como instalar e configurar o Microsoft Azure PowerShell](../powershell-install-configure.md).  

----------

### <a name="create-web-app"></a>Crie aplicativo web

```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [web app name] -Location [location] -AppServicePlan [app service plan name]
```

----------

### <a name="create-a-deployment-slot-for-a-web-app"></a>Criar um slot de implantação para um aplicativo web

```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [web app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

----------

### <a name="initiate-multi-phase-swap-and-apply-target-slot-configuration-to-source-slot"></a>Iniciar troca de várias fase e aplicar a configuração de slot de destino para slot de origem

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### <a name="revert-the-first-phase-of-multi-phase-swap-and-restore-source-slot-configuration"></a>Reverter a primeira fase de troca de várias fase e restaurar a configuração do slot de origem

```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

----------

### <a name="swap-deployment-slots"></a>Troque slots de implantação

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### <a name="delete-deployment-slot"></a>Excluir slot de implantação

```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [web app name]/[slot name] -ApiVersion 2015-07-01
```

----------

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>
##Comandos de Interface de linha (Azure comando) Azure para Slots de implantação

A CLI Azure fornece entre plataformas comandos para trabalhar com o Azure, incluindo o suporte para o gerenciamento de slots de implantação do Web App.

- Para obter instruções sobre como instalar e configurar a CLI do Azure, incluindo informações sobre como conectar o Azure CLI à sua assinatura do Azure, consulte [instalar e configurar o CLI do Azure](../xplat-cli-install.md).

-  Para listar os comandos disponíveis para o serviço de aplicativo do Azure no CLI Azure, chamar `azure site -h`.

----------
### <a name="azure-site-list"></a>lista de sites do Azure
Para obter informações sobre aplicativos da web na assinatura atual, ligue **lista site azure**, como no exemplo a seguir.

`azure site list webappslotstest`

----------
### <a name="azure-site-create"></a>Criar site do Azure
Para criar um slot de implantação, ligue para **criar sites azure** e especifique o nome de um aplicativo web existente e o nome do slot para criar, como no exemplo a seguir.

`azure site create webappslotstest --slot staging`

Para habilitar o controle de origem para o novo slot, use a opção **– gito** , como no exemplo a seguir.

`azure site create --git webappslotstest --slot staging`

----------
### <a name="azure-site-swap"></a>troca de site Azure
Para tornar a implantação atualizada slot o aplicativo de produção, use o comando **trocar de site do azure** para executar uma operação de troca, como no exemplo a seguir. O aplicativo de produção não terão nenhum tempo de inatividade, nem passará um início frio.

`azure site swap webappslotstest`

----------
### <a name="azure-site-delete"></a>Excluir site Azure
Para excluir um slot de implantação que não for mais necessário, use o comando **Excluir site azure** , como no exemplo a seguir.

`azure site delete webappslotstest --slot staging`

----------

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

## <a name="next-steps"></a>Próximas etapas ##
[Azure serviço de aplicativo Web App – bloco web access para slots de implantação de não-produção](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Avaliação gratuita do Microsoft Azure](/pricing/free-trial/)

## <a name="whats-changed"></a>O que mudou
* Para um guia para a alteração de sites para o serviço de aplicativo consulte: [o serviço de aplicativo do Azure e seu impacto sobre serviços existentes do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png
 
