<properties 
    pageTitle="Como realizar streaming ao vivo usando os serviços de mídia do Azure para criar fluxos de multi-taxa de bits com .NET | Microsoft Azure" 
    description="Este tutorial orienta você pelas etapas de criação de um canal que recebe um fluxo de taxa de bits única ao vivo e codifica fluxo de multi-taxa de bits usando o SDK do .NET." 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article"
    ms.date="10/12/2016"
    ms.author="juliako;anilmur"/>


#<a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-net"></a>Como realizar streaming ao vivo usando os serviços de mídia do Azure para criar fluxos de multi-taxa de bits com .NET

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [API REST](https://msdn.microsoft.com/library/azure/dn783458.aspx)

>[AZURE.NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F).

##<a name="overview"></a>Visão geral

Este tutorial orienta você pelas etapas de criação de um **canal** que recebe um fluxo de taxa de bits única ao vivo e codifica fluxo de multi-taxa de bits.

Para obter mais informações conceituais relacionadas a canais que estão habilitados para codificação ao vivo, consulte [Live streaming usando os serviços de mídia do Azure para criar fluxos de multi-taxa de bits](media-services-manage-live-encoder-enabled-channels.md).


##<a name="common-live-streaming-scenario"></a>Cenário de Streaming Live comum

As etapas a seguir descrevem tarefas envolvidas na criação de aplicativos comuns de streaming ao vivo.

>[AZURE.NOTE] Atualmente, a duração máxima recomendada de um evento ao vivo é 8 horas. Se você precisa executar um canal para longos períodos de tempo, contate amslived no Microsoft.com.

1. Conecte uma câmera de vídeo para um computador. Inicie e configure um codificador ao vivo no local que possa um fluxo de taxa de bits única em um dos seguintes protocolos de saída: RTMP, Streaming suave ou RTP (MPEG-TS). Para obter mais informações, consulte [suporte de RTMP de serviços de mídia do Azure e Live codificadores](http://go.microsoft.com/fwlink/?LinkId=532824).

Esta etapa também poderia ser executada depois de criar seu canal.

1. Criar e iniciar um canal.

1. Recuperar o canal inclusão URL.

A URL de recebimento é usada pelo codificador ao vivo para enviar o fluxo para o canal.

1. Recupere a URL de visualização de canal.

Use esta URL para verificar se seu canal corretamente está recebendo o fluxo ao vivo.

2. Crie um ativo.
3. Se você quiser para o ativo a ser criptografado dinamicamente durante a reprodução, faça o seguinte:
1. Crie uma chave de conteúdo.
1. Configure diretiva de autorização da chave de conteúdo.
1. Configure política de entrega de ativos (usada por embalagem dinâmica e criptografia dinâmico).
3. Crie um programa e especifique para usar o ativo que você criou.
1. Publica o ativo associado ao programa criando um localizador de OnDemand.

Certifique-se de ter pelo menos uma unidade reservada no ponto de extremidade streaming do qual você deseja transmitir conteúdo de streaming.

1. Inicie o programa quando você estiver pronto para iniciar o streaming e arquivamento.
2. Opcionalmente, o codificador ao vivo pode ser sinalizado para iniciar uma publicidade. O anúncio é inserido no fluxo de saída.
1. Pare o programa sempre que você deseja parar de streaming e o evento de arquivamento.
1. Excluir o programa (e, opcionalmente, excluir o ativo).

## <a name="what-youll-learn"></a>O que você aprenderá

Este tópico mostra como executar operações diferentes em canais e programas usando o SDK do .NET de serviços de mídia. Porque muitas operações são demorada .NET APIs que gerenciar longas operações são usadas.

O tópico mostra como fazer o seguinte:

1. Criar e iniciar um canal. APIs de execução demorada são usadas.
1. Obter os canais inclusão ponto de extremidade (input). Neste ponto de extremidade deve ser fornecido com o codificador que pode enviar um fluxo de taxa de bits única ao vivo.
1. Obter o ponto de extremidade de visualização. Neste ponto de extremidade é usado para visualizar seu fluxo.
1. Crie um ativo que será usado para armazenar seu conteúdo. As políticas de entrega de ativos devem ser configuradas também, conforme mostrado neste exemplo.
1. Crie um programa e especifique para usar o ativo que foi criado anteriormente. Inicie o programa. APIs de execução demorada são usadas.
1. Crie um localizador para o ativo, para que o conteúdo é publicado e pode ser transmitido para seus clientes.
1. Mostrar e ocultar slates. Iniciar e parar anúncios. APIs de execução demorada são usadas.
1. Limpe seu canal e todos os recursos associados.


##<a name="prerequisites"></a>Pré-requisitos

A seguir é necessárias para concluir o tutorial.

- Para concluir este tutorial, você precisa de uma conta do Azure.

Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F). Obtenha créditos que podem ser usados para experimentar serviços Azure pagos. Mesmo após os créditos são usados para cima, você pode manter a conta e usar serviços gratuitos do Azure e recursos, como o recurso de aplicativos Web do serviço de aplicativo do Azure.
- Uma conta de serviços de mídia. Para criar uma conta de serviços de mídia, consulte [Criar conta](media-services-portal-create-account.md).
- Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express) ou versões posteriores.
- Você deve usar mídia serviços .NET SDK versão 3.2.0.0 ou mais recente.
- Uma webcam e um codificador que pode enviar um fluxo de taxa de bits única ao vivo.

##<a name="considerations"></a>Considerações

- Atualmente, a duração máxima recomendada de um evento ao vivo é 8 horas. Se você precisa executar um canal para longos períodos de tempo, contate amslived no Microsoft.com.
- Certifique-se de ter pelo menos uma unidade reservada no ponto de extremidade streaming do qual você deseja transmitir conteúdo de streaming.

##<a name="download-sample"></a>Exemplo de download

Obtenha e executar uma amostra de [aqui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-encode-live-stream-with-ams-clear/).


##<a name="set-up-for-development-with-media-services-sdk-for-net"></a>Configurar para o desenvolvimento com o SDK de serviços de mídia para .NET

1. Crie um aplicativo de console usando o Visual Studio.
1. Adicione o SDK dos serviços de mídia para .NET para seu aplicativo de console usando o pacote do NuGet de serviços de mídia.

##<a name="connect-to-media-services"></a>Se conectar aos serviços de mídia
Como prática recomendada, você deve usar um arquivo de App para armazenar a chave de nome e uma conta de serviços de mídia.

>[AZURE.NOTE]Para localizar o nome e a chave valores, acesse o portal do Azure e selecione sua conta. A janela configurações é exibida à direita. Na janela Configurações, selecione chaves. Clicando no ícone ao lado de cada caixa de texto, o valor é copiado para a área de transferência do sistema.

Adicione a seção appSettings para o arquivo App e defina os valores para sua chave de nome e uma conta da conta dos serviços de mídia.


    <?xml version="1.0"?>
    <configuration>
      <appSettings>
          <add key="MediaServicesAccountName" value="YouMediaServicesAccountName" />
          <add key="MediaServicesAccountKey" value="YouMediaServicesAccountKey" />
      </appSettings>
    </configuration>
     
    
##<a name="code-example"></a>Exemplo de código

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Net;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
    
    namespace EncodeLiveStreamWithAmsClear
    {
        class Program
        {
            private const string ChannelName = "channel001";
            private const string AssetlName = "asset001";
            private const string ProgramlName = "program001";
    
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
    
    
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
                IChannel channel = CreateAndStartChannel();
    
                // The channel's input endpoint:
                string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();
    
                Console.WriteLine("Intest URL: {0}", ingestUrl);
    
    
                // Use the previewEndpoint to preview and verify 
                // that the input from the encoder is actually reaching the Channel. 
                string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();
    
                Console.WriteLine("Preview URL: {0}", previewEndpoint);
    
                // When Live Encoding is enabled, you can now get a preview of the live feed as it reaches the Channel. 
                // This can be a valuable tool to check whether your live feed is actually reaching the Channel. 
                // The thumbnail is exposed via the same end-point as the Channel Preview URL.
                string thumbnailUri = new UriBuilder
                {
                    Scheme = Uri.UriSchemeHttps,
                    Host = channel.Preview.Endpoints.FirstOrDefault().Url.Host,
                    Path = "thumbnails/input.jpg"
                }.Uri.ToString();
    
                Console.WriteLine("Thumbain URL: {0}", thumbnailUri);
    
                // Once you previewed your stream and verified that it is flowing into your Channel, 
                // you can create an event by creating an Asset, Program, and Streaming Locator. 
                IAsset asset = CreateAndConfigureAsset();
    
                IProgram program = CreateAndStartProgram(channel, asset);
    
                ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);
    
                // You can use slates and ads only if the channel type is Standard.  
                StartStopAdsSlates(channel);
    
                // Once you are done streaming, clean up your resources.
                Cleanup(channel);
    
            }
    
            public static IChannel CreateAndStartChannel()
            {
                var channelInput = CreateChannelInput();
                var channePreview = CreateChannelPreview();
                var channelEncoding = CreateChannelEncoding();
    
    
                ChannelCreationOptions options = new ChannelCreationOptions
                {
                    EncodingType = ChannelEncodingType.Standard,
                    Name = ChannelName,
                    Input = channelInput,
                    Preview = channePreview,
                    Encoding = channelEncoding
                };
    
                Log("Creating channel");
                IOperation channelCreateOperation = _context.Channels.SendCreateOperation(options);
                string channelId = TrackOperation(channelCreateOperation, "Channel create");
    
                IChannel channel = _context.Channels.Where(c => c.Id == channelId).FirstOrDefault();
    
                Log("Starting channel");
                var channelStartOperation = channel.SendStartOperation();
                TrackOperation(channelStartOperation, "Channel start");
    
                return channel;
            }
    
            /// <summary>
            /// Create channel input, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelInput CreateChannelInput()
            {
                return new ChannelInput
                {
                    StreamingProtocol = StreamingProtocol.RTPMPEG2TS,
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelInput001",
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }
    
            /// <summary>
            /// Create channel preview, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelPreview CreateChannelPreview()
            {
                return new ChannelPreview
                {
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelPreview001",
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }
    
            /// <summary>
            /// Create channel encoding, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelEncoding CreateChannelEncoding()
            {
                return new ChannelEncoding
                {
                    SystemPreset = "Default720p",
                    IgnoreCea708ClosedCaptions = false,
                    AdMarkerSource = AdMarkerSource.Api,
                    // You can only set audio if streaming protocol is set to StreamingProtocol.RTPMPEG2TS.
                    AudioStreams = new List<AudioStream> { new AudioStream { Index = 103, Language = "eng" } }.AsReadOnly()
                };
            }
    
            /// <summary>
            /// Create an asset and configure asset delivery policies.
            /// </summary>
            /// <returns></returns>
            public static IAsset CreateAndConfigureAsset()
            {
                IAsset asset = _context.Assets.Create(AssetlName, AssetCreationOptions.None);
    
                IAssetDeliveryPolicy policy =
                    _context.AssetDeliveryPolicies.Create("Clear Policy",
                    AssetDeliveryPolicyType.NoDynamicEncryption,
                    AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
    
                asset.DeliveryPolicies.Add(policy);
    
                return asset;
            }
    
            /// <summary>
            /// Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
            /// however each Program must have a unique name within your Media Services account.
            /// </summary>
            /// <param name="channel"></param>
            /// <param name="asset"></param>
            /// <returns></returns>
            public static IProgram CreateAndStartProgram(IChannel channel, IAsset asset)
            {
                IProgram program = channel.Programs.Create(ProgramlName, TimeSpan.FromHours(3), asset.Id);
                Log("Program created", program.Id);
    
                Log("Starting program");
                var programStartOperation = program.SendStartOperation();
                TrackOperation(programStartOperation, "Program start");
    
                return program;
            }
    
            /// <summary>
            /// Create locators in order to be able to publish and stream the video.
            /// </summary>
            /// <param name="asset"></param>
            /// <param name="ArchiveWindowLength"></param>
            /// <returns></returns>
            public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
            {
                // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
                var locator = _context.Locators.CreateLocator
                    (
                        LocatorType.OnDemandOrigin,
                        asset,
                        _context.AccessPolicies.Create
                            (
                                "Live Stream Policy",
                                ArchiveWindowLength,
                                AccessPermissions.Read
                            )
                    );
    
                return locator;
            }
    
            /// <summary>
            /// Perform operations on slates.
            /// </summary>
            /// <param name="channel"></param>
            public static void StartStopAdsSlates(IChannel channel)
            {
                int cueId = new Random().Next(int.MaxValue);
                var path = Path.GetFullPath(Path.Combine(AppDomain.CurrentDomain.BaseDirectory, @"..\\..\\SlateJPG\\DefaultAzurePortalSlate.jpg"));
    
                Log("Creating asset");
                var slateAsset = _context.Assets.Create("Slate test asset " + DateTime.Now.ToString("yyyy-MM-dd HH-mm"), AssetCreationOptions.None);
                Log("Slate asset created", slateAsset.Id);
    
                Log("Uploading file");
                var assetFile = slateAsset.AssetFiles.Create("DefaultAzurePortalSlate.jpg");
                assetFile.Upload(path);
                assetFile.IsPrimary = true;
                assetFile.Update();
    
                Log("Showing slate");
                var showSlateOpeartion = channel.SendShowSlateOperation(TimeSpan.FromMinutes(1), slateAsset.Id);
                TrackOperation(showSlateOpeartion, "Show slate");
    
                Log("Hiding slate");
                var hideSlateOperation = channel.SendHideSlateOperation();
                TrackOperation(hideSlateOperation, "Hide slate");
    
                Log("Starting ad");
                var startAdOperation = channel.SendStartAdvertisementOperation(TimeSpan.FromMinutes(1), cueId, false);
                TrackOperation(startAdOperation, "Start ad");
    
                Log("Ending ad");
                var endAdOperation = channel.SendEndAdvertisementOperation(cueId);
                TrackOperation(endAdOperation, "End ad");
    
                Log("Deleting slate asset");
                slateAsset.Delete();
            }
    
            /// <summary>
            /// Clean up resources associated with the channel.
            /// </summary>
            /// <param name="channel"></param>
            public static void Cleanup(IChannel channel)
            {
                IAsset asset;
                if (channel != null)
                {
                    foreach (var program in channel.Programs)
                    {
                        asset = _context.Assets.Where(se => se.Id == program.AssetId)
                                                .FirstOrDefault();
    
                        Log("Stopping program");
                        var programStopOperation = program.SendStopOperation();
                        TrackOperation(programStopOperation, "Program stop");
    
                        program.Delete();
    
                        if (asset != null)
                        {
                            Log("Deleting locators");
                            foreach (var l in asset.Locators)
                                l.Delete();
    
                            Log("Deleting asset");
                            asset.Delete();
                        }
                    }
    
                    Log("Stopping channel");
                    var channelStopOperation = channel.SendStopOperation();
                    TrackOperation(channelStopOperation, "Channel stop");
    
                    Log("Deleting channel");
                    var channelDeleteOperation = channel.SendDeleteOperation();
                    TrackOperation(channelDeleteOperation, "Channel delete");
                }
            }
    
    
            /// <summary>
            /// Track long running operations.
            /// </summary>
            /// <param name="operation"></param>
            /// <param name="description"></param>
            /// <returns></returns>
            public static string TrackOperation(IOperation operation, string description)
            {
                string entityId = null;
                bool isCompleted = false;
    
                Log("starting to track ", null, operation.Id);
                while (isCompleted == false)
                {
                    operation = _context.Operations.GetOperation(operation.Id);
                    isCompleted = IsCompleted(operation, out entityId);
                    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
                }
                // If we got here, the operation succeeded.
                Log(description + " in completed", operation.TargetEntityId, operation.Id);
    
                return entityId;
            }
    
            /// <summary> 
            /// Checks if the operation has been completed. 
            /// If the operation succeeded, the created entity Id is returned in the out parameter.
            /// </summary> 
            /// <param name="operationId">The operation Id.</param> 
            /// <param name="channel">
            /// If the operation succeeded, 
            /// the entity Id associated with the sucessful operation is returned in the out parameter.</param>
            /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
            private static bool IsCompleted(IOperation operation, out string entityId)
            {
    
                bool completed = false;
    
                entityId = null;
    
                switch (operation.State)
                {
                    case OperationState.Failed:
                        // Handle the failure. 
                        // For example, throw an exception. 
                        // Use the following information in the exception: operationId, operation.ErrorMessage.
                        Log("operation failed", operation.TargetEntityId, operation.Id);
                        break;
                    case OperationState.Succeeded:
                        completed = true;
                        entityId = operation.TargetEntityId;
                        break;
                    case OperationState.InProgress:
                        completed = false;
                        Log("operation in progress", operation.TargetEntityId, operation.Id);
                        break;
                }
                return completed;
            }
    
    
            private static void Log(string action, string entityId = null, string operationId = null)
            {
                Console.WriteLine(
                    "{0,-21}{1,-51}{2,-51}{3,-51}",
                    DateTime.Now.ToString("yyyy'-'MM'-'dd HH':'mm':'ss"),
                    action,
                    entityId ?? string.Empty,
                    operationId ?? string.Empty);
            }
        }
    }   


##<a name="next-step"></a>Próxima etapa

Examine os serviços de mídia caminhos de aprendizado.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="looking-for-something-else"></a>Procurando algo mais?

Se este tópico não contêm o que você estava esperando, está faltando algo ou em alguma outra maneira não atender às suas necessidades, forneça-nos com você comentários usando o segmento Disqus abaixo.
