<properties
    pageTitle="Introdução ao Azure pilha chave cofre | Microsoft Azure"
    description="Começar a usar o Azure pilha chave cofre"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="ricardom"/>


# <a name="getting-started-with-key-vault"></a>Introdução ao Cofre de chave

Esta seção descreve as etapas para criar um cofre, gerenciar chaves e senhas, bem como autorizar usuários ou aplicativos invocar operações no cofre na pilha do Azure. As seguintes etapas pressupõem que existe uma inscrição de locatário e KeyVault serviço está registrado dentro dessa assinatura. Todos os comandos de exemplo se baseiam os cmdlets KeyVault disponíveis como parte do SDK do PowerShell do Azure.

## <a name="enabling-the-tenant-subscription-for-vault-operations"></a>Habilitando a assinatura de locatário para operações de cofre 

Antes que você possa emitir operações contra qualquer cofre, você precisa garantir que sua assinatura estiver habilitada para operações de cofre. Você pode confirmar que por meio do seguinte comando do PowerShell:

    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -AutoSize

A saída do comando acima deve informar "Registrado" para o estado de "Registro" de cada linha.

    ProviderNamespace RegistrationState ResourceTypes Locations
    Microsoft.KeyVault Registered {operations} {local}
    Microsoft.KeyVault Registered {vaults} {local}
    Microsoft.KeyVault Registered {vaults/secrets} {local}
    

 Se não for o caso, você deve chamar o seguinte comando para registrar o serviço de KeyVault dentro de sua assinatura:

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault

E, a seguir é a saída do comando:
    
    ProviderNamespace : Microsoft.KeyVault
    RegistrationState : Registered
    ResourceTypes : {operations, vaults, vaults/secrets}
    Locations : {local}


>[AZURE.NOTE] Se você receber o erro: "*a assinatura não estiver registrada no Azure chave cofre*" ao chamar KeyVault cmdlets, confirme que você ativou o provedor de recursos de KeyVault por instruções acima.


## <a name="creating-a-hardened-container-a-vault-in-azure-stack-to-store-and-manage-cryptographic-keys-and-secrets"></a>Criando um contêiner robusto (um cofre) na pilha do Azure para armazenar e gerenciar segredos e chaves de criptografia

Para criar um cofre, um locatário deve primeiro criar um grupo de recursos. Os seguintes comandos do PowerShell criam um grupo de recursos e um cofre desse grupo de recursos. O exemplo também inclui a saída típica esse cmdlet.

### <a name="creating-a-resource-group"></a>Criando um grupo de recursos:

    New-AzureRmResourceGroup -Name vaultrg010 -Location local -Verbose -Force

Saída:

    VERBOSE: Performing the operation "Replacing resource group ..." on target "".
    VERBOSE: 12:52:51 PM - Created resource group 'vaultrg010' in location 'local'
    ResourceGroupName : vaultrg010
    Location : local
    ProvisioningState : Succeeded
    Tags :
    ResourceId : /subscriptions/fa881715-3802-42cc-a54e-a06adf61584d/resourceGroups/vaultrg010
    

### <a name="creating-a-vault"></a>Criando um cofre:


    New-AzureRmKeyVault -VaultName vault010 -ResourceGroupName vaultrg010 -Location local -Verbose

Saída:

    VaultUri : https://vault010.vault.azurestack.local
    TenantId : 5454420b-2e38-4b9e-8b56-1712d321cf33
    TenantName : 5454420b-2e38-4b9e-8b56-1712d321cf33
    Sku : Standard
    EnabledForDeployment : False
    EnabledForTemplateDeployment : False
    EnabledForDiskEncryption : False
    AccessPolicies : {5454420b-2e38-4b9e-8b56-1712d321cf33}
    AccessPoliciesText :
    Tenant ID : 5454420b-2e38-4b9e-8b56-1712d321cf33
    Object ID : ca342e90-f6aa-435b-a11c-dfe5ef0bfeeb
    Application ID :
    Display Name : Tenant Admin (tenantadmin1@msazurestack.onmicrosoft.com)
    Permissions to Keys : get, create, delete, list, update, import, backup, restore
    Permissions to Secrets : all
    OriginalVault : Microsoft.Azure.Management.KeyVault.Vault
    ResourceId : /subscriptions/fa881715-3802-42cc-a54e-a06adf61584d/resourceGroups/vaultrg010/providers/Microsoft.KeyVault/vaults/vault010
    VaultName : vault010
    ResourceGroupName : vaultrg010
    Location : local
    Tags : {}
    TagsTable :
    
A saída desse cmdlet mostra propriedades do cofre chave que você acabou de criar. As duas propriedades mais importantes são:

-   **Nome do cofre**: no exemplo, isso é **vault010**. Você usará esse nome para outros cmdlets de chave cofre.

-   **URI do cofre**: no exemplo, isso é https://vault010.vault.azurestack.local. Aplicativos que usam seu cofre por meio de sua API REST devem usar esse URI.

Sua conta do Azure agora está autorizada a realizar operações na chave cofre. Ainda, ninguém está.


## <a name="operating-on-keys-and-secrets"></a>Operando em chaves e senhas

Depois de criar um cofre, siga as etapas a seguir para criar, gerenciar chaves e senhas:

### <a name="creating-a-key"></a>Criando uma chave

Para criar uma chave, use a **Adicionar AzureKeyVaultKey** por exemplo abaixo. Após a criação de chave bem-sucedida, o cmdlet produzirá os detalhes importantes recém-criado.

    Add-AzureKeyVaultKey -VaultName \$vaultName -Name\$keyVaultKeyName -Verbose -Destination Software

A seguir é a saída do cmdlet *Add-AzureKeyVaultKey* :

    Attributes : Microsoft.Azure.Commands.KeyVault.Models.KeyAttributes
    Key : {"kid":"https://vault010.vault.azurestack.local/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff","kty":"RSA","key\_ops":\["encrypt"
    ,"decrypt","sign","verify","wrapKey","unwrapKey"\],"n":"nDkcBQCyyLnXtbwFMnXOWzPDqWKiyjf0G3QTxvuN\_MansEn9X-91q4\_WFmRBCd5zWBqz671iuZO\_D4r0P25
    Fe2lAq\_3T1gATVNGR7LTEU9W5h8AoY10bmt4e0y66Jn2vUV-UTCz4\_vtKSKoiuNXHFR\_tGZ-6YX-frqKIiC8pbE4Qvz1x-c7E-eM\_Cpu87koL95n-Hl3wQRQRPXEPRR6gcHR5E74D1
    gLEFCWKySTo4nXtLoeBMNK5QYEBZIAS61ACbR4czjHn6ty-tZeVTc7hyK\_UO2EbJovQIAhyayfq018uNtCBzjjkqJKnY34kviVCPoTQqOdpHa0FHrloe5FeIw","e":"AQAB"}
    VaultName : vault010
    Name : keyVaultKeyName001
    Version : 86062b02b10342688f3b0b3713e343ff
    Id : https://vault010.vault.azurestack.local:443/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff
    
Agora você pode referenciar essa chave que você criou ou carregado Cofre de chave do Azure, usando seu URI. Use **as teclas/https://vault010.vault.azurestack.local:443/keyVaultKeyName001** para obter sempre a versão atual; e usar **https://vault010.vault.azurestack.local:443/chaves/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff** para obter a versão específica.

### <a name="retrieving-a-key"></a>Recuperar uma chave

Use o **Get-AzureKeyVaultKey** para recuperar uma chave e seus detalhes por exemplo a seguir:

    Get-AzureKeyVaultKey -VaultName vault010 -Name keyVaultKeyName001

O exemplo a seguir é a saída de Get-AzureKeyVaultKey

    Attributes : Microsoft.Azure.Commands.KeyVault.Models.KeyAttributes
    Key : {"kid":"https://vault010.vault.azurestack.local/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff","kty":"RSA","key\_ops":\["encrypt"
    ,"decrypt","sign","verify","wrapKey","unwrapKey"\],"n":"nDkcBQCyyLnXtbwFMnXOWzPDqWKiyjf0G3QTxvuN\_MansEn9X-91q4\_WFmRBCd5zWBqz671iuZO\_D4r0P25
    Fe2lAq\_3T1gATVNGR7LTEU9W5h8AoY10bmt4e0y66Jn2vUV-UTCz4\_vtKSKoiuNXHFR\_tGZ-6YX-frqKIiC8pbE4Qvz1x-c7E-eM\_Cpu87koL95n-Hl3wQRQRPXEPRR6gcHR5E74D1
    gLEFCWKySTo4nXtLoeBMNK5QYEBZIAS61ACbR4czjHn6ty-tZeVTc7hyK\_UO2EbJovQIAhyayfq018uNtCBzjjkqJKnY34kviVCPoTQqOdpHa0FHrloe5FeIw","e":"AQAB"}
    VaultName : vault010
    Name : keyVaultKeyName001
    Version : 86062b02b10342688f3b0b3713e343ff
    Id : https://vault010.vault.azurestack.local:443/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff

### <a name="setting-a-secret"></a>Definindo um segredo

    $secretvalue = ConvertTo-SecureString 'User@123' -AsPlainText -Force
    Set-AzureKeyVaultSecret -Name MySecret-VaultName vault010 -SecretValue $secretvalue
    
Saída

    Vault Name : vault010
    Name : MySecret
    Version : 65a387f2ed4a416180e852b970846f5b
    Id : https://vault010.vault.azurestack.local:443/secrets/MySecret/65a387f2ed4a416180e852b970846f5b
    Enabled : True
    Expires :
    Not Before :
    Created : 8/17/2016 10:49:52 PM
    Updated : 8/17/2016 10:49:52 PM
    Content Type :
    Tags : 

### <a name="retrieving-a-secret"></a>Recuperando um segredo

    Get-AzureKeyVaultSecret -VaultName vault010

Saída

    Vault Name : vault010
    Name : MySecret
    Version :
    Id : https://vault010.vault.azurestack.local:443/secrets/MySecret
    Enabled : True
    Expires :
    Not Before :
    Created : 8/17/2016 10:49:52 PM
    Updated : 8/17/2016 10:49:52 PM
    Content Type :
    Tags :

Agora, sua chave cofre e chave ou segredo está pronto para aplicativos para usar.
Você deve autorizar aplicativos usá-las.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorize o aplicativo para usar a tecla ou segredo 

Para autorizar o aplicativo para acessar a chave ou segredo no cofre, use o cmdlet Set -**AzureRmKeyVaultAccessPolicy** .

Por exemplo, se o nome do cofre é *ContosoKeyVault* e o aplicativo que você deseja autorizar tem uma *ID do cliente* de *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*e você deseja autorizar o aplicativo para descriptografar e entrar com chaves em seu cofre, execute o seguinte:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Se você quiser autorizar aplicativo mesmo ler segredos no seu cofre, execute o seguinte:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get


## <a name="next-steps"></a>Próximas etapas
[Implantar uma máquina virtual com uma senha de Cofre de chave](azure-stack-kv-deploy-vm-with-secret.md)

[Implantar uma máquina virtual com um certificado de chave cofre](azure-stack-kv-push-secret-into-vm.md)