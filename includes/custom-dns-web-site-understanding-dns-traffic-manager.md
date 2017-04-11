O sistema de nome de domínio (DNS) é usado para localizar itens na internet. Por exemplo, quando você inserir um endereço no seu navegador ou clique em um link em uma página da web, ele usa DNS para traduzir o domínio para um endereço IP. O endereço IP é semelhante a um endereço, mas não é muito humano amigável. Por exemplo, é muito mais fácil de lembrar um nome DNS como **contoso.com** que se lembrar do endereço IP como 192.168.1.88 ou 2001:0:4137:1f67:24a2:3888:9cce:fea3.

O sistema DNS baseia-se nos *registros*. Registros associar um específico *nome*, como **contoso.com**, um endereço IP ou outro nome DNS. Quando um aplicativo, como um navegador da web, procura um nome no DNS, ele encontra o registro e usa tudo o que ela aponta para como o endereço. Se o valor aponta para é um endereço IP, o navegador usará esse valor. Se ela aponta para outro nome DNS, o aplicativo tem de fazer resolução novamente. Por fim, toda a resolução de nome terminará em um endereço IP.

Quando você cria um site do Azure, um nome de DNS é automaticamente atribuído ao site. Esse nome assume a forma de ** &lt;yoursitename&gt;. azurewebsites.net**. Quando você adicionar seu site como um ponto de extremidade do Gerenciador de tráfego do Azure, seu site, em seguida, é acessível através do ** &lt;yourtrafficmanagerprofile&gt;. trafficmanager.net** domínio.

> [AZURE.NOTE] Quando seu site estiver configurado como um ponto de extremidade do Gerenciador de tráfego, você usará o **. trafficmanager.net** endereço quando criar registros de DNS.

> Você só pode usar registros CNAME com o Gerenciador de tráfego

Também existem vários tipos de registros, cada uma com suas próprias funções e limitações, mas sites configurado para como pontos de extremidade do Gerenciador de tráfego, podemos cuidado sobre uma; somente Registros *CNAME* .

###<a name="cname-or-alias-record"></a>Registro CNAME ou Alias

Um registro CNAME mapeia um nome DNS *específico* , como **mail.contoso.com** ou **www.contoso.com**, para outro nome de domínio (canônico). No caso de sites do Azure usando o Gerenciador de tráfego, o nome de domínio canônica é a ** &lt;myapp >. trafficmanager.net** nome de domínio do seu perfil do Gerenciador de tráfego. Uma vez criado, o CNAME cria um alias para o ** &lt;myapp >. trafficmanager.net** nome de domínio. A entrada CNAME resolverá para o endereço IP do seu ** &lt;myapp >. trafficmanager.net** nome de domínio automaticamente, para que se altera o endereço IP do site, você não terá qualquer ação.

Quando o tráfego chega no Gerenciador de tráfego, ele encaminha o tráfego para seu site, usando a método para que está configurado de balanceamento de carga. Isso é totalmente transparente para os visitantes do seu site. Eles só verá o nome de domínio personalizado no navegador deles.

> [AZURE.NOTE] Alguns registradores de domínio só permitem mapear subdomínios ao usar um registro CNAME, como **www.contoso.com**e não nomes de raiz, como **contoso.com**. Para obter mais informações sobre registros CNAME, consulte a documentação fornecida pelo seu registrador de domínios, <a href="http://en.wikipedia.org/wiki/CNAME_record">na entrada Wikipedia no registro CNAME</a>ou o documento de <a href="http://tools.ietf.org/html/rfc1035">Nomes de domínio IETF - implementação e especificação</a> .
