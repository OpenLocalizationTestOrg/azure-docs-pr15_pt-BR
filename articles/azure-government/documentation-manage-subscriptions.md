<properties
    pageTitle="Serviços governamentais Azure | Microsoft Azure"
    description="Informações sobre como gerenciar sua assinatura no governo do Azure"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="zakramer"
    manager="liki"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/21/2016"
    ms.author="zakramer" />


#  <a name="managing-and-connecting-to-your-subscription-in-azure-government"></a>Gerenciamento e se conectar à sua assinatura do governo do Azure

Governo Azure tem pontos de extremidade para o gerenciamento de seu ambiente e URLs exclusivos. É importante usar conexões adequadas para gerenciar seu ambiente por meio do portal ou PowerShell. Quando você estiver conectado ao ambiente do governo do Azure, as operações normais para gerenciar um serviço funciona se o componente foi implantado.

## <a name="connecting-via-the-portal"></a>Conectando através do portal
O portal é a principal maneira que a maioria das pessoas se conectam ao governo do Azure.  Para se conectar, navegue até o portal em [https://portal.azure.us](https://portal.azure.us).  A versão herdada do portal do Azure pode ser acessada por meio de [https://manage.windowsazure.us](https://manage.windowsazure.us).

Assinaturas podem ser criadas para a sua conta conectando-se ao [https://account.windowsazure.us](https://account.windowsazure.us).

## <a name="connecting-via-powershell"></a>Conectar-se por meio do PowerShell

Se você estiver usando o Azure PowerShell para gerenciar uma grande assinatura através de script ou acessar recursos que não estão disponíveis atualmente no portal do Azure, que você precisa se conectar ao governo do Azure em vez do Azure público.  Se você tiver usado o PowerShell no Azure público, ele é principalmente o mesmo.  As diferenças no Azure governo são:

+ Conectando a sua conta
+ Nomes de região

>[AZURE.NOTE] Se você não tiver usado o PowerShell, confira a [Introdução ao Azure PowerShell](../powershell-install-configure.md).

Quando você inicia o PowerShell, você precisa saber Azure PowerShell para se conectar ao Azure governo especificando um parâmetro de ambiente.  O parâmetro garante que o PowerShell está se conectando a pontos de extremidade correto.  O conjunto de pontos de extremidade é determinado quando você conecta login à sua conta.  APIs diferentes exigem diferentes versões da opção de ambiente:

Tipo de Conexão | Comando
---|----
Comandos de [Gerenciamento de serviço](https://msdn.microsoft.com/library/dn708504.aspx) | `Add-AzureAccount -Environment AzureUSGovernment`
Comandos de [Gerenciamento de recursos](https://msdn.microsoft.com/library/mt125356.aspx) | `Add-AzureRmAccount -EnvironmentName AzureUSGovernment`
Comandos do [Active Directory do Azure](https://msdn.microsoft.com/library/azure/jj151815.aspx) | `Connect-MsolService -AzureEnvironment UsGovernment`
[V2 de comando do Azure Active Directory](https://msdn.microsoft.com/library/azure/mt757189.aspx) | `Connect-AzureAD -AzureEnvironmentName AzureUSGovernment`

Você também pode usar a opção de ambiente ao se conectar a uma conta de armazenamento usando New-AzureStorageContext e especifique AzureUSGovernment.

### <a name="determining-region"></a>Determinando região

Quando você estiver conectado, não há uma diferença adicional – as regiões usadas para um serviço de destino.  Cada nuvem Azure tem regiões diferentes.  Você pode vê-los listados na página de disponibilidade do serviço.  Normalmente, você usar a região no parâmetro local para um comando.

Há um problema.  As regiões do governo do Azure precisam ser formatado de maneira diferente seus nomes comuns:

Nome comum | Comando
---|----
EUA Gov Virgínia | USGov Virgínia
EUA Gov Iowa | Iowa USGov

>[AZURE.NOTE] Não há nenhum espaço entre dos EUA e Gov ao usar o parâmetro local.

Se você nunca deseja validar as regiões disponíveis no Azure governo, você pode executar os seguintes comandos e imprimir a lista atual:

    Get-AzureLocation

Se você estiver curioso para os ambientes disponíveis através do Azure, você pode executar:

    Get-AzureEnvironment

## <a name="next-steps"></a>Próximas etapas

Se você estiver procurando mais informações, consulte:

+ [Documentos do PowerShell em GitHub](https://github.com/Azure/azure-powershell)
+ [Instruções passo a passo sobre como conectar ao gerenciamento de recursos](https://blogs.msdn.microsoft.com/azuregov/2015/10/08/configuring-arm-on-azure-gc/)
+ [Documentos do PowerShell Azure no MSDN](https://msdn.microsoft.com/library/mt619274.aspx)

Para obter informações complementares e atualizações assine o [Microsoft Azure governo Blog] (https://blogs.msdn.microsoft.com/azuregov/)
