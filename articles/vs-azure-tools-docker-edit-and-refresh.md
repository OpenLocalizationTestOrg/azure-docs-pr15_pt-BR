<properties
   pageTitle="Depuração de aplicativos em um contêiner de Docker local | Microsoft Azure"
   description="Saiba como modificar um aplicativo que está em execução em um contêiner de Docker local, atualize o contêiner por meio de editar e atualizar e definir pontos de interrupção de depuração"
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/22/2016"
   ms.author="mlearned" />

# <a name="debugging-apps-in-a-local-docker-container"></a>Depuração de aplicativos em um contêiner de Docker local

## <a name="overview"></a>Visão geral
O Visual Studio Tools for Docker oferece uma maneira consistente para desenvolver em um e validar seu aplicativo localmente em um contêiner de Linux Docker.
Você não precisa reiniciar o contêiner sempre que você alterar um código.
Este artigo mostrará como usar o recurso de "Editar e atualizar" para iniciar um aplicativo Web do ASP.NET Core em um contêiner de Docker local, faça as alterações necessárias e, em seguida, atualize o navegador para ver essas alterações.
Ele também mostrará como definir pontos de interrupção para depuração.

> [AZURE.NOTE] Suporte de contêiner Windows estará disponível em uma versão futura

## <a name="prerequisites"></a>Pré-requisitos
As seguintes ferramentas precisam estar instalado.

- [Atualização de 2015 do Visual Studio 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- Instalar a [Atualização de 2015 do Visual Studio 3](https://go.microsoft.com/fwlink/?LinkId=691129)
- [SDK do Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)

Para executar contêineres de Docker localmente, será necessário um cliente docker local.
Você pode usar lançadas [Docker ferramentas](https://www.docker.com/products/overview#/docker_toolbox) que requer Hyper-V para desativado, ou você pode usar [Docker para Windows Beta](https://beta.docker.com) que usa Hyper-V e requer o Windows 10.

Se usando ferramentas de Docker, você precisará [Configurar o cliente de Docker](./vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. Crie um aplicativo web

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. adicionar suporte de Docker

[AZURE.INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]


## <a name="3-edit-your-code-and-refresh"></a>3. editar seu código e atualização

Para rapidamente iteramos alterações, você pode iniciar o aplicativo dentro de um contêiner e continuar a fazer alterações, visualizá-las como faria com IIS Express.

1. Defina a configuração da solução como `Debug` e pressione ** &lt;CTRL + F5 >** construir sua imagem de docker e executá-lo localmente.

    Depois que a imagem contêiner foi criada e é executado em um contêiner de Docker, o Visual Studio iniciará o aplicativo da Web no seu navegador padrão.
    Se você estiver usando o navegador Microsoft Edge ou caso contrário ter erros, consulte a seção [solução de problemas](vs-azure-tools-docker-troubleshooting-docker-errors.md) .

1. Vá para a página sobre, que é onde vamos fazer nossas alterações.

1. Retornar ao Visual Studio e abra `Views\Home\About.cshtml`.

1. Adicione o seguinte conteúdo HTML para o final do arquivo e salvar as alterações.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```

1.  Exibindo a janela de saída, quando a compilação de .NET é concluída e ver essas linhas, volte ao seu navegador e atualize a página sobre.

    ```
    Now listening on: http://*:80
    Application started. Press Ctrl+C to shut down
    ```

1.  Suas alterações foram aplicadas!

## <a name="4-debug-with-breakpoints"></a>4. depurar com pontos de interrupção

Muitas vezes, alterações deverão ainda mais inspeção, aproveitando os recursos de depuração do Visual Studio.

1.  Retornar ao Visual Studio e abrir`Controllers\HomeController.cs`

1.  Substitua o conteúdo do método About() com o seguinte:

    ```
    string message = "Your application description page from wthin a Container";
    ViewData["Message"] = message;
    ````

1.  Definir um ponto de interrupção à esquerda da `string message`… linha.

1.  Acertar ** &lt;F5 >** para iniciar a depuração.

1.  Navegue até a página sobre ao seu ponto de interrupção.

1.  Alterne para o Visual Studio para exibir o ponto de interrupção e inspecionar o valor da mensagem.

    ![][2]

##<a name="summary"></a>Resumo

Com o [Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS), você pode obter a produtividade do trabalho localmente, com o realismo de produção de desenvolvimento dentro de um contêiner de Docker.

## <a name="troubleshooting"></a>Solução de problemas

[Visual Studio Docker desenvolvimento de solução de problemas](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Saiba mais sobre Docker com o Visual Studio, Windows e do Azure

- [Ferramentas de docker para Visual Studio](http://aka.ms/dockertoolsforvs) - desenvolvendo o seu código .NET Core em um contêiner
- [Ferramentas de docker para Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) - construa e implante contêineres de docker
- [Ferramentas de docker para Visual Studio código](http://aka.ms/dockertoolsforvscode) - serviços de idioma para edição de arquivos de docker, com mais situações e2e chegando
- [Informações do contêiner Windows](http://aka.ms/containers)- Windows Server e informações do servidor de Nano
- [Serviço de contêiner Azure](https://azure.microsoft.com/services/container-service/) - [conteúdo de serviço do Azure contêiner](http://aka.ms/AzureContainerService)
-    Para obter mais exemplos de como trabalhar com Docker, consulte [Trabalhando com Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) de [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 conectar [demonstração](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Para mais guias de início rápido da demonstração de HealthClinic.biz, consulte [Início rápido de ferramentas de desenvolvedor do Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

## <a name="various-docker-tools"></a>Diversas ferramentas de Docker

[Algumas ferramentas excelentes docker (blog de Steve Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Artigos boas

[Introdução ao Microservices de NGINX](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Apresentações

- [Marco Lasker: VS Live Las Vegas 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
- [Introdução ao ASP.NET Core @ construir 2016 - onde você em demonstração](https://channel9.msdn.com/Events/Build/2016/B810)
- [Desenvolvimento de aplicativos .NET em contêineres, 9 de canal](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
