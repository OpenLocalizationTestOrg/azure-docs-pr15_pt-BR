<properties
   pageTitle="Interface de usuário do Azure contrato móvel - Analytics"
   description="Aprenda a analisar dados históricos sobre seu aplicativo usando o contrato de celular do Azure"
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

# <a name="how-to-analyze-historical-data-about-your-application"></a>Como analisar dados históricos sobre seu aplicativo

Este artigo descreve a guia de **análise** do portal do **Contrato de celular** . Você usa o portal de **Contrato Mobile** para monitorar e gerenciar seus aplicativos móveis. Observe que para começar a usar o portal do primeiro é necessário criar uma conta do **Azure Mobile contrato** .


A seção de análise da interface do usuário fornece informações agregadas sobre seu aplicativo com base em dados históricos que são atualizados a cada 24 horas. As informações são exibidas em diferentes painéis compostos de mapas, grades e gráficos de barra/linha/pizza. Os dados também podem ser baixados como arquivos. csv. A maioria das mesmas informações está disponível em tempo real na seção Monitor da interface do usuário, e também podem ser acessada da API Analytics.

>[AZURE.NOTE] Muitas seções do portal do **Mobile contrato** UI contêm o botão **Mostrar Ajuda** . Pressione este botão para obter mais informações contextuais sobre uma seção.

## <a name="standard-and-custom-analytics"></a>Análise de padrão e personalizado

Contrato de celular Azure fornece um conjunto de informações analíticas básicas e padrão sobre os aplicativos que podem ser visualizados como gráfico assim que você integrar seu aplicativo com o SDK. Contrato de celular Azure também oferece a capacidade de reunir informações de análise personalizadas adicionais que desejar sobre o comportamento de seus usuários finais. Você pode fazer isso criando um plano de marca de "Marcas personalizadas (app info)", criado a partir de **configurações** para que o Azure Mobile contrato pode coletar esses dados adicionais para você.



## <a name="analytics"></a>Análise
- Painel: Mostra informações gerais sobre seus usuários novos e ativará e seus tendências.
- Usuários: Os usuários são identificados por seu identificador de dispositivo: este identificador é exclusiva para cada dispositivo (um novo usuário é realmente um novo dispositivo). Um usuário é considerado como nova em um intervalo de tempo determinado, se ele tiver realizado sua primeira sessão durante este intervalo de tempo. Um usuário é considerado como mantidos se ele tiver executado pelo menos uma sessão durante os últimos 7 dias. Usuários ativos são usuários que feitas pelo menos uma sessão durante um determinado período. Você pode classificar por, mensal, semanal, diariamente ou por hora períodos de tempo. Todos os gráficos são semelhantes, mas permitem que você para filtrar por recursos diferentes, como a versão do seu aplicativo e depois para classificar por um período de tempo. As informações padrão obtidas por meio da integração o SDK incluem o seguinte: usuários ativos, o novo usuário, o número de sessões, o comprimento de cada sessão, informações técnicas sobre o país/região, locais, local, carrier de idioma, dispositivos, firmware, rede (WIFI), versões do aplicativo e SDK, usado por clientes. Essas informações podem ser exibidas em tempo real da seção monitor.

> Observação: O período de tempo se baseia na data dos usuários configurações de dispositivo, para que um usuário cuja telefone tem a data incorretamente definida poderia aparecem no período de tempo incorreto.

- Retenção: Um usuário é considerado como mantidos em um intervalo de tempo determinado se ele tiver realizado sua primeira sessão durante este intervalo de tempo. Você pode alterar os intervalos de tempo durante o qual os usuários retidos (e novos usuários) são contados para horas, dias, semanas ou meses. A análise de retenção de usuário baseia-se na parte superior de colaboradores. Um cohort é o conjunto de todos os novos usuários detectado em um determinado período (ou seja, o conjunto de usuários que executarão sua primeira sessão durante esse período). Usamos colaboradores de 1 dia, 2 dias, 4-dia, 7 dias ou 1 mês. Todos os dias de 1, 2 dias, 4-dia, 7 dias, ou 1 mês, Azure contrato móvel calcula o conjunto de todos os usuários que pertencem a cohort e são fornecidas um cohort, ainda ativo (ou seja, o conjunto de usuários que executou pelo menos uma sessão durante o período). Este conjunto de usuários é chamado de uma versão de cohort. (Azure Mobile contrato mostrar quantos usuários ainda estiver usando seu aplicativo, mas somente a loja específica de plataforma informa quantos usuários desinstalado seu aplicativo - por exemplo, GooglePlay, iTunes, Windows Store, etc.).
- Sessões: Um uso do aplicativo por um usuário. Sessões são geradas da sequência de atividades realizadas pelos usuários (uma atividade geralmente está associada ao uso de uma tela do aplicativo, mas isso pode variar, dependendo da maneira que o SDK foi integrado no aplicativo). Um usuário pode realizar apenas uma atividade por vez: uma sessão começa assim que o usuário iniciar sua primeira atividade e parar quando ele termina sua última atividade. Se um usuário permanecer mais de alguns segundos sem executar qualquer atividade, em seguida, sua sequência de atividades é dividida em duas sessões distintas.
- Atividades: Os nomes de cada tela em seu aplicativo e o comprimento de usuários de tempo gastam em cada tela. Atividades são uma opção analítica personalizada que corresponderão às marcas "app info" você configurou para seu próprio aplicativo:
- Caminho do usuário: Mostra como seus usuários navegar por meio de atividades do seu aplicativo (telas). Você pode mover o controle deslizante para ajustar o nível de detalhes. Nós azuis representam as atividades do seu aplicativo. Seu tamanho é proporcional o tempo gasto usuários nele. Nós brancos representam parar e iniciar sessão. Nós vermelhas representam travar. Os links representam transições entre as atividades do seu aplicativo (ou atividades e travamentos). Clique em um nó ou um link para exibir uma dica de ferramenta com mais informações sobre seus dados: o tempo gasto em uma tela específica, a contagem de transições e a porcentagem de transições da atividade de origem para a atividade de destino. (Um---60%---> B significa que os usuários em uma atividade ficará atividade B 60% do tempo.) Você pode reorganizar o gráfico que você deseja esclarecer sua posição é salva toda vez que fizer uma alteração. Você pode mostrar ou ocultar as falhas para clarear o gráfico.
- Eventos: Ações específicas feitas por um usuário no aplicativo. A distribuição de eventos é mostrada como a contagem de eventos por usuário por sessão. Um evento representa uma ação instantânea, por exemplo, um clique em um botão ou a recepção de uma notificação. (O significado de eventos depende de como o SDK foi integrado no aplicativo.) Um evento pode ocorrer durante uma sessão ou um trabalho ou pode ser autônomo.
- Tarefas: Semelhantes aos eventos, exceto que eles focalizar o comprimento da ação. Por exemplo, trabalhos poderiam informar informações técnicas sobre quanto tempo leva conteúdo a carga ou uma chamada ao serviço web. Ele também pode mostrar quanto tempo leva um usuário para preencher um formulário, crie uma conta ou fazer uma compra. Um trabalho representa a duração de uma tarefa, por exemplo, a duração de uma tarefa de download ou a hora uma faixa é exibida na tela. (O significado de trabalhos depende de como o SDK foi integrado no aplicativo.) Trabalhos são geralmente associados a tarefas de plano de fundo que são executadas fora do escopo de uma sessão (ou seja, sem qualquer atividade de usuário).
- Technicals: Informações técnicas sobre os dispositivos dos usuários do aplicativo que você pode controlar, como a localidade, Carrier, rede, dispositivo, Firmware e tamanho dos dispositivos dos usuários e a versão do seu aplicativo e a versão SDK usada no seu aplicativo de tela.
- Erros: Informações sobre erros técnicos dentro do aplicativo que não causam o aplicativo falhar. Um erro representa um problema instantâneo, por exemplo, uma falha de rede ou uma manipulação incorreta. (O significado de eventos depende de como o SDK foi integrado no aplicativo.) Um erro pode ocorrer durante uma sessão ou um trabalho ou pode ser autônomo.
- Travar: Informações sobre erros que causam seu aplicativo falhar. Uma falha é uma condição inesperada onde o aplicativo não executará suas funções esperadas e deve ser interrompido. Uma falha é geralmente devido a um erro no aplicativo.

![Analytics2][11]

## <a name="accessing-the-retention-overview"></a>Acessando a visão geral de retenção
![Analytics3][12]

A visão geral de retenção é dividida no meio em vários cartões de visita, cada uma mostrando a visão geral de um determinado período de retenção. O período de retenção de 2 dias é visto no exemplo. Outros cartões de mostram os períodos de retenção de 4 dias e 7 dias.

## <a name="understanding-the-retention-overview-cards"></a>Noções básicas sobre os cartões de visão geral de retenção
![Analytics4][13]

### <a name="each-card-is-composed-of-3-main-parts"></a>Cada cartão é composto de 3 partes principais:
1. 1: o cohort e o período considerados
2. 2-4: a retenção para o período atual
3. 5: um minigráfico do histórico

### <a name="here-is-detailed-information-about-each-element"></a>Aqui está informações detalhadas sobre cada elemento:
1.    Cohort e período: este título oferece o tipo de cohort. Veja "2 dias período" significa que vamos examinar o comportamento de usuários por 2 dias, os usuários que chegaram durante um período de 2 dias e se conectarem nos seguintes blocos de 2 dias. O exemplo acima considera a atividade de usuários entre os 21 e 22 de novembro.
2.    Isso retorna a taxa de retenção sobre a 21 e 22 de novembro para os usuários que chegam em 19 e 20 de novembro. Aqui, tivemos 1 usuário ativo entre os 21 e 22, sobre o 3 que estavam novos usuários entre o dia 19 e 20.
3.    Esse indicador visual fornece as mesmas informações acima são representados graficamente. (O terceiro do círculo é do número 33%.) A cor fornece informações adicionais: verde indica este número está aumentando do cálculo anterior. Amarelo significa estável e vermelha significa diminuindo.
4.    Isso indica os valores usados para o cálculo.
5.    Este é um minigráfico do histórico dos valores de retenção. Ele permite que você veja os valores no passado para ter uma visão geral de como ele desenvolvido.


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
