<properties 
    pageTitle="Como usar o Inspetor de API para rastrear chamadas no gerenciamento de API do Azure" 
    description="Saiba como rastrear chamadas usando o Inspetor de API de gerenciamento de API do Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-use-the-api-inspector-to-trace-calls-in-azure-api-management"></a>Como usar o Inspetor de API para rastrear chamadas no gerenciamento de API do Azure

Gerenciamento de API fornece uma ferramenta de Inspetor de API para ajudá-lo com depuração e suas APIs de solução de problemas. O Inspetor de API pode ser usado por programação e também podem ser usado diretamente do portal do desenvolvedor. 

Além das operações de rastreamento, o Inspetor de API também rastreia avaliações de [expressão de política](https://msdn.microsoft.com/library/azure/dn910913.aspx) . Para ver uma demonstração, consulte [nuvem cobrir episódio 177: mais recursos de gerenciamento de API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avanço para 21:00.

Este guia fornece uma passo a passo de como usar o Inspetor de API.

>[AZURE.NOTE] Inspetor de API rastreamentos só são gerados e disponibilizados para solicitações que contêm as chaves de assinatura que pertencem à conta de [administrador](api-management-howto-create-groups.md) .

## <a name="trace-call"></a> Inspetor de API de uso para uma chamada de rastreamento

Para usar o Inspetor de API, adicione um **ocp-apim-rastreamento: true** cabeçalho à sua chamada de operação, de solicitação e baixe e inspecionar o rastreamento usando a URL indicada pelo cabeçalho de resposta **ocp-apim-local de rastreamento** . Isso pode ser feito programaticamente e também pode ser feito diretamente do portal do desenvolvedor.

Este tutorial mostra como usar o Inspetor de API para operações de rastreamento usando a API de calculadora básicas que está configurado no tutorial [Gerenciar sua primeira API](api-management-get-started.md) obtendo iniciado. Se você ainda não concluiu esse tutorial leva apenas alguns momentos para importar a API de calculadora básica, ou você pode usar outra API de sua escolha como a API de eco. Cada instância do serviço de gerenciamento de API vem pré-configurado com uma API de eco que pode ser usado para testar e saiba mais sobre gerenciamento de API. A API de eco retorna qualquer entrada é enviada a ele. Para usá-lo, você pode chamar qualquer verbo HTTP e o valor de retorno será simplesmente o que você enviou. 



Para começar, clique em **portal do desenvolvedor** no Portal de clássico do Azure para o serviço de gerenciamento de API. Operações podem ser chamadas diretamente do portal do desenvolvedor do que fornece uma maneira conveniente para visualizar e testar as operações de uma API.

>Se você ainda não tiver criado uma instância de serviço de gerenciamento de API, consulte [criar uma instância de serviço de gerenciamento de API][] do tutorial de [Introdução ao gerenciamento de API do Azure][] .

![Portal de gerenciamento de API de desenvolvedor][api-management-developer-portal-menu]

Clique em **APIs** do menu superior e clique em **Calculadora básica**.

![API de eco][api-management-api]

Clique em **experimentar** para tentar a operação de **Adicionar dois inteiros** .

![Experimente][api-management-open-console]

Manter o padrão de valores de parâmetro e, em seguida, selecione a chave de assinatura para o produto que você deseja usar da **chave de assinatura** suspensa.

Por padrão no portal do desenvolvedor do **Rastreamento de Apim Ocp** cabeçalho já está definido como **true**. Esse cabeçalho configura estando ou não um rastreamento é gerado.

![Enviar][api-management-http-get]

Clique em **Enviar** para invocar a operação.

![Enviar][api-management-send-results]

Na resposta cabeçalhos será um **ocp-apim-local de rastreamento** com um valor semelhante ao seguinte exemplo.

    ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

O rastreamento pode ser baixado do local especificado e revisado conforme demonstrou na próxima etapa.

## <a name="inspect-trace"> </a>Inspecionar o rastreamento

Para revisar os valores no rastreamento, baixe o arquivo de rastreamento da URL **ocp-apim-local de rastreamento** . Ele é um arquivo de texto no formato JSON e contém entradas semelhantes ao seguinte exemplo.

    {
        "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
        "traceEntries": {
            "inbound": [
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0725926",
                    "data": {
                        "request": {
                            "method": "GET",
                            "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
                            "headers": [
                                {
                                    "name": "Ocp-Apim-Subscription-Key",
                                    "value": "5d7c41af64a44a68a2ea46580d271a59"
                                },
                                {
                                    "name": "Connection",
                                    "value": "Keep-Alive"
                                },
                                {
                                    "name": "Host",
                                    "value": "contoso5.azure-api.net"
                                }
                            ]
                        }
                    }
                },
                {
                    "source": "mapper",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0726213",
                    "data": {
                        "configuration": {
                            "api": {
                                "from": "/calc",
                                "to": {
                                    "scheme": "http",
                                    "host": "calcapi.cloudapp.net",
                                    "port": 80,
                                    "path": "/api",
                                    "queryString": "",
                                    "query": {},
                                    "isDefaultPort": true
                                }
                            },
                            "operation": {
                                "method": "GET",
                                "uriTemplate": "/add?a={a}&b={b}"
                            },
                            "user": {
                                "id": 1,
                                "groups": [
                                    "Administrators",
                                    "Developers"
                                ]
                            },
                            "product": {
                                "id": 1
                            }
                        }
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0727522",
                    "data": {
                        "message": "Request is being forwarded to the backend service.",
                        "request": {
                            "method": "GET",
                            "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
                            "headers": [
                                {
                                    "name": "Ocp-Apim-Subscription-Key",
                                    "value": "5d7c41af64a44a68a2ea46580d271a59"
                                },
                                {
                                    "name": "X-Forwarded-For",
                                    "value": "33.52.215.35"
                                }
                            ]
                        }
                    }
                }
            ],
            "outbound": [
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1960601",
                    "data": {
                        "response": {
                            "status": {
                                "code": 200,
                                "reason": "OK"
                            },
                            "headers": [
                                {
                                    "name": "Pragma",
                                    "value": "no-cache"
                                },
                                {
                                    "name": "Content-Length",
                                    "value": "124"
                                },
                                {
                                    "name": "Cache-Control",
                                    "value": "no-cache"
                                },
                                {
                                    "name": "Content-Type",
                                    "value": "application/xml; charset=utf-8"
                                },
                                {
                                    "name": "Date",
                                    "value": "Tue, 23 Jun 2015 19:51:35 GMT"
                                },
                                {
                                    "name": "Expires",
                                    "value": "-1"
                                },
                                {
                                    "name": "Server",
                                    "value": "Microsoft-IIS/8.5"
                                },
                                {
                                    "name": "X-AspNet-Version",
                                    "value": "4.0.30319"
                                },
                                {
                                    "name": "X-Powered-By",
                                    "value": "ASP.NET"
                                }
                            ]
                        }
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1961112",
                    "data": {
                        "message": "Response headers have been sent to the caller. Starting to stream the response body."
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1963155",
                    "data": {
                        "message": "Response body streaming to the caller is complete."
                    }
                }
            ]
        }
    }

## <a name="next-steps"> </a>Próximos passos

-   Assista a uma demonstração do rastreamento expressões de política em [nuvem cobrir episódio 177: mais recursos de gerenciamento de API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/). Avançar para 21:00 para ver a demonstração.

>[AZURE.VIDEO episode-177-more-api-management-features-with-vlad-vinogradsky]

[Use API Inspector to trace a call]: #trace-call
[Inspect the trace]: #inspect-trace
[Next steps]: #next-steps

[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md

[Introdução ao gerenciamento de API do Azure]: api-management-get-started.md
[Criar uma instância de serviço de gerenciamento de API]: api-management-get-started.md#create-service-instance
[Azure Classic Portal]: https://manage.windowsazure.com/


[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png




 