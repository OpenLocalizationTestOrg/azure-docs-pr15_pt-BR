<properties
    pageTitle="Azure Mobile contrato iOS notas de versão do SDK | Microsoft Azure"
    description="Últimas atualizações e procedimentos para iOS SDK para contrato de celular do Azure"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="piyushjo" />

#<a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Notas de versão do Azure Mobile contrato iOS SDK

##<a name="400-09122016"></a>4.0.0 (12/09/2016)

-   Notificação fixa não actioned em dispositivos iOS 10.
-   Reprovar XCode 7.

##<a name="324-06302016"></a>3.2.4 (30/06/2016)

-   Agregação fixa entre logs técnicos e outras.

##<a name="323-06072016"></a>3.2.3 (06/07/2016)

-   Corrigir o erro onde comentários de entrega não é informado quando o aplicativo está no plano de fundo.
-   Otimizado o envio de logs técnicos.

##<a name="322-04072016"></a>3.2.2 (04/07/2016)

-   Bug corrigido em cancelamento de solicitação HTTP que às vezes leva falhe.

##<a name="321-12112015"></a>3.2.1 (12/11/2015)

-   Corrigido o atraso quando uma nova instância do aplicativo é disparada por uma notificação com links profunda

##<a name="320-10082015"></a>3.2.0 (10/08/2015)

-   Habilitado Bitcode no SDK para torná-lo a trabalhar com **Xcode 7**.
-   Bugs corrigidos relacionados a notificações do aplicativo.
-   As notificações do aplicativo tornou mais confiável no caso de bateria baixa e outros esses cenários.
-   Removido logs de console extra gerados pelo 3º biblioteca de terceiros.

##<a name="310-08262015"></a>3.1.0 (26/08/2015)

-   Corrigi o erro de compatibilidade do iOS 9 com uma biblioteca de terceiros. Ela foi causando falhas enquanto enviando controla os resultados, informações de aplicativo ou dados extras.

##<a name="300-06192015"></a>3.0.0 (19/06/2015)

-   Contrato de celular usa silencioso notificações Push.
-   Descartados suporte para iOS 4. x. Iniciando a partir desta versão de destino de implantação do seu aplicativo deve ter pelo menos iOS 6.

##<a name="220-05212015"></a>2.2.0 (21/05/2015)

-   A id do dispositivo móvel contrato para dispositivos < iOS 6 agora baseado em um GUID gerado no momento da instalação.

##<a name="210-04242015"></a>2.1.0 (24/04/2015)

-   Compatibilidade de rápida adicionada.
-   Ao clicar em uma notificação, a ação que URL agora é executado direita depois que o aplicativo é aberto.
-   Arquivo de cabeçalho ausentes adicionado no pacote SDK.
-   Corrigido um problema quando o gerador de relatórios de falha de contrato Mobile foi desabilitado.

##<a name="200-02172015"></a>2.0.0 (17/02/2015)

-   Versão inicial do Azure contrato móvel
-   configuração de appId/sdkKey é substituída por uma configuração de cadeia de conexão.
-   Removido API para enviar e receber mensagens XMPP aleatório de entidades XMPP aleatório.
-   Removido API para enviar e receber mensagens entre dispositivos.
-   Aprimoramentos de segurança.
-   Acompanhamento de SmartAd removido.
