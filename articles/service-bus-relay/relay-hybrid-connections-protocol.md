<properties 
    pageTitle="Protocolo de conexões de híbrido de retransmissão Azure | Microsoft Azure"
    description="zure guia de protocolo de conexões de híbrido de retransmissão."
    services="service-bus"
    documentationCenter="na"
    authors="clemensv"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="sethm" />

# <a name="azure-relay-hybrid-connections-protocol"></a>Protocolo de conexões do Azure retransmissão híbrido

Retransmissão Azure é um dos principais recursos pilares da plataforma barramento de serviço do Azure. Novo recurso de "Híbrido conexões" da retransmissão é uma evolução segura, abrir-protocolo com base em HTTP e WebSocket.

Ele substitui o anterior, chamado igualmente o recurso de "Serviços BizTalk" que foi criado em uma fundação de protocolo proprietário. A integração de conexões híbrido em serviços de aplicativo do Azure continuarão a funcionar como-é.

"Híbrido conexões" permite estabelecer comunicação bidirecional, binário fluxo entre dois aplicativos em rede, por meio da qual uma ou ambas as partes podem residem atrás de Firewalls ou NATs.

Este documento descreve as interações do lado do cliente com a retransmissão de conexões híbrido para conectar clientes nas funções de ouvinte e remetente e como ouvintes aceitam novas conexões.

## <a name="interaction-model"></a>Modelo de interação

A retransmissão de conexões híbrido conecta duas partes, fornecendo um ponto de reunião na nuvem Azure que os participantes podem descobrir e conectar a perspectiva da própria rede. Desse ponto de reunião é chamado "Híbrido Conexão" neste e em outra documentação, nas APIs e também no Portal do Azure. O ponto de extremidade do serviço de conexões híbrido será chamado como "serviço" para o restante deste documento.

O modelo de interação depende da nomenclatura estabelecida por muitos outros APIs de rede:

Não há um ouvinte que indica primeiro prontidão para lidar com conexões de entrada e subsequentemente aceitá-los conforme elas são recebidas. No outro lado, há um cliente de conexão que o ouvinte, esperando dessa conexão aceitas para estabelecer um caminho de comunicação bidirecional se conectará. "Conectar", "Ouvir", "Aceitar" é os mesmos termos que você encontrará no soquete maioria das APIs.

Qualquer modelo de comunicação de retransmissão tem qualquer uma das partes fazer conexões de saída em direção a um ponto de extremidade do serviço, que torna o "ouvinte" também um "cliente" em uso colloquial e também pode causar outras sobrecargas terminologia; a terminologia precisa, portanto, usamos para conexões híbrida é da seguinte maneira:

Os programas nos dois lados de uma conexão são chamados de "cliente", pois são clientes do serviço. O cliente que aguarda e aceita conexões é o "ouvinte" ou considerados na "função ouvinte". O cliente que inicia uma nova conexão em direção a um ouvinte por meio do serviço é chamado de "remetente" ou a função"remetente".

## <a name="listener-interactions"></a>Interações de ouvinte

O ouvinte tem quatro interações com o serviço; todos os detalhes de fios descritos posteriormente neste documento na seção de referência.

### <a name="listen"></a>Ouvir

Para indicar prontidão para o serviço que um ouvinte está pronto para aceitar conexões, ele cria uma conexão de soquete da web de saída. O handshake de conexão recebe o nome de uma Conexão de híbrido configurado no namespace retransmissão e um token de segurança que confere "Ouvir" com esse nome. Quando o soquete web for aceita pelo serviço, o registro está concluído e soquete web estabelecida é mantido ativo como "canal controle" para habilitar todas as interações subsequentes. O serviço permite até 25 ouvintes simultâneas em uma Conexão de híbrido. Se houver ouvintes 2 ou mais ativos, conexões de entrada serão ser equilibradas-las em ordem aleatória; não é garantia de distribuição justa.

### <a name="accept"></a>Aceitar

Sempre que um remetente abre uma nova conexão no serviço, o serviço será escolha e notificar um do ouvinte ativo na Conexão híbrido de. A notificação é enviada para o ouvinte sobre o canal de controle aberto como uma mensagem JSON que contém a URL do ponto de extremidade soquete Web que o ouvinte deve se conectar para aceitar a conexão.

A URL pode e deve ser usada diretamente pelo ouvinte sem qualquer trabalho extra; as informações codificadas só são válidas por um curto período de tempo, essencialmente para desde que o remetente está disposto a aguardar para a conexão seja estabelecida ponta a ponta, mas até no máximo 30 segundos. A URL só pode ser usada para tentativa de uma conexão com êxito. Assim que a conexão de soquete Web com a URL de reunião for estabelecida, todas as atividades mais neste soquete Web serão enviadas de e para o remetente, sem qualquer intervenção ou interpretação pelo serviço.

### <a name="renew"></a>Renovar 

O token de segurança que deve ser usado para registrar o ouvinte e manter o canal de controle pode expirar enquanto o ouvinte estiver ativo. A expiração do token não afetará conexões em andamento, mas ele causará o canal de controle para ser descartados pelo serviço em ou logo após o instantâneas de expiração. O gesto de "renovação" é uma mensagem JSON que o ouvinte pode enviar para substituir o símbolo associado com o canal de controle, para que o canal de controle pode ser mantido por longos períodos.

### <a name="ping"></a>Ping 

Se o canal de controle permanecer ocioso por um longo tempo, intermediários no caminho, como carga balanceadores ou NATs podem cancelar a conexão de TCP. O gesto de "ping" evita que enviando uma pequena quantidade de dados no canal que lembra todos na rota de rede que a conexão deve ser de funcionamento e ela também serve como um teste de execução para o ouvinte. Se o ping falhar, o canal de controle deve ser considerado inutilizável e o ouvinte deve se reconectar.

## <a name="sender-interaction"></a>Interação de remetente

O remetente tem apenas uma única interação com o serviço, ele se conecta.

### <a name="connect"></a>Conectar-se

O gesto "conectar" abre um soquete Web no serviço, fornecendo o nome da Conexão a híbrido e uma (opcional, mas necessário por padrão) token de segurança elevado permissão "Enviar" na sequência de consulta. O serviço, em seguida, interagir com o ouvinte da maneira descrito acima e tiver o ouvinte a criar uma conexão de reunião que ingressarão com este soquete da Web. Depois de soquete Web foi aceita, todas as outras interações no soquete da Web, portanto, será com um ouvinte conectado.

## <a name="interaction-summary"></a>Resumo de interação

O resultado desse modelo de interação é que o cliente remetente vem fora o handshake com o soquete Web "limpo" que está conectada a um ouvinte e que precisa sem ainda mais preâmbulos ou preparação. Isso permite que praticamente qualquer implementação de cliente de soquete Web existente prontamente aproveitar o serviço híbrido conexões apenas fornecendo uma URL construída corretamente em sua camada de cliente de soquete da Web.

A conexão de reunião soquete Web que o ouvinte obtém por meio de interação aceitar também é limpo e poderá ser entregue ao qualquer implementação de servidor de soquete Web existente com alguns mínimo abstração extra que distingue operações de "aceitar" em ouvintes de rede local da sua estrutura e operações de remoto "aceitar" híbrido conexões.

## <a name="protocol-reference"></a>Referência de protocolo

Esta seção descreve os detalhes das interações protocolo descritos acima.

Todas as conexões de soquete Web são feitas na porta 443 como uma atualização do 1.1 de HTTPS, que normalmente é extraídos pela estrutura de soquete algumas Web ou API. A descrição aqui é mantida implementação neutro, sem sugerindo uma estrutura específica.

## <a name="listener-protocol"></a>Protocolo de ouvinte

O protocolo de ouvinte consiste em duas gestos de conexão e três operações de mensagem.

### <a name="listener-control-channel-connection"></a>Conexão de canal de controle de ouvinte

O canal de controle é aberto com a criação de uma conexão de soquete Web para:

*wss: / / {namespace-endereço} /* *$servicebus* */* *hybridconnection /**{caminho}? sb-hc-action =... & sb-hc-id =... & sb-hc-token =... "*

O *endereço do namespace* é o nome de domínio totalmente qualificado do namespace Azure retransmissão que hospeda a Conexão híbridos, normalmente do formulário {*meunome}. servicebus.windows.net.*

As opções de parâmetro da sequência de consulta são os seguintes

| Parâmetro        | Necessário? | Descrição                                                                                                                                                                                        |
|--------------|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ação de hc SB | Sim       | Para a função de ouvinte o parâmetro deve ser **sb-hc-action = ouvir**                                                                                                                                |
| {caminho}       | Sim       | O caminho de namespace codificada como URL da Conexão híbrido pré-configurado para registrar esse ouvinte em. Essa expressão é acrescentada ao fixed * **$servicebus**/**hybridconnection /** * parte do caminho. |
| SB-hc-token  | Sim\*     | O ouvinte deve fornecer um válido, codificada como URL serviço barramento compartilhado Token de acesso do namespace ou Conexão de híbrido que confere o direito de **ouvir** .                                           |
| SB-hc-id     | Não        | Essa ID opcional fornecido pelo cliente permite que o rastreamento de diagnóstico de ponta a ponta.                                                                                                                             |

Se a conexão de soquete Web falhar devido ao caminho de Conexão híbrido não sendo registrado, ou um token inválido ou ausente ou algum outro erro, os comentários de erro serão fornecido usando o modelo de comentários de status de HTTP 1.1 regular. A descrição do status conterá uma rastreamento-id do erro que pode ser comunicada ao Azure suporte:

| Código | Erro          | Descrição                                                            |
|------|----------------|------------------------------------------------------------------------|
| 404  | Não encontrado      | O **caminho** do híbrido Conexão é inválido ou a URL base está incorreto |
| 401  | Não autorizado   | O token de segurança está ausente ou malformado ou inválido                  |
| 403  | Proibido      | O token de segurança não é válido para esse caminho para esta ação          |
| 500  | Erro interno | Algo está errado no serviço                                    |

Se a conexão de soquete Web intencionalmente desligar pelo serviço depois que ele tiver sido inicialmente configurado, o motivo para fazer isso serão comunicadas usando um apropriado Web soquete protocolo código de erro juntamente com uma mensagem de erro descritiva que também incluirá uma id de controle. O serviço não seja desligado o canal de controle sem encontrar uma condição de erro. Qualquer desligamento normal é controlada de cliente.

| Status de WS | Descrição                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1001      | O caminho de Conexão híbrido foi excluído ou desativado.                           |
| 1008      | O token de segurança expirou e a política de autorização, portanto, foi violada. |
| 1011      | Algo está errado dentro do serviço.                                           |

### <a name="accept-handshake"></a>Aceitar handshake 

A notificação de aceitar é enviada pelo serviço para o ouvinte sobre o canal de controle estabelecida anteriormente como uma mensagem JSON em um quadro de texto de soquete da Web. Não há nenhuma resposta a esta mensagem.

A mensagem contém um objeto JSON denominado "aceitar", que define as seguintes propriedades no momento:

-   **endereço** – a cadeia de caracteres de URL a ser usado para estabelecer soquete para o serviço Web para aceitar uma conexão de entrada.

-   **id** – o identificador exclusivo para esta conexão. Se a identificação foi fornecida pelo cliente do remetente, ele é o valor de remetente fornecido, caso contrário, é um valor gerado pelo sistema.

-   **connectHeaders** – todos os cabeçalhos HTTP que foram fornecidas para o ponto de extremidade de retransmissão pelo remetente, que também inclui o protocolo Sec-WebSocket e os cabeçalhos Sec-WebSocket-Extensions.

| Aceitar mensagem                                                                     |
|------------------------------------------------------------------------------------|
```json
{                                                                                  
    "accept" : {                                                                                    
        "address" : "wss://168.61.148.205:443/$servicebus/hybridconnection/{path}?...",                                                                          
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736\_G0\_G1",                         
        "connectHeaders" : {                                                                
            "Host" : "…",                                                                       
            "Sec-WebSocket-Protocol" : "…",                                                     
            "Sec-WebSocket-Extensions" : "…"                                                    
        }                                                                                     
    }
}
```

A URL de endereço fornecida na mensagem JSON é usada pelo ouvinte estabelecer soquete Web para aceitar ou rejeitar o soquete do remetente.

#### <a name="accepting-the-socket"></a>Aceitar o soquete

Para aceitar, o ouvinte estabelece uma conexão WebSocket para o endereço fornecido.

Mente que para o período de visualização, o endereço URI pode usar um endereço IP vazio e o certificado de TLS fornecido pelo servidor falhará validação nesse endereço. Isso vai ser corrigido durante a visualização.

Se a mensagem "aceitar" carrega um cabeçalho "Sec-WebSocket-Protocol", ele espera que o ouvinte só aceitará o WebSocket se ele dá suporte a esse protocolo e que define o cabeçalho que o WebSocket é estabelecida.

O mesmo se aplica ao cabeçalho "Sec-WebSocket-Extensions". Se o framework oferece suporte a uma extensão, ele deve definir o cabeçalho para a resposta do lado do *servidor* do handshake "Sec-WebSocket-Extensions" necessário para a extensão.

A URL deve ser usada como-é para estabelecer o soquete aceitar, mas contém os seguintes parâmetros:

| Parâmetro        | Necessário? | Descrição                                                                                                                                                                                                                                                                                   |
|--------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ação de hc SB | Sim       | Para aceitar um soquete o parâmetro deve ser **sb-hc-action = aceitar**                                                                                                                                                                                                                          |
| {caminho}       | Sim       | O caminho de namespace codificada como URL da Conexão híbrido pré-configurado para registrar esse ouvinte em. Essa expressão é acrescentada ao fixed * **$servicebus**/**hybridconnection /** * parte do caminho.                                                                                            
                                                                                                                                                                                                                                                                                                                           
                            The path expression MAY be extended with a suffix and a query string expression that follows the registered name after a separating forward slash.                                                                                                                                             
                                                                                                                                                                                                                                                                                                                           
                            This allows the sender client to pass dispatch arguments to the accepting listener when it is not possible to include HTTP headers.                                                                                                                                                            
                                                                                                                                                                                                                                                                                                                           
                            The expectation is that the listener framework will parse out the fixed path portion and the registered name from the path and make the remainder, possibly without any query string arguments prefixed by “sb-“, available to the application for deciding whether to accept the connection.  
                                                                                                                                                                                                                                                                                                                           
                            For more detail see the “Sender Protocol” section below.                                                                                                                                                                                                                                       |
| SB-hc-id | No        | Consulte Descrição do **id** acima.                                                                                                                                                                                                                                                              |

Se houver um erro, o serviço pode responder da seguinte maneira:

| Código | Erro          | Descrição                         |
|------|----------------|-------------------------------------|
| 403  | Proibido      | A URL não é válida.               |
| 500  | Erro interno | Algo está errado no serviço |

Depois que a conexão tiver sido estabelecida, o servidor será desligado soquete Web quando o soquete de Web do remetente desliga, ou com o seguinte status

| Status de WS | Descrição                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1001      | Cliente do remetente desliga a conexão                                        |
| 1001      | O caminho de Conexão híbrido foi excluído ou desativado.                           |
| 1008      | O token de segurança expirou e a política de autorização, portanto, foi violada. |
| 1011      | Algo está errado dentro do serviço.                                           |

#### <a name="rejecting-the-socket"></a>Rejeitando soquete

Rejeitar soquete após inspecionar a mensagem "aceitar" requer um handshake semelhante para que o código de status e a descrição de status comunicar o motivo para a rejeição podem fluir volta ao remetente.

A opção de design de protocolo aqui é usar um handshake WebSocket (que é projetado para finalizar em um estado de erro definido) para que implementações de cliente ouvinte pode continuar a confiar em um cliente WebSocket e não é necessário utilizar um extra, vazio cliente HTTP.

Para rejeitar o soquete, o cliente leva o endereço URI da mensagem "aceitar" e acrescenta dois parâmetros de cadeia de caracteres de consulta para ele:

| Parâmetro             | Necessário? | Descrição                             |
|-------------------|-----------|-----------------------------------------|
| statusCode        | Sim       | Código de status HTTP numérico                |
| statusDescription | Sim       | Humano legível motivo para a rejeição |

O URI resultante é usado para estabelecer uma conexão WebSocket; novamente, se de que o certificado TLS pode não coincidir com o endereço durante a visualização, portanto validação talvez precise ser desabilitado.

Ao concluir corretamente, esse handshake intencionalmente falhará com um código de erro HTTP 410, desde que nenhuma WebSocket foi estabelecida. Se ocorrer um erro, estas são as opções:

| Código | Erro          | Descrição                         |
|------|----------------|-------------------------------------|
| 403  | Proibido      | A URL não é válida.               |
| 500  | Erro interno | Algo está errado no serviço |

### <a name="listener-token-renewal"></a>Renovação de token de ouvinte

Quando o token do ouvinte está prestes a expirar, ele pode substituí-lo enviando uma mensagem de quadro de texto para o serviço por meio do canal de controle estabelecidas. A mensagem contém um objeto JSON denominado "renewToken", que define a propriedade seguinte neste momento:

-   **token** – um válido, codificada como URL serviço barramento compartilhado Token de acesso do namespace ou Conexão de híbrido que confere o direito de **ouvir** .

| renewToken mensagem                                                                                                                                                    
|------------------------------------------------------------------------------------------------|
```json
{
    "renewToken" : {      
        "token" : "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fHybridConnection1%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
    }                                                                                                                                                            
}
```                                                                                                                                                                      

Se a validação de token falhar, acesso negado e serviço de nuvem fechará o websocket de canal de controle com um erro, caso contrário, não há nenhuma resposta.

| Status de WS | Descrição                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1008      | O token de segurança expirou e a política de autorização, portanto, foi violada. |

<a name="sender-protocol"></a>Protocolo de remetente
---------------

O protocolo de remetente é efetivamente idêntico ao como um ouvinte é estabelecido. O objetivo é o máximo de transparência para o soquete da Web de ponta a ponta. O endereço para se conectar ao é o mesmo que o ouvinte, mas a "ação" difere e o token precisa uma permissão diferente:

*wss: / / {namespace-endereço} /* *$servicebus* */* *hybridconnection /**{caminho}? sb-hc-action =... & sb-hc-id = … \[& sbc-hc-token = … \]*

O *endereço do namespace* é o nome de domínio totalmente qualificado do namespace Azure retransmissão que hospeda a Conexão híbridos, normalmente do formulário {*meunome}. servicebus.windows.net.*

A solicitação pode conter aleatório cabeçalhos HTTP extras, inclusive os definidos pelo aplicativo. Todos os cabeçalhos fornecidos fluam para o ouvinte e podem ser encontrados no objeto "connectHeader" da mensagem de controle de "aceitar".

As opções de parâmetro da sequência de consulta são os seguintes

| Parâmetro        | Necessário? | Descrição                                                                                                                                                                                                       |
|--------------|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ação de hc SB | Sim       | Para a função de ouvinte o parâmetro deve ser **ação = conectar**                                                                                                                                                    |
| {caminho}       | Sim       | O caminho de namespace codificada como URL da Conexão híbrido pré-configurado para registrar esse ouvinte em.                                                                                                               
                                                                                                                                                                                                                                               
                            The path expression MAY be extended with a suffix and a query string expression to communicate further                                                                                                             
                                                                                                                                                                                                                                               
                            If the Hybrid Connection is registered under the path “hyco”, the path expression can be “**hyco/**suffix?param=value&…” followed by the query string parameters defined here. A complete expression may then be:  
                                                                                                                                                                                                                                               
                            *wss://{ns}/**$servicebus**/**hybridconnection/*** **hyco/**suffix?param=value*& sb-hc-action=…&sb-hc-id=…\[&sbc-hc-token=…\]*                                                                                     
                                                                                                                                                                                                                                               
                            The path expression is passed through to the listener in the address URI contained in the “accept” control message.                                                                                                |
| símbolo de hc SB | Yes\*     | O ouvinte deve fornecer um válido, codificada como URL serviço barramento compartilhado Token de acesso do namespace ou Conexão de híbrido que confere o direito de **Enviar** .                                                            | | SB-hc-id | No        | Uma identificação opcional que permite o rastreamento de diagnóstico de ponta a ponta e é disponibilizada para o ouvinte durante o handshake de aceitar.                                                                                       |

Se a conexão de soquete Web falhar devido ao caminho de Conexão híbrido não sendo registrado, ou um token inválido ou ausente ou algum outro erro, os comentários de erro serão fornecido usando o modelo de comentários de status de HTTP 1.1 regular. A descrição do status conterá uma rastreamento-id do erro que pode ser comunicada ao Azure suporte:

| Código | Erro          | Descrição                                                            |
|------|----------------|------------------------------------------------------------------------|
| 404  | Não encontrado      | O **caminho** do híbrido Conexão é inválido ou a URL base está incorreto |
| 401  | Não autorizado   | O token de segurança está ausente ou malformado ou inválido                  |
| 403  | Proibido      | O token de segurança não é válido para esse caminho para esta ação          |
| 500  | Erro interno | Algo está errado no serviço                                    |

Se a conexão de soquete Web intencionalmente desligar pelo serviço depois que ele tiver sido inicialmente configurado, o motivo para fazer isso serão comunicadas usando um apropriado Web soquete protocolo código de erro juntamente com uma mensagem de erro descritiva que também incluirá uma id de controle.

| Status de WS | Descrição                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1000      | O ouvinte desligar o soquete.                                                 |
| 1001      | O caminho de Conexão híbrido foi excluído ou desativado.                           |
| 1008      | O token de segurança expirou e a política de autorização, portanto, foi violada. |
| 1011      | Algo está errado dentro do serviço.                                           |

## <a name="next-steps"></a>Próximas etapas:

- [Retransmissão perguntas Frequentes](relay-faq.md)
- [Criar um namespace](relay-create-namespace-portal.md)
- [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Introdução ao nó](relay-hybrid-connections-node-get-started.md)