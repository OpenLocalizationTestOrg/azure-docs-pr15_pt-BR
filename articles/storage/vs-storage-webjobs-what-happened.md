<properties
    pageTitle="O que aconteceu com meu projeto WebJob (Visual Studio Azure Storage conectado serviço)? | Microsoft Azure"
    description="Descreve o que aconteceu em um projeto do Azure WebJob depois de se conectar a uma conta de armazenamento usando o Visual Studio conectado serviços"
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

# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>O que aconteceu com meu projeto WebJob (Visual Studio Azure Storage conectado serviço)?

## <a name="references-added"></a>Referências adicionadas

O pacote NuGet de armazenamento do Azure foi adicionado ou atualizado no seu projeto do Visual Studio.  
Este pacote adiciona as referências de .NET a seguir:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.ConfigurationManager**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System**
- **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Cadeia de Conexão para o armazenamento do Azure adicionado
No arquivo App. config do seu projeto, as entradas **AzureWebJobsStorage** e **AzureWebJobsDashboard** foram atualizadas com chave e cadeia de conexão da conta de armazenamento selecionado.

Para obter mais informações, consulte [recursos de documentação WebJobs do Azure](http://go.microsoft.com/fwlink/?linkid=390226).
