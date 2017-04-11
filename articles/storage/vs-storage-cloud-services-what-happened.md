<properties
    pageTitle="O que aconteceu ao meu projeto de serviço de nuvem? | Microsoft Azure | Serviços do Visual Studio conectado"
    description="Descreve o que acontece em um projeto de serviços de nuvem depois de se conectar a uma conta de armazenamento do Azure usando o Visual Studio conectado serviços"
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

# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>O que aconteceu com o meu projeto de serviços de nuvem (Visual Studio Azure Storage conectado serviço)?

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

## <a name="connection-string-for-azure-storage-added"></a>Cadeia de Conexão para o armazenamento do Azure adicionado
Elementos foram criados com a conta de armazenamento selecionado cadeia de conexão e chave. Modificações foram feitas nos seguintes arquivos:

- **ServiceDefinition.csdef**
- **ServiceConfiguration.Cloud.cscfg**
- **ServiceConfiguration.Local.cscfg**
