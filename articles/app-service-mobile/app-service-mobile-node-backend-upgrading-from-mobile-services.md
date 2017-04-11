<properties
    pageTitle="Atualizar de serviços móveis para o serviço de aplicativo do Azure - Node"
    description="Saiba como atualizar facilmente o aplicativo de serviços de celular para um aplicativo do aplicativo de serviço móvel"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="node"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Atualize o seu serviço de celular de Azure Node existente para o serviço de aplicativo

Serviço de aplicativo Mobile é uma nova forma de criar aplicativos móveis usando o Microsoft Azure. Para saber mais, consulte [quais são os aplicativos Mobile?].

Este tópico descreve como atualizar um aplicativo de back-end node existente de serviços de celular do Azure para um novos aplicativos de celular de serviço de aplicativo. Enquanto você executa essa atualização, seu aplicativo de serviços Mobile existente pode continuar operando.  Se você precisar atualizar um aplicativo de back-end Node, consulte [Atualizando seus serviços de celular .NET](./app-service-mobile-net-upgrading-from-mobile-services.md).

Quando um back-end móvel é atualizado para o serviço de aplicativo do Azure, ele tem acesso a todos os recursos de serviço de aplicativo e é cobrado de acordo com [preços de serviço de aplicativos], não os serviços de celular preços.

## <a name="migrate-vs-upgrade"></a>Migrar versus atualização

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] É recomendável que você [executar uma migração](app-service-mobile-migrating-from-mobile-services.md) antes de passar por uma atualização. Dessa forma, você pode colocar as duas versões do seu aplicativo no mesmo plano de serviço de aplicativo e não provocam sem nenhum custo adicional.

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Melhorias no SDK do servidor Node de aplicativos móveis

Atualização para o novo [SDK de aplicativos Mobile](https://www.npmjs.com/package/azure-mobile-apps) fornece muitas melhorias, incluindo:

- Com base em de [Express framework](http://expressjs.com/en/index.html), o novo SDK de nó é simplificada e projetado para acompanhar novas versões de nó conforme elas vêm check-out. Você pode personalizar o comportamento do aplicativo com Express middleware.

- Melhorias de desempenho significativas em comparação com o SDK dos serviços móveis.

- Agora você pode hospedar um site junto com seu back-end móvel; da mesma forma, é fácil adicionar o SDK do Azure Mobile a qualquer aplicativo express.v4 existente.

- Projetado para desenvolvimento de várias plataformas e local, o SDK de aplicativos móveis podem ser desenvolvido e executado localmente em plataformas Windows, Linux e OSX. Agora é fácil de usar técnicas de desenvolvimento de nó comuns como executar testes de [grande](https://mochajs.org/) antes da implantação.

## <a name="overview"></a>Visão geral de atualização básica

Para auxiliar na atualização de um back-end Node, o serviço de aplicativo do Azure forneceu um pacote de compatibilidade.  Após a atualização, você terá um site niew que pode ser implantado para um novo site de serviço de aplicativo.

O cliente de serviços de celular SDKs são **não** é compatível com o novo servidor de aplicativos Mobile SDK. Para fornecer continuidade de serviço para seu aplicativo, você não deve publicar alterações em um site atualmente servir clientes publicados. Em vez disso, você deve criar um novo aplicativo móvel que serve como uma duplicata. Você pode colocar esse aplicativo no mesmo plano de serviço de aplicativo para evitar que sejam custo financeiro adicional.

Você terá duas versões do aplicativo: um que permanece o mesmo e serve publicado aplicativos aí e outra que você pode atualizar e de destino com uma nova versão de cliente. Você pode mover e testar seu código no seu ritmo, mas deverá garantir que as correções feitas obter aplicadas a ambas. Depois que você acha que um número desejado de cliente aplicativos à solta tem atualizado para a versão mais recente, é possível excluir o aplicativo migrado original se desejar. Ele não provoca custos monetários adicionais, se hospedado no mesmo plano de serviço de aplicativo como o seu aplicativo de celular.

A estrutura de tópicos inteira para o processo de atualização é a seguinte:

1. Baixe o serviço de Mobile Azure (migrados) existente.
2. Converta o projeto para um aplicativo móvel do Azure usando o pacote de compatibilidade.
3. Corrija as diferenças (como configurações de autenticação).
4. Implante o seu projeto de aplicativo do Azure Mobile convertido em um novo serviço de aplicativo.
4. Solte uma nova versão do seu aplicativo de cliente que usam o novo aplicativo de celular.
5. (Opcional) Exclua seu aplicativo de serviço móvel migradas original.

Exclusão pode ocorrer quando você não vir qualquer tráfego na sua original serviço móvel migradas.

## <a name="install-npm-package"></a>Instalar os pré-requisitos

Você deve instalar [nó] em seu computador local.  Você também deve instalar o pacote de compatibilidade.  Após o nó estiver instalado, você pode executar o comando a seguir de uma nova cmd ou o prompt do PowerShell:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a>Obter seus Scripts de serviços móveis Azure

- Faça logon [Portal do Azure].
- Usar **Todos os recursos** ou **Serviços de aplicativo**, encontre o seu site de serviços de celular.
- Dentro do site, clique em **Ferramentas** -> **Kudu** -> **Ir** para abrir o site Kudu.
- Clique no **Console depuração** -> **PowerShell** para abrir o console de depuração.
- Navegue até `site/wwwroot/App_Data/config` clicando em cada diretório alternadamente
- Clique no ícone de download lado a `scripts` diretório.

Isso irá baixar scripts no formato ZIP.  Criar uma nova pasta no seu computador local e descompactar o `scripts.ZIP` arquivo dentro do diretório.  Isso criará uma `scripts` diretório.

## <a name="scaffold-app"></a>O novo back-end do Azure Mobile aplicativos Scaffold

Execute o seguinte comando do diretório que contém o diretório de scripts:

```scaffold-mobile-app scripts out```

Isso criará um back-end do Azure Mobile aplicativos scaffolded no `out` diretório.  Embora não seja necessário, é uma boa ideia verificar o `out` diretório em um repositório de código de origem de sua escolha.

## <a name="deploy-ama-app"></a>Implante seu back-end de aplicativos do Azure Mobile

Durante a implantação, você precisará fazer o seguinte:

1. Crie um novo aplicativo de celular no [Portal do Azure].
2. Executar o `createViews.sql` script no seu banco de dados conectado.
3. Vincule o banco de dados que esteja vinculado ao serviço de celular para seu novo serviço de aplicativo.
4. Link quaisquer outros recursos (como Hubs de notificação) para o novo aplicativo de serviço.
5. Implante o código gerado para seu novo site.

### <a name="create-a-new-mobile-app"></a>Criar um novo aplicativo móvel

1. Logon no [Portal do Azure].

2. Clique em **+ novo** > **Web + Mobile** > **Aplicativo móvel**, em seguida, forneça um nome para seu back-end do aplicativo Mobile.

3. Para o **Grupo de recursos**, selecione um grupo de recursos existente ou crie um novo (usando o mesmo nome como seu aplicativo). 
 
    Você pode selecionar outro plano de serviço do aplicativo ou criar um novo. Para saber mais sobre os serviços de aplicativo planos e como criar um novo plano em uma guia de preços diferentes nível e no seu local desejado, consulte [Visão geral detalhada de planos de serviço de aplicativo do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Para o **plano de serviço de aplicativo**, o plano de padrão (na [camada padrão](https://azure.microsoft.com/pricing/details/app-service/)) está selecionado. Você também pode selecionar um plano diferente ou [Crie um novo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Configurações do plano de serviço de aplicativo determinam o [local, recursos, custos e recursos de computação](https://azure.microsoft.com/pricing/details/app-service/) associado ao seu aplicativo. 

    Depois que decidir sobre o plano, clique em **criar**. Isso cria o back-end do aplicativo Mobile. 


### <a name="run-createviewssql"></a>Executar CreateViews.SQL

O aplicativo scaffolded contém um arquivo chamado `createViews.sql`.  Este script deve ser executado no banco de dados de destino.  A cadeia de conexão do banco de dados de destino pode ser obtida de seu serviço móvel migrado da lâmina **configurações** em **Cadeias de caracteres de Conexão**.  Ele é denominado `MS_TableConnectionString`.

Você pode executar esse script no SQL Server Management Studio ou Visual Studio.

### <a name="link-the-database-to-your-app-service"></a>Vincular o banco de dados ao seu serviço de aplicativo

Link do banco de dados existente para seu serviço de aplicativo:

- No [Portal do Azure], abra seu aplicativo de serviço.
- Selecione **todas as configurações** -> **conexões de dados**.
- Clique em **+ Add**.
- Na lista suspensa, selecione o **Banco de dados SQL**
- Em **Banco de dados SQL**, selecione seu banco de dados existente, clique em **Selecionar**.
- Em **cadeia de caracteres de Conexão**, insira o nome de usuário e a senha do banco de dados, e clique em **Okey**.
- Na lâmina **Adicionar conexões de dados** , clique em **Okey**.

O nome de usuário e senha podem ser encontradas exibindo a cadeia de Conexão do banco de dados de destino no seu serviço móvel migradas.


### <a name="set-up-authentication"></a>Configurar autenticação

Os aplicativos do Azure Mobile permite que você configure Azure Active Directory, Facebook, Google, Microsoft e Twitter autenticação dentro do serviço.  Autenticação personalizada precisará ser desenvolvida separadamente.  Consulte a documentação de [Conceitos de autenticação] e a documentação de [Início rápido de autenticação] para obter mais informações.  

## <a name="updating-clients"></a>Atualizar clientes móveis

Quando você tiver um operacionais back-end do aplicativo móvel, você pode trabalhar em uma nova versão do seu aplicativo de cliente que consome-lo. Aplicativos Mobile também inclui uma nova versão do cliente SDKs e semelhante a atualização do servidor acima, será necessário remover todas as referências a SDK de serviços do Mobile antes de instalar as versões de aplicativos Mobile.

Uma das principais alterações entre as versões é que os construtores não precisam mais uma tecla de aplicativo. Você agora basta passa a URL do seu aplicativo de celular. Por exemplo, nos clientes .NET, o `MobileServiceClient` construtor agora é:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Você pode ler sobre como instalar o SDK do novo e usando a nova estrutura via os links abaixo:

- [Android versão 2.2 ou posterior](app-service-mobile-android-how-to-use-client-library.md)
- [iOS versão 3.0.0 ou posterior](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) versão 2.0.0 ou posterior](app-service-mobile-dotnet-how-to-use-client-library.md)
- [Apache Cordova versão 2.0 ou posterior](app-service-mobile-cordova-how-to-use-client-library.md)

Se seu aplicativo torna usar notificações por push, anote as instruções de registro específico para cada plataforma, como Houve algumas alterações lá também.

Quando você tiver a nova versão do cliente pronta, experimente contra seu projeto de servidor atualizado. Após validar que ele funciona, você pode liberar uma nova versão do seu aplicativo para clientes. Por fim, depois que seus clientes tiveram a oportunidade de receber essas atualizações, você pode excluir a versão de serviços de celular do seu aplicativo. Neste ponto, você atualizou completamente um aplicativo de celular do serviço de aplicativo usando o servidor de aplicativos Mobile SDK mais recente.

<!-- URLs. -->

[Portal do Azure]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Quais são os aplicativos Mobile?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Preço de serviços de aplicativo]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Conceitos de autenticação]: ../app-service/app-service-authentication-overview.md
[Início rápido de autenticação]: app-service-mobile-auth.md

[Portal do Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
