<properties 
pageTitle="Manipular eventos de ciclo de vida do serviço de nuvem | Microsoft Azure" 
description="Saiba como os métodos de ciclo de vida de uma função de serviço de nuvem podem ser usados no .NET" 
services="cloud-services" 
documentationCenter=".net" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>

# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Personalizar o ciclo de vida de uma função da Web ou de trabalho no .NET

Quando você cria uma função de trabalho, você pode estender a classe de [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) que fornece métodos para substituição que permitem que você respondem a eventos de ciclo de vida. Para funções da web essa classe é opcional, portanto você deve usá-lo para responder a eventos de ciclo de vida.

## <a name="extend-the-roleentrypoint-class"></a>Estender a classe de RoleEntryPoint

A classe [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) inclui métodos chamados pelo Azure quando ele **for iniciado**, **executa**ou **interrompe** uma função da web ou de trabalho. Opcionalmente, você pode substituir esses métodos para gerenciar a inicialização de função, função desligamento sequências ou o segmento de execução da função. 

Ao estender **RoleEntryPoint**, você deve estar ciente dos seguintes comportamentos dos métodos:

-   Os métodos [OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) e [OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx) retornam um valor booliano, portanto, é possível retornar **false** entre esses métodos.

     Se seu código retorna **false**, o processo de função é encerrado abruptamente, sem executar qualquer sequência de desligamento, que talvez seja necessário no lugar. Em geral, você deve evitar retornando **false** do método **OnStart** .
     
-   Qualquer exceção não tratada dentro de uma sobrecarga de um método de **RoleEntryPoint** será tratada como uma exceção não tratada.

     Se ocorrer uma exceção dentro de um dos métodos do ciclo de vida, Azure dispare o evento [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) e, em seguida, o processo é encerrado. Depois que sua função foi colocada em offline, ele será reiniciado por Azure. Quando ocorre uma exceção não tratada, o evento de [interrupção](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx) não é gerado e o método **OnStop** não é chamado.

Se sua função não inicia ou é reciclagem entre os estados de parar e inicializar, ocupado, seu código pode ser lançar uma exceção não tratada dentro de um dos eventos de ciclo de vida reinicia da função de cada vez. Nesse caso, use o evento [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) para determinar a causa de exceção e tratá-lo adequadamente. Sua função também pode ser retornando do método [Executar](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) , que faz com que a função reiniciar. Para obter mais informações sobre estados de implantação, consulte [Problemas que causam funções comuns a Lixeira](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [AZURE.NOTE] Se você estiver usando as **Ferramentas do Azure para Microsoft Visual Studio** para desenvolver seu aplicativo, os modelos de projeto de função estendem automaticamente a classe **RoleEntryPoint** para você, nos arquivos *WebRole.cs* e *WorkerRole.cs* .

## <a name="onstart-method"></a>Método OnStart

O método **OnStart** é chamado quando sua instância de função é colocada online por Azure. Enquanto o código OnStart está em execução, a instância da função está marcada como **ocupado** e nenhum tráfego externo será direcionado para ele pelo Balanceador de carga. Você pode substituir esse método para realizar o trabalho de inicialização, como implementar manipuladores e iniciando o [Diagnóstico do Azure](cloud-services-how-to-monitor.md).

Se **OnStart** retorna **true**, a instância é inicializada com êxito e Azure chama o método **RoleEntryPoint.Run** . Se **OnStart** retornará **Falso**, a função encerra imediatamente, sem executar qualquer sequências de desligamento planejado.

O exemplo de código a seguir mostra como substituir o método **OnStart** . Este método configura e inicia um monitor de diagnóstico quando a instância da função inicia e configura uma transferência de dados de registro em log para uma conta de armazenamento:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>Método OnStop

O método **OnStop** é chamado após uma instância da função foi colocada em offline por Azure e antes do processo for encerrado. Você pode substituir esse método para chamar o código necessário para a sua instância de função para desligado corretamente.

> [AZURE.IMPORTANT] Código em execução no método **OnStop** tem um tempo limitado para concluir quando ele é chamado por razões diferente de um desligamento iniciado pelo usuário. Após esse tempo decorrido, o processo é finalizado, portanto, você deve garantir que o código no podem **OnStop** método executar rapidamente ou tolera não executando para conclusão. O método **OnStop** é chamado depois que o evento de **interrupção** é gerado.


## <a name="run-method"></a>Executar método

Você pode substituir o método **Executar** para implementar um thread de execução longa para sua instância de função.

Substituindo o método **Executar** não é necessário; a implementação padrão inicia um segmento que é suspenso uma eternidade. Se você substituir o método **execute** , seu código deve bloquear indefinidamente. Se o método **execute** retorna, a função é automaticamente normalmente reciclada; em outras palavras, o Azure gera o evento **Interrompendo** e chama o método de **OnStop** para que as sequências de desligamento podem ser executadas antes que a função é colocada offline.


### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementando os métodos de ciclo de vida do ASP.NET para uma função de web

Você pode usar os métodos de ciclo de vida do ASP.NET, além dos fornecidos pela classe **RoleEntryPoint** , gerenciar inicialização e sequências de desligamento para uma função web. Se você estiver transferindo um aplicativo ASP.NET existente para o Azure, isso pode ser útil para fins de compatibilidade. Os métodos de ciclo de vida do ASP.NET são chamados de dentro do método **RoleEntryPoint** . O **aplicativo\_iniciar** método é chamado após concluir o método **RoleEntryPoint.OnStart** . O **aplicativo\_final** método é chamado antes do método **RoleEntryPoint.OnStop** é chamado.

## <a name="next-steps"></a>Próximas etapas
Saiba como [criar um pacote de serviço de nuvem](cloud-services-model-and-package.md).