<properties 
   pageTitle="Interface de usuário do Azure contrato móvel - alcance como"
   description="Visão geral da Interface de usuário para Azure contrato móvel" 
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

# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>Como começar a usar e gerenciar envia para chegar aos seus usuários finais

Depois que o SDK é totalmente integrado ao seu aplicativo, você pode começar a usar a seção alcance da interface do usuário para as notificações por Push para os usuários do aplicativo.  

## <a name="do-your-first-push-notification-campaign"></a>Faça sua primeira campanha de notificação de envio
-    Confirme que seu alcance é integrado ao seu aplicativo com o SDK. 
-    Selecione seu aplicativo
 
![First1][1]

-    Vá para a seção "Alcance" e clique em "novo aviso"
 
![First2][2]

-    Criar uma nova campanha e chame-a
 
 ![First3][3]

-    Selecione como a notificação deve ser entregue, como do aplicativo somente
 
![First4][4]

-    Crie a mensagem que você deseja push
 
![First5][5]

-    Você pode escrever um título a notificação (opcional).
-    Escreva o conteúdo da mensagem de envio.
-    Você pode carregar uma imagem. Lembre-se de que o tamanho do arquivo não pode exceder 32.768 bytes.
-    Você também tem a capacidade de selecionar mais opções, mas para o foco deste tutorial, veremos que posteriormente.

-    Selecione o tipo de conteúdo como notificação somente
 
![First6][6]

-    Criar sua campanha de envio e ela será exibida na sua lista de campanha.
 
![First7][7]

## <a name="test-your-push-notification-campaign"></a>Testar sua campanha de notificação de envio
![Test1][8]

-    Registre seu dispositivo.
-    Clique na caixa de seleção do dispositivo que você deseja enviar.
-    Clique no botão "Testar" para enviar o envio para o dispositivo.
 
![Test2][9]

-    Ativar a campanha
 
![Realizado no mercado3][10]

-    Agora que você criou sua campanha basta para ativá-lo para a notificação ser enviada para os seus usuários.
 
## <a name="send-personalized-pushes"></a>Enviar coloca personalizadas
-    Este exemplo cria um envio onde um código de desconto personalizado é inserido na notificação de envio.
 
![Personalize1][11]

Personalização works substituindo um marcador por uma marca de informações do aplicativo, portanto, você terá que garantir que o usuário tenha o aplicativo-info PRI definido pela primeira vez. Neste exemplo, os usuários de destino terá uma marca de informações de aplicativo chamada rebate_code definido.
Como você pode ver acima o conteúdo de notificação de envio inclui o $ de marcador {rebate_code} que indicará que ele a ser substituído pelo conteúdo real da marca de informações do aplicativo.

> Aviso: Se a marca de informações de aplicativo não está definida para o usuário, o usuário não receberá o envio.

-    Resultado
 
![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>Você pode personalizar ainda mais o texto sua notificação
![Personalize3][13]

-    Incluindo o título da notificação,
-    E o conteúdo da mensagem.
-    Escolha o tipo de anúncio (exibição de texto ou da Web)
 
![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>Corpo de um comunicado também pode ser personalizado com:
-    A URL da ação, você deseja personalizar a página inicial
-    O título,
-    O corpo da mensagem.
 
 
## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>Diferenciar sua notificação de envio por Push (ou reduzir aplicativo)
-    Escolha o tipo de notificação você serão enviados por push, selecione seu aplicativo, vá para a seção "Atinja", selecione ou criar uma campanha de envio e vá para a seção "Notificação".
 
-    Clique em "modo de entrega" desejado.
-    Clique na caixa de seleção "Restringir atividades" quando quiser que a notificação ocorre em atividades específicas (telas).

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>Modo de entrega de "Ausência somente o aplicativo"
![Differentiate2][16]

"Ausência somente o aplicativo" modo de entrega fornece uma notificação de envio quando o aplicativo é fechado. Esta é a notificação de envio padrão.
Quando você seleciona "sair do aplicativo", você já deve ter fornecido os certificados da plataforma que seu aplicativo está criando no (APNS ou GCM).

### <a name="see-also"></a>Consulte também
-  [Serviço de notificação – certificados Push da Apple](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), nuvem do Google Messaging – certificado] (http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>"do aplicativo somente" modo de entrega
![Differentiate3][17]

Modo de entrega de "Do aplicativo somente" fornece notificação de envio quando o aplicativo está em execução.
Para essa notificação, você não precisa passam pelo sistema APNS e GCM.
Você pode usar o sistema de entrega do aplicativo alcançar seus usuários finais.
Totalmente, você pode personalizar a notificação e decidir em que atividade (tela) a notificação será exibida.

### <a name="anytime-delivery-mode"></a>Modo de entrega "A qualquer momento"
Você pode escolher um modo de entrega "A qualquer momento", garante para acessar seu usuário final se o aplicativo está executando ou não.
Quando você seleciona "A qualquer momento", você já deve ter fornecido os certificados da plataforma do seu aplicativo é baseando (APNS ou GCM). 
 
## <a name="schedule-a-push-campaign"></a>Agendar uma campanha de envio
### <a name="plan-to-start-a-campaign"></a>Planejar iniciar uma campanha
![Shedule1][18]

É o 21 de março e você tiver um comunicado para fazer e planejado para o 22 de março à meia-noite. Você não precisa ficar na frente a interface para realizar um estudo! Você pode planejar antecipadamente o exato minuto notificações serão enviadas.
-    Desmarque "Nenhum" caixa de seleção e selecione uma hora de início 
-    Escolha a data e a hora em que você deseja iniciar a campanha de envio.

### <a name="plan-to-end-a-campaign"></a>Plano para encerrar uma campanha
![Shedule2][19]

Você quer sua campanha para parar no 25º de março em 3.00 pm, mas você sabe que não será lá fazê-lo.
Você não precisa ficar na frente da interface para envio por push! Você pode planejar antecipadamente o exato minuto que sua campanha será interrompida.
-    Clique em "Nenhum" caixa de seleção ou selecione uma hora de término
-    Escolha a data e a hora em que deseja terminar a campanha de envio.

### <a name="end-a-campaign-manually"></a>Encerrar uma campanha manualmente
![Shedule3][20]

Por padrão, "Nenhum" caixas de seleção são selecionadas.
Isso significa que a campanha será iniciado assim que você ativá-lo na seção alcance e terminarão quando você interromperá na seção alcance.
 
> Observação: Campanhas criadas sem uma data de término armazenam o envio localmente no dispositivo e mostrá-la na próxima vez que o aplicativo é aberto, mesmo se a campanha manualmente é encerrada.

## <a name="enhance-a-push-notification-with-a-text-view"></a>Aprimorar uma notificação de envio com um modo de exibição de texto
### <a name="what-is-a-text-view"></a>O que é um modo de exibição de texto?
![TextView1][21]

Um modo de exibição de texto é um pop-up com o conteúdo de texto. Este pop-up aparecerá depois que o usuário final clicou a notificação de envio.
Um modo de exibição de texto permite que você apresente mais conteúdo ao usuário final. Isso também é a oportunidade de apresentar um plano de ação como saltar para uma página de seu aplicativo, redirecionando para uma loja, abrindo uma página da web, enviar um email, iniciar uma pesquisa de localização geográfica-localizada, etc...

### <a name="example-text-view"></a>Exemplo: Modo de exibição de texto
-    Criar sua campanha de notificação por Push na seção "Alcance" e dê um nome para sua campanha
 
![TextView2][22]

-    Escreva a mensagem que aparecerá na notificação.
-    Selecione o tipo de conteúdo de anúncio de "texto"
 
![TextView3][23]

> Observação: quando você pressionar um modo de exibição de texto, ele sempre vem com uma notificação primeiro. 

- Defina o texto (depois de ter selecionado o conteúdo de lançamento do texto, subseção será exibida, permitindo que você defina o texto que você deseja exibir.)
 
![TextView4][24]

-    Escreva o título que aparecerá na parte superior da mensagem.
-    Escreva o conteúdo principal do modo de exibição de texto.
-    Escreva o conteúdo que será exibido no botão de ação (um botão de ação permite que o aplicativo tornar uma ação específica como abrir uma página do aplicativo, redirecionando para uma loja de aplicativos ou qualquer tipo de fontes que você pode fornecer).
-    Escreva o conteúdo que aparecerá no botão Sair (clicando no botão Sair, o modo de exibição de texto desaparecerá.)
 
-    Criar sua campanha de notificação por push e ela será exibida na lista de campanha.
 
![TextView5][25]

-    Ative sua campanha de notificação de envio para enviar o modo de exibição de texto para seus usuários.
 
![TextView6][26]

-    Resultado
 
![TextView7][27]

-    O usuário recebe a notificação e clique nela.
-    O modo de exibição de texto é exibido como um pop-up permitindo que o usuário interagir com ele.

## <a name="enhance-a-push-notification-with-a-web-view"></a>Aprimorar uma notificação de envio com um modo de exibição da Web
### <a name="what-is-a-web-view"></a>O que é um modo de exibição da Web?
![WebView1][28]

Um modo de exibição de web é um pop-up com conteúdo da web. Este pop-up aparece quando o usuário final clicou a notificação de envio.
Um modo de exibição da web permite que você tenha mais de interação com o usuário final.
Isso também é a oportunidade de apresentar um plano de ação como o redirecionamento para App Store, abrindo uma página da web, enviar um email, iniciar uma pesquisa de localização geográfica-localizada, etc...

### <a name="example-web-view"></a>Exemplo: Modo de exibição de Web
-    Crie sua campanha de Push na seção "Alcance" e dê um nome para sua campanha.
 
![WebView2][29]

-    Escreva a mensagem que aparecerá na notificação.
-    Selecione o tipo de conteúdo de anúncio como "web"
 
![WebView3][30]

### <a name="about-announcement-types"></a>Sobre tipos de lançamento:
- Notificação somente: é uma notificação padrão simple. O que significa que se um usuário clica nele, nenhum modo de exibição adicional será exibido, mas somente a ação associada a ele vai ocorrer.
- Comunicado de texto: é uma notificação de que o usuário tenha uma olhada em um modo de exibição de texto é ativado.
- Comunicado de Web: é uma notificação de que o usuário tenha uma olhada em um modo de exibição de web é ativado.
Selecione o conteúdo de "Comunicado Web".

> Observação: Quando você pressionar um modo de exibição da web, ele sempre vem com uma notificação primeiro.

- Definir o conteúdo da web (depois de ter selecionado o conteúdo de lançamento da web, a subseção será exibida, permitindo que você defina o conteúdo de exibição da web que você deseja exibir.)

 
![WebView4][31]

-    Escreva o título que aparecerá na parte superior da mensagem (opcional).
-    Escreva o código HTML aqui.
-    Clique na fonte de edição de botão de modo para alternar edition e ver como ele é exibido.
-    Escreva o conteúdo que será exibido no botão de ação (um botão de ação permite que o aplicativo tornar uma ação específica como abrir uma página do aplicativo, redirecionando para uma loja ou qualquer tipo de fontes que você pode fornecer).
-    Escreva o conteúdo que aparecerá no botão Sair (clicando no botão Sair, o modo de exibição de web desaparecerá).
 
-    Resultado
 
![WebView5][32]

-    O usuário recebe a notificação e clique nela.
-    O modo de exibição de texto é exibido como um pop-up permitindo que o usuário interagir com ele.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
 
