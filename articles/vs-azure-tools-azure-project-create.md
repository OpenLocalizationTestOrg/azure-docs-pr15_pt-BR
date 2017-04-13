<properties
   pageTitle="Criando um projeto do Azure com o Visual Studio | Microsoft Azure"
   description="Criando um projeto do Azure com o Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="creating-an-azure-project-with-visual-studio"></a>Criando um projeto do Azure com o Visual Studio

As ferramentas do Azure para Visual Studio fornecem um modelo que permite que você crie um serviço de nuvem para Azure. As ferramentas também ajudarão-lo a configurar, depurar e implantar o serviço de nuvem para o Azure.

Uma solução de serviços de nuvem Azure contém os seguintes tipos de projetos:

- **Projeto Azure**

    O projeto Azure tem associações para os projetos de função na solução. Ele também inclui a definição de serviço e os arquivos de configuração do serviço. O arquivo de definição de serviço define as configurações de tempo de execução para seu aplicativo incluindo quais funções são necessárias, pontos de extremidade e tamanho da máquina virtual. O arquivo de configuração do serviço configura quantas instâncias de uma função são executadas e os valores das configurações definidas para uma função. Para obter mais informações sobre essas configurações, consulte [como: configurar as funções para um serviço de nuvem do Azure com o Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

- **Projeto de função Web**

    Uma função de trabalho executa o processamento de plano de fundo. Uma função de trabalho pode se comunicar com os serviços de armazenamento e com outros serviços baseados na Internet. Uma função de trabalho pode ter qualquer número de pontos de extremidade de HTTP, HTTPS ou TCP.

    - **Função de Web do ASP.NET**, para a criação de um aplicativo ASP.NET com um front-end da web
    - **Função de Web do ASP.NET MVC5**
    - **Função de Web do ASP.NET MVC4**
    - **Função de Web do ASP.NET MVC3**
    - **Função do WCF serviço Web**, para a criação de um serviço WCF
    - **Função do Silverlight Business aplicativo Web** (exige o Visual Studio 2012)

- **Função de trabalho de cache**

    Uma função que fornece um cache dedicado ao seu aplicativo.

- **Função de trabalho com fila de barramento de serviço**

    Uma fila de barramento de serviço que oferece a funcionalidade de filas de mensagens para se comunicar com o processo de trabalho. Para obter mais informações, consulte [como filas de barramento de serviço de uso](http://go.microsoft.com/fwlink/?LinkId=260560).

## <a name="to-create-an-azure-cloud-service-project-in-visual-studio"></a>Criar um projeto de serviço de nuvem Azure no Visual Studio

1. Inicie o Microsoft Visual Studio como administrador.

1. Na barra de menus, escolha **arquivo**, **novo** **projeto**.

1. No painel **Tipos de projeto** , escolha **nuvem** do projeto Visual c# ou Visual Basic nós do modelo.

1. No painel **modelos** , escolha **O serviço de nuvem do Azure**.

1. Especifica qual versão do .NET Framework que você deseja usar para desenvolver o seu projeto.

1. Insira um nome e local para seu projeto e um nome para a solução. Escolha o botão de **Okey** .

1. Na caixa de diálogo **Novo projeto do Azure** , escolha as funções que você deseja adicionar e escolha o botão de seta para a direita para adicioná-las a sua solução. Você pode adicionar quantas funções conforme necessário.

1. Para renomear uma função que você adicionou ao seu projeto, passe o mouse sobre a função na caixa de diálogo **Novo projeto do Azure** e escolha o ícone **Renomear** à direita da função. Você também pode renomear uma função dentro de sua solução depois que ela foi adicionada.
