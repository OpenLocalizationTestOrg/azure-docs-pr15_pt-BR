<properties 
   pageTitle="Interface de usuário do Azure contrato móvel - segmentos" 
   description="Aprenda a criar e gerenciar segmentos de usuários a identificar padrões de uso usando o contrato de celular do Azure" 
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

# <a name="how-to-create-and-manage-segments-of-users-to-identify-usage-patterns"></a>Como criar e gerenciar segmentos de usuários a identificar padrões de uso

Este artigo descreve a guia **segmentos** do portal do **Contrato de celular** . Você usa o portal de **Contrato Mobile** para monitorar e gerenciar seus aplicativos móveis.

A seção de segmentos da interface do usuário permite que você trabalhe em segmentação seus usuários com base no comportamento diferente e a análise de que você pode obter do aplicativo e também pode acessar por meio da API de segmentos. Segmentos primeiro são calculados 24 horas após eles são criados, e eles são recalculados a cada 24 horas com base nas informações de análise do mais recentes. Depois que um segmento é calculado, ele exibe um gráfico de "Dia histórico de dia" cada dia.


>[AZURE.NOTE] Muitas seções do portal do **Mobile contrato** UI contêm o botão **Mostrar Ajuda** . Pressione este botão para obter mais informações contextuais sobre uma seção.

## <a name="create-segments"></a>Criar segmentos
Você pode criar um segmento com base em critérios de até 10 em um período específico para cima a 60 dias no passado da seção de análise. Por exemplo, você pode criar um segmento com base nas pessoas que têm exibidos determinadas páginas ou pesquisou conteúdo específico em seu aplicativo nos últimos 10 dias. Essas informações estarão disponíveis na seção de análise. Portanto, você pode usá-lo para criar um segmento e configurar uma notificação de envio para esse subconjunto de usuários de destino para obtê-los para voltar para o aplicativo. 
 
> Observação: Depois que um segmento foi calculado, não poderá ser editado; pode apenas ser duplicado (copiado) ou destruído (excluído). Um segmento pode ser duplicado dentro do mesmo aplicativo (com o mesmo AppID) e, em seguida, ele também pode ser duplicado em outros aplicativos (com um AppID diferentes). 
 
 ![segments1][35] 

## <a name="examples-segments"></a>Segmentos de exemplos
 ![segments2][36]

Segmentos permitem que você os usuários finais do seu aplicativo de segmento.
Segmentação de seus usuários é uma estratégia de marketing importante. Contrato de celular Azure permite que você obtenha dados históricos e criar segmentos personalizados. Esta ferramenta poderosa permite que você saiba mais sobre a experiência de seus clientes em seu aplicativo. Analisar os segmentos e facilmente usar os segmentos como destinos de envio.
Um caso de uso comum é que você deseja enviar uma notificação para incentivar os usuários finais para classificar o seu aplicativo no repositório de um envio. Em vez de enviar uma notificação para todos os seus usuários finais, você pode criar um segmento que especifique somente os usuários que tem usado seu aplicativo todos os dias para o último mês e tinham um ótimo usuário a experiência. Quando você envia notificações por push menos e altamente direcionadas, você recebe um melhor ROI.
 
 ![segments3][37]

### <a name="segments-you-can-create-based-on-the-major-azure-mobile-engagement-elements"></a>Segmentos que você pode criar com base nos principais elementos de contrato do Azure Mobile:
- Evento: Crie um segmento que destinos um específico evento do aplicativo que aconteceu com mais de duas vezes uma semana. 
- Sessão: Crie um segmento de usuários que usou o aplicativo mais de 5 vezes na semana passada.
- Atividade: Crie um segmento de usuários que tenha usado uma página ou conteúdo mais ou menos tempo 10 no mês passado.
- Tarefa: Crie um segmento de usuários que concluiu um trabalho mais de duas vezes por dia.
- Falha: Crie um segmento de todos os usuários que tiveram uma falha na semana passada de mais de 10 vezes. (Você poderia push deste segmento com um pedido de desculpas ou até mesmo um cupom!)
- Erro: Crie um segmento de todos os usuários que tiveram o erro mais do 100 vezes da últimos 3 dias.
- Informações de aplicativo: Crie um segmento que tem como alvo uma App Info personalizado que aconteceram durante os últimos dias 25.
 
 ![segments4][38]

Para usar o segmento de maneira ideal, você deve ter feito uma integração personalizada do SDK em seu aplicativo com um plano de marcação de marcas de "App Info".
Em seguida, vá para a home page da interface, selecione o aplicativo desejado e clique na seção "Segmentos".

1. Selecione a seção "Segmentos".
2. Clique em "novo segmento" botão para criar um novo segmento.

## <a name="real-life-example-create-a-simple-segment-based-on-session-information"></a>Exemplo de vida real: Criar um segmento simple com base nas informações de "Sessão"
Crie um segmento de todos os usuários finais que tenha usado seu aplicativo pelo menos 50 vezes na semana passada. A partir daí, encontre somente os usuários finais que gasto com pelo menos 30 segundos em seu aplicativo por sessão. Isso mostrará todos os usuários finais que tenham uma experiência positiva em seu aplicativo. Em seguida, o segmento criado pode ser usado para enviar uma notificação para esses usuários finais peça para classificar seu aplicativo no repositório.
 
 ![segments5][39]

1. Nomeie seu segmento para encontrá-lo rapidamente na lista de "Segmento".
2. Clique no botão "Criar".
 
 ![segments6][40]

Selecione a sessão.
 
 ![segments7][41]

1. Selecione o período de "Semana passada".
2. Clique em Avançar.
 
 ![segments8][42]

1. Selecione o operador que é relevante entre a lista: =; ≥, ≤.
2. Insira o número desejado.
3. Selecione a ocorrência desejado. 
4. Clique em Avançar.
Este exemplo é definido para que a semana passada, correspondam aos usuários que feitas pelo menos 50 sessões.
 
 ![segments9][43]

Para a segmentação de sessão, você pode escolher o comprimento por sessão como um critério.

1. Selecione o operador na lista.
2. Forneça o tamanho por sessão.
3. Clique em Avançar.
Neste exemplo, ele diz que ao longo de todas as sessões que tenha sido segmentada na seção ocorrência, selecione somente os usuários que gasto com mais de 30 segundos por sessão.
 
 ![segments10][44]

Nomeie seu critério para recuperá-la no funil concluído e clique em Concluir.
 
 ![segments11][45]

Quando você terminar a configuração do critério, ele aparecerá no funil de segmento.
Como um segmento é baseado em dados de análise, segmentos são calculados uma vez por dia.
Neste exemplo, 47,7% dos usuários finais totais corresponde ao critério. Eles devem ser os usuários que tiveram uma boa experiência e serão provável fornecer uma classificação maior se você enviar uma notificação solicitando para classificar o aplicativo no repositório.


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
 
