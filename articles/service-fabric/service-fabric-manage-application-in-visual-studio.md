<properties
   pageTitle="Gerenciar seus aplicativos no Visual Studio | Microsoft Azure"
   description="Use o Visual Studio para criar, desenvolver, empacotar, implantar e depurar seus aplicativos de serviço tecidos e serviços."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="seanmck;mikhegn"/>

# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Use o Visual Studio para simplificar a escrita e gerenciamento de seus aplicativos de serviço tecidos

Você pode gerenciar seus aplicativos de estrutura de serviço do Azure e serviços por meio do Visual Studio. Depois que você [configurar seu ambiente de desenvolvimento](service-fabric-get-started.md), você pode usar o Visual Studio para criar aplicativos de serviço tecidos, adicionar registro de serviços ou pacote e implantar aplicativos no seu cluster de desenvolvimento local.

## <a name="deploy-your-service-fabric-application"></a>Implantar seu aplicativo de serviço tecidos

Por padrão, a implantar um aplicativo combina as etapas a seguir em uma operação simple:

1. Criar o pacote de aplicativo
2. Carregar o pacote de aplicativos para o armazenamento de imagens
3. Registrar o tipo de aplicativo
4. Remoção de qualquer aplicativo instâncias em execução
5. Criando uma nova instância do aplicativo

No Visual Studio, pressionar **F5** também implantar seu aplicativo e anexar o depurador a todas as instâncias do aplicativo. Você pode usar **Ctrl + F5** para implantar um aplicativo sem depuração, ou você pode publicar um cluster local ou remoto, usando o perfil de publicação. Para obter mais informações, consulte [publicar um aplicativo para um cluster remoto, usando o Visual Studio](service-fabric-publish-app-remote-cluster.md).

### <a name="application-debug-mode"></a>Modo de depuração de aplicativo

Por padrão, o Visual Studio remove instâncias existentes do seu tipo de aplicativo quando você interrompe a depuração ou (se você implantou o aplicativo sem anexar o depurador), quando você reimplantar o aplicativo. Nesse caso, todos os dados do aplicativo são removidos. Durante a depuração localmente, talvez você queira manter os dados que você já tiver criado ao testar uma nova versão do aplicativo, você deseja manter o aplicativo em execução ou desejar que as sessões de depuração subsequentes para atualizar o aplicativo. Ferramentas de estrutura de serviço do Visual Studio fornecem uma propriedade chamada de **Modo de depuração de aplicativo**, que controla se o **F5** deve desinstalar o aplicativo, manter o aplicativo em execução após uma sessão de depuração termina ou ativar o aplicativo para ser atualizado em sessões de depuração subsequentes, em vez de removidos e redistribuído.

#### <a name="to-set-the-application-debug-mode-property"></a>Para definir a propriedade modo de depuração de aplicativo

1. No menu de atalho do projeto aplicativo, escolha **Propriedades** (ou pressione a tecla **F4** ).
2. Na janela **Propriedades** , defina a propriedade **Modo de depuração de aplicativo** .

    ![Defina a propriedade de modo de depuração de aplicativo][debugmodeproperty]

Estas são as opções de **Modo de depuração de aplicativos** disponíveis.

1. **Atualização automática**: O aplicativo continua sendo executado quando encerra a sessão de depuração. A próxima **F5** tratará a implantação como uma atualização usando o modo automático sem monitoramento para atualizar rapidamente o aplicativo para uma versão mais recente com uma cadeia de caracteres de data acrescentada. O processo de atualização preserva todos os dados que você inseriu em uma sessão de depuração anterior.

2. **Manter o aplicativo**: O aplicativo continua sendo executado no cluster quando encerra a sessão de depuração. Sobre o próximo **F5** o aplicativo será removido e o aplicativo recém-criado será implantado no cluster.

3. **Remover o aplicativo** faz com que o aplicativo seja removido quando encerra a sessão de depuração.

Para **Atualizar automaticamente** os dados são preservados aplicando os recursos de atualização de aplicativos da estrutura de serviço, mas ele foi desenvolvido para otimizar para desempenho em vez de segurança. Para obter mais informações sobre como atualizar aplicativos e como você pode realizar uma atualização em um ambiente real, consulte [atualizar o aplicativo de serviço tecidos](service-fabric-application-upgrade.md).

![Exemplo de nova versão do aplicativo com data acrescentada][preservedata]

>[AZURE.NOTE] Essa propriedade não existe antes da versão 1.1 das ferramentas de estrutura de serviço para Visual Studio. Antes de 1.1, use a propriedade **Preservar dados em Iniciar** para obter o mesmo comportamento. A opção "Manter Application" foi introduzida em versão 1.2 das ferramentas de estrutura de serviço para Visual Studio.

## <a name="add-a-service-to-your-service-fabric-application"></a>Adicionar um serviço ao seu aplicativo de serviço tecidos

Você pode adicionar novos serviços ao seu aplicativo para estender sua funcionalidade.  Para garantir que o serviço está incluído no seu pacote de aplicativo, adicione o serviço através do item de menu **Novo serviço tecidos...** .

![Adicionar um novo serviço de tecidos ao seu aplicativo][newservice]

Selecione um tipo de projeto de serviço tecidos para adicionar ao seu aplicativo e especifique um nome para o serviço.  Consulte [Escolher uma estrutura de seu serviço](service-fabric-choose-framework.md) para ajudá-lo a decidir qual tipo de serviço para usar.

![Selecione um tipo de projeto de serviço de tecidos para adicionar ao seu aplicativo][addserviceproject]

O novo serviço será adicionado à sua solução e o pacote de aplicativo existente. As referências de serviço e uma instância de serviço padrão serão adicionados ao manifesto do aplicativo. O serviço será criado e iniciado na próxima vez que você implantar o aplicativo.

![O novo serviço será adicionado ao seu manifesto de aplicativo][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Empacotar o aplicativo de serviço tecidos

Para implantar o aplicativo e seus serviços em um cluster, você precisa criar um pacote de aplicativos.  O pacote organiza manifesto do aplicativo, manifest(s) de serviço e outros arquivos necessários em um layout específico.  Visual Studio configura e gerencia o pacote na pasta de aplicativo do projeto, no diretório 'pkg'.  Clicando no **pacote** no menu de contexto do **aplicativo** cria ou atualiza o pacote de aplicativos.  Talvez você queira fazer isso se você implantar o aplicativo usando scripts do PowerShell personalizados.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Remover aplicativos e tipos de aplicativo usando o Explorador de nuvem

Você pode executar operações de gerenciamento de cluster básico a partir do Visual Studio usando o Explorador de nuvem, que você pode iniciar no menu **Exibir** . Por exemplo, você pode excluir aplicativos e remover o provisionamento de tipos de aplicativos em clusters locais ou remotos.

![Remover um aplicativo](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

>[AZURE.TIP] Para maior funcionalidade de gerenciamento de cluster, consulte [visualizando seu cluster com o serviço tecidos Explorer](service-fabric-visualizing-your-cluster.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas

- [Modelo de aplicativo de serviço tecidos](service-fabric-application-model.md)
- [Implantação do aplicativo de serviço tecidos](service-fabric-deploy-remove-applications.md)
- [Gerenciando parâmetros de aplicativo para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md)
- [Depuração de seu aplicativo de serviço tecidos](service-fabric-debugging-your-application.md)
- [Visualizando seu cluster usando o serviço tecidos Explorer](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]:./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
