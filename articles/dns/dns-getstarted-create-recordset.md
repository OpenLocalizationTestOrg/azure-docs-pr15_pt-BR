<properties
   pageTitle="Criar um conjunto de registros e registros para uma zona DNS usando o PowerShell | Microsoft Azure"
   description="Como criar registros de host DNS do Azure. Configuração do registro define e registros usando o PowerShell"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>



# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>Criar conjuntos de registros de DNS e registros usando o PowerShell


> [AZURE.SELECTOR]
- [Portal do Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [CLI Azure](dns-getstarted-create-recordset-cli.md)

Este artigo conduz você pelo processo de criação de registros e conjuntos de registros usando o Windows PowerShell. Depois de criar sua zona DNS, você pode adicionar os registros DNS do seu domínio. Para fazer isso, primeiro é necessário entender os registros DNS e conjuntos de registros.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="verify-that-you-have-the-latest-version-of-powershell"></a>Verifique se você tem a versão mais recente do PowerShell

Verifique se você instalou a versão mais recente do Azure Gerenciador de recursos de cmdlets do PowerShell. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.

## <a name="create-a-record-set-and-record"></a>Criar um conjunto de registros e registro

Esta seção descreve como criar um conjunto de registro e registro.


### <a name="1-connect-to-your-subscription"></a>1. se conectar à sua assinatura

Abra o console do PowerShell e conectar à sua conta. Use o exemplo a seguir para ajudá-lo a se conectar:

    Login-AzureRmAccount

Verifique as assinaturas para a conta.

    Get-AzureRmSubscription

Especifica a assinatura que você deseja usar.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

Para obter mais informações sobre como trabalhar com o PowerShell, consulte [Usando o Windows PowerShell com o Gerenciador de recursos](../powershell-azure-resource-manager.md).


### <a name="2-create-a-record-set"></a>2. Crie um conjunto de registros

Criar conjuntos de registros usando o `New-AzureRmDnsRecordSet` cmdlet. Ao criar um conjunto de registros, você precisa especificar que o registro define nome, a zona, o tempo de vida útil (TTL) e o tipo de registro.

Para criar um conjunto de registros na apex da zona (neste caso, "contoso.com"), use o nome do registro "@", incluindo as aspas. Isso é uma convenção DNS comuns.

O exemplo a seguir cria um registro definido com o nome relativo "www" na zona DNS "contoso.com". O nome totalmente qualificado do conjunto de registros é "www.contoso.com". O tipo de registro é "A", e o TTL é 60 segundos. Depois de concluir essa etapa, você terá um conjunto de registro vazio "www" que está atribuído a variável *$rs*.

    $rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

#### <a name="if-a-record-set-already-exists"></a>Se um registro definir já existe

Se um registro definir já existir, o comando falhará, a menos que o *-Substituir* opção é usada. O *-Substituir* opção aciona um prompt de confirmação, que pode ser eliminado usando o *-força* alternar.


    $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]


Neste exemplo, você pode especificar a zona usando o nome da zona e o nome do grupo de recursos. Como alternativa, você pode especificar um objeto de zona, conforme retornado por `Get-AzureRmDnsZone` ou `New-AzureRmDnsZone`.

    $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
    $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

`New-AzureRmDnsRecordSet`Retorna um objeto local que representa o conjunto de registro que foi criado no Azure DNS.

### <a name="3-add-a-record"></a>3. adicionar um registro

Para usar o conjunto de registros recém-criado "www", você precisa adicionar registros a ele. Você pode adicionar IPv4 *registros para o registro de "www"* definir usando o exemplo a seguir. Este exemplo depende da variável *$rs* definido por você na etapa anterior.

Adicionar registros a um registro definir usando `Add-AzureRmDnsRecordConfig` é uma operação offline. Somente o local variável *$rs* é atualizado.


    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

### <a name="4-commit-the-changes"></a>4. confirmar as alterações

Confirme as alterações para o conjunto de registros. Use `Set-AzureRmDnsRecordSet` para carregar as alterações no registro que configurar DNS do Azure.

    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="5-retrieve-the-record-set"></a>5. recuperar o conjunto de registros

Você pode recuperar o registro definir do Azure DNS usando `Get-AzureRmDnsRecordSet` conforme mostrado no exemplo a seguir.


    Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : MyAzureResourceGroup
    Ttl               : 3600
    Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
    RecordType        : A
    Records           : {134.170.185.46, 134.170.188.221}
    Tags              : {}


Você também pode usar a ferramenta nslookup ou outras ferramentas DNS para o novo conjunto de registros da consulta.

Se você ainda não tenha delegado o domínio para os servidores de nome de DNS do Azure, você precisa especificar explicitamente o nome, servidor e endereço para sua região.


    nslookup www.contoso.com ns1-01.azure-dns.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    Name:    www.contoso.com
    Addresses:  134.170.185.46
                134.170.188.221

## <a name="create-a-record-set-of-each-type-with-a-single-record"></a>Criar um conjunto de registro de cada tipo com um único registro


Os exemplos a seguir mostram como criar um conjunto de registro de cada tipo de registro. Cada conjunto de registros contém um único registro.

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]


## <a name="next-steps"></a>Próximas etapas

[Como gerenciar zonas DNS usando o PowerShell](dns-operations-dnszones.md)

[Gerenciar registros de DNS e conjuntos de registros usando o PowerShell](dns-operations-recordsets.md)

[Automatizar as operações de Azure com .NET SDK](dns-sdk.md)
