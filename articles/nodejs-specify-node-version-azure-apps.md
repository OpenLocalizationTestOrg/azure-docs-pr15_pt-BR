<properties
    pageTitle="Especificando uma versão Node"
    description="Aprenda a especificar a versão do Node usada pelos Sites do Azure e serviços de nuvem"
    services=""
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="specifying-a-nodejs-version-in-an-azure-application"></a>Especificando uma versão Node em um aplicativo do Azure

Quando um aplicativo Node de hospedagem, talvez você queira garantir que seu aplicativo use uma versão específica de Node. Há várias maneiras de fazer isso para aplicativos hospedados no Azure.

##<a name="default-versions"></a>Versões padrão

As versões de Node fornecidas pelo Azure são atualizadas constantemente. A menos que especificado em contrário, a versão padrão especificado no `WEBSITE_NODE_DEFAULT_VERSION` variável de ambiente será usado. Para substituir esse valor padrão, siga as etapas nas seções seguintes deste artigo

> [AZURE.NOTE] Se estiver hospedando seu aplicativo em um serviço de nuvem do Azure (função web ou de trabalho), e ele for a primeira vez que você implantou o aplicativo, o Azure tentará usar a mesma versão do Node conforme você instalou em seu ambiente de desenvolvimento se ele corresponde a uma das versões padrão disponíveis no Azure.

##<a name="versioning-with-packagejson"></a>Controle de versão com package.json

Você pode especificar a versão do Node a ser usado, adicionando o seguinte ao arquivo **package.json** :

    "engines":{"node":version}

Onde *versão* é o número de versão específica para usar. Você pode pode especificar condições mais complexas para versão, tais como:

    "engines":{"node": "0.6.22 || 0.8.x"}

Como 0.6.22 não é uma das versões disponíveis no ambiente de hospedagem, a versão mais recente do 0,8 série que está disponível será usado - 0.8.4.

##<a name="versioning-websites-with-app-settings"></a>Sites de controle de versão com configurações de aplicativo
Se você estiver hospedando o aplicativo em um site, você pode definir a variável de ambiente **WEBSITE_NODE_DEFAULT_VERSION** para a versão desejada. 

##<a name="versioning-cloud-services-with-powershell"></a>Serviços de nuvem do controle de versão com o PowerShell

Se você estiver hospedando o aplicativo em um serviço de nuvem e estiver implantando o aplicativo usando o PowerShell do Azure, é possível substituir a versão de Node padrão, usando o cmdlet do PowerShell **Set-AzureServiceProjectRole** . Por exemplo:

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

Observe que os parâmetros na instrução acima diferenciam maiusculas de minúsculas.  Você pode verificar que a versão correta do Node foi selecionada verificando a propriedade de **mecanismos** em **package.json sua função**.

Você também pode usar o **Get-AzureServiceProjectRoleRuntime** para recuperar uma lista de versões de Node disponíveis para aplicativos hospedados como um serviço de nuvem.  Sempre verificar a versão do Node depende de seu projeto está nessa lista.

##<a name="using-a-custom-version-with-azure-websites"></a>Usando uma versão personalizada com sites do Azure

Enquanto o Azure fornece várias versões de padrão do Node, talvez você queira usar uma versão que não é fornecida por padrão. Se seu aplicativo estiver hospedado como um site do Azure, você pode fazer isso usando o arquivo de **iisnode.yml** . As etapas a seguir orientam durante o processo de usar uma versão personalizada do Node com um site do Azure:

1. Criar uma nova pasta e, em seguida, criar um arquivo de **Server. js** dentro do diretório. O arquivo **Server. js** deve conter o seguinte:

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    Isto irá exibir a versão de Node sendo usada quando você navega do site.

2. Criar um novo site e observe o nome do site. Por exemplo, a seguir usa as [Ferramentas de linha de comando Azure] para criar um novo site do Azure chamado **Meu_Site**e habilitar um repositório gito do site.

        azure site create mywebsite --git

3. Crie uma nova pasta chamada **bin** como um filho do diretório que contém o arquivo **Server. js** .

4. Baixe a versão específica de **node.exe** (a versão do Windows) que você deseja usar com o aplicativo. Por exemplo, a seguir usa **curl** baixar versão 0.8.1:

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Salve o arquivo de **node.exe** para a pasta **bin** criada anteriormente.

5. Criar um arquivo de **iisnode.yml** na mesma pasta que o arquivo **Server. js** e adicione o seguinte conteúdo para o arquivo de **iisnode.yml** :

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Este caminho é onde o arquivo de **node.exe** dentro de seu projeto será localizado depois de publicar seu aplicativo para o site do Azure.

6. Publica seu aplicativo. Por exemplo, desde que eu criei um novo site com o parâmetro – gito anteriormente, os seguintes comandos serão adicionar os arquivos do aplicativo ao meu repositório gito local e, em seguida, push-los para o repositório de site:

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    Depois que o aplicativo foi publicado, abra o site em um navegador. Você verá uma mensagem informando "Olá do Azure executando a versão de nó: v0.8.1".

##<a name="next-steps"></a>Próximas etapas

Agora que você sabe como especificar a versão do Node usado pelo seu aplicativo, saiba como [trabalhar com módulos], [Construir e implantar um Site Node]e [como usar as ferramentas de linha de comando do Azure para Mac e Linux].

Para obter mais informações, consulte o [Node Developer Center](/develop/nodejs/).

[Como usar as ferramentas de linha de comando do Azure para Mac e Linux]: xplat-cli-install.md
[Ferramentas de linha de comando Azure]: xplat-cli-install.md
[trabalhar com módulos]: nodejs-use-node-modules-azure-apps.md
[criar e implantar um Site da Web Node]: web-sites-nodejs-develop-deploy-mac.md
