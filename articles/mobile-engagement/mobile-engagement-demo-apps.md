<properties
    pageTitle="Aplicativo de demonstração de contrato Mobile Azure | Microsoft Azure"
    description="Descreve onde baixar, como usar e os benefícios de usar o aplicativo de demonstração de contrato de celular do Azure"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/10/2016"
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement-demo-app"></a>Aplicativo de demonstração do Azure contrato de celular

Nós já publicados um aplicativo de demonstração do Azure Mobile contrato para **iOS**, **Android**e **Windows** plataformas ajudar você a localizar recursos úteis e saiba mais sobre o contrato de celular.

O aplicativo ajuda você a:

- Localize facilmente links úteis para os recursos de contrato Mobile como vídeos, documentação, Fórum de suporte e para onde ir para gerar solicitações de recursos.
- Notificações de amostra de experiência que são suportadas pelo contrato de celular para obter ideias para seus próprios aplicativos móveis.
- Use uma implementação de referência para estudar como implementar o contrato de Mobile em seu próprio aplicativo. Você pode aprender a:

    - Colete dados de análise.
    - Implemente cenários de notificação avançados de tipos como *intersticiais de tela inteira* ou *pop-up*.
    - Implementar pesquisas e votações.
    - Implemente cenários de dados e envio por push silencioso.   

## <a name="app-installation"></a>Instalação de aplicativo
Este aplicativo está disponível nos seguintes armazenamentos aplicativo:

- **Aplicativo de demonstração Universal do Windows**:

    - Baixe o aplicativo no [aplicativo Windows armazenar](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2).
    - O aplicativo foi desenvolvido como um aplicativo Universal do Windows 10. O código-fonte está disponível no [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows).

- **aplicativo de demonstração do iOS**:

    - Baixe o aplicativo na [Apple armazenar](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090).
    - O aplicativo foi desenvolvido no iOS rápido. O código-fonte está disponível no [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios).

- **Aplicativo de demonstração android**:

    - Baixe o aplicativo na [armazenar Google Play](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement).
    - O código-fonte está disponível no [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android).

![Aplicativo de demonstração Universal do Windows][1]

![aplicativo de demonstração de iOS][2]
![aplicativo de demonstração Android][3]


## <a name="usage"></a>Uso

Você pode usar este aplicativo das seguintes maneiras:

**Baixe o aplicativo em seu dispositivo do aplicativo armazenar links (fornecidas anteriormente):**

>[AZURE.IMPORTANT] Não é necessário uma conta do Microsoft Azure ou precisar conectar o aplicativo a um back-end. O aplicativo funciona independentemente.

- Depois que o aplicativo em seu dispositivo, você pode percorrer os links no menu do lado esquerdo para localizar os recursos úteis sobre o contrato de celular.
- Adicionamos o [RSS do serviço feed](https://aka.ms/azmerssfeed) no aplicativo para que você sempre está atualizado sobre as últimas atualizações do produto.
- Você também pode ir através dos cenários de notificação de amostra para experimentar o tipo de notificações que são suportados pelo Mobile contrato para cada plataforma. Essas notificações podem ocorrer localmente - que é, você pode clicar nos botões nas telas para mostrar a experiência de notificações, que é idêntica ao enviar as notificações da plataforma do contrato de celular.

![Menu de aplicativo para Windows][4]

![Menu de aplicativo para iOS][5]
![menu de aplicativo para Android][6]

**Baixe o código-fonte dos links GitHub (fornecidos anteriormente):**

- Depois de baixar o código-fonte, abra-o no ambiente de desenvolvimento respectivos – XCode para iOS, Android Studio para Android e Visual Studio para Windows.
- Em seguida, você deverá seguir nossos [etapas básicas de integração de SDK](mobile-engagement-windows-store-dotnet-get-started.md) para que você possa se conectar a esse aplicativo para sua própria instância de back-end do contrato de celular.
    - Você precisa configurar uma cadeia de conexão no aplicativo.
    - Você também precisa configurar a plataforma de notificação por push para o aplicativo.
- Você notará que o próprio aplicativo tem como instrumentos contrato de celular. Portanto, ao abrir o aplicativo após conectá-lo ao back-end, você poderá ver a sessão do usuário, atividades, eventos e assim por diante, na guia **Monitor** .
- Você também poderá enviar notificações para este aplicativo em sua própria instância de contrato de celular, em vez de usar notificações de locais.
    - Aqui você pode adicionar seu dispositivo como um dispositivo de teste usando o item de menu **obter a identificação de dispositivo** no aplicativo. Assim, você obtém um ID de dispositivo que você registra como um dispositivo de teste com sua instância de back-end de plataforma.

    ![Identificação de dispositivo no Windows][7]

    ![ID do dispositivo IOS][8]
    ![identificação de dispositivo no Android][9]

## <a name="key-features-of-the-demo-app"></a>Principais recursos do aplicativo demonstração

- Conforme mencionado anteriormente, com este aplicativo, você tem todos os recursos principais para contrato de celular na sua mão. Você pode usar os links no menu à esquerda.

- Você pode experimentar as notificações de fora do aplicativo para cada plataforma. Essas notificações podem ser fornecidas como **notificação somente**, onde clicar notificação simplesmente abre uma tela nativo do aplicativo (usando **vinculação profunda**) – ou um **anúncio da Web**, onde você pode fornecer conteúdo HTML adicional do back-end Mobile contrato seja exibida quando a notificação é clicada.

    ![Notificações de fora do aplicativo][29]

- IOS, você precisa fechar o aplicativo para ver as notificações de envio do sistema ou fora do aplicativo. Você pode examinar a implementação aqui para adicionar **botões de ação**, como aqueles que são adicionados a essa notificação de fora do aplicativo para *comentários* e *compartilhamento* (de forma que o usuário pode demorar direita de ação a partir da notificação próprio).

    ![Notificações de fora do aplicativo IOS][11] ![Exibição de notificação de fora do aplicativo IOS][14]

- No Android, as opções de suporte estão adicionando texto de várias linhas (**Texto grande**) ou uma imagem de notificação (**Visão geral**) à notificação, juntamente com os **botões de ação** (conforme suportado pelo iOS).

    ![Notificações de fora do aplicativo no Android][12] ![Exibição de notificação de fora do aplicativo no Android][15]

- No Windows 10, você pode ver a aparência das notificações no PC. Essa notificação também é mostrada no **Centro de notificação**do Windows 10. Não há nenhum suporte para adicionar **botões de ação** no momento no SDK do Windows.

    ![Notificações de fora do aplicativo no Windows][10] ![Exibição de fora do aplicativo no Windows][13]

- Você pode experimentar notificações de "do aplicativo" padrão para cada plataforma. Isso é uma experiência de duas etapas onde uma janela de **notificação** é exibida primeiro. Quando você clicar nele, ele abre um **comunicado**, em tela inteira como exibido na captura de tela a seguir. O conteúdo deste anúncio vem de sua instância de back-end do contrato de celular. O SDK tem modelos para notificações e anúncios. Você pode facilmente personalizá-las, como mostra este aplicativo de demonstração com a adição de nosso logotipo e colorir.  

    ![Notificações do aplicativo no Windows][16]

    ![Notificações do aplicativo IOS][17]  ![Notificações do aplicativo no Android][18]

    **iOS**, **Android**

- Você também pode usar o recurso de **categoria** de contrato Mobile personalizar esta experiência padrão. No aplicativo demonstração, nós já demonstrou duas maneiras de alterar a experiência das notificações. Observe que o recurso de categoria ainda não é suportado no SDK do Windows.

    **Tela inteira intersticial:**

    ![Notificação do aplicativo - categoria intersticial][30]

    ![Categoria intersticial IOS][21]  ![Categoria intersticial no Android][22]

    **Notificação de pop-up:**

    ![Notificação do aplicativo - categoria pop-up][31]

    ![Notificação de pop-up IOS][19]   ![Notificação de pop-up no Android][20]

**iOS**, **Android**

- Contrato de Mobile também dá suporte a um tipo especializado de notificação do aplicativo chamado **votações**. Isso permite que você enviar pesquisas rápidas para seus usuários de aplicativo segmentado. Você pode adicionar perguntas e opções para cada pergunta como a captura de tela a seguir. Isso irá obter exibido como uma notificação do aplicativo para o usuário do aplicativo.   

    ![Notificações de votação][32]

    ![Pesquisa no Windows][26]

    ![Pesquisa IOS][27]   ![Pesquisa no Android][28]

**iOS**, **Android**

- Contrato de celular também suporta enviar silenciosas notificações de **Envio de dados** . Com essas notificações, você pode enviar dados de seu serviço (como os dados JSON no exemplo a seguir), que você pode tratar de seu aplicativo e executar alguma ação. Um exemplo é como estamos alterando o preço de um item seletivamente usando as notificações de envio de dados.

    ![Notificação de envio de dados][33]

    ![Notificação de envio de dados no Windows][23]

    ![Notificação de envio de dados IOS][24]  ![Notificação de envio de dados no Android][25]

**iOS**, **Android**

> [AZURE.NOTE] Você pode exibir instruções detalhadas para qualquer uma dessas notificações clicando em **clique aqui para obter instruções sobre como enviar essas notificações de plataforma Mobile contrato** em qualquer tela de notificação de amostra.


[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png
