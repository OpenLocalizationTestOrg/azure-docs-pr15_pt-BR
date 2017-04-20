# <a name="securing-your-iot-deployment"></a>Protegendo sua implantação IoT

Este artigo fornece o próximo nível de detalhe protegerão a infraestrutura baseada no Windows Azure IoT Internet das coisas (IoT). Ele vincula a detalhes de nível de implementação para configuração e implantação de cada componente. Ele também oferece comparações e escolhas entre vários métodos concorrentes.

Protegendo a implantação do Windows Azure IoT pode ser dividido nas seguintes áreas de três segurança:

- **Segurança de dispositivo**: Protegendo o dispositivo IoT enquanto ele é implantado à solta.
- **Segurança de Conexão**: assegurar que todos os dados transmitidos entre o dispositivo IoT e IoT Hub é confidenciais e à prova.
- **Segurança na nuvem**: fornecendo meios de proteger dados enquanto ele é movido por todos e é armazenado na nuvem.

![Três áreas de segurança][img-overview]

## <a name="secure-device-provisioning-and-authentication"></a>Provisionamento de dispositivo seguro e autenticação

O pacote do Windows Azure IoT protege dispositivos IoT por dois métodos a seguir:

- Fornecendo uma chave de identidade exclusiva (tokens de segurança) para cada dispositivo, que pode ser usado pelo dispositivo para se comunicar com o IoT Hub.

- Usando um [certificado x. 509] no dispositivo[ lnk-x509] e a chave privada como um meio para autenticar o dispositivo para o IoT Hub. Esse método de autenticação garante que a chave privada no dispositivo não é conhecida fora do dispositivo a qualquer momento, fornecendo um nível mais alto de segurança.

O método de token de segurança fornece autenticação para cada chamada feita pelo dispositivo ao IoT Hub associando a chave simétrica para cada chamada. Autenticação baseada em x. 509 permite que a autenticação de um dispositivo IoT na camada física como parte do estabelecimento da conexão TLS. O método baseada em tokens de segurança pode ser usado sem a autenticação de x. 509, que é um padrão de menos seguro. A escolha entre os dois métodos principalmente é determinada pelo seguro como a autenticação de dispositivo precisa estar e a disponibilidade de armazenamento seguro no dispositivo (para armazenar a chave privada com segurança).

## <a name="iot-hub-security-tokens"></a>Tokens de segurança do IoT Hub

Hub de IoT usa tokens de segurança para autenticar dispositivos e serviços para evitar o envio de chaves na rede. Além disso, os tokens de segurança são limitados em escopo e a validade de tempo. SDKs do Azure IoT Hub gerar automaticamente tokens sem exigir qualquer configuração especial. Alguns cenários, no entanto, exigem que o usuário gerar e usar os tokens de segurança diretamente. Eles incluem o uso direto do mostra MQTT, AMQP ou HTTP, ou a implementação do serviço de token padrão.

Mais detalhes na estrutura do token de segurança e seu uso podem ser encontrados nos seguintes artigos:

-   [Estrutura do token de segurança][lnk-security-tokens]
-   [Usando tokens SAS como um dispositivo][lnk-sas-tokens]

Cada IoT Hub tem um [Registro de identidade do dispositivo] [ lnk-identity-registry] que podem ser usados para criar recursos por dispositivo no serviço, como uma fila que contém mensagens de nuvem para dispositivo em trânsito e para permitir o acesso a pontos de extremidade voltados para o dispositivo. Registro de identidade do IoT Hub fornece armazenamento seguro de identidades de dispositivo e chaves de segurança para uma solução. Individuais ou grupos de identidades de dispositivo podem ser adicionados a uma lista de permissões ou uma lista de bloqueios, habilitando o controle completo sobre o acesso ao dispositivo. Os artigos a seguir fornecem mais detalhes sobre a estrutura do registro de identidade do dispositivo e suporte para as operações.

[Hub de IoT oferece suporte aos protocolos como HTTP, AMQP e MQTT][lnk-protocols]. Cada um desses protocolos usam tokens de segurança do dispositivo IoT IoT hub maneiras diferentes:

- AMQP: Segurança de simples e baseada em declarações AMQP SASL ({policyName}@sas.root.{iothubName} no caso de tokens de Hub nível; {deviceId} em caso de tokens de escopo de dispositivo).

- MQTT: De conexão pacotes utiliza {deviceId} como {}, {IoThubhostname} / {deviceId} no campo **nome de usuário** e um token SAS no campo **senha** .

- HTTP: Token válido é no cabeçalho da solicitação de autorização.

Registro de identidade do IoT Hub dispositivo pode ser usado para configurar as credenciais de segurança de cada dispositivo e controle de acesso. No entanto, se uma solução IoT já tiver um investimento significativo em um [esquema de registro e/ou autenticação de identidade personalizada no dispositivo][lnk-custom-auth], ele pode ser integrado em uma infra-estrutura existente com IoT Hub criando um serviço de token.

### <a name="x509-certificate-based-device-authentication"></a>Autenticação do dispositivo baseado no certificado x. 509

O uso de um [certificado de x. 509 baseados no dispositivo] [ lnk-use-x509] e seu par de chaves pública e privada associada permite que a autenticação adicional na camada física. A chave privada é armazenada com segurança no dispositivo e não é detectável fora do dispositivo. O certificado x. 509 contém informações sobre o dispositivo, como ID do dispositivo e outros detalhes organizacionais. Uma assinatura do certificado é gerada utilizando a chave privada.

Fluxo de provisionamento de dispositivo de alto nível:

- Associe um identificador para um dispositivo físico – identity do dispositivo e/ou o certificado x. 509 associados ao dispositivo durante o dispositivo de fabricação ou preparação.

- Crie uma entrada de identidade correspondente no IoT Hub – identity do dispositivo e informações de dispositivo associado no registro do dispositivo de Hub IoT.

- Armazene com segurança de impressão digital do certificado x. 509 no registro do dispositivo IoT Hub.

### <a name="root-certificate-on-device"></a>Certificado raiz no dispositivo

Ao estabelecer uma conexão TLS segura com IoT Hub, o dispositivo IoT autentica IoT Hub usando um certificado raiz que faz parte do SDK do dispositivo. Para o cliente C SDK o certificado está localizado na pasta "\\c\\certificados" na raiz do repo. Embora esses certificados raiz sejam vida útil longa, eles ainda poderão expirar ou ser revogados. Se não houver nenhuma forma de atualizar o certificado no dispositivo, o dispositivo não pode ser capaz de se conectar subsequentemente para o IoT Hub (ou qualquer outro serviço de nuvem). Ter um meios para atualizar o certificado raiz depois que o dispositivo IoT for implantado efetivamente será atenuar esse risco.

## <a name="securing-the-connection"></a>Protegendo a conexão

Conexão de Internet entre o dispositivo IoT e IoT Hub está protegido usando o padrão de segurança de camada de transporte (TLS). Azure IoT suporta [TLS 1.2][lnk-tls12], TLS 1.1 e o TLS 1.0, nesta ordem. Suporte para TLS 1.0 é fornecido apenas para compatibilidade com versões anteriores. É recomendável usar o TLS 1.2 desde que ele fornece segurança máxima.

Pacote do Azure IoT suporta os seguintes conjuntos de codificação, nesta ordem.

| Conjunto de codificação | Comprimento |
|--------------|--------|
| TLS\_ECDHE\_RSA\_com\_AES\_256\_CBC\_SHA384 (0xc028) ECDH secp384r1 (eq. FS 7680 bits RSA) | 256    |
| TLS\_ECDHE\_RSA\_com\_AES\_128\_CBC\_SHA256 (0xc027) ECDH secp256r1 (eq. FS 3072 bits RSA) | 128    |
| TLS\_ECDHE\_RSA\_com\_AES\_256\_CBC\_SHA (0xc014) secp384r1 ECDH (eq. FS 7680 bits RSA)           | 256    |
| TLS\_ECDHE\_RSA\_com\_AES\_128\_CBC\_SHA (0xc013) secp256r1 ECDH (eq. FS 3072 bits RSA)           | 128    |
| TLS\_RSA\_com\_AES\_256\_GCM\_SHA384 (0x9d) | 256    |
| TLS\_RSA\_com\_AES\_128\_GCM\_SHA256 (0x9c) | 128    |
| TLS\_RSA\_com\_AES\_256\_CBC\_SHA256 (0x3d) | 256    |
| TLS\_RSA\_com\_AES\_128\_CBC\_SHA256 (0x3c) | 128    |
| TLS\_RSA\_com\_AES\_256\_CBC\_SHA (0x35)    | 256    |
| TLS\_RSA\_com\_AES\_128\_CBC\_SHA (0x2f)    | 128    |
| TLS\_RSA\_com\_3DES\_EDE\_CBC\_SHA (0xa)    | 112    |

## <a name="securing-the-cloud"></a>Protegendo a nuvem

Hub do Azure IoT permite a definição de [políticas de controle de acesso] [ lnk-protocols] para cada chave de segurança. Ele usa o seguinte conjunto de permissões para conceder acesso a cada um dos pontos de extremidade do IoT Hub. Permissões limitam o acesso a um Hub IoT baseado na funcionalidade.

- **RegistryRead**. Concede acesso de leitura ao registro de identidade do dispositivo. Para obter mais informações, consulte o [Registro de identidade do dispositivo][lnk-identity-registry].

- **RegistryReadWrite**. Concede ao ler e gravar o acesso em registro de identidade do dispositivo. Para obter mais informações, consulte o [Registro de identidade do dispositivo][lnk-identity-registry].

- **ServiceConnect**. Concede acesso a comunicação e o monitoramento de pontos de extremidade voltados para o serviço em nuvem. Por exemplo, ele concede permissão para os serviços de nuvem de back-end recebe mensagens de dispositivo para nuvem, enviar mensagens de nuvem para dispositivo e recuperar as confirmações de entrega correspondente.

- **DeviceConnect**. Concede acesso aos pontos de extremidade de comunicação voltados para o dispositivo. Por exemplo, ele concede permissão para enviar mensagens de dispositivo para nuvem e receber mensagens de nuvem para dispositivo. Esta permissão é usada pelos dispositivos.

Existem duas maneiras de obter permissões de **DeviceConnect** com IoT Hub com [tokens de segurança][lnk-sas-tokens]: usando uma chave de identidade do dispositivo, ou uma chave de política de acesso compartilhado. Além disso, é importante observar que toda a funcionalidade acessível a partir de dispositivos é exposta pelo design em pontos de extremidade com o prefixo `/devices/{deviceId}`.

[Componentes de serviço só podem gerar tokens de segurança] [ lnk-service-tokens] usando compartilhado conceder as permissões apropriadas de políticas de acesso.

Hub de IoT Azure e outros serviços que podem ser parte da solução de permitem o gerenciamento de usuários usando o Windows Azure Active Directory.

Dados incluídos por Azure IoT Hub podem ser consumidos por uma variedade de serviços, como o Azure Stream Analytics e o armazenamento de blob Azure. Esses serviços permitem o acesso de gerenciamento. Leia mais sobre esses serviços e as opções disponíveis abaixo:

- [Azure DocumentDB][lnk-docdb]: um serviço de banco de dados escalável, totalmente indexados para dados semi-estruturados que gerencia os metadados para os dispositivos que você provisionar, como as propriedades de segurança, configuração e atributos. DocumentDB oferece o processamento de alto desempenho e alta taxa de transferência, não reconhece o esquema de indexação de dados e uma interface de consulta avançada do SQL.

- [Análise do Azure Stream][lnk-asa]: em tempo real fluxo de processamento na nuvem que permite que você rapidamente desenvolver e implantar uma solução de análise de baixo custo para descobrir em tempo real ideias de dispositivos, sensores, infraestrutura e aplicativos. Os dados desse serviço totalmente gerenciado podem ser dimensionados para qualquer volume ao mesmo tempo, resiliência, baixa latência e alta taxa de transferência.

- [Serviços do Azure App][lnk-appservices]: uma plataforma de nuvem para criar poderosa web e aplicativos móveis que se conectam aos dados em qualquer lugar; na nuvem ou no local. Crie aplicativos móveis para iOS, Android e Windows o envolvimento. Integre seu Software como um serviço (SaaS) e aplicativos corporativos com conectividade imediata de dezenas de serviços em nuvem e aplicativos empresariais. O código no IDE (.NET, NodeJS, PHP, Python ou Java) para criar aplicativos da web e APIs mais rapidamente e de idioma favorito.

- [Lógica de aplicativos][lnk-logicapps]: recurso de aplicativos da lógica do serviço de aplicativo do Windows Azure ajuda integrar sua solução IoT aos seus sistemas de linha de negócios existentes e automatizar os processos de fluxo de trabalho. Lógica de aplicativos permite aos desenvolvedores de fluxos de trabalho de design que iniciar a partir de um disparador e, em seguida, executam uma série de etapas — regras e ações que usam conectores poderosas para integrar com seus processos de negócios. Lógica Apps oferece imediata de conectividade para um grande ecossistema de SaaS, baseadas em nuvem e aplicativos no local.

- [Armazenamento de blob Azure][lnk-blob]: armazenamento em nuvem confiável e econômica para os dados que seus dispositivos de enviar para a nuvem.

## <a name="conclusion"></a>Conclusão

Este artigo fornece detalhes de nível de projetar e implantar uma infra-estrutura de IoT usando o Windows Azure IoT de visão geral da implementação. A configuração de cada componente a ser segura é chave no protegerão a infraestrutura IoT geral. As escolhas de design disponíveis no Azure IoT fornecem algum nível de flexibilidade e escolha; No entanto, cada opção pode ter implicações de segurança. É recomendável que cada uma dessas opções seja avaliado por meio de uma avaliação de risco/custo.

[img-overview]: media/iot-secure-your-deployment/overview.png

[lnk-security-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure
[lnk-sas-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-as-a-device
[lnk-identity-registry]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-protocols]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-custom-auth]: ../articles/iot-hub/iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: http://www.itu.int/rec/T-REC-X.509-201210-I/en
[lnk-use-x509]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-tls12]: https://tools.ietf.org/html/rfc5246
[lnk-service-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#using-security-tokens-from-service-components
[lnk-docdb]: https://azure.microsoft.com/services/documentdb/
[lnk-asa]: https://azure.microsoft.com/services/stream-analytics/
[lnk-appservices]: https://azure.microsoft.com/services/app-service/
[lnk-logicapps]: https://azure.microsoft.com/services/app-service/logic/
[lnk-blob]: https://azure.microsoft.com/services/storage/
