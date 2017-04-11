<properties 
    pageTitle="Enviar notificação personalizada com contrato de celular do Azure" 
    description="Como enviar notificações personalizadas, incluindo informações de perfil de usuário nas notificações como seus nomes"        
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="all" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="personalize-notifications-by-including-user-name"></a>Personalizar notificações, incluindo o nome de usuário

Na busca para tornar as notificações mais atraente para os usuários do aplicativo, você deve considerar personalizando-los. Uma abordagem eficiente consiste de seletivamente usando os nomes de usuários do aplicativo para as notificações para torná-las mais pessoal de endereços. Uma palavra de precaução aqui - você deve abordar adicionando nomes de usuário para as notificações cuidadosamente porque se você sobrecarregar essa estratégia, em seguida, ele pode se deparar com como assustador para alguns usuários de aplicativo. Você também deve garantir que você está permitir que o usuário optar para fornecer esses detalhes pessoais a você consentimento completo no aplicativo móvel antes de começar a usar isso. 

Tecnicamente, com o Azure Mobile contrato, você pode realizar Personalizando as notificações seguindo as etapas abaixo que usaremos o cenário de incluir o nome de usuário às notificações. Você usará o conceito de informações do aplicativo ou marcas cujos valores poderiam ser passados pelo SDK do integrada no aplicativo Mobile ou por meio de APIs. Essas informações de aplicativo ou marcas podem ser usadas:

1. para o direcionamento notificações para usuários específicos com base nos valores das informações de aplicativo ou 
2. como espaços reservados nas notificações que serão substituídas com valores específicos do dispositivo/usuário ao enviar notificações para esse dispositivo. 

> [AZURE.IMPORTANT] Observe que a velocidade de enviar notificações verá uma redução devido a esse processamento adicional de substituir valores de informações de aplicativo com cada notificações. 

##<a name="register-app-info-in-the-mobile-engagement-portal"></a>Registrar informações de aplicativo no Portal do contrato móvel

1) Você começar com Registrando informações de aplicativo ou marcas no portal do Azure. Vá para **configurações** -> **marca (aplicativo-Info)** para isso.  

![][1]  

2) Clique em **nova marca (aplicativo-info)** e fornecer o nome como *nome_de_usuário* e o tipo de *cadeia de caracteres* e clique em **Enviar**. 

![][2]

3) Por fim, você verá este aplicativo-info registrado semelhante ao seguinte:

![][3]

##<a name="send-app-info-from-the-client-sdk"></a>Enviar informações de aplicativo do cliente do SDK

Aqui, estamos usando o exemplo de aplicativo Windows Universal mas métodos equivalentes existem para nossos outros SDKs também. 

Pressupondo que você tenha um método no aplicativo móvel onde obter as informações de perfil do usuário como seus nomes provavelmente depois de autenticá-los, você chamará `SendAppInfo` método aqui e preencher o valor da `user_name` informações de aplicativo que você anteriormente registrado no serviço móvel contrato back-end. 

    Dictionary<object, object> appInfo = new Dictionary<object, object>();
    appInfo.Add("user_name", str);
    EngagementAgent.Instance.SendAppInfo(appInfo); 

##<a name="send-personalized-notifications"></a>Enviar notificações personalizadas

Agora você está pronto para enviar notificações usando este **nome_de_usuário**. 

1) Acesse o Portal de contrato de celular na guia **atinja** para criar uma notificação e você pode usar esse espaço reservado no seguinte formato em qualquer lugar no título notificação ou corpo. 

![][4]  

> [AZURE.NOTE] Todos os usuários para o qual as informações de aplicativo nome_de_usuário não estiver definida, não receberá nenhuma notificação. Se você executar a campanha de notificação no modo de teste e se você não tiver o aplicativo-informações definido enviaremos '?' caractere para substituir o espaço reservado. 

2) Quando o contrato de celular selecionará um dispositivo para enviar essa notificação e em seguida, ele será examinar este aplicativo-info e substitua o valor no espaço reservado.  
Por exemplo, se tiver definimos `str = "Scott"` para um usuário que o dispositivo do registro obterá associado com as informações de aplicativo de **nome_de_usuário = SCOTT** para esse usuário e esse usuário verá uma notificação de envio do aplicativo no seguinte formato de ausência. 

![][5]  

<!-- Images. -->
[1]: ./media/mobile-engagement-send-personalized-notifications/app-info.png
[2]: ./media/mobile-engagement-send-personalized-notifications/create-app-info.png
[3]: ./media/mobile-engagement-send-personalized-notifications/app-info-user-name.png
[4]: ./media/mobile-engagement-send-personalized-notifications/personal-notification.png
[5]: ./media/mobile-engagement-send-personalized-notifications/notification.png

