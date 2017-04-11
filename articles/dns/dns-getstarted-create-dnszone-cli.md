<properties
   pageTitle="Criar uma zona DNS usando CLI | Microsoft Azure"
   description="Aprenda a criar zonas DNS para o DNS do Azure passo a passo para iniciar o seu domínio DNS usando CLI de hospedagem"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="create-an-azure-dns-zone-using-cli"></a>Criar uma zona de DNS do Azure usando CLI


> [AZURE.SELECTOR]
- [Portal do Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [CLI Azure](dns-getstarted-create-dnszone-cli.md)


Este artigo o orientará pelas etapas para criar uma zona DNS usando CLI. Você também pode criar uma zona DNS usando o PowerShell ou o portal do Azure.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


## <a name="before-you-begin"></a>Antes de começar

Estas instruções usam CLI do Microsoft Azure. Certifique-se de atualizar para a mais recente CLI Azure (0.9.8 ou posterior) para usar os comandos de DNS do Azure. Tipo de `azure -v` para verificar qual versão do Azure CLI atualmente está instalado no seu computador.

## <a name="step-1---set-up-azure-cli"></a>Etapa 1: configurar CLI do Azure

### <a name="1-install-azure-cli"></a>1. Instale CLI Azure

Você pode instalar CLI do Azure para Windows, Linux ou Mac. As seguintes etapas devem ser concluídas antes que você pode gerenciar o DNS do Azure usando CLI do Azure. Mais informações estão disponíveis em [instalar o CLI do Azure](../xplat-cli-install.md). Os comandos DNS exigem Azure CLI versão 0.9.8 ou posterior.

Todos os comandos de provedor de rede no CLI podem ser encontrado usando o seguinte comando:

    azure network

### <a name="2-switch-cli-mode"></a>2. modo CLI mudança

DNS Azure usa o Gerenciador de recursos do Azure. Verifique se que você alternar modo CLI para usar comandos ARM.

    azure config mode arm

### <a name="3-sign-in-to-your-azure-account"></a>3. entrar sua conta do Azure

Você será solicitado a autenticar com suas credenciais. Tenha em mente que você só pode usar contas ORGID.

    azure login -u "username"

### <a name="4-select-the-subscription"></a>4. Selecione a assinatura

Escolha quais das suas assinaturas Azure usar.

    azure account set "subscription name"

### <a name="5-create-a-resource-group"></a>5. criar um grupo de recursos

Gerenciador de recursos de Azure requer que todos os grupos de recursos especificam um local. Isso é usado como o local padrão para os recursos desse grupo de recursos. No entanto, como todos os recursos DNS são global, não regionais, a opção de local do grupo de recursos não tem impacto sobre o DNS do Azure.

Você pode ignorar esta etapa se você estiver usando um grupo de recursos existentes.

    azure group create -n myresourcegroup --location "West US"


### <a name="6-register"></a>6. registro de

O serviço do Azure DNS é gerenciado pelo provedor de recursos de Microsoft.Network. Sua assinatura do Azure precisa ser registrado para usar este provedor de recursos antes de poder usar Azure DNS. Esta é uma operação única para cada assinatura.

    azure provider register --namespace Microsoft.Network


## <a name="step-2---create-a-dns-zone"></a>Etapa 2 - criar uma zona DNS

Uma zona DNS é criada usando o `azure network dns zone create` comando. Opcionalmente, você pode criar uma zona DNS juntamente com marcas. Marcas são uma lista de pares de valor de nome e são usadas pelo Gerenciador de recursos do Azure para os recursos de etiqueta para fins de cobranças ou de agrupamento. Para obter mais informações sobre marcas, consulte [usando marcas para organizar seus recursos Azure](../resource-group-using-tags.md).

No Azure DNS, os nomes de zona devem ser especificados sem um terminando **'.'**. Por exemplo, como '**contoso.com**' em vez de '**contoso.com.**'.


### <a name="to-create-a-dns-zone"></a>Para criar uma zona DNS

O exemplo a seguir cria uma zona DNS chamada *contoso.com* no grupo de recursos chamado *MyResourceGroup*.

Use o exemplo para criar sua zona DNS, substituindo os valores para seu próprio.

    azure network dns zone create myresourcegroup contoso.com

### <a name="to-create-a-dns-zone-and-tags"></a>Para criar uma zona DNS e marcas.

Azure DNS CLI suporta tags das zonas DNS especificadas usando o opcional *-marca* parâmetro. O exemplo a seguir mostra como criar uma zona DNS com duas marcas, projeto = demonstração e env = teste.

Use o exemplo a seguir para criar uma zona DNS e marcas, substituindo os valores para seu próprio.

    azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## <a name="view-records"></a>Exibir registros

Criando uma zona DNS também cria os seguintes registros DNS:

- O registro de 'Início de autoridade' (SOA). Isso está presente na raiz de cada zona DNS.

- Os registros de servidor de (nomes NS) autoritativas nome. Eles mostram quais servidores de nome estiver hospedando a zona. DNS Azure usa um pool de servidores de nomes e tão diferentes servidores de nomes podem ser atribuídos a diferentes regiões no Azure DNS. Consulte o [representante um domínio ao Azure DNS](dns-domain-delegation.md) para obter mais informações.

Para visualizar esses registros, use `azure network dns-record-set show`.<BR>
*Uso: conjunto de registros de dns de rede show <-grupo de recursos >< nome de zona dns > <name><type>*


No exemplo abaixo, se você executar o comando com o recurso de grupo *myresourcegroup*, o registro definir nome *"@"* (para um registro de raiz) e digite *SOA*, ele produzirá a saída a seguir:


    azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/SOA
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    SOA record:
    data:      Email                         : msnhst.microsoft.com
    data:      Expire time                   : 604800
    data:      Host                          : edge1.azuredns-cloud.net
    data:      Minimum TTL                   : 300
    data:      Refresh time                  : 900
    data:      Retry time                    : 300
    data:                                    :
<BR>
Para exibir os registros de NS criados com a zona, use o seguinte comando:

    azure network dns record-set show myresourcegroup "contoso.com" "@" NS
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    NS records
    data:        Name server domain name     : ns1-05.azure-dns.com
    data:        Name server domain name     : ns2-05.azure-dns.net
    data:        Name server domain name     : ns3-05.azure-dns.org
    data:        Name server domain name     : ns4-05.azure-dns.info
    data:
    info:    network dns-record-set show command OK

>[AZURE.NOTE] Conjuntos de registro na raiz (ou *apex*) de um uso de zona DNS **@** como o registro definir nome.

## <a name="test"></a>Teste

Você pode testar sua zona DNS usando ferramentas DNS como nslookup, CAVAR, ou o `Resolve-DnsName` cmdlet do PowerShell.

Se você ainda não tiver delegada seu domínio para usar a nova zona no Azure DNS, você precisa direcionar a consulta DNS diretamente para um dos servidores de nomes para sua região. Os servidores de nomes para sua zona recebem os registros NS, conforme listado por "Mostrar de conjunto de registros de dns de rede azure" acima. Certifique-se a substituir os valores corretos para seu fuso no comando abaixo.

O exemplo a seguir usa CAVAR para consultar o domínio contoso.com usando os servidores de nome atribuídos para a zona DNS. A consulta tem que apontar para um servidor de nomes para os quais nós usamos * @ * e o nome de zona usando CAVAR.

     <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
    (1 server found)
    global options: +cmd
    Got answer:
    ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
    flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
    WARNING: recursion requested but not available

    OPT PSEUDOSECTION:
    EDNS: version: 0, flags:; udp: 4000
    QUESTION SECTION:
    contoso.com.                        IN      A

    AUTHORITY SECTION:
    contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
    msnhst.microsoft.com. 6 900 300 604800 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## <a name="next-steps"></a>Próximas etapas

Depois de criar uma zona DNS, crie [conjuntos de registro e registros](dns-getstarted-create-recordset-cli.md) para iniciar a resolução de nomes do seu domínio da Internet.
