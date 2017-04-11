<properties
   pageTitle="Gerenciar os registros DNS reverso para os serviços do Azure (clássico) usando o PowerShell | Microsoft Azure"
   description="Como gerenciar os registros DNS reverso ou registros PTR para os serviços do Azure usando o PowerShell no modelo clássico de implantação. "
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/28/2016"
   ms.author="smalone" />

# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-classic-using-azure-powershell"></a>Como gerenciar os registros DNS reverso dos seus serviços Azure (clássico) usando o PowerShell do Azure

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Saiba como [executar essas etapas usando o modelo do Gerenciador de recursos](dns-reverse-dns-record-operations-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Validação de registros DNS reverso
Para garantir que um terceiro não é possível criar registros DNS reverso mapeamento até seus domínios DNS, o Azure só permite a criação de um registro DNS reverso onde uma das seguintes opções for verdadeira:

- O inverso FQDN do DNS é o nome do serviço de nuvem para o qual ele foi especificado, ou qualquer nome de serviço de nuvem dentro da mesma assinatura por exemplo, DNS reverso é "contosoapp1.cloudapp.net.".
- O FQDN do DNS reverso encaminhar resolve para o nome ou IP do serviço de nuvem para que ele foi especificado ou qualquer nome de serviço de nuvem ou IP dentro da mesma assinatura por exemplo, DNS reverso é "app1.contoso.com". o que é um alias de CName para contosoapp1.cloudapp.net.

Verificações de validação são executadas somente quando a propriedade DNS reversa para um serviço de nuvem está definida ou modificada. Validação de re periódica não é executada.

## <a name="add-reverse-dns-to-existing-cloud-services"></a>Adicione DNS reverso aos serviços de nuvem existente
Você pode adicionar um registro DNS reverso para um serviço de nuvem existente usando o cmdlet "Set-AzureService":

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="create-a-cloud-service-with-reverse-dns"></a>Criar um serviço de nuvem com DNS reverso
Você pode adicionar um novo serviço de nuvem com a propriedade DNS reversa especificada usando o cmdlet "Set-AzureService":

    PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="view-reverse-dns-for-existing-cloud-services"></a>Modo de exibição DNS reverso para serviços de nuvem existentes
Você pode exibir o valor configurado para um serviço de nuvem existente usando o cmdlet "Get-AzureService":

    PS C:\> Get-AzureService "contosoapp1"

## <a name="remove-reverse-dns-from-existing-cloud-services"></a>Remover o DNS reverso existente dos serviços de nuvem
Você pode remover uma propriedade DNS reversa de um serviço de nuvem existente usando o cmdlet "Set-AzureService". Isso é feito definindo o valor da propriedade DNS reverso em branco:

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[AZURE.INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[AZURE.INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]
