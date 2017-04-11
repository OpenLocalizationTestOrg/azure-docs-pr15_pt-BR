<properties
    pageTitle="Começar a usar o SDK do Azure CDN para Node | Microsoft Azure"
    description="Aprenda a gravar Node aplicativos para gerenciar CDN do Azure."
    services="cdn"
    documentationCenter="nodejs"
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="casoper"/>

# <a name="get-started-with-azure-cdn-development"></a>Introdução ao desenvolvimento de CDN do Azure

> [AZURE.SELECTOR]
- [Node](cdn-app-dev-node.md)
- [.NET](cdn-app-dev-net.md)

Você pode usar o [SDK do Azure CDN para Node](https://www.npmjs.com/package/azure-arm-cdn) para automatizar a criação e gerenciamento de perfis CDN e pontos de extremidade.  Este tutorial percorre a criação de um aplicativo de console Node simples que demonstra várias das operações disponíveis.  Este tutorial não pretende descrevem todos os aspectos do Azure CDN SDK para Node em detalhes.

Para concluir este tutorial, você já deve ter [Node](http://www.nodejs.org) **4** ou posterior instalado e configurado.  Você pode usar qualquer editor de texto que você deseja criar o seu aplicativo Node.  Para escrever este tutorial, usei [Visual Studio código](https://code.visualstudio.com).  

> [AZURE.TIP] A [concluiu projeto a partir deste tutorial](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) está disponível para download no MSDN.

[AZURE.INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Crie seu projeto e adicione dependências NPM

Agora que podemos criar um grupo de recursos para nossos perfis CDN e nossa permissão de aplicativo do Azure AD para gerenciar perfis CDN e pontos de extremidade dentro desse grupo, podemos começar criando nosso aplicativo.

Crie uma pasta para armazenar seu aplicativo.  A partir de um console com as ferramentas de Node no caminho atual, defina sua localização atual para essa nova pasta e inicializar o seu projeto executando:
    
    npm init
    
Em seguida, aparecerá uma série de perguntas ao inicializar o seu projeto.  Para o **ponto de entrada**, este tutorial usa *app.js*.  Você pode ver minhas outras opções no exemplo a seguir.

![Saída de inicialização NPM](./media/cdn-app-dev-node/cdn-npm-init.png)

Agora, nosso projeto é inicializado com um arquivo de *packages.json* .  Nosso projeto vai usar algumas bibliotecas Azure contidas em NPM pacotes.  Vamos usar o tempo de execução de cliente do Azure para Node (ms-restante-azure) e a biblioteca de cliente do Azure CDN para Node (cd de arm azure).  Vamos adicionar aqueles ao projeto como dependências.
 
    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Depois de concluir os pacotes de instalação, o *package.json* arquivo deve parecer semelhante a este exemplo (versão números podem diferir):

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Finalmente, usando o editor de texto, crie um arquivo de texto em branco e salve-o na pasta raiz do nossa projeto como *app.js*.  Agora está prontos para começar a escrever código.

## <a name="requires-constants-authentication-and-structure"></a>Requer, constantes, autenticação e estrutura

Com *app.js* aberto no nosso editor, vamos dar início a estrutura básica de nosso programa escrito.

1. Adicione o "requer" para nossos pacotes NPM na parte superior com o seguinte:

    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```

2. Precisamos definir algumas constantes que nossos métodos usará.  Adicione o seguinte.  Certifique-se de substituir os espaços reservados, incluindo o ** &lt;colchetes&gt;**, com seus próprios valores conforme necessário.

    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";

    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```

3. Em seguida, vamos criar uma instância do cliente de gerenciamento de CDN e dar a ele nossas credenciais.

    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
    
    Se você estiver usando autenticação de usuário individual, essas duas linhas terá uma aparência um pouco diferentes.

    >[AZURE.IMPORTANT] Use somente este exemplo de código se você estiver escolhendo ter autenticação de usuário individual, em vez de um serviço principal.  Tenha cuidado para proteger suas credenciais de usuário individual e mantê-las secreta.

    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```

    Certifique-se de substituir os itens de ** &lt;colchetes&gt; ** com as informações corretas.  Para `<redirect URI>`, use o redirecionamento URI que você inseriu quando você registrou o aplicativo no Azure AD.
    

4.  Nosso aplicativo de console Node vai demorar alguns parâmetros de linha de comando.  Vamos validar pelo menos um parâmetro passado.

    ```javascript
    //Collect command-line parameters
    var parms = process.argv.slice(2);

    //Do we have parameters?
    if(parms == null || parms.length == 0)
    {
        console.log("Not enough parameters!");
        console.log("Valid commands are list, delete, create, and purge.");
        process.exit(1);
    }
    ```

5. Que nos traz a parte principal da nosso programa, onde estamos desativar ramificação para outras funções com base em quais parâmetros foram passados.

    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;

        case "create":
            cdnCreate();
            break;
        
        case "delete":
            cdnDelete();
            break;

        case "purge":
            cdnPurge();
            break;

        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```

6.  Em vários lugares no nosso programa, podemos precisará certificar-se de que o número correto de parâmetros foram passado e exibe ajuda se eles não parecem corretos.  Vamos criar funções para fazê-lo.

    ```javascript
    function requireParms(parmCount) {
        if(parms.length < parmCount) {
            usageHelp(parms[0].toLowerCase());
            process.exit(1);
        }
    }

    function usageHelp(cmd) {
        console.log("Usage for " + cmd + ":");
        switch(cmd)
        {
            case "list":
                console.log("list profiles");
                console.log("list endpoints <profile name>");
                break;

            case "create":
                console.log("create profile <profile name>");
                console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
                break;
            
            case "delete":
                console.log("delete profile <profile name>");
                console.log("delete endpoint <profile name> <endpoint name>");
                break;

            case "purge":
                console.log("purge <profile name> <endpoint name> <path>");
                break;

            default:
                console.log("Invalid command.");
        }
    }
    ```

7. Finalmente, as funções que usaremos no cliente gerenciamento CDN são assíncronas, para que eles precisam de um método para chamar novamente quando ela terminar.  Vamos criar uma que pode exibir a saída do cliente do gerenciamento de CDN (se houver) e saia do programa normalmente.

    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Agora que a estrutura básica de nosso programa escrita, podemos deve criar as funções chamadas com base em nossos parâmetros.

## <a name="list-cdn-profiles-and-endpoints"></a>Lista CDN perfis e pontos de extremidade

Vamos começar com o código para listar nosso perfis existentes e pontos de extremidade.  Meus comentários de código fornecem a sintaxe esperada para sabemos onde vai cada parâmetro.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Criar pontos de extremidade e perfis CDN

Em seguida, vamos escrever as funções para criar perfis e pontos de extremidade.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>Limpar um ponto extremo

Considerando que o ponto de extremidade tiver sido criado, uma tarefa comum que queremos executar no nosso programa está limpando conteúdo em nosso ponto de extremidade.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Excluir pontos de extremidade e perfis CDN

A última função que podemos incluirá exclui pontos de extremidade e perfis.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Executar o programa

Agora podemos pode executar nosso programa Node usando nosso depurador favorito ou no console.

> [AZURE.TIP] Se você estiver usando o Visual Studio código como seu depurador, você precisará configurar seu ambiente para passar os parâmetros de linha de comando.  Código do Visual Studio faz isso no arquivo **lanuch.json** .  Procure por uma propriedade chamada **argumentos** e adicione uma matriz de valores de cadeia de caracteres para seus parâmetros, para que ele pareça semelhante a esta: `"args": ["list", "profiles"]`.

Vamos começar listando nossos perfis.

![Perfis de lista](./media/cdn-app-dev-node/cdn-list-profiles.png)

Obtivemos de volta uma matriz vazia.  Como não temos quaisquer perfis em nosso grupo de recursos, que é esperado.  Agora, vamos criar um perfil.

![Criar perfil](./media/cdn-app-dev-node/cdn-create-profile.png)

Agora, vamos adicionar um ponto de extremidade.

![Criar ponto de extremidade](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Por fim, vamos excluir nosso perfil.

![Excluir perfil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Próximas etapas

Para ver o projeto deste passo a passo, [Baixe o exemplo](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74)concluído.

Para ver a referência para o SDK do Azure CDN Node, exiba a [referência](http://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Para localizar documentação adicional sobre o SDK do Azure para Node, exiba a [referência completa](http://azure.github.io/azure-sdk-for-node/).

Gerencie seus recursos CDN com [PowerShell](./cdn-manage-powershell.md).

