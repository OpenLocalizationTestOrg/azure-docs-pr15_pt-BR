<properties
    pageTitle="Depuração de aplicativos do Azure no Eclipse"
    description="Saiba mais sobre os aplicativos de Azure depuração usando o Kit de ferramentas do Azure para Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690949.aspx -->

# <a name="debugging-azure-applications-in-eclipse"></a>Depuração de aplicativos do Azure no Eclipse #

Usando o Kit de ferramentas do Azure para Eclipse, você pode depurar seus aplicativos se ele estiver executando o Azure ou o emulador de computação se você estiver usando o Windows como seu sistema operacional. A imagem a seguir mostra as propriedades de **depuração** diálogo usado para ativar a depuração remota:

![][ic719504]

Este tutorial supõe que você já tiver um aplicativo que foi criado com êxito e está familiarizado com o emulador de computação implantando no Azure.

Vamos usar o aplicativo do tutorial de [usando a biblioteca de tempo de execução de serviço do Azure no JSP][] como ponto de partida para este tópico. Antes de prosseguir, crie esse aplicativo se você ainda não tiver feito isso.

## <a name="to-debug-your-application-while-running-in-azure"></a>Depurar seu aplicativo durante a execução no Azure ##

>[AZURE.WARNING] Suporte de atual do Kit de ferramentas para depuração de Java remota destina-se principalmente para implantações em execução no emulador computação Azure. Porque a conexão de depuração não é seguro, você não deve ativar a depuração remota em implantações de produção. Se você precisar depurar um aplicativo em execução na nuvem Azure, usar uma implantação de teste, mas perceber que o acesso não autorizado à sua sessão de depuração é possível se alguém conhece o endereço IP de sua implantação de nuvem, mesmo se estiver em uma implantação de teste.

1. Construir seu projeto para teste no emulador: Explorador no Eclipse de projeto, **MyAzureProject**de atalho, clique em **Propriedades**, clique em **Azure**e definir **Construir para** a **implantação na nuvem**.
1. Recrie o projeto: no menu Eclipse, clique em **projeto**, clique em **Construir todos**.
1. Implante seu aplicativo para *teste* no Azure.
    >[AZURE.IMPORTANT] Conforme mencionado acima, é altamente recomendável que você depurar no emulador computação na maioria dos casos, então depurar no ambiente de teste somente se depuração adicional é necessária. É recomendável não depurar no ambiente de produção.
1. Depois que sua implantação está pronta no Azure, obter o nome DNS para a implantação do [Portal de gerenciamento do Azure][]. Uma implantação preparação possui um nome DNS na forma de http://*&lt;guid&gt;*. cloudapp.net, onde * &lt;guid&gt; * é um valor GUID atribuído pelo Azure.
1. No Explorador de projeto do Eclipse, **WorkerRole1**de atalho, clique **Azure**e clique em **depuração**.
1. Na caixa de diálogo **Propriedades de depuração de WorkerRole1** :
    1. Verificar **Ativar depuração remota para esta função.**
    1. **Ponto de extremidade de entrada para usar**, use **depuração (público: 8090, particular: 8090)**.
    1. Certifique-se de **Que JVM iniciar no modo suspenso, aguardando uma conexão de depurador** está desmarcada.
        >[AZURE.IMPORTANT] A opção **JVM iniciar no modo suspenso, aguardando uma conexão de depurador** destina depuração cenários no emulador computação somente avançada (não para implantações de nuvem). Se a opção **JVM iniciar no modo suspenso, aguardando uma conexão de depurador** for usada, ele será suspender o processo de inicialização do servidor até que o depurador Eclipse está conectado à sua JVM. Embora você possa usar esta opção para uma sessão de depuração usando o emulador de computação, não usá-lo para uma sessão de depuração em uma implantação de nuvem. Inicialização do servidor ocorrerá em uma tarefa de inicialização do Azure e a nuvem Azure não disponibilizar pontos de extremidade públicos até que a tarefa de inicialização seja concluída. Portanto, um processo de inicialização não será concluída com êxito se essa opção estiver habilitada em uma implantação de nuvem, porque não será capaz de receber uma conexão de um cliente Eclipse externo.
1. Clique em **criar configurações de depuração**.
1. Na caixa de diálogo **Configuração de depurar Azure** :
    1. Para o **projeto de Java depurar**, selecione o projeto **MyHelloWorld** .
    1. Para **configurar depuração para**, verifique a **nuvem Azure (transferência)**.
    1. Certifique-se de **que emulador de computação do Azure** está desmarcada.
    1. Do **Host**, digite o nome DNS de sua implantação em estágios, mas sem o anterior **http://**. Por exemplo (use seu GUID no lugar o GUID mostrado aqui): **4e616d65-6f6e-6 d 65-6973-526f62657274.cloudapp.net**
1. Clique **Okey** para fechar a caixa de diálogo **Configuração de depurar do Azure** .
1. Clique **Okey** para fechar a caixa de diálogo de **Propriedades de depuração de WorkerRole1** .
1. Se você não tiver um ponto de interrupção já definido no JSP, configurá-lo:
    1. No Explorador de projeto do Eclipse, expanda **MyHelloWorld**, expanda o **conteúdo da Web**e clique duas vezes em **JSP**.
    1. Dentro JSP, clique com botão direito na barra azul à esquerda do seu código Java e clique em **Pontos de interrupção de alternância**, conforme mostrado a seguir: ![][ic551537]
1. No menu do Eclipse, clique em **Executar** e, em seguida, clique em **Configurações de depuração**.
1. Na caixa de diálogo **Configurações de depurar** , expanda o **Aplicativo Java remoto** no painel à esquerda, selecione **Azure nuvem (WorkerRole1)**e clique em **Depurar**.
1. Em seu navegador, execute o aplicativo em estágios, **http://***&lt;guid&gt;***.cloudapp.net/MyHelloWorld**, substituindo o GUID de seu nome de DNS para * &lt;guid&gt;*. Se for solicitado por uma caixa de diálogo **Confirmar alternar de perspectiva** , clique em **Sim**. Sua sessão de depuração agora deve ser executado para a linha de código onde o ponto de interrupção foi definido.

>[AZURE.NOTE] Se você estiver tentando iniciar um controle remoto depuração conexão a uma implantação que tem várias instâncias de função em execução, você atualmente não é possível controlar qual instância depurador será inicialmente conectado, como o balanceador de carga Azure selecionará aleatoriamente uma instância. Quando você está conectado com essa instância, no entanto, você continuará a mesma instância de depuração. Observação Além disso, se houver um período de inatividade de mais de 4 minutos (por exemplo, quando você está parado em um ponto de interrupção por muito tempo), o Azure pode fechar a conexão.

## <a name="debugging-a-specific-role-instance-in-a-multi-instance-deployment"></a>Depuração uma instância de função específica em uma implantação de várias instâncias ##

Quando sua implantação está em execução na nuvem, você estará provavelmente executando-lo em vários computadores, ou função, instâncias. Isso permite que você tire proveito dos garantia de disponibilidade do Azure 99,95% e dimensionar seu aplicativo.

Em cenários assim, talvez você precise depurar remotamente seu aplicativo Java em uma instância de função específica. No entanto, se você habilitar apenas uma empresa de entrada regular para depuração, balanceador de carga Azure tornará praticamente impossível para conectar o depurador a uma instância de função específica. Em vez disso, ele irá conectá-lo em uma instância de função que ela seleciona aleatoriamente.

Este é o tipo de cenário onde as vantagens dos pontos de extremidade de entrada do instância tornará mais fácil para você depurar uma instância de função específica.

Digamos que você planeja executar até 5 instâncias de função da sua implantação. Usando a página de propriedades de **pontos de extremidade** na caixa de diálogo de propriedades de função, crie um ponto de extremidade de entrada de instância e atribuí-la um intervalo de portas públicos, em vez de um número de porta única. Por exemplo, na caixa de entrada de **porta pública** , especifique **81-85**.

Depois de implantar seu aplicativo com esse ponto de extremidade de instância, Azure irá atribuir um número de porta exclusivo deste intervalo para cada uma das instâncias de função. Em seguida, para descobrir qual instância tem atribuída qual número de porta, você pode usar a variável de ambiente *InstanceEndpointName***_PUBLICPORT** (onde *InstanceEndpointName* é o nome atribuído quando você criou o ponto de extremidade de instância) configurada automaticamente pelo Kit de ferramentas na sua implantação (por exemplo, retornando seu valor no rodapé de uma página da Web, portanto você pode lê-lo quando você navega-lo).

Depois que você souber quais número da porta pública instância foi atribuída, você pode fazer referência-la na sua configuração de depuração no Eclipse, por fixá-lo para o nome de host do seu serviço. Isso permitirá o depurador Eclipse para se conectar a essa instância específica e não qualquer das outras instâncias.

## <a name="windows-only-to-debug-your-application-while-running-in-the-compute-emulator"></a>Somente Windows: depurar seu aplicativo enquanto executa no emulador computação ##

>[AZURE.NOTE] O emulador Azure só está disponível no Windows. Ignore esta seção se você estiver usando um sistema operacional diferente do Windows. 

1. Construir seu projeto para teste no emulador: Explorador no Eclipse de projeto, **MyAzureProject**de atalho, clique em **Propriedades**, clique em **Azure**e definir **Construir para** **teste no emulador**.
1. Recrie o projeto: no menu Eclipse, clique em **projeto**, clique em **Construir todos**.
1. No Explorador de projeto do Eclipse, **WorkerRole1**de atalho, clique **Azure**e clique em **depuração**.
1. Na caixa de diálogo **Propriedades de depuração de WorkerRole1** :
    1. Verificar **Ativar depuração remota para esta função.**
    1. **Ponto de extremidade de entrada para usar**, use o ponto de extremidade padrão gerado automaticamente pelo Kit de ferramentas, listada como **depuração (público: 8090, particular: 8090)**.
    1. Certifique-se de que a opção **JVM iniciar no modo suspenso, aguardando uma conexão de depurador** está desmarcada.
        >[AZURE.IMPORTANT] A opção **JVM iniciar no modo suspenso, aguardando uma conexão de depurador** destina depuração cenários no emulador computação somente avançada (não para implantações de nuvem). Se a opção **JVM iniciar no modo suspenso, aguardando uma conexão de depurador** for usada, ele será suspender o processo de inicialização do servidor até que o depurador Eclipse está conectado à sua JVM. Embora você possa usar esta opção para uma sessão de depuração usando o emulador de computação, não usá-lo para uma sessão de depuração em uma implantação de nuvem. Inicialização do servidor ocorrerá em uma tarefa de inicialização do Azure e a nuvem Azure não disponibilizar pontos de extremidade públicos até que a tarefa de inicialização seja concluída. Portanto, um processo de inicialização não será concluída com êxito se essa opção estiver habilitada em uma implantação de nuvem, porque não será capaz de receber uma conexão de um cliente Eclipse externo.
1. Clique em **criar configurações de depuração**.
1. Na caixa de diálogo **Configuração de depuração do Azure** :
    1. Para o **projeto de Java depurar**, selecione o projeto **MyHelloWorld** .
    1. Para **configurar depuração para**, marque **emulador de computação do Azure**.
1. Clique **Okey** para fechar a caixa de diálogo **Configuração de depuração do Azure** .
1. Clique **Okey** para fechar a caixa de diálogo de **Propriedades de depuração de WorkerRole1** .
1. Defina um ponto de interrupção JSP:
    1. No Explorador de projeto do Eclipse, expanda **MyHelloWorld**, expanda o **conteúdo da Web**e clique duas vezes em **JSP**.
    1. Dentro JSP, clique com botão direito na barra azul à esquerda do seu código Java e clique em **Pontos de interrupção de alternância**, conforme mostrado a seguir: ![][ic551537]

       Um ponto de interrupção é definido se você vir um ícone de ponto de interrupção dentro da barra azul à esquerda do seu código Java.
1. Inicie o aplicativo no emulador computação clicando no botão **executar no emulador Azure** na barra de ferramentas Azure.
1. No menu do Eclipse, clique em **Executar** e, em seguida, clique em **Configurações de depuração**.
1. Na caixa de diálogo **Configurações de depurar** , expanda **Aplicativo Java remoto** no painel à esquerda, selecione **Emulador Azure (WorkerRole1)**e clique em **Depurar**.
1. Após o emulador de computação indica que o aplicativo está sendo executado, em seu navegador, execute **http://localhost:8080/MyHelloWorld**.
    Se for solicitado por uma caixa de diálogo **Confirmar alternar de perspectiva** , clique em **Sim**.
    Sua sessão de depuração agora deve ser executado para a linha de código onde o ponto de interrupção foi definido.

Isso mostrado como depurar no emulador computação; a próxima seção mostra como depurar um aplicativo implantado no Azure.

## <a name="debugging-notes"></a>Depuração de anotações ##

* Após a depuração, você pode alternar a perspectiva de **Depurar** **Java** via clicando no menu do Eclipse, clicando em **janela**, **Perspectiva aberto**, e selecionando a perspectiva que você deseja usar.
* Para habilitar a depuração remota no GlassFish, não use o recurso de configuração de depuração remota do Kit de ferramentas do Azure para Eclipse. Em vez disso, configure o GlassFish manualmente. Devido a maneira que GlassFish trata opções Java predefinidas em variáveis de ambiente, remoto depuração recurso de configuração do Kit de ferramentas não funciona corretamente com GlassFish. Se o recurso de configuração remoto depuração para o do Kit de ferramentas estiver habilitado, ele pode impedir GlassFish iniciando.

## <a name="see-also"></a>Consulte também ##

[Kit de ferramentas de Azure para Eclipse][]

[Criando um aplicativo Hello World para Azure no Eclipse][]

[Instalando o Kit de ferramentas do Azure para Eclipse][] 

Para obter mais informações sobre como usar o Azure com Java, consulte o [Azure Java Developer Center][].

<!-- URL List -->

[Central de desenvolvedores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portal de gerenciamento do Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Kit de ferramentas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criando um aplicativo Hello World para Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalando o Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Usando a biblioteca de tempo de execução de serviço Azure no JSP]: http://go.microsoft.com/fwlink/?LinkID=699551

<!-- IMG List -->

[ic719504]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic719504.png
[ic551537]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic551537.png
