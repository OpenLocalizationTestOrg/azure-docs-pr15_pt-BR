<properties
    pageTitle="Fornecimento contínuo com gito e Visual Studio Team Services no Azure | Microsoft Azure"
    description="Saiba como configurar seus projetos de equipe do Visual Studio Team Services para usar gito automaticamente criem e implantem ao recurso Web App em serviços de nuvem ou serviço de aplicativo do Azure."
    services="cloud-services"
    documentationCenter=".net"
    authors="mlearned"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="mlearned"/>

# <a name="continuous-delivery-to-azure-using-visual-studio-team-services-and-git"></a>Fornecimento contínuo no Azure usando o Visual Studio Team Services e gito

Você pode usar os projetos de equipe do Visual Studio Team Services para hospedar um repositório gito para seu código-fonte e automaticamente construir e implantar em aplicativos web Azure ou serviços em nuvem sempre que você enviar uma confirmação para o repositório.

Você precisará Visual Studio 2013 e o SDK do Azure instalado. Se você ainda não tiver o Visual Studio 2013, baixe-o escolhendo o link **Introdução gratuitamente** em [www.visualstudio.com](http://www.visualstudio.com). Instale o SDK do Azure [aqui](http://go.microsoft.com/fwlink/?LinkId=239540).


> [AZURE.NOTE]Você precisa de uma conta de serviços de equipe do Visual Studio para concluir este tutorial: você pode [Abrir uma conta de serviços de equipe do Visual Studio gratuitamente](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Para configurar um serviço de nuvem automaticamente criem e implantem no Azure usando os serviços de equipe do Visual Studio, siga estas etapas.

## <a name="1-create-a-git-repository"></a>1: criar um repositório de gito

1. Se você ainda não tiver uma conta de serviços de equipe do Visual Studio, você pode obter um [aqui](http://go.microsoft.com/fwlink/?LinkId=397665). Quando você cria seu projeto de equipe, escolha gito como seu sistema de controle de origem. Siga as instruções para conectar-se o Visual Studio ao seu projeto de equipe.

2. No **Explorador de equipe**, escolha o link **clonar este repositório** .

    ![][3]

3. Especifique o local da cópia local e escolha o botão **Reproduzir** .

## <a name="2-create-a-project-and-commit-it-to-the-repository"></a>2: criar um projeto e confirmá-lo no repositório

1. No **Explorador de equipe**, na seção de **soluções** , escolha o link **novo** para criar um novo projeto no repositório local.

    ![][4]

2. Você pode implantar um aplicativo web ou um serviço de nuvem (aplicativo do Azure) seguindo as etapas neste passo a passo. Crie um novo projeto de serviço de nuvem do Azure ou um novo projeto do ASP.NET MVC. Certifique-se de que o projeto destina-se ao .NET Framework 4 ou posterior. Se você estiver criando um projeto de serviço de nuvem, adicione uma função de web do ASP.NET MVC e uma função de trabalho.
Se você quiser criar um aplicativo web, escolha o modelo de projeto de **Aplicativo Web ASP.NET** e escolha **MVC**. Consulte [criar um aplicativo web do ASP.NET no serviço de aplicativo do Azure](../app-service-web/web-sites-dotnet-get-started.md) para obter mais informações.

3. Abrir o menu de atalho para a solução e escolha **Confirmar**.

    ![][7]

4. Se esta for a primeira vez que você usou gito no Visual Studio Team Services, você precisará fornecer algumas informações para identifique-se no gito. Na área de **Alterações pendentes** do **Team Explorer**, insira seu nome de usuário e endereço de email. Digite um comentário para a confirmação e, em seguida, escolha o botão de **confirmação** .

    ![][8]

5. Observe as opções para incluir ou excluir alterações específicas quando você check-in. Se as alterações desejadas forem excluídas, escolha **Incluir tudo**.

6. Agora que você já confirmada as alterações em sua cópia local do repositório. Em seguida, sincronize as alterações com o servidor escolhendo o link de **sincronização** .

## <a name="3-connect-the-project-to-azure"></a>3: conectar o projeto a Azure

1. Agora que você tem um repositório de gito no Visual Studio Team Services com alguns código-fonte nela, você está pronto para se conectar seu repositório gito ao Azure.  No [portal de clássico Azure](http://go.microsoft.com/fwlink/?LinkID=213885), selecione seu aplicativo web ou serviço de nuvem ou crie um novo escolhendo o ícone + na parte inferior esquerda e escolhendo **Serviço em nuvem** ou **Web App** e, em seguida, **Criar rápida**.

    ![][9]

3. Para serviços de nuvem, escolha o link **Configurar publicação com os serviços de equipe do Visual Studio** . Para aplicativos web, escolha o link **Configurar a implantação do controle de origem** .

    ![][10]

2. No assistente, digite o nome da sua conta de serviços de equipe do Visual Studio na caixa de texto e escolha o link **Autorizar agora** . Você pode ser solicitado a entrar.

    ![][11]

3. Na **Solicitação de Conexão** pop-up de diálogo, clique em **Aceitar** para autorizar Azure para configurar seu projeto de equipe no Visual Studio Team Services.

    ![][12]

4. Depois de autorização estiver concluído, você verá uma lista suspensa que contém seus projetos de equipe do Visual Studio Team Services.  Selecione o nome do projeto de equipe que você criou nas etapas anteriores e escolha o botão de marca de seleção do assistente.

    ![][13]

    Na próxima vez que você enviar uma confirmação para seu repositório, serviços de equipe do Visual Studio será construa e implante seu projeto no Azure.

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4: disparar uma recriação e reimplantar seu projeto

1. No Visual Studio, abra um arquivo e alterá-lo. Por exemplo, alterar o arquivo `_Layout.cshtml` em modos de exibição\\pasta compartilhada em uma função de web MVC.

    ![][17]

2. Editar o texto de rodapé para o site e salve o arquivo.

    ![][18]

3. No **Solution Explorer**, abra o menu de atalho para o nó de solução, o nó do projeto ou o arquivo que você alterou e escolha **Confirmar**.

4. Digite um comentário e escolha **Confirmar**.

    ![][20]

5. Escolha o link de **sincronização** .

    ![][38]

6. Escolha o link de **envio** por push sua confirmação para o repositório no Visual Studio Team Services. (Você também pode usar o botão de **sincronização** para copiar sua confirmações para o repositório. A diferença é que **sincronização** também obtém as alterações mais recentes do repositório.)

    ![][39]

7. Escolha o botão **início** para retornar à home page do **Team Explorer** .

    ![][21]

8. Escolha **cria** para exibir as compilações em andamento.

    ![][22]

    **Team Explorer** mostra que uma compilação foi acionada para seu check-in.

    ![][23]

9. Para exibir um log detalhado no desenrolar de compilação, clique duas vezes no nome da compilação em andamento.

10. Enquanto a compilação está em andamento, dê uma olhada a definição de compilação que foi criada quando você usou o Assistente para vincular a Azure.  Abra o menu de atalho para a definição de compilação e escolha **Editar definição de compilação**.

    ![][25]

11. Na guia **disparador** , você verá que a definição de compilação é definida para construir em cada check-in, por padrão. (Para um serviço de nuvem, Visual Studio Team Services cria e implanta a ramificação mestre para o ambiente de teste automaticamente. Você ainda precisa fazer uma etapa manual para implantar o site ao vivo. Para um aplicativo web que não tem o ambiente de teste, ele implanta a ramificação mestre diretamente para o site ao vivo.

    ![][26]

1. Na guia **processo** , você pode ver que o ambiente de implantação está definido para o nome do seu aplicativo web ou serviço de nuvem.

    ![][27]

1. Especifica valores para as propriedades se quiser que os valores diferentes dos padrões. As propriedades para publicação Azure estão na seção **implantação** e talvez você também precise definir parâmetros de MSBuild. Por exemplo, em uma nuvem projeto service, para especificar uma configuração de serviço diferente de "Nuvem", definir os parâmetros de MSbuild para `/p:TargetProfile=[YourProfile]` onde *[YourProfile]* corresponde a um arquivo de configuração de serviço com um nome como ServiceConfiguration. *YourProfile*.cscfg.

    A tabela a seguir mostra as propriedades disponíveis na seção **implantação** :

  	|Propriedade|Valor padrão|
  	|---|---|
  	|Permitir certificados não confiáveis|Se for falso, certificados SSL devem ser assinados por uma autoridade raiz.|
  	|Permitir atualização|Permite a implantação atualizar uma implantação existente em vez de criar um novo. Preserva o endereço IP.|
  	|Não excluir|Se for verdadeiro, não substituir uma implantação não relacionada existente (atualização é permitida).|
  	|Caminho para configurações de implantação|O caminho para o seu arquivo de .pubxml para um aplicativo web, relativo a pasta raiz da repo. Ignorado para serviços de nuvem.|
  	|Ambiente de implantação do SharePoint|O mesmo que o nome do serviço.|
  	|Ambiente de implantação do Azure|O aplicativo ou nuvem nome do serviço web.|

1. Neste momento, a compilação deve ser concluída com êxito.

    ![][28]

1. Se você clicar duas vezes no nome da compilação, o Visual Studio mostra um **Resumo da compilação**, incluindo qualquer resultado de teste de projetos de teste de unidade associada.

    ![][29]

1. No [portal de clássico Azure](http://go.microsoft.com/fwlink/?LinkID=213885), você pode exibir a implantação associada na guia **implantações** quando o ambiente temporário está selecionado.

    ![][30]

1.  Navegue até a URL do site. Para um aplicativo web, escolha o botão **Procurar** no portal. Para um serviço de nuvem, escolha a URL na seção **Resumo rápido de geral** da página de **painel** que mostra o ambiente de teste.

    Implantações de integração contínua para serviços de nuvem são publicadas no ambiente de teste por padrão. Você pode alterar isso definindo a propriedade de **Ambiente de serviço de nuvem alternativo** para **produção**. Aqui é onde a URL do site na página de painel do serviço na nuvem.

    ![][31]

    Uma nova guia do navegador será aberta para revelar seu site em execução.

    ![][32]

1.  Se você fazer outras alterações ao seu projeto, você disparador mais cria e você irá acumular várias implantações. Na última vez está marcada como ativo.

    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5: reimplantar uma compilação anterior

Esta etapa é opcional. No portal do Azure clássico, escolha uma implantação anterior e escolha **reimplantar** para retroceder seu site para um check-in anterior. Observe que isso irá disparar uma nova compilação no TFS e criar uma nova entrada no seu histórico de implantação.

![][34]

## <a name="6-change-the-production-deployment"></a>6: alterar a implantação de produção

Quando você estiver pronto, você pode promover o ambiente de teste para o ambiente de produção, escolhendo **trocar** no portal de clássico do Azure. Ambiente de teste recentemente implantado é promovido para a produção e o ambiente de produção anterior, se houver, torna-se um ambiente de teste. A implantação ativa pode ser diferente para ambientes de teste e produção, mas o histórico de implantação de compilações recentes é a mesma, independentemente de ambiente.

![][35]

## <a name="7-deploy-from-a-working-branch"></a>7: implantar de uma ramificação de trabalho.

Quando você usa gito, você geralmente fazer alterações em uma ramificação de trabalho e integra a ramificação mestre quando o desenvolvimento atinge um estado terminado. Durante a fase de desenvolvimento de um projeto, você desejará construa e implante a ramificação de trabalho para Azure.

1. No **Explorador de equipe**, escolha o botão **Home** e, em seguida, escolha o botão de **ramificações** .

    ![][40]

2. Escolha o link **Nova ramificação** .

    ![][41]

3. Insira o nome da ramificação, como "trabalhando," e escolha **Criar ramificação**. Isso cria uma nova ramificação local.

    ![][42]

4. Publica a ramificação. Escolha o nome da ramificação em **ramificações não publicadas**e escolha **Publicar**.

    ![][44]

6. Por padrão, somente as alterações à ramificação mestre disparam uma compilação contínua. Para configurar a compilação contínua para uma ramificação de trabalho, escolha a página **cria** no **Team Explorer**e escolha **Editar definição de compilação**.

7. Abra a guia de **Configurações de fonte** . Em **monitorados ramifica para integração contínua e compilação**, escolha **clique aqui para adicionar uma nova linha**.

    ![][47]

8. Especifique a ramificação que você criou, como chefe/referências/trabalho.

    ![][48]

9. Fazer uma alteração no código, abrir o menu de atalho para o arquivo foi alterado e clique em seguida **Confirmar**.

    ![][43]

10. Escolha o link de **Confirmações não sincronizadas** e escolha o botão de **sincronização** ou o link de **envio por Push** para copiar as alterações na cópia da ramificação trabalhar no Visual Studio Team Services.

    ![][45]

11. Navegue até o modo de exibição **cria** e localize a compilação que você tem disparou apenas para a ramificação de trabalho.

## <a name="next-steps"></a>Próximas etapas

Para saber mais dicas sobre como usar gito com os serviços de equipe do Visual Studio, consulte [desenvolver e compartilhar seu código em gito usando o Visual Studio](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) e para obter informações sobre como usar um repositório de gito que não é gerenciado pelo Visual Studio Team Services para publicar Azure, consulte [Implantação contínuo ao serviço de aplicativo do Azure](../app-service-web/app-service-continuous-deployment.md). Para obter mais informações sobre os serviços de equipe do Visual Studio, consulte [Serviços de equipe do Visual Studio](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG
