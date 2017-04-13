<properties
   pageTitle="Compilação de linha de comando para o Azure | Microsoft Azure"
   description="Compilação de linha de comando do Azure"
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

# <a name="command-line-build-for-azure"></a>Compilação de linha de comando do Azure

## <a name="overview"></a>Visão geral

Você pode criar um pacote para implantação Azure executando MSBuild no prompt de comando. Você pode configurar e definir compilações para depuração, teste e produção, além de automatizar algumas do processo de compilação.


## <a name="microsoft-build-engine-msbuild"></a>Mecanismo de compilação Microsoft (MSBuild)

Usando o Microsoft Build Engine (MSBuild), você pode criar produtos em compilação ambientes de laboratório onde o Visual Studio não está instalado. MSBuild usa um formato XML para arquivos de projeto do extensível e totalmente suportado pela Microsoft. Nesse formato de arquivo, você pode descrever quais itens devem ser criados para uma ou mais plataformas e configurações.

Você também pode executar MSBuild no prompt de comando e este tópico descreve essa abordagem. Definindo propriedades no prompt de comando, você pode criar configurações específicas de um projeto. Da mesma forma, você também pode definir os destinos que criará o comando MSBuild. Para obter mais informações sobre parâmetros de linha de comando e MSBuild, consulte [Referência de linha de comando do MSBuild](https://msdn.microsoft.com/library/ms164311.aspx).

## <a name="installation"></a>Instalação

Como o procedimento a seguir descreve, você deve instalar software e ferramentas no servidor de compilação antes de criar um pacote do Azure usando MSBuild:

1. Instale o .NET Framework 4 ou posterior, que inclui MSBuild.

1. Instalar as [Ferramentas de criação do Azure](http://go.microsoft.com/fwlink/?LinkId=394615) (procure MicrosoftAzureAuthoringTools x64.msi ou MicrosoftAzureAuthoringTools-x86.msi.

1. Instalar as [Bibliotecas do Azure para .NET](http://go.microsoft.com/fwlink/?LinkId=394616) (procure MicrosoftAzureLibsForNet x64.msi ou MicrosoftAzureLibs-x86.msi.

1. Copie o arquivo de Microsoft.WebApplication.targets de uma instalação do Visual Studio em outro computador.

    O arquivo está localizado no C:\Program Files (x86) de diretório \MSBuild\Microsoft\Visual Studio\v12.0\WebApplications (v11.0 para Visual Studio 2012) e você deve copiá-lo para o mesmo diretório no servidor de compilação.

1. Instale as [Ferramentas Azure para Visual Studio](http://go.microsoft.com/fwlink/?LinkId=394616).

    Procure por WindowsAzureTools.vs120.exe criar projetos do Visual Studio 2013.

## <a name="msbuild-parameters"></a>Parâmetros de MSBuild

A maneira mais simples para criar um pacote é executar MSBuild com a `/t:Publish` opção. Por padrão, esse comando cria um diretório em relação a pasta raiz para o projeto, como ProjectDir\bin\Configuration\app.publish\. quando você cria um projeto do Azure, você gerar dois arquivos, o próprio arquivo de pacote e o arquivo de configuração que acompanha:

- Project.cspkg

- ServiceConfiguration.TargetProfile.cscfg

Por padrão, cada projeto Azure inclui um arquivo de configuração do serviço para local (depuração) cria e outro para compilações de nuvem (temporário ou produção), mas você pode adicionar ou remover arquivos de configuração do serviço conforme necessário. Quando você cria um pacote dentro do Visual Studio, você será solicitado qual arquivo de configuração do serviço para incluir junto com o pacote. Quando você cria um pacote usando MSBuild, o arquivo de configuração do serviço local é incluído por padrão. Para incluir um arquivo de configuração de serviço diferente, defina o `TargetProfile` propriedade do comando MSBuild (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Se você quiser usar um diretório alternativo para o pacote armazenado e arquivos de configuração, defina o caminho usando o `/p:PublishDir=Directory\` opção, incluindo o separador de barra invertida à direita.

## <a name="deployment"></a>Implantação

Depois que o pacote é criado, você pode implantá-lo no Azure. Para um tutorial que demonstra esse processo, consulte o site Azure. Para obter informações sobre como automatizar esse processo, consulte [Fornecimento contínuo para serviços de nuvem no Azure](./cloud-services/cloud-services-dotnet-continuous-delivery.md).
