<properties 
   pageTitle="A depuração de serviços de nuvem Azure | Microsoft Azure"
   description="Depuração de serviços de nuvem do Azure"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="debugging-cloud-services"></a>Depuração de serviços de nuvem

Você pode usar diferentes abordagens para depurar um aplicativo do Azure usando as ferramentas do Azure para Microsoft Visual Studio e o SDK do Azure:

- Você pode depurar um aplicativo do Azure do Visual Studio quando você está desenvolvendo, assim como faria com qualquer aplicativo Visual c# ou Visual Basic. Para obter mais informações, consulte [depurar seu serviço de nuvem em seu computador local](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).

- Você pode usar o diagnóstico do Azure para registrar informações detalhadas de código em execução dentro de funções, se as funções estiverem executando no ambiente de desenvolvimento ou no Azure. Para obter mais informações, consulte [log de coletando dados usando o diagnóstico do Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).

- Se você estiver usando o Visual Studio Enterprise para escrever funções direcionadas para o .NET Framework 4 ou do .NET Framework 4.5, você pode habilitar IntelliTrace no momento em que você implantar um serviço de nuvem Azure do Visual Studio. IntelliTrace fornece um log que você pode usar com o Visual Studio para depurar seu aplicativo, como se fosse executado no Azure. Para obter mais informações, consulte [depuração de um serviço de nuvem publicados com IntelliTrace e Visual Studio]( http://go.microsoft.com/fwlink/p/?LinkId=623016).

- Você pode habilitar a depuração remota em seus serviços de nuvem no momento quando você implanta o serviço de nuvem do Visual Studio. Se você optar por habilitar depuração remota para uma implantação, serviços de depuração remota são instalados nas máquinas virtuais que executam cada instância de função. Esses serviços, como msvsmon.exe, não afetam o desempenho ou resultam em custos extras. Para obter mais informações, consulte [depuração de um serviço de nuvem no Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).



