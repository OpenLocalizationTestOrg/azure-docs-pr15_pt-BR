<properties 
    pageTitle="Gerenciar contas de serviços de mídia do Microsoft Azure com o PowerShell" 
    description="Saiba como gerenciar contas de serviços de mídia do Azure com os cmdlets do PowerShell." 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016"
    ms.author="juliako"/>


#<a name="manage-azure-media-services-accounts-with-powershell"></a>Gerenciar contas de serviços de mídia do Microsoft Azure com o PowerShell

> [AZURE.SELECTOR]
- [Portal](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [RESTANTE](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] Para poder criar uma conta de serviços de mídia do Azure, você deve ter uma conta do Azure. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Avaliação gratuita do Azure</a>.

##<a name="overview"></a>Visão geral 

Este artigo lista os cmdlets do PowerShell do Azure para serviços de mídia do Azure (AMS) no Gerenciador de recursos do Azure framework. Os cmdlets existe no namespace **Microsoft.Azure.Commands.Media** .

## <a name="versions"></a>Versões

**ApiVersion**: "2015-10-01"
               

## <a name="new-azurermmediaservice"></a>Novo AzureRmMediaService

Cria um serviço de mídia.

### <a name="syntax"></a>Sintaxe

Conjunto de parâmetro: StorageAccountIdParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

Conjunto de parâmetro: StorageAccountsParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### <a name="parameters"></a>Parâmetros

**-ResourceGroupName &lt;cadeia de caracteres&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

Aliases | Nenhum
---|---
Necessário?   |  verdadeiro
Posicionar?   |  0
Valor padrão |Nenhum
Aceitar a entrada do pipeline? |True(ByPropertyName)
Aceitar caracteres curinga?  |FALSO

**-AccountName &lt;cadeia de caracteres&gt;**

Especifica o nome do serviço do mídia.

Aliases |Nome
---|---
Necessário? |verdadeiro
Posicionar? |1
Valor padrão |Nenhum
Aceitar a entrada do pipeline? |FALSO
Aceitar caracteres curinga? |FALSO

**-Local &lt;cadeia de caracteres&gt;**

Especifica a localização do recurso do serviço de mídia.

Aliases |Nenhum
---|---
Necessário? |verdadeiro
Posicionar? |2
Valor padrão  |Nenhum
Aceitar a entrada do pipeline? |True(ByPropertyName)
Aceitar caracteres curinga? |FALSO

**-StorageAccountId &lt;cadeia de caracteres&gt;**

Especifica uma conta de armazenamento principal associado ao serviço de mídia.

- Nova conta de armazenamento (criada com a API do Gerenciador de recursos) somente oferece suportada.

- A conta de armazenamento deve existir e tem o mesmo local com o serviço de mídia.

Aliases |Nenhum
---|---
Necessário? |verdadeiro
Posicionar? |3
Valor padrão  |Nenhum
Aceitar a entrada do pipeline? |True(ByPropertyName)
Nome do conjunto de parâmetro |StorageAccountIdParamSet
Aceitar caracteres curinga?|FALSO

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

Especifica as contas de armazenamento associado ao serviço de mídia.

- Nova conta de armazenamento (criada com a API do Gerenciador de recursos) somente oferece suportada.

- A conta de armazenamento deve existir e tem o mesmo local com o serviço de mídia.

- Apenas uma conta de armazenamento pode ser especificada como principal.

Aliases |Nenhum
---|---
Necessário?  |verdadeiro
Posicionar?  |3
Valor padrão |Nenhum
Aceitar a entrada do pipeline? |True(ByPropertyName)
Nome do conjunto de parâmetro |StorageAccountsParamSet
Aceitar caracteres curinga? |FALSO

**-Marcas &lt;hash&gt;**

Especifica uma tabela de hash das marcas que estão associadas com o serviço de mídia.

- Exemplo:@{"tag1"="value1";"tag2"=:value2"}

Aliases |Nenhum
---|---
Necessário?  |FALSO
Posicionar?  |nomeados
Valor padrão |Nenhum
Aceitar a entrada do pipeline? |FALSO
Aceitar caracteres curinga? |FALSO

**&lt;Parâmetros_do_comando&gt;**

Esse cmdlet dá suporte a parâmetros comuns:-depurar, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detalhado, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Entradas

O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### <a name="outputs"></a>Saídas

O tipo de saída é o tipo dos objetos que o cmdlet emite.

## <a name="set-azurermmediaservice"></a>Set-AzureRmMediaService

Atualiza um serviço de mídia.

### <a name="syntax"></a>Sintaxe

    Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### <a name="parameters"></a>Parâmetros

**-ResourceGroupName &lt;cadeia de caracteres&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

Aliases |Nenhum
---|---
Necessário?  |verdadeiro
Posicionar?  |0
Valor padrão |Nenhum
Aceitar a entrada do Pipeline? |True(ByPropertyName)
Aceitar caracteres curinga? |FALSO

**-AccountName &lt;cadeia de caracteres&gt;**

Especifica o nome do serviço do mídia.

Aliases |Nome
---|---
Necessário? |True
Posicionar? |1
Valor padrão |Nenhum
Aceitar a entrada do pipeline? |True(ByPropertyName)
Aceitar caracteres curinga? |FALSO

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

Especifica as contas de armazenamento associado ao serviço de mídia.

- Nova conta de armazenamento (criada com a API do Gerenciador de recursos) somente oferece suportada.

- A conta de armazenamento deve existir e tem o mesmo local com o serviço de mídia.

- Apenas uma conta de armazenamento pode ser especificada como principal.

Aliases |Nenhum
---|---
Necessário? |FALSO
Posicionar? |Nomeados
Valor padrão |Nenhum
Aceitar a entrada do pipeline? |True(ByPropertyName)
Nome do conjunto de parâmetro |StorageAccountsParamSet
Aceitar caracteres curinga? |FALSO

**-Marcas &lt;hash&gt;**

Especifica uma tabela de hash das marcas que estão associadas a esse serviço de mídia.

- As marcas que estão associadas com o serviço de mídia são substituídas por valor especificado pelo cliente.

Aliases |Nenhum
---|---
Necessário? |FALSO
Posicionar?  |Nomeados
Valor padrão |Nenhum
Aceitar a entrada do pipeline? |True(ByPropertyName)
Aceitar caracteres curinga? |FALSO

**&lt;Parâmetros_do_comando&gt;**

Esse cmdlet dá suporte a parâmetros comuns:-depurar, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detalhado, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Entradas

O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### <a name="outputs"></a>Saídas

O tipo de saída é o tipo dos objetos que o cmdlet emite.

## <a name="remove-azurermmediaservice"></a>Remover AzureRmMediaService

Remove um serviço de mídia.

### <a name="syntax"></a>Sintaxe

    Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parâmetros

**-ResourceGroupName &lt;cadeia de caracteres&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

Aliases |Nenhum
---|---
Necessário? |verdadeiro
Posicionar? |0
Valor padrão |Nenhum
Aceitar a entrada do pipeline? |True(ByPropertyName)
Aceitar caracteres curinga? |FALSO

**-AccountName &lt;cadeia de caracteres&gt;**

Especifica o nome do serviço do mídia.

Aliases |Nenhum
---|---
Necessário? |verdadeiro
Posicionar? |2
Valor padrão |Nenhum
Aceitar a entrada do pipeline?  |True(ByPropertyName)
Aceitar caracteres curinga? |FALSO

**&lt;Parâmetros_do_comando&gt;**

Esse cmdlet dá suporte a parâmetros comuns:-depurar, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detalhado, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Entradas

O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### <a name="outputs"></a>Saídas

O tipo de saída é o tipo dos objetos que o cmdlet emite.

## <a name="get-azurermmediaservice"></a>Get-AzureRmMediaService

Obtém todos os serviços de mídia em um grupo de recursos ou um serviço de mídia com um determinado nome.

### <a name="syntax"></a>Sintaxe

ParameterSet: ResourceGroupParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>] 

ParameterSet: AccountNameParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parâmetros

**-ResourceGroupName &lt;cadeia de caracteres&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

Aliases |Nenhum
---|---
Necessário? |verdadeiro
Posicionar?  |0
Valor padrão |Nenhum
Aceitar a entrada do pipeline? |True(ByPropertyName)
Nome do conjunto de parâmetro |ResourceGroupParameterSet, AccountNameParameterSet
Aceitar caracteres curinga?   FALSO

**-AccountName &lt;cadeia de caracteres&gt;**

Especifica o nome do serviço do mídia.

Aliases |Nenhum
---|---
Necessário? |verdadeiro
Posicionar?  |1
Valor padrão |Nenhum
Aceitar a entrada do pipeline? |True(ByPropertyName)
Nome do conjunto de parâmetro  |AccountNameParameterSet
Aceitar caracteres curinga? |FALSO

**&lt;Parâmetros_do_comando&gt;**

Esse cmdlet dá suporte a parâmetros comuns:-depurar, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detalhado, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Entradas

O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### <a name="outputs"></a>Saídas

O tipo de saída é o tipo dos objetos que o cmdlet emite.

## <a name="get-azurermmediaservicekeys"></a>Get-AzureRmMediaServiceKeys

Obtém chaves de um serviço de mídia.

### <a name="syntax"></a>Sintaxe

    Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parâmetros

**-ResourceGroupName &lt;cadeia de caracteres&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

Aliases |Nenhum
---|---
Necessário? |verdadeiro
Posicionar?  |0
Valor padrão |Nenhum
Aceitar a entrada do pipeline? |True(ByPropertyName)
Aceitar caracteres curinga? |FALSO

**-AccountName &lt;cadeia de caracteres&gt;**

Especifica o nome do serviço do mídia.

Aliases |Nenhum
---|---
Necessário? |verdadeiro
Posicionar? |1
Valor padrão |Nenhum
Aceitar a entrada do pipeline? |True(ByPropertyName)
Aceitar caracteres curinga? |FALSO

**&lt;Parâmetros_do_comando&gt;**

Esse cmdlet dá suporte a parâmetros comuns:-depurar, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detalhado, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Entradas

O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### <a name="outputs"></a>Saídas

O tipo de saída é o tipo dos objetos que o cmdlet emite.

## <a name="set-azurermmediaservicekey"></a>Set-AzureRmMediaServiceKey

Gera novamente uma chave primária ou secundária de um serviço de mídia.

### <a name="syntax"></a>Sintaxe

    Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### <a name="parameters"></a>Parâmetros

**-ResourceGroupName &lt;cadeia de caracteres&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

Aliases |Nenhum
---|---
Necessário?  |verdadeiro
Posicionar?  |0
Valor padrão |Nenhum
Aceitar a entrada do pipeline?  |True(ByPropertyName)
Aceitar caracteres curinga? |FALSO

**-AccountName &lt;cadeia de caracteres&gt;**

Especifica o nome do serviço do mídia.

Aliases |Nenhum
---|---
Necessário? |verdadeiro
Posicionar?  |1
Valor padrão |Nenhum
Aceitar a entrada do pipeline?   |True(ByPropertyName)
Aceitar caracteres curinga? |FALSO

**-KeyType &lt;KeyType&gt;**

Especifica o tipo de chave do serviço mídia.

- Primário ou secundário

Aliases |Nenhum
---|---
Necessário?  |verdadeiro
Posicionar?  |2
Valor padrão |Nenhum
Aceitar a entrada do pipeline? |FALSO
Aceitar caracteres curinga? |FALSO

**&lt;Parâmetros_do_comando&gt;**

Esse cmdlet dá suporte a parâmetros comuns:-depurar, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detalhado, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Entradas

O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### <a name="outputs"></a>Saídas

O tipo de saída é o tipo dos objetos que o cmdlet emite.

## <a name="sync-azurermmediaservicestoragekeys"></a>AzureRmMediaServiceStorageKeys de sincronização

Sincroniza as chaves de conta de armazenamento para uma conta de armazenamento associado ao serviço de mídia.

### <a name="syntax"></a>Sintaxe

    Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountId] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parâmetros

**-ResourceGroupName &lt;cadeia de caracteres&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

Aliases |Nenhum
---|---
Necessário? |verdadeiro
Posicionar? |0
Valor padrão |Nenhum
Aceitar a entrada do pipeline? |True(ByPropertyName)
Aceitar caracteres curinga? |FALSO

**-AccountName &lt;cadeia de caracteres&gt;**

Especifica o nome do serviço do mídia.

Aliases |Nenhum
---|---
Necessário? |verdadeiro
Posicionar? |1
Valor padrão |Nenhum
Aceitar a entrada do pipeline? |True(ByPropertyName)
Aceitar caracteres curinga? |FALSO

**-StorageAccountId &lt;cadeia de caracteres&gt;**

Especifica a conta de armazenamento associada ao serviço de mídia.

Aliases |ID
---|---
Necessário? |verdadeiro
Posicionar?  |2
Valor padrão |Nenhum
Aceitar a entrada do pipeline? |      True(ByPropertyName)
Aceitar caracteres curinga? |FALSO

**&lt;Parâmetros_do_comando&gt;**

Esse cmdlet dá suporte a parâmetros comuns:-depurar, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detalhado, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Entradas

O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### <a name="outputs"></a>Saídas

O tipo de saída é o tipo dos objetos que o cmdlet emite.

## <a name="next-step"></a>Próxima etapa 

Confira os serviços de mídia caminhos de aprendizado.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
