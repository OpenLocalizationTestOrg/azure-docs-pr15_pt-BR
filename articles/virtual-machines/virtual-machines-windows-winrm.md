<properties
    pageTitle="Configuração de acesso de WinRM para máquinas virtuais no Gerenciador de recursos do Azure | Microsoft Azure"
    description="Como configurar o acesso de WinRM para uso com uma máquina virtual de Gerenciador de recursos do Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/16/2016"
    ms.author="singhkay"/>

# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Configuração de acesso de WinRM para máquinas virtuais no Gerenciador de recursos do Azure

## <a name="winrm-in-azure-service-management-vs-azure-resource-manager"></a>WinRM no gerenciamento de serviço do Azure versus do Gerenciador de recursos do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico

* Para obter uma visão geral do Gerenciador de recursos do Azure, consulte este [artigo](../azure-resource-manager/resource-group-overview.md)
* Para diferenças entre o gerenciamento de serviço do Azure e Gerenciador de recursos do Azure, consulte este [artigo](../resource-manager-deployment-model.md)

A diferença chave na configuração de WinRM entre as duas pilhas é como o certificado é instalado na máquina virtual. Na pilha do Gerenciador de recursos do Azure, os certificados são modelados como recursos gerenciados pelo provedor chave cofre recurso. Portanto, o usuário precisa fornecer seu próprio certificado e carregue-o em um cofre de chave antes de usá-lo em uma máquina virtual.

Aqui estão as etapas que necessárias para configurar uma máquina virtual com conectividade WinRM

1. Criar um cofre chave
2. Criar um certificado autoassinado
3. Carregar seu certificado auto-assinado Cofre de chave
4. Obter a URL do seu certificado auto-assinado no cofre de chave
5. Fazer referência a URL de certificados autoassinados ao criar uma máquina virtual

## <a name="step-1-create-a-key-vault"></a>Etapa 1: Criar um cofre chave

Você pode usar o abaixo de comando para criar o Cofre de chave

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Etapa 2: Criar um certificado autoassinado
Você pode criar um certificado autoassinado usando esse script PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Etapa 3: Carregar seu certificado auto-assinado ao Cofre de chave

Antes de carregar o certificado ao Cofre chave criado na etapa 1, ele precisa convertidos em um formato que o provedor de recursos Microsoft.Compute compreendam. O abaixo PowerShell script permitirá que você faça isso

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Etapa 4: Obter a URL do seu certificado auto-assinado no cofre de chave

O provedor de recursos Microsoft.Compute precisa de uma URL para o segredo dentro do cofre chave ao provisionar a máquina virtual. Isso permite que o provedor de recursos de Microsoft.Compute baixar o segredo e criar o certificado equivalente na máquina virtual.

>[AZURE.NOTE]A URL do segredo precisa incluir a versão também. Uma URL de exemplo se parece com abaixo https://contosovault.vault.azure.net:443/segredos/contososecret/01h9db0df2cd4300a20ence585a6s7ve


#### <a name="templates"></a>Modelos

Você pode obter o link para a URL no modelo usando o abaixo código

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell

Você pode obter esta URL usando o abaixo de comando do PowerShell

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Etapa 5: Sua URL de certificados autoassinados de referência ao criar uma máquina virtual

#### <a name="azure-resource-manager-templates"></a>Modelos de Gerenciador de recursos do Azure

Ao criar uma máquina virtual por meio de modelos, o certificado obtém referenciado na seção segredos e a seção winRM conforme abaixo:

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Um modelo de amostra para acima pode ser encontrado aqui no [201-máquina virtual-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Código de origem para esse modelo pode ser encontrado no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell

    $vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Etapa 6: Conectar-se para a máquina virtual
Antes que você possa se conectar para a máquina virtual será necessário para garantir que seu computador está configurado para gerenciamento remoto WinRM. Iniciar o PowerShell como administrador e execute o abaixo comando para garantir que tudo estiver configurado.

    Enable-PSRemoting -Force

>[AZURE.NOTE] Talvez seja necessário certificar-se de que o serviço WinRM executando se acima não funciona. Você pode fazer que usando`Get-Service WinRM`

Quando a instalação estiver concluída, você pode se conectar à máquina virtual usando o abaixo comando

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate