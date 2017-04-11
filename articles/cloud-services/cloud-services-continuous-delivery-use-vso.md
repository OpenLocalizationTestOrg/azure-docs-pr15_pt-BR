<properties
    pageTitle="Fornecimento contínuo com o Visual Studio Team Services no Azure | Microsoft Azure"
    description="Saiba como configurar seus projetos de equipe do Visual Studio Team Services para criar e implantar o recurso de Web App nos serviços de nuvem ou serviço de aplicativo do Azure automaticamente."
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

# <a name="continuous-delivery-to-azure-using-visual-studio-team-services"></a>Fornecimento contínuo no Azure usando os serviços de equipe do Visual Studio

Você pode configurar seus projetos de equipe de serviços de equipe do Visual Studio para automaticamente construir e implantar em aplicativos web Azure ou serviços em nuvem.  (Para obter informações sobre como configurar uma compilação contínua e implantar o sistema usando um *local* Team Foundation Server, consulte [Fornecimento contínuo para serviços de nuvem no Azure](cloud-services-dotnet-continuous-delivery.md).)

Este tutorial supõe que você tenha Visual Studio 2013 e o SDK do Azure instalado. Se você ainda não tiver o Visual Studio 2013, baixe-o escolhendo o link **Introdução gratuitamente** em [www.visualstudio.com](http://www.visualstudio.com). Instale o SDK do Azure [aqui](http://go.microsoft.com/fwlink/?LinkId=239540).

> [AZURE.NOTE]Você precisa de uma conta de serviços de equipe do Visual Studio para concluir este tutorial: você pode [Abrir uma conta de serviços de equipe do Visual Studio gratuitamente](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Para configurar um serviço de nuvem automaticamente criem e implantem no Azure usando os serviços de equipe do Visual Studio, siga estas etapas.

## <a name="1-create-a-team-project"></a>1: criar um projeto de equipe

Siga as instruções [aqui](http://go.microsoft.com/fwlink/?LinkId=512980) para criar o seu projeto de equipe e vinculá-lo ao Visual Studio. Este passo a passo pressupõe que você está usando o controle de versão de Foundation da equipe (TFVC) como sua solução de controle de origem. Se você quiser usar gito para controle de versão, consulte [a versão gito deste passo a passo](http://go.microsoft.com/fwlink/p/?LinkId=397358).

## <a name="2-check-in-a-project-to-source-control"></a>2: check-in de um projeto ao controle de origem

1. No Visual Studio, abra a solução que deseja implantar ou crie um novo.
Você pode implantar um aplicativo web ou um serviço de nuvem (aplicativo do Azure) seguindo as etapas neste passo a passo.
Se você quiser criar uma nova solução, crie um novo projeto de serviço de nuvem do Azure, ou um novo projeto do ASP.NET MVC. Certifique-se de que o projeto direcionado .NET Framework 4 ou 4,5 e se você estiver criando um projeto de serviço de nuvem, adicionar uma função de web do ASP.NET MVC e uma função de trabalho e escolha o aplicativo da Internet para a função de web. Quando solicitado, escolha o **Aplicativo da Internet**.
Se você quiser criar um aplicativo web, escolha o modelo de projeto de aplicativo Web ASP.NET e escolha MVC. Consulte [criar um aplicativo web do ASP.NET no serviço de aplicativo do Azure](../app-service-web/web-sites-dotnet-get-started.md).

    > [AZURE.NOTE] Visual Studio Team Services só oferece suporte para implantações de CI do Visual Studio Web Applications neste momento. Projetos de Site estão fora do escopo.

1. Abra o menu de contexto para a solução e escolha **Adicionar solução ao controle de origem**.

    ![][5]

1. Aceitar ou alterar os padrões e escolha o botão de **Okey** . Após a conclusão do processo, os ícones de controle de origem aparecem no **Solution Explorer**.

    ![][6]

1. Abrir o menu de atalho para a solução e escolha **Fazer Check-In**.

    ![][7]

1. Na área de **Alterações pendentes** do **Team Explorer**, digite um comentário para o check-in e escolha o botão **Check-In** .

    ![][8]

    Observe as opções para incluir ou excluir alterações específicas quando você check-in. Se alterações desejadas forem excluídas, escolha o link **Incluir tudo** .

    ![][9]

## <a name="3-connect-the-project-to-azure"></a>3: conectar o projeto a Azure

1. Agora que você tem um projeto de equipe VS Team Services com alguns código-fonte nela, você está pronto para se conectar seu projeto de equipe no Azure.  No [portal de clássico Azure](http://go.microsoft.com/fwlink/?LinkID=213885), selecione seu aplicativo web ou serviço de nuvem ou crie um novo escolhendo o **+** ícone na parte inferior esquerda e escolhendo **Serviço em nuvem** ou **Web App** e, em seguida, **Criar rápida**. Escolha o link **Configurar publicação com os serviços de equipe do Visual Studio** .

    ![][10]

1. No assistente, digite o nome da sua conta de serviços de equipe do Visual Studio na caixa de texto e clique no link **Autorizar agora** . Você pode ser solicitado a entrar.

    ![][11]

1. Na **Solicitação de Conexão** pop-up de diálogo, escolha botão **Aceitar** para autorizar Azure para configurar seu projeto de equipe no VS Team Services.

    ![][12]

1. Quando a autorização estiver concluído, você verá uma lista suspensa contendo uma lista de seus projetos de equipe do Visual Studio Team Services. Escolha o nome do projeto de equipe que você criou nas etapas anteriores e, em seguida, escolha o botão de marca de seleção do assistente.

    ![][13]

1. Depois de seu projeto está vinculado, você verá algumas instruções para verificar as alterações ao seu projeto de equipe do Visual Studio Team Services.  Em sua próxima check-in, os serviços de equipe do Visual Studio será construir e implantar o seu projeto no Azure.  Tente fazer isso agora, clicando no link de **Check-In do Visual Studio** e, em seguida, o link de **Iniciar o Visual Studio** (ou equivalente **Visual Studio** botão na parte inferior da tela do portal).

    ![][14]

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4: disparar uma recriação e reimplantar seu projeto

1. No do Visual Studio **Team Explorer**, escolha o link do **Gerenciador de controle de código-fonte** .

    ![][15]

1. Navegue até o arquivo de solução e abri-lo.

    ![][16]

1. No **Solution Explorer**, abra um arquivo e alterá-lo. Por exemplo, alterar o arquivo `_Layout.cshtml` em modos de exibição\\pasta compartilhada em uma função de web MVC.

    ![][17]

1. Editar o logotipo do site e escolha **Ctrl + S** para salvar o arquivo.

    ![][18]

1. No **Explorador de equipe**, escolha o link de **Alterações pendentes** .

    ![][19]

1. Insira um comentário e, em seguida, escolha o botão **Check-In** .

    ![][20]

1. Escolha o botão **início** para retornar à home page do **Team Explorer** .

    ![][21]

1. Escolha o link **cria** para exibir as compilações em andamento.

    ![][22]

    **Team Explorer** mostra que uma compilação foi acionada para seu check-in.

    ![][23]

1. Clique duas vezes no nome da compilação em andamento para exibir um log detalhado no desenrolar de compilação.

    ![][24]

1. Enquanto a compilação está em andamento, dê uma olhada a definição de compilação que foi criada quando você vinculou TFS no Azure usando o assistente.  Abra o menu de atalho para a definição de compilação e escolha **Editar definição de compilação**.

    ![][25]

    Na guia **disparador** , você verá que a definição de compilação é definida para construir em cada check-in por padrão.

    ![][26]

    Na guia **processo** , você pode ver que o ambiente de implantação está definido para o nome do seu aplicativo web ou serviço de nuvem. Se você estiver trabalhando com web apps, as propriedades que você vê serão diferentes daqueles mostrado aqui.

    ![][27]

1. Especifica valores para as propriedades se quiser que os valores diferentes dos padrões. As propriedades para publicação Azure estão na seção **implantação** .

    A tabela a seguir mostra as propriedades disponíveis na seção **implantação** :

  	|Propriedade|Valor padrão|
  	|---|---|
  	|Permitir certificados não confiáveis|Se for falso, certificados SSL devem ser assinados por uma autoridade raiz.|
  	|Permitir atualização|Permite a implantação atualizar uma implantação existente em vez de criar um novo. Preserva o endereço IP.|
  	|Não excluir|Se for verdadeiro, não substituir uma implantação não relacionada existente (atualização é permitida).|
  	|Caminho para configurações de implantação|O caminho para o seu arquivo de .pubxml para um aplicativo web, relativo a pasta raiz da repo. Ignorado para serviços de nuvem.|
  	|Ambiente de implantação do SharePoint|O mesmo que o nome do serviço.|
  	|Ambiente de implantação do Azure|O aplicativo ou nuvem nome do serviço web.|

1. Se você estiver usando várias configurações de serviço (.cscfg arquivos), você pode especificar a configuração desejada na configuração **Construir, Avançado, MSBuild argumentos** . Por exemplo, para usar ServiceConfiguration.Test.cscfg, defina MSBuild argumentos opção linha `/p:TargetProfile=Test`.

    ![][38]

    Neste momento, a compilação deve ser concluída com êxito.

    ![][28]

1. Se você clicar duas vezes no nome da compilação, o Visual Studio mostra um **Resumo da compilação**, incluindo qualquer resultado de teste de projetos de teste de unidade associada.

    ![][29]

1. No [portal de clássico Azure](http://go.microsoft.com/fwlink/?LinkID=213885), você pode exibir a implantação associada na guia **implantações** quando o ambiente temporário está selecionado.

    ![][30]

1.  Navegue até a URL do site. Para um aplicativo web, basta clicar no botão **Procurar** na barra de comando. Para um serviço de nuvem, escolha a URL na seção **Resumo rápido de geral** da página de **painel** que mostra o ambiente de teste para um serviço de nuvem. Implantações de integração contínua para serviços de nuvem são publicadas no ambiente de teste por padrão. Você pode alterar isso definindo a propriedade de **Ambiente de serviço de nuvem alternativo** para **produção**. Captura de tela mostra onde a URL do site está na página de painel do serviço na nuvem.

    ![][31]

    Uma nova guia do navegador será aberta para revelar seu site em execução.

    ![][32]

    Para serviços de nuvem, se você fazer outras alterações ao seu projeto, você disparador mais cria e você irá acumular várias implantações. Na última vez marcado como ativo.

    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5: reimplantar uma compilação anterior

Esta etapa se aplica aos serviços de nuvem e é opcional. No portal do Azure clássico, escolha uma implantação anterior e escolha o botão **reimplantar** para retroceder seu site para um check-in anterior.  Observe que isso irá disparar uma nova compilação no TFS e criar uma nova entrada no seu histórico de implantação.

![][34]

## <a name="6-change-the-production-deployment"></a>6: alterar a implantação de produção

Esta etapa se aplica somente aos serviços de nuvem, não os aplicativos web. Quando você estiver pronto, você pode promover o ambiente de teste para o ambiente de produção, escolhendo o botão **trocar** no portal de clássico do Azure. Ambiente de teste recentemente implantado é promovido para a produção e o ambiente de produção anterior, se houver, torna-se um ambiente de teste. A implantação ativa pode ser diferente para ambientes de teste e produção, mas o histórico de implantação de compilações recentes é a mesma, independentemente de ambiente.

![][35]

## <a name="7-run-unit-tests"></a>7: executar testes de unidade

Esta etapa se aplica somente a web apps, não serviços em nuvem. Para colocar uma entrada de qualidade na sua implantação, você pode executar testes de unidade e se eles falharem, você pode interromper a implantação.

1.  No Visual Studio, adicione um projeto de teste de unidade.

    ![][39]

1.  Adicione referências de projeto para o projeto que você deseja testar.

    ![][40]

1.  Adicione alguns testes de unidade. Para começar, tente um teste fictício que sempre irá passar.

        ```
        using System;
        using Microsoft.VisualStudio.TestTools.UnitTesting;

        namespace UnitTestProject1
        {
            [TestClass]
            public class UnitTest1
            {
                [TestMethod]
                [ExpectedException(typeof(NotImplementedException))]
                public void TestMethod1()
                {
                    throw new NotImplementedException();
                }
            }
        }
        ```

1.  Editar a definição de compilação, escolha a guia de **processo** e expanda o nó de **teste** .

1.  Defina a **compilação de falhas em caso de falha de teste** para True. Isso significa que a implantação não ocorre, a menos que os testes passarem.

    ![][41]

1.  Fila uma nova compilação.

    ![][42]

    ![][43]

1. Enquanto a compilação é continuar, verificar seu andamento.

    ![][44]

    ![][45]

1. Quando a compilação estiver concluída, verifique os resultados de teste.

    ![][46]

    ![][47]

1.  Tente criar um teste que falhará. Adicionar um novo teste copiando primeiro, renomeá-la e comente a linha de código afirmando que NotImplementedException é uma exceção esperada.

        ```
        [TestMethod]
        //[ExpectedException(typeof(NotImplementedException))]
        public void TestMethod2()
        {
            throw new NotImplementedException();
        }
        ```

1. Fazer check-in a alteração fila uma nova compilação.

    ![][48]

1. Exiba os resultados de teste para ver detalhes sobre a falha.

    ![][49]

    ![][50]

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o teste de unidade no Visual Studio Team Services, consulte [executar testes de unidade em sua compilação](http://go.microsoft.com/fwlink/p/?LinkId=510474). Se você estiver usando gito, consulte [compartilhar seu código em gito](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) e [implantação contínua ao serviço de aplicativo do Azure](../app-service-web/app-service-continuous-deployment.md).  Para obter mais informações sobre os serviços de equipe do Visual Studio, consulte [Serviços de equipe do Visual Studio](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png

[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
