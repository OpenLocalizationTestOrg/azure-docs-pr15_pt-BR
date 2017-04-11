<properties
    pageTitle="Serviços de domínio Active Directory do Azure: Guia de administração | Microsoft Azure"
    description="Ingresse em uma máquina virtual Windows a um domínio gerenciado usando o PowerShell do Azure e o modelo de implantação clássico."
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="maheshu"/>


# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-powershell"></a>Ingressar em uma máquina de virtual do Windows Server para um domínio gerenciado usando o PowerShell

> [AZURE.SELECTOR]
- [Portal clássico Azure - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

> [AZURE.IMPORTANT] Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de recursos e clássico](../resource-manager-deployment-model.md). Este artigo aborda usando o modelo clássico de implantação. Os serviços de domínio do Azure AD atualmente não oferece suporte para o modelo do Gerenciador de recursos.

Estas etapas mostram como personalizar um conjunto de comandos do PowerShell do Azure que criam e predefinir uma máquina virtual baseado no Windows Azure usando uma abordagem de bloco de construção. Estas etapas o ajudará a construir uma máquina virtual baseado no Windows Azure e ingressar em um domínio de serviços de domínio do Azure AD gerenciado.

Estas etapas siga uma abordagem de preencher apaga para a criação de conjuntos de comandos do PowerShell do Azure. Essa abordagem pode ser útil se você estiver começando a usar o PowerShell ou desejar saber quais valores para especificar uma configuração bem-sucedida. Usuários avançados do PowerShell podem levar os comandos e substituir seus próprios valores para as variáveis (as linhas que começam com "$").

Se você ainda não fez isso, use as instruções em [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para instalar o Azure PowerShell no computador local. Em seguida, abra um prompt de comando do Windows PowerShell.

## <a name="step-1-add-your-account"></a>Etapa 1: Adicionar sua conta

1. No prompt do PowerShell, digite **AzureAccount de adicionar** e clique em **Inserir**.
2. Digite o endereço de email associado a sua assinatura do Azure e clique em **continuar**.
3. Digite a senha da sua conta.
4. Clique em **entrar**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Etapa 2: Configurar uma conta de armazenamento e de sua assinatura

Defina sua assinatura do Azure e conta de armazenamento executando esses comandos no prompt de comando do Windows PowerShell. Substituir tudo entre aspas, incluindo o < e > caracteres, com os nomes corretos.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Você pode obter o nome da assinatura correta da propriedade nome de inscrição da saída do comando **Get-AzureSubscription** . Você pode obter o nome da conta de armazenamento correto da propriedade rótulo da saída do comando **Get-AzureStorageAccount** depois de executar o comando **Selecionar AzureSubscription** .


## <a name="step-3-step-by-step-walkthrough---provision-the-virtual-machine-and-join-it-to-the-managed-domain"></a>Etapa 3: Passo a passo - provisionar a máquina virtual e ingressar em domínio gerenciado
Veja aqui o comando do PowerShell do Azure correspondente definido para criar esta máquina virtual, com linhas em branco entre cada bloco para fins de legibilidade.

Especifique informações sobre o computador de virtual do Windows para ser provisionado.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

Para os valores InstanceSize D-, DS ou máquinas virtuais de série G, consulte [Máquina Virtual e tamanhos de serviço de nuvem do Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Fornece informações sobre o domínio gerenciado.

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

Especifique o nome do serviço de nuvem.

    $svcname="Contoso100-test"

Especifique o nome da rede virtual à qual a máquina virtual deve estar unida. Certifique-se de que o domínio gerenciado AAD-DS está disponível nesta rede virtual.

    $vnetname="MyPreviewVnet"

Selecione a imagem de máquina virtual a ser usado para provisionar a máquina virtual.

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Configure a máquina virtual - nome do conjunto de máquina virtual, tamanho da instância & imagem a ser usada.

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Obtenha as credenciais de administrador local para a máquina virtual. Escolha uma senha de administrador local fortes.

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

Obtenha as credenciais para uma conta de usuário pertencente ao grupo 'Administradores de DC AAD' ingressar máquina virtual para o domínio gerenciado. Não especificar o nome de domínio - por exemplo, em nosso exemplo, podemos especificar 'Paulo' como o nome de usuário.

    $domainadmincred=Get-Credential –Message "Now type the name (DO NOT INCLUDE THE DOMAIN) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

Configurar a máquina virtual - especificar o requisito de junção de domínio e credenciais necessárias.

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

Defina uma sub-rede para a máquina virtual.

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

Opcional: Aponte para o endereço IP do domínio. Se você definir os endereços IP do domínio gerenciado serviços de domínio do Azure AD sejam os servidores DNS para a rede virtual, esta etapa não é necessária.

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

Agora, provisione a máquina virtual domínio Windows.

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="script-to-provision-a-windows-vm-and-automatically-join-it-to-an-aad-domain-services-managed-domain"></a>Script para provisionar uma máquina virtual Windows e automaticamente ingressar em um domínio gerenciado AAD serviços de domínio
Este conjunto de comandos do PowerShell cria uma máquina virtual para um servidor de linha de negócios que:

- Usa a imagem do Windows Server 2012 R2 data center.
- É uma máquina virtual extra pequena.
- Tem o nome contoso-teste.
- É automaticamente domínio associado ao contoso100 gerenciado domínio.
- É adicionado à mesma rede virtual domínio gerenciado.

Aqui está o script de exemplo completo para criar o computador virtual Windows e ingressá-la automaticamente ao domínio gerenciado dos serviços de domínio do Azure AD.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

    $svcname="Contoso100-test"
    $vnetname="MyPreviewVnet"

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

    $domainadmincred=Get-Credential –Message "Now type the name (not including the domain) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="related-content"></a>Conteúdo relacionado
- [Serviços de domínio Azure AD - guia de Introdução](./active-directory-ds-getting-started.md)

- [Administrar um domínio gerenciado de serviços de domínio do Azure AD](./active-directory-ds-admin-guide-administer-domain.md)
