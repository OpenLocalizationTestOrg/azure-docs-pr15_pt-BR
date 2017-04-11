<properties
    pageTitle="Repetir lógica no SDK do serviços de mídia para .NET | Microsoft Azure"
    description="O tópico apresenta uma visão geral de lógica de repetição no SDK dos serviços de mídia para .NET."
    authors="Juliako"
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
    ms.date="10/25/2016" 
    ms.author="juliako"/>


# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Repetir lógica no SDK do serviços de mídia para .NET

Ao trabalhar com os serviços do Microsoft Azure, podem ocorrer falhas temporárias. Se ocorrer uma falha temporária, na maioria dos casos, após algumas tentativas a operação de sucesso. O SDK de serviços de mídia para .NET implementa a lógica de repetição para lidar com falhas temporárias associadas exceções e erros são causados por solicitações da web, executando consultas, salvar alterações e as operações de armazenamento.  Por padrão, o SDK de serviços de mídia para .NET executa quatro tentativas antes de lançar novamente a exceção ao seu aplicativo. O código em seu aplicativo, em seguida, deve tratar essa exceção corretamente.  
  
 Um breve guia das políticas de solicitação da Web, armazenamento, consulta e SaveChanges é o seguinte:  
  
-   A política de armazenamento é usada para operações de armazenamento de blob (carregamentos ou download de arquivos de ativo).  
  
-   A política de solicitação da Web é usada para solicitações de web genérico (por exemplo, para obter um token de autenticação e resolvendo o ponto de extremidade de cluster de usuários).  
  
-   A política de consulta é usada para consultar entidades do resto (por exemplo, mediaContext.Assets.Where(...)).  
  
-   A política de SaveChanges é usada para fazer nada que altera os dados dentro do serviço (por exemplo, criando uma entidade atualizando uma entidade, chamando uma função de serviço para uma operação).  
  
 Este tópico lista os tipos de exceção e códigos de erro que são tratados pelo SDK de serviços de mídia para .NET uma lógica de repetição.  
  
## <a name="exception-types"></a>Tipos de exceção  

A tabela a seguir descreve exceções que o SDK de serviços de mídia para .NET manipula ou não processa para algumas operações que podem causar falhas temporárias.  
  
Exceção|Solicitação da Web|Armazenamento|Consulta|SaveChanges
----|------|----|---|---
WebException<br/>Para obter mais informações, consulte a seção de [códigos de status de WebException](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) .|Sim|Sim|Sim|Sim  
DataServiceClientException<br/> Para obter mais informações, consulte [códigos de status de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|Não|Sim|Sim|Sim
DataServiceQueryException<br/> Para obter mais informações, consulte [códigos de status de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|Não|Sim|Sim|Sim  
DataServiceRequestException<br/> Para obter mais informações, consulte [códigos de status de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|Não|Sim|Sim|Sim  
DataServiceTransportException|Não|Não|Sim|Sim
TimeoutException|Sim|Sim|Sim|Não
Exceção SocketException|Sim|Sim|Sim|Sim  
StorageException|Não|Sim|Não|Não 
IOException|Não|Sim|Não|Não
  
###  <a name="WebExceptionStatus"></a>Códigos de status de WebException  

A tabela a seguir mostra os códigos de erro WebException a lógica de repetição é implementada. A enumeração [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) define os códigos de status.  
  
Status|Solicitação da Web|Armazenamento|Consulta|SaveChanges  
-----|-----------------|-------------|-----------|----------  
ConnectFailure|Sim|Sim|Sim|Sim
NameResolutionFailure|Sim|Sim|Sim|Sim  
ProxyNameResolutionFailure|Sim|Sim|Sim|Sim  
SendFailure|Sim|Sim|Sim|Sim
PipelineFailure|Sim|Sim|Sim|Não  
ConnectionClosed|Sim|Sim|Sim|Não  
KeepAliveFailure|Sim|Sim|Sim|Não  
UnknownError|Sim|Sim|Sim|Não 
ReceiveFailure|Sim|Sim|Sim|Não  
RequestCanceled|Sim|Sim|Sim|Não  
Tempo limite|Sim|Sim|Sim|Não
Erro de protocolo <br/>A tentativa em erro de protocolo é controlada pela manipulação de código de status HTTP. Para obter mais informações, consulte [códigos de status de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|Sim|Sim|Sim|Sim|  
  
###  <a name="HTTPStatusCode"></a>Códigos de status de erro HTTP  

Quando as operações de consulta e SaveChanges joga DataServiceClientException, DataServiceQueryException ou DataServiceQueryException, o código de status de erro HTTP é retornado na propriedade StatusCode.  A tabela a seguir mostra os códigos de erro a lógica de repetição é implementada.  
  
 
Status|Solicitação da Web|Armazenamento|Consulta|SaveChanges 
---|----|----|----|----
401|Não|Sim|Não|Não
403|Não|Sim<br/>Manipulação de tentativas com aguarda mais longa.|Não|Não  
408|Sim|Sim|Sim|Sim
429|Sim|Sim|Sim|Sim  
500|Sim|Sim|Sim|Não  
502|Sim|Sim|Sim|Não  
503|Sim|Sim|Sim|Sim  
504|Sim|Sim|Sim|Não  
  
Se quiser tirar uma olhada a implementação real do SDK dos serviços de mídia para .NET uma lógica de repetição, consulte [azure-sdk-para-serviços de mídia](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
