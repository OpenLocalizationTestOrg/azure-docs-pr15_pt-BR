<properties
    pageTitle="O que é retransmissão Azure? | Microsoft Azure"
    description="Visão geral de retransmissão Azure"
    services="service-bus"
    documentationCenter=".net"
    authors="banisadr"
    manager="timlt"
    editor="" />

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="babanisa" />

# <a name="what-is-azure-relay"></a>O que é Azure retransmissão?

Serviço de retransmissão Azure facilita seus aplicativos híbridos habilitando com segurança expor serviços que residem dentro de uma rede corporativos para a nuvem pública, sem precisar abrir um conexão de firewall, ou exigem mudanças intrusivas uma infraestrutura de rede corporativa. Retransmissão oferece suporte a uma variedade de protocolos de transporte diferentes e padrões de serviços da web.

O serviço de retransmissão suporta tradicional unidirecional solicitação/resposta e tráfego de ponto-a-ponto. Ele também dá suporte à distribuição de evento em escopo de internet para habilitar cenários publicar/assinar e comunicação bidirecional soquete para eficiência maior ponto a ponto. 

No padrão de transferência de retransmissão de dados, um serviço de local conecta o serviço de retransmissão por meio de uma porta de saída e cria um soquete bidirecional para comunicação vinculado a um endereço de reunião particular. O cliente, em seguida, pode se comunicar com o serviço de local enviando o tráfego para o serviço de retransmissão direcionamento o endereço de reunião. O serviço de retransmissão será então "retransmissão" dados para o serviço de local por meio de um soquete bidirecional dedicado a cada cliente. O cliente não precisa de uma conexão direta para o serviço no local, não é necessário saber onde o serviço reside e o serviço de local não precisa qualquer entradas portas abertas no firewall.

Os elementos principais recursos oferecidos pelo retransmissão estão comunicação bidirecional, sem buffer limites de rede com TCP semelhante a otimização, descoberta de ponto de extremidade, status de conectividade e sobrepostos segurança de ponto de extremidade. Recursos de retransmissão diferem tecnologias de integração de nível de rede como VPN em que ele pode ser com escopo de ponto de extremidade de um único aplicativo em uma única máquina, enquanto tecnologia VPN é muito mais intrusiva, pois depende de alterar o ambiente de rede.

Retransmissão Azure tem dois recursos:

1. [Conexões de híbrido](#hybrid-connections) - usa a abrir padrão Web Sockets habilitação dos cenários de várias plataformas

2. [WCF retransmissões](#wcf-relays) - usa o Windows Communication Foundation para habilitar chamadas de procedimento remoto

Híbrido conexões e o WCF retransmissões habilitam conexão segura para assests que existem dentro de uma rede corporativos. Uso de uma acima da outra depende de suas necessidades específicas detalhadas abaixo:

|                                    | Retransmissão WCF | Conexões de híbrido |
| ---------------------------------- |:---------:|:------------------:|
| **WCF**                            |     x     |                    |
| **Núcleo do .NET**                      |           |         x          |
| **.NET framework**                 |     x     |         x          |
| **JavaScript/NodeJS**              |           |         x          |
| **Java***                          |           |         x          |
| **Protocolo aberto baseada em padrões**  |           |         x          |
| **Vários modelos de programação de RPC** |           |         x          |
*<sub>Por disponibilidade geral</sub>

## <a name="hybrid-connections"></a>Conexões de híbrido

Conexões de híbrido do Azure retransmissão recurso é uma evolução segura, protocolo abrir os recursos de retransmissão existente que pode ser implementado em qualquer plataforma e em qualquer idioma que tenha um recurso WebSocket básico, que inclui explicitamente a API WebSocket em comum navegadores da web. Conexões de híbrido baseia-se em HTTP e WebSocket.

## <a name="wcf-relays"></a>WCF retransmissões

A retransmissão de WCF funciona para o completo .NET Framework (NETFX) e para WCF. Você pode iniciar a conexão entre o seu serviço no local e o serviço de retransmissão usando um conjunto de ligações de retransmissão"WCF". Nos bastidores, as associações de retransmissão mapeiam para novos elementos de ligação de transporte projetados para criar componentes de canal do WCF que se integram ao barramento de serviço na nuvem.

## <a name="service-history"></a>Histórico de serviço

Conexões de híbrido transplants ao primeiro, chamado igualmente o recurso de "Serviços BizTalk" que foi criado no Azure serviço barramento WCF Relay. O novo recurso de conexões de híbrido complementa a retransmissão de WCF existente e esses recursos dois service continuará a existir lado a lado no serviço de retransmissão para um futuro; compartilhar um gateway comum, mas são caso contrário implementações diferentes.

Retransmissão de WCF é a retransmissão herdada oferecendo que muitos clientes já podem usar com seus modelos de programação do WCF.

## <a name="next-steps"></a>Próximas etapas:

- [Retransmissão perguntas Frequentes](relay-faq.md)
- [Criar um namespace](relay-create-namespace-portal.md)
- [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Introdução ao nó](relay-hybrid-connections-node-get-started.md)