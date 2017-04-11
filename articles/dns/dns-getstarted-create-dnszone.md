<properties
   pageTitle="Introdução ao Azure DNS | Microsoft Azure"
   description="Aprenda a criar zonas DNS para o DNS do Azure. Este é um passo a passo para obter sua primeira zona DNS que criou para iniciar o seu domínio DNS usando o PowerShell de hospedagem."
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

# <a name="create-a-dns-zone-using-powershell"></a>Criar uma zona DNS usando o Powershell

> [AZURE.SELECTOR]
- [Portal do Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [CLI Azure](dns-getstarted-create-dnszone-cli.md)

Este artigo o orientará pelas etapas para criar uma zona DNS usando o PowerShell. Você também pode criar uma zona DNS usando CLI ou o portal do Azure.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="tagetag"></a>Sobre marcas e Etags

### <a name="etags"></a>ETags

Suponha que duas pessoas ou dois processos tentam modificar um registro DNS ao mesmo tempo. Qual vence? E o vencedor reconhece que eles já substituídos apenas alterações criadas por outra pessoa?

DNS Azure usa Etags para lidar com alterações simultâneas para o mesmo recurso com segurança. Cada recurso DNS (zona ou conjunto de registros) tem um Etag associada a ele. Sempre que um recurso é recuperado, seu Etag também é recuperado. Ao atualizar um recurso, você tem a opção para transmitir de volta a Etag para que DNS Azure possa verificar que a Etag no coincide com o servidor. Desde que cada atualização a um recurso resulta na Etag sendo gerado novamente, uma incompatibilidade de Etag indica que ocorreu uma alteração simultânea. ETags também são usadas ao criar um novo recurso para garantir que o recurso ainda não existe.

Por padrão, o Azure DNS PowerShell usa Etags para bloquear alterações simultâneas para zonas e gravar conjuntos. Opcional *-Substituir* switch pode ser usada para suprimir Etag verificações, caso em que qualquer simultâneas alterações que ocorreram serão substituídas.

No nível da API REST de DNS do Azure, Etags são especificadas usando cabeçalhos HTTP.  Seu comportamento é fornecido na tabela a seguir:

|Cabeçalho|Comportamento|
|------|--------|
|Nenhum|COLOCAR sempre sucesso (sem verificações Etag)|
|Se CORRESP<etag>|COLOCAR sucesso somente se existe de recurso e Etag corresponder|
|Se CORRESP *     | COLOCAR sucesso somente se existir recurso|
|Se-nenhum-correspondência * |  COLOCAR sucesso somente se o recurso não existe|

### <a name="tags"></a>Marcas

Marcas são diferentes dos Etags. Marcas são uma lista de pares de valor de nome e são usadas pelo Gerenciador de recursos do Azure para os recursos de etiqueta para fins de cobranças ou de agrupamento. Para obter mais informações sobre marcas, consulte [usando marcas para organizar seus recursos Azure](../resource-group-using-tags.md).

Azure DNS PowerShell suporta Tags em zonas e conjuntos de registro especificados usando as opções `-Tag` parâmetro.


## <a name="before-you-begin"></a>Antes de começar

Verifique se você tem os seguintes itens antes de iniciar a configuração.

- Uma assinatura do Azure. Se você ainda não tiver uma assinatura do Azure, você pode ativar seus [benefícios de assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou sinal para cima, para uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Você precisará instalar a versão mais recente do Azure Gerenciador de recursos de cmdlets do PowerShell (1.0 ou posteriores). Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.

## <a name="step-1---sign-in"></a>Etapa 1 - entrar

Abra o console do PowerShell e conectar à sua conta. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de recursos](../powershell-azure-resource-manager.md).

Use o exemplo a seguir para ajudá-lo a se conectar:

    Login-AzureRmAccount

Verifique as assinaturas para a conta.

    Get-AzureRmSubscription

Especifica a assinatura que você deseja usar.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="step-2---create-a-resource-group"></a>Etapa 2 - criar um grupo de recursos

Gerenciador de recursos de Azure requer que todos os grupos de recursos especificam um local. Isso é usado como o local padrão para os recursos desse grupo de recursos. No entanto, como todos os recursos DNS são global, não regionais, a opção de local do grupo de recursos não tem impacto sobre o DNS do Azure.

Você pode ignorar esta etapa se você estiver usando um grupo de recursos existentes.

    New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"


## <a name="step-3---register"></a>Etapa 3 - registro

O serviço do Azure DNS é gerenciado pelo provedor de recursos de Microsoft.Network. Sua assinatura do Azure precisa ser registrado para usar este provedor de recursos antes de poder usar Azure DNS. Esta é uma operação única para cada assinatura.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network


## <a name="step-4----create-a-dns-zone"></a>Etapa 4: criar uma zona DNS

Uma zona DNS é criada usando o `New-AzureRmDnsZone` cmdlet. Há exemplos abaixo para a criação de uma zona DNS com ou sem marcas. Para obter mais informações sobre marcas, consulte a seção sobre [marcas](#tags) neste artigo.

>[AZURE.NOTE] No Azure DNS, os nomes de zona devem ser especificados sem um terminando **'.'**. Por exemplo, como '**contoso.com**' em vez de '**contoso.com.**'.

### <a name="to-create-a-dns-zone"></a>Para criar uma zona DNS

O exemplo a seguir cria uma zona DNS chamada *contoso.com* no grupo de recursos chamado *MyResourceGroup*. Use o exemplo para criar uma zona DNS, substituindo os valores para seu próprio.

    New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

### <a name="to-create-a-dns-zone-with-tags"></a>Para criar uma zona DNS com marcas

O exemplo a seguir mostra como criar uma zona DNS com duas marcas, *projeto = demonstração* e *env = teste*. Use o exemplo para criar uma zona DNS, substituindo os valores para seu próprio.

    New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )

## <a name="view-records"></a>Exibir registros

Criando uma zona DNS também cria os seguintes registros DNS:

- O registro de *Início de autoridade* (SOA). Isso está presente na raiz de cada zona DNS.

- Os registros de servidor de (nomes NS) autoritativas nome. Eles mostram quais servidores de nome estiver hospedando a zona. DNS Azure usa um pool de servidores de nomes e tão diferentes servidores de nomes podem ser atribuídos para diferentes regiões no Azure DNS. Consulte [um domínio ao Azure DNS de representante](dns-domain-delegation.md) para obter mais informações.

Para visualizar esses registros, use `Get-AzureRmDnsRecordSet`:

    Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
    RecordType        : SOA
    Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
    Tags              : {}

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
    Tags              : {}


Conjuntos de registro na raiz (ou *apex*) de um uso de zona DNS **@** como o registro definir nome.


## <a name="test"></a>Teste

Você pode testar sua zona DNS usando ferramentas de DNS como nslookup, cavar ou o [cmdlet do PowerShell DnsName resolver](https://technet.microsoft.com/library/jj590781.aspx).

Se você ainda não tiver delegada seu domínio para usar a nova zona no Azure DNS, você precisará direcionar a consulta DNS diretamente para um dos servidores de nomes para sua região. Os servidores de nomes para sua zona recebem nos registros NS, conforme listado por `Get-AzureRmDnsRecordSet` acima. Certifique-se a substituir os valores corretos para sua região para o comando a seguir.

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = msnhst.microsoft.com
            serial  = 1
            refresh = 900 (15 mins)
            retry   = 300 (5 mins)
            expire  = 604800 (7 days)
            default TTL = 300 (5 mins)


## <a name="next-steps"></a>Próximas etapas

Depois de criar uma zona DNS, crie [conjuntos de registro e registros](dns-getstarted-create-recordset.md) para iniciar a resolução de nomes do seu domínio da Internet.

