<properties
    pageTitle="Atualizar de serviços móveis para o serviço de aplicativo do Azure"
    description="Saiba como atualizar facilmente o aplicativo de serviços de celular para um aplicativo do aplicativo de serviço móvel"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Atualize o seu serviço de celular de Azure .NET existentes ao aplicativo de serviço

Serviço de aplicativo Mobile é uma nova forma de criar aplicativos móveis usando o Microsoft Azure. Para saber mais, consulte [quais são os aplicativos Mobile?].

Este tópico descreve como atualizar um aplicativo de back-end .NET existente de serviços de celular do Azure para um novos aplicativos de celular de serviço de aplicativo. Enquanto você executa essa atualização, seu aplicativo de serviços Mobile existente pode continuar operando.   Se você precisar atualizar um aplicativo de back-end Node, consulte [Atualizando seus serviços de celular Node](./app-service-mobile-node-backend-upgrading-from-mobile-services.md).

Quando um back-end móvel é atualizado para o serviço de aplicativo do Azure, ele tem acesso a todos os recursos de serviço de aplicativo e é cobrado de acordo com [preços de serviço de aplicativos], não os serviços de celular preços.

##<a name="migrate-vs-upgrade"></a>Migrar versus atualização

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] É recomendável que você [executar uma migração](app-service-mobile-migrating-from-mobile-services.md) antes de passar por uma atualização. Dessa forma, você pode colocar as duas versões do seu aplicativo no mesmo plano de serviço de aplicativo e não provocam sem nenhum custo adicional.

###<a name="improvements-in-mobile-apps-net-server-sdk"></a>Melhorias no .NET de aplicativos móveis server SDK

Atualização para o novo [SDK de aplicativos Mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) fornece os seguintes benefícios:

- Mais flexibilidade sobre dependências NuGet. O ambiente de hospedagem não fornece mais suas próprias versões dos pacotes NuGet, para que você possa usar alternativas versões compatíveis. No entanto, se houver novos bugfixes críticas ou atualizações de segurança para o SDK do servidor Mobile ou dependências, você deve atualizar seu serviço manualmente.

- Mais flexibilidade no SDK do celular. Você pode controlar explicitamente quais recursos e rotas estão configuradas, como autenticação, tabela APIs e o ponto de extremidade do registro de envio. Para saber mais, veja [como usar o servidor de .NET SDK para aplicativos do Azure móvel](app-service-mobile-net-upgrading-from-mobile-services.md#server-project-setup).

- Suporte para outros tipos de projeto do ASP.NET e rotas. Agora você pode hospedar controladores MVC e API da Web no mesmo projeto como seu projeto de back-end móvel.

- Suporte para novos recursos de autenticação de serviço de aplicativo, que permitem que você use uma configuração de autenticação comum por meio da web e aplicativos móveis.

##<a name="overview"></a>Visão geral de atualização básica

Em muitos casos, atualizando será tão simple quanto migrando para o novo servidor de aplicativos Mobile SDK e republicar seu código em uma nova instância do aplicativo Mobile. Existem, porém alguns cenários que exigem alguma configuração adicional, como cenários de autenticação avançada e trabalhar com agendada trabalhos. Cada um desses é coberta nas seções posteriores.

>[AZURE.TIP] É recomendável que você ler e entender o restante deste tópico completamente antes de iniciar uma atualização. Tome nota dos recursos que você usa o que são destacados abaixo.

O cliente de serviços de celular SDKs são **não** é compatível com o novo servidor de aplicativos Mobile SDK. Para fornecer continuidade de serviço para seu aplicativo, você não deve publicar alterações em um site atualmente servir clientes publicados. Em vez disso, você deve criar um novo aplicativo móvel que serve como uma duplicata. Você pode colocar esse aplicativo no mesmo plano de serviço de aplicativo para evitar que sejam custo financeiro adicional.

Você terá duas versões do aplicativo: que permanece o mesmo e serve publicado aplicativos aí e outra que você pode atualizar e de destino com uma nova versão de cliente. Você pode mover e testar seu código no seu ritmo, mas deverá garantir que as correções feitas obter aplicadas a ambas. Depois que você acha que um número desejado de cliente aplicativos à solta tem atualizado para a versão mais recente, é possível excluir o aplicativo migrado original se desejar.

A estrutura de tópicos inteira para o processo de atualização é a seguinte:

1. Criar um novo aplicativo móvel
2. Atualizar o projeto para usar o SDK do servidor novo
3. Solte uma nova versão do seu aplicativo cliente
4. (Opcional) Excluir sua instância migrada original

##<a name="mobile-app-version"></a>Criando uma segunda instância do aplicativo
A primeira etapa na atualização é criar o recurso de aplicativo móvel que hospedará a nova versão do seu aplicativo. Se você já tenha migrado um serviço móvel existente, você desejará criar esta versão no mesmo plano de hospedagem. Abra o [portal do Azure] e navegue até seu aplicativo migrado. Anote o plano de serviço de aplicativo ele é executado em.

Em seguida, crie a segunda instância de aplicativo seguindo as [instruções de criação de back-end do .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Quando solicitado a selecionar você plano de serviço do aplicativo ou "plano de hospedagem" escolher o plano do seu aplicativo migrado.

Você provavelmente desejará usar o mesmo banco de dados e o Hub de notificação que você fez nos serviços do Mobile. Você pode copiar esses valores abrindo o [portal do Azure] e navegue para o aplicativo original, e clique em **configurações** > **configurações do aplicativo**. Em **Cadeias de caracteres de Conexão**, copie `MS_NotificationHubConnectionString` e `MS_TableConnectionString`. Navegue até seu novo site de atualização e cole-as no, substituindo quaisquer valores existentes. Repita esse processo para quaisquer outras configurações de aplicativo suas necessidades de aplicativo. Se não usando um serviço migradas, você pode ler cadeias de caracteres de conexão e configurações do aplicativo na guia **Configurar** da seção de serviços de celular do [Azure portal clássico].

Fazer uma cópia do projeto ASP.NET para seu aplicativo e publicá-lo para seu novo site. Usando uma cópia de seu aplicativo cliente atualizado com a nova URL, valide que tudo funciona como esperado.

## <a name="updating-the-server-project"></a>Atualizando o projeto de servidor

Aplicativos Mobile fornece um novo [SDK de servidor de aplicativo Mobile] que proporciona a mesma funcionalidade que o tempo de execução de serviços de celular. Primeiro, você deve remover todas as referências para os pacotes de serviços de celular. No Gerenciador de pacote de NuGet, procure por `WindowsAzure.MobileServices.Backend`. A maioria dos aplicativos verão vários pacotes aqui, incluindo `WindowsAzure.MobileServices.Backend.Tables` e `WindowsAzure.MobileServices.Backend.Entity`. Nesse caso, comece com o pacote mais baixo na árvore de dependência, tais como `Entity`e removê-lo. Quando solicitado, não remova todos os pacotes de dependentes. Repita esse processo até ter removido `WindowsAzure.MobileServices.Backend` em si.

Neste ponto, você terá um projeto que não está mais referências SDK de serviços do Mobile.

Em seguida, você adicionará referências o SDK de aplicativos móveis. Para essa atualização, a maioria dos desenvolvedores desejará Baixe e instale o `Microsoft.Azure.Mobile.Server.Quickstart` pacote, como isso busca todo o conjunto de necessário.

Haverá alguns erros de compilador resultantes de diferenças entre o SDK do, mas elas são fáceis de endereço e estão cobertas no restante desta seção.

### <a name="base-configuration"></a>Configuração básica

Em seguida, em WebApiConfig.cs, você pode substituir:

        // Use this class to set configuration options for your mobile service
        ConfigOptions options = new ConfigOptions();

        // Use this class to set WebAPI configuration options
        HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

com

        HttpConfiguration config = new HttpConfiguration();
        new MobileAppConfiguration()
            .UseDefaultConfiguration()
        .ApplyTo(config);

>[AZURE.NOTE] Se você quiser saber mais sobre o novo servidor .NET SDK e como adicionar ou remover recursos de seu aplicativo, consulte o tópico de [como usar o SDK do .NET server] .

Se seu aplicativo torna usar os recursos de autenticação, você também precisará registrar um middleware OWIN. Nesse caso, você deve mover o código de configuração acima em uma nova classe de inicialização de OWIN.

1. Adicione o pacote do NuGet `Microsoft.Owin.Host.SystemWeb` se ela já não está incluída em seu projeto.
2. No Visual Studio, clique com o botão direito no seu projeto e selecione **Adicionar** -> **Novo Item**. Selecione **Web** -> **Geral** -> **classe OWIN inicialização**.
3. Mover o código acima para MobileAppConfiguration de `WebApiConfig.Register()` para o `Configuration()` método de sua nova classe de inicialização.

Verifique se o `Configuration()` método termina com:

        app.UseWebApi(config)
        app.UseAppServiceAuthentication(config);

Existem outras alterações relacionadas à autenticação descritas na seção autenticação completa abaixo.

### <a name="working-with-data"></a>Trabalhando com dados

Em serviços de celular, o nome do aplicativo móvel serviu como o nome do esquema padrão na configuração do Framework de entidade.

Para garantir que você tem o mesmo esquema que está sendo referenciado como antes, use o seguinte para definir o esquema na DbContext para seu aplicativo:

        string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");

Verifique se que você tiver MS_MobileServiceName definir se você não fizer acima. Você também pode fornecer outro nome de esquema se seu aplicativo personalizado isso anteriormente.

### <a name="system-properties"></a>Propriedades do sistema

#### <a name="naming"></a>Nomeando

No servidor do Azure Mobile Services SDK, propriedades do sistema sempre contenham um sublinhado duplo (`__`) prefixo para as propriedades:

- __createdAt
- __updatedAt
- __deleted
- __version

O cliente de serviços de celular SDKs tem uma lógica especial para análise de propriedades do sistema neste formato.

Em aplicativos do Azure Mobile, propriedades do sistema não tem um formato especial em tem os seguintes nomes:

- criadona
- updatedAt
- excluídos
- Versão

O cliente de aplicativos Mobile SDKs usar os novos nomes de propriedades do sistema, portanto, nenhuma alteração é necessárias para o código do cliente. No entanto, se você estiver criando diretamente chamadas REST seu serviço, em seguida, você deve alterar suas consultas adequadamente.

#### <a name="local-store"></a>Armazenamento local

As alterações aos nomes das propriedades do sistema significam que um banco de dados local de sincronização offline para serviços de celular não é compatível com aplicativos Mobile. Se possível, evite atualizar aplicativos de serviços de celular para aplicativos móveis até após alterações pendentes foram enviados para o servidor de cliente. Em seguida, o aplicativo atualizado deve usar um novo nome de arquivo de banco de dados.

Se um aplicativo cliente é atualizado de serviços de celular para aplicativos Mobile enquanto houver alterações offline pendentes na fila de operação, o banco de dados do sistema deverá ser atualizado para usar os novos nomes de coluna. No iOS, isso pode ser obtido usando migrações leves para alterar os nomes de coluna. No Android e o cliente gerenciado .NET, você deve escrever SQL personalizados para renomear as colunas para as tabelas de objeto de dados.

IOS, você deve alterar o esquema de dados de núcleo para suas entidades de dados coincidir com o seguinte. Observe que as propriedades `createdAt`, `updatedAt` e `version` não tem mais uma `ms_` prefixo:

| Atributo |  Tipo   | Observação                                                 |
|---------- |  ------ | -----------------------------------------------------|
| ID        | Cadeia de caracteres, marcadas como obrigatórias  | chave primária no repositório remoto         |
| criadona | Data    | mapas (opcionais) para a propriedade do sistema de criadona         |
| updatedAt | Data    | mapas (opcionais) para a propriedade do sistema de updatedAt         |
| Versão   | Cadeia de caracteres  | (opcional) usado para detectar conflitos, mapas à versão |

#### <a name="querying-system-properties"></a>Consultando propriedades do sistema

Em serviços de celular do Azure, propriedades do sistema não são enviadas por padrão, mas somente quando eles são solicitados usando a cadeia de consulta `__systemProperties`. Em contraste, no sistema de aplicativos do Azure Mobile propriedades são **sempre selecionado** como eles são parte do modelo de objeto do SDK do servidor.

Essa alteração afeta principalmente implementações personalizadas dos gerentes de domínio, como extensões de `MappedEntityDomainManager`. Em serviços de celular, se um cliente nunca solicita qualquer propriedades do sistema, é possível usar um `MappedEntityDomainManager` que realmente não mapear todas as propriedades. No entanto, em aplicativos do Azure Mobile, essas propriedades não mapeadas causará um erro em consultas de obter.

A maneira mais fácil para resolver o problema é modificar o seu DTOs para que eles herdam `ITableData` em vez de `EntityData`. Em seguida, adicione o `[NotMapped]` atributo para os campos que devem ser omitidos.

Por exemplo, a seguir define `TodoItem` sem propriedades do sistema:

    using System.ComponentModel.DataAnnotations.Schema;

    public class TodoItem : ITableData
    {
        public string Text { get; set; }

        public bool Complete { get; set; }

        public string Id { get; set; }

        [NotMapped]
        public DateTimeOffset? CreatedAt { get; set; }

        [NotMapped]
        public DateTimeOffset? UpdatedAt { get; set; }

        [NotMapped]
        public bool Deleted { get; set; }

        [NotMapped]
        public byte[] Version { get; set; }
    }

Observação: se você receber erros `NotMapped`, adicione uma referência ao assembly `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS

Serviços de celular incluídos algum suporte para CORS envolvendo a solução CORS ASP.NET. Essa camada de disposição foi removida para dar o desenvolvedor mais controle, para que você possa aproveitar diretamente [CORS ASP.NET oferece suporte](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

As principais áreas de interesse se usando CORS são que o `eTag` e `Location` cabeçalhos devem ser permitidos em ordem para o cliente SDKs funcione corretamente.

### <a name="push-notifications"></a>Notificações por push
Para envio, o item principal que você descobrirá ausente do SDK do servidor é a classe de PushRegistrationHandler. Registros são tratados de maneira um pouco diferente em aplicativos móveis e tagless registros estão ativados por padrão. Gerenciar marcas pode ser feito usando APIs personalizado. Consulte as instruções [Registrando marcas](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) para obter mais informações.

### <a name="scheduled-jobs"></a>Tarefas agendadas
Tarefas agendadas não são criadas em aplicativos móveis, para que todos os trabalhos existentes que você tem no seu back-end .NET deverá ser atualizado individualmente. Uma opção é criar um agendada [Trabalho Web] no site de código do aplicativo Mobile. Você também pode configurar um controlador que tem o código do seu trabalho e configurar o [Agendador do Azure] para acertar o ponto de extremidade na agenda esperado.

### <a name="miscellaneous-changes"></a>Alterações diversas
Todos os ApiControllers que seja consumidas por um cliente móvel agora deve ter o `[MobileAppController]` atributo. Isso não é mais incluídos por padrão assim que outros ApiControllers para ir afetados pelas formatadores móveis.

O `ApiServices` objeto não está mais parte do SDK. Para acessar as configurações de aplicativo móvel, você pode usar o seguinte:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

Da mesma forma, log agora é realizada usando a gravação de rastreamento ASP.NET padrão:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>Considerações sobre autenticação

Os componentes de autenticação dos serviços de celular agora foram movidos para o recurso de autenticação/autorização de serviço de aplicativo. Você pode aprender sobre habilitar essa opção para seu site leia o tópico [adicionar autenticação para o seu aplicativo móvel](app-service-mobile-ios-get-started-users.md) .

Para alguns provedores, como AAD, Facebook e Google, você deve ser capaz de aproveitar o registro existente de seu aplicativo de cópia. Basta navegue até portal do provedor de identidade e adicionar um novo URL de redirecionamento para o registro. Configure autenticação/autorização de serviço de aplicativo com o código do cliente e o segredo.

### <a name="controller-action-authorization"></a>Autorização de ação de controlador
Quaisquer instâncias do `[AuthorizeLevel(AuthorizationLevel.User)]` atributo agora deve ser alterado para usar o ASP.NET padrão `[Authorize]` atributo. Além disso, controladores são agora anônimo por padrão, como em outros aplicativos ASP.NET.
Se você estivesse usando uma das outras AuthorizeLevel opções, como o administrador ou o aplicativo, observe que esses são desaparece. Em vez disso, você pode configurar AuthorizationFilters para segredos compartilhados ou configurar um capital de serviço AAD para habilitar chamadas de serviço para com segurança.

### <a name="getting-additional-user-information"></a>Obtendo informações do usuário adicionais

Você pode obter informações adicionais de usuário, incluindo os tokens de acesso por meio do `GetAppServiceIdentityAsync()` método:

        FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();

Além disso, se seu aplicativo levar dependências em usuário IDs, como armazená-los em um banco de dados, é importante observar que os IDs de usuário entre os serviços de celular e aplicativos do aplicativo de serviço Mobile são diferentes. Você ainda pode obter a ID de usuário de serviços de celular, apesar. Todas as subclasses ProviderCredentials tem uma propriedade de ID de usuário. Portanto continuar do exemplo antes:

        string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Se seu aplicativo assumir quaisquer dependências IDs de usuário, é importante que você aproveite o mesmo registro com um provedor de identidade se possível. IDs de usuário geralmente têm como escopo para o registro de aplicativo que foi usado, portanto apresentando um novo registro poderia criar problemas com usuários aos seus dados correspondentes.

### <a name="custom-authentication"></a>Autenticação personalizada

Se seu aplicativo estiver usando uma solução de autenticação personalizada, você desejará certificar-se de que o site atualizado tenha acesso ao sistema. Siga as instruções de novo para autenticação personalizada na [Visão geral do .NET server SDK] integrar sua solução. Observe que os componentes de autenticação personalizada ainda estão no preview.

##<a name="updating-clients"></a>Atualizar clientes
Quando você tiver um operacionais back-end do aplicativo móvel, você pode trabalhar em uma nova versão do seu aplicativo de cliente que consome-lo. Aplicativos Mobile também inclui uma nova versão do cliente SDKs e semelhante a atualização do servidor acima, será necessário remover todas as referências a SDK de serviços do Mobile antes de instalar as versões de aplicativos Mobile.

Uma das principais alterações entre as versões é que os construtores não precisam mais uma tecla de aplicativo. Você agora basta passa a URL do seu aplicativo de celular. Por exemplo, nos clientes .NET, o `MobileServiceClient` construtor agora é:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Você pode ler sobre como instalar o SDK do novo e usando a nova estrutura via os links abaixo:

- [iOS versão 3.0.0 ou posterior](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) versão 2.0.0 ou posterior](app-service-mobile-dotnet-how-to-use-client-library.md)

Se seu aplicativo torna usar notificações por push, anote as instruções de registro específico para cada plataforma, como Houve algumas alterações lá também.

Quando você tiver a nova versão do cliente pronta, experimente contra seu projeto de servidor atualizado. Após validar que ele funciona, você pode liberar uma nova versão do seu aplicativo para clientes. Por fim, depois que seus clientes tiveram a oportunidade de receber essas atualizações, você pode excluir a versão de serviços de celular do seu aplicativo. Neste ponto, você atualizou completamente um aplicativo de celular do serviço de aplicativo usando o servidor de aplicativos Mobile SDK mais recente.

<!-- URLs. -->

[Portal do Azure]: https://portal.azure.com/
[Azure portal clássico]: https://manage.windowsazure.com/
[Quais são os aplicativos Mobile?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[SDK do servidor de aplicativo móvel]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Agendador do Azure]: /en-us/documentation/services/scheduler/
[Trabalho da Web]: ../app-service-web/websites-webjobs-resources.md
[Como usar o SDK do .NET server]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Preço de serviços de aplicativo]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Visão geral do .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
