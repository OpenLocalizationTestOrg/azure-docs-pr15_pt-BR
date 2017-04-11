<properties
   pageTitle="Gerenciar os registros DNS reverso dos seus serviços Azure usando o PowerShell | Microsoft Azure"
   description="Como gerenciar os registros DNS reverso ou registros PTR para os serviços do Azure usando o PowerShell no Gerenciador de recursos"
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/28/2016"
   ms.author="smalone" />

# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-using-azure-powershell"></a>Como gerenciar registros DNS reverso para seus serviços Azure usando o PowerShell do Azure

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implantação clássico](dns-reverse-dns-record-operations-classic-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Validação de registros DNS reverso
Para garantir que um terceiro não é possível criar registros DNS reverso mapeamento até seus domínios DNS, o Azure só permite a criação de um registro DNS reverso onde uma das seguintes opções for verdadeira:

- O "ReverseFqdn" é o mesmo que o "Fqdn" para o recurso de endereço IP público para o qual ele foi especificado ou o "Fqdn" para qualquer endereço IP público dentro da mesma assinatura por exemplo, "ReverseFqdn" é "contosoapp1.northus.cloudapp.azure.com.".

- O "ReverseFqdn" encaminhar resolve para o nome ou o IP do endereço IP público para que ele foi especificado, ou para qualquer endereço IP público "Fqdn" ou IP dentro da mesma assinatura por exemplo, "ReverseFqdn" é "app1.contoso.com". qual é um alias de CName para "contosoapp1.northus.cloudapp.azure.com."

Verificações de validação são executadas somente quando a propriedade DNS reversa para um endereço IP público está definida ou modificada. Validação de re periódica não é executada.

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>Adicione DNS reverso existentes endereços IP públicos
Você pode adicionar reversa de DNS para um endereço IP público existente usando o cmdlet "Set-AzureRmPublicIpAddress":

    PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
    PS C:\> $pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
    PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip

Se desejar adicionar reversa de DNS para um endereço IP público existente que ainda não tenha um nome de DNS, você também deve especificar um nome de DNS. Você pode adicionar a fazer isso usando o cmdlet "Set-AzureRmPublicIpAddress":

    PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
    PS C:\> $pip.DnsSettings = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings
    PS C:\> $pip.DnsSettings.DomainNameLabel = "contosoapp1"
    PS C:\> $pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
    PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip

## <a name="create-a-public-ip-address-with-reverse-dns"></a>Criar um endereço IP público com DNS reverso
Você pode adicionar um novo endereço IP público com a propriedade DNS reversa especificada usando o cmdlet "New-AzureRmPublicIpAddress":

    PS C:\> New-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS -Location WestUS -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>Modo de exibição reversa de DNS para os endereços IP públicos existente
Você pode exibir o valor configurado para um endereço IP público existente usando o cmdlet "Get-AzureRmPublicIpAddress":

    PS C:\> Get-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Remover DNS inversa de endereços IP públicos existentes
Você pode remover uma propriedade DNS reversa de um endereço IP público existente usando o cmdlet "Set-AzureRmPublicIpAddress". Isso é feito definindo o valor da propriedade ReverseFqdn em branco:

    PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
    PS C:\> $pip.DnsSettings.ReverseFqdn = ""
    PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip


[AZURE.INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[AZURE.INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]
