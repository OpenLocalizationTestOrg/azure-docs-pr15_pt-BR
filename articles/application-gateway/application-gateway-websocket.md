<properties
   pageTitle="Suporte de Gateway WebSocket do aplicativo | Microsoft Azure"
   description="Esta página fornece uma visão geral do suporte do aplicativo Gateway WebSocket."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2016"
   ms.author="amsriva"/>

# <a name="application-gateway-websocket-support"></a>Suporte de Gateway WebSocket do aplicativo

Gateway de aplicativo oferece suporte nativo WebSocket em todos os tamanhos de gateway. Não há nenhuma configuração configurável pelo usuário para ativar ou desativar o suporte ao WebSocket seletivamente. Você pode continuar usando um HTTPListener padrão na porta 80/443 receber tráfego WebSocket. Tráfego WebSocket será direcionado para o servidor de back-end habilitado WebSocket usando o pool de back-end apropriado conforme especificado nas regras de gateway do aplicativo. Protocolo WebSocket padronizado no [RFC6455](https://tools.ietf.org/html/rfc6455) permite uma comunicação full-duplex entre cliente e servidor sobre uma conexão de TCP longa duração. Esse recurso permite uma comunicação mais interativa entre o servidor web e o cliente, que pode ser bidirecional sem a necessidade de sondagem como necessário em implementações baseadas em HTTP.  WebSocket tem baixa sobrecarga diferentemente HTTP e pode reutilizar a mesma conexão de TCP para várias solicitação/respostas resulta em uma utilização mais eficiente de recursos. WebSocket protocolos foram projetados para trabalhar em portas HTTP tradicionais de 80 e 443.

O servidor back-end deve responder a testes de gateway do aplicativo, que são descritos na seção [Visão geral de teste de integridade](application-gateway-probe-overview.md) . Testes de integridade de gateway do aplicativo só estão HTTP/HTTPS, isso significa que todos os servidores de back-end devem responder a testes HTTP para application gateway para rotear o tráfego WebSocket no servidor.

## <a name="listener-configuration-element"></a>Elemento de configuração de ouvinte

HTTPListener existente pode ser usado para dar suporte a WebSocket. A seguir é um trecho de HttpListeners elemento de um arquivo de modelo de exemplo. Você precisaria ouvintes HTTP e HTTPS para oferecer suporte WebSocket e proteger o tráfego WebSocket. Da mesma forma que você pode usar o [portal](application-gateway-create-gateway-portal.md) ou o [PowerShell](application-gateway-create-gateway-arm.md) para criar um gateway de aplicativo com ouvintes na porta 80/443 para suporte ao tráfego WebSocket.


    "httpListeners": [
                {
                    "name": "appGatewayHttpsListener",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
                        },
                        "Protocol": "Https",
                        "SslCertificate": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
                        },
                    }
                },
                {
                    "name": "appGatewayHttpListener",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                        },
                        "Protocol": "Http",
                    }
                }
            ],

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Configuração de regra de BackendAddressPool, BackendHttpSetting e roteamento

BackendAddressPool deve ser usado para definir um pool de back-end com servidores WebSocket habilitado. BackendHttpSetting deve ser definido com back-end porta 80/443 somente. Propriedades para afinidade baseada em cookies e requestTimeouts não são relevantes para tráfego WebSocket. Não há nenhuma alteração necessária na regra de roteamento. Regra de roteamento 'Básica' deve continuar a ser usado para vincular o ouvinte apropriado para o pool de endereços de back-end correspondente. 

    "requestRoutingRules": [{
        "name": "<ruleName1>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }
        }

    }, {
        "name": "<ruleName2>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }

        }
    }]

## <a name="websocket-enabled-backend"></a>WebSocket habilitado back-end

Seu back-end deve ter um servidor de web HTTP/HTTPS em execução no configurado porta (normalmente, 80/443) para WebSocket trabalhar. Esse requisito é porque o protocolo WebSocket exige o handshake inicial sejam HTTP com atualização para o protocolo WebSocket como um campo de cabeçalho.

    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13

Outro motivo para isso é que esse teste de integridade de back-end do aplicativo gateway oferece suporte apenas protocolos HTTP/HTTPS. Se o servidor back-end não responder à HTTP/HTTPS testes, ela poderia ser utilizado sair do pool de back-end e sem solicitações incluindo solicitações de WebSocket, seria alcançar este back-end.

## <a name="next-steps"></a>Próximas etapas

Depois de conhecer WebSocket suporte, vá para [criar um gateway de aplicativo](application-gateway-create-gateway.md) para começar com um aplicativo de web WebSocket habilitado.
