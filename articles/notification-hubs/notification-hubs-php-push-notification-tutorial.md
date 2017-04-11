<properties 
    pageTitle="Como usar Hubs de notificação com PHP" 
    description="Saiba como usar Azure notificação Hubs de um PHP back-end." 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="php" 
    ms.devlang="php" 
    ms.topic="article" 
    ms.date="06/07/2016" 
    ms.author="yuaxu"/>

# <a name="how-to-use-notification-hubs-from-php"></a>Como usar Hubs de notificação do PHP
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Você pode acessar todos os recursos de Hubs de notificação de um back-end PHP/Java/Ruby usando a interface REST de Hub de notificação, conforme descrito no tópico MSDN [Notificação Hubs restante APIs](http://msdn.microsoft.com/library/dn223264.aspx).

Neste tópico mostraremos como:

* Criar um cliente restante para recursos de Hubs de notificação em PHP;
* Siga o [tutorial de Introdução Get](notification-hubs-ios-apple-push-notification-apns-get-started.md) para sua plataforma móvel de escolha, implementar a parte de back-end no PHP.

## <a name="client-interface"></a>Interface do cliente
A interface do cliente principal pode fornecer os mesmos métodos que estão disponíveis no [.NET notificação Hubs SDK](http://msdn.microsoft.com/library/jj933431.aspx), isso permitirá que você traduzir diretamente todos os tutoriais e exemplos atualmente disponíveis neste site e contribuiu pela comunidade na internet.

Você pode encontrar todos os códigos disponíveis na [amostra de embalagem PHP restante].

Por exemplo, para criar um cliente:

    $hub = new NotificationHub("connection string", "hubname"); 

Para enviar um iOS nativa notificação:
    
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);

## <a name="implementation"></a>Implementação
Se você ainda não fez, siga nosso [tutorial de Introdução Get] até a última seção onde você precisa implementar o back-end.
Além disso, se desejar você pode usar o código de [amostra de embalagem restante PHP] e vá diretamente para a seção [conclua o tutorial](#complete-tutorial) .

Todos os detalhes para implementar uma embalagem restante completa podem ser encontrados no [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). Nesta seção, podemos descreverá a implementação de PHP das principais etapas necessárias para acessar os pontos de extremidade do resto de Hubs de notificação:

1. Analisará a cadeia de conexão
2. Gerar o token de autorização
3. Executar a chamada HTTP

### <a name="parse-the-connection-string"></a>Analisará a cadeia de conexão

Aqui está a classe principal Implementando o cliente, cujos construtor analisa a cadeia de conexão:

    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";
    
        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;
    
        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;
    
            $this->parseConnectionString($connectionString);
        }
    
        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }
    
            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }


### <a name="create-security-token"></a>Criar token de segurança
Os detalhes sobre a criação de token de segurança estão disponíveis [aqui](http://msdn.microsoft.com/library/dn495627.aspx).
O seguinte método tem a ser adicionado à classe **NotificationHub** para criar o token com base no URI da solicitação atual e as credenciais extraídas da cadeia de conexão.

    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }

### <a name="send-a-notification"></a>Enviar uma notificação
Primeiro, vamos defina uma classe representando uma notificação.

    class Notification {
        public $format;
        public $payload;
    
        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;
    
        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "gcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }
    
            $this->format = $format;
            $this->payload = $payload;
        }
    }

Essa classe é um contêiner para o corpo de uma notificação nativo, ou um conjunto de propriedades no caso de uma notificação de modelo e um conjunto de cabeçalhos que contém propriedades específicas de plataforma (como cabeçalhos WNS e propriedade de expiração de Apple) e formato (plataforma nativa ou modelo).

Consulte a [documentação de notificação Hubs restante APIs](http://msdn.microsoft.com/library/dn495827.aspx) e formatos das plataformas de notificação específica para todas as opções disponíveis.

Contando com essa classe, agora podemos escrever enviar métodos de notificação dentro da classe **NotificationHub** .

    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "gcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }
        
        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notificaiton: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 

Os métodos acima enviar uma solicitação HTTP POST para o ponto de extremidade de /messages do seu hub de notificação, com o corpo correto e os cabeçalhos para enviar a notificação.

##<a name="complete-tutorial"></a>Concluir o tutorial
Agora você pode concluir o tutorial de Introdução enviando a notificação de um PHP back-end.

Inicialize o cliente de Hubs de notificação (substitua o nome de cadeia de caracteres e hub de conexão como instruções no [tutorial de Introdução Get]):

    $hub = new NotificationHub("connection string", "hubname"); 

Em seguida, adicione o código de envio dependendo de sua plataforma móvel de destino.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store e Windows Phone 8.1 (não Silverlight)

    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);

### <a name="ios"></a>iOS

    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);

### <a name="android"></a>Android
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("gcm", $message);
    $hub->sendNotification($notification, null);

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 e 8.1 Silverlight

    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);


### <a name="kindle-fire"></a>Kindle Fire
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);

Executar o código do PHP deve produzir agora uma notificação que aparece em seu dispositivo de destino.


## <a name="next-steps"></a>Próximas etapas
Neste tópico mostrado como criar um cliente simples do restante do Java para Hubs de notificação. A partir daqui, você pode:

* Baixe o completo [amostra de embalagem do restante do PHP], que contém todos os códigos acima.
* Continue aprendendo mais Hubs de notificação marcação recurso no [tutorial Breaking News]
* Saiba mais sobre insistem notificações para usuários individuais [tutorial notificar usuários]

Para obter mais informações, consulte também o [PHP Developer Center](/develop/php/).

[Amostra de embalagem do restante do PHP]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Obter tutorial de Introdução]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
 
