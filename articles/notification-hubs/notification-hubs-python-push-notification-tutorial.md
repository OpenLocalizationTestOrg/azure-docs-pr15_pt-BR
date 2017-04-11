<properties 
    pageTitle="Como usar Hubs de notificação com Python" 
    description="Saiba como usar Azure notificação Hubs de um Python back-end." 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu"
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="python" 
    ms.devlang="php" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="how-to-use-notification-hubs-from-python"></a>Como usar Hubs de notificação do Python
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]
        
Você pode acessar todos os recursos de Hubs de notificação de um back-end Java/PHP/Python/Ruby usando a interface REST de Hub de notificação, conforme descrito no tópico MSDN [Notificação Hubs restante APIs](http://msdn.microsoft.com/library/dn223264.aspx).

> [AZURE.NOTE] Esta é uma implementação de referência de amostra para implementar o envia notificação no Python e não é o SDK do Python de Hub notificações oficialmente suportada.
>
> Este exemplo é escrito usando Python 3.4.

Neste tópico mostraremos como:

* Crie um cliente restante para recursos de Hubs de notificação em Python.
* Envie notificações usando a interface de Python as APIs de resto do Hub de notificação. 
* Obter uma imagem da solicitação/resposta HTTP (REST) para depuração/educacionais finalidade. 

Você pode seguir o [tutorial de Introdução Get](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) para sua plataforma móvel de escolha, implementar a parte de back-end em Python.

> [AZURE.NOTE] O escopo da amostra é limitado apenas para enviar notificações e não fazer qualquer gerenciamento de registro.

## <a name="client-interface"></a>Interface do cliente
A interface do cliente principal pode fornecer os mesmos métodos que estão disponíveis no [.NET notificação Hubs SDK](http://msdn.microsoft.com/library/jj933431.aspx). Isso permitirá que você traduzir diretamente todos os tutoriais e exemplos atualmente disponíveis neste site e contribuiu pela comunidade na internet.

Você pode encontrar todos os códigos disponíveis na [amostra de embalagem Python restante].

Por exemplo, para criar um cliente:

    isDebug = True
    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
    
Para enviar uma notificação do sistema Windows:
    
    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)
    
## <a name="implementation"></a>Implementação
Se você ainda não fez, siga nosso [tutorial de Introdução Get] até a última seção onde você precisa implementar o back-end.

Todos os detalhes para implementar uma embalagem restante completa podem ser encontrados no [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). Nesta seção, podemos descreverá a implementação Python as principais etapas necessárias para acessar os pontos de extremidade do resto de Hubs de notificação e enviar notificações

1. Analisará a cadeia de conexão
2. Gerar o token de autorização
3. Enviar uma notificação usando API REST de HTTP

### <a name="parse-the-connection-string"></a>Analisará a cadeia de conexão

Aqui está a classe principal Implementando o cliente, cujos construtor analisa a cadeia de conexão:

    class NotificationHub:
        API_VERSION = "?api-version=2013-10"
        DEBUG_SEND = "&test"
    
        def __init__(self, connection_string=None, hub_name=None, debug=0):
            self.HubName = hub_name
            self.Debug = debug
    
            # Parse connection string
            parts = connection_string.split(';')
            if len(parts) != 3:
                raise Exception("Invalid ConnectionString.")
    
            for part in parts:
                if part.startswith('Endpoint'):
                    self.Endpoint = 'https' + part[11:]
                if part.startswith('SharedAccessKeyName'):
                    self.SasKeyName = part[20:]
                if part.startswith('SharedAccessKey'):
                    self.SasKeyValue = part[16:]


### <a name="create-security-token"></a>Criar token de segurança
Os detalhes sobre a criação de token de segurança estão disponíveis [aqui](http://msdn.microsoft.com/library/dn495627.aspx).
Os seguintes métodos precisam ser adicionada à classe **NotificationHub** para criar o token com base no URI da solicitação atual e as credenciais extraídas da cadeia de conexão.

    @staticmethod
    def get_expiry():
        # By default returns an expiration of 5 minutes (=300 seconds) from now
        return int(round(time.time() + 300))

    @staticmethod
    def encode_base64(data):
        return base64.b64encode(data)

    def sign_string(self, to_sign):
        key = self.SasKeyValue.encode('utf-8')
        to_sign = to_sign.encode('utf-8')
        signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
        digest = signed_hmac_sha256.digest()
        encoded_digest = self.encode_base64(digest)
        return encoded_digest

    def generate_sas_token(self):
        target_uri = self.Endpoint + self.HubName
        my_uri = urllib.parse.quote(target_uri, '').lower()
        expiry = str(self.get_expiry())
        to_sign = my_uri + '\n' + expiry
        signature = urllib.parse.quote(self.sign_string(to_sign))
        auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
        sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
        return sas_token

### <a name="send-a-notification-using-http-rest-api"></a>Enviar uma notificação usando API REST de HTTP
Usar primeira, permita que define uma classe que representa uma notificação.

    class Notification:
        def __init__(self, notification_format=None, payload=None, debug=0):
            valid_formats = ['template', 'apple', 'gcm', 'windows', 'windowsphone', "adm", "baidu"]
            if not any(x in notification_format for x in valid_formats):
                raise Exception(
                    "Invalid Notification format. " +
                    "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")
    
            self.format = notification_format
            self.payload = payload
    
            # array with keynames for headers
            # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
            # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
            # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
            self.headers = None

Essa classe é um contêiner para o corpo de uma notificação nativo ou um conjunto de propriedades no caso de uma notificação de modelo, um conjunto de cabeçalhos que contém propriedades específicas de plataforma (como cabeçalhos WNS e propriedade de expiração de Apple) e formato (plataforma nativa ou modelo).

Consulte a [documentação de notificação Hubs restante APIs](http://msdn.microsoft.com/library/dn495827.aspx) e formatos das plataformas de notificação específica para todas as opções disponíveis.

Agora com essa classe, podemos escrever enviar métodos de notificação dentro da classe **NotificationHub** .

    def make_http_request(self, url, payload, headers):
        parsed_url = urllib.parse.urlparse(url)
        connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

        if self.Debug > 0:
            connection.set_debuglevel(self.Debug)
            # adding this querystring parameter gets detailed information about the PNS send notification outcome
            url += self.DEBUG_SEND
            print("--- REQUEST ---")
            print("URI: " + url)
            print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
            print("--- END REQUEST ---\n")

        connection.request('POST', url, payload, headers)
        response = connection.getresponse()

        if self.Debug > 0:
            # print out detailed response information for debugging purpose
            print("\n\n--- RESPONSE ---")
            print(str(response.status) + " " + response.reason)
            print(response.msg)
            print(response.read())
            print("--- END RESPONSE ---")

        elif response.status != 201:
            # Successful outcome of send message is HTTP 201 - Created
            raise Exception(
                "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

        connection.close()

    def send_notification(self, notification, tag_or_tag_expression=None):
        url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

        json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

        if any(x in notification.format for x in json_platforms):
            content_type = "application/json"
            payload_to_send = json.dumps(notification.payload)
        else:
            content_type = "application/xml"
            payload_to_send = notification.payload

        headers = {
            'Content-type': content_type,
            'Authorization': self.generate_sas_token(),
            'ServiceBusNotification-Format': notification.format
        }

        if isinstance(tag_or_tag_expression, set):
            tag_list = ' || '.join(tag_or_tag_expression)
        else:
            tag_list = tag_or_tag_expression

        # add the tags/tag expressions to the headers collection
        if tag_list != "":
            headers.update({'ServiceBusNotification-Tags': tag_list})

        # add any custom headers to the headers collection that the user may have added
        if notification.headers is not None:
            headers.update(notification.headers)

        self.make_http_request(url, payload_to_send, headers)

    def send_apple_notification(self, payload, tags=""):
        nh = Notification("apple", payload)
        self.send_notification(nh, tags)

    def send_gcm_notification(self, payload, tags=""):
        nh = Notification("gcm", payload)
        self.send_notification(nh, tags)

    def send_adm_notification(self, payload, tags=""):
        nh = Notification("adm", payload)
        self.send_notification(nh, tags)

    def send_baidu_notification(self, payload, tags=""):
        nh = Notification("baidu", payload)
        self.send_notification(nh, tags)

    def send_mpns_notification(self, payload, tags=""):
        nh = Notification("windowsphone", payload)

        if "<wp:Toast>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'toast', 'X-NotificationClass': '2'}
        elif "<wp:Tile>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'tile', 'X-NotificationClass': '1'}

        self.send_notification(nh, tags)

    def send_windows_notification(self, payload, tags=""):
        nh = Notification("windows", payload)

        if "<toast>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/toast'}
        elif "<tile>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/tile'}
        elif "<badge>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/badge'}

        self.send_notification(nh, tags)

    def send_template_notification(self, properties, tags=""):
        nh = Notification("template", properties)
        self.send_notification(nh, tags)

Os métodos acima enviar uma solicitação HTTP POST para o ponto de extremidade de /messages do seu hub de notificação, com o corpo correto e os cabeçalhos para enviar a notificação.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Usando a propriedade de depuração para habilitar o log detalhado
Habilitando a propriedade de depuração ao inicializar o Hub de notificação gravará informações de log detalhadas sobre a solicitação HTTP e despejo de resposta, bem como mensagem de notificação detalhada envia resultado. Adicionamos recentemente essa propriedade chamada [notificação Hubs TestSend propriedade](http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx) que retorna informações detalhadas sobre o resultado de enviar notificação. Para usá-lo - inicializar usando o seguinte:

    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

A solicitação de notificação Hub enviar URL HTTP obtém anexada com uma sequência de consulta de "teste" como resultado. 

##<a name="complete-tutorial"></a>Concluir o tutorial
Agora você pode concluir o tutorial de Introdução enviando a notificação de um Python back-end.

Inicialize o cliente de Hubs de notificação (substitua o nome de cadeia de caracteres e hub de conexão como instruções no [tutorial de Introdução Get]):

    hub = NotificationHub("myConnectionString", "myNotificationHubName")

Em seguida, adicione o código de envio dependendo de sua plataforma móvel de destino. Este exemplo também adiciona métodos de nível superiores para ativar as notificações de envio com base na plataforma send_windows_notification por exemplo, para windows; send_apple_notification (para a apple) etc. 

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store e Windows Phone 8.1 (não Silverlight)

    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 e 8.1 Silverlight

    hub.send_mpns_notification(toast)

### <a name="ios"></a>iOS

    alert_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_apple_notification(alert_payload)

### <a name="android"></a>Android
    gcm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_gcm_notification(gcm_payload)

### <a name="kindle-fire"></a>Kindle Fire
    adm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_adm_notification(adm_payload)

### <a name="baidu"></a>Baidu
    baidu_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_baidu_notification(baidu_payload)

Executar o código do Python deve produzir uma notificação que aparece em seu dispositivo de destino.

## <a name="examples"></a>Exemplos:

### <a name="enabling-debug-property"></a>Habilitando a propriedade de depuração
Quando você habilita o sinalizador de depuração ao inicializar o NotificationHub então verá detalhada solicitação HTTP e despejo de resposta, bem como NotificationOutcome semelhante ao seguinte onde você possa entender o que são cabeçalhos HTTP são passados na solicitação e quais resposta HTTP recebida do Hub notificação:    ![][1]

Você verá detalhadas por exemplo, o resultado de Hub de notificação 

- Quando a mensagem é enviada com êxito para o serviço de notificação por Push. 
    
        <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>

- Se não houver nenhuma destinos encontrados para qualquer notificação de envio, em seguida, você provavelmente vai veja a seguir na resposta (que indica que não houve nenhum registro encontrado entregar a notificação provavelmente porque os registros tinham algumas marcas incompatíveis)

        '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'

### <a name="broadcast-toast-notification-to-windows"></a>Transmissão de notificação do sistema para o Windows 

Observe que os cabeçalhos que obtém enviados quando você estiver enviando uma notificação do sistema de transmissão para cliente do Windows. 

    hub.send_windows_notification(wns_payload)

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Enviar notificação especificando uma marca (ou expressão de marca)

Observe o cabeçalho HTTP de marcas que é adicionado à solicitação HTTP (no exemplo abaixo, nós está enviando a notificação somente para os registros com carga de 'esportes')

    hub.send_windows_notification(wns_payload, "sports")

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Enviar notificação especificando várias marcas

Observe como o cabeçalho HTTP marcas muda quando várias marcas são enviadas. 
    
    tags = {'sports', 'politics'}
    hub.send_windows_notification(wns_payload, tags)

![][4]

### <a name="templated-notification"></a>Notificação de modelo

Observe que as alterações de cabeçalho de formato HTTP e o corpo de carga é enviado como parte do corpo da solicitação HTTP:

**Cliente - modelo registrado**

        var template =
                        @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
            
**Servidor - envio da carga**
        
        template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
        hub.send_template_notification(template_payload)

![][5]


## <a name="next-steps"></a>Próximas etapas
Neste tópico mostrado como criar um cliente simples do restante do Python para Hubs de notificação. A partir daqui, você pode:

* Baixe o completo [amostra de embalagem restante de Python], que contém todos os códigos acima.
* Continue aprendendo mais Hubs de notificação marcação recurso no [tutorial Breaking News]
* Continue aprendendo sobre o recurso de modelos de Hubs de notificação no [tutorial de localização de notícias]

<!-- URLs -->
[Amostra de embalagem Python restante]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Obter tutorial de Introdução]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Interrompendo tutorial de notícias]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Localizando tutorial de notícias]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
 
