## <a name="about-records"></a>Sobre registros

Cada registro DNS tem um nome e um tipo. Os registros são organizados em vários tipos de acordo com os dados que elas contêm. O tipo mais comum é um registro "A", que mapeia um nome para um endereço IPv4. Outro tipo é um registro de "MX", que mapeia um nome para um servidor de email.

DNS Azure oferece suporte a todos os comuns tipos de registro DNS, incluindo A, AAAA, CNAME, MX, NS, PTR, SOA, SRV e TXT. Observe que:
- Os conjuntos de registro SOA são criados automaticamente com cada zona, eles não podem ser criados separadamente.
- Os registros SPF devem ser criados usando o tipo de registro TXT. Para obter mais informações, consulte [esta página](http://tools.ietf.org/html/rfc7208#section-3.1).

No Azure DNS, os registros são especificados usando nomes relativos. Um nome de domínio "totalmente qualificado" (FQDN) inclui o nome de zona, enquanto um nome "relativo" não. Por exemplo, o nome de registro relativo "www" na zona "contoso.com" dá o nome totalmente qualificado de registro www.contoso.com.

## <a name="about-record-sets"></a>Sobre conjuntos de registros

Às vezes, você precisa criar mais de um registro DNS com um nome e tipo. Por exemplo, suponha que o site de "www.contoso.com" estiver hospedado em dois endereços IP diferentes. O site requer duas diferentes registros a, uma para cada endereço IP. Este é um exemplo de um conjunto de registros:

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

Azure DNS gerencia os registros DNS usando conjuntos de registros. Um conjunto de registros é o conjunto de registros DNS em uma zona tiver o mesmo nome e são do mesmo tipo. A maioria dos conjuntos de registros contêm um único registro, mas exemplos como este, em que um conjunto de registros contém mais de um registro, não são incomuns.

Conjuntos de registros SOA e CNAME são exceções. Os padrões DNS não permitirem que vários registros com o mesmo nome para esses tipos.

O tempo de vida ou TTL, especifica por quanto tempo cada registro é armazenada em cache por clientes antes está sendo consultado novamente. Neste exemplo, o TTL é 3600 segundos ou 1 hora. O TTL for especificado para o conjunto de registros, não para cada registro, para que o mesmo valor é usado para todos os registros dentro desse conjunto de registros.

#### <a name="wildcard-record-sets"></a>Conjuntos de registro de curinga

DNS Azure ofereça suporte a [registros de curinga](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Eles são retornados para qualquer consulta com um nome correspondente (a menos que haja uma correspondência detalhada de um conjunto de registros não curinga). Os conjuntos de registro de curinga são suportados para todos os tipos de registro, exceto NS e SOA.  

Para criar um conjunto de registros de curinga, use o nome do conjunto de registros "\*". Use um nome com o rótulo "\*", por exemplo,"\*foo".

#### <a name="cname-record-sets"></a>Conjuntos de registro CNAME

Os conjuntos de registro CNAME não coexistam com outros conjuntos de registro com o mesmo nome. Por exemplo, é possível criar um registro CNAME definida com o nome relativo "www" e um registro com o nome relativo "www" ao mesmo tempo. Porque o apex zona (nome = ‘@’) sempre contém o NS e SOA gravam conjuntos que foram criados quando a zona foi criada, você não pode criar um registro CNAME definidos a apex a zona. Estas restrições surgirem com os padrões DNS e não são limitações do Azure DNS.
