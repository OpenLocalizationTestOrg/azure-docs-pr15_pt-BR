

1. No seu Mac, inicie o **Acesso às chaves**. Abra **Meus certificados** em **categoria** na barra de navigationn à esquerda. Localize o certificado SSL que você baixou na seção anterior e divulgar seu conteúdo. Selecione apenas o certificado (não selecione a chave privada) e [exportá-lo](https://support.apple.com/kb/PH20122?locale=en_US).

2. No [portal do Azure](https://portal.azure.com/), clique em **Procurar tudo** > **Os serviços de aplicativo** > seu back-end do aplicativo Mobile. Em **configurações**, clique em **Envio de serviço de aplicativo**, clique em seu nome de hub de notificação. Vá para **os serviços de notificação por Push Apple** > **carregar certificado**. Carregue o arquivo. p12, selecionando o correto **modo** (dependendo se o seu cliente SSL certificado anteriores é produção ou área restrita). Salve as alterações.

Seu serviço está configurado para trabalhar com as notificações por push IOS!

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
