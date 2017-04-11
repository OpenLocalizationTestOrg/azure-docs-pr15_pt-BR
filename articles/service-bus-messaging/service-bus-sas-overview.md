<properties
    pageTitle="Compartilhado visão geral de assinaturas de acesso | Microsoft Azure"
    description="O que são compartilhados assinaturas de acesso, como elas funcionam e como usá-las do nó, PHP e c#."
    services="service-bus"
    documentationCenter="na"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/02/2016"
    ms.author="darosa;sethm"/>

# <a name="shared-access-signatures"></a>Assinaturas de acesso compartilhado

*Compartilhado assinaturas de acesso* (SAS) são o mecanismo de segurança principal para barramento de serviço, incluindo o evento Hubs, orientado mensagens (filas e tópicos) e transmitidas de mensagens. Este artigo discute assinaturas de acesso compartilhado, como elas funcionam e como usá-las de maneira independente de plataforma.

## <a name="overview-of-sas"></a>Visão geral de Associações de segurança

Assinaturas de acesso compartilhado são um mecanismo de autenticação baseado em hash seguro SHA-256 ou URIs. SAS é um mecanismo poderoso que é usado por todos os serviços de barramento de serviço. Em uso real, SAS tem dois componentes: uma *Política de acesso compartilhado* e uma *Assinatura de acesso compartilhado* (normalmente chamado de um *token*).

Você pode encontrar informações mais detalhadas sobre assinaturas de acesso compartilhado com barramento de serviço em [autenticação de assinatura de acesso compartilhado com barramento de serviço](service-bus-shared-access-signature-authentication.md).

## <a name="shared-access-policy"></a>Política de acesso compartilhado

Uma observação importante entender sobre SAS é que tudo começa com uma política. Para cada política, você decidir em três partes de informações: **nome**, **escopo**e **permissões**. O **nome** é exatamente isso; um nome exclusivo dentro desse escopo. O escopo é muito fácil: é o URI do recurso em questão. Para um namespace barramento de serviço, o escopo é o nome de domínio totalmente qualificado (FQDN), tais como `https://<yournamespace>.servicebus.windows.net/`.

As permissões disponíveis para uma política são amplamente auto-explicativos:

  + Enviar
  + Ouvir
  + Gerenciar

Depois de criar a política, ele é atribuído uma *Chave primária* e uma *Chave secundária*. Estas são as chaves de criptografia fortes. Não percam ou perda-los - sempre estará disponíveis no [portal do Azure][]. Você pode usar qualquer uma das chaves geradas, e você pode gerá-los novamente a qualquer momento. No entanto, se você gerar ou alterar a chave primária na política, serão invalidadas qualquer assinaturas de acesso compartilhado criado a partir dele.

Quando você cria um namespace barramento de serviço, uma política é criada automaticamente para todo o namespace chamado **RootManageSharedAccessKey**e essa política tem todas as permissões. Você não faça logon como **raiz**, para que não use esta política, a menos que haja um realmente bom motivo. Você pode criar diretivas adicionais na guia **Configurar** para o namespace no portal. É importante anotação que um único nível no carregador de serviço de árvore (namespace, fila, Hub de evento, etc.) só pode ter até 12 políticas anexadas a ele.

## <a name="shared-access-signature-token"></a>Assinatura de acesso compartilhado (token)

A própria diretiva não é o token de acesso de barramento de serviço. É o objeto do qual o token de acesso é gerado - usando a chave primária ou secundária. O token é gerado pelo cuidadosamente criar uma cadeia de caracteres no seguinte formato:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Onde `signature-string` é o hash SHA-256 do escopo do token (**escopo** conforme descrito na seção anterior) com um CRLF acrescentado e uma hora de expiração (em segundos desde a época: `00:00:00 UTC` em 1 de janeiro de 1970).

O hash é semelhante ao seguinte código pseudo-índice e retorna 32 bytes.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Os valores de hash não são na cadeia **SharedAccessSignature** para que o destinatário pode calcular o hash com os mesmos parâmetros, certifique-se de que ela retorna o mesmo resultado. O URI especifica o escopo e o nome da chave identifica a política a ser usado para calcular o hash. Isso é importante de um ponto de vista de segurança. Se a assinatura não corresponderem ao que calcula o destinatário (barramento de serviço), em seguida, acesso negado. Neste ponto você pode ter certeza de que o remetente tinha acesso à chave e deve ser concedido os direitos especificados na política.

## <a name="generating-a-signature-from-a-policy"></a>Gerar uma assinatura de uma política

Como você realmente fazer isso no código? Vamos dar uma olhada em algumas delas.

### <a name="nodejs"></a>NodeJS

```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### <a name="java"></a>Java

```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### <a name="php"></a>PHP

```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time();  
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256',             
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
return $token; 
}
```
 
### <a name="c35"></a>C & #35;

```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="using-the-shared-access-signature-at-http-level"></a>Usando a assinatura de acesso compartilhado (no nível HTTP)
 
Agora que você sabe como criar assinaturas de acesso compartilhado para quaisquer entidades no carregador de serviço, você está pronto para executar uma POSTAGEM HTTP:

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
    
Lembre-se de que isso funciona para tudo. Você pode criar SAS para uma fila, tópico, assinatura, Hub de evento ou retransmissão. Se você usar identidade por Editor para Hubs de evento, você simplesmente acrescentar `/publishers/< publisherid>`.

Se você fornecer um remetente ou um cliente um token SAS, eles não tem a chave diretamente, e eles não é possível reverter o hash para obtê-lo. Assim, você tem controle sobre o que eles podem acessar e por quanto tempo. Uma observação importante a lembrar é que se você alterar a chave primária na política, qualquer assinaturas de acesso compartilhado criado a partir dele serão invalidadas.

## <a name="using-the-shared-access-signature-at-amqp-level"></a>Usando a assinatura de acesso compartilhado (no nível AMQP)

Na seção anterior, você viu como usar o token SAS com uma solicitação HTTP POST para enviar dados para o barramento de serviço. Como você sabe, você pode acessar barramento de serviço usando o Advanced mensagem fila Protocol (AMQP) que é o protocolo preferido para usar por razões de desempenho, em muitos cenários. O uso de token de SAS com AMQP é descrito no documento [AMQP Claim-Based segurança versão 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) que está em rascunho de trabalho desde 2013, mas bem suportada pelo Azure hoje.

Antes de começar a enviar dados para o barramento de serviço, o publisher deve enviar o token SAS dentro de uma mensagem de AMQP para um nó AMQP bem definido chamado **$cbs** (pode ser visto como uma fila "especial" usada pelo serviço para adquirir e validar todos os tokens SAS). O publisher especifique o campo **ReplyTo** a mensagem AMQP; Este é o nó no qual o serviço de respostas para o publisher com o resultado da validação de token (um padrão de solicitação/resposta simples entre o publisher e o serviço). Este nó de resposta é criado "ao instantâneos," falando sobre "criação dinâmica de nó remoto", conforme descrito pela especificação AMQP 1.0. Após verificar que o token SAS é válido, o publisher pode ir adiante e começar a enviar dados para o serviço.

As etapas a seguir mostram como enviar o token SAS com protocolo AMQP usando a biblioteca [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) . Isso é útil se você não pode usar o SDK de barramento oficial do serviço (por exemplo, em WinRT, .net Framework Compact, .net Micro Framework e Mono) desenvolvendo em C\#. Claro, esta biblioteca é útil para ajudar a entender como baseada em declarações segurança funciona no nível do AMQP, como você viu como ele funciona no nível de HTTP (com uma solicitação HTTP POST e token SAS enviado dentro do cabeçalho "Autorização"). Se não precisar tal profunda conhecimento sobre AMQP, você pode usar o SDK do barramento de serviço oficial com .net Framework applications, que fará para você.

### <a name="c35"></a>C & #35;

```
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

O `PutCbsToken()` método recebe a *conexão* (AMQP conexão classe instância conforme fornecida pela [biblioteca AMQP .NET Lite](https://github.com/Azure/amqpnetlite)) que representa a conexão de TCP para o serviço e o parâmetro de *sasToken* que é o token SAS para enviar. 

> [AZURE.NOTE] É importante que a conexão é criado com o **mecanismo de autenticação SASL definida como externo** (e não a sem formatação padrão com o nome de usuário e senha usados quando você não precisa enviar o token SAS).

Em seguida, o publisher cria dois links AMQP para o token SAS de envio e recebimento a resposta (o resultado de validação token) do serviço.

A mensagem AMQP contém um conjunto de propriedades e mais informações do que uma simples mensagem. O token SAS é o corpo da mensagem (usando seu construtor). A propriedade **"ReplyTo"** é definida como o nome do nó para receber o resultado de validação no link receptor (você pode alterar seu nome, se desejar, ele será criado dinamicamente pelo serviço). As últimas três propriedades de aplicativo/personalizadas são usadas pelo serviço para indicar que tipo de operação tem que executar. Conforme descrito pela especificação CBS rascunho, ele devem ser o **nome da operação** ("colocar-token"), o **tipo de token** (nesse caso, "servicebus.windows.net:sastoken") e o **"nome" da audiência** à qual o token se aplica (a entidade inteira).

Após enviar o token SAS no link remetente, o publisher deve ler a resposta no link receptor. A resposta é uma mensagem AMQP simples com uma propriedade de aplicativo denominada **"código de status"** que pode conter os mesmos valores um código de status HTTP. 

## <a name="next-steps"></a>Próximas etapas

Consulte a [referência de API do serviço barramento REST](https://msdn.microsoft.com/library/azure/hh780717.aspx) para obter mais informações sobre o que você pode fazer com esses tokens SAS.

Para obter mais informações sobre autenticação de barramento de serviço, consulte [barramento de serviço de autenticação e autorização](service-bus-authentication-and-authorization.md). 

Mais exemplos de Associações em c# e Java Script de são [nesta](http://developers.de/blogs/damir_dobric/archive/2013/10/17/how-to-create-shared-access-signature-for-service-bus.aspx)postagem de blog.

[Portal do Azure]: https://portal.azure.com