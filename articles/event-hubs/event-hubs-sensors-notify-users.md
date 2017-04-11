<properties 
   pageTitle="Notificar os usuários de dados recebidos de outros sistemas ou sensores | Microsoft Azure"
   description="Descreve como usar o evento Hubs para notificar os usuários de dados."
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="spyros;sethm" />

# <a name="notify-users-of-data-received-from-sensors-or-other-systems"></a>Notificar os usuários de dados recebidos de sensores ou outros sistemas

Suponha que você tenha um aplicativo que monitora dados em tempo real ou produz relatórios em uma agenda. Se você examinar o site no qual os gráficos em tempo real ou relatórios são exibidos, talvez você veja algo que requer ação. E se você precisa ser alertado sobre essas situações, ao invés de confiar em lembrar verificar o site? Imagine que você possui uma luz crescer em um efeito e você precisa saber imediatamente se light sai. Uma maneira de fazer isso seria com um sensor de luz no efeito, organizando sejam enviadas um email se light está desativado.

![][1]

Em outro cenário, imagine que você executa um recurso utilizando animais, e você deve ser alertado sobre níveis de fornecimento de estoque baixo. Por exemplo, você pode organizar a ser enviado uma mensagem de texto de seu sistema de ERP se seu estoque de armazém alimentos cachorro caiu para um nível crítico. 

![][2]

O problema é como obter informações críticas quando determinadas condições forem atendidas, não quando você chegar ao redor de check-out de um relatório estático. Se você estiver usando um [Hub de evento do Azure][] ou [Azure IoT Hub][] para receber dados de dispositivos ou aplicativos corporativos como [Dynamics AX][], você terá várias opções para como processá-los. Você pode exibi-los em um site, você pode analisá-los, você pode armazená-los e você pode usá-los para acionar comandos para fazer algo. Para fazer isso, você pode usar ferramentas poderosas como [Sites do Azure][], [SQL Azure][], [HDInsight][], [Cortana inteligência Suite][], [IoT Suite][], [Lógica de aplicativos][]ou [Hubs de notificação do Azure][]. Mas, às vezes, tudo o que você deseja fazer é enviar dados para alguém com um mínimo de sobrecarga. Para mostrar como fazer isso com apenas um pouco de código, fornecemos uma nova amostra, [AppToNotifyUsers][]. Opções incluídas são emails (SMTP), SMS e telefone.

## <a name="application-structure"></a>Estrutura do aplicativo

O aplicativo é escrito em c# e o arquivo Leiame na amostra contém todas as informações de que você precisa modificar, criar e publicar o aplicativo. As seções a seguir fornecem uma visão geral do que o aplicativo faz.

Vamos começar com a suposição de que você tenha eventos críticos sendo enviados a um Hub de evento do Azure ou IoT Hub. Qualquer hub fará, desde que você tenha acesso a ele e souber a cadeia de conexão.

Se você não tiver um hub Hub de evento ou IoT, você pode configurar facilmente um ambiente de teste com uma proteção Arduino e um Pi framboesa, seguindo as instruções do projeto [Se conectar a pontos](https://github.com/Azure/connectthedots) . O sensor de luz na blindagem Arduino envia os níveis de luz através o Pi a um [Hub de evento do Azure][] (**ehdevices**) e um trabalho de [Análise de fluxo de Azure](https://azure.microsoft.com/services/stream-analytics/) envia alertas para um segundo hub de evento (**ehalerts**) se os níveis de luz recebidos ficar abaixo de um determinado nível.

Quando **AppToNotify** for iniciado, ele lê um arquivo de configuração (App) para obter a URL e as credenciais para o Hub de eventos recebendo os alertas. Em seguida, ele gera um processo para monitorar continuamente que Hub de evento para qualquer mensagem que venha a – contanto que você tenha pode acessar a URL para o Hub de evento ou IoT hub e credenciais válidas, este código do evento Hubs leitor continuamente lerá o que vem. Durante a inicialização, o aplicativo também lê a URL e as credenciais para o serviço de mensagens (telefone de email, SMS,) que você deseja usar e o nome/endereço do remetente e uma lista de destinatários.

Depois que o monitor de evento Hub detectar uma mensagem, ele aciona um processo que envia a mensagem usando o método especificado no arquivo de configuração. Observe que ele envia cada mensagem detecta. Se você definir o monitor para apontar a um Hub de evento que recebe dez mensagens por segundo, o remetente envia dez mensagens por segundo – dez emails por segundo, mensagens de SMS de dez por segundo, dez chamadas telefônicas por segundo. Por que motivo, certifique-se de que você monitora um Hub de evento que recebe apenas os alertas que precisam ser enviado, não um Hub de evento que recebe todos os dados processados de seu sensores ou aplicativos.

## <a name="applicability"></a>Capacidade de aplicação

O código neste exemplo só mostra como monitorar Hubs de eventos e como chamar serviços de mensagens externos que você deseja adicionar essa funcionalidade ao seu aplicativo. Observe que essa solução é um DIY, voltada para desenvolvedores exemplo apenas. Ele não atender aos requisitos de empresa como redundância, falhas sobre, reinicie após falha, etc. Para obter mais soluções de produção e abrangente, consulte o seguinte:

- Usando conectores ou usando o serviço de [Aplicativos do Azure lógica](../app-service-logic/app-service-logic-connectors-list.md) de notificações de envio.
- Usando [Hubs de notificação do Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx), conforme descrito no blog [transmissão as notificações por push para milhões de dispositivos móveis usando Hubs de notificação do Azure](http://weblogs.asp.net/scottgu/broadcast-push-notifications-to-millions-of-mobile-devices-using-windows-azure-notification-hubs). 

## <a name="next-steps"></a>Próximas etapas

É simples criar um serviço de notificação simples que envia emails ou mensagens de texto para destinatários, chamadas ou -los, para dados de retransmissão recebidos por um Hub de evento ou IoT Hub. Para implantar a solução para notificar os usuários com base em dados recebidos por estas hubs, visite [AppToNotifyUsers][].

Para obter mais informações sobre estas hubs, consulte os seguintes artigos:

- [Hubs de evento Azure]
- [Hub de IoT Azure]
- Comece com um [tutorial Hubs de evento].
- Um [aplicativo de exemplo que usa o evento Hubs]completo.

Para implantar a solução para notificar os usuários com base em dados recebidos por estas hubs, visite:

- [AppToNotifyUsers][]

[Tutorial de Hubs de evento]: event-hubs-csharp-ephcs-getstarted.md
[Hub de IoT Azure]: https://azure.microsoft.com/services/iot-hub/
[Hubs de evento Azure]: https://azure.microsoft.com/services/event-hubs/
[Hub de evento Azure]: https://azure.microsoft.com/services/event-hubs/
[aplicativo de exemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[AppToNotifyUsers]: https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications
[Dynamics AX]: http://www.microsoft.com/dynamics/erp-ax-overview.aspx
[Sites Azure]: https://azure.microsoft.com/services/app-service/web/
[Do SQL Azure]: https://azure.microsoft.com/services/sql-database/
[HDInsight]: https://azure.microsoft.com/services/hdinsight/
[Pacote de inteligência de Cortana]: http://www.microsoft.com/server-cloud/cortana-analytics-suite/Overview.aspx?WT.srch=1&WT.mc_ID=SEM_lLFwOJm3&bknode=BlueKai
[IoT Suite]: https://azure.microsoft.com/solutions/iot-suite/
[Aplicativos de lógica]: https://azure.microsoft.com/services/app-service/logic/
[Hubs de notificação do Azure]: https://azure.microsoft.com/services/notification-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/services/stream-analytics/
 
[1]: ./media/event-hubs-sensors-notify-users/event-hubs-sensor-alert.png
[2]: ./media/event-hubs-sensors-notify-users/event-hubs-erp-alert.png