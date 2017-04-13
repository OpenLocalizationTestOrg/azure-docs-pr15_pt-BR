<properties
    pageTitle="Tomcat em uma máquina virtual | Microsoft Azure"
    description="Este tutorial usa recursos criados com o modelo clássico de implantação e mostra como criar uma máquina Virtual do Windows e configurá-lo para executar o servidor de aplicativos do Apache Tomcat."
    services="virtual-machines-windows"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.workload="web"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>Como executar um servidor de aplicativo Java em uma máquina virtual criada com o modelo clássico de implantação

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Com o Azure, você pode usar uma máquina virtual para fornecer recursos de servidor. Como exemplo, uma máquina virtual em execução no Azure pode ser configurada para hospedar um servidor de aplicativo Java, como o Apache Tomcat. Depois de concluir este guia, você terá uma compreensão de como criar uma máquina virtual em execução no Azure e configurá-lo para executar um servidor de aplicativo Java.

Você aprenderá:

* Como criar uma máquina virtual que tem um Java Development Kit (JDK) já instalado.
* Como entrar em sua máquina virtual remotamente.
* Como instalar um servidor de aplicativo Java em sua máquina virtual.
* Como criar um ponto de extremidade para sua máquina virtual.
* Como abrir uma porta no firewall para o seu servidor de aplicativo.

Para os fins deste tutorial, um servidor de aplicativos do Apache Tomcat será instalado em uma máquina virtual. Instalação completa resultará em uma instalação Tomcat como a seguir.

![Máquina virtual executada Apache Tomcat][virtual_machine_tomcat]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Para criar uma máquina virtual

1. Entrar no [portal clássico Azure](https://manage.windowsazure.com).
2. Clique em **novo**, clique em **Calcular**, clique em **Máquina Virtual**e, em seguida, clique em **Galeria de**.
3. Na caixa de diálogo **Selecionar imagem da máquina Virtual** , selecione **JDK 7 Windows Server 2012**.
Observe que **JDK 6 Windows Server 2012** está disponível se você tiver aplicativos legados que não estão prontos para executar em JDK 7.
4. Clique em **Avançar**.
5. Na caixa de diálogo **configuração de máquina Virtual** :
    1. Especifique um nome para a máquina virtual.
    2. Especifique o tamanho para usar para a máquina virtual.
    3. Insira um nome para o administrador no campo **Nome de usuário** . Lembre-se esse nome e a senha que você digitará Avançar, você irá usá-los quando remotamente entrar na máquina virtual.
    4. Digite uma senha no campo **nova senha** e digite-a novamente no campo **Confirmar** . Esta é a senha da conta de administrador.
    5. Clique em **Avançar**.
6. Na caixa de diálogo **configuração de máquina Virtual** seguinte:
    1. **Serviço de nuvem**, use o padrão de **criar um novo serviço de nuvem**.
    2. O valor de **nome DNS de serviço de nuvem** deve ser exclusivo em cloudapp.net. Se necessário, modificar esse valor para que esse Azure indica que ele seja exclusivo.
    2. Especifique uma região, grupo de afinidade ou rede virtual. Para os fins deste tutorial, especifique uma região como **Oeste EUA**.
    2. **Conta de armazenamento**, selecione **usar uma conta de armazenamento gerado automaticamente**.
    3. Para **Definir de disponibilidade**, selecione **(nenhum)**.
    4. Clique em **Avançar**.
7. Em final caixa de diálogo de **configuração de máquina Virtual** :
    1. Aceite as entradas de ponto de extremidade padrão.
    2. Clique em **Concluir**.

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>Para entrar em sua máquina virtual remotamente

1. Faça logon no [portal de clássico Azure](https://manage.windowsazure.com).
2. Clique em **máquinas virtuais**.
3. Clique no nome da máquina virtual que você deseja entrar.
4. Depois da máquina virtual, um menu pop-up na parte inferior da página permite conexões.
5. Clique em **Conectar**.
6. Responda às solicitações conforme necessário para se conectar à máquina virtual. Isso deve envolver salvar ou abrir o arquivo. rdp que contenha os detalhes de conexão. Talvez seja necessário copiar a url: porta como a última parte da primeira linha do arquivo. rdp e colá-lo em um aplicativo de entrada remoto.

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>Instalar um servidor de aplicativo Java em sua máquina virtual

Você pode copiar um servidor de aplicativos Java para sua máquina virtual, ou você pode instalar um servidor de aplicativo Java por meio de um instalador.

Para os fins deste tutorial, Tomcat será instalado.

1. Quando você estiver conectado à sua máquina virtual, abra uma sessão do navegador para [Apache Tomcat](http://tomcat.apache.org/download-70.cgi).
2. Clique duas vezes no link do **Instalador de serviço do Windows de 32 bits/64 bits**. Usando essa técnica, Tomcat será instalado como um serviço do Windows.
3. Quando solicitado, escolha executar o instalador.
4. No Assistente de **Instalação do Apache Tomcat** , siga os prompts para instalar o Tomcat. Para os fins deste tutorial, aceitando os padrões está corretamente configurado. Quando você chegar a caixa de diálogo **Concluindo o Assistente de configuração do Apache Tomcat** , opcionalmente, você pode verificar **Executar Apache Tomcat** para ter Tomcat iniciar agora. Clique em **Concluir** para concluir o processo de configuração do Tomcat.

## <a name="to-start-tomcat"></a>Para iniciar o Tomcat
Se você não escolheu executar o Tomcat na caixa de diálogo **Concluindo o Assistente de configuração do Apache Tomcat** , comece abrindo um prompt de comando em sua máquina virtual e executando **líquido iniciar Tomcat7**.

Agora você deve ver Tomcat executando se você executar o navegador da máquina virtual e abrir <http://localhost:8080>.

Para ver Tomcat executando máquinas externo, você precisa criar um ponto de extremidade e abrir uma porta.

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>Para criar um ponto de extremidade para sua máquina virtual
1. Entrar no [portal clássico Azure](https://manage.windowsazure.com).
2. Clique em **máquinas virtuais**.
3. Clique no nome da máquina virtual que esteja executando o servidor de aplicativos Java.
4. Clique em **pontos de extremidade**.
5. Clique em **Adicionar**.
6. Na caixa de diálogo **Adicionar ponto de extremidade** , certifique-se de **ponto de extremidade do adicionar autônoma** está selecionada e clique em **Avançar**.
7. Na caixa de diálogo **detalhes do ponto de extremidade de novo** :
    1. Especifique um nome para o ponto de extremidade; Por exemplo, **HttpIn**.
    2. Especifica **TCP** para o protocolo.
    3. Especifica **80** para a porta pública.
    4. Especifica **8080** para a porta particular.
    5. Clique no botão **Concluir** para fechar a caixa de diálogo. Seu ponto de extremidade agora será criado.

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>Para abrir uma porta no firewall para sua máquina virtual
1. Entrar sua máquina virtual.
2. Clique em **Iniciar do Windows**.
3. Clique em **Painel de controle**.
4. Clique em **sistema e segurança**, clique em **Firewall do Windows**e, em seguida, clique em **Configurações avançadas**.
5. Clique em **Regras de entrada**e, em seguida, clique em **Nova regra**.
 ![Nova regra de entrada][NewIBRule]
6. Para o **Tipo de regra**, selecione a **porta**e clique em **Avançar**.
 ![Nova porta de regra de entrada][NewRulePort]
7. Na tela de **portas e protocolo** , selecione **TCP**, especificar **8080** como a **porta local específico**e, em seguida, clique em **Avançar**.
 ![Nova regra de entrada][NewRuleProtocol]
8. Na tela de **ação** , selecione **Permitir a conexão**e clique em **Avançar**.
 ![Nova ação de regra de entrada][NewRuleAction]
9. Na tela de **perfil** , certifique-se de que **domínio**, **particular**e **pública** estão selecionadas e clique em **Avançar**.
 ![Novo perfil de regra de entrada][NewRuleProfile]
10. Na tela de **nome** , especifique um nome para a regra, como **HttpIn** (o nome da regra não é necessário para corresponder ao nome do ponto de extremidade, porém) e clique em **Concluir**.  
 ![Novo nome da regra de entrada][NewRuleName]

Neste ponto, seu site Tomcat deve ser visto de um navegador externo usando uma URL do formulário * *http://*seu\_DNS\_nome*. cloudapp.net**onde ** *seu\_DNS\_nome** * é o nome DNS que você especificou quando você criou a máquina virtual.

## <a name="application-lifecycle-considerations"></a>Considerações de ciclo de vida do aplicativo
* Você pode criar seu próprio arquivo de aplicativo da web (guerra) e adicioná-lo à pasta **webapps** . Por exemplo, crie um projeto de web dinâmico de página de serviço de Java (JSP) básica e exportá-lo como um arquivo de guerra, copie guerra à pasta de **webapps** Apache Tomcat na máquina virtual, execute-o em um navegador.
* Por padrão quando o serviço Tomcat estiver instalado, ele é definido para iniciar manualmente. Você pode alternar para iniciar automaticamente usando o snap-in de serviços. Inicie o snap-in de serviços clicando em **Iniciar do Windows**, **Ferramentas administrativas**e, em seguida, **Serviços**. Clique duas vezes no serviço **Apache Tomcat** e definir o **tipo de inicialização** como **automático**.

    ![Configuração de um serviço para iniciar automaticamente][service_automatic_startup]

    O benefício de ter início Tomcat automaticamente é que ele será iniciado se a máquina virtual seja reiniciada (por exemplo, após as atualizações de software que exigem a reinicialização são instaladas).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre outros serviços (como armazenamento do Azure, barramento de serviço e banco de dados SQL) que talvez você queira incluir com seus aplicativos Java exibindo as informações disponíveis no [Java Developer Center](https://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProfile.png
