<properties 
    pageTitle="Referência de política de gerenciamento de API do Azure" 
    description="Saiba mais sobre as políticas disponíveis para configurar o gerenciamento de API." 
    services="api-management" 
    documentationCenter="" 
    authors="vladvino" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="apimpm"/>

# <a name="azure-api-management-policy-reference"></a>Referência de política de gerenciamento de API do Azure

Esta seção fornece um índice para as políticas na [referência de política de gerenciamento de API][]. Para obter informações sobre como adicionar e configurar políticas, consulte [políticas de gerenciamento de API][].

Expressões de política podem ser usadas como valores de atributo ou valores de texto em qualquer uma das políticas de gerenciamento de API, a menos que a política especifica o contrário. Algumas políticas como as políticas de [fluxo de controle][] e [Defina variável][] são baseadas em expressões de política. Para obter mais informações, consulte [avançada políticas][] e [expressões de política][]

## <a name="policy-reference-index"></a>Índice de referência de política

-   [Diretivas de restrição de acesso][]
    -   [Cabeçalho HTTP check][] - impõe existência e/ou o valor de um cabeçalho HTTP.
    -   [Taxa de chamadas de limite por assinatura][] - uso de APIs impede picos limitando taxa de chamada, em uma base por assinatura.
    -   [Taxa de chamadas de limite pela chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) - uso de APIs impede picos limitando taxa de chamada, em uma base por chave.
    -   [Restringir chamador IPs][] - filtros (permite/nega) chamadas de endereços IP específicos e/ou intervalos de endereços.
    -   [Definir cota de uso por assinatura][] - permite que você imponha uma renovável ou vida útil chamada volume e/ou a largura de banda cota, por assinatura.
    -   [Definir cota de uso pela chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) - permite que você imponha uma renovável ou vida útil chamada volume e/ou a largura de banda cota, em uma base por chave.
    -   [Validar JWT][] - impõe existência e a validade de um JWT extraída de um cabeçalho HTTP especificado ou um parâmetro de consulta especificado.
-   [Diretivas avançadas][]
    -   [Fluxo de controle][] - condicionalmente aplica instruções de política com base nos resultados da avaliação de booleanas [expressões][].
    -   [Solicitação de encaminhar][] - encaminha a solicitação para o serviço de back-end.
    -   [Log de eventos Hub][] - enviar mensagens no formato especificado para um destino de mensagem definido por uma entidade de [agente de log](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger) .
    -   [Repetir](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry) - execução de tentativas das instruções circunscrito política, se e até que a condição é atendida. Execução será Repita os intervalos de tempo especificado e até especificado contagem de repetição.
    -   [Retornar resposta](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) - execução de pipeline de Anulações e retorna a resposta especificada diretamente para o chamador.
    -   [Enviar solicitação de uma maneira](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) - envia uma solicitação para a URL especificada sem aguardando uma resposta.
    -   [Enviar solicitação](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) - envia uma solicitação para a URL especificada.
    -   [Método de solicitação de set](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) - permite que você altere o método HTTP para uma solicitação.
    -   [Definir status](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) - altera o código de status HTTP para o valor especificado.
    -   [Definir variável][] - manter um valor em uma variável nomeado [contexto][] para acesso posterior.
    -   [Rastreamento](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace) - adiciona uma cadeia de caracteres na saída [Inspetor de API](../api-management/api-management-howto-api-inspector.md) .
    -   [Aguarde](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) - aguarda circunscrito enviar solicitação, obter o valor de cache ou políticas de fluxo de controle para ser concluída antes de continuar.
-   [Políticas de autenticação][]
    -   [Autenticar com Basic][] - autenticar com um serviço de back-end usando a autenticação básica.
    -   [Autenticar com o certificado de cliente][] - autenticar com um serviço de back-end usando certificados de cliente.
-   [Políticas de cache][] 
    -   [Obtenha do cache][] - executar cache pesquisar e retornar uma resposta em cache válida quando disponível.
    -   [Armazenamento em cache][] - resposta Caches acordo com a configuração de controle de cache especificado.
    -   [Obter valor do cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) - recuperar um item em cache pela chave.
    -   [Valor de armazenamento em cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) - Store um item no cache pela chave.
    -   [Remover o valor do cache](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey) - remover um item no cache pela chave.
-   [Cross diretivas de domínio][] 
    -   [Permitir chamadas de domínio cruzado][] - torna a API acessíveis do Adobe Flash e o Microsoft Silverlight clientes baseados em navegador.
    -   [CORS][] - adiciona o suporte (CORS) para uma operação ou uma API para permitir chamadas entre domínios de clientes baseados em navegador de compartilhamento de recursos entre origens.
    -   [JSONP][] - adiciona JSON com suporte (JSONP) de preenchimento a uma operação ou uma API para permitir chamadas de domínio cruzado de JavaScript baseadas em navegador clientes.
-   [Políticas de transformação][] 
    -   [Converter JSON e XML][] - converte solicitação ou resposta corpo de JSON para XML.
    -   [Converter XML para JSON][] - converte solicitação ou resposta do corpo do XML como JSON.
    -   [Localizar e substituir a cadeia de caracteres no corpo][] - localiza uma subcadeia de solicitação ou resposta e substitui por uma subcadeia de caracteres diferente.
    -   [URLs de máscara no conteúdo][] - grava novamente (máscaras) links na resposta corpo para que eles apontem para o link equivalente através do gateway.
    -   [Configurar o serviço de back-end][] - altera o serviço de back-end para uma solicitação de entrada.
    -   [Definir corpo][] - define o corpo da mensagem para solicitações de entrada e de saída.
    -   [Cabeçalho HTTP definir][] - atribui um valor com uma resposta existente e/ou o cabeçalho de solicitação ou adiciona um novo cabeçalho de resposta e/ou a solicitação.
    -   [Definir o parâmetro da sequência de consulta][] - adiciona, exclusões ou substitui o valor de parâmetro da sequência de consulta de solicitação.
    -   [Reconfiguração de URL][] - converte uma URL de solicitação de sua forma pública ao formulário esperado pelo serviço da web.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre expressões de política, consulte o vídeo a seguir.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Diretivas de restrição de acesso]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Verificar cabeçalho HTTP]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Taxa de chamadas de limite por assinatura]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Restringir o chamador IPs]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Cota de uso de conjunto por assinatura]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Validar JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Diretivas avançadas]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Fluxo de controle]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Definir variável]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expressões]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[contexto]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Solicitação de encaminhamento]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Log de eventos Hub]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Políticas de autenticação]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Autenticar com Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Autenticar com certificado de cliente]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Políticas de cache]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Obtenha do cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Armazenar em cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Cross diretivas de domínio]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Permitir chamadas entre domínios]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Políticas de transformação]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Converter JSON em XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Converter XML em JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Localizar e substituir a cadeia de caracteres no corpo]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[URLs de máscara no conteúdo]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Serviço de back-end do conjunto]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Definir corpo]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Cabeçalho HTTP conjunto]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Parâmetro de cadeia de consulta de conjunto]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Reconfiguração de URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Políticas de gerenciamento de API]: api-management-howto-policies.md
[Referência de política de gerenciamento de API]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Expressões de política]: https://msdn.microsoft.com/library/azure/dn910913.aspx

 
