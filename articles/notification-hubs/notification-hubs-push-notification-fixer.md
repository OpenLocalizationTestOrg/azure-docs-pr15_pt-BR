<properties 
    pageTitle="Notificação Azure Hubs - diretrizes de diagnóstico" 
    description="Diretrizes sobre como diagnosticar problemas comuns com Hubs de notificação do Azure." 
    services="notification-hubs" 
    documentationCenter="Mobile" 
    authors="ysxu" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="NA" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs---diagnosis-guidelines"></a>Hubs de notificação do Azure - diretrizes de diagnóstico

##<a name="overview"></a>Visão geral

Uma das perguntas mais comuns que ouvimos de nossos clientes Hubs de notificação do Azure é como descobrir por que eles não ver uma notificação enviada de seu back-end do aplicativo são exibidos no dispositivo cliente - onde e por que as notificações descartadas e como corrigir o problema. Neste artigo veremos as diversas razões por notificações podem obter descartadas ou acabem não nos dispositivos. Vamos também por meio de maneiras nas quais você pode analisar e descobrir a causa. 

Primeiro, é essencial entender como Hubs de notificação do Azure envia notificações para os dispositivos.
![][0]

Em um fluxo de notificação de envio típico, a mensagem é enviada da do **aplicativo back-end** para **Hub de notificação do Azure (NH)** , que por sua vez faz algum processamento em todos os registros que levar em consideração as marcas configuradas e expressões de marca para determinar "destinos" ou seja, todos os registros que precisam receber a notificação de envio. Esses registros podem abranger em qualquer uma ou todas as nossas plataformas suportadas - iOS, Google, Windows, Windows Phone, dispositivos Kindle e Baidu para China Android. Depois que os destinos são estabelecidos, NH e em seguida, coloca as notificações, dividir em vários lote de registros, para a plataforma do dispositivo específica **Serviço de notificação de envio por Push (PNS)** - por exemplo, APNS para a Apple, GCM para Google etc. NH autentica com os respectivos PNS com base nas credenciais que você definir no Portal de clássico do Azure na página Configurar Hub de notificação. O PNS encaminha as notificações para os respectivos **dispositivos cliente**. Essa é a plataforma recomendada maneira de entregar notificações por push e observe que o segmento final da entrega da notificação ocorrerá entre a plataforma PNS e o dispositivo. Portanto, temos quatro componentes principais - *cliente*, *back-end do aplicativo*, *Hubs de notificação do Azure (NH)* e *Os serviços de notificação de envio por Push (PNS)* e qualquer um desses podem causar notificações sendo interrompidas. Obter mais detalhes sobre essa arquitetura está disponível na [Visão geral de Hubs de notificação].

Falha ao entregar notificações pode ocorrer durante o teste/teste inicial fase que podem indicar um problema de configuração ou pode acontecer em produção onde todos ou alguns das notificações podem estar recebendo ignorados indicando algum aplicativo mais profundo ou problema padrão de mensagens. Na seção, abaixo, veremos vários cenários de notificações ignorados desde comuns para o tipo mais raro, que algumas das quais você descobrirá óbvio e alguns outros não muito. 

##<a name="azure-notifications-hub-mis-configuration"></a>Configuração incorreta do Azure Hub de notificações 

Azure Hubs de notificação precisa autenticar no contexto do aplicativo do desenvolvedor para poder enviar notificações com êxito para os respectivos PNS. Isso se tornou possível pelo desenvolvedor criando uma conta de desenvolvedor com a plataforma respectiva (Google, Apple Windows etc.) e, em seguida, registrar seus aplicativos onde eles obter credenciais que precisam ser configurados no portal na seção configuração Hubs de notificação. Se nenhuma notificação estiver fazendo através de, primeira etapa deve ser garantir que as credenciais corretas estão configuradas no Hub do notificação correspondência-los com o aplicativo criado em sua conta de desenvolvedor específico de plataforma. Você encontrará nossos [Obtendo tutoriais de Introdução] útil para acessar esse processo de maneira passo a passo. Aqui estão algumas configurações incorreta comuns:

1. **Geral**
 
    a) Certifique-se de que seu nome de hub de notificação (sem erros de digitação) é o mesmo:

    - Onde você está registrando do cliente, 
    - Onde você está enviando notificações de back-end,  
    - Onde você configurou as credenciais PNS e 
    - Cujas credenciais SAS que você configurou no cliente e back-end. 
        
    b) Certifique-se de que você está usando as cadeias de caracteres de configuração corretas SAS no cliente e o back-end do aplicativo. Como regra, você deve estar usando o **DefaultListenSharedAccessSignature** no cliente e **DefaultFullSharedAccessSignature** no back-end aplicativo (que fornece a permissão para poder enviar notificação para o NH)

2. **Configuração do serviço de notificação por Push da Apple (APNS)**
 
    Você deve manter dois hubs diferentes - um para produção e outro para teste finalidade. Isso significa carregando o certificado que você vai usar em ambiente de área restrita a um hub separado e o certificado que você vai usar em produção a um hub separada. Não tente carregar diferentes tipos de certificados ao mesmo hub como ele pode causar falhas de notificação na linha para baixo. Se você estiver em uma posição onde você inadvertidamente carregou tipos diferentes de certificado ao mesmo hub, é recomendável excluir o hub e começar do zero. Se por algum motivo, você não é possível excluir o hub, em seguida, no mínimo, você deve excluir todos os registros existentes do hub. 

3. **Configuração de mensagens de nuvem do Google (GCM)** 

    a) Certifique-se de que você está ativando "Google Cloud mensagens para Android" em seu projeto de nuvem. 
    
    ![][2]
    
    b) Certifique-se de que você crie uma "chave de servidor" enquanto obter as credenciais que NH usará para autenticar com GCM. 
    
    ![][3]
    
    c) Certifique-se de que você tenha configurado "ID do projeto" no cliente que é uma entidade inteiramente numérica que você pode obter no painel:
    
    ![][1]

##<a name="application-issues"></a>Problemas de aplicativo

1) **Marcas / marca expressões**

Se você estiver usando expressões de marca ou marcas para sua audiência de segmento, sempre é possível que, quando você está enviando a notificação, não há nenhum destino foi encontrado com base em expressões marcas/marca que estiver especificando em sua chamada de envio. É melhor analisar seus registros para garantir que há marcas que correspondem ao enviar notificação e, em seguida, verifique se o recebimento de notificação somente a partir de clientes com esses registros. Por exemplo Se todos os seus registros com NH terminaram com dizem marca "Política" e você estiver enviando uma notificação com marca "Esportes", ele não será enviado a qualquer dispositivo. Um caso complexo pode envolver expressões de marca onde você apenas registrado com "Marca A" ou "Marca B", mas ao enviar notificações, você está destinando "Marca A & & marca B". Na seção auto diagnosticar dicas abaixo, existem maneiras nas quais você pode examinar seus registros juntamente com as marcas que eles têm. 

2) **Problemas de modelo**

Se você estiver usando modelos, certifique-se de que você está seguindo as diretrizes descritas no [Guia do modelo]. 

3) **Registros inválidos**

Considerando que o Hub de notificação foi configurado corretamente e todas as expressões marcas/marca foram usadas corretamente resulta em localizar de destinos válidos para o qual as notificações precisam ser enviados, NH aciona desativar vários lotes de processamento em paralelo - cada lote enviando mensagens a um conjunto de registros. 

> [AZURE.NOTE] Como podemos fazer o processamento em paralelo, não garantimos a ordem na qual as notificações serão entregues. 

Agora o Hub de notificações do Azure é otimizada para um modelo de entrega de mensagem de "em mais uma vez". Isso significa que podemos tentar uma duplicação para que nenhuma notificações serão entregues mais de uma vez em um dispositivo. Para garantir que isso estamos examine os registros e certifique-se de que apenas uma mensagem é enviada por identificador de dispositivo antes de realmente enviar a mensagem para a PNS. Como cada lote é enviada para o PNS, que por sua vez é aceitar e Validando os registros, é possível que o PNS detecta um erro com um ou mais dos registros em um lote, retorna um erro ao Azure NH e interrompe o processamento, portanto, soltando desse lote completamente. Isso é especialmente verdadeiro com APNS que usa um protocolo de fluxo TCP. Embora nós são otimizados para a maioria depois de entrega, nesse caso, podemos remover o registro com falha de nosso banco de dados e tente novamente a entrega de notificação para o restante dos dispositivos em que lote.

Você pode obter informações de erro para a tentativa de falha na entrega contra um registro usando as APIs REST de Hubs de notificação de Azure: [por mensagem de telemetria: obter Telemetria da mensagem de notificação](https://msdn.microsoft.com/library/azure/mt608135.aspx) e [PNS comentários](https://msdn.microsoft.com/library/azure/mt705560.aspx). Consulte o [SendRESTExample](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample) por exemplo código.

##<a name="pns-issues"></a>Problemas PNS

Depois que a mensagem de notificação foi recebida pelos respectivos PNS é sua responsabilidade para fornecer a notificação no dispositivo. Azure Hubs de notificação estiver fora do aqui a imagem e não tem controle quando ou se a notificação vai ser entregue para o dispositivo. Como os serviços de notificação de plataforma são bastante robustos, notificações tendem a atingir os dispositivos em alguns segundos da PNS. Se o PNS Entretanto é a otimização Hubs de notificação do Azure aplique uma estratégia de retirada exponencial e se o PNS permanece inacessível por 30 min, em seguida, temos uma política in-loco para expirar e solte as mensagens permanentemente. 

Se um PNS tenta entregar uma notificação, mas o dispositivo está offline, a notificação é armazenada pela PNS para um período de tempo limitado e entregue ao dispositivo quando ela estiver disponível. Apenas uma notificação recente para um determinado aplicativo está armazenada. Se várias notificações são enviadas enquanto o dispositivo estiver offline, cada nova notificação causa a notificação prévia ser descartada. Esse comportamento de manter somente a notificação mais recente é conhecido como coalescimento notificações no APNS e recolhendo no GCM (que usa uma chave de recolhimento). Se o dispositivo permanece offline por um longo período, qualquer notificações que foram sendo armazenadas para ele serão descartadas. Fonte - [orientação APNS] & [orientações GCM]

Com Hubs de notificação do Azure - você pode passar uma chave unindo por meio de um cabeçalho HTTP usando o genérico `SendNotification` API (por exemplo, para .NET SDK – `SendNotificationAsync`) que também usa cabeçalhos HTTP que são passados como é a respectiva PNS. 

##<a name="self-diagnose-tips"></a>Auto diagnosticar dicas

Aqui, examinaremos os vários caminhos para diagnosticar e raiz causam problemas Hub de notificação:

###<a name="verify-credentials"></a>Verificar credenciais

1. **Portal do desenvolvedor PNS**

    Verificá-los no respectivo PNS portal do desenvolvedor do (APNS, GCM, WNS etc) usando nossos [Obtendo tutoriais de Introdução].

2. **Portal do Azure clássico**

    Vá para a guia de configurar para revisar e correspondam aos obtida do portal do desenvolvedor do PNS as credenciais. 

    ![][4]

###<a name="verify-registrations"></a>Verificar registros

1. **O Visual Studio**

    Se você usa o Visual Studio para desenvolvimento, em seguida, você pode se conectar ao Microsoft Azure e exibir e gerenciar um monte de serviços Azure incluindo Hub de notificações do "Server Explorer". Isto é especialmente útil para seu ambiente de desenvolvimento/teste. 

    ![][9]

    Você pode exibir e gerenciar todos os registros no seu hub categorizados perfeitamente para plataforma, nativa ou registro de modelo, quaisquer marcas, PNS identificador, identificação de registro e a data de expiração. Você também pode editar um registro instantâneos - que é útil dizer que se você quiser editar quaisquer marcas. 

    ![][8]
 
    > [AZURE.NOTE] A funcionalidade do Visual Studio para editar registros só deve ser usada durante o desenvolvimento/teste com um número limitado de registros. Se houver surgir uma necessidade para corrigir seus registros em massa, considere a possibilidade de usar a funcionalidade de registro de importação/exportação descrito aqui - [Registros de importação/exportação] (https://msdn.microsoft.com/library/dn790624.aspx)

2. **Explorador de barramento de serviço**

    Muitos clientes utilizam ServiceBus explorer descrito aqui - [ServiceBus Explorer] para exibir e gerenciar seu hub de notificação. Ele é um projeto de código-fonte aberto disponível code.microsoft.com - [código ServiceBus Explorer]

###<a name="verify-message-notifications"></a>Verifique se as notificações de mensagem

1. **Azure Portal clássico**

    Você pode ir para a guia "Depuração" enviar notificações de teste para seus clientes sem precisar de qualquer back-end do serviço para cima e em execução. 

    ![][7]

2. **O Visual Studio**

    Você também pode enviar notificações de teste dos comforts do Visual Studio:

    ![][10]

    Você pode ler mais sobre a funcionalidade do explorer Visual Studio notificação Hub Azure aqui - 
    
    - [Visão geral do VS Server Explorer]
    - [Postagem de Blog do Explorador de servidor de VS - 1]
    - [Postagem de Blog do Explorador de servidor de VS - 2]

###<a name="debug-failed-notifications-review-notification-outcome"></a>Notificações de falha de depuração / revisar o resultado de notificação

**Propriedade EnableTestSend**

Quando você envia uma notificação por meio de Hubs de notificação, inicialmente ele apenas obtém enfileirado para NH fazer processamento para descobrir todos os seus destinos e eventualmente NH envia para o PNS. Isso significa que quando você estiver usando API REST ou qualquer um do SDK do cliente, o retorno bem-sucedido da chamada enviar apenas significa que a mensagem tenha sido com êxito enfileirada com Hub de notificação. Ele não fornece percepção o que aconteceu quando NH eventualmente adquiriu enviar a mensagem para PNS. Se sua notificação não está chegando ao dispositivo do cliente, há uma possibilidade de que, quando NH tentou entregar a mensagem para PNS, ocorreu um erro, por exemplo, o tamanho de carga excedeu o máximo permitido pela PNS ou as credenciais configuradas no NH são inválido etc. Para obter uma visão geral dos erros PNS, apresentamos uma propriedade chamada [EnableTestSend recurso]. Essa propriedade é ativada automaticamente quando você envia mensagens de teste do portal ou cliente Visual Studio e, portanto, permite que você veja informações detalhadas de depuração. Você pode usá-lo por meio de APIs adotando o exemplo do SDK do .NET onde ele estiver disponível no momento e será adicionado a todos os SDKs de cliente eventualmente. Para usar esta com a chamada REST, basta acrescentar um parâmetro da sequência de consulta chamado "test" no final da sua chamada de envio por exemplo 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*Exemplo (.NET SDK)*
 
Suponha que você estiver usando o SDK do .NET para enviar uma notificação do sistema nativo:

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
 
`result.State`será simplesmente estado `Enqueued` no final da execução sem qualquer entender o que aconteceu com o envio. Agora você pode usar o `EnableTestSend` propriedade booleana ao inicializar o `NotificationHubClient` e pode obter status detalhadas sobre os erros PNS encontrou ao enviar a notificação. A chamada de envio aqui levará mais tempo para retornar porque ele está retornando somente após NH ter entregue a notificação para PNS para determinar o resultado. 
 
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);
    
    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);
    
    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }

*Exemplo de saída*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong
 
Esta mensagem indica um dos credenciais inválidas são configuradas no hub do notificação ou um problema com os registros no hub e o curso recomendado seria excluir esse registro e deixar que o cliente recriá-lo antes de enviar a mensagem. 
 
> [AZURE.NOTE] Observe que o uso dessa propriedade muito é acelerado e portanto você deve somente usar a isso no ambiente de desenvolvimento/teste com conjunto limitado de registros. Nós somente enviar notificações de depuração para 10 dispositivos. Também temos um limite de processamento envia depuração 10 por minuto. 

###<a name="review-telemetry"></a>Revisar Telemetria 

1. **Usar o Azure Portal clássico**

    O portal permite que você obtenha uma visão geral de todas as atividades no seu Hub de notificação. 
    
    a) a partir da guia "painel", você pode exibir um modo de exibição agregado da registros, notificações, bem como erros por plataforma. 
    
    ![][5]
    
    b) você também pode adicionar muitas outras métricas específicas de plataforma da guia "Monitor" para fazer uma análise mais profunda particularmente erros específicos PNS retornados quando NH tenta enviar a notificação para o PNS. 
    
    ![][6]
    
    c) você deve começar com revisando as **Mensagens de entrada**, **Operações de registro**, **Notificações bem-sucedida** e acesse por tabulação de plataforma para analisar os erros específicos PNS. 
    
    d) se você tiver o hub de notificação configurado incorretamente com as configurações de autenticação e em seguida, você verá o erro de autenticação de PNS. Esta é uma boa indicação para verificar as credenciais PNS. 

2) **Acesso através de programação**

Mais detalhes aqui- 

- [Acesso de programação de telemetria]
- [Acesso de telemetria via amostra APIs] 

> [AZURE.NOTE] Telemetria vários recursos relacionados à como **Registros de importação/exportação**, **Acesso de telemetria via APIs** etc estão disponíveis apenas no nível padrão. Se você tentar usar esses recursos se você estiver no nível básico ou livre, em seguida, você obterá mensagem de exceção para esse efeito enquanto você usa o SDK e um HTTP 403 (proibido) ao usá-los diretamente das APIs REST. Certifique-se de que você moveu até padrão nível via Portal clássico do Azure.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png
 
<!-- LINKS -->
[Visão geral de Hubs de notificação]: notification-hubs-push-notification-overview.md
[Tutoriais de Introdução]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Diretrizes de modelo]: https://msdn.microsoft.com/library/dn530748.aspx 
[Orientação de APNS]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[Orientação de GCM]: http://developer.android.com/google/gcm/adv.html
[Os registros de importação/exportação]: http://msdn.microsoft.com/library/dn790624.aspx
[ServiceBus Explorer]: http://msdn.microsoft.com/library/dn530751.aspx
[Código do ServiceBus Explorer]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Visão geral do VS Server Explorer]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Postagem de Blog do Explorador de servidor de VS - 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[Postagem de Blog do Explorador de servidor de VS - 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[Recurso de EnableTestSend]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[Acesso de programação de telemetria]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Acesso de telemetria via amostra APIs]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

 