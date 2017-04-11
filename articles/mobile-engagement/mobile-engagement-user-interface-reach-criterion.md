<properties 
   pageTitle="Interface de usuário do Azure contrato móvel - alcance critério" 
   description="Saiba como usar critérios de direcionamento para enviar campanhas de envio para selecionar um subconjunto de seus usuários usando o contrato de celular do Azure" 
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


# <a name="how-to-use-targeting-criteria-to-send-push-campaigns-to-a-select-subset-of-your-users"></a>Como usar critérios de direcionamento para enviar campanhas de envio para selecionar um subconjunto de seus usuários

O direcionamento de sua audiência por critérios específicos com o botão "Novos critérios" é um dos conceitos mais eficientes em Azure Mobile contrato que ajuda a enviar relevantes notificações de envio que os clientes respondam em vez de spam todos. Você pode limitar a sua audiência com base em critérios padrão e simular coloca para determinar quantas pessoas receberão a notificação.

**Consulte também:**

- [Interface do usuário documentação - alcance - nova campanha de envio][Link 27]

## <a name="audience-criteria-can-include"></a>Critérios de audiência podem incluir:
- **Technicals:** Você pode direcionar com base nas mesmas informações técnicas que você pode ver nas seções Monitor e análise. **Consulte também:** [Documentação de interface do usuário - Analytics] [ Link 15], [Documentação de interface do usuário - Monitor][Link 16]
- **Local:** Aplicativos que usam o "local de Tempo Real relatório" com geográfica-instalação da cerca podem usar localização geográfica como critérios para uma audiência do local GPS de destino. Também chamada de "Preguiça área local Reporting" ser usado para uma audiência do telefone celular local de destino ("local de Tempo Real relatório" e "Relatório de localização de área preguiçoso" devem ser ativada do SDK). **Consulte também:** [Documentação do SDK - iOS - integração] [ Link 5], [SDK documentação - Android - integração][Link 5]
- **Atinja comentários:** Você pode direcionar sua audiência com base nos seus comentários de notificações de alcance anterior por meio de comentários de alcance de anúncios, votações e envia dados. Isso permite melhor destino sua audiência após dois ou três atinja campanhas que você poderia na primeira vez. Ele também pode ser usado para filtrar os usuários que já recebeu uma notificação com conteúdo semelhante, definindo uma campanha não sejam enviadas aos usuários que já recebeu uma determinada campanha anterior. Você pode até mesmo excluir usuários que estão incluídos uma campanha específica que ainda está ativa recebam coloca novo. **Consulte também:** [Documentação de interface do usuário - atinja - Push Conteúda][Link 29]
- **Instalar controle:** Você pode controlar informações com base em onde os usuários instalado seu aplicativo. **Consulte também:** [Documentação de interface do usuário - configurações][Link 20]
- **Perfil de usuário:** Você pode direcionar padrão com base nas informações do usuário e você pode destino com base nas informações de aplicativo personalizado que você criou. Isso inclui usuários que estão conectados no momento e usuários que atendeu perguntas específicas solicitado que você tenha a configurar no próprio aplicativo em vez de apenas como eles respondeu a campanhas anterior. Todas as suas informações de aplicativo definido para seu aplicativo aparecem nesta lista.
- Segmentos: Você também pode destino com base em segmentos que você tenha criado com base em comportamento de usuário específico que contém vários critérios. Todos os segmentos definidos para seu aplicativo aparecem nesta lista. **Consulte também:** [Documentação de interface do usuário - segmentos][Link 18]
- **App Info:** Marcas de informações de aplicativo personalizadas podem ser criadas em "Configurações" para controlar o comportamento do usuário. **Consulte também:** [Documentação de interface do usuário - configurações][Link 20]

## <a name="example"></a>Exemplo: 
Se você quiser enviar um comunicado somente para o subconjunto dos usuários que executou uma ação de compra do aplicativo.

1. Vá até a página de configurações do aplicativo, selecione o menu "App info" e selecione "Novo app info"
2. Registrar uma novo informações de aplicativo booliana chamada "inAppPurchase"
3. Tornar o seu aplicativo definir essas informações de aplicativo para "true" quando o usuário executa com êxito uma compra do aplicativo (usando o sendAppInfo ("inAppPurchase", …) função)
4. Se você não quiser fazer isso partir de seu aplicativo, você pode fazer isso em seu back-end usando o dispositivo API)
5. Em seguida, basta necessárias para criar seu lançamento, com um critério limitando sua audiência aos usuários tendo "inAppPurchase" definida como "true")
 
> Observação: Direcionamento com base em critérios diferentes marcas de informações de aplicativo requer Azure Mobile contrato reunir informações de dispositivos de seus usuários antes do envio sejam enviado e, portanto, pode causar um atraso. Configuração de envio complexas opções (como atualizando selos) também podem atrasar envia. Usar uma campanha de "um captura" da API Push é o método de envio mais rápido absoluto em contrato de celular do Azure. Usando marcas de informações de aplicativo somente como critérios de envio para uma campanha de alcance (a partir da API atinja ou a interface do usuário) é o método mais rápido próxima como marcas de informações do aplicativo são armazenadas no lado do servidor. Usando outros critérios de direcionamento para uma campanha de envio é o método de envio mais flexível, mas mais lento, pois Azure Mobile contrato tem que consultar os dispositivos para enviar a campanha.
 
![Alcance-Criterion1][29] 

## <a name="criterion-options-apply-to"></a>Opções de critério se aplicam a:
- **Technicals**     
- Nome firmware: nome Firmware
- Versão do firmware: versão de Firmware
- Modelo do dispositivo: modelo de dispositivo
- Fabricante do dispositivo: o fabricante do dispositivo
- Versão do aplicativo: versão do aplicativo
- Nome da transportadora: nome da transportadora, indefinido
- País Carrier: país Carrier, indefinido
- Tipo de rede: tipo de rede
- Localidade: localidade
- Tamanho de tela: tamanho de tela
- **Local**      
- Última conhecida área: país, região, localidade
- Tempo real geográfica-instalação da cerca: lista de POIs (nome, ações), aponta para Circular (nome, Latitude, Longitude, Radius em metros)
- **Atingir comentários**     
- Comentários de comunicado: comunicado, comentários
- Pesquisar comentários: sondagem, comentários
- Pesquisar comentários de resposta: Pesquisar comentários de resposta, pergunta, escolha
- Comentários de envio de dados: envio de dados, comentários
- **Instalar o controle**     
- Armazenar: Armazenamento, indefinido
- Origem: Fonte, indefinido
- **Perfil de usuário**     
- Gênero: Masculino ou feminino, indefinido
- Data de origem: operador, data, indefinido
- Opt-in: verdadeiro ou falso, indefinido
- **Informações de aplicativo**      
- Caracteres: Cadeia indefinida
- Data: operador, data, indefinido
- Inteiro: operador, número, indefinido
- Booliano: verdadeiro ou falso, indefinido
- **Segmento**    
- Nome segmentos (da lista suspensa), exclusão (usuários de destino que não fazem parte deste segmento).

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
 
