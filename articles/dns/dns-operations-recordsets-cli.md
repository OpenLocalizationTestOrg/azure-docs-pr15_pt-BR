<properties
   pageTitle="Gerenciar conjuntos de registros de DNS e registros de DNS do Azure usando o Azure CLI | Microsoft Azure"
   description="Gerenciando os conjuntos de registro de DNS e registros de DNS do Azure ao hospedar seu domínio no Azure DNS. Todos os comandos para operações em conjuntos de registros e registros."
   services="dns"
   documentationCenter="na"
   authors="jtuliani"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="jtuliani"/>

# <a name="manage-dns-records-and-record-sets-by-using-cli"></a>Gerenciar os registros DNS e conjuntos de registros usando CLI


> [AZURE.SELECTOR]
- [Portal do Azure](dns-operations-recordsets-portal.md)
- [CLI Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


Este artigo mostra como gerenciar conjuntos de registro e registros de zona DNS usando a interface de linha entre plataformas Azure (comando).

É importante compreender a diferença entre os conjuntos de registro de DNS e registros DNS individuais. Um conjunto de registros é um conjunto de registros em uma zona que têm o mesmo nome e são do mesmo tipo. Para obter mais informações, consulte [registros e conjuntos de registros de compreensão](dns-getstarted-create-recordset-cli.md).


## <a name="configure-the-cross-platform-azure-cli"></a>Configurar a CLI do Azure entre plataformas

Azure DNS é um serviço de somente Gerenciador de recursos do Azure. Ele não tem uma API de gerenciamento de serviço do Azure. Certifique-se de que a CLI Azure está configurada para usar o modo do Gerenciador de recursos, usando o `azure config mode arm` comando.

Se você vir **erro: 'dns' não é um comando azure**, é provável porque você está usando o Azure CLI no modo de gerenciamento de serviço do Azure, não no modo do Gerenciador de recursos.

## <a name="create-a-new-record-set-and-record"></a>Criar um novo conjunto de registro e registro

Para criar um novo registro definido no portal do Azure, consulte [criar um conjunto de registro e registros](dns-getstarted-create-recordset-cli.md).


## <a name="retrieve-a-record-set"></a>Recuperar um conjunto de registros

Para recuperar um conjunto de registros existente, use `azure network dns record-set show`. Especificar o grupo de recursos, o nome da zona registro que defina o nome relativo e o tipo de registro. Use o exemplo abaixo, substituindo os valores com seus próprios.

    azure network dns record-set show myresourcegroup contoso.com www A


## <a name="list-record-sets"></a>Conjuntos de registro de lista

Você pode listar todos os registros em uma zona DNS usando o `azure network dns record-set list` comando. Você precisa especificar o nome do grupo de recursos e o nome de zona.

### <a name="to-list-all-record-sets"></a>Para listar todos os conjuntos de registro

Este exemplo retorna todos os conjuntos de registro, independentemente de nome ou tipo de registro:

    azure network dns record-set list myresourcegroup contoso.com

### <a name="to-list-record-sets-of-a-given-type"></a>Para os conjuntos de registro da lista de um determinado tipo

Este exemplo retorna todos os conjuntos de registros que correspondem ao tipo de registro especificado (nesse caso, "A" registros):

    azure network dns record-set list myresourcegroup contoso.com A


## <a name="add-a-record-to-a-record-set"></a>Adicionar um registro a um conjunto de registros

Adicionar registros para conjuntos de registros usando o `azure network dns record-set add-record`comando. Os parâmetros para adicionar registros a um conjunto de registros variam dependendo do tipo de registro que está sendo definido. Por exemplo, quando você usa um conjunto de registro do tipo "A", você pode apenas especificar registros com o parâmetro `-a <IPv4 address>`.

Para criar um conjunto de registros, use o `azure network dns record-set create`comando. Especificar o grupo de recursos, o nome da zona registro que defina o nome relativo, tipo de registro e tempo de vida (TTL). Se o `--ttl` parâmetro não está definido, o valor (em segundos) padrão de quatro.

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300


Depois de criar o conjunto de registros "A", adicione o endereço IPv4 usando o `azure network dns record-set add-record`comando.

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1


Os exemplos a seguir mostram como criar um conjunto de registro de cada tipo de registro. Cada conjunto de registros contém um único registro.

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]


## <a name="update-a-record-in-a-record-set"></a>Atualizar um registro em um conjunto de registros

### <a name="to-add-another-ip-address-1234-to-an-existing-a-record-set-www"></a>Para adicionar outro endereço IP (1.2.3.4) para um registro a existente "" defina ("www"):

    azure network dns record-set add-record  myresourcegroup contoso.com  A
    -a 1.2.3.4
    info:    Executing command network dns record-set add-record
    Record set name: www
    + Looking up the dns zone "contoso.com"
    + Looking up the DNS record set "www"
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/a
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:        IPv4 address                : 192.168.1.1
    data:        IPv4 address                : 1.2.3.4
    data:
    info:    network dns record-set add-record command OK

### <a name="to-remove-an-existing-value-from-a-record-set"></a>Para remover um valor existente de um conjunto de registros
Use `azure network dns record-set delete-record`.

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
    info:    Executing command network dns record-set delete-record
    + Looking up the DNS record set "www"
    Delete DNS record? [y/n] y
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/A
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:    IPv4 address                    : 192.168.1.1
    data:
    info:    network dns record-set delete-record command OK



## <a name="remove-a-record-from-a-record-set"></a>Remover um registro de um conjunto de registros

Registros podem ser removidos de um registro definir usando `azure network dns record-set delete-record`. O registro que está sendo removido deve ser uma correspondência exata com um registro existente em todos os parâmetros.

Removendo o último registro de um conjunto de registros não exclui o conjunto de registros. Para obter mais informações, consulte a seção deste artigo chamado [Excluir um conjunto de registro](#delete).

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

    azure network dns record-set delete myresourcegroup contoso.com www A

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Remover um registro de AAAA de um conjunto de registros

    azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### <a name="remove-a-cname-record-from-a-record-set"></a>Remover um registro CNAME de um conjunto de registros

    azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com


### <a name="remove-an-mx-record-from-a-record-set"></a>Remover um registro MX de um conjunto de registros

    azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### <a name="remove-an-ns-record-from-record-set"></a>Remover um registro NS do conjunto de registros

    azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### <a name="remove-a-ptr-record-from-a-record-set"></a>Remover um registro PTR de um conjunto de registros
Nesse caso ' Meu-arpa-zone.com' representa a zona ARPA que representa o intervalo IP.  Cada registro PTR definido desta zona corresponde a um endereço IP dentro este intervalo IP.

    azure network dns record-set delete-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"

### <a name="remove-an-srv-record-from-a-record-set"></a>Remover um registro SRV de um conjunto de registros

    azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com"

### <a name="remove-a-txt-record-from-a-record-set"></a>Remover um registro TXT de um conjunto de registros

    azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## <a name="delete"></a>Excluir um conjunto de registros

Os conjuntos de registro podem ser excluídos usando a `Remove-AzureRmDnsRecordSet` cmdlet. Não é possível excluir a SOA e registro NS define no apex a zona (nome = "@") que foram criados automaticamente quando a zona foi criada. Eles serão excluídos automaticamente se a zona é excluída.

No exemplo a seguir, o "Um" conjunto de registros "teste-a" será removido da zona DNS "contoso.com":

    azure network dns record-set delete myresourcegroup contoso.com  "test-a" A

A opção opcional *- p* pode ser usada para suprimir o prompt de confirmação.


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o DNS do Azure, consulte [Visão geral do DNS do Azure](dns-overview.md). Para obter informações sobre como automatizar DNS, consulte [usando o SDK do .NET de conjuntos de zonas DNS criando e registro](dns-sdk.md).

Se você quiser trabalhar com registros DNS reverso, consulte [como gerenciar os registros DNS reverso dos seus serviços utilizando a CLI do Azure](dns-reverse-dns-record-operations-cli.md).
