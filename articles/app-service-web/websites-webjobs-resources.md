<properties 
    pageTitle="Recursos de documentação do Azure WebJobs" 
    description="Recursos recomendados para aprender como usar WebJobs do Azure e o SDK do Azure WebJobs." 
    services="app-service" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="tdykstra"/>

# <a name="azure-webjobs-documentation-resources"></a>Recursos de documentação do Azure WebJobs

## <a name="overview"></a>Visão geral

Este tópico fornece links para recursos de documentação sobre como usar WebJobs do Azure e o SDK do Azure WebJobs. WebJobs Azure fornecem uma maneira fácil de executar scripts ou programas como processos de plano de fundo no contexto de um [aplicativo de serviço web app, API aplicativo ou aplicativo móvel](../app-service/app-service-value-prop-what-is.md). Você pode carregar e executar um arquivo executável como como cmd, bat, exe (.NET), ps1, MOS, php, Aj, js e jar. Esses programas executados como WebJobs em um cronograma (cron) ou continuamente.

A finalidade do [WebJobs SDK](websites-webjobs-resources.md) é simplificar o código que você escreve para tarefas comuns que um WebJob pode realizar, como o processamento de imagens, fila de processamento, agregação RSS, manutenção de arquivo e enviar emails. O SDK do WebJobs possui recursos internos para trabalhar com o armazenamento do Azure e barramento de serviço, para agendamento de tarefas e tratamento de erros e para muitos outros cenários comuns. Além disso, foi projetada para ser extensível e não há um [repositório de origem para extensões de abrir](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview). [Funções do Azure](../azure-functions/functions-overview.md) (atualmente em preview) se baseia em uma versão do SDK WebJobs que funciona com c# script, node e outros idiomas. 

Criar, implantar e gerenciar WebJobs são perfeita com ferramentas integradas no Visual Studio. Você pode criar WebJobs a partir de modelos, publicar e gerenciar (executar/parar/monitor/depurar)-los. 

O painel de WebJobs no portal do Azure fornece recursos de gerenciamento poderosos que dão controle total sobre a execução de WebJobs, incluindo a capacidade de invocar funções individuais dentro de WebJobs. O painel também exibe a saída de log e tempos de execução de função. 

##<a name="getstarted"></a>Introdução ao WebJobs e o SDK do WebJobs

* [Introdução ao Azure WebJobs](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [WebJobs Azure são impressionantes e você deve começar a usá-los agora!](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html) (Blog post por Troia busca).
* [Recursos do Azure WebJobs](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [O que é o SDK do WebJobs](websites-dotnet-webjobs-sdk.md)
* [Orientação de trabalhos de plano de fundo por padrões e práticas Microsoft](/documentation/articles/best-practices-background-jobs/)
* [Anunciando o 1.1.0 RTM do Microsoft Azure WebJobs SDK](/blog/azure-webjobs-sdk-1-1-0-rtm/)
* [Introdução ao SDK do Azure WebJobs](websites-dotnet-webjobs-sdk-get-started.md)
* [Como usar o armazenamento do Azure fila com o SDK do WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Como usar o armazenamento de blob do Microsoft Azure com o SDK do WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Como usar o armazenamento de tabela Azure com o SDK do WebJobs](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Como usar o barramento de serviço do Azure com o SDK do WebJobs](websites-dotnet-webjobs-sdk-service-bus.md)
* [Como usar WebHooks com o SDK do WebJobs, com exemplos para GitHub, IFTTT e HTTP](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/WebHooks-Walkthrough)
* [Azure WebJobs SDK referência rápida (download PDF)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* [Documentação de configurações de WebJobs em GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* Vídeos
    * [WebJobs e o SDK do WebJobs](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
    * [Série de vídeos de WebJobs Azure no canal 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
    * [Introdução ao WebJobs ferramentas para Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
    * [Ferramentas de WebJobs e depuração remota](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)
    * [Atualização de WebJobs Azure com Pranav Rastogi - extensibilidade na versão 1.1](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-183-Azure-WebJobs-Update-with-Pranav-Rastogi)

Consulte também as seções a seguir no [WebJobs Implantando](#deploy) e [teste e depuração WebJobs](#debug).

##<a name="deploy"></a>Implantando WebJobs

* [Como implantar o Azure WebJobs usando o Visual Studio](websites-dotnet-deploy-webjobs.md)
* [Como implantar WebJobs usando o Portal do Azure](web-sites-create-web-jobs.md)
* [Permitindo a entrega de linha de comando ou contínua de WebJobs Azure](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Gito implantar um aplicativo de console do .NET para Azure usando WebJobs](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [Implantando um WebJob F # no Azure](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx)
* [Implantando serviços personalizados como Webjobs do Azure](http://withouttheloop.com/articles/2015-06-23-deploying-custom-services-as-azure-webjobs/)
* Vídeos
    * [Introdução ao WebJobs ferramentas para Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
    * [Ferramentas de WebJobs e depuração remota](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="schedule"></a>Agendamento WebJobs

* [A caixa de diálogo do Azure WebJob adicionar](websites-dotnet-deploy-webjobs.md#configure)
* [Criar uma WebJob agendada no Portal do Azure](web-sites-create-web-jobs.md#CreateScheduled)
* [Conectar um trabalho de Agendador para uma WebJob](http://blog.davidebbo.com/2015/05/scheduled-webjob.html)
* [Agendamento Azure WebJobs com expressões cron](http://blog.amitapple.com/post/2015/06/scheduling-azure-webjobs/)
* [Funções WebJob individuais usando o WebJobs SDK TimerTrigger de agendamento](websites-dotnet-webjobs-sdk.md#schedule)

##<a name="debug"></a>Teste e depuração WebJobs

* [Novo desenvolvedor e recursos de depuração para WebJobs Azure no Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Exibir o painel de WebJobs](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [Como escrever logs usando o SDK do WebJobs e exibi-los no painel de controle](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [WebJobs depuração remota](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [Quem criou blob?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Código de interativo hospedagem na nuvem](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Adicionando rastreamento a WebJobs Azure](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Monitorar, diagnosticar e solucionar problemas de armazenamento do Microsoft Azure](../storage/storage-monitoring-diagnosing-troubleshooting.md)
* Vídeos
    * [Ferramentas de WebJobs e depuração remota](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="scale"></a>Dimensionamento WebJobs

* [Dimensionamento de seu aplicativo Web com sites do Azure](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Serviço de aplicativo do azure: arquitetura de aplicativos Web de pronto para empresas de grande escala](https://channel9.msdn.com/Events/Build/2014/3-626). Capas dimensionamento dos aplicativos web com WebJobs, incluindo o SDK do WebJobs.
* Vídeos
    * [Dimensionamento WebJobs](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

##<a name="additional"></a>Recursos adicionais de WebJobs

* [Postagem de blog WebJobs GA Azure por Magnus Mårtensson](http://magnusmartensson.com/azure-webjobs-ga)
* [Executando trabalhos de Web do Powershell no serviço de aplicativo do Azure](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Planilha de recursos quando o Azure disparada WebJobs conclui](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Política de retenção de Backup de aplicativo Web Simple com WebJobs](https://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Azure Web Apps e serviços de nuvem lenta na primeira solicitação](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Mostra como usar WebJobs para simular o recurso AlwaysOn que só está disponível para a camada de preços padrão.
* [Desligamento WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Desligamento para WebJobs SDK, consulte [desligamento](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).)
* [Automatizar Backups com Azure WebJobs & AzCopy](http://markjbrown.com/azure-webjobs-azcopy/)
* Vídeos
    * [Vídeos de WebJobs Azure por Magnus Mårtensson](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
    * [Série de vídeos de WebJobs Azure no canal 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>Recursos adicionais de WebJobs SDK

* [Notas da versão WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes)
* [Código-fonte WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk)
* [Extensões de WebJobs SDK código-fonte](https://github.com/Azure/azure-webjobs-sdk-extensions), com [a guia detalhado para o modelo de extensibilidade](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).  
* [Código-fonte WebJobs SDK Script](https://github.com/Azure/azure-webjobs-sdk-script/) (usado para [Funções do Azure](../azure-functions/functions-overview.md))
* [WebJob para carregar arquivos FREB para o armazenamento do Azure usando o SDK do WebJobs](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Hospedagem Azure webjobs fora do Azure, com os benefícios de log de um Azure hospedado webjob](http://bypassion.dk/?p=510)
* [Criando uma ferramenta de importação de dados com o Azure WebJobs](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* [Visão geral de funções Azure](../azure-functions/functions-overview.md)
* Vídeos
    * [Série de vídeos de WebJobs Azure no canal 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>Aplicativos de WebJob de exemplo

* [Aplicativos de amostra fornecidos pela equipe de WebJobs no GitHub](https://github.com/azure/azure-webjobs-sdk-samples)
* [Simples Azure Web App com WebJobs Backend usando o SDK do WebJobs](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Demonstra o uso de WebJobs programadas e controlada por evento. Consulte o blog postar [recriando o SiteMonitR usando o SDK do Azure WebJobs](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

##<a name="blogs"></a>Blogs

* [Blog do Azure](/blog)
* [Blog de Amit da Apple](http://blog.amitapple.com/). Se concentra em WebJobs (não o SDK).
* [Blog de Magnus Mårtensson](http://magnusmartensson.com/)

##<a name="gethelp"></a>Obtendo ajuda com WebJobs

* [StackOverflow para WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow para o SDK WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [StackOverflow para funções do Azure](http://stackoverflow.com/questions/tagged/azure-functions)
* [Fórum do Azure e ASP.NET](http://forums.asp.net/1247.aspx)
* [Azure Fórum do aplicativo de serviço Web Apps](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Site de voz de usuário de aplicativos Web Azure](https://feedback.azure.com/forums/169385-websites/)
* [Twitter](http://twitter.com/). Use o hashtag #AzureWebJobs.
* [Reportar um problema ou bug WebJobs](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues)

