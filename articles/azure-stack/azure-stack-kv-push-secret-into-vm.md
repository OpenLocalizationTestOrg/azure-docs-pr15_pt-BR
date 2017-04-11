<properties
    pageTitle="Implantar uma máquina virtual com um certificado usando Azure pilha chave cofre | Microsoft Azure"
    description="Saiba como implantar uma máquina virtual e inserir um certificado do Azure pilha chave cofre"
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
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="create-vms-and-include-certificates-retrieved-from-key-vault"></a>Criar VMs e incluir certificados recuperados do Cofre de chave

Na pilha do Azure, VMs são implantadas por meio do Gerenciador de recursos do Azure e agora você pode armazenar certificados no Azure pilha chave cofre. Pilha de Azure (provedor de Microsoft.Compute recurso seja específico) envia-los em suas VMs quando as VMs são implantadas. Certificados podem ser usados em muitos cenários, incluindo SSL, criptografia e autenticação de certificado baseado.

Usando esse método, você pode manter o certificado de segurança. Agora é não na imagem de máquina virtual ou em arquivos de configuração do aplicativo ou algum outro local não seguro. Definindo a política de acesso apropriado para o cofre chave, você também pode controlar quem obterá acesso ao seu certificado. Outro benefício é que você pode gerenciar todos os certificados em um só lugar no Azure pilha chave cofre.

Aqui está uma rápida visão geral do processo:

-   É necessário um certificado no formato. pfx.

-   Crie um cofre chave (usando um modelo ou o script de exemplo a seguir).

-   Verifique se que você ativou a opção EnabledForDeployment.

-   Carregue o certificado como um segredo.

## <a name="deploying-vms"></a>Implantando VMs

Esse script de exemplo cria um cofre chave e, em seguida, armazena um certificado armazenado no arquivo. pfx em um diretório local, o cofre chave como um segredo.

    $vaultName = "contosovault"
    $resourceGroup = "contosovaultrg"
    $location = "local"
    $secretName = "servicecert"
    $fileName = "keyvault.pfx"
    $certPassword = "abcd1234"

    # =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

    $fileContentBytes = get-content \$fileName -Encoding Byte

    $fileContentEncoded =
    [System.Convert\]::ToBase64String(\$fileContentBytes)
    $jsonObject = @"
    {
    "data": "\$filecontentencoded",
    "dataType" :"pfx",
    "password": "\$certPassword"
    }
    @$jsonObjectBytes = [System.Text.Encoding\]::UTF8.GetBytes(\$jsonObject)
    $jsonEncoded = \[System.Convert\]::ToBase64String(\$jsonObjectBytes)
    Switch-AzureMode -Name AzureResourceManager
    New-AzureResourceGroup -Name \$resourceGroup -Location \$location
    New-AzureKeyVault -VaultName \$vaultName -ResourceGroupName
    $resourceGroup -Location \$location -sku standard -EnabledForDeployment
    $secret = ConvertTo-SecureString -String \$jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName \$vaultName -Name \$secretName -SecretValue \$secret

A primeira parte do script lê o arquivo. pfx e armazena-a como um JSON objeto com o arquivo conteúdo codificação base64. Em seguida, o objeto JSON também é codificada em base64.

Em seguida, cria um novo grupo de recursos e, então, cria um cofre chave. Observe o último parâmetro para o comando New-AzureKeyVault, '-EnabledForDeployment', que concede acesso ao Azure (especificamente para o provedor de recursos de Microsoft.Compute) para ler segredos da chave do cofre para implantações.

O último comando simplesmente armazena o objeto JSON de codificação base64 no cofre chave como um segredo.

Eis um exemplo de saída do script anterior:

    VERBOSE:  – Created resource group 'contosovaultrg' in
    location
    'eastus'
    ResourceGroupName : contosovaultrg
    Location          : eastus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions NotActions
                        ======= ==========
                        \*
    ResourceId        :
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-dd149b4aeb56/re
    sourceGroups/contosovaultrg
    VaultUri             : https://contosovault.vault.azure.net
    TenantId             : xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47
    TenantName           : xxxxxxxx
    Sku                  : standard
    EnabledForDeployment : True
    AccessPolicies       : {xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47}
    AccessPoliciesText   :
                           Tenant ID              :
                           xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47
                           Object ID              :
                           xxxxxxxx-xxxx-xxxx-xxxx-b092cebf0c80
                           Application ID         :
                           Display Name           : Derick Developer  (derick@contoso.com)
                           Permissions to Keys    : get, create, delete,
                           list, update, import, backup, restore
                           Permissions to Secrets : all
    OriginalVault        : Microsoft.Azure.Management.KeyVault.Vault
    ResourceId           :
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-dd149b4aeb56                 
    /resourceGroups/contosovaultrg/providers/Microsoft.KeyV
                           ault/vaults/contosovault
    VaultName            : contosovault
    ResourceGroupName    : contosovaultrg
    Location             : eastus
    Tags                 : {}
    TagsTable            :
    SecretValue     : System.Security.SecureString
    SecretValueText :
    ew0KImRhdGEiOiAiTUlJSkN3SUJBekNDQ01jR0NTcUdTSWIzRFFFSEFh
    Q0NDTGdFZ2dpME1JSUlzRENDQmdnR0NTcUdTSWIzRFFFSEFhQ0NCZmtF           
    Z2dYMU1JSUY4VENDQmUwR0N5cUdTSWIzRFFFTUNnRUNvSUlFL2pDQ0JQ
    &lt;&lt;&lt; Output truncated… &gt;&gt;&gt;
    Attributes      :
    Microsoft.Azure.Commands.KeyVault.Models.SecretAttributes
    VaultName       : contosovault
    Name            : servicecert
    Version         : e3391a126b65414f93f6f9806743a1f7
    Id              :
    https://contosovault.vault.azure.net:443/secrets/servicecert
                      /e3391a126b65414f93f6f9806743a1f7

Agora você está pronto para implantar um modelo de máquina virtual. Observe o URI do segredo da saída (conforme realçado na saída anterior em verde).

Você precisará de um modelo localizado aqui. Os parâmetros de interesse especial (além os parâmetros de máquina virtual usuais) são o nome do cofre, o grupo de recursos do cofre e o URI secreto. Claro, você também pode baixá-lo do GitHub e modificar conforme necessário.

Quando esta máquina virtual for implantado, o Azure insere o certificado para a máquina virtual.
No Windows, certificados no arquivo. pfx são adicionados com a chave privada não. O certificado é adicionado para o local do certificado LocalMachine, com o armazenamento de certificado que o usuário fornecido. No Linux, o arquivo de certificado é colocado no diretório /var/lib/waagent, com o nome de arquivo &lt;UppercaseThumbprint&gt;. CRT para o X509 arquivo de certificado, e &lt;UppercaseThumbprint&gt;.prv para a chave particular.
Ambos os arquivos estão .pem formatado.

O aplicativo geralmente localiza o certificado usando a impressão digital e não exige modificação.

## <a name="retiring-certificates"></a>Cancelando certificados


Na seção anterior, mostrar como enviar um novo certificado para suas VMs existentes. Mas seu certificado antigo ainda está na máquina virtual e não pode ser removido. Para maior segurança, você pode alterar o atributo de segredo antigo como 'Desativado', para que mesmo que um modelo antigo tenta criar uma máquina virtual com esta versão antiga do certificado, ele será. Aqui está como você definir uma versão específica de secreta a ser desativado:

    Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0

## <a name="conclusion"></a>Conclusão


Com esse novo método, o certificado pode ser mantido separado da imagem de máquina virtual ou a carga de aplicativo. Portanto removemos um ponto de exposição.

O certificado também pode ser renovado e carregado Cofre de chave sem precisar criar novamente a imagem de máquina virtual ou o pacote de implantação do aplicativo. O aplicativo ainda precisa ser fornecido com o novo URI para essa nova versão de certificado apesar.

Separando o certificado da máquina virtual ou a carga de aplicativo, podemos agora reduzida o número de pessoas que terão acesso direto para o certificado.

Como um benefício extra, agora você tem um local conveniente em Cofre de chave para gerenciar todos os certificados, incluindo as todas as versões que foram implantadas ao longo do tempo.

## <a name="next-steps"></a>Próximas etapas

[Implantar uma máquina virtual com uma senha de Cofre de chave](azure-stack-kv-deploy-vm-with-secret.md)

[Permitir que um aplicativo acessar Cofre de chave](azure-stack-kv-sample-app.md)