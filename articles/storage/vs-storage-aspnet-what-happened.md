<properties
    pageTitle="O que aconteceu ao meu projeto ASP.NET? | Microsoft Azure | Serviços do Visual Studio conectado"
    description="Descreve o que acontece após a adição de armazenamento do Azure para um projeto do ASP.NET usando o Visual Studio conectado serviços"
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

# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>O que aconteceu com meu projeto ASP.NET (Visual Studio Azure Storage conectado serviço)?

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

##<a name="connection-string-for-azure-storage-added"></a>Cadeia de Conexão para o armazenamento do Azure adicionado
No arquivo Web. config do seu projeto, um elemento foi criado com a conta de armazenamento selecionado cadeia de conexão e chave.

Para obter mais informações, consulte [ASP.NET](http://www.asp.net).
