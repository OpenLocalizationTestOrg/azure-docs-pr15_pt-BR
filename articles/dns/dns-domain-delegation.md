<properties
   pageTitle="Delegar seu domínio ao Azure DNS | Microsoft Azure"
   description="Compreenda como alterar delegação de domínio e usar os servidores de nomes do DNS do Azure para fornecer a hospedagem de domínio."
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2016"
   ms.author="sewhee"/>


# <a name="delegate-a-domain-to-azure-dns"></a>Delegar um domínio DNS do Azure

DNS Azure permite hospedar uma zona DNS e gerenciar os registros DNS para um domínio no Azure. Em ordem para consultas DNS para um domínio alcançar Azure DNS, o domínio deve ser delegada Azure DNS do domínio pai. Tenha em mente Azure DNS não é o registrador de domínio. Este artigo explica como funciona a delegação de domínio e como delegar domínios DNS do Azure.




## <a name="how-dns-delegation-works"></a>Como funciona a delegação de DNS

### <a name="domains-and-zones"></a>Domínios e zonas

O sistema de nome de domínio é uma hierarquia de domínios. Inicia a hierarquia do domínio 'raiz', cujo nome é simplesmente '**.**'.  Abaixo dele vêm domínios primários, como 'com', 'líquido', 'org', 'Reino Unido' ou 'jp'.  Abaixo desses são domínios de segundo nível, como 'org.uk' ou 'co.jp'.  E assim por diante. Os domínios na hierarquia DNS são hospedados usando zonas DNS separadas. Essas zonas são distribuídas globalmente, hospedado pelo servidores de nomes DNS em todo o mundo.

**Zona de DNS**

Um domínio é um nome exclusivo no sistema de nome de domínio, por exemplo, 'contoso.com'. Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Por exemplo, o domínio 'contoso.com' pode conter um número de registros DNS como mail.contoso.com (para um servidor de email) e www.contoso.com (para um site).

**Registrador de domínio**

Um registrador de domínio é uma empresa que pode fornecer nomes de domínio da Internet. Eles irá verificar se o domínio da Internet que você deseja usar está disponível e permitem que você comprá-lo. Depois que o nome de domínio está registrado, você será o proprietário legal para o nome de domínio. Se você já tiver um domínio da Internet, você usará o registrador de domínios atual para o representante do Azure DNS.

>[AZURE.NOTE] Para saber mais informações sobre quem é o proprietário de um nome de domínio específico, ou para obter informações sobre como comprar um domínio, consulte [gerenciamento de domínios de Internet no Azure AD](https://msdn.microsoft.com/library/azure/hh969248.aspx).

### <a name="resolution-and-delegation"></a>Resolução e delegação

Há dois tipos de servidores DNS:

- Um servidor DNS _autoritativo_ hospeda zonas DNS. Ele responde consultas DNS para registros nessas zonas somente.
- Um servidor DNS _recursivo_ não hospedam zonas DNS. Ele responde todas as consultas DNS ligando para os servidores DNS autorizados para coletar os dados necessários.

>[AZURE.NOTE] DNS Azure fornece um serviço DNS autoritativo.  Ele não fornece um serviço DNS recursiva.

> Serviços de nuvem e VMs no Azure são configuradas automaticamente para usar uma recursiva de serviços DNS que é fornecida separadamente como parte da infraestrutura do Azure.  Para obter informações sobre como alterar essas configurações de DNS, consulte [Resolução de nomes no Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

Os clientes DNS em PCs ou dispositivos móveis normalmente chama um servidor DNS recursiva para executar qualquer consultas DNS que precisam os aplicativos cliente.

Quando um servidor DNS recursiva recebe uma consulta para um registro DNS como 'www.contoso.com', ele primeiro precisa localizar o servidor de nomes que hospeda a zona para o domínio 'contoso.com'. Para fazer isso, ele começa em servidores de nomes raiz e dali localiza os servidores de nomes que hospeda a zona de 'com'. Em seguida, ele consulta os servidores de nomes 'com' para localizar os servidores de nomes que hospeda a zona de 'contoso.com'.  Finalmente, é capaz de consultar esses servidores de nomes para 'www.contoso.com'.

Isso é chamado de resolução do nome DNS. Especificamente, resolução DNS inclui etapas adicionais como CNAMEs seguintes, mas que não é importante para entender como funciona a delegação de DNS.

Como uma zona pai 'apontar' para os servidores de nomes para uma zona filho? Ele faz isso usando um tipo especial de registro DNS chamado registro NS (NS significa 'servidor de nome'). Por exemplo, a zona raiz contém registros NS para 'com' e mostra os servidores de nomes para a zona 'com'. Por sua vez, a zona de 'com' contém registros NS para 'contoso.com', que mostra os servidores de nomes para a zona 'contoso.com'. Configurar os registros NS para uma zona filho em uma zona pai é chamado Delegando o domínio.


![Servidor de nomes DNS](./media/dns-domain-delegation/image1.png)

Cada delegação realmente tem duas cópias dos registros NS; um na zona pai apontando para o filho e outro na zona filho em si. A zona de 'contoso.com' contém os registros NS para contoso.com (além dos registros NS 'com'). Eles são chamados os registros NS autoritativos e que chegam na apex da zona filho.


## <a name="delegating-a-domain-to-azure-dns"></a>Delegando um domínio DNS do Azure

Depois de criar sua zona DNS no DNS do Azure, você precisa configurar os registros NS na zona pai para tornar Azure DNS a fonte de resolução de nome para seu fuso autoridade. Para adquirida de um registrador de domínios, seu registrador oferecerá a opção configurar esses registros NS.

>[AZURE.NOTE] Você não precisa possui um domínio para criar uma zona DNS com este nome de domínio no Azure DNS. No entanto, é necessário o proprietário do domínio para configurar a delegação DNS Azure com o registrador de domínios.

Por exemplo, suponha que o domínio 'contoso.com' de compra e crie uma zona com o nome de contoso.com no Azure DNS. Como o proprietário do domínio, seu registrador oferecerá a opção para configurar os endereços de servidor de nome (ou seja, os registros NS) para seu domínio. O registrador armazenará esses registros NS no domínio pai, neste caso '.com'. Clientes do mundo, em seguida, serão direcionados para seu domínio na zona de DNS do Azure ao tentar resolver registros DNS em 'contoso.com'.

### <a name="finding-the-name-server-names"></a>Localizando os nomes de servidor de nome

Antes de você pode delegar sua zona de DNS para o DNS do Azure, primeiro é necessário saber os nomes de servidor de nome para sua região. Azure DNS aloca servidores de nome de um pool de cada vez que uma zona é criada.

A maneira mais fácil para ver os servidores de nome atribuídos à zona é por meio do portal do Azure.  Neste exemplo, a zona 'contoso.net' foi atribuída servidores de nomes ' ns1-01.azure-dns.com', 'ns2-01.azure-dns .net', ' ns3-01.azure-dns.org', e ' ns4-01.azure-dns.info':

 ![Servidor de nomes DNS](./media/dns-domain-delegation/viewzonens500.png)

DNS Azure cria automaticamente os registros NS autoritativos na sua região que contém os servidores de nome atribuído.  Para ver os nomes de servidor de nome por meio do PowerShell do Azure ou CLI do Azure, basta recuperar esses registros.

Usando o PowerShell do Azure, os registros NS autoritativos podem ser recuperados da seguinte maneira. Observe que o nome do registro “@” é usado para se referir a registros na apex da zona.

    PS> $zone = Get-AzureRmDnsZone –Name contoso.net –ResourceGroupName MyResourceGroup
    PS> Get-AzureRmDnsRecordSet –Name “@” –RecordType NS –Zone $zone

    Name              : @
    ZoneName          : contoso.net
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                        ns4-01.azure-dns.info}
    Tags              : {}

Você também pode usar a CLI do Azure entre plataformas para recuperar os registros NS autoritativos e, portanto, descobrir os servidores de nome atribuídos à zona:

    C:\> azure network dns record-set show MyResourceGroup contoso.net @ NS
    info:    Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
    data:    Id                              : /subscriptions/.../resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.net/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 172800
    data:    NS records
    data:        Name server domain name     : ns1-01.azure-dns.com.
    data:        Name server domain name     : ns2-01.azure-dns.net.
    data:        Name server domain name     : ns3-01.azure-dns.org.
    data:        Name server domain name     : ns4-01.azure-dns.info.
    data:
    info:    network dns record-set show command OK

### <a name="to-set-up-delegation"></a>Para configurar a delegação

Cada registrador tem suas próprias ferramentas de gerenciamento de DNS para alterar os registros de servidor de nomes para um domínio. Na página de gerenciamento de DNS do registrador de domínios, edite os registros NS e substitua os registros NS daqueles que Azure DNS criados.

Ao delegar um domínio ao Azure DNS, você deve usar os nomes de servidor de nome fornecidos pelo Azure DNS.  Você sempre deve usar todos os 4 nome nomes de servidores, independentemente do nome do seu domínio.  Delegação de domínio não exige o nome do servidor para usar o mesmo domínio de nível superior como seu domínio.

Você não deve usar registros de união para apontar para a Azure nome DNS server endereços IP, desde que esses endereços IP podem mudar no futuro. Delegações usando os nomes de servidor de nome em sua própria zona, às vezes chamada de 'servidores de nomes de banido', não são aceitos atualmente no Azure DNS.

### <a name="to-verify-name-resolution-is-working"></a>Para verificar a resolução de nome está funcionando

Depois de concluir a delegação, você pode verificar se a resolução de nome está funcionando usando uma ferramenta como 'nslookup' para consultar o registro SOA para seu fuso (que é automaticamente criado quando a zona é criada).

Observe que você não precisa especificar os servidores de nome de DNS do Azure, desde que o processo de resolução DNS normal encontrará os servidores de nomes automaticamente se a delegação tiver sido configurada corretamente.

    nslookup –type=SOA contoso.com

    Server: ns1-04.azure-dns.com
    Address: 208.76.47.4

    contoso.com
    primary name server = ns1-04.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)

## <a name="delegating-sub-domains-in-azure-dns"></a>Delegando subdomínios no DNS do Azure

Se você deseja configurar uma zona filho separada, você pode delegar um subdomínio no Azure DNS. Por exemplo, tendo configurado e delegada 'contoso.com' no Azure DNS, suponha que você gostaria de configurar uma zona filha separada, 'parceiros.contoso.com'.

Configurando um subdomínio segue um processo semelhante como uma delegação normal. A única diferença é que na etapa 3, que os registros NS devem ser criados na zona pai contoso.com no Azure DNS, em vez de sendo configurado por meio de um registrador de domínio.


1. Crie a zona filho 'parceiros.contoso.com' no Azure DNS.
2. Pesquise os registros NS autoritativos na zona filho para obter os servidores de nomes que hospeda a zona filho no Azure DNS.
3. Delegar a zona filho Configurando registros NS na zona pai apontando para a zona filho.


### <a name="to-delegate-a-sub-domain"></a>Delegar um subdomínio

O exemplo a seguir PowerShell demonstra como isso funciona. As mesmas etapas podem ser executadas através do Portal do Azure ou por meio da CLI do Azure entre plataformas.

#### <a name="step-1-create-the-parent-and-child-zones"></a>Etapa 1. Criar pai e filho zonas

Primeiro, criamos pai e filho zonas. Eles podem ser no mesmo grupo de recursos ou grupos de recursos diferentes.

    $parent = New-AzureRmDnsZone -Name contoso.com -ResourceGroupName RG1
    $child = New-AzureRmDnsZone -Name partners.contoso.com -ResourceGroupName RG1

#### <a name="step-2-retrieve-ns-records"></a>Etapa 2. Registros NS recuperar

Em seguida, podemos recuperar os registros NS autoritativos da zona filho, conforme mostrado no exemplo a seguir.  Esta página contém os servidores de nome atribuídos à zona filho.

    $child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

#### <a name="step-3-delegate-the-child-zone"></a>Etapa 3. Delegar a zona filho

Crie registro NS correspondente definido na zona pai para concluir a delegação. Observe que o nome do conjunto de registros na zona pai corresponde ao nome de zona de filho, nesse caso "parceiros".

    $parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
    $parent_ns_recordset.Records = $child_ns_recordset.Records
    Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset

### <a name="to-verify-name-resolution-is-working"></a>Para verificar a resolução de nome está funcionando

Você pode verificar se tudo está configurado corretamente pesquisando o registro SOA da zona filho.

    nslookup –type=SOA partners.contoso.com

    Server: ns1-08.azure-dns.com
    Address: 208.76.47.8

    partners.contoso.com
        primary name server = ns1-08.azure-dns.com
        responsible mail addr = msnhst.microsoft.com
        serial = 1
        refresh = 900 (15 mins)
        retry = 300 (5 mins)
        expire = 604800 (7 days)
        default TTL = 300 (5 mins)

## <a name="next-steps"></a>Próximas etapas

[Gerenciar as zonas DNS](dns-operations-dnszones.md)

[Gerenciar registros de DNS](dns-operations-recordsets.md)

