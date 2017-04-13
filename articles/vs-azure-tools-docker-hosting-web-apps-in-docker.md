<properties
   pageTitle="Implantar um contêiner de ASP.NET Core Linux Docker para um host Docker remoto | Microsoft Azure"
   description="Saiba como usar o Visual Studio Tools for Docker para implantar o aplicativo web do ASP.NET Core em um contêiner de Docker em execução em uma máquina virtual Linux do Azure Docker Host"   
   services="azure-container-service"
   documentationCenter=".net"
   authors="mlearned"
   manager="douge"
   editor=""/>

<tags
   ms.service="azure-container-service"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/08/2016"
   ms.author="mlearned"/>

# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>Implantar um contêiner ASP.NET para um host Docker remoto

## <a name="overview"></a>Visão geral
Docker é um mecanismo de contêiner leve, semelhante em algumas maneiras de uma máquina virtual, que você pode usar para hospedar aplicativos e serviços.
Este tutorial o orienta usando a extensão do [Visual Studio Tools de 2015 para Docker](http://aka.ms/DockerToolsForVS) para implantar um aplicativo do ASP.NET Core um host Docker no Azure usando o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos
A seguir é necessário para concluir este tutorial:

- Criar uma máquina virtual Host do Azure Docker conforme descrito em [como usar máquina docker com o Azure](./virtual-machines/virtual-machines-linux-docker-machine.md)
- Instalar a [Atualização de 2015 do Visual Studio 3](https://go.microsoft.com/fwlink/?LinkId=691129)
- [SDK do Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)
- Instale o [Visual Studio 2015 Tools for Docker - visualização](http://aka.ms/DockerToolsForVS)

## <a name="1-create-an-aspnet-core-web-app"></a>1. criar o aplicativo web do ASP.NET Core
As etapas a seguir o guiará durante a criação de um aplicativo ASP.NET Core básico que será usado neste tutorial.

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. adicionar suporte de Docker

[AZURE.INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-use-the-dockertaskps1-powershell-script"></a>3. use o Script do PowerShell DockerTask.ps1 

1.  Abra um prompt de PowerShell para o diretório raiz do seu projeto. 

    ```
    PS C:\Src\WebApplication1>
    ```

1.  Validar remoto host está em execução. Você deve ver estado = execução 

    ```
    docker-machine ls
    NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
    MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
    ```

    > [AZURE.NOTE] Se você estiver usando a versão Beta Docker, seu host não será listado aqui.

1.  Compilar o aplicativo usando o - parâmetro de compilação

    ```
    PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
    ```  

    > [AZURE.NOTE] Se você estiver usando a versão Beta Docker, omita - máquina argumento
    > 
    > ```
    > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
    > ```  


1.  Executar o aplicativo, usando o parâmetro – executar

    ```
    PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
    ```

    > [AZURE.NOTE] Se você estiver usando a versão Beta Docker, omita - máquina argumento
    > 
    > ```
    > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
    > ```

    Quando docker for concluída, você verá os resultados similares ao seguinte:

    ![Exibir seu aplicativo][3]

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png
