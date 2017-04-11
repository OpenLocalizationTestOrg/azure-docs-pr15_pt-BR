<properties
   pageTitle="Solucionando problemas com o rastreamento de eventos | Microsoft Azure"
   description="Os maioria dos problemas comuns encontrados ao implantar os serviços do Microsoft Azure serviço tecidos."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/31/2016"
   ms.author="mattrow"/>


# <a name="troubleshoot-common-issues-when-you-deploy-services-on-azure-service-fabric"></a>Solucionar problemas comuns quando você implantar os serviços do Azure serviço tecidos

Quando você estiver executando os serviços em seu computador de desenvolvedor, é fácil de usar [Ferramentas de depuração do Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Para clusters remotos, [relatórios de integridade](service-fabric-view-entities-aggregated-health.md) são sempre um bom lugar para começar. São as maneiras mais fácil de acessar esses relatórios por meio do PowerShell ou [SFX](service-fabric-visualizing-your-cluster.md). Este artigo pressupõe que você está depuração um cluster remoto e ter uma compreensão básica de como usar essas ferramentas.

##<a name="application-crash"></a>Falha de aplicativo
O "partição está abaixo contagem de réplica ou instância de destino" relatório é uma boa indicação que seu serviço está travando. Para descobrir onde seu serviço está travando um pouco leva mais investigação. Quando seu serviço é executado em escala, seu melhor amigo será um conjunto de rastreamentos bem elaborados-out.  Sugerimos que você tente [Diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-wad.md) para coletar esses rastreamentos e usando uma solução como [Pesquisar elástica](service-fabric-diagnostic-how-to-use-elasticsearch.md) para visualização e pesquisando os rastreamentos.

![Integridade de partição SFX](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

###<a name="during-service-or-actor-initialization"></a>Durante a inicialização de serviço ou ator
As exceções antes do tipo de serviço for inicializado fará com que o processo falhe. Para esses tipos de falhas, o log de eventos do aplicativo mostrará o erro de seu serviço.
Estas são as exceções mais comuns para ver antes do serviço for inicializado.

***System.IO.FileNotFoundException***

Esse erro geralmente é devido a Faltam dependências de assembly. Verifique a propriedade CopyLocal no Visual Studio ou no cache global de assembly para o nó.

***System.Runtime.InteropServices.COMException***
 *em System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory (IntPtr, IFabricStatefulServiceFactory)*
 
 Isso indica que o nome do tipo de serviço registrado não coincidir com o manifesto de serviço.

[Diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-wad.md) podem ser configurados para carregar o log de eventos do aplicativo para todos os seus nós automaticamente.

###<a name="runasync-or-onactivateasync"></a>RunAsync() ou OnActivateAsync()
Se o travamento acontece durante a inicialização ou executando do seu tipo de serviço registrado ou ator, a exceção será identificada pelo tecidos de serviço do Azure. Você pode exibir essas dos provedores EventSource detalhados na seção "Próximos passos".

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre diagnóstico existente fornecido pelo serviço tecidos:

* [Diagnóstico de atores confiável](service-fabric-reliable-actors-diagnostics.md)
* [Diagnóstico de serviços confiável](service-fabric-reliable-services-diagnostics.md)
