<properties 
   pageTitle="Zonas DNS e registros | Microsoft Azure" 
   description="Visão geral de suporte para hospedar zonas DNS e registros de DNS do Microsoft Azure." 
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
   ms.date="10/17/2016"
   ms.author="jtuliani"/>

# <a name="dns-zones-and-records"></a>Registros e zonas DNS

Esta página explica os principais conceitos de domínios, zonas DNS e os registros DNS e conjuntos de registros e como eles são suportados no Azure DNS.

## <a name="domain-names"></a>Nomes de domínio
 
O sistema de nome de domínio é uma hierarquia de domínios. Inicia a hierarquia do domínio 'raiz', cujo nome é simplesmente '**.**'.  Abaixo dele vêm domínios primários, como 'com', 'líquido', 'org', 'Reino Unido' ou 'jp'.  Abaixo desses são domínios de segundo nível, como 'org.uk' ou 'co.jp'. Os domínios na hierarquia DNS são distribuídos globalmente, hospedado pelo servidores de nomes DNS em todo o mundo.

Um registrador de nomes de domínio é uma organização que permite adquirir um nome de domínio, como 'contoso.com'.  Comprar um nome de domínio, você dá o direito para controlar a hierarquia DNS com esse nome, por exemplo, permitindo direcionar o nome 'www.contoso.com' para o site da sua empresa. O registrador pode hospedar o domínio em suas própria servidores de nomes em seu nome ou como alternativa, você pode especificar os servidores de nome alternativo.

DNS Azure fornece uma infraestrutura de servidor de nome distribuídos globalmente, alta disponibilidade, que você pode usar para hospedar seu domínio. Ao hospedar seus domínios no Azure DNS, você pode gerenciar seus registros DNS com as mesmas credenciais, APIs, ferramentas, de cobrança e suporte como outros serviços Azure.

DNS Azure não suportamos comprando de nomes de domínio. Se você deseja comprar um nome de domínio, você precisará usar um registrador de nomes de domínio de terceiros. O registrador normalmente cobrará uma pequena taxa anual. Os domínios, em seguida, podem ser hospedados no Azure DNS para o gerenciamento de registros de DNS. Consulte o [representante um domínio ao Azure DNS](dns-domain-delegation.md) para obter detalhes.

## <a name="dns-zones"></a>Zonas DNS 

Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Para iniciar seu domínio no Azure DNS de hospedagem, você precisa criar uma zona DNS desse nome de domínio. Cada registro DNS do seu domínio, em seguida, é criado dentro dessa zona DNS. 

Por exemplo, o domínio 'contoso.com' pode conter vários registros DNS, como mail.contoso.com (para um servidor de email) e www.contoso.com (para um site da web).

Ao criar uma zona DNS no DNS do Azure, o nome da zona deve ser exclusivo dentro do grupo de recursos. O mesmo nome de zona pode ser reutilizado em um grupo de recurso diferente ou uma assinatura diferente do Azure. Onde várias zonas compartilham o mesmo nome, cada instância é atribuída endereços de servidor de nome diferente. Apenas um conjunto de endereços pode ser configurado com o registrador de nome.

>[AZURE.NOTE] Você não precisa próprio nome de domínio para criar uma zona DNS com esse nome de domínio no Azure DNS. No entanto, é necessário o proprietário do domínio para configurar os servidores de nomes DNS Azure como os servidores de nome correto para o nome de domínio com o registrador de nome.

Para obter mais informações, consulte [representante um domínio ao Azure DNS](dns-domain-delegation.md).

## <a name="dns-records"></a>Registros DNS

### <a name="record-types"></a>Tipos de registro

Cada registro DNS tem um nome e um tipo. Os registros são organizados em vários tipos de acordo com os dados que elas contêm. O tipo mais comum é um registro 'A', que mapeia um nome para um endereço IPv4. Outro tipo comum é um registro de 'MX', que mapeia um nome para um servidor de email.

DNS Azure oferece suporte a todos os tipos de registro de DNS comuns: A, AAAA, CNAME, MX, NS, PTR, SOA, SRV e TXT.

### <a name="record-names"></a>Nomes de registros

No DNS do Azure, os registros são especificados usando nomes relativos. Um nome de domínio *totalmente qualificado* (FQDN) inclui o nome de zona, enquanto um nome *relativo* não. Por exemplo, o nome de registro relativo 'www' na zona 'contoso.com' fornece o nome de registro totalmente qualificado 'www.contoso.com'.

Um registro de *apex* é um registro DNS na raiz (ou *apex*) de uma zona DNS. Por exemplo, na zona DNS 'contoso.com', um registro de apex também tem o nome totalmente qualificado 'contoso.com' (às vezes, isso se chama um domínio *"descoberto"* ).  Por convenção, o nome relativo '@' é usado para criar registros de apex.

### <a name="record-sets"></a>Conjuntos de registros
Às vezes, você precisa criar mais de um registro DNS com um nome e tipo. Por exemplo, suponha que o site de 'www.contoso.com' estiver hospedado em dois endereços IP diferentes. O site requer duas diferentes registros a, uma para cada endereço IP. Este é um exemplo de um conjunto de registros:

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

DNS Azure gerencia registros DNS usando *conjuntos de registro*. Um conjunto de registro (também conhecido como um *recurso de* conjunto de registros) é o conjunto de registros DNS em uma zona têm o mesmo nome e são do mesmo tipo. A maioria dos conjuntos de registros contêm um único registro, mas exemplos como este, em que um conjunto de registros contém mais de um registro, não são incomuns.

Por exemplo, suponhamos que você já tiver criado um registro 'www' na zona 'contoso.com', apontando para o IP endereço '134.170.185.46' (o primeiro registro acima).  Para criar o segundo registro você faria adicionar esse registro ao conjunto de registros existente, em vez de criar um novo conjunto de registros.

Os tipos de registro SOA e CNAME são exceções. Os padrões DNS não permitirem vários registros com o mesmo nome para esses tipos, portanto, esses conjuntos de registro só podem conter um único registro.

### <a name="time-to-live"></a>Tempo de vida

O tempo de vida ou TTL, especifica por quanto tempo cada registro é armazenada em cache por clientes antes está sendo consultado novamente. No exemplo acima, o TTL é 3600 segundos ou 1 hora.

No Azure DNS, o TTL for especificado para o conjunto de registros, não para cada registro, para que o mesmo valor é usado para todos os registros dentro desse conjunto de registros.  Você pode especificar qualquer valor TTL entre 1 e 2.147.483.647 segundos.

### <a name="wildcard-records"></a>Registros de curinga

DNS Azure ofereça suporte a [registros de curinga](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Registros de curinga são retornados em resposta a qualquer consulta com um nome correspondente (a menos que haja uma correspondência detalhada de um conjunto de registros não curinga). Os conjuntos de registro de curinga são suportados para todos os tipos de registro, exceto NS e SOA.  

Para criar um conjunto de registros de curinga, use o nome do conjunto de registros '\*'. Como alternativa, você também pode usar um nome com '\*'como seu mais à esquerda rótulo, por exemplo,'\*foo '.

### <a name="cname-records"></a>Registros CNAME

Os conjuntos de registro CNAME não coexistam com outros conjuntos de registro com o mesmo nome. Por exemplo, é possível criar um registro CNAME definida com o nome relativo 'www' e um registro com o nome relativo 'www' ao mesmo tempo.

Porque o apex zona (nome = ‘@’) sempre contém o NS e SOA gravam conjuntos que foram criados quando a zona foi criada, você não pode criar um registro CNAME definidos a apex a zona.

Estas restrições surgirem com os padrões DNS e não são limitações do Azure DNS.

### <a name="ns-records"></a>Registros NS

Um conjunto de registros NS é criado automaticamente na apex de cada zona (nome = ‘@’), e é excluído automaticamente quando a zona é excluída (ela não pode ser excluída separadamente).  Você pode modificar o TTL deste conjunto de registros, mas você não pode modificar os registros, que são pré-configurado para consultar os servidores de nomes DNS Azure atribuídos à zona.

Você pode criar e excluir outros registros NS dentro da zona, não no apex a zona.  Isso permite que você configure zonas filho (consulte [delegar subdomínios no Azure DNS](dns-domain-delegation.md#delegating-sub-domains-in-azure-dns)).

### <a name="soa-records"></a>Registros SOA

Um conjunto de registros SOA é criado automaticamente na apex de cada zona (nome = ‘@’), e é excluído automaticamente quando a zona é excluída.  Registros SOA não podem ser criados ou excluídos separadamente. 

Você pode modificar todas as propriedades do registro SOA, exceto para a propriedade 'host', que é pré-configurada para se referir ao nome do servidor de nome primário fornecido pelo DNS do Azure.

### <a name="spf-records"></a>Registros SPF

Registros de política Framework (SPF) do remetente são usadas para especificar quais servidores de email são permitidos para enviar email em nome de um nome de domínio específico.  Configuração correta de registros SPF é importante impedir que os destinatários marcar seu email como 'lixo eletrônico'.

As RFCs DNS originalmente introduzido um novo tipo de registro de 'SPF' para oferecer suporte a esse cenário. Para oferecer suporte a mais antigos servidores de nomes, eles também permitidos a usar o tipo de registro TXT para especificar os registros SPF.  Este ambiguidade levou a confusão, que foi resolvido por [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1).  Isso declarado que os registros SPF só devem ser criados usando o tipo de registro TXT e desaprovado o tipo de registro SPF. 

**Os registros SPF são suportados pelo DNS do Azure e devem ser criados usando o tipo de registro TXT.** Não há suporte para o tipo de registro SPF obsoleto. Quando a [importação de um DNS arquivo de zona](dns-import-export.md), todos os registros SPF usando o tipo de registro SPF são convertidas no tipo de registro TXT. 

### <a name="srv-records"></a>Registros SRV

[Registros SRV](https://en.wikipedia.org/wiki/SRV_record) são usados por vários serviços para especificar locais do servidor. Ao especificar um registro SRV no Azure DNS:

- O *serviço* e o *protocolo* devem ser especificados como parte do nome do conjunto de registros, o prefixo sublinhados.  Por exemplo, '\_sip. \_tcp.name'.  Para um registro em apex a zona, não é necessário especificar '@' no nome do registro, basta usar o serviço e protocol, por exemplo, '\_sip. \_tcp'.
- A *prioridade*, *espessura*, *porta*e *destino* são especificados como parâmetros de cada registro no conjunto de registros. 

## <a name="tags-and-metadata"></a>Marcas e metadados

### <a name="tags"></a>Marcas
Marcas são uma lista de pares de valor de nome e são usadas pelo Gerenciador de recursos do Azure para os recursos de etiqueta.  Gerenciador de recursos de Azure usa marcas para habilitar exibições filtradas da sua conta do Azure e também permite que você defina uma política de quais tags que são necessárias. Para obter mais informações sobre marcas, consulte [usando marcas para organizar seus recursos Azure](../resource-group-using-tags.md).

DNS Azure suporta usando o Gerenciador de recursos do Azure marcas em recursos de zona DNS.  Ele não suporte marcas em conjuntos de registros de DNS.

### <a name="metadata"></a>Metadados

Como uma alternativa às marcas de conjunto de registros, o DNS do Azure oferece suporte anotar conjuntos de registros usando 'metadados'.  Semelhante às marcas, metadados permitem associar pares nome-valor a cada conjunto de registros.  Isso pode ser útil, por exemplo, para o registro a finalidade de cada conjunto de registros.  Diferentemente das marcas, metadados não podem ser usados para fornecer um modo de exibição filtrado da sua conta do Azure e não podem ser especificado em uma diretiva de Gerenciador de recursos do Azure.

## <a name="limits"></a>Limites

Os seguintes limites de padrão se aplicam ao usar o Azure DNS:

[AZURE.INCLUDE [dns-limits](../../includes/dns-limits.md)] 

## <a name="next-steps"></a>Próximas etapas

- Para começar a usar o Azure DNS, saiba como [criar uma zona DNS](dns-getstarted-create-dnszone-portal.md) e [criar registros DNS](dns-getstarted-create-recordset-portal.md).
- Para migrar uma zona DNS existente, saiba como [Importar e arquivo de zona DNS](dns-import-export.md).
