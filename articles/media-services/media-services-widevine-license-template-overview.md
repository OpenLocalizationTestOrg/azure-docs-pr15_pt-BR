<properties 
    pageTitle="Visão geral do modelo de licença Widevine | Microsoft Azure" 
    description="Este tópico fornece uma visão geral de um modelo de licença de Widevine que usadas para configurar Widevine licenças." 
    authors="juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>

#<a name="widevine-license-template-overview"></a>Visão geral do modelo de licença Widevine

##<a name="overview"></a>Visão geral

Serviços de mídia do Azure agora permite que você configure e solicitar Widevine licenças. Quando o player de usuário final tenta reproduzir seu conteúdo Widevine protegido, uma solicitação é enviada para o serviço de entrega de licença para obter uma licença. Se o serviço de licença aprovar a solicitação, ele emite a licença que é enviada para o cliente e pode ser usada para descriptografar e reproduzir o conteúdo especificado.

Solicitação de licença Widevine é formatada como uma mensagem JSON.  

Observe que você pode optar por criar uma mensagem vazia sem valores apenas "{}" e um modelo de licença será criado com todos os padrões.  

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

##<a name="json-message"></a>Mensagem JSON

Nome | Valor | Descrição
---|---|---
carga |Cadeia de caracteres codificada em Base64 |A solicitação de licença enviada por um cliente. 
content_id | Cadeia de caracteres codificada em Base64|Identificador usado para originar KeyId(s) e chaves de conteúdo para cada content_key_specs.track_type.
provedor |cadeia de caracteres |Usado para procurar políticas e chaves de conteúdo. Necessário.
nome_da_diretiva | cadeia de caracteres |Nome de uma política de registrado anteriormente. Opcional
allowed_track_types | enumeração  | SD_ONLY ou SD_HD. Controles que conteúdo teclas devem ser incluídos na licença
content_key_specs | matriz de JSON estruturas, consulte **Especificações de chave conteúdo** abaixo | Um mais refinado refinado controle na qual conteúdo teclas para retornar. Consulte especificação de chave conteúdo abaixo para obter detalhes.  Somente um allowed_track_types e content_key_specs pode ser especificado. 
use_policy_overrides_exclusively | booliano. VERDADEIRO ou falso | Use atributos de política especificados pelo policy_overrides e omitir todos política armazenada anteriormente.
policy_overrides | JSON estruturar, consulte a **Política substitui** abaixo | Configurações de política para esta licença.  No caso desse ativo possui uma política predefinida, esses valores especificados serão usados. 
session_init | JSON estruturar, consulte **Inicialização da sessão** abaixo | Dados opcionais passados à licença.
parse_only | booliano. VERDADEIRO ou falso | A solicitação de licença é analisada, mas sem licença é emitida. No entanto, os valores de formulário a solicitação de licença são retornados na resposta.  

##<a name="content-key-specs"></a>Especificações de chave de conteúdo 

Se uma política preexistente existir, não é necessário para especificar qualquer um dos valores na especificação de chave de conteúdo.  A política preexistente associada com esse conteúdo será usada para determinar a proteção de saída como HDCP e CGMS.  Se uma política preexistente não estiver registrada com o servidor de licença de Widevine, o provedor de conteúdo pode inserir os valores na solicitação de licença.   


Cada content_key_specs deve ser especificado para todas as faixas, independentemente da opção use_policy_overrides_exclusively. 


Nome | Valor | Descrição
---|---|---
content_key_specs. track_type | cadeia de caracteres | Um nome de tipo de controle. Se content_key_specs for especificado na solicitação de licença, verifique se especificar que todos os tipos de controlar explicitamente. Falha ao fazer isso resultará em falha reprodução últimos 10 segundos. 
content_key_specs  <br/> security_level | UInt32 | Define os requisitos de eficiência do cliente para reprodução. <br/> 1 - caixa branca baseada em software de criptografia é necessária. <br/> 2 - é necessário Cripto software e um decodificador ofuscado. <br/> 3 - as operações de criptografia e material de chave devem ser executadas em um ambiente de execução confiável foi feito de hardware. <br/> 4 - a criptografia e decodificar de conteúdo devem ser executadas em um ambiente de execução confiável foi feito de hardware.  <br/> 5 - a criptografia, decodificação e tudo manuseio da mídia (compactado e descompactado) devem ser tratados em um ambiente de execução confiável foi feito de hardware.  
content_key_specs <br/> required_output_protection.hDC | string - um destes: HDCP_NONE, HDCP_V1, HDCP_V2 | Indica se HDCP é necessária
content_key_specs <br/>chave | Na Base 64 <br/>cadeia de caracteres codificada|Chave de conteúdo a ser usado para este controle. Se especificado, é necessário o track_type ou key_id.  Essa opção permite que o provedor de conteúdo inserir a chave de conteúdo para esta faixa em vez de permitir que o servidor de licenças de Widevine gerar ou de uma chave de pesquisa.
content_key_specs.key_id| Cadeia de caracteres binária, codificado na Base 64 16 bytes | Identificador exclusivo para a chave. 


##<a name="policy-overrides"></a>Substituições de política 

Nome | Valor | Descrição
---|---|---
policy_overrides. can_play | booliano. VERDADEIRO ou falso | Indica que a reprodução do conteúdo é permitida. O padrão é falso.
policy_overrides. can_persist | booliano. VERDADEIRO ou falso |Indica que a licença pode ser persistentes ao armazenamento não-voláteis para uso offline. O padrão é falso.
policy_overrides. can_renew | booliano verdadeiro ou falso |Indica que a renovação desta licença é permitida. Se verdadeiro, a duração da licença pode ser estendida por pulsação. O padrão é falso. 
policy_overrides. license_duration_seconds | Int64 | Indica a janela de tempo para esta licença específica. Um valor de 0 indica que não há nenhum limite para a duração. Padrão é 0. 
policy_overrides. rental_duration_seconds | Int64 | Indica a janela de tempo enquanto reprodução é permitida. Um valor de 0 indica que não há nenhum limite para a duração. Padrão é 0. 
policy_overrides. playback_duration_seconds | Int64 | A janela de visualização de tempo após a reprodução é iniciada dentro da duração de licença. Um valor de 0 indica que não há nenhum limite para a duração. Padrão é 0. 
policy_overrides. renewal_server_url |cadeia de caracteres | Todas as solicitações de pulsação (renovação) para esta licença deverão ser direcionadas para a URL especificada. Este campo é usado apenas se can_renew for verdadeiro.
policy_overrides. renewal_delay_seconds |Int64 |Quantos segundos após license_start_time, antes de tentar primeiro renovação. Este campo é usado apenas se can_renew for verdadeiro. O padrão é 0 
policy_overrides. renewal_retry_interval_seconds | Int64 | Especifica o atraso em segundos entre as solicitações de renovação de licença subsequentes, no caso de falha. Este campo é usado apenas se can_renew for verdadeiro. 
policy_overrides. renewal_recovery_duration_seconds | Int64 | A janela de tempo, na qual reprodução tem permissão para continuar enquanto renovação é tentativas, ainda malsucedido devido a problemas de back-end com o servidor de licença. Um valor de 0 indica que não há nenhum limite para a duração. Este campo é usado apenas se can_renew for verdadeiro.
policy_overrides. renew_with_usage | booliano verdadeiro ou falso |Indica que a licença deverão ser enviada para renovação quando uso é iniciado. Este campo é usado apenas se can_renew for verdadeiro. 

##<a name="session-initialization"></a>Inicialização da sessão

Nome | Valor | Descrição
---|---|---
provider_session_token | Cadeia de caracteres codificada em Base64 |Esse token de sessão é passado de volta da licença e continuará a existir no renovação subsequentes.  O token de sessão não serão mantidas além sessões. 
provider_client_token | Cadeia de caracteres codificada em Base64 | Símbolo de cliente envie de volta à resposta de licença.  Se a solicitação de licença contém um token de cliente, esse valor será ignorado. O token de cliente irá persistir além sessões de licença.
override_provider_client_token | booliano. VERDADEIRO ou falso |Se false e a solicitação de licença contiver um token de cliente, use o símbolo da solicitação mesmo que um token de cliente foi especificado nesta estrutura.  Se verdadeiro, sempre use o token especificado nesta estrutura.

##<a name="configure-your-widevine-licenses-using-net-types"></a>Configurar suas licenças Widevine usando tipos de .NET

Serviços de mídia fornece APIs de .NET que permitem que você configure suas licenças de Widevine. 

###<a name="classes-as-defined-in-the-media-services-net-sdk"></a>Classes conforme definido no SDK do .NET de serviços de mídia

A seguir estão as definições desses tipos.

    public class WidevineMessage
    {
        public WidevineMessage();
    
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

###<a name="example"></a>Exemplo

O exemplo a seguir mostra como usar APIs do .NET para configurar uma licença de Widevine simple.

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Consulte também

[Usando criptografia comuns PlayReady e/ou Widevine dinâmico](media-services-protect-with-drm.md)
