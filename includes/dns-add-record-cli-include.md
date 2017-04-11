#### <a name="create-an-a-record-set-with-single-record"></a>Criar um registro com registro único

Para criar um conjunto de registros, use `azure network dns record-set create`. Especificar o grupo de recursos, o nome da zona registro que defina o nome relativo, tipo de registro e tempo de vida (TTL).

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

Depois de criar o registro conjunto, adicionar o endereço IPv4 para o registro definido com `azure network dns record-set add-record`.

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1

#### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Criar um registro de AAAA definida com um único registro

    azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

#### <a name="create-a-cname-record-set-with-a-single-record"></a>Criar um registro CNAME definida com um único registro

Registros CNAME permitem somente um valor de cadeia de caracteres único.


    azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300

    azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"


#### <a name="create-an-mx-record-set-with-a-single-record"></a>Criar um registro MX definida com um único registro

Neste exemplo, usamos o nome de conjunto de registros "@" para criar o registro MX no apex a zona (neste caso, "contoso.com"). Isso é comum de registro MX.

    azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

    azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


#### <a name="create-an-ns-record-set-with-a-single-record"></a>Criar um registro NS definida com um único registro

    azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300

    azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com"

#### <a name="create-a-ptr-record-set-with-a-single-record"></a>Criar um registro PTR definida com um único registro  
Nesse caso ' minha-arpa-zone.com' representa a zona ARPA que representa o intervalo IP.  Cada registro PTR definido desta zona corresponde a um endereço IP dentro este intervalo IP.    

    azure network dns record-set add-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"   

#### <a name="create-an-srv-record-set-with-a-single-record"></a>Criar um registro SRV definida com um único registro

Se você estiver criando um registro SRV na raiz da zona, você pode especificar "_service" e "_protocol" no nome do registro. Não é necessário incluir "@" no nome do registro.


    azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com"

#### <a name="create-a-txt-record-set-with-single-record"></a>Crie um registro TXT definida com registro único

    azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record"
