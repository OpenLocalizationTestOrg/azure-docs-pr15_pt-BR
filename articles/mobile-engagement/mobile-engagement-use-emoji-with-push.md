<properties 
    pageTitle="Usar emoticons Emoji de contrato de celular do Azure" 
    description="Como usar emoticons Emoji dentro de suas notificações de envio"     
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="use-emoji-emoticon-within-push-notifications"></a>Usar o emoticon Emoji dentro de notificações de envio por Push

Você pode incluir Emoji emoticons em você notificação por push em algumas etapas simples: 

1. Em primeiro lugar para encontrar o Emoji que você deseja enviar na mensagem. Certifique-se de que o Emoji você estiver selecionando será suportado pelo dispositivo de destino como fabricantes de dispositivo levar algum tempo para adicionar Emojis aprovadas recentemente para as plataformas de dispositivo. 

2. No **Windows** - você pode navegar para esse [link](http://apps.timwhitlock.info/emoji/tables/unicode) e copiar o ícone 'Native'.

    ![][7] 

3. No **Mac** - você pode encontrar que os Emojis no aplicativo de dicionário em Editar -> Emoji & símbolos.

    ![][6] 

4. Agora, vamos para a guia **atinja** o portal do Azure Mobile contrato. Selecione o tipo da sua notificação de envio (anúncio de sondagem etc). Para este exemplo escolhemos um envio de lançamento.

5. Especifique os campos diferentes da notificação até atingir o texto da notificação. Isso é onde você adicionará seu Emoji Emoticon. Você pode optar por colocá-lo no título, a mensagem ou ambos. Você precisa arrastar e soltar ou copiar o Emoji que você achar dos locais acima. 

    ![][1]

6. Preencha os outros campos para a notificação e salvá-lo. 

7. Quando você executa um teste ou ativar o lançamento, você verá uma notificação com o emoticon conforme especificado.   

    ![][3] ![][4] ![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png

