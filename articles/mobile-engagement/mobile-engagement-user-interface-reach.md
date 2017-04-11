<properties 
   pageTitle="Interface de usuário do Azure contrato móvel - alcance" 
   description="Saiba como comunique-se aos usuários do seu aplicativo com notificações por push usando o contrato de celular do Azure" 
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


# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>Como comunique-se aos usuários do seu aplicativo com notificações por push

Este artigo descreve a guia **ATINJA** do portal do **Contrato de celular** . Você usa o portal de **Contrato Mobile** para monitorar e gerenciar seus aplicativos móveis. Observe que para começar a usar o portal do primeiro é necessário criar uma conta do **Azure Mobile contrato** . Para obter mais informações, consulte [criar uma conta do Azure Mobile contrato](mobile-engagement-create.md).

A seção de alcance da interface do usuário é a ferramenta de gerenciamento de campanha Push onde você pode criar/editar/ativar/término/monitor e obter estatísticas sobre campanhas de notificação de envio e recursos que também podem ser acessados através da API atinja (e alguns elementos de baixo nível API do Push). Lembre-se de que, se você estiver usando as APIs ou a interface do usuário, você precisará integrar Azure Mobile contrato e alcance em seu aplicativo para cada plataforma com o SDK antes de poder usar atinja campanhas.

>[AZURE.NOTE] Muitas seções do portal do **Mobile contrato** UI contêm o botão **Mostrar Ajuda** . Pressione este botão para obter mais informações contextuais sobre uma seção.

## <a name="four-types-of-push-notifications"></a>Quatro tipos de notificações de envio
1.    Anúncios - permitem que você enviar mensagens de anúncios para usuários que redirecioná-las para outro local dentro de seu aplicativo ou enviá-los a uma página da Web ou o repositório de fora do seu aplicativo. 
2.    Votações - permitem coletar informações de usuários finais fazendo-as perguntas.
3.    Dados coloca - permitem que você enviar um arquivo de dados binários ou na Base 64. As informações contidas em um envio de dados são enviadas ao seu aplicativo para modificar a experiência atual dos usuários em seu aplicativo. Seu aplicativo precisa ser capaz de processar os dados em um envio de dados.

## <a name="campaign-details"></a>Detalhes de campanha

Você pode editar, clonar, excluir ou ativar campanhas que não tenham sido ativadas ainda passando sobre seus nomes ou você pode clicar para abri-los. Você pode clonar campanhas que já foi ativadas passando sobre seus nomes ou você pode clicar para abri-los. No entanto, você não pode alterar uma campanha depois que ele foi ativado.
 
![Reach1][18]

## <a name="reach-feedback"></a>Atingir comentários

Clique em **estatísticas** para ver os detalhes de uma campanha de alcance. Modo de exibição **simples** fornece uma representação visual na forma de um gráfico de barras de coluna sobre o que aconteceu depois uma campanha foi ativada. O modo de exibição **Avançado** fornece mais granulares detalhes sobre a campanha de envio. Esses detalhes não estará disponíveis se você estiver enviando uma campanha de teste isto é um envio enviado para um dispositivo de teste. Aqui está como você deve interpretar esses detalhes:

1. **Pushed** - Especifica o número de mensagens enviadas para os dispositivos. Esse número dependerá do público-alvo que você especificou ao criar a campanha de envio. Se você não especificar qualquer público-alvo, em seguida, este push será enviada para todos os dispositivos registrados. Como todos os outros serviços de envio, podemos não as notificações de envio diretamente aos dispositivos mas em vez disso push-las para a plataforma respectiva serviços de notificação específicos Push (PNS - GCM/APNS/WNS) para que eles podem oferecer as notificações para os dispositivos. 

2.  **Entregue** - Especifica o número de mensagens que são entregues pela PNS para o dispositivo com êxito e confirmada como recebida pelo SDK do contrato de celular. 
        
    *Motivos para entregue contagem sendo menor do que contagem enviada:*
    
    1. Se o usuário tiver desinstalado o aplicativo do dispositivo, mas o PNS não conhece-no momento que podemos enviar o envio para o PNS a mensagem será ignorada.
    2. Se o dispositivo tem o aplicativo, mas próprios dispositivos estavam offline por longos períodos de tempo, o PNS falhará entregar a mensagem para o dispositivo. 
    3. Se a mensagem chegar entregue para o dispositivo, mas o SDK do contrato Mobile no aplicativo não reconhece o conteúdo da mensagem, ele descarta essa mensagem. Isso pode acontecer se a personalização da notificação no aplicativo gera uma exceção que podemos capturar no SDK e solte a mensagem. Isso também pode ocorrer se o aplicativo do dispositivo está usando uma versão do SDK do contrato Mobile que não é capaz de entender a versão mais recente da mensagem push enviada da plataforma, mas isso é apenas quando o aplicativo foi atualizado após a notificação foi enviada da plataforma do serviço. Na guia **Avançado** informará quantas mensagens foram ignoradas. 
    4. Em dispositivos iOS, mensagens às vezes não acessar entregue se ambos o dispositivo está na bateria baixa ou se o aplicativo está consumindo quantidade significativa de energia ao processar notificações remotas. Esta é uma limitação dos dispositivos iOS.   

3.  **Exibidas** - Especifica o número de mensagens que são mostradas com êxito para o usuário do aplicativo no dispositivo na forma de uma notificação de envio/out-de-app sistema no Centro de notificação ou uma notificação do aplicativo dentro do aplicativo móvel.  Na guia **Avançado** informará quantos foram notificações do sistema e quantas eram notificações do aplicativo. 
    
    *Motivos para exibidas contagem sendo menor que a contagem de entregue (aguardando a ser exibido)*
    
    1. Se a campanha de notificação tinha uma data de término nele, em seguida, é possível que a notificação foi entregue, mas quando o tempo veio para abrir e exibi-la para o usuário do aplicativo, ele já foi expirado para que ele nunca foi exibido.   
    2. Se a notificação é uma notificação do aplicativo, em seguida, a notificação só será exibida quando o usuário do aplicativo abre o aplicativo. Em casos onde o usuário do aplicativo não abriu o aplicativo, o SDK informará que a notificação foi entregue, mas ainda não exibida até que o aplicativo é aberto. 
    2. Se a notificação é uma notificação do aplicativo e configurado ser mostrado em uma atividade/tela específica e também a notificação será relatada como entregue, mas ainda não entregues até que o usuário abre o aplicativo em uma tela específica. 
    
4.  **Interações do usuário** - Especifica o número de mensagens que o usuário do aplicativo tem interagir com e incluirá as mensagens que são actioned ou foi encerrado. 

    - *O usuário do aplicativo pode ação uma notificação de uma das seguintes maneiras:*
            
        1. Se a notificação é uma notificação do sistema/out-de-app ou uma notificação do aplicativo enviadas como somente notificação e o usuário do aplicativo clica na notificação.
        2. Se a notificação é uma notificação do aplicativo com um texto ou modo de exibição de web ou votações, em seguida, o usuário do aplicativo clica no botão de ação na notificação.
        3. Se a notificação é uma notificação do aplicativo com uma exibição da web, em seguida, o usuário do aplicativo clica em uma URL da web somente na exibição de [Android]
    
    - *O usuário do aplicativo pode sair de uma notificação de uma das seguintes maneiras:*
    
        1. Clicando no botão fechar a notificação diretamente. 
        2. Dedo ausente ou excluindo a notificação. 
        3. Notificações do aplicativo com conteúdo de texto/web e votações geralmente são exibidas para o usuário do aplicativo em um processo de duas etapas. Eles ver uma notificação primeiro e quando as pessoas clicarem nele, eles ver o conteúdo da web/texto/votação subsequente. O usuário do aplicativo pode sair uma notificação em uma destas etapas e os detalhes no modo de exibição avançado captura isso. 

5.  **Actioned** - Especifica o número de mensagens que foram explicitamente actioned pelo usuário aplicativo. Este é o número mais interessante como isso informa quantos usuários de aplicativo foram interessados pela mensagem que você enviadas na notificação. 
 
> [AZURE.NOTE] No iOS e janelas Abrir plataformas, se o usuário tiver o aplicativo e a campanha foi uma campanha "A qualquer momento" e é possível que ambos sair do aplicativo e notificações do aplicativo são exibidos ao mesmo tempo. Isso pode causar uma contagem de exibidas superior a entregue. Se o usuário interage ou ações a notificação e até mesmo a contagem de usuário interações/Actioned poderia ser maior do que entregue. 


![Reach2][19]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 
