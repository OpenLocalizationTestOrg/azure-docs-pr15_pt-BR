<properties 
    pageTitle="Como configurar o computador para o desenvolvimento de serviços de mídia com .NET" 
    description="Saiba mais sobre os pré-requisitos para serviços de mídia usando o SDK de serviços de mídia para .NET. Também Saiba como criar um aplicativo do Visual Studio." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/24/2016"  
    ms.author="juliako"/>

#<a name="media-services-development-with-net"></a>Desenvolvimento de serviços de mídia com .NET

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Este tópico aborda como começar a desenvolver aplicativos de serviços de mídia usando .NET.

A biblioteca do **Azure Media Services.NET SDK** permite programar usando .NET de serviços de mídia. Para facilitar ainda desenvolver com .NET, a biblioteca de **Extensões do Azure Media Services .NET SDK** é fornecida. Esta biblioteca contém um conjunto de métodos de extensão e as funções de auxiliar que irá simplificar seu código .NET. As duas bibliotecas estão disponíveis por meio de **NuGet** e **GitHub**.


##<a name="prerequisites"></a>Pré-requisitos

-   Uma conta de serviços de mídia em uma assinatura do Azure nova ou existente. Consulte o tópico [como criar uma conta de serviços de mídia](media-services-portal-create-account.md).
-   Sistemas operacionais: Windows 10, Windows 7, Windows 2008 R2 ou Windows 8.
-   .NET framework 4,5.
-    Visual Studio 2015, Visual Studio 2013, o Visual Studio 2012 ou Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express).


##<a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto do Visual Studio

Esta seção mostra como criar um projeto no Visual Studio e configurá-la para desenvolvimento de serviços de mídia.  Nesse caso, o projeto é um aplicativo de console c# Windows, mas as mesmas etapas de instalação mostradas aqui se aplicam a outros tipos de projetos que você pode criar para aplicativos de serviços de mídia (por exemplo, um aplicativo Windows Forms ou um aplicativo Web ASP.NET).

Esta seção mostra como usar o **NuGet** para adicionar o SDK do .NET de serviços de mídia e outras bibliotecas dependentes.

Como alternativa, você pode obter os bits de Media Services .NET SDK mais recentes do GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) e [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), criar a solução e adicione as referências ao projeto cliente. Observe que todas as dependências necessárias obtém baixadas e extraídas automaticamente.

1. Crie um novo aplicativo de Console em c# no Visual Studio 2010 SP1 ou versões posteriores de VS. Insira o **nome**, o **local**e o **nome da solução**e clique em Okey.

2. Crie a solução.

2. Utilize o **NuGet** para instalar e adicionar **Extensões de SDK do Azure mídia serviços .NET**. Instalar este pacote, também instala o **SDK do .NET de serviços de mídia** e adiciona todos os outras dependências necessárias.

    Certifique-se de que você tenha a versão mais recente do NuGet instalado. Para mais informações e instruções de instalação, consulte [NuGet](http://nuget.codeplex.com/).

2. No Solution Explorer, clique com botão direito no nome do projeto e escolha gerenciar NuGet pacotes...

    Caixa de diálogo Gerenciar pacotes NuGet é exibida.

3. Na Galeria Online, pesquise extensões de serviços de mídia do Azure, escolha extensões de SDK do Azure Media serviços .NET e, em seguida, clique no botão Instalar.

    O projeto é modificado e referências extensões de SDK do .NET de serviços de mídia, SDK do .NET de serviços de mídia e outros conjuntos de dependentes são adicionados.

4. Para promover um ambiente de desenvolvimento mais limpo, considere a habilitação NuGet pacote restaurar. Para obter mais informações, consulte [NuGet pacote restaurar "](http://docs.nuget.org/consume/package-restore).

3. Adicione uma referência ao assembly **System. Configuration** . Esse assembly contém o System. Configuration. Classe de **ConfigurationManager** que é usado para acessar arquivos de configuração (por exemplo, App).

    Para adicionar referências usando a caixa de diálogo Gerenciar referências, clique com botão direito no nome do projeto no Solution Explorer. Em seguida, selecione Adicionar e referências.

    A caixa de diálogo Gerenciar referências é exibida.

4. Em conjuntos de módulos do .NET framework, localize e selecione o assembly System. Configuration e pressione Okey.
5. Abra o arquivo App (adicionar o arquivo ao seu projeto se ele não foi adicionado por padrão) e adicione uma seção *appSettings* ao arquivo.     
Defina os valores para seu serviços de mídia do Azure nome e uma conta chave da conta, conforme mostrado no exemplo a seguir.

    Para localizar o nome e a chave valores, acesse o portal do Azure e selecione sua conta. A janela configurações é exibida à direita. Na janela Configurações, selecione chaves. Clicando no ícone ao lado de cada caixa de texto, o valor é copiado para a área de transferência do sistema.


        <configuration>
        ...
            <appSettings>
              <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
              <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
            </appSettings>

        </configuration>

6. Substitua as existente **usando** instruções no início do arquivo Program.cs pelo código a seguir.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;

Neste ponto, você está pronto para começar a desenvolver um aplicativo de serviços de mídia.    


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
