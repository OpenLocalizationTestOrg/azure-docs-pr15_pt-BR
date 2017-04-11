<properties
    pageTitle="Usar o armazenamento do Azure em aplicativos da Windows Store | Microsoft Azure"
    description="Aprenda a criar um aplicativo da Windows Store que usa o armazenamento de Blob do Azure, fila, tabela ou arquivo."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>
    
# <a name="how-to-use-azure-storage-in-windows-store-apps"></a>Como usar o armazenamento do Azure nos aplicativos da Windows Store

## <a name="overview"></a>Visão geral

Este guia mostra como começar com o desenvolvimento de um aplicativo da Windows Store que utiliza o armazenamento do Azure.

## <a name="download-required-tools"></a>Baixar ferramentas necessárias

- [Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx) torna mais fácil de criar, depurar, localizar, pacote e implantar aplicativos da Windows Store. Visual Studio 2012 ou posterior é necessário.
- A [Biblioteca de cliente de armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage) fornece uma biblioteca de classes de tempo de execução do Windows para trabalhar com o armazenamento do Azure.
- [WCF dados serviços ferramentas para Windows Store Apps](http://www.microsoft.com/download/details.aspx?id=30714) estende a experiência de adicionar referência de serviço com suporte de OData do lado do cliente para aplicativos da Windows Store no Visual Studio.

## <a name="develop-apps"></a>Desenvolva aplicativos

### <a name="getting-ready"></a>Se preparando

Crie um novo projeto de aplicativo da Windows Store no Visual Studio 2012 ou posterior:

![repositório-aplicativos-armazenamento-vs-projeto][store-apps-storage-vs-project]

Em seguida, adicione uma referência para a biblioteca de cliente de armazenamento do Azure clicando **referências**, clicando em **Adicionar referência**e, em seguida, navegando até o armazenamento de cliente para Windows Runtime da biblioteca que você baixou:

![repositório de aplicativos-armazenamento-escolha-biblioteca][store-apps-storage-choose-library]

### <a name="using-the-library-with-the-blob-and-queue-services"></a>Usando a biblioteca com os serviços de Blob e fila

Neste ponto, o aplicativo está pronto para os serviços do Azure Blob e fila de chamadas. Adicione as seguintes instruções **using** para que tipos de armazenamento do Azure podem ser referenciados diretamente:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;

Em seguida, adicione um botão à sua página. Adicione o seguinte código para o evento **Click** e modificar seu método de manipulador de eventos usando a [palavra-chave assíncrono](http://msdn.microsoft.com/library/vstudio/hh156513.aspx):

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();

Esse código supõe que você tenha variáveis de cadeia de caracteres de dois, *accountName* e *accountKey*. Eles representam o nome da sua conta de armazenamento e a chave da conta que está associada essa conta.

Criar e executar o aplicativo. Clicando no botão Verificar se existe um recipiente chamado *container1* em sua conta e então criá-la se não.

### <a name="using-the-library-with-the-table-service"></a>Usando a biblioteca com o serviço de tabela

Tipos que são usados para se comunicar com o serviço de tabela do Azure dependem WCF Data Services para a biblioteca de aplicativo da Windows Store. Em seguida, adicione uma referência para as bibliotecas necessárias do WCF usando o Package Manager Console:

![repositório-aplicativos--pacote-Gerenciador de armazenamento][store-apps-storage-package-manager]

Use o seguinte comando para Gerenciador de pacote de ponto para o local em seu computador:

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

Este comando adicionará automaticamente todas as referências necessárias ao seu projeto. Se não quiser usar o Console Gerenciador de pacote, você pode adicionar a pasta de NuGet de serviços de dados WCF em sua máquina local à lista de fontes de pacote e adicione a referência por meio da UI, conforme descrito em [Gerenciar pacotes de NuGet usando a caixa de diálogo](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog).

Quando você ter referenciado o pacote NuGet de serviços de dados WCF, altere o código no evento **Click** do seu botão:

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();

Este código verifica se uma tabela denominada *Tabela1* existe em sua conta e, em seguida, cria-se não.

Você também pode adicionar uma referência a Microsoft.WindowsAzure.Storage.Table.dll, que está disponível no mesmo pacote que você baixou. Esta biblioteca contém funcionalidade adicional, como serialização baseada em reflexão e consultas genéricas. Observe que esta biblioteca não dá suporte a JavaScript.



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png
