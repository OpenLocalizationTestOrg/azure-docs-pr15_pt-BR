<properties
    pageTitle="Notificações de envio separado por localização geográfica com Hubs de notificação do Azure e dados espacial Bing | Microsoft Azure"
    description="Neste tutorial, você aprenderá a oferecer as notificações por push baseada no local com Hubs de notificação do Azure e dados espacial do Bing."
    services="notification-hubs"
    documentationCenter="windows"
    keywords="notificação de envio, notificação de envio"
    authors="dend"
    manager="yuaxu"
    editor="dend"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/31/2016"
    ms.author="dendeli"/>
    
# <a name="geo-fenced-push-notifications-with-azure-notification-hubs-and-bing-spatial-data"></a>Notificações de envio separado por localização geográfica com Hubs de notificação do Azure e dados espacial do Bing
 
 > [AZURE.NOTE] Para concluir este tutorial, você deve ter uma conta do Azure active. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02).

Neste tutorial, você aprenderá a fornecer as notificações por push baseada no local com Hubs de notificação do Azure e dados espacial do Bing, aproveitados de dentro de um aplicativo de plataforma Universal do Windows.

##<a name="prerequisites"></a>Pré-requisitos
Primeiramente, você precisa certificar-se de que você tem todos os softwares e serviços pré-requisitos:

* [Visual Studio 2015 atualização 1](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx) ou posterior ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409) fará também). 
* Versão mais recente do [SDK do Azure](https://azure.microsoft.com/downloads/). 
* [Conta do Centro de desenvolvimento do Bing Maps](https://www.bingmapsportal.com/) (você pode criar uma gratuitamente e associá-lo a sua conta da Microsoft). 

##<a name="getting-started"></a>Guia de Introdução

Vamos começar pela criação do projeto. No Visual Studio, inicie um novo projeto de tipo de **Aplicativo em branco (Windows Universal)**.

![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

Uma vez concluída a criação do projeto, você deve ter o equipamento para o aplicativo em si. Agora vamos configurar tudo para a infraestrutura de localização geográfica-instalação da cerca. Como vamos usar os serviços do Bing para isso, há um ponto de extremidade de API REST público que nos quadros de local específico de consulta permite:

    http://spatial.virtualearth.net/REST/v1/data/
    
Você precisará especificar os seguintes parâmetros para que ele funcione:

* **ID da fonte de dados** e o **Nome da fonte de dados** – em API do Bing Maps, fontes de dados contêm vários metadados bucketed, como locais e horário comercial de operação. Você pode ler mais sobre esses aqui. 
* **Nome de entidade** – a entidade que você deseja usar como um ponto de referência para a notificação. 
* **Chave de API do Bing Maps** – esta é a chave que você adquiriu anteriormente quando você criou a conta do Centro de desenvolvimento do Bing.
 
Vamos fazer uma análise aprofundada na configuração para cada um dos elementos acima.

##<a name="setting-up-the-data-source"></a>Configuração de fonte de dados

Você pode fazer isso no Centro de desenvolvimento de mapas do Bing. Basta clicar em **fontes de dados** na barra de navegação superior e selecione **Gerenciar fontes de dados**.

![](./media/notification-hubs-geofence/bing-maps-manage-data.png)

Se você não tiver trabalhado com API do Bing Maps antes, provavelmente não haverá qualquer fontes de dados presentes, basta você possa criar um novo clicando em dados e carregar uma fonte de dados. Certifique-se de que você preencha todos os campos obrigatórios:

![](./media/notification-hubs-geofence/bing-maps-create-data.png)

Você deve estar se perguntando – o que é o arquivo de dados e o que devo você estar carregando? Para os fins desse teste, podemos usar apenas a amostra baseada em barra vertical que quadros uma área da concientizá de são Francisco:

    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    
Os dados acima representam esta entidade:

![](./media/notification-hubs-geofence/bing-maps-geofence.png)

Simplesmente copiar e colar a cadeia de caracteres acima em um novo arquivo e salve-o como **NotificationHubsGeofence.pipe**e carregue-o no Centro de desenvolvimento do Bing.

>[AZURE.NOTE]Você pode ser solicitado para especificar uma nova chave para a **chave mestre** que é diferente da **Chave de consulta**. Basta criar uma nova chave por meio de painel e atualize a página de carregamento de fonte de dados.

Depois que você carregar o arquivo de dados, você precisará certificar-se de que você publica a fonte de dados. 

Vá para **Gerenciar fontes de dados**, assim como fizemos acima, encontrar sua fonte de dados na lista e clique em **Publicar** na coluna **ações** . Em um pouco, você deverá ver sua fonte de dados na guia **Fontes de dados publicadas** :

![](./media/notification-hubs-geofence/bing-maps-published-data.png)

Se você clicar em **Editar**, você poderá ver num relance locais que apresentamos nele:

![](./media/notification-hubs-geofence/bing-maps-data-details.png)

Neste ponto, o portal não mostram os limites para o geofence que criamos – tudo o que precisamos é uma confirmação de que o local especificado é nas proximidades à direita.

Agora você tem todos os requisitos da fonte de dados. Para obter os detalhes na URL de solicitação de chamada API, no Centro de desenvolvimento de mapas do Bing, clique em **fontes de dados** e selecione **Informações da fonte de dados**.

![](./media/notification-hubs-geofence/bing-maps-data-info.png)

A **Consulta URL** é o que estamos após aqui. Este é o ponto de extremidade em relação à qual podemos pode executar consultas para verificar se o dispositivo está dentro dos limites de um local ou não. Para executar essa verificação, podemos simplesmente precisa executar uma chamada GET em relação a URL de consulta, com os seguintes parâmetros acrescentados:

    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY

Dessa forma que você estiver especificando um ponto de destino que podemos obter do dispositivo e do Bing Maps executará automaticamente os cálculos para ver se ele está dentro do geofence. Depois que você executa a solicitação por meio de um navegador (ou ondulação), você obterá padrão resposta JSON:

![](./media/notification-hubs-geofence/bing-maps-json.png)

Esta resposta só acontece quando o ponto realmente está dentro dos limites designados. Se não for, você obterá uma classificação vazios **resultados** :

![](./media/notification-hubs-geofence/bing-maps-nores.png)

##<a name="setting-up-the-uwp-application"></a>Configurando o aplicativo de UWP

Agora que temos a fonte de dados pronta, podemos começar a trabalhar no aplicativo UWP que estamos inicializar anteriormente.

Primeiramente, podemos deve habilitar serviços de localização para nosso aplicativo. Para fazer isso, clique duas vezes em `Package.appxmanifest` arquivo no **Solution Explorer**.

![](./media/notification-hubs-geofence/vs-package-manifest.png)

Na guia do pacote propriedades que acabou de abrir, clique em **recursos** e certifique-se de que você selecione **local**:

![](./media/notification-hubs-geofence/vs-package-location.png)

Como o recurso de localização é declarado, crie uma nova pasta na solução denominada `Core`e adicione um novo arquivo dentro dela, chamado `LocationHelper.cs`:

![](./media/notification-hubs-geofence/vs-location-helper.png)

O `LocationHelper` própria classe é bastante básica neste ponto – tudo o que ela faz é permitir conosco obter o local do usuário por meio de API do sistema:

    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }

Você pode ler mais sobre como obter o local do usuário nos aplicativos UWP no [documento do MSDN](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx)oficial.

Para verificar que a aquisição de local estiver realmente funcionando, abra o lado de código da sua página principal (`MainPage.xaml.cs`). Criar um novo manipulador de eventos para o `Loaded` evento no `MainPage` construtor:

    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }

A implementação do manipulador de eventos é da seguinte maneira:

    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }

Observe que podemos declarado o manipulador como assíncronas porque `GetCurrentLocation` é awaitable e, portanto, requer a ser executado em um contexto assíncrono. Além disso, como em determinadas circunstâncias nós pode terminar com um local nulo (por exemplo, o local de serviços estão desabilitados ou o aplicativo foi negado permissões para acesso local), precisamos certificar-se de que ele é tratado corretamente com uma verificação de nula.

Execute o aplicativo. Verifique se que você permitir acesso local:

![](./media/notification-hubs-geofence/notification-hubs-location-access.png)

Uma vez os inícios de aplicativos, você deve ser capaz de ver as coordenadas na janela de **saída** :

![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

Agora você sabe que funciona de aquisição do local – fique à vontade para remover o manipulador de eventos de teste para carregado porque nós não ser usá-lo mais.

A próxima etapa é capturar as alterações de localização. Para isso, vamos voltar para o `LocationHelper` classe e adicionar manipulador de eventos para `PositionChanged`:

    geolocator.PositionChanged += Geolocator_PositionChanged;

A implementação mostrará as coordenadas de local na janela de **saída** :

    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }

##<a name="setting-up-the-backend"></a>Configuração de back-end

Baixe o [Exemplo de back-end do .NET do GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). Quando o download for concluído, abra a `NotifyUsers` pasta e subsequentemente – o `NotifyUsers.sln` arquivo.

Definir o `AppBackend` projeto como o **Projeto de inicialização** e iniciá-lo.

![](./media/notification-hubs-geofence/vs-startup-project.png)

O projeto já está configurado para enviar notificações por push para dispositivos de destino, portanto, precisamos fazer somente duas coisas – trocar a conexão à direita de cadeia de caracteres para o hub de notificação e adicionar identificação de limite para enviar a notificação somente quando o usuário está dentro do geofence.

Para configurar a cadeia de conexão, o `Models` pasta aberta `Notifications.cs`. O `NotificationHubClient.CreateClientFromConnectionString` função deve conter as informações sobre seu hub de notificação que você pode obter no [Portal do Azure](https://portal.azure.com) (procure dentro a lâmina de **Políticas de acesso** nas **configurações**). Salve o arquivo de configuração atualizados.

Agora, precisamos criar um modelo para o resultado de API do Bing Maps. A maneira mais fácil de fazer isso é com o botão direito no `Models` pasta, **Adicionar** > **classe**. Nomeie- `GeofenceBoundary.cs`. Depois, copie o JSON da resposta de API que discutimos na primeira seção e em uso de Visual Studio **Editar** > **Colar especial** > **JSON Colar como Classes**. 

Dessa maneira podemos garantir que o objeto será desserializado exatamente como ela foi criada. Seu conjunto de classe resultante deve ser semelhante a esta:

    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }

Em seguida, abra `Controllers`  >  `NotificationsController.cs`. Precisamos ajustar a chamada de postagem para a conta para a latitude e longitude de destino. Para isso, basta adicionar duas cadeias de caracteres para a assinatura de função – `latitude` e `longitude`.

    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)

Criar uma nova classe dentro do projeto chamado `ApiHelper.cs` – usaremos para se conectar ao Bing verificar aponte interseções de limite. Implementar um `IsPointWithinBounds` função, assim:

    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }

>[AZURE.NOTE] Certifique-se de substituir o ponto de extremidade de API com a URL de consulta que você anteriormente obtida do Centro de desenvolvimento do Bing (mesmo se aplica à chave API). 

Se houver resultados da consulta, que significa que o ponto especificado dentro dos limites de geofence, para nós retornar `true`. Se não houver nenhum resultado, Bing é nos dizendo que o ponto está fora do quadro de pesquisa, para nós retornar `false`.

De volta ao `NotificationsController.cs`, crie uma verificação direita antes da instrução switch:

    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }

Dessa forma, a notificação é enviada apenas quando o ponto está dentro dos limites.

##<a name="testing-push-notifications-in-the-uwp-app"></a>Teste as notificações por push no aplicativo UWP

Voltar ao aplicativo UWP, podemos agora deve conseguir testar as notificações. Dentro do `LocationHelper` classe, crie uma nova função – `SendLocationToBackend`:

    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }

>[AZURE.NOTE] Troque o `POST_URL` até o local do seu aplicativo web implantado que criamos na seção anterior. Por agora, é Okey para executá-la localmente, mas enquanto você trabalha sobre como implantar uma versão pública, você precisará hospedá-lo com um provedor externo.

Vamos agora Certifique-se de que estamos registre o aplicativo UWP para notificações por push. No Visual Studio, clique em **projeto** > **armazenar** > **associar o aplicativo com o armazenamento**.

![](./media/notification-hubs-geofence/vs-associate-with-store.png)

Assim que você entrar sua conta de desenvolvedor, certifique-se de selecionar um aplicativo existente ou crie um novo e associar o pacote ele. 

Vá até o Centro de desenvolvimento e abra o aplicativo que você acabou de criar. Clique em **Serviços** > **Notificações Push** > **site Live Services**.

![](./media/notification-hubs-geofence/ms-live-services.png)

No site, anote o **Segredo do aplicativo** e o **SID de pacote**. Você precisará tanto no Portal do Azure – abra o seu hub de notificação, clique em **configurações** > **Serviços de notificação** > **Windows (WNS)** e insira as informações nos campos obrigatórios.

![](./media/notification-hubs-geofence/notification-hubs-wns.png)

Clique em **Salvar**.

Clique com o botão direito nas **referências** no **Solution Explorer** e selecione **Gerenciar pacotes do NuGet**. Será necessário adicionar uma referência para o **barramento de serviço do Microsoft Azure gerenciado biblioteca** – basta procurar `WindowsAzure.Messaging.Managed` e adicioná-la ao seu projeto.

![](./media/notification-hubs-geofence/vs-nuget.png)

Para fins de teste, podemos criar o `MainPage_Loaded` novamente, manipulador de eventos e adicione este trecho de código para ele:

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }

Acima registra o aplicativo com o hub de notificação. Você está pronto para começar! 

Em `LocationHelper`, dentro do `Geolocator_PositionChanged` manipulador, você pode adicionar uma parte do código de teste que forçada colocará a localização dentro do geofence:

    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");

Porque não está passando as coordenadas reais (que podem não estar dentro dos limites no momento) e estiver usando valores de teste predefinida, veremos uma notificação aparecem no atualização:

![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

##<a name="whats-next"></a>O que vem a seguir?

Existem algumas etapas que talvez você precise acompanhar além acima para certificar-se de que a solução está pronto para produção.

Primeiramente, talvez seja necessário garantir que geofences sejam dinâmico. Isso exigirá algum trabalho extra com a API do Bing para poder carregar novos limites dentro da fonte de dados existente. Consulte a [documentação da API do Bing espacial Data Services](https://msdn.microsoft.com/library/ff701734.aspx) para obter mais detalhes sobre o assunto.

Segundo, enquanto você está trabalhando para garantir que a entrega é feita para os participantes certos, talvez você queira apontá-los por meio de [marcação](notification-hubs-tags-segment-push-message.md).

A solução mostrada acima descreve um cenário em que você pode ter uma ampla variedade de plataformas de destino, para que não podemos limitar o geofencing para recursos específicos do sistema. Dito isso, a plataforma Windows Universal oferece recursos para [detectar geofences direita - de-prontos](https://msdn.microsoft.com/windows/uwp/maps-and-location/set-up-a-geofence).

Para obter mais detalhes sobre os recursos de Hubs de notificação, Confira nosso [portal da documentação](https://azure.microsoft.com/documentation/services/notification-hubs/).
