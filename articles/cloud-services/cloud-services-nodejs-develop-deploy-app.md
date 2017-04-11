<properties
    pageTitle="Guia de Introdução ao Node | Microsoft Azure"
    description="Aprenda a criar um aplicativo de web Node simples e implantá-lo em um serviço de nuvem Azure."
    services="cloud-services"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="build-and-deploy-a-nodejs-application-to-an-azure-cloud-service"></a>Criar e implantar um aplicativo Node para um serviço de nuvem do Azure

> [AZURE.SELECTOR]
- [Node](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

Este tutorial mostra como criar um aplicativo Node simples em execução em um serviço de nuvem do Azure. Serviços de nuvem são os blocos de construção de aplicativos de nuvem scalable no Azure. Eles permitem a separação e escala-out dos componentes front-end e back-end do seu aplicativo e gerenciamento independente.  Serviços de nuvem fornecem uma máquina virtual dedicada robusta para cada função de hospedagem confiável.

Para obter mais informações sobre os serviços de nuvem e como eles se compara a sites do Azure e máquinas virtuais, consulte [comparação de máquinas virtuais, serviços de nuvem e sites de Azure].

>[AZURE.TIP] Procurando para criar um site simple? Se seu cenário envolve apenas um site simple front-end, considere [usar um aplicativo web leve]. Você pode atualizar facilmente para um serviço de nuvem como o seu aplicativo web cresce e suas necessidades mudam.

Seguindo este tutorial, você irá criar um aplicativo web simples hospedado dentro de uma função web. Você usará o emulador de computação para testar seu aplicativo localmente e implantá-lo usando ferramentas de linha de comando do PowerShell.

O aplicativo é um aplicativo simples "hello world":

![Um navegador da web exibindo a página da web Hello World][A web browser displaying the Hello World web page]

## <a name="prerequisites"></a>Pré-requisitos

> [AZURE.NOTE] Este tutorial usa PowerShell do Azure, que requer o Windows.

- Instalar e configurar [O Powershell do Azure].
- Baixe e instale o [SDK do Azure para .NET 2.7]. Na configuração da instalação, selecione:
    - MicrosoftAzureAuthoringTools
    - MicrosoftAzureComputeEmulator


## <a name="create-an-azure-cloud-service-project"></a>Criar um projeto de serviço de nuvem do Azure

Execute as seguintes tarefas para criar um novo projeto de serviço de nuvem do Azure, juntamente com estrutura de Node básica:

1. Executar **o Windows PowerShell** como administrador; no menu **Iniciar** ou **Tela Iniciar**, procure **O Windows PowerShell**.

2. [PowerShell se conectar] à sua assinatura.

3. Insira o seguinte cmdlet do PowerShell para criar para criar o projeto:

        New-AzureServiceProject helloworld

    ![O resultado do comando New-AzureService helloworld][The result of the New-AzureService helloworld command]

    O cmdlet **New-AzureServiceProject** gera uma estrutura básica para a publicação de um aplicativo de Node para um serviço na nuvem. Ele contém arquivos de configuração necessários para publicação no Azure. O cmdlet também altera seu diretório de trabalho para o diretório para o serviço.

    O cmdlet cria os seguintes arquivos:

    -   **ServiceConfiguration.Cloud.cscfg**, **ServiceConfiguration.Local.cscfg** e **ServiceDefinition.csdef**: específicas do Azure arquivos necessárias para publicar seu aplicativo. Para obter mais informações, consulte [Visão geral da criação de um serviço hospedado para Azure].

    -   **deploymentSettings.json**: armazena configurações locais que são usadas pelos cmdlets de implantação do PowerShell do Azure.

4.  Insira o seguinte comando para adicionar uma nova função da web:

        Add-AzureNodeWebRole

    ![A saída do comando Add-AzureNodeWebRole][The output of the Add-AzureNodeWebRole command]

    O cmdlet **Add-AzureNodeWebRole** cria um aplicativo Node básico. Ele também modifica os arquivos **.csfg** e **.csdef** para adicionar entradas de configuração para a nova função.

    > [AZURE.NOTE] Se você não especificar um nome de função, um nome padrão será usado. Você pode fornecer um nome como o primeiro parâmetro de cmdlet:`Add-AzureNodeWebRole MyRole`

O aplicativo Node é definido no arquivo **Server. js**, localizado no diretório para a função de web (**WebRole1** por padrão). Aqui está o código:

    var http = require('http');
    var port = process.env.port || 1337;
    http.createServer(function (req, res) {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Hello World\n');
    }).listen(port);

Este código é essencialmente igual a amostra de "Olá, mundo" no site [nodejs.org] , exceto que ele usa o número da porta atribuída pelo ambiente de nuvem.

## <a name="deploy-the-application-to-azure"></a>Implantar o aplicativo do Azure

    [AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

### <a name="download-the-azure-publishing-settings"></a>Baixar as definições de publicação Azure

Para implantar seu aplicativo no Azure, você deve primeiro baixar as configurações de publicação para sua assinatura do Azure.

1.  Execute o seguinte cmdlet do PowerShell do Azure:

        Get-AzurePublishSettingsFile

    Isso vai usar seu navegador para navegar até a página de download de configurações de publicação. Você pode ser solicitado a fazer logon com uma Account da Microsoft. Em caso afirmativo, use a conta associada a sua assinatura do Azure.

    Salve o perfil baixado para um local de arquivo que você pode acessar facilmente.

2.  Execute seguindo cmdlet para importar o perfil de publicação que você baixou:

        Import-AzurePublishSettingsFile [path to file]


    > [AZURE.NOTE] Após importar as configurações de publicação, considere excluindo o arquivo baixado publishsettings, porque ele contém informações que podem permitir que alguém para acessar sua conta.

### <a name="publish-the-application"></a>Publicar o aplicativo

Para publicar, execute os seguintes comandos:

    $ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))   
    Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch

- **-ServiceName** Especifica o nome para a implantação. Este valor deve ser um nome exclusivo, caso contrário, o processo de publicação falhará. Move o comando **Get-Date** em uma cadeia de caracteres de data/hora que deve tornar o nome exclusivo.

- **-Local** Especifica o data center que o aplicativo será hospedado no. Para ver uma lista dos data centers disponíveis, use o cmdlet **Get-AzureLocation** .

- **-Início** abre uma janela de navegador e navega para o serviço hospedado após a conclusão da implantação.

Após a publicação estiver concluído, você verá uma resposta semelhante à seguinte:

![A saída do comando Publicar-AzureService][The output of the Publish-AzureService command]

> [AZURE.NOTE]
> Pode levar alguns minutos para que o aplicativo implantar e se tornam disponíveis quando publicado pela primeira vez.

Após a implantação ter sido concluído, uma janela do navegador será aberto e navegue até o serviço de nuvem.

![Uma janela do navegador exibindo a página de mundo Olá; a URL indica que a página está hospedada no Azure.][A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]

Seu aplicativo agora está em execução no Azure.

O cmdlet **Publicar AzureServiceProject** executa as seguintes etapas:

1.  Cria um pacote para implantar. O pacote contém todos os arquivos em sua pasta de aplicativo.

2.  Cria uma nova **conta de armazenamento** , caso não exista. A conta de armazenamento do Azure é usada para armazenar o pacote de aplicativo durante a implantação. Você pode excluir a conta de armazenamento com segurança após a conclusão da implantação.

3.  Cria um novo **serviço de nuvem** , se ainda não existir. Um **serviço de nuvem** é o contêiner no qual o aplicativo está hospedado quando ele for implantado no Azure. Para obter mais informações, consulte [Visão geral da criação de um serviço hospedado para Azure].

4.  Publica o pacote de implantação do Azure.


## <a name="stopping-and-deleting-your-application"></a>Interrompendo e excluindo seu aplicativo

Após implantar seu aplicativo, talvez você queira desabilitá-lo para que você possa evitar custos extras. Faturas Azure web instâncias de função por hora de tempo de servidor consumida. Hora do servidor é consumida quando seu aplicativo é implantado, mesmo se as instâncias não estiver executando e estiverem no estado interrompido.

1.  Na janela do Windows PowerShell, interrompa a implantação de serviço criada na seção anterior, com o seguinte cmdlet:

        Stop-AzureService

    Interromper o serviço pode demorar vários minutos. Quando o serviço for interrompido, você recebe uma mensagem indicando que ele parou.

    ![O status do comando parar-AzureService][The status of the Stop-AzureService command]

2.  Para excluir o serviço, ligue o seguinte cmdlet:

        Remove-AzureService

    Quando solicitado, digite **Y** para excluir o serviço.

    Excluindo o serviço pode demorar vários minutos. Após o serviço foi excluído, você recebe uma mensagem indicando que o serviço foi excluído.

    ![O status do comando Remover-AzureService][The status of the Remove-AzureService command]

    > [AZURE.NOTE] Excluindo o serviço não exclui a conta de armazenamento que foi criada quando o serviço foi inicialmente publicado, e você continuará será cobrado para armazenamento usado. Se nada mais é usando o armazenamento, talvez você queira excluí-la.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte o [Node Developer Center].

<!-- URL List -->

[Comparação de sites, serviços de nuvem e máquinas virtuais Azure]: ../app-service-web/choose-web-site-cloud-service-vm.md
[usando um aplicativo web leve]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Powershell Azure]: ../powershell-install-configure.md
[SDK do Azure para .NET 2.7]: http://www.microsoft.com/en-us/download/details.aspx?id=48178
[Conectar PowerShell]: ../powershell-install-configure.md#how-to-connect-to-your-subscription
[nodejs.org]: http://nodejs.org/
[Visão geral da criação de um serviço hospedado para o Azure]: https://azure.microsoft.com/documentation/services/cloud-services/
[Central de desenvolvedores do Node]: https://azure.microsoft.com/develop/nodejs/

<!-- IMG List -->

[The result of the New-AzureService helloworld command]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
[The output of the Add-AzureNodeWebRole command]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
[A web browser displaying the Hello World web page]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
[A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
[The status of the Stop-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
[The status of the Remove-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png
