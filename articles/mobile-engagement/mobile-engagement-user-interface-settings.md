<properties 
   pageTitle="Interface de usuário do Azure contrato móvel - configurações" 
   description="Saiba como gerenciar as configurações globais do seu aplicativo usando o contrato de celular do Azure" 
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

# <a name="how-to-manage-the-global-settings-of-your-application"></a>Como gerenciar as configurações globais do seu aplicativo

As opções do menu **configurações** disponíveis para variar um aplicativo, dependendo da plataforma do aplicativo e as permissões que você possui a concessão para o aplicativo. As configurações incluem o seguinte: detalhes, projetos, Push nativo, velocidade de envio por Push, marca (informações de aplicativo) e pressão comercial. A opção de menu marca (informações de aplicativo) da seção configurações pode ser gerenciada pelo seu aplicativo (usando o SDK) ou por seu back-end (usando a API do dispositivo). 


>[AZURE.NOTE] Muitas seções do portal do **Mobile contrato** UI contêm o botão **Mostrar Ajuda** . Pressione este botão para obter mais informações contextuais sobre uma seção.

## <a name="details"></a>Detalhes

Permite que você altere o nome e descrição do seu aplicativo, exiba o proprietário do seu aplicativo e suas permissões de função. 

Configuração de análise permite exibir ou alterar o dia de início do semanas e o tempo de retenção em dias.
 
  ![settings1][46]
 
## <a name="projects"></a>Projetos

Permite que você selecione todos os projetos que deseja que apareça em seu aplicativo. 

Você também pode procurar um projeto e exibir o nome, descrição, proprietário e suas permissões de função de qualquer projeto de de que seu aplicativo faz parte.

Para obter mais informações, consulte: [Documentação de interface do usuário – Home][Link 13]
 
  ![settings3][48]

## <a name="native-push"></a>Envio nativo

Permite que você registrar um novo certificado ou excluir e um certificado existente para uso com push nativo. Envio nativo permite Azure Mobile contrato por push para seu aplicativo a qualquer momento, mesmo quando não estiver funcionando. 

Após fornecer credenciais ou certificados para pelo menos um serviço de envio por Push nativo, você pode selecionar "Qualquer hora" ao criar campanhas alcançar e também usa o parâmetro "notificação" na API do PUSH.



### <a name="apple-push-notification-service-apns"></a>Serviço de notificações de envio do Apple (APNS)

Para habilitar Push nativa usando o serviço de notificação por Push da Apple, você precisará registrar seu certificado. Você precisará especificar o tipo de certificado como desenvolvimento (DES) ou produção (produção). Em seguida, você será necessário carregar seu certificado e a senha.

Para obter mais informações, consulte: [documentação SDK - iOS - como preparar seu aplicativo notificações por Push da Apple][Link 5]
 
![settings4][49]
 
### <a name="windows-push-notification-service-wpns"></a>Serviço de notificações de envio do Windows (WPNS)

Para habilitar Push nativa usando o serviço de notificação do Windows, você deve fornecer credenciais do seu aplicativo. Você precisará seu pacote SID (security identifier) e a chave secreta.
 
![settings5][50]
 
### <a name="google-cloud-messaging-for-android-gcm"></a>Nuvem do Google de mensagens para Android (GCM)

Para habilitar Push nativo usando GCM, você precisa siga as instruções do Google. Em seguida, você deve colar uma chave de API simple de servidor, configurado sem restrições de IP. Requer a integração com o SDK para Android v1.12.0 +.

Para obter mais informações, consulte: 

- [SDK documentação Android como integrar GCM][Link 5]
- [Guia GCM de desenvolvedor do Google](http://developer.android.com/guide/google/gcm/gs.html)
 
### <a name="amazon-device-messaging-for-android-adm"></a>Dispositivo Amazon mensagens para Android (ADM)

Para habilitar Push nativo usando ADM, você deve fornecer Amazon <OAuth credentials> consiste em uma identificação de cliente e o segredo do cliente (exige a integração com o SDK do Android anterior à versão 2.1.0 +).

Para obter mais informações, consulte: 

- [SDK documentação Android como integrar ADM][Link 5]
- [Documentação do Amazon desenvolvedor ADM](https://developer.amazon.com/sdk/adm/credentials.html#Getting)
 
![settings6][51]

## <a name="push-speed"></a>Velocidade de envio

Mostra a velocidade de envio atual do seu aplicativo e permite que você defina a velocidade de envio do seu aplicativo.
 
  ![settings7][52]

## <a name="tag-app-info"></a>Marca (informações de aplicativo)

![settings11][56]
  
## <a name="commercial-pressure"></a>Pressão comercial


![settings12][57]


## <a name="see-also"></a>Consulte também

- [Conceitos][Link 6]
- [Serviço guia de solução de problemas][Link 24]

 

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

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
 
