<properties
    pageTitle="Log do Azure cofre chave | Microsoft Azure"
    description="Usar este tutorial para ajudá-lo a começar a usar o Azure chave cofre log."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/31/2016"
    ms.author="cabailey"/>

# <a name="azure-key-vault-logging"></a>Log do Azure cofre chave #
Azure Cofre de chave está disponível na maioria das regiões. Para obter mais informações, consulte a [chave do cofre preços página](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introdução  
Após ter criado um ou mais compartimentos de chave, você provavelmente desejará monitorar como e quando sua chaves compartimentos são acessados e por quem. Você pode fazer isso por habilitar o log de Cofre de chave, que salva informações em uma conta de armazenamento do Azure que você fornecer. Um novo recipiente chamado **ideias de logs de auditevent** será criado automaticamente para a sua conta de armazenamento especificado, e você pode usar essa mesma conta de armazenamento para coletar logs para vários compartimentos de chave.

Você pode acessar suas informações de registro em log no máximo, 10 minutos após a chave cofre operação. Na maioria dos casos, será mais rápido do que isso.  Cabe ao gerenciar seus logs em sua conta de armazenamento:

- Use métodos de controle de acesso Azure padrão para proteger seus logs restringindo quem pode acessá-los.
- Exclua registros que você não quiser mais manter na sua conta de armazenamento.

Use este tutorial para ajudá-lo a começar a usar o log, para criar sua conta de armazenamento, habilitar o log e interpretar as informações de log coletadas do Azure chave cofre.  


>[AZURE.NOTE]  Este tutorial não inclui instruções sobre como criar compartimentos de chave, chaves ou segredos. Para obter essas informações, consulte [Introdução ao Azure chave cofre](key-vault-get-started.md). Ou, para obter instruções de Interface de linha de comando de plataforma híbrida, consulte [Este tutorial equivalente](key-vault-manage-with-cli.md).
>
>Atualmente, você não pode configurar Azure chave cofre no portal do Azure. Em vez disso, use estas instruções do PowerShell do Azure.

Os logs que você coletar podem ser visualizados usando a análise de Log do pacote de gerenciamento de operações. Para obter mais informações, consulte [solução de compartimento de chave do Azure (visualização) na análise de Log](../log-analytics/log-analytics-azure-key-vault.md).

Para obter informações gerais sobre o Azure chave cofre, consulte [o que é Azure chave cofre?](key-vault-whatis.md)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você deve ter o seguinte:

- Um cofre chave existente que você tem usado.  
- PowerShell Azure, **versão mínima do 1.0.1**. Para instalar o PowerShell do Azure e associá-lo a sua assinatura do Azure, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Se você já tiver instalado o Azure PowerShell e não souber a versão do console do PowerShell do Azure, digite `(Get-Module azure -ListAvailable).Version`.  
- Logs de armazenamento suficiente no Azure para seu Cofre de chave.


## <a id="connect"></a>Conectar-se a suas assinaturas ##

Iniciar uma sessão do PowerShell do Azure e entrar em sua conta do Azure com o seguinte comando:  

    Login-AzureRmAccount

Na janela do navegador pop-up, insira seu nome de usuário da conta do Azure e senha. PowerShell Azure obterá todas as assinaturas que são associados com essa conta e por padrão, usa primeiro.

Se você tiver várias assinaturas, talvez seja necessário especificar um grupo específico que foi usado para criar seu Cofre de chave do Azure. Digite o seguinte para ver as assinaturas para sua conta:

    Get-AzureRmSubscription

Em seguida, para especificar a assinatura que está associada a sua chave cofre que fizerem logon, digite:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Para obter mais informações sobre a configuração do PowerShell do Azure, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).


## <a id="storage"></a>Criar uma nova conta de armazenamento para seus logs ##

Embora você possa usar uma conta existente do armazenamento para seus logs, vamos criar uma nova conta de armazenamento que for dedicada logs de chave cofre. Para sua conveniência para quando temos especificar isso mais tarde, podemos vai armazenar os detalhes em uma variável chamada **sa**.

Para facilitar o adicional de gerenciamento, também usaremos mesmo grupo de recursos como aquele que contém nosso principais cofre. O [tutorial de Introdução](key-vault-get-started.md), esse grupo de recursos é chamado **ContosoResourceGroup** e podemos continuará a usar o local do Leste Asiático. Substitua esses valores para seu próprio, conforme aplicável:

    $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name ContosoKeyVaultLogs -Type Standard_LRS -Location 'East Asia'


>[AZURE.NOTE]  Se você decidir usar uma conta de armazenamento existente, ele deve usar a mesma assinatura como seu cofre chave e ele deve usar o modelo de implantação do Gerenciador de recursos, em vez do modelo de implantação do clássico.

## <a id="identify"></a>Identificar o cofre chave para os logs ##

Nosso tutorial de Introdução ao obter, o nome da nossa principais cofre foi **ContosoKeyVault**, para nós continuará a usar esse nome e armazenar os detalhes em uma variável chamada **kv**:

    $kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>Habilitar o log ##

Para habilitar o log de chave cofre, vamos usar o cmdlet Set-AzureRmDiagnosticSetting, junto com as variáveis que criamos para nossa nova conta de armazenamento e nosso principais cofre. Nós também definirá o **-habilitado** sinalizar para **$true** e definir a categoria para AuditEvent (a categoria somente para o log de chave cofre),:


    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

A saída para isso inclui:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0


Isso confirma que log agora está habilitada para seu cofre chave, salvar informações de sua conta de armazenamento.

Opcionalmente, você também pode definir política de retenção para os logs de forma que logs mais antigos serão automaticamente excluídos. Por exemplo, defina a política de retenção usando o sinalizador **- RetentionEnabled** **$true** e defina **- RetentionInDays** parâmetro a **90** para que os logs de mais de 90 dias serão automaticamente excluídos.

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90

O que é registrado:

- Todas as solicitações de API REST autenticadas são registradas, que inclui solicitações com falha como resultado de permissões de acesso, erros do sistema ou solicitações de incorretas.
- Operações na chave cofre em si, que inclui a criação, exclusão, políticas de acesso do cofre chave de configuração, e atualizar os atributos de chave cofre como marcas.
- Operações em chaves e senhas no cofre de chave, que inclui criar, modificar ou excluir estas teclas ou segredos; operações como entrar, verificar, criptografar, descriptografar, quebrar e não quebrar chaves, obtenha segredos, lista teclas e segredos e suas versões.
- Solicitações não autenticadas que resultam em uma resposta 401. Por exemplo, solicitações não tem um token de portador, ou expirada ou formado ou que possuem um token inválido.  


## <a id="access"></a>Acessar os logs ##

Logs de chave cofre são armazenados no contêiner **ideias de logs de auditevent** a conta de armazenamento que você forneceu. Para listar todos os blobs nesse contêiner, digite:

    Get-AzureStorageBlob -Container 'insights-logs-auditevent' -Context $sa.Context

A saída ficará algo semelhante a esta:

**Uri de contêiner: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**


**Nome**

**----**

**identificação de recurso = / assinaturas/361DA5D4-A47A-4 79 C-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/provedores/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**identificação de recurso = / assinaturas/361DA5D4-A47A-4 79 C-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/provedores/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

* * resourceId = / assinaturas/361DA5D4-A47A-4 79 C-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/provedores/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json***


Como você pode ver a partir desta saída, os blobs seguem uma convenção de nomenclatura: **resourceId =<ARM resource ID>/y =<year>/m =<month>/d =<day of month>/h =<hour>/m =<minute>/filename.json**

Os valores de data e hora usam UTC.

Como a mesma conta de armazenamento pode ser usada para coletar logs para vários recursos, a identificação do recurso completo no nome do blob é muito útil para acessar ou baixar apenas as bolhas que você precisa. Mas antes de fazer isso, primeiro abordaremos como baixar todos os blobs.

Primeiro, crie uma pasta para baixar os blobs. Por exemplo:

    New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

Em seguida, obter uma lista de todos os blobs:  

    $blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

Conduza essa lista por meio de 'Get-AzureStorageBlobContent' para baixar os blobs em nossa pasta de destino:

    $blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

Quando você executar este comando segundo, a **/** delimitador nos nomes de blob criar uma estrutura de pasta completo sob a pasta de destino e essa estrutura será usada para baixar e armazenar os blobs como arquivos.

Para baixar seletivamente blobs, use caracteres curinga. Por exemplo:

- Se você tiver vários compartimentos de chave e deseja baixar logs para apenas um cofre chave, chamado CONTOSOKEYVAULT3:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3

- Se você tiver vários grupos de recursos e deseja baixar logs para apenas um grupo de recursos, use `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'

- Se você deseja baixar todos os logs do mês de janeiro de 2016, use `-Blob '*/year=2016/m=01/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

Agora você está pronto para começar olhando Novidades nos logs. Mas antes de passar para que, dois parâmetros mais para Get-AzureRmDiagnosticSetting que talvez você precise saber:

- Para consultar o status das configurações de diagnóstico para o recurso de cofre chave:`Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`

- Para desabilitar o registro em log para o recurso de cofre chave:`Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`


## <a id="interpret"></a>Interpretar os logs de chave cofre ##

Blobs individuais são armazenados como texto, formatado como um blob JSON. Este é um exemplo de entrada de log execução `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`:

    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }


A tabela a seguir lista os nomes de campo e descrições.


| Nome do campo        | Descrição |
| ------------- |-------------|
| tempo      | Data e hora (UTC).|
| identificação de recurso      | ID do Azure recurso Gerenciador de recursos. Para logs de chave cofre, isso é sempre a identificação do recurso de chave cofre.|
| operationName      | Nome da operação, como documentadas na tabela a seguir.|
| operationVersion      | Esta é a versão de API REST solicitada pelo cliente.|
| categoria      | Para logs de chave cofre, AuditEvent é o valor único, disponível.|
| resultType      | Resultado da solicitação de API REST.|
| resultSignature      | Status HTTP.|
| resultDescription     | Descrição adicional sobre o resultado, quando disponível.|
| durationMs      | Tempo necessário para atender à solicitação de API REST, em milissegundos. Isso não inclui a latência de rede, para que o tempo que você medir no lado do cliente pode não corresponder neste momento.|
| callerIpAddress      | Endereço IP do cliente que fizeram a solicitação.|
| correlationId      | Um GUID opcional que o cliente pode passar para correlação logs do lado cliente com logs no lado do serviço (compartimento de chave).|
| identidade      | Identidade do token apresentado ao fazer a solicitação de API REST. Isso geralmente é um "usuário", "capital de serviço" ou uma combinação "usuário + appId" como em caso de uma solicitação, resultando em um cmdlet do PowerShell do Azure.|
| Propriedades      | Esse campo irá conter informações diferentes com base na operação (operationName). Na maioria dos casos, contém informações de cliente (a cadeia de caracteres useragent passada pelo cliente), a solicitação de API REST exata URI e o código de status HTTP. Além disso, quando um objeto é retornado como resultado de uma solicitação (por exemplo, KeyCreate ou VaultGet)-la também conterá o URI de chave (como "id"), cofre URI ou URI secreta.|




Os valores de campo **operationName** estão em formato de ObjectVerb. Por exemplo:

- Todas as operações de chave cofre têm a ' cofre`<action>`' Formatar, tais como `VaultGet` e `VaultCreate`.

- Todas as operações principais têm a ' chave`<action>`' Formatar, tais como `KeySign` e `KeyList`.

- Todas as operações secretas têm a ' segredo`<action>`' Formatar, tais como `SecretGet` e `SecretListVersions`.

A tabela a seguir lista os operationName e comando API REST correspondente.

| operationName        | Comando da API REST |
| ------------- |-------------|
| Autenticação      | Por meio de ponto de extremidade do Active Directory do Azure|
| VaultGet      | [Obter informações sobre um cofre chave](https://msdn.microsoft.com/en-us/library/azure/mt620026.aspx)|
| VaultPut      | [Criar ou atualizar um cofre chave](https://msdn.microsoft.com/en-us/library/azure/mt620025.aspx)|
| VaultDelete      | [Excluir um cofre chave](https://msdn.microsoft.com/en-us/library/azure/mt620022.aspx)|
| VaultPatch      | [Atualizar um cofre chave](https://msdn.microsoft.com/library/azure/mt620025.aspx)|
| VaultList      | [Lista todos os compartimentos chaves em um grupo de recursos](https://msdn.microsoft.com/en-us/library/azure/mt620027.aspx)|
| KeyCreate      | [Criar uma chave](https://msdn.microsoft.com/en-us/library/azure/dn903634.aspx)|
| KeyGet      | [Obter informações sobre uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878080.aspx)|
| KeyImport      | [Importar uma chave para um cofre](https://msdn.microsoft.com/en-us/library/azure/dn903626.aspx)|
| KeyBackup      | [Fazer backup de uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878058.aspx).|
| KeyDelete      | [Excluir uma chave](https://msdn.microsoft.com/en-us/library/azure/dn903611.aspx)|
| KeyRestore      | [Restaurar uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878106.aspx)|
| KeySign      | [Entrar com uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878096.aspx)|
| KeyVerify      | [Verifique com uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878082.aspx)|
| KeyWrap      | [Quebrar uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878066.aspx)|
| KeyUnwrap      | [Não quebrar uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878079.aspx)|
| KeyEncrypt      | [Criptografar com uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878060.aspx)|
| KeyDecrypt      | [Descriptografar com uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878097.aspx)|
| KeyUpdate      | [Atualizar uma chave](https://msdn.microsoft.com/en-us/library/azure/dn903616.aspx)|
| KeyList      | [Lista as teclas em um cofre](https://msdn.microsoft.com/en-us/library/azure/dn903629.aspx)|
| KeyListVersions      | [As versões de uma chave de lista](https://msdn.microsoft.com/en-us/library/azure/dn986822.aspx)|
| SecretSet      | [Criar um segredo](https://msdn.microsoft.com/en-us/library/azure/dn903618.aspx)|
| SecretGet      | [Obter secreta](https://msdn.microsoft.com/en-us/library/azure/dn903633.aspx)|
| SecretUpdate      | [Atualizar um segredo](https://msdn.microsoft.com/en-us/library/azure/dn986818.aspx)|
| SecretDelete      | [Excluir um segredo](https://msdn.microsoft.com/en-us/library/azure/dn903613.aspx)|
| SecretList      | [Segredos de lista em um cofre](https://msdn.microsoft.com/en-us/library/azure/dn903614.aspx)|
| SecretListVersions      | [Lista de versões de um segredo](https://msdn.microsoft.com/en-us/library/azure/dn986824.aspx)|




## <a id="next"></a>Próximas etapas ##

Para um tutorial que usa o Azure chave cofre em um aplicativo web, consulte [Usar Azure chave Cofre de um aplicativo Web](key-vault-use-from-web-application.md).

Para programação referências, consulte [Guia do desenvolvedor do Azure chave cofre](key-vault-developers-guide.md).

Para uma lista de cmdlets do PowerShell 1.0 do Azure para Cofre de chave do Azure, consulte [Cmdlets de Cofre de chave do Azure](https://msdn.microsoft.com/library/azure/dn868052.aspx).

Para um tutorial em rotação de chaves e o log de auditoria com Cofre de chave do Azure, veja [como configuração Cofre de chave com a rotação de chaves de ponta a ponta e auditoria](key-vault-key-rotation-log-monitoring.md).
