<properties 
   pageTitle="Interface de usuário do Azure contrato móvel - minha conta" 
   description="Saiba como gerenciar seus dispositivos de perfil e teste de conta usando o contrato de celular do Azure" 
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

# <a name="how-to-manage-your-account-profile-and-test-devices"></a>Como gerenciar seus dispositivos de perfil e teste de conta
 
Este artigo descreve a página **principal** do portal do **Contrato de celular** . Você usa o portal de **Contrato Mobile** para monitorar e gerenciar seus aplicativos móveis. 
 
Para acessar a página **Minha conta** , clique na sua conta no topo da página.

A seção Minha conta da interface do usuário é onde você pode exibir e alterar as configurações associadas com sua conta, inclusive suas configurações de perfil e testar identificações de dispositivo. Essas configurações contêm itens que também podem ser acessados por meio da API do dispositivo.

![MyAccount1][7]  

## <a name="profile"></a>Perfil:
Você pode exibir ou alterar qualquer uma das suas configurações de conta mostradas abaixo. Você também pode dar outra permissão de usuário para usar o aplicativo com base em seus endereços de email na [página inicial](mobile-engagement-user-interface-home.md).

![MyAccount2][8]  

## <a name="devices"></a>Dispositivos:
Você pode exibir, adicionar ou remover testar IDs de dispositivo os dispositivos de teste que você pode usar para testar suas campanhas **alcançar** ou **push** . Contextual instruções sobre como localizar a identificação de dispositivo de dispositivos para cada plataforma (iOS, Android, Windows Phone, etc.) são exibidos quando você clica em "Novo dispositivo". 
 
![MyAccount3][9]  
 
Para usar a API do Push ou API do dispositivo que você precisa saber o identificador de dispositivo exclusivo de seus usuários (o parâmetro deviceid). Há várias maneiras de recuperá-la:
 
1. Do seu back-end, você pode usar o recurso de "Get" da API do dispositivo para obter a lista completa de identificadores de dispositivos.
2. De seu aplicativo, você pode usar o SDK para colocá-lo. (No Android, chamar a função getDeviceID() da classe agente e IOS, leia a propriedade deviceid da classe agente.)
3. De um anúncio de alcance, se a URL da ação associada ao comunicado contém o padrão de {deviceid}, ele será automaticamente substituído pelo identificador do dispositivo aciona a ação.
http://<example>.com/registeruser?deviceid = {deviceid} & otherparam = myparamdata serão substituídas por: http://<example>.com/registeruser?deviceid = XXXXXXXXXXXXXXXX & otherparam = myparamdata 
4. De um anúncio de web alcance, se o código HTML do lançamento contém o padrão de {deviceid}, ele será automaticamente substituído pelo identificador do dispositivo exibindo o lançamento da web.
Aqui está minha identificador de dispositivo: {deviceid} será substituída por: aqui está minha identificador de dispositivo: XXXXXXXXXXXXXXXX
5.  Abra seu aplicativo em seu dispositivo e execute um evento em seu aplicativo que foram marcado.
De "UI - seus eventos de aplicativo - Monitor - - detalhes", encontre o evento que você realizou na lista.
Clique em para este evento no Monitor.
Você deve encontrar sua ID de dispositivo na lista de dispositivos que executou este evento.
Em seguida, você pode copiar esta ID de dispositivo e registrá-lo no "Dispositivos de interface do usuário - minha conta - - novo dispositivo - Select sua plataforma de dispositivo".
>(Estar ciente de que quando IDFA está desabilitada para iOS, a ID do dispositivo podem alterar ao longo do tempo se você desinstalar e reinstalar o seu aplicativo.)

##<a name="troubleshooting-guide"></a>Guia de solução de problemas
-  [Guia solução de problemas - serviço][Link 24]

## <a name="see-also"></a>Consulte também
-  [Documentação de interface do usuário - página inicial][Link 13]


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


 
 
