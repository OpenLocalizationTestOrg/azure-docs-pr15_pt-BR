## <a name="setting-up-powershell-for-resource-manager-templates"></a>Configurar o PowerShell para modelos do Gerenciador de recursos

Antes de poder usar o PowerShell do Azure com o Gerenciador de recursos, você precisará ter as versões de PowerShell do Azure e à direita do Windows PowerShell.

### <a name="verify-powershell-versions"></a>Verificar versões do PowerShell

Verifique se que você tem o Windows PowerShell versão 3.0 ou 4.0. Para localizar a versão do Windows PowerShell, digite este comando no prompt de comando do Windows PowerShell.

    $PSVersionTable

Você receberá o seguinte tipo de informações:

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2


Verifique se o valor de **PSVersion** é 3.0 ou 4.0. Caso contrário, consulte [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

### <a name="set-your-azure-account-and-subscription"></a>Configurar sua conta do Azure e assinatura

Se você ainda não tiver uma assinatura do Azure, você pode ativar seus [benefícios de assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou sinal para cima, para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

Abra um prompt de comando do PowerShell do Azure e faça logon no Azure com este comando.

    Login-AzureRmAccount

Se você tiver várias assinaturas Azure, você pode listar suas assinaturas Azure com este comando.

    Get-AzureRmSubscription

Você receberá o seguinte tipo de informações:

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName :
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Você pode definir a assinatura do Azure atual executando esses comandos no prompt de comando do PowerShell do Azure. Substituir tudo entre aspas, incluindo o < e > caracteres, com o nome correto.

    $subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
    Select-AzureRmSubscription -SubscriptionName $subscr -Current

Para obter mais informações sobre assinaturas do Azure e contas, consulte [como: conectar-se à sua assinatura](powershell-install-configure.md#Connect).
