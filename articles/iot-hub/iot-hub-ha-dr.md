<properties
 pageTitle="Hub de IoT HA e DR | Microsoft Azure"
 description="Descreve recursos que ajudam a desenvolver soluções altamente disponíveis do IoT com desastres recursos de recuperação."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="elioda"/>

# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Hub de IoT alta disponibilidade e recuperação de desastres

Como um serviço Azure, IoT Hub fornece alta disponibilidade (HA) usando redundâncias no nível da região Azure, sem qualquer trabalho adicional exigido pela solução. Além disso, o Azure oferece vários recursos que ajudam a criar soluções com recursos de recuperação (DR) desastres ou disponibilidade de região entre, se necessário. Você deve projetar e preparar suas soluções para tirar proveito desses recursos de DR se você quiser fornecer global, região de cruz alta disponibilidade para dispositivos ou usuários. O artigo [Orientações técnicas de continuidade de negócios de Azure](../resiliency/resiliency-technical-guidance.md) descreve os recursos internos do Azure para continuidade de negócios e DR. O papel de [recuperação de dados e alta disponibilidade para aplicativos do Azure][] fornece orientação de arquitetura sobre estratégias para aplicativos do Azure atingir HA e DR.

## <a name="azure-iot-hub-dr"></a>Azure IoT Hub DR
Além de região UE HA, IoT Hub implementa mecanismos de failover de recuperação de dados que exigem sem interação por parte do usuário. IoT Hub DR auto é iniciada e tem um objetivo de tempo de recuperação (RTO) das horas de 2-26 e os seguintes objetivos de ponto de recuperação (RPOs).

| Funcionalidade | RPO |
| ------------- | --- |
| Disponibilidade do serviço para operações de registro e comunicação | Possível perda de CName |
| Dados de identidade no registro de identidade de dispositivo | 0-5 minutos de perda de dados |
| Mensagens de dispositivo à nuvem | Todas as mensagens não lidas são perdidas |
| Operações de monitoramento de mensagens | Todas as mensagens não lidas são perdidas |
| Mensagens de nuvem para dispositivo | 0-5 minutos de perda de dados |
| Fila de nuvem para dispositivo comentários | Todas as mensagens não lidas são perdidas |

## <a name="regional-failover-with-iot-hub"></a>Failover regional com IoT Hub

Um tratamento completo topologias de implantação em soluções IoT está fora do escopo deste artigo, mas para fins de alta disponibilidade e recuperação consideraremos o modelo de implantação de *failover regional* .

Em um modelo de failover regionais, back-end solução é executado principalmente em um data center local, mas um hub IoT adicional e back-end são implantados em outro local do data center para fins de failover, caso o hub IoT do data center principal sofre uma interrupção ou a conectividade de rede do dispositivo ao data center principal alguma forma for interrompida. Dispositivos usam um ponto de extremidade do serviço secundário sempre que o gateway principal não pode ser acessado. Com um recurso de failover entre região, a disponibilidade de solução pode ser melhorada além a alta disponibilidade de uma única região.

Em um alto nível, para implementar um modelo de failover regionais com IoT Hub, você precisará do seguinte.

* **Um hub de IoT e dispositivo roteamento lógica secundário**: no caso de uma interrupção do serviço na sua região primária, dispositivos devem iniciar a conexão com sua região secundário. Devido à natureza reconhecer o estado da maioria dos serviços envolvidos, é comum para administradores de solução acionar o processo de failover entre região. A melhor maneira de comunicar o novo ponto de extremidade para dispositivos, mantendo o controle do processo, é regularmente a verificação de um serviço de *Assistente* para o ponto de extremidade ativo atual. O serviço de assistente pode ser um aplicativo web simples que é replicado e mantido acessível usando técnicas de redirecionamento de DNS (por exemplo, usando o [Gerenciador de tráfego do Azure][]).
* **Replicação de registro de identidade** - para ser utilizada, o secundário IoT hub deve conter todas as identidades do dispositivo que podem se conectar à solução. A solução deve manter backups replicados geográfica de identidades do dispositivo e carregue-os para o hub de IoT secundário antes de trocar o ponto de extremidade ativo para os dispositivos. A funcionalidade de exportação de identidade de dispositivo do IoT Hub é muito útil neste contexto. Para obter mais informações, consulte [Guia Desenvolvedor do Hub IoT - registro de identidade][].
* **Mesclando lógica** - quando a região principal fica disponível novamente, todo o estado e os dados que foram criados no site secundário devem ser migrados de volta para a região principal. Isso principalmente se relaciona a identidades do dispositivo e meta dados de aplicativos, que devem ser mesclados com o hub de IoT primário e qualquer outra loja de aplicativos específicos na região primária. Para simplificar essa etapa, geralmente é recomendável que você use idempotente operações. Isso minimiza efeitos lado não apenas de distribuição consistente eventual de eventos, mas também de duplicatas ou entrega de fora da ordem de eventos. Além disso, a lógica do aplicativo deve ser projetada para suportar possíveis inconsistências ou "ligeiramente" sair do estado de data. Isso é devido ao tempo adicional necessário para o sistema "consertar" com base em objetivos de ponto de recuperação (RPO).

## <a name="next-steps"></a>Próximas etapas

Siga estes links para saber mais sobre Azure IoT Hub:

- [Introdução ao IoT Hubs (Tutorial)][lnk-get-started]
- [O que é Azure IoT Hub?][]

[Recuperação de dados e alta disponibilidade para aplicativos do Azure]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Gerenciador de tráfego Azure]: https://azure.microsoft.com/documentation/services/traffic-manager/
[Guia de desenvolvedor do IoT Hub - registro de identidade]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[O que é Azure IoT Hub?]: iot-hub-what-is-iot-hub.md
