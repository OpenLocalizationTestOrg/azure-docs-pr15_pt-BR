<properties 
    pageTitle="Operações de longa duração de sondagem | Microsoft Azure" 
    description="Este tópico mostra como as operações de longa duração de votação." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="delivering-live-streaming-with-azure-media-services"></a>Entregar Streaming ao vivo com os serviços de mídia do Microsoft Azure

##<a name="overview"></a>Visão geral

Serviços de mídia do Microsoft Azure oferece APIs que enviar solicitações para serviços de mídia para iniciar operações (por exemplo: criar, iniciar, parar ou excluir um canal). Essas operações são demorada.

O SDK do .NET de serviços de mídia fornece APIs que enviar a solicitação e aguarde a conclusão da operação (internamente, as APIs são sondagem para o andamento da operação em alguns intervalos). Por exemplo, quando você chamar o canal. Start (), o método retorna após o canal é iniciado. Você também pode usar a versão assíncrona: aguardar canal. StartAsync() (para obter informações sobre o padrão assíncrono baseado em tarefas, consulte [toque](https://msdn.microsoft.com/library/hh873175(v=vs.110).aspx)). APIs que enviar uma solicitação de operação e então poll do status até concluir a operação são chamadas "métodos de sondagem". Esses métodos (especialmente a versão assíncrona) são recomendados para aplicativos cliente avançado e/ou serviços com estado.

Há situações em que um aplicativo não puder aguardar uma solicitação de http longa duração e deseja pesquisar o andamento da operação manualmente. Um exemplo típico seria um navegador interagindo com um serviço web sem estado: quando o navegador solicita para criar um canal, o serviço web inicia uma operação de execução demorada e retorna a identificação de operação para o navegador. O navegador pode pedir o serviço da web para obter o status de operação com base na identificação. O SDK do .NET de serviços de mídia fornece APIs que são úteis para esse cenário. Essas APIs são chamadas "métodos de sondagem não".
Os "métodos de sondagem não" têm o seguinte padrão de nomenclatura: enviar*OperationName*operação (por exemplo, SendCreateOperation). Enviar os métodos de operação*OperationName*retornar o objeto **IOperation** ; o objeto retornado contém informações que podem ser usadas para controlar a operação. Os métodos de envio*OperationName*OperationAsync retornam **tarefa<IOperation>**.

Atualmente, as seguintes classes oferecem suporte a métodos de sondagem não: **canal**, **StreamingEndpoint**e **programa**.

Para votar para o status da operação, use o método **GetOperation** na classe **OperationBaseCollection** . Usar intervalos a seguir para verificar o status de operação: para operações de **canal** e **StreamingEndpoint** , usar 30 segundos; para operações de **programa** , use 10 segundos.


##<a name="example"></a>Exemplo

O exemplo a seguir define uma classe chamada **ChannelOperations**. Essa definição de classe pode ser um ponto de partida para a sua definição de classe do serviço web. Para simplificar, os exemplos a seguir usam as versões não assíncrono métodos.

O exemplo também mostra como o cliente pode usar essa classe.

###<a name="channeloperations-class-definition"></a>Definição de classe ChannelOperations

    /// <summary> 
    /// The ChannelOperations class only implements 
    /// the Channel’s creation operation. 
    /// </summary> 
    public class ChannelOperations
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;
    
        public ChannelOperations()
        {
                _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
                _context = new CloudMediaContext(_cachedCredentials);    }
    
        /// <summary>  
        /// Initiates the creation of a new channel.  
        /// </summary>  
        /// <param name="channelName">Name to be given to the new channel</param>  
        /// <returns>  
        /// Operation Id for the long running operation being executed by Media Services. 
        /// Use this operation Id to poll for the channel creation status. 
        /// </returns> 
        public string StartChannelCreation(string channelName)
        {
            var operation = _context.Channels.SendCreateOperation(
                new ChannelCreationOptions
                {
                    Name = channelName,
                    Input = CreateChannelInput(),
                    Preview = CreateChannelPreview(),
                    Output = CreateChannelOutput()
                });
    
            return operation.Id;
        }
    
        /// <summary> 
        /// Checks if the operation has been completed. 
        /// If the operation succeeded, the created channel Id is returned in the out parameter.
        /// </summary> 
        /// <param name="operationId">The operation Id.</param> 
        /// <param name="channel">
        /// If the operation succeeded, 
        /// the created channel Id is returned in the out parameter.</param>
        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
        public bool IsCompleted(string operationId, out string channelId)
        {
            IOperation operation = _context.Operations.GetOperation(operationId);
            bool completed = false;
    
            channelId = null;
    
            switch (operation.State)
            {
                case OperationState.Failed:
                    // Handle the failure. 
                    // For example, throw an exception. 
                    // Use the following information in the exception: operationId, operation.ErrorMessage.
                    break;
                case OperationState.Succeeded:
                    completed = true;
                    channelId = operation.TargetEntityId;
                    break;
                case OperationState.InProgress:
                    completed = false;
                    break;
            }
            return completed;
        }
    
    
        private static ChannelInput CreateChannelInput()
        {
            return new ChannelInput
            {
                StreamingProtocol = StreamingProtocol.RTMP,
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
    
        private static ChannelOutput CreateChannelOutput()
        {
            return new ChannelOutput
            {
                Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
            };
        }
    }

###<a name="the-client-code"></a>O código do cliente

    ChannelOperations channelOperations = new ChannelOperations();
    string opId = channelOperations.StartChannelCreation("MyChannel001");
    
    string channelId = null;
    bool isCompleted = false;
    
    while (isCompleted == false)
    {
        System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
        isCompleted = channelOperations.IsCompleted(opId, out channelId);
    }
    
    // If we got here, we should have the newly created channel id.
    Console.WriteLine(channelId);
 


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
