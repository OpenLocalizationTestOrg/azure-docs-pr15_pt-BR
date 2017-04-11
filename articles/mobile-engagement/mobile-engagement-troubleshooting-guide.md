<properties 
   pageTitle="Guias de solução de problemas do Azure contrato móvel" 
   description="Guia de solução de problemas do Azure contrato móvel" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="azure-mobile-engagement---troubleshooting-guide"></a>Azure contrato móvel - guia de solução de problemas

## <a name="introduction"></a>Introdução
O guia de solução de problemas a seguir o ajudará a compreender causas de alguns problemas comuns e será permitem solucionar sozinho. 

## <a name="general"></a>Geral

Em geral, você sempre deve garantir o seguinte:

1. Certifique-se de que você passaram por todas as etapas necessárias para integração conforme descrito em nossos [tutoriais de Introdução](mobile-engagement-windows-store-dotnet-get-started.md)
2. Você está usando a versão mais recente da plataforma SDKs. 
3. Teste em um dispositivo real e um emulador porque alguns dos problemas são específicas ao emulador somente. 
4. Você não é atingir qualquer limites/limitações de contrato Mobile que estão documentadas [aqui](../azure-subscription-service-limits.md)
5. Se você não conseguir se conectar ao serviço móvel contrato back-end ou vendo dados não está sendo carregados continuamente, em seguida, certifique-se de que não há nenhum incidente de serviço contínuo marcando [aqui](https://azure.microsoft.com/status/)

## <a name="monitor-issues"></a>Problemas com o 'Monitor'

### <a name="i-am-not-seeing-my-device-showing-up-on-the-monitor-tab"></a>Não estou vendo meu dispositivo aparecendo na guia Monitor
Guia Monitor mostra os dispositivos conectados à sua plataforma de contrato Mobile em tempo real. Se você depurar em um emulador e o dispositivo, você deve ver pelo menos uma sessão aqui. Se o aplicativo tiver sido distribuído, em seguida, você verá o medidor de sessões ativas refletir os dispositivos que estão conectados à plataforma em tempo real. 

Se você não está vendo seu dispositivo na guia Monitor é provavelmente um problema de integração de SDK. Algumas etapas comuns para solucionar problemas são da seguinte maneira:

1. Certifique-se de que você estiver usando a cadeia de conexão correto no aplicativo móvel e é a seção de chaves SDK e não a seção de chaves de API. A cadeia de conexão se conecta a seu aplicativo móvel na instância do aplicativo móvel contrato no qual você verá o seu dispositivo na guia Monitor. 
2. Para plataforma Windows - se a sua página substitui o `OnNavigatedTo` método, verifique se você chamar `base.OnNavigatedTo(e)`.
3. Se você precisa integrar o contrato de celular a um aplicativo móvel existente e em seguida, você também pode garantir que você não está faltando quaisquer etapas examinando as etapas de integração avançada [aqui](mobile-engagement-windows-store-integrate-engagement.md)
4. Certifique-se de que você está enviando pelo menos um/atividade de tela, substituindo a página com EngagementActivity dependendo da plataforma que você está trabalhando conforme descrito nos [tutoriais de Introdução](mobile-engagement-windows-store-dotnet-get-started.md).

### <a name="i-am-seeing-the-monitor-tab-showing-a-session-even-when-i-have-disconnected-or-closed-my-app-emulator"></a>Estou vendo a guia Monitor mostrando uma sessão mesmo quando eu tenho desconectado ou fechado meu aplicativo / emulador. 
Se você for o único conectado à plataforma neste momento e você estiver usando um emulador para abrir o aplicativo, em seguida, isso provavelmente devido a quirks emulador. Em geral, é necessário garantir que você voltar à tela inicial do emulador para a sessão de aplicativo desconectar com êxito. Além disso, na plataforma Windows, durante a depuração com o Visual Studio, você talvez precise garantir no Visual Studio, vá para a barra de menus do **Ciclo de vida de eventos** e clique em **Suspender** realmente fechar a sessão. Consulte o [tutorial do Windows](mobile-engagement-windows-store-dotnet-get-started.md) para obter detalhes. 

## <a name="analytics-issues"></a>Problemas de 'Analytics'

### <a name="i-am-not-seeing-any-data-refreshed-data-on-analytics-tab"></a>Eu não estou vendo quaisquer dados / atualizados dados na guia análise 
Dados de análise são recalculados regularmente e pode levar até 24 horas para essa atualização. Estes dados não estiverem em tempo real e você verá que ele atualizado dentro esse período de 24 horas.
Certifique-se no entanto que você está enviando pelo menos uma tela ou atividade no back-end plataforma por qualquer substituição pelo menos uma página com `EngagementActivity` ou chamar `SendActivity` explcitly. 

### <a name="i-am-seeing-incorrectly-captured-datetime-for-a-device-on-the-analytics-tab"></a>Estou vendo incorretamente capturada data/hora para um dispositivo na guia análise
O período de tempo para análise baseia-se na data dos usuários configurações de dispositivo. Portanto, certifique-se de que o dispositivo tem a data definida corretamente. 

## <a name="segment-issues"></a>Problemas de 'Segmento'

### <a name="i-created-a-segment-and-it-is-showing-up-as-greyed-out-or-not-showing-any-data"></a>Eu criei um segmento e ele está aparecendo como esmaecido ou não mostrando todos os dados
Criação de segmento não está em tempo real no momento. Ela é calculada ao mesmo tempo, como os dados de análise são agregados e, portanto, pode levar até 24 horas. Você deve verificar novamente mais tarde, mas, enquanto isso, você também deve garantir que seus aplicativos móveis realmente estão enviando os dados com base na qual você está formando os segmentos. Por exemplo Se um evento disser 'foo' não está sendo enviado por qualquer dispositivo móvel e seria quaisquer dados de segmento para um segmento criado com EventName = foo como critério. Você também deve verificar a integração com seu SDK para garantir que seu aplicativo móvel está enviando os dados corretamente. 

## <a name="reach-or-push-notifications-issues"></a>Problemas de 'Atinja' ou notificações Push

### <a name="my-push-messages-are-not-being-delivered"></a>Minhas mensagens de envio não estão sendo entregues 

1. Tente enviar notificações para um dispositivo de teste primeiro para garantir que todos os componentes - aplicativo móvel, SDK e o serviço são conectadas corretamente e capaz de fornecer notificações por push. 
2. Sempre enviar a mais simples 'out-de-app notificação' primeiro por meio de uma campanha que não está agendada e nem tem qualquer critério de audiência especificado. Isso é novamente para provar que conectividade de notificação está funcionando corretamente. 
3. Se você estiver tendo problemas na entrega de notificações do aplicativo, em seguida, também é uma boa primeira etapa para experimentar o envio de uma notificação de out-de-app primeiro. 
4. Certifique-se de que a 'nativo Push' está configurado corretamente para o aplicativo móvel. Dependendo da plataforma-la ou envolve chaves (Android, Windows) ou certificados (iOS). Consulte a [Interface do usuário - configurações](mobile-engagement-user-interface-settings.md)
5. Sair do aplicativo notificações poderiam também ser bloqueadas pelo usuário através do sistema operacional móvel do então certifique-se de não for o caso. 
6. Certifique-se de que você não está definindo a opção de *Ignorar público, envio será enviado aos usuários por meio da API* na seção de **campanha** de uma campanha de alcance porque isso garante que as notificações por push só podem ser enviadas por meio de APIs. 
7. Certifique-se de que você está testando sua campanha de envio com os dois um dispositivo conectado via rede de operadora de telefone e WIFI para eliminar a conexão de rede como uma fonte de possível problemas.
8. Certifique-se de que a data/hora do sistema no seu dispositivo/emulador é correta porque qualquer dispositivo fora de sincronia também será interferir com a capacidade de enviar notificação do serviço para fornecer notificações. 

Plataforma mais específica de solução de problemas de instruções abaixo:

1. **iOS** 

    - Certifique-se de que os certificados são válidos e ainda para iOS notificações Push. 
    - Certifique-se de que você está configurando um certificado de *produção* corretamente em seu aplicativo de contrato de celular. 
    - Certifique-se de que você está testando em um *dispositivo real, físico.* O simulator iOS não é possível processar mensagens de envio.
    - Certifique-se de que o identificador de pacote está configurado corretamente no aplicativo móvel. Consulte as instruções [aqui](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)
    - Ao testar, use a distribuição "Ad Hoc" no seu perfil de provisionamento móvel. Não será capaz de receber uma notificação se seu aplicativo é compilado usando "Depurar"

2. **Android**

    - Certifique-se de que você especificou o número correto de projeto no arquivo de AndroidManifest.xml do seu aplicativo móvel que é seguido pelo caractere \n. 
    
            <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
        
    - Certifique-se de que você não está ausentes ou configurado incorretamente todas as permissões no arquivo manifesto Android 
    - Certifique-se de que o número do projeto que você está adicionando ao seu aplicativo cliente é da mesma conta onde você adquiriu a chave do servidor GCM. Quaisquer incompatibilidades entre os dois impedirá a coloca saindo. 
    - Se você estiver recebendo as notificações do sistema, mas não do aplicativo e em seguida, revisão [especificar um ícone para a seção notificações](mobile-engagement-android-get-started.md) como provavelmente você não está especificando o ícone correto no arquivo manifesto Android. 
    - Se você estiver enviando uma notificação de BigPicture, em seguida, certifique-se de que se você tiver servidores de imagem externa e em seguida, eles precisam ser capazes de suporte HTTP "Obter" e "Cabeça".

3. **Windows**
    
    - Certifique-se de que você associou o aplicativo de um aplicativo da Windows Store válido. No Visual Studio - você terá clique com botão direito do projeto e selecione a opção de "Associar o aplicativo com o Store" e selecione o aplicativo que você criou na Windows Store. Este aplicativo da Windows Store deve ser a mesma de onde você obteve as credenciais de envio nativo configurar no portal do contrato de celular.
    - Se você estiver recebendo notificações por push de fora do aplicativo, mas não no-notificações do aplicativo com `EngagementOverlay` integração, em seguida, certifique-se há um elemento de grade raiz na sua página. EngagementOverlay usa o primeiro elemento de "Grade" encontrados no seu arquivo xaml para adicionar web dois modos de exibição na sua página. Se você quiser localizar onde modos de exibição web serão definidos, você pode definir uma grade denominada "EngagementGrid" como isso Entretanto você terá que garantir que há suficiente altura e largura para a web subsequentes dois modos de exibição que mostrarão a notificação e o seguinte anúncio como notificação do aplicativo:
        
            <Grid x:Name="EngagementGrid"></Grid>

### <a name="i-created-a-push-notificationannouncement-campaign-and-even-after-it-sent-me-the-notification-it-is-showing-as-active-what-does-it-mean"></a>Criei um notificação/anúncio de envio/campanha e mesmo após ela me enviada a notificação, ele está mostrando como 'Active'. O que significa? 
A **campanha** que você criou no contrato Mobile é chamado então porque é um significado de notificação por push demorada que novos dispositivos se conecte à sua plataforma de contrato móvel, elas serão automaticamente enviadas a notificação que você configura aqui, desde que elas satisfaçam o critério que você definir na campanha. Isso não é uma única notificação captura a instalação. Você precisará manualmente, clique no botão **Concluir** para encerrar a campanha para que ele não enviar ainda mais notificações. 

### <a name="i-created-a-push-campaign-and-i-am-receiving-notifications-successfully-however-whenever-i-open-up-the-app-i-get-the-same-notification-even-when-i-had-actioned-it-before"></a>Criei uma campanha de envio e estou recebendo notificações com êxito no entanto sempre que abrir o aplicativo, recebo a mesma notificação mesmo quando eu tinha actioned antes? 
É provável que isso aconteça durante o teste e se você estiver usando emuladores ou alguns teste estrutura como TestFlight. O que está acontecendo aqui é que em cada aplicativo executar instância, ele ao adquirir um DeviceID novos e enviá-lo à nossa back-end que está causando a plataforma Mobile contrato a tratará como um novo dispositivo e enviando a notificação. 

## <a name="getting-support"></a>Obtendo suporte

Se você não consegue resolver o problema por conta própria, em seguida, você pode:

1. Pesquise o problema nos segmentos existentes no Fórum de StackOverflow e [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/windows/en-US/home?forum=azuremobileengagement) e se não, em seguida, faça uma pergunta lá. 
2. Se você encontrar um recurso ausente, em seguida, adicionar/voto para a solicitação no nosso [Fórum UserVoice](https://feedback.azure.com/forums/285737-mobile-engagement/)
3. Se você tiver aberto de suporte da Microsoft um incidente de suporte, fornecendo os seguintes detalhes: 
    - ID da assinatura Azure
    - Plataforma (por exemplo, iOS, Android etc)
    - ID de aplicativo
    - ID da campanha (para problemas de notificação de envio)
    - ID do dispositivo
    - Versão do Mobile contrato SDK (por exemplo, Android SDK anterior à versão 2.1.0)
    - Detalhes do erro com mensagem de erro exata e cenário
