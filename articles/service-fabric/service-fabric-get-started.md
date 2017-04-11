<properties
   pageTitle="Configurar o ambiente de desenvolvimento | Microsoft Azure"
   description="Instale o tempo de execução, SDK e ferramentas e criar um cluster de desenvolvimento local. Depois de concluir essa configuração, você estará pronto para criar aplicativos."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/26/2016"
   ms.author="ryanwi"/>

# <a name="prepare-your-development-environment"></a>Preparar o ambiente de desenvolvimento

> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

 Para criar e executar [aplicativos do Azure serviço tecidos] [ 1] em sua máquina de desenvolvimento, instale o tempo de execução, SDK e ferramentas. Você também precisa habilitar a execução dos scripts do Windows PowerShell incluído no SDK.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="supported-operating-system-versions"></a>Versões de sistema operacional compatível
As seguintes versões de sistema operacional têm suporte para desenvolvimento:

- Windows 7
- Windows 8/Windows 8.1
- Windows Server 2012 R2
- Windows 10

>[AZURE.NOTE] Windows 7 inclui apenas o Windows PowerShell 2.0 por padrão. Cmdlets do PowerShell do serviço tecidos requer PowerShell 3.0 ou superior. Você pode [baixar o Windows PowerShell 5.0] [ powershell5-download] da Microsoft Download Center.

## <a name="install-the-runtime-sdk-and-tools"></a>Instalar o tempo de execução, SDK e ferramentas

O Web Platform Installer oferece duas configurações para desenvolvimento de estrutura de serviço:

- [Instalar o tempo de execução do serviço tecidos, SDK e ferramentas para Visual 2015 Studio (requer a atualização do Visual Studio 2015 2 ou posterior)][full-bundle-vs2015]
- [Instalar o tempo de execução do serviço tecidos e SDK somente (sem ferramentas do Visual Studio)][core-sdk]

## <a name="enable-powershell-script-execution"></a>Habilitar a execução de scripts do PowerShell

Estrutura de serviço usa scripts do Windows PowerShell para criar um cluster de desenvolvimento local e para implantar aplicativos do Visual Studio. Por padrão, o Windows bloqueia esses scripts sejam executados. Para ativá-los, você deve modificar sua política de execução do PowerShell. Abra o PowerShell como administrador e digite o seguinte comando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>Próximas etapas
Agora que você terminou de configurar seu ambiente de desenvolvimento, começa a construção e a execução de aplicativos.

- [Criar seu primeiro aplicativo de serviço tecidos no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
- [Saiba como implantar e gerenciar aplicativos em seu cluster local](service-fabric-get-started-with-a-local-cluster.md)
- [Saiba mais sobre os modelos de programação: serviços confiáveis e atores confiável](service-fabric-choose-framework.md)
- [Confira os exemplos de código de estrutura de serviço no GitHub](https://aka.ms/servicefabricsamples)
- [Visualizar seu cluster usando o serviço tecidos Explorer](service-fabric-visualizing-your-cluster.md)
- [Siga o caminho de aprendizagem de estrutura de serviço para obter uma introdução abrangente à plataforma](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Página de campanha de estrutura de serviço"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Link de VS WebPI de 2015"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Link de Dev15 WebPI"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Link de SDK WebPI Core"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
