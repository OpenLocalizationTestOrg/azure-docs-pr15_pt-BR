<properties
    pageTitle="Gerenciamento de registro"
    description="Este tópico explica como registrar dispositivos com hubs de notificação para receber notificações por push."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="registration-management"></a>Gerenciamento de registro

##<a name="overview"></a>Visão geral

Este tópico explica como registrar dispositivos com hubs de notificação para receber notificações por push. O tópico descreve os registros em um alto nível, em seguida, apresenta dois principais padrões para registrar dispositivos: registrar do dispositivo diretamente para o hub de notificação e registrando por meio de um back-end do aplicativo. 


##<a name="what-is-device-registration"></a>O que é o registro do dispositivo

Registro de dispositivos com um Hub de notificação é realizado usando um **registro** ou **instalação**.

#### <a name="registrations"></a>Registros
Um registro associa a alça de serviço de notificação de plataforma (PNS) para um dispositivo com marcas e, possivelmente, um modelo. A alça de PNS pode ser uma ChannelURI, o token de dispositivo ou a identificação de registro do GCM. Marcas são usadas para rotear notificações para o conjunto correto de alças de dispositivo. Para obter mais informações, consulte [Roteamento e expressões de marca](notification-hubs-tags-segment-push-message.md). Modelos são usados para implementar o transformação por registro. Para obter mais informações, consulte [modelos](notification-hubs-templates-cross-platform-push-messages.md).

#### <a name="installations"></a>Instalações
Uma instalação é um aprimorado propriedades relacionadas de registro que inclui um conjunto de envio. É a abordagem melhor e mais recente para registrar seus dispositivos. No entanto, ele não é suportado pelo lado cliente .NET SDK ([SDK do Hub de notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) ainda.  Isso significa que se você estiver registrando do próprio dispositivo cliente, você teria usar a abordagem de [Notificação Hubs REST API](https://msdn.microsoft.com/library/mt621153.aspx) para instalações de suporte. Se você estiver usando um serviço de back-end, você deve ser capaz de usar o [SDK do Hub de notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Estes são alguns principais vantagens usando instalações:

* Criar ou atualizar uma instalação é totalmente idempotente. Portanto, você pode repetir-sem dúvidas sobre registros duplicados.
* O modelo de instalação torna mais fácil fazer individuais coloca - direcionamento dispositivo específico. Uma marca de sistema **"$InstallationId: [installationId]"** é adicionado automaticamente com cada registro de instalação com base. Portanto, você pode chamar um enviar a essa marca para direcionar um dispositivo específico sem precisar fazer qualquer codificação adicionais.
* Usar instalações também permite fazer atualizações de registro parcial. A atualização parcial de uma instalação é solicitada com um método de PATCH usando o [padrão de Patch de JSON](https://tools.ietf.org/html/rfc6902). Isso é especialmente útil quando você deseja atualizar marcas no registro. Você não precisa suspenso o registro inteiro e, em seguida, reenviar todas as marcas anterior novamente.

Uma instalação pode conter as seguintes propriedades. Para uma listagem completa da instalação propriedades, consulte [criar ou substituir uma instalação com a API REST](https://msdn.microsoft.com/library/azure/mt621153.aspx) ou [Propriedades de instalação](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx) para o.

    // Example installation format to show some supported properties
    {
        installationId: "",
        expirationTime: "",
        tags: [],
        platform: "",
        pushChannel: "",
        ………
        templates: {
            "templateName1" : {
                body: "",
                tags: [] },
            "templateName2" : {
                body: "",
                // Headers are for Windows Store only
                headers: {
                    "X-WNS-Type": "wns/tile" }
                tags: [] }
        },
        secondaryTiles: {
            "tileId1": {
                pushChannel: "",
                tags: [],
                templates: {
                    "otherTemplate": {
                        bodyTemplate: "",
                        headers: {
                            ... }
                        tags: [] }
                }
            }
        }
    }

 

É importante observar que instalações por padrão e registros não está mais expirarem.

Registros e instalações devem conter um identificador PNS válido para cada dispositivo/canal. Como as alças PNS só podem ser obtidas em um aplicativo cliente do dispositivo, um padrão é registrar diretamente no dispositivo com o aplicativo cliente. Por outro lado, considerações de segurança e lógica de negócios relacionados a marcas podem exigir que você gerencie registro de dispositivo no back-end do aplicativo. 

#### <a name="templates"></a>Modelos

Se você quiser usar [modelos](notification-hubs-templates-cross-platform-push-messages.md), a instalação do dispositivo também manter todos os modelos associados a esse dispositivo em um JSON formato (veja exemplo acima). Os nomes de modelo ajudam a diferentes modelos de destino para o mesmo dispositivo.

Observe que cada nome de modelo mapeado para o corpo de um modelo e um conjunto opcional de marcas. Além disso, cada plataforma pode ter propriedades de modelo adicionais. Para Windows Store (usando WNS) e Windows Phone 8 (usando MPNS), um conjunto adicional de cabeçalhos pode ser parte do modelo. No caso de APNs, você pode definir uma propriedade de expiração para uma constante ou uma expressão de modelo. Para uma listagem completa da instalação propriedades, consulte [criar ou substituir uma instalação com restante](https://msdn.microsoft.com/library/azure/mt621153.aspx) tópico.

#### <a name="secondary-tiles-for-windows-store-apps"></a>Blocos secundários para aplicativos da Windows Store

Para aplicativos de cliente da Windows Store, enviando notificações para blocos secundários é a mesma que enviá-las a principal. Isso também é suportado em instalações. Observe que quadros secundários têm um ChannelUri diferente, que o SDK em seu aplicativo cliente manipula transparente.

O dicionário de SecondaryTiles usa a mesma TileId que é usado para criar o objeto SecondaryTiles em seu aplicativo da Windows Store.
Como com a ChannelUri principal, ChannelUris dos blocos secundários pode alterar a qualquer momento. Para manter as instalações no hub do notificação atualizado, o dispositivo deve atualizá-los com o ChannelUris atual dos blocos de secundário.


##<a name="registration-management-from-the-device"></a>Gerenciamento de registro do dispositivo

Ao gerenciar o registro de dispositivos de aplicativos cliente, back-end só é responsável por enviar notificações. Aplicativos cliente manter PNS alças atualizados e registrar marcas. A figura a seguir ilustra esse padrão.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

O dispositivo primeiro recupera a alça de PNS dos PNS, em seguida, registra diretamente com o hub de notificação. Depois que o registro é bem sucedido, o back-end do aplicativo pode enviar uma notificação direcionamento esse registro. Para obter mais informações sobre como enviar notificações, consulte [Roteamento e expressões de marca](notification-hubs-tags-segment-push-message.md).
Observe que, nesse caso, você usará apenas ouvir direitos para acessar seu hubs de notificação do dispositivo. Para obter mais informações, consulte [segurança](notification-hubs-push-notification-security.md).

Registro do dispositivo é o método mais simples, mas ele apresenta algumas desvantagens.
A primeira desvantagem é que um aplicativo cliente só pode atualizar suas marcas quando o aplicativo está ativo. Por exemplo, se um usuário tiver dois dispositivos que registrar marcas relacionadas a equipes de esporte, quando o primeiro dispositivo registra para uma marca adicional (por exemplo, Seahawks), o segundo dispositivo não receberá as notificações sobre o Seahawks até que o aplicativo no segundo dispositivo seja executado uma segunda vez. Mais geralmente, quando marcas são afetadas por vários dispositivos, Gerenciando marcas de back-end é uma opção desejável.
A segunda desvantagem de gerenciamento de registro do aplicativo cliente é que, desde que podem ser quebrados aplicativos, proteger o registro de marcas específicas requer cuidado extra, conforme explicado na seção "segurança em nível de marca".



#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Código de exemplo para registrar com um hub de notificação de um dispositivo usando uma instalação 

No momento, isso é suportado apenas usando a [API de REST de Hubs de notificação](https://msdn.microsoft.com/library/mt621153.aspx).

Você também pode usar o método de PATCHES usando o [padrão de Patch JSON](https://tools.ietf.org/html/rfc6902) para atualizar a instalação.

    class DeviceInstallation
    {
        public string installationId { get; set; }
        public string platform { get; set; }
        public string pushChannel { get; set; }
        public string[] tags { get; set; }
    }

    private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
         string hubName, string listenConnectionString)
    {
        if (deviceInstallation.installationId == null)
            return HttpStatusCode.BadRequest;

        // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
        ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
        string hubResource = "installations/" + deviceInstallation.installationId + "?";
        string apiVersion = "api-version=2015-04";

        // Determine the targetUri that we will sign
        string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

        //=== Generate SaS Security Token for Authorization header ===
        // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
        string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

        using (var httpClient = new HttpClient())
        {
            string json = JsonConvert.SerializeObject(deviceInstallation);

            httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

            var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    string installationId = null;
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a installation id in application data, create and store as application data.
    if (!settings.ContainsKey("__NHInstallationId"))
    {
        installationId = Guid.NewGuid().ToString();
        settings.Add("__NHInstallationId", installationId);
    }

    installationId = (string)settings["__NHInstallationId"];

    var deviceInstallation = new DeviceInstallation
    {
        installationId = installationId,
        platform = "wns",
        pushChannel = channel.Uri,
        //tags = tags.ToArray<string>()
    };

    var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                        "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

    if (statusCode != HttpStatusCode.Accepted)
    {
        var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    else
    {
        var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }

   

#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Código de exemplo para registrar com um hub de notificação de um dispositivo usando um registro


Esses métodos criar ou atualizar um registro para o dispositivo em que eles são chamados. Isso significa que, para atualizar a alça ou as marcas, você deve substituir o registro inteiro. Lembre-se de que os registros são temporárias, portanto você deve sempre ter um armazenamento confiável com as marcas atuais que precisa de um dispositivo específico.


    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // The Device id from the PNS
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // If you are registering from the client itself, then store this registration id in device
    // storage. Then when the app starts, you can check if a registration id already exists or not before
    // creating.
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a registration id in application data, store in application data.
    if (!settings.ContainsKey("__NHRegistrationId"))
    {
        // make sure there are no existing registrations for this push handle (used for iOS and Android)    
        string newRegistrationId = null;
        var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
        foreach (RegistrationDescription registration in registrations)
        {
            if (newRegistrationId == null)
            {
                newRegistrationId = registration.RegistrationId;
            }
            else
            {
                await hub.DeleteRegistrationAsync(registration);
            }
        }

        newRegistrationId = await hub.CreateRegistrationIdAsync();

        settings.Add("__NHRegistrationId", newRegistrationId);
    }
     
    string regId = (string)settings["__NHRegistrationId"];

    RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
    registration.RegistrationId = regId;
    registration.Tags = new HashSet<string>(YourTags);

    try
    {
        await hub.CreateOrUpdateRegistrationAsync(registration);
    }
    catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
    {
        settings.Remove("__NHRegistrationId");
    }


## <a name="registration-management-from-a-backend"></a>Gerenciamento de registro de um back-end

O gerenciamento de registros de back-end requer escrever código adicional. O aplicativo do dispositivo deve fornecer as PNS atualizados tratar no back-end toda vez que o aplicativo é iniciado (juntamente com marcas e modelos) e back-end deve atualizar esta alça no hub notificação. A figura a seguir ilustra este design.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

As vantagens de gerenciamento de registros de back-end incluem a capacidade para modificar marcas aos registros, mesmo quando o aplicativo correspondente do dispositivo está inativo e para autenticar o aplicativo cliente antes de adicionar uma marca ao seu registro.


#### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Código de exemplo para registrar com um hub de notificação de um back-end usando uma instalação

O dispositivo cliente ainda obtém sua alça de PNS e propriedades de instalação relevante como antes e chama uma API personalizada back-end que pode executar o registro e autorizar marcas etc. O back-end pode aproveitar o [SDK do Hub de notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Você também pode usar o método de PATCHES usando o [padrão de Patch JSON](https://tools.ietf.org/html/rfc6902) para atualizar a instalação.
 

    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // Custom API on the backend
    public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
    {

        Installation installation = new Installation();
        installation.InstallationId = deviceUpdate.InstallationId;
        installation.PushChannel = deviceUpdate.Handle;
        installation.Tags = deviceUpdate.Tags;

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                installation.Platform = NotificationPlatform.Mpns;
                break;
            case "wns":
                installation.Platform = NotificationPlatform.Wns;
                break;
            case "apns":
                installation.Platform = NotificationPlatform.Apns;
                break;
            case "gcm":
                installation.Platform = NotificationPlatform.Gcm;
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }


        // In the backend we can control if a user is allowed to add tags
        //installation.Tags = new List<string>(deviceUpdate.Tags);
        //installation.Tags.Add("username:" + username);

        await hub.CreateOrUpdateInstallationAsync(installation);

        return Request.CreateResponse(HttpStatusCode.OK);
    }


#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Código de exemplo para registrar com um hub de notificação de um dispositivo usando uma identificação de registro

Do seu back-end do aplicativo, você pode executar operações básicas de CRUDS nos registros. Por exemplo:

    var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");
            
    // create a registration description object of the correct type, e.g.
    var reg = new WindowsRegistrationDescription(channelUri, tags);

    // Create
    await hub.CreateRegistrationAsync(reg);

    // Get by id
    var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

    // update
    r.Tags.Add("myTag");

    // update on hub
    await hub.UpdateRegistrationAsync(r);

    // delete
    await hub.DeleteRegistrationAsync(r);


O back-end deve tratar concorrência entre as atualizações de registro. Barramento de serviço oferece controle de concorrência otimista para gerenciamento de registro. No nível de HTTP, isso é implementado com o uso de ETag em operações de gerenciamento de registro. Este recurso transparente é usado pelo Microsoft SDKs, qual lançar uma exceção se uma atualização for rejeitada por razões de concorrência. O back-end do aplicativo é responsável por lidar com essas exceções e repetindo a atualização, se necessário.