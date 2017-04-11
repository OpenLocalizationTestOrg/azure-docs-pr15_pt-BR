<properties
   pageTitle="Gerenciar conjuntos de registros de DNS e registros usando o portal do Azure | Microsoft Azure"
   description="Gerenciando os conjuntos de registro de DNS e registros de DNS do Azure ao hospedar seu domínio no Azure DNS. Todos os comandos do PowerShell para operações em conjuntos de registros e registros."
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

# <a name="manage-dns-records-and-record-sets-by-using-powershell"></a>Gerenciar registros de DNS e conjuntos de registros usando o PowerShell



> [AZURE.SELECTOR]
- [Portal do Azure](dns-operations-recordsets-portal.md)
- [CLI Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)



Este artigo mostra como gerenciar registros de zona DNS e conjuntos de registros usando o Windows PowerShell.

É importante compreender a diferença entre os conjuntos de registro de DNS e registros DNS individuais. Um conjunto de registros é o conjunto de registros em uma zona que têm o mesmo nome e são do mesmo tipo. Para obter mais informações, consulte [registros usando o portal do Azure e conjuntos de registros de DNS de criar](dns-getstarted-create-recordset-portal.md).

Para gerenciar seus registros e conjuntos de registros, é necessário a versão mais recente do Azure Gerenciador de recursos de cmdlets do PowerShell. Para obter mais informações, consulte [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md). Para obter mais informações sobre como trabalhar com o PowerShell, consulte [Usando o PowerShell Azure com o Gerenciador de recursos do Azure](../powershell-azure-resource-manager.md).



## <a name="create-a-new-record-set-and-a-record"></a>Criar um novo conjunto de registros e um registro

Para criar um registro definir usando o PowerShell, consulte [registros usando o PowerShell e conjuntos de registros de DNS de criar](dns-getstarted-create-recordset.md).

## <a name="get-a-record-set"></a>Obter um conjunto de registros

Para recuperar um conjunto de registros existente, use `Get-AzureRmDnsRecordSet`. Especifique o registro define nome relativo, o tipo de registro e a zona.

    $rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Como com `New-AzureRmDnsRecordSet`, o nome do registro deve ser um nome relativo, significando que ele deve excluir o nome de zona.

Você pode especificar a zona usando o nome da zona e o nome do grupo de recursos, ou usando um objeto de zona:

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
    $rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone

`Get-AzureRmDnsRecordSet`Retorna um objeto local que representa o conjunto de registro que foi criado no Azure DNS.

## <a name="list-record-sets"></a>Conjuntos de registro de lista

Você também pode usar`Get-AzureRmDnsRecordSet` para conjuntos de registro de lista se você omitir o *– nome* e/ou *– RecordType* parâmetros.

### <a name="to-list-all-record-sets"></a>Para listar todos os conjuntos de registro

Este exemplo retorna todos os conjuntos de registro, independentemente de nome ou tipo de registro:

    $list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

### <a name="to-list-record-sets-of-a-given-record-type"></a>Para os conjuntos de registro da lista de um determinado tipo de registro

Este exemplo retorna todos os conjuntos de registros que correspondem ao tipo de registro especificado. Nesse caso, o registro definir isto é retornado é "A" registros:

    $list = Get-AzureRmDnsRecordSet –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

A zona pode ser especificada usando ambos os *– ZoneName* e *– ResourceGroupName* parâmetros (conforme mostrado) ou especificando um objeto de zona:

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
    $list = Get-AzureRmDnsRecordSet -Zone $zone

## <a name="add-a-record-to-a-record-set"></a>Adicionar um registro a um conjunto de registros

Adicionar registros para conjuntos de registros usando o `Add-AzureRmDnsRecordConfig` cmdlet. Esta é uma operação offline. Somente o objeto local que representa o conjunto de registros é alterado.

Os parâmetros para adicionar registros a um conjunto de registros variam dependendo do tipo de conjunto de registros. Por exemplo, ao usar um conjunto de registro do tipo "A", você pode apenas especificar registros com o parâmetro *-IPv4Address*.

Registros adicionais podem ser adicionados a cada conjunto de registros por chamadas adicionais para `Add-AzureRmDnsRecordConfig`. Você pode adicionar até 20 registros a qualquer conjunto de registros. No entanto, conjuntos de registros do tipo "CNAME" podem conter no máximo um registro, e um conjunto de registros não pode conter dois registros idênticos. Os conjuntos de registro vazios (com zero records) podem ser criados, mas não aparecem nos servidores de nomes DNS do Azure.

Depois de conjunto de registros contém a coleção desejada de registros, você precisará confirmá-lo usando o `Set-AzureRmDnsRecordSet` cmdlet. Depois de um conjunto de registros foi confirmado, ele substitui o registro existente definido no Azure DNS.

### <a name="to-create-an-a-record-set-with-a-single-record"></a>Para criar um registro definida com um único registro

    $rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
    Set-AzureRmDnsRecordSet -RecordSet $rs

A sequência de operações para criar um registro também pode ser *obsoletos*, significando que você passa o objeto de conjunto de registros usando a barra vertical em vez de passando-lo como um parâmetro. Por exemplo:

    New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### <a name="additional-record-type-examples"></a>Exemplos de tipo de registro adicionais

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## <a name="modify-existing-record-sets"></a>Modificar os conjuntos de registro existentes

As etapas para modificar um conjunto de registros existente são semelhantes às etapas executadas durante a criação de registros. A sequência de operações é a seguinte:

1.  Recuperar o registro existente definido usando `Get-AzureRmDnsRecordSet`.

2.  Modificar o registro definido por adicionando registros, remover registros, alterando os parâmetros de registro, ou alterar o registro define o tempo de vida útil (TTL). Esta é uma operação offline. Somente o objeto local que representa o conjunto de registros é alterado.

3.  Confirmar as alterações usando o `Set-AzureRmDnsRecordSet` cmdlet. Isso substitui o registro existente definido no Azure DNS.


### <a name="to-update-a-record-in-an-existing-record-set"></a>Atualizar um registro em um conjunto de registro existente

Neste exemplo, vamos alterar o endereço IP de um registro a existente "":

    $rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Records[0].Ipv4Address = "134.170.185.46"
    Set-AzureRmDnsRecordSet -RecordSet $rs

O `Set-AzureRmDnsRecordSet` cmdlet usa etag verificações para garantir que as alterações simultâneas não são substituídas. Use o *-Substituir* sinalizador suprima essas verificações. Para obter mais informações, consulte [sobre marcas e etags](dns-getstarted-create-dnszone.md#tagetag).

### <a name="to-modify-an-soa-record"></a>Para modificar um registro SOA

Você não pode adicionar ou remover registros do registro SOA criada automaticamente definida como a apex zona (nome = "@"). No entanto, você pode modificar qualquer um dos parâmetros dentro do registro SOA (exceto "Host") e o registro definir TTL.

O exemplo a seguir mostra como alterar a propriedade de *Email* do registro SOA:

    $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Records[0].Email = "admin.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Para modificar os registros NS na apex de zona

Você não pode adicionar, para remover ou modificar os registros do conjunto registros de NS criada automaticamente em apex a zona (nome = "@"). A única alteração que é permitida é modificar o conjunto de registros TTL.

O exemplo a seguir mostra como alterar a propriedade TTL do conjunto de registros NS:

    $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Ttl = 300
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="to-add-records-to-an-existing-record-set"></a>Para adicionar registros a um conjunto de registro existente

Neste exemplo, vamos adicionar dois registros MX adicionais para o conjunto de registros existente:

    $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
    Set-AzureRmDnsRecordSet -RecordSet $rs

## <a name="remove-a-record-from-an-existing-record-set"></a>Remover um registro de um conjunto de registro existente

Registros podem ser removidos de um registro definir usando `Remove-AzureRmDnsRecordConfig`. O registro que está sendo removido deve ser uma correspondência exata com um registro existente em todos os parâmetros. Alterações devem ser confirmadas usando `Set-AzureRmDnsRecordSet`.

Removendo o último registro de um conjunto de registros não exclui o conjunto de registros. Para obter mais informações, consulte [Excluir um conjunto de registro](#delete-a-record-set) abaixo.


    $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
    Set-AzureRmDnsRecordSet -RecordSet $rs

A sequência de operações remover um registro de um conjunto de registros também pode ser usada, significando que você passa o objeto de conjunto de registros usando a barra vertical em vez de passando-lo como um parâmetro. Por exemplo:

    Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Remover um registro de AAAA de um conjunto de registros

    $rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-a-cname-record-from-a-record-set"></a>Remover um registro CNAME de um conjunto de registros

Como um conjunto de registros CNAME pode conter no máximo um registro, remover esse registro deixa um conjunto de registros vazio.

    $rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-mx-record-from-a-record-set"></a>Remover um registro MX de um conjunto de registros

    $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-ns-record-from-record-set"></a>Remover um registro NS do conjunto de registros

    $rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-srv-record-from-a-record-set"></a>Remover um registro SRV de um conjunto de registros

    $rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-a-txt-record-from-a-record-set"></a>Remover um registro TXT de um conjunto de registros

    $rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
    Set-AzureRmDnsRecordSet -RecordSet $rs

## <a name="delete-a-record-set"></a>Excluir um conjunto de registros

Os conjuntos de registro podem ser excluídos usando a `Remove-AzureRmDnsRecordSet` cmdlet. Não é possível excluir a SOA e registro NS define no apex a zona (nome = "@") que foram criados automaticamente quando a zona foi criada. Eles serão excluídos automaticamente se a zona é excluída.

Use um dos três métodos a seguir para remover um conjunto de registros:

### <a name="specify-all-the-parameters-by-name"></a>Especificar todos os parâmetros por nome

Opcional *-força* opção pode ser usada para suprimir o prompt de confirmação.

    Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]


### <a name="specify-the-record-set-by-name-and-type-and-specify-the-zone-by-object"></a>Especifique o conjunto de registros por nome e tipo e a zona por objeto

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### <a name="specify-the-record-set-by-object"></a>Especificar o registro definido por objeto

    $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

Quando você define o conjunto usando um objeto de registros, ele habilita verificações de etag garantir que as alterações simultâneas não são excluídas. Opcional *-Substituir* sinalizador elimina essas verificações. Para obter mais informações, consulte [Etags e marcas](dns-getstarted-create-dnszone.md#tagetag) .

O objeto de conjunto de registros também pode ser usado em vez de estão sendo passados como um parâmetro:

    Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o DNS do Azure, consulte [Visão geral do DNS do Azure](dns-overview.md). Para obter informações sobre como automatizar DNS, consulte [usando o SDK do .NET de conjuntos de zonas DNS criando e registro](dns-sdk.md).

Para obter mais informações sobre os registros DNS reverso, consulte [como gerenciar registros DNS reverso para seus serviços usando o PowerShell](dns-reverse-dns-record-operations-ps.md).
