<properties
    pageTitle="Restaurar chave de chave cofre e segredo para VMs criptografadas usando o Backup do Azure | Microsoft Azure"
    description="Saiba como restaurar chave cofre chave e o segredo em Backup Azure usando o PowerShell"
    services="backup"
    documentationCenter=""
    authors="JPallavi"
    manager="vijayts"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="JPallavi" />

# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Restaurar chave de chave cofre e segredo para VMs criptografadas usando o Backup do Azure
Este artigo fala sobre como usar o Backup de máquina virtual do Azure para executar restauração de VMs criptografada do Azure, se sua chave e segredo não existir no cofre de chave. Estas etapas também podem ser usadas se você quiser manter uma cópia separada da chave (chave de criptografia) e segredo (chave de criptografia BitLocker) para a máquina virtual restaurada.

## <a name="pre-requisites"></a>Pré-requisitos

1. **Backup criptografado VMs** - criptografados Azure VMs tenha sido feito o backup usando o Backup do Azure. Consulte o artigo [Gerenciar backup e restauração de VMs Azure usando o PowerShell](backup-azure-vms-automation.md) para obter detalhes sobre como fazer backup de VMs criptografada do Azure.

2. **Configurar o Azure chave cofre** – Certifique-se de chave cofre que precisam ser restaurados chaves e senhas já está presente. Consulte o artigo [Introdução ao Azure chave cofre](../key-vault/key-vault-get-started.md) para obter detalhes sobre o gerenciamento de chave cofre.

## <a name="setup-recovery-services-vault"></a>Compartimento de serviços de recuperação de configuração 
Use as seguintes etapas para efetuar login no PowerShell e definir o contexto de Cofre de serviços de recuperação

### <a name="log-in-to-azure-powershell"></a>Faça logon no Azure PowerShell 

Faça logon conta do Microsoft Azure usando o seguinte cmdlet

```
PS C:\> Login-AzureRmAccount
```

### <a name="set-recovery-services-vault-context"></a>Contexto de Cofre de serviços de recuperação de conjunto

Depois de conectado, use o cmdlet a seguir para obter a lista das suas assinaturas disponíveis

```
PS C:\> Get-AzureRmSubscription
```

Selecione a assinatura nas quais recursos estão disponíveis

```
PS C:\> Set-AzureRmContext -SubscriptionId "<subscription-id>"
```

Definir o contexto de cofre usando Cofre de serviços de recuperação onde o backup foi habilitado para VMs criptografadas

```
PS C:\> Get-AzureRmRecoveryServicesVault -ResourceGroupName "<rg-name>" -Name "<rs-vault-name>" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="get-recovery-point"></a>Obter o ponto de recuperação 

Selecione o recipiente no cofre que representa criptografada máquina virtual Azure

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "<vm-name>"
```

Usando esse contêiner, voltar item para a máquina virtual correspondente para cima

```
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
```

Obter uma matriz de pontos de recuperação para o item selecionado de backup no rp variável

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-encrypted-virtual-machine"></a>Restaurar criptografada máquina virtual
Use as seguintes etapas para restaurar VM criptografada, sua chave e o segredo.

### <a name="restore-key"></a>Restaurar chave

A matriz $rp acima, é classificada em ordem inversa de tempo com o ponto de recuperação mais recente no índice 0. Por exemplo: $rp [0] seleciona o último ponto de recuperação.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation "C:\Users\downloads"
```

> [AZURE.NOTE]
Depois que esse cmdlet é executado com êxito, um arquivo blob obtém gerado na pasta especificada na máquina onde ele é executado. Esse arquivo blob representa chave criptografadas em forma criptografada.

Restaure chave voltar ao Cofre de chave usando o seguinte cmdlet. 

```
PS C:\> Restore-AzureKeyVaultKey -VaultName "contosokeyvault" -InputFile "C:\Users\downloads\key.blob"
```

### <a name="restore-secret"></a>Restaurar segredo

Restaurar dados secretos de ponto de recuperação obtido acima

```
PS C:\> $rp1.KeyAndSecretDetails.SecretUrl

https://contosokeyvault.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/20aaae9eaa99996d89d99a29990d999a
```

> [AZURE.NOTE]
O texto antes vault.azure.net representa o nome original do cofre chave. O texto após segredos / representa nome secreta. 

Obtenha o nome secreta e valor da saída do cmdlet executar acima, caso você queira usar o mesmo nome secreto. Em outros casos, $secretname abaixo devem ser atualizados para usar o novo nome secreto. 

```
PS C:\> $secretname = "B3284AAA-DAAA-4AAA-B393-60CAA848AAAA"
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
```

Definir marcas para o segredo, no caso de máquina virtual precisa ser restaurado também. Para a marca DiskEncryptionKeyFileName, o valor deve conter nome do segredo que você planeja usar. 

```
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://contosokeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
```

> [AZURE.NOTE]
Valor DiskEncryptionKeyFileName é igual ao nome secreta obtido acima. Valor de DiskEncryptionKeyEncryptionKeyURL pode ser obtido do cofre chave após restaurar as chaves novamente e usando o cmdlet [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx)   

Definir secreta voltar à chave cofre

```
PS C:\> Set-AzureKeyVaultSecret -VaultName "contosokeyvault" -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  "Wrapped BEK"
```

### <a name="restore-virtual-machine"></a>Restaurar máquina virtual
Cmdlets do PowerShell acima ajudá-lo restaurar chave e secreta voltar ao Cofre de chave, se você tiver feito backup criptografada máquina virtual usando o Backup de máquina virtual do Azure. Depois de restaurá-los, consulte o artigo [Gerenciar backup e restauração de VMs Azure usando o PowerShell](backup-azure-vms-automation.md) para restaurar VMs criptografadas.
