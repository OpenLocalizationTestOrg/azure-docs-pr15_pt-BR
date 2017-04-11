<properties 
    pageTitle="Usando Axinom para fornecer Widevine licenças aos serviços de mídia do Azure | Microsoft Azure" 
    description="Este artigo descreve como você pode usar serviços de mídia do Azure (AMS) para distribuir um fluxo dinamicamente criptografada por AMS com PlayReady e Widevine DRMs. A licença PlayReady proveniente de servidor de licenças do PlayReady de serviços de mídia e Widevine licença é fornecida pelo servidor de licenças de Axinom." 
    services="media-services" 
    documentationCenter="" 
    authors="willzhan" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"   
    ms.author="willzhan;Mingfeiy;rajputam;Juliako"/>

#<a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Usando Axinom para fornecer Widevine licenças aos serviços de mídia do Azure  

> [AZURE.SELECTOR]
- [castLabs](media-services-castlabs-integration.md)
- [Axinom](media-services-axinom-integration.md)

##<a name="overview"></a>Visão geral

Serviços de mídia Azure (AMS) adicionou proteção dinâmica Google Widevine (consulte o [blog do Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) para obter detalhes). Além disso, Player de mídia do Azure (AMP) também adicionou suporte de Widevine (consulte [AMP do documento](http://amp.azure.net/libs/amp/latest/docs/) para obter detalhes). Isso é uma grande conquista de streaming de conteúdo de traço protegido por CENC com multi-native-DRM (PlayReady e Widevine) em navegadores modernos equipados com MSE e EME.

Começando com a mídia serviços .NET SDK versão 3.5.2, serviços de mídia permite que você configure o modelo de licença de Widevine e obter Widevine licenças. Você também pode usar os seguintes parceiros AMS para ajudá-lo a oferecer Widevine licenças: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

Este artigo descreve como integrar e testar o servidor de licenças de Widevine gerenciada pelo Axinom. Especificamente, ele cubra:  

- Configurando criptografia comuns dinâmico com multi-DRM (PlayReady e Widevine) com URLs de aquisição de licença correspondentes;
- Gerando um símbolo JWT para atender aos requisitos de servidor de licença;
- Desenvolvimento de aplicativo de Player de mídia do Azure que trata a aquisição de licença com a autenticação de token de JWT;

O sistema concluído e o fluxo de conteúdo que chave, chave ID, propagação chave, token JTW e suas declarações pode ser melhor descrita o diagrama a seguir.

![TRAÇO e CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

##<a name="content-protection"></a>Proteção de conteúdo

Para configurar proteção dinâmica e política de entrega de chave, consulte blog do Mingfei: [como configurar Widevine embalagem com os serviços de mídia do Azure](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Você pode configurar proteção de CENC dinâmica com multi-DRM para traço streaming tendo o seguinte:

1. Proteção de PlayReady para MS Edge e IE11, que pode ter um restrições de autorização token. A política de restrita token deve ser acompanhada por um token emitido por um seguro Token STS (serviço), como o Active Directory do Azure;
1. Proteção de Widevine para o Chrome, ele pode exigir autenticação de token com token emitido por outra STS. 

Consulte [Geração de Token de JWT](media-services-axinom-integration.md#jwt-token-generation) seção para por que o Azure Active Directory não pode ser usado como um STS Widevine servidor do Axinom de licenças.

###<a name="considerations"></a>Considerações

1. Você deve usar o Axinom especificado ID para gerar a chave de conteúdo para configurar o serviço de entrega de chave de chave de propagação chave (8888000000000000000000000000000000000000) e seus gerado ou selecionado. Servidor de licenças de Axinom irá emitir todas as licenças que contém as chaves de conteúdo com base na mesma chave propagação, que é válida para teste e produção.
1. A URL de aquisição de licença Widevine para teste: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense). HTTP e HTTS são permitidos.

##<a name="azure-media-player-preparation"></a>Preparação do Player de mídia do Microsoft Azure

AMP v1.4.0 oferece suporte a reprodução de conteúdo de AMS dinamicamente compactado com PlayReady e Widevine DRM.
Se o servidor de licenças de Widevine não exige autenticação de token, não há nada adicional que você precisa fazer para testar o conteúdo de um traço protegido pelo Widevine. Por exemplo, a equipe de AMP fornece uma simples [amostra](http://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevine_notoken.html), onde você pode ver o funcionamento na borda e IE11 com PlayReady e Chrome com Widevine.
O servidor de licença de Widevine fornecido pelo Axinom requer autenticação de token de JWT. O token JWT deve ser enviada com solicitação de licença por meio de um cabeçalho HTTP "X-AxDRM-mensagem". Para essa finalidade, você precisa adicionar o seguinte javascript na página da web que hospeda AMP antes de definir a fonte:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

O restante do código AMP é API AMP padrão como documento AMP [aqui](http://amp.azure.net/libs/amp/latest/docs/).

Observe que o javascript acima para cabeçalho de autorização personalizada de configuração ainda é uma abordagem de curto prazo antes do oficial abordagem de longo prazo em AMP é lançada.

##<a name="jwt-token-generation"></a>Geração de Token de JWT

Servidor de licença de Axinom Widevine para teste requer autenticação de token de JWT. Além disso, uma das declarações no token de JWT é de um tipo de objeto complexo em vez de tipo de dados primitivo.

Infelizmente, Azure AD só pode emitir tokens JWT com tipos primitivos. Da mesma forma, API do .NET Framework (System.IdentityModel.Tokens.SecurityTokenHandler e JwtPayload) somente permite a entrada de tipo de objeto complexo como declarações. No entanto, as declarações ainda são serializadas como cadeia de caracteres. Portanto nós não pode usar qualquer um dos dois para gerar o token JWT para Widevine solicitação de licença.


De John Sheehan [pacote JWT Nuget](https://www.nuget.org/packages/JWT) atende às necessidades, vamos usar este pacote Nuget.

Abaixo está o código para gerar token JWT com as declarações necessárias conforme necessário pelo servidor de licenças de Axinom Widevine para teste:

    
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;
    
    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.
    
                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };
    
                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);
    
                return token;
            }
    
            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }
    
                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }
    
                return HexAsBytes;
            }
    
        }  
    
    }  

Servidor de licenças de Axinom Widevine

    <add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

###<a name="considerations"></a>Considerações

1.  Embora o serviço de entrega de licença AMS PlayReady requer "portador =" precede um token de autenticação, servidor de licenças de Axinom Widevine não usá-lo.
2.  A chave de comunicação Axinom é usada como chave de assinatura. Observe que a chave é uma cadeia de caracteres hexadecimal, no entanto, ele deve ser tratado como uma série de bytes não uma cadeia de caracteres quando a codificação. Isso é feito pelo método ConvertHexStringToByteArray.

##<a name="retrieving-key-id"></a>Recuperando ID da chave

Você pode ter notado que no código para gerar um JWT token, chave ID é necessária. Como as necessidades de token de JWT esteja pronto antes de player de carregamento AMP, chave ID precisa ser recuperadas para poder gerar o token JWT.

Claro, há várias maneiras de obter mantenha da chave de ID. Por exemplo, um pode armazenar ID de chave junto com metadados de conteúdo em um banco de dados. Ou você pode recuperar ID de arquivo MPD traço (descrição de apresentação de mídia) de chave. O código a seguir é para o último.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }
    
        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();
    
        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);
    
        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }
    
        return key_id;
    }

##<a name="summary"></a>Resumo

Com o mais recente adição de suporte de Widevine na proteção do conteúdo de serviços de mídia do Azure e o Azure Media Player, estamos capazes de implementar o streaming de traço + Multi-native-DRM (PlayReady + Widevine) com o serviço de licença de ambas as PlayReady no servidor de licença de AMS e Widevine a partir Axinom para os seguintes navegadores modernos:

- Chrome
- Microsoft Edge no Windows 10
- IE 11 no Windows 8.1 e Windows 10
- Também há suporte para Firefox (Desktop) e o Safari no Mac (não iOS) via Silverlight e a mesma URL com o Player de mídia do Azure

Os parâmetros a seguir são necessárias no servidor de licença de solução mini aproveitando Axinom Widevine. Com exceção de chave ID, o restante dos parâmetros são fornecidos pelo Axinom com base em sua configuração de servidor de Widevine.


Parâmetro|Como ele é usado
---|---
ID de chave de comunicação|Deve ser incluído como valor da declaração "com_key_id" no símbolo JWT (consulte [Esta](media-services-axinom-integration.md#jwt-token-generation) seção).
Chave de comunicação|Deve ser usado como a chave de assinatura de token JWT (consulte [Esta](media-services-axinom-integration.md#jwt-token-generation) seção).
Chave propagação|Deve ser usada para gerar a chave de conteúdo com qualquer conteúdo determinado ID de chave (consulte [Esta](media-services-axinom-integration.md#content-protection) seção).
URL de aquisição de licença Widevine|Deve ser usado na configuração de política de entrega de ativos para traço streaming (consulte [Esta](media-services-axinom-integration.md#content-protection) seção).
ID da chave conteúdo|Devem ser incluídos como parte do valor de declaração de mensagem de qualificação do token JWT (consulte [Esta](media-services-axinom-integration.md#jwt-token-generation) seção). 


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###<a name="acknowledgments"></a>Confirmações 

Podemos gostaria de confirmar as seguintes pessoas quem contribuíram para criar este documento: Kristjan Jõgi de Axinom, Mingfei Yan e Rajput Amit.
