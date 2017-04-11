<properties
    pageTitle="Comparando Azure Mobile contrato com outros serviços do Azure semelhantes"
    description="Comparando Azure Mobile contrato com outros serviços Azure semelhantes - HockeyApp, AppInsights, Hubs de notificação"
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
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="comparing-azure-mobile-engagement-with-other-similar-azure-services"></a>Comparando Azure Mobile contrato com outros serviços do Azure semelhantes

Na lista de serviços oferecidos pelo Microsoft Azure nunca é expandindo e às vezes você deve estar imaginando como é Azure Mobile contrato diferente esse serviço que você acabou de ler ou ouve sobre. Este artigo tenta limpar a confusão e direciona você escolha Azure Mobile contrato quando ele é mais apropriado para seu uso. 
 
Contrato de celular Azure é um serviço direcionado especificamente **para comerciantes/CMOs digitais** , mas pode ser usado por qualquer **proprietário do aplicativo móvel ou publisher** que deseja aumentar a utilização, retenção e monetização dos seus aplicativos móveis. 

*É uma plataforma de usuário-contrato do software como serviço (SaaS) que fornece orientadas a dados ideias para uso do aplicativo, segmentação de usuário em tempo real e permite notificações por push contextualmente reconhecimento e do aplicativo de mensagens.* 

Dividir essa definição, temos as seguintes características principais que também destaca sua proposta de valor exclusivo:

1.  **Notificações por push contextualmente reconhecimento e do aplicativo de mensagens**
        
    Este é o foco principal do produto - executar notificações por push orientada e personalizada. E para isso acontecer, podemos coletar dados de análise avançada de comportamento. 

2.  **Orientados a dados ideias para uso do aplicativo**

    Fornecemos entre plataformas SDKs para coletar a análise de comportamento sobre os usuários do aplicativo. Observe a análise de comportamento de termo (como a análise de desempenho) porque vamos nos concentrar em como os usuários de aplicativo estão usando o aplicativo. Coletamos dados de análise de desempenho básico sobre erros, falhas etc mas isso é não o foco principal do produto. 

3.  **Segmentação de usuário em tempo real**

    Depois que você tenha coletado dados de análise de comportamento de usuários do aplicativo, podemos permitem segmento sua audiência com base em vários parâmetros e coletar dados para que você possa executar campanhas de envio de destino. 

4.  **Software-como um serviço (SaaS):**

    Temos um portal separado do Azure portal de gerenciamento do que é otimizado para interagir e visualize a análise avançada de comportamento sobre os usuários do aplicativo e executar push campanhas de marketing. O produto está voltado para você começar em nenhum tempo!   
 
Com esse conjunto de diferenciação em mãos, veja aqui como podemos comparar com outras ofertas Azure visivelmente semelhantes - Observe que o artigo não faz uma comparação de nível de recurso, mas leva um cenário mais abordagem para definir qual produto funciona melhor com base em:
 
1.  [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) é a solução de DevOps móveis da Microsoft. Com ele, você pode distribuir compilações para testadores beta, coletar dados de falha e obter comentários do usuário. Ele também integra-se com o Visual Studio Team Services habilitando implantações de compilação fácil e integração de item de trabalho. 
    
    Aqui, o foco está em DevOps e coletar dados de análise de desempenho sobre os aplicativos móveis. Você pode acabar com a integração entre os dois HockeyApps e contrato de Mobile em seu aplicativo e que não serão incomum porque, embora haja alguma sobreposição os dados coletados, o foco principal dos produtos é diferente e ajudam a eles objetivos diferentes para você.  

2.  [Obtenção de informações de aplicativo](../application-insights/app-insights-overview.md) Se seu aplicativo móvel tem um lado do servidor, em seguida, você usará ideias de aplicativo para monitorar o lado do seu aplicativo de servidor de web, mas para os aplicativos de móveis do lado do cliente, você deve usar HockeyApp. 

3.  [Notificação Hubs](https://azure.microsoft.com/services/notification-hubs/) fornece um serviço leve no sentido de que você não precisa de um SDK integrado no aplicativo móvel e você pode ter controle total do aplicativo móvel e ele permite o envio de notificações de envio com recursos básicos de marcação. Isso é ótimo para qualquer proprietário de aplicativo que se preocupar menos sobre direcionamento e mais informações de envio/comunicar-se instantaneamente para seus usuários de aplicativo (difusão a uma população grande). 

    Observe o foco sobre envio mais rápidas notificações com recurso de segmentação básicas. 

Vamos alguns cenários:

1.  Tim faz parte da equipe de marketing do digital em loja Adventure Works que publica aplicativos móveis para os usuários. Objetivo de Tim é garantir que os usuários que baixar seus aplicativos móveis continuem a usá-lo e com frequência. Esse aumenta não apenas uma marca anexar com os usuários do aplicativo, mas também aumenta monetização quando os usuários do aplicativo fazem compras usando o aplicativo móvel. Para isso Tim precisará enviar notificações direcionadas para os usuários do aplicativo, que eles útil, incentivá-los para abrir o aplicativo e voltar a ele com frequência. Isso é onde Tim será útil contrato de celular. 

2.  Joann faz parte da equipe de desenvolvimento de aplicativos móveis da Adventure Works e está procurando um produto registrar detalhes sobre falhas, exceções, e obter telemetria de desempenho de um aplicativo. Isso é onde Joann será útil HockeyApp. Joann pode integrar HockeyApp para seu desenvolvedor com foco em cenários tanto contrato de celular do Azure para Tim no mesmo aplicativo para obter o melhor de ambos. 

3.  Robin faz parte da equipe de desenvolvimento de aplicativos móveis na rede de notícias da Contoso e todas que ela quer é enviar alertas de notícias à ruptura para todos os usuários sem muito segmentação assim que o alerta de notícias é disparado. Isso é onde Robin será útil Hubs de notificação. Neste cenário, é possível no entanto que, como o aplicativo móvel amadurece, há um requisito fazer muito mais sofisticada segmentação e obter detalhes sobre o aplicativo comportamento do usuário. No momento, será necessário Robin avaliar Azure Mobile contrato. 
 
Recapitulando, a finalidade do contrato Mobile não está apenas para coletar analytics - não é "outro produto de análise da Microsoft". É sobre o envio de notificações por push alvo e para este direcionamento, podemos coletar dados de comportamento de análise, mas o foco permanece sobre o envio de notificações de envio que fazem mais sentido para os usuários do aplicativo para que ela não vir como spam. 

Para obter mais detalhes - dê uma olhada este [vídeo rápido](mobile-engagement-overview.md) sobre o contrato de Mobile em poucas palavras. 

