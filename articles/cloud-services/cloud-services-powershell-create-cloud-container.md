<properties
   pageTitle="Criar um contêiner de serviço de nuvem com PowerShell | Microsoft Azure"
   description="Este artigo explica como criar um contêiner de serviço de nuvem com o PowerShell. O contêiner hospeda funções da web e de trabalho."
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="cawa"/>

# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Usar um comando do PowerShell do Azure para criar um contêiner de serviço de nuvem vazio
Este artigo explica como criar rapidamente um contêiner de serviços de nuvem usando cmdlets do PowerShell do Azure. Siga as etapas abaixo:

1. Instale o cmdlet do PowerShell do Microsoft Azure na página [downloads do PowerShell do Azure](http://aka.ms/webpi-azps) .
2. Abra o prompt de comando do PowerShell.
3. Use o [Add-AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) para entrar.

    > [AZURE.NOTE] Para obter mais instruções sobre como instalar o cmdlet do PowerShell do Azure e se conectar à sua assinatura do Azure, consulte [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

4. Use o cmdlet **New-AzureService** para criar um contêiner de serviço de nuvem Azure vazia.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

5. Siga este exemplo para chamar o cmdlet:
```
New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
```

Para obter mais informações sobre a criação de serviço de nuvem Azure, execute:
```
Get-help New-AzureService
```

### <a name="next-steps"></a>Próximas etapas

 * Para gerenciar a implantação do serviço de nuvem, consulte os comandos [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remover-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx)e [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx) . Você também pode consultar como [Configurar os serviços de nuvem](cloud-services-how-to-configure.md) para obter mais informações.

 * Para publicar seu projeto de serviço de nuvem do Azure, consulte o exemplo de código **PublishCloudService.ps1** do [fornecimento contínuo de serviço de nuvem no Azure](cloud-services-dotnet-continuous-delivery.md).
