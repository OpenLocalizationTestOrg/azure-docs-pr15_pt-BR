<properties 
   pageTitle="Interface de usuário do Azure contrato móvel - alcance conteúdo" 
   description="Saiba como gerenciar o conteúdo exclusivo dos diferentes tipos de campanhas de notificação de envio no contrato de celular do Azure" 
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

# <a name="how-to-manage-the-unique-content-of-the-different-types-of-push-notification-campaigns"></a>Como gerenciar o conteúdo exclusivo dos diferentes tipos de campanhas de notificação de envio
 
Você pode usar a seção de conteúdo de uma nova campanha de alcance para modificar o conteúdo da sua anúncios, votações, envia dados e peças (somente para o Windows Phone). A configuração de conteúdo de campanhas de envio é específica para o tipo de campanha. 
 
### <a name="content-types"></a>Tipos de conteúdo:
- Anúncios
- Votações
- Verificações extras de dados
- Blocos (somente para o Windows Phone)
 
## <a name="content-of-announcements"></a>Conteúdo de anúncios
 ![Alcance Content1][30] 

### <a name="choose-the-type-of-your-announcement"></a>Escolha o tipo de anúncio:
-    Notificação somente: é uma notificação padrão simple. O que significa que se um usuário clica nele, nenhum modo de exibição adicional será exibido, mas somente a ação associada a ele vai ocorrer.
-    Comunicado de texto: é uma notificação de que o usuário tenha uma olhada em um modo de exibição de texto é ativado.
-    Comunicado de Web: é uma notificação de que o usuário tenha uma olhada em um modo de exibição de web é ativado.

### <a name="see-also"></a>Consulte também
- [Atingir - como tutoriais - anúncios][Link 3] 

### <a name="about-web-view-announcements"></a>Sobre lançamentos de modo de exibição de Web:
Ocorrências do padrão de "{deviceid}" no código HTML ou código JavaScript que você fornecer aqui serão substituídas automaticamente pelo identificador do dispositivo exibindo o lançamento. Esta é uma maneira fácil de recuperar identificadores de dispositivos de contrato do Azure Mobile em um serviço web externos hospedado no seu escritório.
Se você quiser criar um modo de exibição de web de tela inteira (sem os botões de ação e sair de padrão fornecemos) você pode usar as seguintes funções de código de JavaScript do anúncio de exibir sua web: 

-    executar a ação de lançamento: ReachContent.actionContent()
-    sair do anúncio de: ReachContent.exitContent()
 
### <a name="choose-your-action"></a>Escolha a ação:

### <a name="about-action-urls"></a>Sobre URLs de ação:
Qualquer URL que pode ser interpretada pelo sistema operacional de um dispositivo de destino pode ser usado como uma URL de ação.
Qualquer dedicada URL que seu aplicativo pode oferecer suporte (por exemplo, para fazer com que os usuários saltar para uma determinada tela) também podem ser usados como uma URL de ação.
Cada ocorrência do padrão {deviceid} é substituída automaticamente pelo identificador do dispositivo executar a ação. Isso pode ser usado para recuperar facilmente identificadores de dispositivos do Azure Mobile contrato por meio de um serviço web externo hospedado no seu escritório.

- **Android + iOS ações**
    - Abrir uma página da web
    - http://\[domínio de site da web\] 
    - Exemplo: http://www.azure.com
    - Enviar um email
    - mailto:\[email-destinatário\]?subject =\[assunto\]& corpo =\[mensagem\] 
    - Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Enviar um SMS
    - SMS:\[número de telefone\] 
    - Exemplo: sms:2125551212
    - Discar um número de telefone
    - Tel:\[número de telefone\] 
    - Exemplo: tel:2125551212
- **Somente ações de Android**
    - Baixar um aplicativo no repositório de reprodução
    - Market://details?id=\[pacote de aplicativos\] 
    - Exemplo: market://details?id=com.microsoft.office.word
    - Iniciar uma pesquisa de localização geográfica-localizada
    - Geo:0, 0?q =\[consulta de pesquisa\] 
    - Exemplo: geo:0, 0? p = starbucks, paris
- **somente ações de iOS**
    - Baixar um aplicativo no repositório de aplicativo
    - http://iTunes.Apple.com/ [country] /app/ [nome do aplicativo] /id [id de aplicativo]? mt = 8 
    - Exemplo: http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Ações do Windows
    - Abrir uma página da web
    - http://\[domínio de site da web\] 
    - Exemplo: http://www.azure.com
    - Enviar um email
    - mailto:\[email-destinatário\]?subject =\[assunto\]& corpo =\[mensagem\] 
    - Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Enviar um SMS (o aplicativo de armazenamento do Skype obrigatório)
    - SMS:\[número de telefone\] 
    - Exemplo: sms:2125551212
    - Discar um número de telefone (o aplicativo de armazenamento do Skype obrigatório)
    - Tel:\[número de telefone\] 
    - Exemplo: tel:2125551212
    - Baixar um aplicativo no repositório de reprodução
    - MS-windows-store: PDP?PFN =\[ID do pacote de aplicativo\] 
    - Exemplo: ms-windows-store: PDP? PFN = 4d91298a-07cb-40fb-aecc-4cb5615d53c1
    - Iniciar uma pesquisa de bingmaps
    - bingmaps:?q =\[consulta de pesquisa\] 
    - Exemplo: bingmaps:? p = starbucks, paris
    - Usar um esquema personalizado
    - \[esquema personalizado\]://\[parâmetros de esquema personalizado\] 
    - Exemplo: myCustomProtocol://myCustomParams
    - Usar um pacote de dados (aplicativo Store para extensão ler obrigatório)
    - \[pasta\]\[dados\]. \[extensão\] 
    - Example:myfolderdata.txt
 
### <a name="build-a-tracking-url"></a>Crie uma URL de rastreamento:
-    Consulte a seção "Configurações" o <UI Documentation> para instruções sobre a criação de uma URL de acompanhamento que permitirá que os usuários baixem um de seus outros aplicativos.
 
### <a name="define-the-texts-of-your-announcement"></a>Definir os textos de anúncio
Preencha o título, conteúdo e textos de botão de seu lançamento. Você pode direcionar uma audiência de uma campanha de futura com base nos comentários alcance de como os usuários responderam a esta campanha. Público-alvo pode ser baseado nos comentários do se esta campanha foi apenas enviada, respondidas, actioned ou foi encerrado.

### <a name="see-also"></a>Consulte também
- [Interface do usuário documentação - alcance - novo critério de envio][Link 28]

## <a name="content-of-polls"></a>Conteúdo de votações
![Alcance conteúdo2][31] preencha o título, descrição e textos de botão de seu lançamento. Em seguida, adicione perguntas e opções para as respostas para suas perguntas.
Você pode direcionar uma audiência de uma campanha de futura com base nos comentários alcance de como os usuários responderam a esta campanha. Público-alvo pode ser baseado em se esta campanha foi apenas enviada, respondidas, actioned ou foi encerrado. Público-alvo também pode ser baseado nos comentários de resposta de votação, onde a pergunta e resposta escolha são usados como critérios.

### <a name="see-also"></a>Consulte também
- [Interface do usuário documentação - alcance - novo critério de envio][Link 28]
 
## <a name="content-of-data-pushes"></a>Envia conteúdo dos dados
![Alcance-Content3][32] 

### <a name="choose-the-type-of-your-data"></a>Escolha o tipo de dados:
- Texto
- Dados binários
- Dados Base64

### <a name="define-the-content-of-your-data"></a>Definir o conteúdo dos seus dados
- Se você selecionou Enviar dados de texto, copie e cole o texto na caixa "conteúdo".
- Se você selecionou para enviar dados binários ou na Base 64, use o botão "carregar seu arquivo" para carregar o arquivo.
- Você pode direcionar uma audiência de uma campanha de futura com base nos comentários alcance de como os usuários responderam a esta campanha. Público-alvo pode ser baseado em se esta campanha foi apenas enviada, respondidas, actioned ou foi encerrado.

### <a name="see-also"></a>Consulte também
- [Interface do usuário documentação - alcance - novo critério de envio][Link 28]

## <a name="content-of-tiles-windows-phone-only"></a>Conteúdo dos blocos (somente para o Windows Phone)
![Alcance-Content4][33]

### <a name="define-the-content-of-your-tile"></a>Definir o conteúdo de seu bloco
A carga de bloco é o texto a ser exibido no bloco do aplicativo em dispositivos Windows Phone.
Um envio de bloco é a versão de serviço de notificação de envio da Microsoft (MPNS) de um envio nativo para Windows Phone. O tipo de envio de bloco é o único tipo de envio que não tem uma resposta e então a audiência de campanhas futuras não pode ser construída nos resultados de uma campanha de envio de peças. 

### <a name="see-also"></a>Consulte também
- [API documentação - alcance API - nativo Push][Link 4]

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
 
