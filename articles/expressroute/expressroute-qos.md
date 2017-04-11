<properties
   pageTitle="Requisitos de QoS para rota expressa | Microsoft Azure"
   description="Esta página fornece requisitos detalhados para configurar e gerenciar QoS para rota expressa circuitos."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="expressroute-qos-requirements"></a>Requisitos de ExpressRoute QoS

Skype for Business tem várias cargas de trabalho que exigem tratamento de QoS diferenciado. Se você planeja consumir serviços de voz por meio de rota expressa, você deve atender aos requisitos descritos abaixo.

![](./media/expressroute-qos/expressroute-qos.png)

>[AZURE.NOTE] Requisitos de QoS se aplicam ao Microsoft correspondência somente. Os valores DSCP em seu tráfego de rede recebido em correspondência público Azure e correspondência particular Azure serão redefinidos como 0. 

A tabela a seguir fornece uma lista de marcações DSCP usado pelo Skype for Business. Para obter mais informações, consulte [Gerenciando QoS para Skype for Business](https://technet.microsoft.com/library/gg405409.aspx) .

| **Classe de tráfego** | **Tratamento (marcação DSCP)** | **Skype para cargas de trabalho de negócios** |
|---|---|---|
| **Voz** | EF (46) | Skype / voz do Lync |
| **Interativo** | AF41 (34) | Vídeo |
|   | AF21 (18) | Compartilhamento de aplicativo | 
| **Padrão** | AF11 (10) | Transferência de arquivo|
|   | CS0 (0) | Mais alguma coisa| 


- Você deve classificar as cargas de trabalho e marcar os valores DSCP corretos. Siga a orientação fornecida [aqui](https://technet.microsoft.com/library/gg405409.aspx) sobre como definir marcações DSCP em sua rede.

- Você deve configurar e suporte várias filas de QoS dentro de sua rede. Voz deve ser uma classe autônoma e receber o tratamento de EF especificado no RFC 3246. 

- Você pode decidir o mecanismo de fila, política de detecção de congestionamento e alocação de largura de banda por classe de tráfego. Mas, o DSCP marcando para Skype para cargas de trabalho de negócios deve ser preservado. Se você estiver usando as marcações de DSCP não listadas acima, por exemplo, AF31 (26), você deve reconfigurar esse valor de DSCP a 0 antes de enviar o pacote para a Microsoft. Microsoft apenas envia pacotes marcados com o valor DSCP mostrado na tabela acima. 

## <a name="next-steps"></a>Próximas etapas

- Consulte os requisitos para [Roteamento](expressroute-routing.md) e [NAT](expressroute-nat.md).
- Consulte os links a seguir para configurar sua conexão de rota expressa.

    - [Criar um circuito de rota expressa](expressroute-howto-circuit-classic.md)
    - [Configurar o roteamento](expressroute-howto-routing-classic.md)
    - [Vincular um VNet a um circuito de rota expressa](expressroute-howto-linkvnet-classic.md)
