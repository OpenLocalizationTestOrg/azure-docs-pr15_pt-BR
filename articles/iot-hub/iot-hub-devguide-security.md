<properties
 pageTitle="Guia do desenvolvedor - controlar o acesso às IoT Hub | Microsoft Azure"
 description="Guia de desenvolvedor do Azure IoT Hub - como controlar o acesso ao IoT Hub e gerenciar a segurança"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="control-access-to-iot-hub"></a>Controlar o acesso às IoT Hub

## <a name="overview"></a>Visão geral

Este artigo descreve as opções para proteger o seu hub IoT. Hub de IoT usa *permissões* para conceder acesso a cada pontos de extremidade do hub IoT. Permissões de limitam o acesso a um hub de IoT com base em funcionalidade.

Este artigo descreve:

- As permissões diferentes que você pode conceder a um aplicativo de dispositivo ou back-end para acessar o seu hub IoT.
- O processo de autenticação e os tokens que ele usa para verificar permissões.
- Como as credenciais de escopo para limitar o acesso a recursos específicos.
- Suporte de IoT Hub certificados x. 509.
- Mecanismos de autenticação de dispositivo personalizados que usam registros de identidade de dispositivo existente ou esquemas de autenticação.

### <a name="when-to-use"></a>Quando usar

Você deve ter permissões apropriadas para acessar qualquer um dos pontos de extremidade IoT Hub. Por exemplo, um dispositivo deve incluir um token contendo as credenciais de segurança juntamente com todas as mensagens que envia a IoT Hub.

## <a name="access-control-and-permissions"></a>Permissões e controle de acesso

Você pode conceder [permissões](#iot-hub-permissions) das seguintes maneiras:

* **Nível de hub compartilhados políticas de acesso**. Políticas de acesso compartilhado podem conceder qualquer combinação de [permissões](#iot-hub-permissions). Você pode definir políticas no [portal do Azure][lnk-management-portal], ou por programação usando o [provedor de recursos do IoT Hub APIs REST][lnk-resource-provider-apis]. Um hub IoT recém-criado tem as seguintes políticas padrão:

    - **iothubowner**: política com todas as permissões.
    - **serviço**: política com permissão de ServiceConnect.
    - **dispositivo**: política com permissão de DeviceConnect.
    - **registryRead**: política com permissão de RegistryRead.
    - **registryReadWrite**: política com permissões RegistryRead e RegistryWrite.


* **Credenciais de segurança de cada dispositivo**. Cada IoT Hub contém um [registro de identidade do dispositivo][lnk-identity-registry]. Para cada dispositivo nesse registro, você pode configurar credenciais de segurança que conceder permissões de **DeviceConnect** com escopo definidas para os pontos de extremidade do dispositivo correspondente.

Por exemplo, em uma solução IoT típica:

- O componente de gerenciamento de dispositivo usa a política de *registryReadWrite* .
- O componente de processador de evento usa a política de *serviço* .
- O componente de lógica de negócios de dispositivo runtime usa a política de *serviço* .
- Dispositivos individuais se conectar usando credenciais armazenadas no registro de identidade do hub IoT.

## <a name="authentication"></a>Autenticação

Hub de IoT Azure concede acesso a pontos de extremidade Verificando um token em relação às políticas de acesso compartilhado e credenciais de segurança do dispositivo identidade do registro.

Credenciais de segurança, como chaves simétricas, nunca são enviadas durante a transmissão.

> [AZURE.NOTE] O provedor de recursos do Azure IoT Hub é protegido por meio de sua assinatura do Azure, como estão todos os provedores no [Gerenciador de recursos do Azure][lnk-azure-resource-manager].

Para obter mais informações sobre como criar e usar tokens de segurança, consulte [tokens de segurança do IoT Hub][lnk-sas-tokens].

### <a name="protocol-specifics"></a>Especificações de protocolo

Cada protocolo com suporte, como MQTT, AMQP e HTTP, transportes tokens de maneiras diferentes.

Ao usar MQTT, o pacote de conectar tem o deviceId como, {iothubhostname} / {deviceId} no campo nome de usuário e um token SAS no campo senha. {iothubhostname} deve ser o CName completo do hub IoT (por exemplo, contoso.azure-devices.net).

Ao usar [AMQP][lnk-amqp], IoT Hub suporta [SASL simples] [ lnk-sasl-plain] [AMQP declarações com base-segurança]e[lnk-cbs].

Se você usar AMQP declarações com base-segurança, o padrão especifica como transmitir esses tokens.

Para SASL simples, pode ser o **nome de usuário** :

* `{policyName}@sas.root.{iothubName}`Se usando tokens de nível do hub.
* `{deviceId}@sas.{iothubname}`Se usando tokens de escopo do dispositivo.

Em ambos os casos, o campo de senha contém o token, conforme descrito em [tokens de segurança do IoT Hub][lnk-sas-tokens].

HTTP implementa autenticação, incluindo um token válido no cabeçalho da solicitação de **autorização** .

#### <a name="example"></a>Exemplo

Nome de usuário (DeviceId diferencia maiusculas de minúsculas):`iothubname.azure-devices.net/DeviceId`

Senha (gerar SAS com o Explorador de dispositivo):`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] O [Azure IoT Hub SDKs] [ lnk-sdks] gerar tokens automaticamente ao conectar ao serviço. Em alguns casos, SDK do não oferece suporte a todos os protocolos ou todos os métodos de autenticação.

### <a name="special-considerations-for-sasl-plain"></a>Considerações especiais para SASL simples

Ao usar SASL simples com AMQP, um cliente se conectar a um hub IoT pode usar um único token para cada conexão de TCP. Quando o token expira, a conexão de TCP desconecta do serviço e aciona um reconectar. Esse comportamento, embora não problemático para um componente de back-end do aplicativo, é muito prejudicial para um aplicativo do lado do dispositivo pelos seguintes motivos:

*  Gateways geralmente conectar em nome de muitos dispositivos. Ao usar SASL simples, eles precisam criar uma conexão de TCP distinta para cada dispositivo conectado a um hub IoT. Este cenário consideravelmente aumenta o consumo de energia e recursos de rede e aumenta a latência de cada conexão de dispositivo.
* Dispositivos com recursos limitados negativamente são afetados pelo aumento do uso de recursos para se reconectar após cada expiração do token.

## <a name="scope-hub-level-credentials"></a>Credenciais de nível de hub de escopo

Você pode fazer as políticas de segurança em nível de hub criando tokens com um recurso restrito URI. Por exemplo, o ponto de extremidade para enviar mensagens de dispositivo à nuvem em um dispositivo é **/devices/ {deviceId} / mensagens/eventos**. Você também pode usar uma política de acesso de nível de hub compartilhado com permissões de **DeviceConnect** entre um token cujo resourceURI é **/devices/ {deviceId}**. Essa abordagem cria um token que só é útil para enviar mensagens em nome do dispositivo **deviceId**.

Esse mecanismo é semelhante à [diretiva de editor de evento Hubs][lnk-event-hubs-publisher-policy]e permite que você implementar métodos de autenticação personalizados.

## <a name="security-tokens"></a>Tokens de segurança

Hub de IoT usa tokens de segurança para autenticar dispositivos e serviços para evitar o envio de teclas na conexão. Além disso, os tokens de segurança são limitados no escopo e a validade de tempo. [Azure SDKs de Hub IoT] [ lnk-sdks] gerar automaticamente tokens sem a necessidade de qualquer configuração especial. Alguns cenários, no entanto, exigem que o usuário gerar e usar tokens de segurança diretamente. Eles incluem o uso direto dos emerge as MQTT, AMQP ou HTTP ou a implementação do padrão de serviço de token, como explicado em [autenticação de dispositivo personalizado][lnk-custom-auth].

Hub de IoT também permite que os dispositivos autenticar com IoT Hub usando [certificados x. 509][lnk-x509]. 

### <a name="security-token-structure"></a>Estrutura de token de segurança
Você usa tokens de segurança para conceder acesso limitado a hora a dispositivos e serviços para funcionalidade específica no IoT Hub. Para garantir que somente os dispositivos autorizados e serviços podem se conectar, tokens de segurança devem ser assinados com uma chave de política de acesso compartilhado ou uma chave simétrica armazenada com uma identidade de dispositivo no registro identidade.

Um token assinado com uma chave concede de política de acesso compartilhado acesso a todas as funcionalidades associadas às permissões de política de acesso compartilhado. Por outro lado, um token assinado com chave simétrica de uma identidade dispositivo apenas concede a permissão de **DeviceConnect** para a identidade do dispositivo associado.

O token de segurança tem o seguinte formato:

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Estes são os valores esperados:

| Valor | Descrição |
| ----- | ----------- |
| {assinatura} | Uma cadeia de caracteres de assinatura HMAC-SHA256 do formulário: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: A chave é decodificar de base64 e usada como chave para executar a computação HMAC-SHA256. |
| {resourceURI} | Prefixo URI (por segmento) dos pontos de extremidade que podem ser acessados com esse token, começando com o nome do host do hub IoT (sem protocol). Por exemplo,`myHub.azure-devices.net/devices/device1` |
| {expiração} | Cadeias de caracteres UTF8 para o número de segundos desde época 00:00:00 UTC em 1 de janeiro de 1970. |
| {URL-codificados-resourceURI} | Menor caso a codificação de URL de URI do recurso de letras minúsculas |
| {policyName} | O nome da política acesso compartilhado ao qual se refere a esse símbolo. Ausente no caso de tokens fazendo referência a credenciais de registro do dispositivo. |

**Observação sobre prefixo**: prefixo o URI é calculado pelo segmento e não pelo caractere. Por exemplo `/a/b` é um prefixo para `/a/b/c` , mas não para `/a/bc`.

O trecho de Node a seguir mostra uma função chamada **generateSasToken** que calcula o token de entradas `resourceUri, signingKey, policyName, expiresInMins`. As próximas seções detalham como inicializar as entradas diferentes para os casos de uso de token diferentes.

    var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
        resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();

        // Set expiration in seconds
        var expires = (Date.now() / 1000) + expiresInMins * 60;
        expires = Math.ceil(expires);
        var toSign = resourceUri + '\n' + expires;

        // Use crypto
        var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
        hmac.update(toSign);
        var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

        // Construct autorization string
        var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
        + base64UriEncoded + "&se=" + expires;
        if (policyName) token += "&skn="+policyName;
        return token;
    };

Como uma comparação, o código Python equivalente para gerar um token de segurança é:

    from base64 import b64encode, b64decode
    from hashlib import sha256
    from time import time
    from urllib import quote_plus, urlencode
    from hmac import HMAC

    def generate_sas_token(uri, key, policy_name, expiry=3600):
        ttl = time() + expiry
        sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
        print sign_key
        signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

        rawtoken = {
            'sr' :  uri,
            'sig': signature,
            'se' : str(int(ttl))
        }

        if policy_name is not None:
            rawtoken['skn'] = policy_name

        return 'SharedAccessSignature ' + urlencode(rawtoken)

> [AZURE.NOTE] Como o prazo de validade do token é validado em máquinas IoT Hub, é importante que o descompasso do relógio do computador que gera o token seja mínimo.

### <a name="use-sas-tokens-in-a-device-client"></a>Usar tokens SAS em um cliente do dispositivo

Há duas maneiras de obter permissões **DeviceConnect** com IoT Hub com tokens de segurança: use uma [chave de dispositivo simétrica do registro de identidade do dispositivo](#use-a-symmetric-key-in-the-identity-registry)ou use uma [chave de política de acesso compartilhado](#use-a-shared-access-policy).

Lembre-se de que todas as funcionalidades acessíveis de dispositivos é exposta por design em pontos de extremidade com prefixo `/devices/{deviceId}`.

> [AZURE.IMPORTANT] A única maneira que IoT Hub autentica um dispositivo específico está usando a chave simétrica de identidade do dispositivo. Em casos quando uma política de acesso compartilhado é usada para acessar a funcionalidade do dispositivo, a solução deve considerar o componente emitir o token de segurança como um subcomponente confiável.

Os pontos de extremidade voltados para o dispositivo são (independentemente do protocolo):

| Ponto de extremidade | Funcionalidade |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events` | Envie mensagens de dispositivo na nuvem. |
| `{iot hub host name}/devices/{deviceId}/devicebound` | Receba mensagens de nuvem para dispositivo. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Usar uma chave simétrica no registro identidade

Ao usar a chave simétrica de uma identidade dispositivo para gerar um token a policyName (`skn`) elemento do token é omitido.

Por exemplo, um símbolo criado para acessar todas as funcionalidades do dispositivo deve ter os seguintes parâmetros:

* URI do recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chave de assinatura: qualquer chave simétrica para a `{device id}` identidade,
* Nenhum nome de política,
* qualquer hora de expiração.

Um exemplo usando a função de nó acima seria:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

O resultado, que concede acesso a todas as funcionalidades do device1, seria:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [AZURE.NOTE] É possível gerar um token de seguro usando a ferramenta de .NET [Dispositivo Explorer][lnk-device-explorer].

### <a name="use-a-shared-access-policy"></a>Usar uma política de acesso compartilhado

Ao criar um token de uma política de acesso compartilhado, a política de nome do campo `skn` deve ser definido como o nome da política usada. Também é necessário que a política concede a permissão **DeviceConnect** .

Os dois cenários principais para usar políticas de acesso compartilhado para acessar a funcionalidade do dispositivo são:

* [nuvem gateways de protocolo][lnk-endpoints],
* [Serviços de token] [ lnk-custom-auth] usado para implementar esquemas de autenticação personalizados.

Como a política de acesso compartilhado potencialmente pode conceder acesso para conectar-se como qualquer dispositivo, é importante usar o recurso correto URI ao criar tokens de segurança. Isso é especialmente importante para serviços de token, que têm escopo o token para um dispositivo específico usando o URI do recurso. Esse ponto é menos relevante para gateways de protocolo conforme eles são intermediários já o tráfego para todos os dispositivos.

Como exemplo, um serviço de token usando a política de acesso compartilhado pré-criada chamada **dispositivo** criaria um token com os seguintes parâmetros:

* URI do recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chave de assinatura: uma das chaves do `device` política,
* nome da política: `device`,
* qualquer hora de expiração.

Um exemplo usando a função de nó acima seria:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

O resultado, que concede acesso a todas as funcionalidades do device1, seria:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

Um gateway de protocolo poderia usar o mesmo token para todos os dispositivos simplesmente definindo o URI do recurso para `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Use tokens de segurança dos componentes de serviço

Componentes de serviço só podem gerar tokens de segurança usando diretivas de acesso compartilhado conceder as permissões apropriadas conforme explicado anteriormente.

Estas são as funções de serviço expostas nos pontos de extremidade:

| Ponto de extremidade | Funcionalidade |
| ----- | ----------- |
| `{iot hub host name}/devices` | Criar, atualizar, recuperar e excluir identidades do dispositivo. |
| `{iot hub host name}/messages/events` | Receba mensagens de dispositivo na nuvem. |
| `{iot hub host name}/servicebound/feedback` | Receba comentários para mensagens de nuvem para dispositivo. |
| `{iot hub host name}/devicebound` | Envie mensagens de nuvem para dispositivo. |

Como exemplo, um serviço gerando usando a política de acesso compartilhado pré-criada chamada **registryRead** criaria um token com os seguintes parâmetros:

* URI do recurso: `{IoT hub name}.azure-devices.net/devices`,
* chave de assinatura: uma das chaves do `registryRead` política,
* nome da política: `registryRead`,
* qualquer hora de expiração.

    ponto de extremidade de var = "myhub.azure-devices.net/devices";   var policyName = 'dispositivo';   var policyKey = '...';

    var token = generateSasToken (ponto de extremidade, policyKey, policyName, 60);

O resultado, que seria conceder acesso para ler todas as identidades de dispositivo, seria:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead

## <a name="supported-x509-certificates"></a>Com suporte certificados x. 509

Você pode usar qualquer certificado x. 509 para autenticar um dispositivo com IoT Hub. Isso inclui:

-   **Um certificado x. 509 existente**. Um dispositivo pode já ter um certificado x. 509 associado a ele. O dispositivo pode usar este certificado para autenticar com IoT Hub.

-   **Um certificado de X-509 gerado automaticamente e auto-assinado**. Um fabricante de dispositivo ou implantador interno pode gerar esses certificados e armazenar a chave privada correspondente (e o certificado) no dispositivo. Você pode usar ferramentas como [OpenSSL] [ lnk-openssl] e [Windows SelfSignedCertificate] [ lnk-selfsigned] utilitário para essa finalidade.

-   **Certificado x. 509 assinado CA**. Você também pode usar um certificado x. 509 gerado e assinado por uma autoridade de certificação (CA) para identificar um dispositivo e autenticar um dispositivo com IoT Hub.

Um dispositivo pode usar um certificado x. 509 ou um token de segurança para autenticação, mas não ambos.

### <a name="register-an-x509-client-certificate-for-a-device"></a>Registrar um certificado de cliente x. 509 para um dispositivo

O [SDK do serviço de IoT Azure para c#] [ lnk-service-sdk] (versão 1.0.8+) dá suporte a registrar um dispositivo que usa um certificado de cliente x. 509 para autenticação. Outras APIs como importação/exportação de dispositivos também oferecem suporte a certificados de cliente x. 509.

### <a name="c-support"></a>C\# suporte

A classe **RegistryManager** fornece uma maneira de registrar um dispositivo. Em particular, os métodos **AddDeviceAsync** e **UpdateDeviceAsync** habilitar um usuário registrar e atualizar um dispositivo no registro de identidade de dispositivo do Iot Hub. Esses dois métodos levam uma instância de **dispositivo** como entrada. A classe de **dispositivo** inclui uma propriedade de **autenticação** que permite ao usuário especificar primárias e secundários impressões de digitais de certificado de x. 509. A impressão digital representa um hash SHA-1 do certificado x. 509 (armazenado usando codificação de DER binária). Os usuários têm a opção de especificar uma impressão digital principal ou uma impressão digital secundária ou ambos. Impressões digitais primárias e secundários são suportados para manipular os cenários de sobreposição de certificado.

> [AZURE.NOTE] Hub de IoT não exigem ou armazenar o certificado de cliente x. 509 inteiro, somente a impressão digital.

Aqui está um exemplo C\# trecho de código para registrar um dispositivo usando um certificado de cliente x. 509:

```
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-client-certificate-during-runtime-operations"></a>Use um certificado de cliente x. 509 durante operações de tempo de execução

O [dispositivo de IoT Azure SDK para .NET] [ lnk-client-sdk] (versão 1.0.11+) suporta o uso de certificados de cliente x. 509.

### <a name="c-support"></a>C\# suporte

A classe **DeviceAuthenticationWithX509Certificate** suporta a criação de instâncias de  **DeviceClient** usando um certificado de cliente x. 509.

Aqui está um trecho de código de exemplo:

```
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-authentication"></a>Autenticação do dispositivo personalizado

Você pode usar o Hub de IoT [registro de identidade do dispositivo] [ lnk-identity-registry] para configurar credenciais de segurança de cada dispositivo e usando [tokens]de controle de acesso[lnk-sas-tokens]. No entanto, se uma solução IoT já tem um investimento significativo em um esquema de registro e/ou a autenticação de identidade do dispositivo personalizado, você pode integrar este infraestrutura existente com IoT Hub pela criação de um *serviço de token*. Dessa forma, você pode usar outros recursos de IoT em sua solução.

Um serviço de token é um serviço de nuvem personalizados. Ele usa um IoT Hub *política de acesso compartilhado* com permissões de **DeviceConnect** para criar tokens de *escopo do dispositivo* . Esses tokens habilitar um dispositivo para se conectar ao seu hub IoT.

  ![Etapas do padrão de serviço de token][img-tokenservice]

Estas são as principais etapas do padrão token de serviço:

1. Crie uma política de acesso do Hub IoT compartilhado com permissões **DeviceConnect** para seu hub IoT. Você pode criar essa política no [portal do Azure] [ lnk-management-portal] ou programaticamente. O serviço de token usa esta política para assinar os tokens que ele cria.
2. Quando um dispositivo precisa acessar seu hub IoT, ele solicita um token assinado do seu serviço de token. O dispositivo pode autenticar com o seu esquema de registro/autenticação de identidade de dispositivo personalizado para determinar a identidade do dispositivo que o serviço de token usa para criar o token.
3. O serviço de token retorna um token. O token é criado usando `/devices/{deviceId}` como `resourceURI`, com `deviceId` como o dispositivo sendo autenticado. O serviço de token usa a política de acesso compartilhado para construir o token.
4. O dispositivo usa o token diretamente com o hub de IoT.

> [AZURE.NOTE] Você pode usar a classe do .NET [SharedAccessSignatureBuilder] [ lnk-dotnet-sas] ou classe Java [IotHubServiceSasToken] [ lnk-java-sas] para criar um token no seu serviço de token.

O serviço de token pode definir a expiração do token conforme desejado. Quando o token expira, o hub de IoT servidores a conexão do dispositivo. Em seguida, o dispositivo deve solicitar um novo token do serviço de token de. Se você usar um tempo de expiração curto, aumenta a carga do dispositivo e o serviço de token.

Para um dispositivo para se conectar ao seu hub, você deve ainda adicioná-lo no registro de identidade de dispositivo do IoT Hub — mesmo que o dispositivo está usando um token e não é uma chave de dispositivo para se conectar. Portanto, você pode continuar a usar o controle de acesso de cada dispositivo ativando ou desativando identidades de dispositivo no [registro de identidade IoT Hub] [ lnk-identity-registry] quando o dispositivo autentica com um símbolo. Isso reduz os riscos de usando tokens com tempos de expiração longa.

### <a name="comparison-with-a-custom-gateway"></a>Comparação com um gateway personalizado

O padrão de serviço de token é a maneira recomendada para implementar um esquema de registro/autenticação de identidade personalizadas com IoT Hub. É recomendável porque IoT Hub continua a tratar a maioria do tráfego de solução. No entanto, há casos em que o esquema de autenticação personalizada é portanto interligado com o protocolo que um serviço de processamento de todo o tráfego (*gateway personalizado*) é necessário. Um exemplo disto é [segurança de camada de transporte (TLS) e chaves pré-compartilhadas (PSKs)][lnk-tls-psk]. Para obter mais informações, consulte o [gateway de protocolo] [ lnk-protocols] tópico.

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem mais informações sobre como controlar o acesso ao seu hub IoT.

## <a name="iot-hub-permissions"></a>Permissões de IoT Hub

A tabela a seguir lista as permissões que você pode usar para controlar o acesso ao seu hub IoT.

| Permissão            | Anotações |
| --------------------- | ----- |
| **RegistryRead**      | Concede acesso de leitura para o registro do dispositivo de identidade. Para obter mais informações, consulte o [registro de identidade do dispositivo][lnk-identity-registry]. |
| **RegistryReadWrite** | Concessões de leitura e gravação acesso para o registro do dispositivo de identidade. Para obter mais informações, consulte o [registro de identidade do dispositivo][lnk-identity-registry]. |
| **ServiceConnect**    | Concede acesso à nuvem voltados para o serviço de comunicação e monitoramento pontos de extremidade. Por exemplo, ele concede permissão aos serviços de nuvem de back-end para receber mensagens de dispositivo na nuvem, enviar mensagens de nuvem para dispositivo e recuperar as confirmações de entrega correspondente. |
| **DeviceConnect**     | Concede o acesso a pontos de extremidade de comunicação voltados para o dispositivo. Por exemplo, ele concede permissão para enviar mensagens de dispositivo na nuvem e receber mensagens de nuvem para dispositivo. Essa permissão é usada pelos dispositivos. |

## <a name="additional-reference-material"></a>Materiais de referência adicionais

Outros tópicos de referência no guia do desenvolvedor incluem:

- [Pontos de extremidade do IoT Hub] [ lnk-endpoints] descreve os vários pontos de extremidade que cada hub IoT expõe para operações de gerenciamento e de tempo de execução.
- [Cotas e Throttling] [ lnk-quotas] descreve as cotas que se aplicam ao serviço IoT Hub e o comportamento de otimização esperar quando você usar o serviço.
- [Hub de IoT SDKs de dispositivo e serviço] [ lnk-sdks] listas no idioma vários SDKs você um uso ao desenvolver dispositivo e serviço aplicativos que interagem com IoT Hub.
- [Linguagem de consulta para gêmeos, métodos e trabalhos] [ lnk-query] descreve a linguagem de consulta que você pode usar para recuperar informações do IoT Hub sobre Gêmeos de dispositivo, métodos e trabalhos.
- [Suporte de IoT Hub MQTT] [ lnk-devguide-mqtt] fornece mais informações sobre o suporte de IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a controlar o acesso do IoT Hub, você pode estar interessado nos seguintes tópicos de guia desenvolvedor:

- [Usar Gêmeos de dispositivo para sincronizar o estado e configurações][lnk-devguide-device-twins]
- [Invocar um método direto em um dispositivo][lnk-devguide-directmethods]
- [Agendar trabalhos em vários dispositivos][lnk-devguide-jobs]

Se você quiser experimentar alguns dos conceitos descritos neste artigo, você pode estar interessado nos seguintes tutoriais do IoT Hub:

- [Introdução ao Azure IoT Hub][lnk-getstarted-tutorial]
- [Como enviar mensagens de nuvem para dispositivo com IoT Hub][lnk-c2d-tutorial]
- [Como processar mensagens de dispositivo à nuvem IoT Hub][lnk-d2c-tutorial]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
