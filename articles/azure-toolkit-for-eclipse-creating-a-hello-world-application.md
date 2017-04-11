<properties
    pageTitle="Criar um serviço de nuvem do mundo de saudação para Azure no Eclipse"
    description="Saiba como criar um aplicativo de Hello World simples usando o Kit de ferramentas do Azure para Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690944.aspx -->

# <a name="create-a-hello-world-cloud-service-for-azure-in-eclipse"></a>Criar um serviço de nuvem do mundo de saudação para Azure no Eclipse #

As etapas a seguir mostram como criar e implantar um aplicativo JSP básico no Azure usando o Kit de ferramentas do Azure para Eclipse. Um exemplo JSP é mostrado para simplicidade, mas etapas altamente semelhantes seria apropriadas para um servlet Java, que diz respeito implantação Azure.

O aplicativo terá aparência semelhante à seguinte:

![][ic600360]

## <a name="prerequisites"></a>Pré-requisitos ##

* Um Java Developer Kit (JDK), v 1.7 ou posterior.
* Eclipse IDE para Java EE desenvolvedores, Indigo ou posterior. Isso pode ser baixado do <http://www.eclipse.org/downloads/>.
* Uma distribuição de um servidor de web baseados em Java ou o servidor de aplicativos, como o Apache Tomcat, GlassFish, servidor da aplicação JBoss, Jetty ou IBM® WebSphere® aplicativo servidor liberdade Core.
* Uma assinatura do Azure, que pode ser adquirida no <http://azure.microsoft.com/pricing/purchase-options/>.
* O Kit de ferramentas Azure para Eclipse. Para obter mais informações, consulte [instalar o Kit de ferramentas do Azure para Eclipse][].

## <a name="to-create-a-hello-world-application"></a>Para criar um aplicativo Hello World ##

Primeiro, começaremos com a criação de um projeto Java.

*  Iniciar Eclipse e no menu, clique em **arquivo**, clique em **novo**e clique em **Projeto de Web dinâmico**. (Se você não vir o **Projeto de Web dinâmico** listado como um projeto disponível após clicar em **arquivo**, **novo**, faça o seguinte: clique em **arquivo**, clique em **novo**, clique em **projeto …**, expanda **Web**, clique em **Dinâmico Web Project**e clique em **Avançar**.)
*  Para fins deste tutorial, nomeie o projeto **MyHelloWorld**. (Certifique-se você usar esse nome, etapas subsequentes neste tutorial esperam seu arquivo de guerra seja nomeado MyHelloWorld). Sua tela aparecerá similar ao seguinte: ![][ic589576]
* Clique em **Concluir**.
* No modo de exibição do Eclipse Explorador de projeto, expanda **MyHelloWorld**. **Conteúdo da Web**de atalho, clique em **novo**e, em seguida, clique em **Arquivo JSP**.
* Na caixa de diálogo **Novo arquivo JSP** , nomeie o arquivo **JSP**. Manter a pasta pai como **MyHelloWorld/conteúdo da Web**, conforme mostrado a seguir:  ![][ic659262]
* Na caixa de diálogo **Selecionar modelo de JSP** para fins deste tutorial, selecione **Novo arquivo JSP (html)** e clique em **Concluir**.
* Quando o arquivo JSP abre no Eclipse, adicionar texto para exibir dinamicamente **Olá, mundo!** dentro das existentes `<body>` elemento. Seu atualizado `<body>` conteúdo deve ser exibido como o seguinte:
```
    <body>
    <b><% out.println("Hello World!"); %></b>
    </body>
```
* Salve JSP.

## <a name="to-deploy-your-application-to-azure-the-quick-and-simple-way"></a>Para implantar seu aplicativo no Azure, a maneira mais simple e mais rápida ##

Assim que você tiver um aplicativo da web Java pronto para testar, você pode usar o atalho a seguir para testá-la diretamente na nuvem Azure.

1. No Explorador de projeto do Eclipse, clique em **MyHelloWorld**.
1. Na barra de ferramentas Eclipse, clique no botão suspenso de **publicação** e clique em **Publicar como serviço de nuvem do Azure**
    ![][publishDropdownButton]
1. Se você estiver publicando este aplicativo no Azure pela primeira vez e você não tiver criado um projeto de implantação do Azure para este aplicativo antes, um projeto de implantação do Azure ser criado automaticamente para você. Você verá o seguinte prompt, que também lista o pacote JDK e o servidor de aplicativo que será implantada automaticamente para executar o aplicativo.
    ![][ic789598]

    Essa abordagem de atalho permite uma maneira rápida e fácil de testar seu aplicativo no Azure sem precisar configurar um servidor específico ou JDK diferente dos padrões. Se você estiver satisfeito com os padrões, você pode clicar em **Okey** para continuar com as etapas a seguir.
    No entanto, se você quiser alterar o JDK ou servidor de aplicativo para uso em seu aplicativo, você poderá fazer isso mais tarde editando o projeto de implantação Azure que foi criado automaticamente para você, ou clique em **Cancelar** e leia a **seção de projetos de implantação do Azure sobre** deste tutorial.
1. Na caixa de diálogo **Publicar Azure** :
    1. Se não houver nenhuma inscrição para selecionar na lista de **assinaturas** ainda, siga estas etapas para importar informações sobre sua assinatura:
        1. Clique em **Importar de arquivo de configurações de publicação**.
        1. Na caixa de diálogo **Importar informações de assinatura** , clique em **Baixar o arquivo de configurações de publicação**. Se você ainda não conectado para sua conta do Azure, você será solicitado a fazer logon no. Em seguida, você será solicitado a salvar um Azure Publicar arquivo de configurações. Salvá-lo no computador local.
        1. Ainda na caixa de diálogo **Importar informações de assinatura** , clique no botão **Procurar** , selecione o arquivo de configurações de publicar salvas localmente na etapa anterior e clique em **Abrir**. Sua tela deve ser semelhante ao seguinte: ![][ic644267]
        1. Clique em **Okey**.
    1. Para **assinatura**, selecione a assinatura que você deseja usar para a sua implantação.
    1. **Conta de armazenamento**, selecione a conta de armazenamento que você deseja usar ou clique em **novo** para criar uma nova conta de armazenamento.
    1. **Nome do serviço**, selecione o serviço de nuvem que você deseja usar ou clique em **novo** para criar um novo serviço de nuvem.
    1. Para o **Sistema operacional de destino**, selecione a versão do sistema operacional que você deseja usar para sua implantação.
    1. Para o **ambiente de destino**, para fins deste tutorial, selecione **preparação**. (Quando você estiver pronto para implantar seu site de produção, você vai alterar isso para **produção**.)
    1. Opcional: Certifique-se que **Substituir implantação anterior** está marcada se quiser que a nova implantação para substituir automaticamente a implantação anterior. Quando você ativar essa opção, você irá não problemas de experiência "409 Conflito" ao publicar no mesmo local.
        Observe que a caixa de diálogo **Publicar Azure** contém uma seção para **Acesso remoto**. Por padrão, acesso remoto não está habilitado e podemos não ativará para este exemplo. Para habilitar o acesso remoto, você deverá inserir um nome de usuário e senha para usar quando remotamente efetuar login no. Para obter mais informações sobre acesso remoto, consulte [Habilitando o acesso remoto para implantações do Azure em Eclipse][].
        Sua caixa de diálogo **Publicar Azure** aparecerão similar ao seguinte: ![][ic719488]
1. Clique em **Publicar** para publicar o ambiente de teste.
    Quando solicitado a executar uma compilação inteira, clique em **Sim**. Isso pode levar alguns minutos para que a primeira compilação.
    Um **Registro de atividade do Azure** será exibida na sua seção de modos de exibição Eclipse com guias.
    ![][ic719489]
   Você pode usar esse log, bem como o modo de exibição do **Console** , para ver o progresso da implantação. Uma alternativa é fazer logon no [Portal de gerenciamento do Azure][]e use a seção de **Serviços de nuvem** para monitorar o status.
1. Quando sua implantação é implantada com êxito, o **Log de atividade do Azure** mostrará um status de **publicado**. Clique em **publicado**, conforme mostrado na imagem a seguir, e o seu navegador abrirá uma instância de sua implantação.
    ![][ic719490]

Como isso era uma implantação em um ambiente de teste, o nome DNS será o formato http://&lt;*guid*&gt;. cloudapp.net e a disposição de URL que contêm o nome DNS mais um sufixo para seu aplicativo. Por exemplo, http://447564652c20426f6220526f636b7321.cloudapp.net/MyHelloWorld. (A parte **MyHelloWorld** diferencia maiusculas de minúsculas). Você também pode ver o nome DNS se você clicar no nome de implantação no Portal de gerenciamento de plataforma do Azure (na parte de serviços de nuvem de portal de gerenciamento).

Embora este passo a passo para uma implantação no ambiente de teste, uma implantação de produção segue as mesmas etapas, exceto na caixa de diálogo **Publicar Azure** , selecione **produção** em vez de **preparação** para o **ambiente de destino**. Uma implantação de produção resulta em uma URL com base no nome DNS de sua escolha, em vez de um GUID como usado para preparação.

>[AZURE.WARNING] Neste ponto, você implantou seu aplicativo do Azure para a nuvem. No entanto, antes de prosseguir, perceba que um aplicativo implantado, mesmo se não estiver funcionando, continuará a acumular faturáveis para sua assinatura. Portanto, é muito importante que você exclua implantações indesejadas sua assinatura do Azure.

## <a name="about-azure-deployment-projects"></a>Sobre projetos de implantação do Azure ##

Para implantar um ou mais aplicativos Java no Azure, é necessário um projeto de implantação do Azure. Ele desempenha a função do "pacote" que seus aplicativos precisam ser disposto em para ser publicada no Azure.

Além de obter as informações sobre seus aplicativos, um projeto de implantação do Azure também contém informações sobre outros componentes importantes da sua implantação, mais importante: o contêiner de servidor de aplicativo para executar o aplicativo da web no e o tempo de execução Java executá-la no. Azure suporta uma grande seleção de servidores de aplicativo de Java, entre que você pode escolher e tempos de execução de Java.

Embora o exemplo usado aqui é bastante simplificado para fins educativos, um projeto de implantação do Azure também pode conter outras informações importantes de configuração que permite que você crie serviços de nuvem quase absolutamente complexo, escaláveis, altamente disponível, vários níveis com seus aplicativos. Você pode habilitar **afinidade de sessão ("sessões conjunta")**, **rápida de armazenamento em cache**, **depuração remota**, **Transferindo SSL**, **firewall/porta roteamento**, **acesso remoto**e um número de outros recursos poderosos.

Se você concluiu a seção anterior deste tutorial ("para implantar seu aplicativo no Azure, a maneira mais simple e mais rápida"), você verá um novo projeto de implantação do Azure no Explorador de projeto gerado automaticamente para você e denominada "**MyHelloWorld_onAzure**".

Você também poderia ter iniciado neste tutorial primeiro criar um projeto em branco implantação Azure si mesmo e, em seguida, adicionando seus aplicativos para ele. É mais um longo processo, mas dando a você mais controle sobre a configuração inicial do começo.

Para criar um novo projeto de implantação do Azure do zero, clique no botão **Novo projeto de implantação do Azure** ![][ic710876].

Independentemente de se você estiver trabalhando com um projeto de implantação do Azure já existente ou criar um do zero, é possível alterar suas configurações e componentes, como o JDK ou servidor de aplicativos, igualmente facilmente a qualquer momento.

Para alterar o JDK, ou o servidor de aplicativos ou da lista de aplicativos em um projeto de implantação do Azure existente:

1. Expanda o nó de projeto (por exemplo, **MyHelloWorld_onAzure**) no Explorador de projeto
2. Clique com botão direito **WorkerRole1**
3. Expanda o submenu **Azure** no menu de contexto
4. Clique em **configuração do servidor**

Independentemente de se você começou a estas etapas de configuração do servidor editando um projeto de implantação do Azure existente como mostrado acima ou criando um novo a partir do zero, você verá o mesmo tipo de caixas de diálogo permitindo que você configure seu JDK, componentes de servidor e aplicativo. Para saber mais como alterar as configurações nessas caixas de diálogo, por exemplo, para alterar o JDK, o servidor de aplicativo e adicionar ou remover aplicativos em uma implantação, consulte o artigo de [Propriedades de configuração do servidor][] .

## <a name="windows-only-to-deploy-your-application-to-the-compute-emulator"></a>Somente Windows: para implantar seu aplicativo para o emulador de computação ##

>[AZURE.NOTE] O emulador Azure só está disponível no Windows. Ignore esta seção se você estiver usando um sistema operacional diferente do Windows.

Se você tiver criado um novo projeto de implantação do Azure seguindo as etapas descritas anteriormente, isto é implicitamente, publicando seu aplicativo ao Azure, JDK e ao aplicativo servidores foram configurados para a nuvem, mas não para emulação local. Para preparar seu projeto para teste no emulador local, siga estas etapas:

1. No Explorador de projeto do Eclipse, clique em **MyHelloWorld_onAzure**.
1. Clique com botão direito em **WorkerRole1**.
1. Expanda o submenu **Azure** no menu de contexto.
1. Clique em **configuração do servidor**.
1. Na guia **JDK** , verifique se o Kit de ferramentas previamente tiver configurado um padrão JDK local para você. Se não, ou se você quiser alterar os padrões equivalente, certifique-se de que a caixa de seleção **usar o JDK a partir deste caminho de arquivo para teste localmente** está marcada e o local de instalação do JDK que você deseja usar for especificado. Se você quiser alterá-lo, clique no botão **Procurar** e usando o controle de navegação, selecione o local do diretório do JDK usar.
1. Clique na guia **servidor** .
1. Na caixa de texto **caminho de servidor Local** , na parte inferior da caixa de diálogo, digite o caminho de um servidor instalados localmente que coincida com o tipo e número de versão principal do servidor selecionado na parte superior da caixa de diálogo, sob a caixa de seleção **implantar um servidor desse tipo** . Se você quiser usar um tipo diferente ou uma versão principal do servidor do aplicativo, altere a seleção nessa caixa de seleção primeiro.
1. Clique em **Okey**.
1. Na barra de ferramentas Eclipse, clique no botão **executar no emulador Azure** , ![][ic710879]. Se o botão **executar no emulador Azure** não estiver habilitado, verifique se **MyHelloWorld_onAzure** está selecionada no Explorador de projeto do Eclipse e certifique-se de que o Explorador de projeto do Eclipse tem foco como a janela atual. Isso primeiro iniciar uma compilação completa do seu projeto e, em seguida, iniciar o aplicativo de web de Java no emulador computação. (Observe que, dependendo das características de desempenho do seu computador, a primeira compilação pode levar entre alguns segundos para alguns minutos, mas cria subsequentes obterá com mais rapidez.) Após a primeira etapa de compilação foi concluída, você será solicitado pelo controle de conta de usuário para Windows (UAC) para permitir que este comando fazer alterações em seu computador. Clique em **Sim**.

>[AZURE.IMPORTANT] Se você não vir o prompt do UAC, marque na barra de tarefas do Windows para o ícone UAC e clique nela primeiro. Às vezes, o UAC prompt não aparecem como uma janela mais alta, mas fica visível somente como um ícone de barra de tarefas.

1. Examine a saída do emulador computação interface de usuário para determinar se há problemas com o seu projeto. Dependendo do conteúdo do sua implantação, pode levar alguns minutos para seu aplicativo para ser iniciado totalmente dentro o emulador de computação.
1. Iniciar o navegador e usar a URL `http://localhost:8080/MyHelloWorld` como o endereço (o `MyHelloWorld` parte da URL diferencia maiusculas de minúsculas). Você deve ver seu aplicativo de MyHelloWorld (a saída de JSP), semelhante a imagem a seguir: ![][ic589579]

Quando estiver pronto para interromper o seu aplicativo seja executado no emulador computação, na barra de ferramentas Eclipse, clique no botão **Redefinir Azure emulador** ![][ic710880].

## <a name="to-delete-your-deployment"></a>Para excluir sua implantação ##

Para excluir sua implantação dentro do Kit de ferramentas do Azure para Eclipse, verifique se **MyHelloWorld_onAzure** está selecionada no Explorador de projeto do Eclipse, certifique-se de que o Explorador de projeto do Eclipse tem a janela atual foco e, em seguida, clique no botão **Cancelar publicação** ![][ic710883], na barra de ferramentas Eclipse. (Você pode fazer a mesma operação clicando **MyHelloWorld_onAzure** no Explorador de projeto do Eclipse, clicando em **Azure** e clicando em **desimplantar da nuvem do Azure**). Isso exibirá a caixa de diálogo **Cancelar a publicação de projeto do Azure** .

![][ic719491]

Selecione o serviço de assinatura e nuvem que contém sua implantação, selecione a implantação que você deseja excluir e, em seguida, clique em **Cancelar publicação**.

(Uma alternativa para usar o Kit de ferramentas para excluir a implantação é usar a seção de **Serviços de nuvem** do Portal de gerenciamento do Azure: Navegue até sua implantação, selecione-o e clique no botão **Excluir** . Isso parar e, em seguida, excluir, a implantação. Se você só quiser interromper a implantação e não excluí-la, clique no botão **Parar** em vez de no botão **Excluir** , mas como mencionado acima, se você não excluir a implantação, encargos faturáveis continuará acumular para a sua implantação, mesmo se ele for interrompido).

## <a name="see-also"></a>Consulte também ##

[Kit de ferramentas de Azure para Eclipse][]

[Instalando o Kit de ferramentas do Azure para Eclipse][] 

[Novidades no Azure Kit de ferramentas para Eclipse][]

Para obter mais informações sobre como usar o Azure com Java, consulte o [Azure Java Developer Center][].

<!-- URL List -->

[Central de desenvolvedores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portal de gerenciamento do Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Role Properties]: http://go.microsoft.com/fwlink/?LinkID=699525
[Kit de ferramentas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Habilitar o acesso remoto para implantações Azure em Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699538
[Instalando o Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propriedades de configuração do servidor]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Novidades no Azure Kit de ferramentas para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic589576]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589576.png
[ic589579]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589579.png
[ic600360]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic600360.png
[ic644267]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic644267.png
[ic659262]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic659262.png
[ic710876]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710876.png
[ic710879]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710880.png
[ic710882]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710883.png
[ic719488]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719488.png
[ic719489]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719489.png
[ic719490]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719490.png
[ic719491]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719491.png
[ic789598]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic789598.png
[publishDropdownButton]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/publishDropdownButton.png
