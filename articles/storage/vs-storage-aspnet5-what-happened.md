<properties
    pageTitle="O que aconteceu com meu projeto ASP.NET 5 (Visual Studio conectado serviços) | Armazenamento do Microsoft Azure"
    description="Descreve o que acontece depois de se conectar a uma conta de armazenamento do Azure em um projeto do Visual Studio ASP.NET 5 usando o Visual Studio conectado serviços"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>O que aconteceu com meu projeto ASP.NET 5 (Visual Studio Azure Storage conectado services)?

## <a name="references-added"></a>Referências adicionadas

O pacote NuGet de armazenamento do Azure foi adicionado ao seu projeto do Visual Studio.  
Este pacote adiciona as referências de .NET a seguir:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.Configuration**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System**
- **System.Spatial**

Além disso, o pacote do NuGet **Microsoft.Framework.Configuration.Json** foi adicionado.

## <a name="connection-string-for-azure-storage-added"></a>Cadeia de Conexão para o armazenamento do Azure adicionado
No arquivo config.json do seu projeto, um elemento foi criado com a conta de armazenamento selecionado cadeia de conexão e chave.

Para obter mais informações, consulte [ASP.NET 5](http://www.asp.net/vnext).
