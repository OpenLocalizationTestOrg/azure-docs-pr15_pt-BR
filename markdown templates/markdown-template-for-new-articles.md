<properties
   pageTitle="Título da página que exibe os resultados de tabulação e de pesquisa do navegador"
   description="Descrição de artigo que será exibida em páginas iniciais e na maioria dos resultados de pesquisa"
   services="service-name"
   documentationCenter="dev-center-name"
   authors="GitHub-alias-of-only-one-author"
   manager="manager-alias"
   editor=""/>

<tags
   ms.service="required"
   ms.devlang="may be required"
   ms.topic="article"
   ms.tgt_pltfrm="may be required"
   ms.workload="required"
   ms.date="mm/dd/yyyy"
   ms.author="Your MSFT alias or your full email address;semicolon separates two or more"/>

# <a name="markdown-template-article-heading-1-or-h1-heading-at-the-top-of-the-article"></a>Modelo de redução (artigo título 1 ou H1): título na parte superior do artigo

Para copiar a redução deste modelo, copie o artigo no seu local repo, ou clique no botão bruto na UI GitHub e copie a redução.

  ![Texto ALT; não deixe em branco. Descreva a imagem.][8]

Parágrafo de Introdução: Lorem dolor amet, adipiscin elit. Phasellus interdum nulla risus, lacinia porta nisl imperdiet sed. Mauris dolor mauris, tempus sed lacinia nec, felis não euismod. Nunc semper porta ultrices. Maecenas neque nulla, condimentum vitae ipsum ficam amet, dignissim aliquet nisi.

## <a name="heading-2-h2"></a>Título 2 (H2)

Aenean ficam amet leo nec purus placerat fermentum ac gravida odio. Aenean tellus lectus, faucibus em rhoncus in, faucibus sed urna.  volutpat mi id purus ultrices iaculis nec não neque. [Texto de link para link fora do azure.microsoft.com](http://weblogs.asp.net/scottgu). Dolor de dictum Nullam em aliquam pharetra. Vivamus ac hendrerit mauris [texto de link para um artigo em uma pasta de serviço do link de exemplo](../articles/expressroute/expressroute-bandwidth-upgrade.md).

Obter mais de 10 vezes tráfego do [Google]  [ gog] que do [Yahoo]  [ yah] ou [MSN] [msn].

> [AZURE.NOTE] Texto da nota recuado.  A palavra 'nota' será adicionada durante a publicação. Recortar UE pretium lacus. Nullam purus est, iaculis sed est nível, euismod vehicula odio. Curabitur lacinia, erat tristique iaculis rutrum, erat sem sodales nisi, da UE condimentum turpis nisi um purus.

1. Aenean ficam amet leo nec **Purus** placerat fermentum ac gravida odio.

2. Aenean tellus lectus, faucius em **Rhoncus** in, faucibus sed urna. Suspendisse volutpat mi id purus ultrices iaculis nec não neque.

    ![Texto ALT; não deixe em branco. Carro de coletor em corrida vermelho.][5]

3. Dolor de dictum Nullam em aliquam pharetra. Vivamus ac hendrerit mauris. Sed dolor dui, condimentum et varius a, vehicula em nisl.

    ![Texto ALT; não deixe em branco][6]


Suspendisse volutpat mi id purus ultrices iaculis nec não neque. Dolor de dictum Nullam em aliquam pharetra. Vivamus ac hendrerit mauris. Otrus informatus: [1 de Link para outro tópico de documentação azure.microsoft.com](virtual-machines-windows-hero-tutorial.md)

## <a name="heading-h2"></a>Título (H2)

Recortar UE pretium lacus. Nullam purus est, iaculis sed est nível, euismod vehicula odio.

1. Curabitur lacinia, erat tristique iaculis rutrum, erat sem sodales nisi, da UE condimentum turpis nisi um purus.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2. Vestibulum aposta ipsum primis em faucibus orci luctus et ultrices posuere cubilia.

        // Because toast alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }


    > [AZURE.NOTE] Duis sed diam não <i>nisl molestie</i> pharetra eget um est. [Link 2 para outro tópico de documentação azure.microsoft.com](web-sites-custom-domain-name.md)


Quisque commodo eros nível lectus euismod auctor eget ficam amet leo. Proin faucibus suscipit tellus dignissim ultrices.

## <a name="heading-2-h2"></a>Título 2 (H2)

1. Condimentum de sed Maecenas nisi. Suspendisse potenti.

  + Fusce
  + Malesuada
  + Sem

2. Nullam em massa da UE tellus tempus hendrerit.

    ![Texto ALT; não deixe em branco. Exemplo de um vídeo de 9 de canal.][7]

3. Quisque felis enim, fermentum Recortar aliquam nec, da magna de pulvinar pellentesque.




<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas

Vestibul aposta ipsum primis em faucibus orci luctus et ultrices posuere cubilia Curae; Nullam ultricies, ipsum vitae volutpat hendrerit, eros de pretium de diam purus, vitae tincidunt nulla lorem sed turpis: [3 de Link para outro tópico de documentação azure.microsoft.com](storage-whatis-account.md).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    
