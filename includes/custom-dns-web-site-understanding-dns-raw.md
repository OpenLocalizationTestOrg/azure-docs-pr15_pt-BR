O sistema de nome de domínio (DNS) é usado para localizar recursos na internet. Por exemplo, quando você inserir um endereço de aplicativo da web no seu navegador ou clique em um link em uma página da web, ele usa DNS para traduzir o domínio para um endereço IP. O endereço IP é semelhante a um endereço, mas não é muito humano amigável. Por exemplo, é muito mais fácil de lembrar um nome DNS como **contoso.com** que se lembrar do endereço IP como 192.168.1.88 ou 2001:0:4137:1f67:24a2:3888:9cce:fea3.

O sistema DNS baseia-se nos *registros*. Registros associar um específico *nome*, como **contoso.com**, um endereço IP ou outro nome DNS. Quando um aplicativo, como um navegador da web, procura um nome no DNS, ele encontra o registro e usa tudo o que ela aponta para como o endereço. Se o valor aponta para é um endereço IP, o navegador usará esse valor. Se ela aponta para outro nome DNS, o aplicativo tem de fazer resolução novamente. Por fim, toda a resolução de nome terminará em um endereço IP.

Quando você cria um aplicativo web no aplicativo de serviço, um nome DNS é automaticamente atribuído para o aplicativo web. Esse nome assume a forma de ** &lt;yourwebappname&gt;. azurewebsites.net**. Há também um endereço IP virtual disponível para uso quando os registros de DNS de criação, para que você pode criar registros que apontem para o **. azurewebsites.net**, ou você pode apontar para o endereço IP.

> [AZURE.NOTE] O endereço IP do seu aplicativo web irá alterar se você excluir e recria seu aplicativo web ou alterar o modo de plano de serviço de aplicativo para **livre** após ela ter sido definida **básica**, **compartilhado**ou **padrão**.

Também há vários tipos de registros, cada uma com suas próprias funções e limitações, mas web Apps nós apenas me preocupar com os dois registros *e *CNAME* * .

###<a name="address-record-a-record"></a>Registro de endereço (um registro)

Um registro de um domínio, como **contoso.com** ou **www.contoso.com**, *ou um domínio curinga* mapas como ** \*. contoso.com**, para um endereço IP. No caso de um aplicativo da web no aplicativo de serviço, o IP virtual do serviço ou um IP específico de endereço que você comprou para seu aplicativo web.

Os principais benefícios de um registro ao longo de um registro CNAME são:

* É possível mapear um domínio raiz como **contoso.com** para um endereço IP; Muitos registradores permitem apenas este usando um registros

* Você pode ter uma entrada que usa um curinga, como ** \*. contoso.com**, que seria lidar com solicitações de vários subdomínios como **mail.contoso.com**, **blogs.contoso.com**ou **www.contso.com**.

> [AZURE.NOTE] Desde que um registro é mapeado para um endereço IP estático, ela não poderá resolver automaticamente alterações para o endereço IP do seu aplicativo web. É fornecido um endereço IP para uso com registros quando você configura as definições de nome de domínio personalizado para seu aplicativo web; No entanto, esse valor pode alterar se você excluir e recria seu aplicativo web ou alterar o modo de plano de serviço de aplicativo para trás para **livre**.

###<a name="alias-record-cname-record"></a>Alias (registro CNAME)

Um registro CNAME mapeia um nome DNS *específico* , como **mail.contoso.com** ou **www.contoso.com**, para outro nome de domínio (canônico). No caso de aplicativo de serviço Web Apps, o nome de domínio canônica é a ** &lt;yourwebappname >. azurewebsites.net** nome de domínio de seu aplicativo web. Uma vez criado, o CNAME cria um alias para o ** &lt;yourwebappname >. azurewebsites.net** nome de domínio. A entrada CNAME resolverá para o endereço IP do seu ** &lt;yourwebappname >. azurewebsites.net** nome de domínio automaticamente, para que se altera o endereço IP do web app, você não terá qualquer ação.

> [AZURE.NOTE] Alguns registradores de domínio só permitem mapear subdomínios ao usar um registro CNAME, como **www.contoso.com**e não nomes de raiz, como **contoso.com**. Para obter mais informações sobre registros CNAME, consulte a documentação fornecida pelo seu registrador de domínios, <a href="http://en.wikipedia.org/wiki/CNAME_record">na entrada Wikipedia no registro CNAME</a>ou o documento de <a href="http://tools.ietf.org/html/rfc1035">Nomes de domínio IETF - implementação e especificação</a> .

###<a name="web-app-dns-specifics"></a>Especificações DNS do aplicativo Web

Usando um registro com aplicativos Web requer que você primeiro crie um dos seguintes registros TXT:

* **Para o domínio raiz** - TXT de DNS de um registro de **@** para ** &lt;yourwebappname&gt;. azurewebsites.net**.

* **Para um domínio específico de subum** nome de DNS ** &lt;subdomínio >** para ** &lt;yourwebappname&gt;. azurewebsites.net**. Por exemplo, **blogs** se o registro for para **blogs.contoso.com**.

* **Para o curinga sub-dodmains** - TXT de DNS de um registro de * * * para ** &lt;yourwebappname&gt;. azurewebsites.net**.

Esse registro TXT é usado para confirmar que você é o proprietário do domínio que você está tentando usar. Isso é além de criar um registro apontando para o endereço IP virtual de seu aplicativo web.

Você pode encontrar o endereço IP e **. azurewebsites.net** nomes para seu aplicativo web, executando as seguintes etapas:

1. No navegador, abra o [Portal do Azure](https://portal.azure.com).

2. Na lâmina **Aplicativos Web** , clique no nome do seu aplicativo web e, em seguida, selecione **domínios personalizados** na parte inferior da página.

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. Na lâmina **domínios personalizados** , você verá o endereço IP virtual. Salvar essas informações, pois ele será usado ao criar registros DNS

    ![](./media/custom-dns-web-site/virtual-ip-address.png)

    > [AZURE.NOTE] Você não pode usar nomes de domínio personalizado com um aplicativo de web **livre** e atualize o plano de serviço de aplicativo **compartilhado**, **básico**, **padrão**ou **Premium** camada. Para obter mais informações sobre o plano de serviço de aplicativo de preços níveis, inclusive como alterar o nível de preço de seu aplicativo web, veja [como dimensionar aplicativos web](../articles/web-sites-scale.md).
