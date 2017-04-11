<properties 
    pageTitle="Usando castLabs para fornecer Widevine licenças aos serviços de mídia do Azure | Microsoft Azure" 
    description="Este artigo descreve como você pode usar serviços de mídia do Azure (AMS) para distribuir um fluxo dinamicamente criptografada por AMS com PlayReady e Widevine DRMs. A licença PlayReady proveniente de servidor de licenças do PlayReady de serviços de mídia e Widevine licença é fornecida pelo servidor de licenças de castLabs." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="Mingfeiy;willzhan;Juliako"/>


#<a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Usando castLabs para fornecer Widevine licenças aos serviços de mídia do Azure

> [AZURE.SELECTOR]
- [Axinom](media-services-axinom-integration.md)
- [castLabs](media-services-castlabs-integration.md)

##<a name="overview"></a>Visão geral

Este artigo descreve como você pode usar serviços de mídia do Azure (AMS) para distribuir um fluxo dinamicamente criptografada por AMS com PlayReady e Widevine DRMs. A licença PlayReady proveniente de servidor de licenças do PlayReady de serviços de mídia e Widevine licença é fornecida pelo servidor de licenças de **castLabs** .

Para reprodução streaming conteúdo protegido por CENC (PlayReady e/ou Widevine), você pode usar o [Player de mídia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Consulte o [documento de AMP](http://amp.azure.net/libs/amp/latest/docs/) para obter detalhes.

O diagrama a seguir demonstra um alto nível serviços de mídia do Azure e arquitetura de integração de castLabs.

![integração](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

##<a name="typical-system-set-up"></a>Sistema típico configurar

- Conteúdo de mídia está armazenado no AMS.
- IDs de chave de chaves de conteúdo são armazenados em castLabs e AMS.
- castLabs e AMS têm autenticação de token integrada. As seções a seguir discutem tokens de autenticação. 
- Quando um cliente solicitar fluxo de vídeo, o conteúdo é dinamicamente criptografado com **Criptografia comuns** (CENC) e dinamicamente empacotado pelos AMS Streaming suave e traço. Também fornecemos PlayReady M2TS criptografia de fluxo de ensino fundamental para protocolo de streaming de HLS.
- Licença PlayReady é recuperada do servidor de licenças de AMS e Widevine licença é recuperada do servidor de licenças de castLabs. 
- Media Player automaticamente decide qual licença para buscar baseada na funcionalidade da plataforma de cliente. 

##<a name="authentication-token-generation-for-getting-a-license"></a>Geração de token de autenticação para obter uma licença

CastLabs e AMS suporte ao formato de token JWT (JSON Web Token) usado para autorizar uma licença. 

###<a name="jwt-token-in-ams"></a>Token JWT de AMS 

A tabela a seguir descreve token JWT de AMS. 

Emissor|Cadeia de caracteres do emissor do escolhido seguro Token STS (serviço)
---|---
Público-alvo|Cadeia de caracteres de audiência do STS usado
Declarações|Um conjunto de declarações
NotBefore|Começar a validade do token
Expirar|Final de validade do token
SigningCredentials|A chave que é compartilhada entre o servidor de licenças do PlayReady, castLabs servidor de licenças e STS, pode ser simétrica ou assimétricos chave.

###<a name="jwt-token-in-castlabs"></a>Token JWT de castLabs

A tabela a seguir descreve token JWT de castLabs. 

Nome|Descrição
---|---
optData|Uma cadeia JSON que contém informações sobre você. 
CRT|Uma cadeia JSON que contém informações sobre o ativo, seus direitos de reprodução e informações de licença.
IAT|A data/hora atual na época.
jti|Um identificador exclusivo sobre esse token (cada token só pode ser usado uma vez no sistema castLabs).

##<a name="sample-solution-set-up"></a>Solução de exemplo configurar 

A [solução de exemplo](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) consiste em dois projetos:

-   Um aplicativo de console que pode ser usado para definir restrições de DRM sobre um ativo já ingerido, para PlayReady e Widevine.
-   Um aplicativo Web que distribui tokens, que podem ser vistos como uma versão simplificada muito de um STS.


Para usar o aplicativo de console:

1.  Altere o App para configurar credenciais de AMS, castLabs credenciais, configuração de STS e chave compartilhada.
2.  Carregar um ativo na AMS.
3.  Obtenha o UUID do ativo carregado e alterar 32 de linha no arquivo Program.cs:

         var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();

4.  Use um idasset para nomear o ativo no sistema castLabs (linha 44 no arquivo Program.cs).

    Você deve definir idasset para **castLabs**; ele deve ser uma cadeia de caracteres alfanumérica exclusiva.

5.  Execute o programa.


Para usar o aplicativo da Web (STS):

1.  Alterar o Web. config para loja de castlabs configuração ID, a configuração de STS e a chave compartilhada.
2.  Implante a sites Azure.
3.  Navegue até o site.

##<a name="playing-back-a-video"></a>Reproduzir um vídeo

Para reproduzir um vídeo criptografado com criptografia comum (PlayReady e/ou Widevine), você pode usar o [Player de mídia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Ao executar o aplicativo de console, a ID de chave de conteúdo e a URL de manifesto são exibidos.

1.  Abrir uma nova guia e iniciar o seu STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2.  Vá para o [Player de mídia do Microsoft Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3.  Cole a URL de streaming.
4.  Clique na caixa de seleção **Opções avançadas** .
5.  Na lista suspensa de **proteção** , selecione PlayReady e/ou Widevine.
6.  Cole o token obtido de seu STS na caixa Token de texto. 
    
    Não é necessário o servidor de licença castLab a "portador =" prefixo na frente do token. Portanto remova que antes de enviar o token.
7.  Atualize o player.
8.  O vídeo deve estar em execução.


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
