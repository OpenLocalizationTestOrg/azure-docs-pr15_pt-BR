<properties
    pageTitle="WebJobs no serviço de aplicativo do Azure"
    description="Aprenda a criar WebJobs para executar testes de plano de fundo, interagir com os serviços, como armazenamento e barramento de serviço e crie tarefas agendadas."
    services="app-service"
    documentationCenter=""
    authors="christopheranderson"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/10/2015"
    ms.author="chrande"/>

# <a name="using-webjobs-in-azure-app-service"></a>Usando WebJobs no serviço de aplicativo do Azure

Este artigo se refere a recursos de documentação sobre como usar WebJobs do Azure e o SDK do Azure WebJobs. WebJobs Azure fornecem uma maneira fácil de executar scripts ou programas como processos de plano de fundo no [Aplicativo de serviço Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Você pode carregar e executar um arquivo executável como como cmd, bat, exe (.NET), ps1, MOS, php, Aj, js e jar. Esses programas executados como WebJobs em um cronograma (cron) ou continuamente.

O SDK do WebJobs torna mais fácil de usar o armazenamento do Azure. O SDK do WebJobs tem uma vinculação e um sistema de disparador que funciona com o Microsoft Azure armazenamento Blobs, filas e tabelas bem como filas de barramento de serviço.

Criar, implantar e gerenciar WebJobs são perfeita com ferramentas integradas no Visual Studio. Você pode criar WebJobs a partir de modelos, publicar e gerenciar (executar/parar/monitor/depurar)-los.

O painel de WebJobs no portal do Azure fornece recursos de gerenciamento poderosos que dão controle total sobre a execução de WebJobs, incluindo a capacidade de invocar funções individuais dentro de WebJobs. O painel também exibe a saída de log e tempos de execução de função.

[AZURE.INCLUDE [app-service-blueprint-webjobs](../../includes/app-service-blueprint-webjobs.md)]
