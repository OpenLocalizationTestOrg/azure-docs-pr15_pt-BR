#<a name="configuring-a-custom-domain-name-for-an-azure-website"></a>Configurando um nome de domínio personalizado para um site do Azure

Quando você cria um site, o Azure fornece um subdomínio amigável no domínio azurewebsites.net, para que os usuários podem acessar seu site usando uma URL, como http://&lt;meusite >. azurewebsites.net. No entanto, se você configurar seus sites para compartilhado ou modo padrão, você pode mapear seu site para seu próprio nome de domínio.

Opcionalmente, você pode usar o Gerenciador de tráfego do Azure para carregar o tráfego de entrada de saldo para seu site. Para obter mais informações sobre como funciona o Gerenciador de tráfego com sites, consulte [Controlar o tráfego de Sites da Web do Azure com o Gerenciador de tráfego do Azure][trafficmanager].

> [AZURE.NOTE] Os procedimentos nesta tarefa se aplicam aos sites do Azure; para serviços de nuvem, consulte <a href="/develop/net/common-tasks/custom-dns/">Configurar um nome de domínio personalizado no Azure</a>.

> [AZURE.NOTE] As etapas nesta tarefa exigem que você configure seus sites para Shared ou modo padrão, que possa alterar quanto você é cobrado para sua assinatura. Consulte <a href="/pricing/details/web-sites/">Detalhes de preços de sites</a> para obter mais informações.

Neste artigo:

-   [Noções básicas sobre registros CNAME e A](#understanding-records)
-   [Configurar seus sites para o modo padrão ou compartilhado](#bkmk_configsharedmode)
-   [Adicionar ao Gerenciador de tráfego de seus sites da web](#trafficmanager)
-   [Adicionar um CNAME do seu domínio personalizado](#bkmk_configurecname)
-   [Adicionar um registro a para seu domínio personalizado](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>Compreender os registros CNAME e A</h2>

CNAME (ou registros de alias) e os dois registros permitem que você associar um nome de domínio um site, no entanto, cada um deles funcionam de maneira diferente.

###<a name="cname-or-alias-record"></a>Registro CNAME ou Alias

Um registro CNAME mapeia um domínio *específico* , como **contoso.com** ou **www.contoso.com**, para um nome de domínio canônico. Nesse caso, o nome de domínio canônica é ao ** &lt;myapp >. azurewebsites.net** nome de domínio do seu site Azure ou o ** &lt;myapp >. trafficmgr.com** nome de domínio do seu perfil do Gerenciador de tráfego. Uma vez criado, o CNAME cria um alias para o ** &lt;myapp >. azurewebsites.net** ou ** &lt;myapp >. trafficmgr.com** nome de domínio. A entrada CNAME resolverá para o endereço IP do seu ** &lt;myapp >. azurewebsites.net** ou ** &lt;myapp >. trafficmgr.com** nome de domínio automaticamente, para que se altera o endereço IP do site, você não terá qualquer ação.

> [AZURE.NOTE] Alguns registradores de domínio só permitem mapear subdomínios ao usar um registro CNAME, como www.contoso.com e não nomes de raiz, como contoso.com. Para obter mais informações sobre registros CNAME, consulte a documentação fornecida pelo seu registrador de domínios, <a href="http://en.wikipedia.org/wiki/CNAME_record">na entrada Wikipedia no registro CNAME</a>ou o documento de <a href="http://tools.ietf.org/html/rfc1035">Nomes de domínio IETF - implementação e especificação</a> .

###<a name="a-record"></a>Um registro

Um registro de um domínio, como **contoso.com** ou **www.contoso.com**, *ou um domínio curinga* mapas como ** \*. contoso.com**, para um endereço IP. No caso de um site do Azure, o IP virtual do serviço ou um IP específico de endereço que você comprou para seu site. Portanto, o principal benefício de um registro ao longo de um registro CNAME é que você pode ter uma entrada que usa um curinga, como * **. contoso.com**, que usaria solicitações de vários subdomínios, como * *mail.contoso.com**, * *login.contoso.com**, ou * *www.contso.com**.

> [AZURE.NOTE] Desde que um registro é mapeado para um endereço IP estático, ele não pode resolver automaticamente as alterações para o endereço IP do seu site. É fornecido um endereço IP para uso com registros quando você configura as definições de nome de domínio personalizado para seu site; No entanto, esse valor pode alterar se você excluir e recria seu site ou alterar o modo de site para trás para liberar.

> [AZURE.NOTE] Registros não podem ser usados com o Gerenciador de tráfego de balanceamento de carga. Para obter mais informações, consulte [Controlar o tráfego de Sites da Web do Azure com o Gerenciador de tráfego do Azure][trafficmanager].

<a name="bkmk_configsharedmode"></a><h2>Configurar seus sites para o modo padrão ou compartilhado</h2>

Definir um nome de domínio personalizado em um site está disponível somente para o Shared e modos de padrão para sites Azure. Antes de trocar de um site do modo de site gratuito para compartilhado ou modo de site padrão, primeiro você deve remover caps gastos no lugar para sua assinatura do site. Para obter mais informações sobre preços de modo de compartilhado e padrão, consulte [Detalhes de preços][PricingDetails].

1. No navegador, abra o [Portal de gerenciamento][portal].
2. Na guia **sites** , clique no nome do seu site.

    ![][standardmode1]

3. Clique na guia **escala** .

    ![][standardmode2]


4. Na seção **Geral** , defina o modo de site clicando em **compartilhado**.

    ![][standardmode3]

    > [AZURE.NOTE] Se você usará o Gerenciador de tráfego com este site, você deve usar Selecione modo padrão em vez de compartilhado.

5. Clique em **Salvar**.
6. Quando solicitado sobre o aumento no custo de modo compartilhado (ou de modo padrão se você optar por padrão), clique em **Sim** se você concordar.

    <!--![][standardmode4]-->

    **Observação**<br />
   Se você receber um erro de "Configurando escala para o site 'nome do site' falhou", você pode usar o botão Detalhes para obter mais informações.

<a name="trafficmanager"></a><h2>(Opcional) Adicione seus sites ao Gerenciador de tráfego</h2>

Se você quiser usar seu site com o Gerenciador de tráfego, execute as seguintes etapas.

1. Se você não tiver um perfil do Gerenciador de tráfego, use as informações em [criar um perfil do Gerenciador de tráfego usando a criação rápida] [ createprofile] para criar uma. Observação a **. trafficmgr.com** nome de domínio associado ao seu perfil do Gerenciador de tráfego. Isso será usado em uma etapa posterior.

2. Use as informações em [Adicionar ou excluir pontos de extremidade] [ addendpoint] para adicionar seu site como um ponto de extremidade no seu perfil do Gerenciador de tráfego.

    > [AZURE.NOTE] Se seu site não estiver listado, ao adicionar um ponto de extremidade, verifique se ele está configurado para o modo padrão. Você deve usar o modo padrão para seu site para trabalhar com o Gerenciador de tráfego.

3. Faça logon no site do registrador DNS e vá para a página de gerenciamento de DNS. Procure links ou áreas do site rotulado como **Nome de domínio**, **DNS**ou **Gerenciamento de servidor de nome**.

4. Agora, localize onde você pode selecionar ou inserir registros CNAME. Você pode precisar selecionar o tipo de registro de uma queda ou ir para uma página de configurações avançadas. Você deve procurar palavras **CNAME**, **Alias**ou **subdomínios**.

5. Você também deve fornecer o alias de domínio ou subdomínio para o CNAME. Por exemplo, **"www"** se desejar criar um alias para **www.customdomain.com**.

5. Você também deve fornecer um nome de host é o nome de domínio canônico para este alias CNAME. Este é o **. trafficmgr.com** nome para seu site.

Por exemplo, o seguinte registro CNAME encaminha todo o tráfego de **www.contoso.com** para **contoso.trafficmgr.com**, o nome de domínio de um site:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nome de alias/Host/subdomínio</strong></td>
<td><strong>Domínio canônico</strong></td>
</tr>
<tr>
<td>www</td>
<td>Contoso.trafficmgr.com</td>
</tr>
</table>

Um visitante de **www.contoso.com** nunca verá o host true (contoso.azurewebsite.net), para que o processo de encaminhamento é invisível para o usuário final.

> [AZURE.NOTE] Se você estiver usando o Gerenciador de tráfego com um site, você não precisa executar as etapas nas seções a seguir, '**Adicionar um CNAME do seu domínio personalizado**' e '**Adicionar um registro a para seu domínio personalizado**'. O registro CNAME criado nas etapas anteriores vai rotear o tráfego de entrada ao Gerenciador de tráfego, que encaminha o tráfego para o site endpoint(s).

<a name="bkmk_configurecname"></a><h2>Adicionar um CNAME do seu domínio personalizado</h2>

Para criar um registro CNAME, adicione uma nova entrada na tabela de DNS do seu domínio personalizado usando ferramentas fornecidas pelo seu registrador de domínios. Cada registrador tem um método semelhante, mas ligeiramente diferentes especificar um registro CNAME, mas os conceitos são os mesmos.

1. Use um destes métodos para localizar o **. azurewebsite.net** nome de domínio atribuída ao seu site.

    * Faça logon no [Portal de gerenciamento do Azure][portal], selecione seu site, selecione **Painel de controle**e, em seguida, localize a entrada de **URL do Site** na seção **rapidamente** .

    * Instalar e configurar [O Powershell do Azure](/manage/install-and-configure-windows-powershell/)e, em seguida, use o seguinte comando:

            get-azurewebsite yoursitename | select hostnames

    * Instalar e configurar a [Interface de linha de comando do Azure](/manage/install-and-configure-cli/)e, em seguida, use o seguinte comando:

            azure site domain list yoursitename

    Salvar esta **. azurewebsite.net** nome, pois ele será usado nas etapas a seguir.

3. Faça logon no site do registrador DNS e vá para a página de gerenciamento de DNS. Procure links ou áreas do site rotulado como **Nome de domínio**, **DNS**ou **Gerenciamento de servidor de nome**.

4. Agora, localize onde você pode selecionar ou inserir registros CNAME. Você pode precisar selecionar o tipo de registro de uma queda ou ir para uma página de configurações avançadas. Você deve procurar palavras **CNAME**, **Alias**ou **subdomínios**.

5. Você também deve fornecer o alias de domínio ou subdomínio para o CNAME. Por exemplo, **"www"** se desejar criar um alias para **www.customdomain.com**. Se você quiser criar um alias para o domínio raiz, ele pode estar listado como a '**@**' símbolo em Ferramentas DNS do seu registrador de domínios.

5. Você também deve fornecer um nome de host é o nome de domínio canônico para este alias CNAME. Este é o **. azurewebsite.net** nome para seu site.

Por exemplo, o seguinte registro CNAME encaminha todo o tráfego de **www.contoso.com** para **contoso.azurewebsite.net**, o nome de domínio de um site:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nome de alias/Host/subdomínio</strong></td>
<td><strong>Domínio canônico</strong></td>
</tr>
<tr>
<td>www</td>
<td>Contoso.azurewebsite.NET</td>
</tr>
</table>

Um visitante de **www.contoso.com** nunca verá o host true (contoso.azurewebsite.net), para que o processo de encaminhamento é invisível para o usuário final.

> [AZURE.NOTE] O exemplo acima se aplica somente tráfego no subdomínio __www__ . Desde que você não pode usar caracteres curinga com registros CNAME, você deve criar um CNAME para cada domínio/subdomínio. Se você deseja direcionar o tráfego de subdomínios, como *. contoso.com, para o endereço de azurewebsite.net, você pode configurar uma entrada de __Redirecionamento de URL__ ou __URL encaminhar__ em suas configurações de DNS ou criar um registro.

> [AZURE.NOTE] Pode levar alguns minutos para que seu CNAME propagar pelo sistema DNS. Você não pode definir o CNAME para o site até que o CNAME tiver propagado. Você pode usar um serviço como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para verificar se o CNAME está disponível.

###<a name="add-the-domain-name-to-your-website"></a>Adicione o nome de domínio para seu site

Depois que o registro CNAME para o nome de domínio tiver propagado, você deve associá-lo com seu site. Você pode adicionar o nome de domínio personalizado definido pelo registro CNAME para o seu site usando um do Azure Interface de linha (Azure comando) ou usando o Portal de gerenciamento do Azure.

**Para adicionar um nome de domínio usando as ferramentas de linha de comando**

Instalar e configurar a [Interface de linha de comando do Azure](/manage/install-and-configure-cli/)e, em seguida, use o seguinte comando:

    azure site domain add customdomain yoursitename

Por exemplo, o seguinte adicionará um nome de domínio personalizado do **www.contoso.com** para o site de **contoso.azurewebsite.net** :

    azure site domain add www.contoso.com contoso

Você pode confirmar que o nome de domínio personalizado foi adicionado ao site usando o seguinte comando:

    azure site domain list yoursitename

A lista retornada por esse comando deve conter o nome de domínio personalizado, bem como o padrão **. azurewebsite.net** entrada.

**Para adicionar um nome de domínio usando o Portal de gerenciamento do Azure**

1. No navegador, abra o [Portal de gerenciamento do Azure][portal].

2. Na guia **sites** , clique no nome do seu site, selecione **Painel de controle**e, em seguida, selecione **Gerenciar domínios** na parte inferior da página.

    ![][setcname2]

6. Na caixa de texto de **Nomes de domínio** , digite o nome do domínio que você tenha configurado.

    ![][setcname3]

6. Clique na marca de seleção para aceitar o nome de domínio.

Após a conclusão da configuração, o nome de domínio personalizado será listado na seção **nomes de domínio** da página **Configure** do seu site.

<a name="bkmk_configurearecord"></a><h2>Adicionar um registro a para seu domínio personalizado</h2>

Para criar um registro, você deve primeiro encontrar o endereço IP do seu site. Em seguida, adicione uma entrada na tabela de DNS para seu domínio personalizado usando as ferramentas fornecidas pelo seu registrador de domínios. Cada registrador tem um método semelhante, mas ligeiramente diferentes especificar um registro, mas os conceitos são os mesmos. Além de criar um registro, você também deve criar um registro CNAME que Azure usa para verificar o registro.

1. No navegador, abra o [Portal de gerenciamento do Azure][portal].

2. Na guia **sites** , clique no nome do seu site, selecione **Painel de controle**e, em seguida, selecione **Gerenciar domínios** na parte inferior da tela.

    ![][setcname2]

5. Na caixa de diálogo **Gerenciar domínios personalizados** , localize **O endereço IP para usar ao configurar registros**. Copie o endereço IP. Isso será usado ao criar o registro.

5. Na caixa de diálogo **Gerenciar domínios personalizados** , observe o nome de domínio awverify no final do texto na parte superior da caixa de diálogo. Ele deve ser **awverify.mysite.azurewebsites.net** onde **meusite** é o nome do seu site. Copie isso, pois é o nome de domínio usado ao criar a registro CNAME de verificação.

6. Faça logon no site do registrador DNS e vá para a página de gerenciamento de DNS. Procure links ou áreas do site rotulado como **Nome de domínio**, **DNS**ou **Gerenciamento de servidor de nome**.

6. Encontre onde você pode selecionar ou inserir registros A e CNAME. Você pode precisar selecionar o tipo de registro de uma queda ou ir para uma página de configurações avançadas.

7. Execute as seguintes etapas para criar o registro:

    1. Selecione ou insira o domínio ou o subdomínio que usará o registro. Por exemplo, selecione **www** se desejar criar um alias para **www.customdomain.com**. Se você quiser criar uma entrada de curinga para todos os subdomínios, digite '__*__'. Isso assumirá todos os subdomínios como **mail.customdomain.com**, **login.customdomain.com**e **www.customdomain.com**.

        Se você quiser criar um registro para o domínio raiz, ele pode estar listado como a '**@**' símbolo em Ferramentas DNS do seu registrador de domínios.

    2. Insira o endereço IP do seu serviço de nuvem no campo fornecido. Isto associa a entrada de domínio usada no registro com o endereço IP da sua implantação do serviço de nuvem.

        Por exemplo, a seguinte que um registro encaminha todo o tráfego de **contoso.com** para **137.135.70.239**, o endereço IP do nosso aplicativo implantado:

        <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
        <tr>
        <td><strong>Nome de host/subdomínio</strong></td>
        <td><strong>Endereço IP</strong></td>
        </tr>
        <tr>
        <td>@</td>
        <td>137.135.70.239</td>
        </tr>
        </table>

        Este exemplo demonstra Criando um registro para o domínio raiz. Se você desejar criar uma entrada de curinga para cobrir todos os subdomínios, você digitaria '__*__' como o subdomínio.

7. Em seguida, crie um registro CNAME que tenha um alias de **awverify**e um domínio canônico de **awverify.mysite.azurewebsites.net** obtido anteriormente.

    > [AZURE.NOTE] Embora um alias de awverify pode funcionar para alguns registradores, outras pessoas podem exigir o nome de domínio completo alias de awverify.www.customdomainname.com ou awverify.customdomainname.com.

    Por exemplo, este procedimento criará um registro CNAME que Azure pode usar para verificar a configuração de registro de um.

    <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
    <tr>
    <td><strong>Nome de alias/Host/subdomínio</strong></td>
    <td><strong>Domínio canônico</strong></td>
    </tr>
    <tr>
    <td>awverify</td>
    <td>awverify.contoso.azurewebsites.NET</td>
    </tr>
    </table>

> [AZURE.NOTE] Pode levar algum tempo para o awverify CNAME para propagar pelo sistema DNS. Você não pode definir o nome de domínio personalizado definido pelo registro para o site até que o awverify CNAME tiver propagado. Você pode usar um serviço como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para verificar se o CNAME está disponível.

###<a name="add-the-domain-name-to-your-website"></a>Adicione o nome de domínio para seu site

Depois que o registro CNAME de **awverify** nome do domínio tiver propagado, em seguida, você pode associar o domínio personalizado definido pelo registro com seu site. Você pode adicionar o nome de domínio personalizado definido pelo registro ao seu site usando a CLI do Azure ou usando o Portal de gerenciamento do Azure.

**Para adicionar um nome de domínio usando a Interface de linha do Azure (Azure comando)**

Instalar e configurar o [Azure CLI](/manage/install-and-configure-cli/)e, em seguida, use o seguinte comando:

    azure site domain add customdomain yoursitename

Por exemplo, o seguinte adicionará um nome de domínio personalizado de **contoso.com** para o site de **contoso.azurewebsite.net** :

    azure site domain add contoso.com contoso

Você pode confirmar que o nome de domínio personalizado foi adicionado ao site usando o seguinte comando:

    azure site domain list yoursitename

A lista retornada por esse comando deve conter o nome de domínio personalizado, bem como o padrão **. azurewebsite.net** entrada.

**Para adicionar um nome de domínio usando o Portal de gerenciamento do Azure**

1. No navegador, abra o [Portal de gerenciamento do Azure][portal].

2. Na guia **sites** , clique no nome do seu site, selecione **Painel de controle**e, em seguida, selecione **Gerenciar domínios** na parte inferior da página.

    ![][setcname2]

6. Na caixa de texto de **Nomes de domínio** , digite o nome do domínio que você tenha configurado.

    ![][setcname3]

6. Clique na marca de seleção para aceitar o nome de domínio.

Após a conclusão da configuração, o nome de domínio personalizado será listado na seção **nomes de domínio** da página **Configure** do seu site.

> [AZURE.NOTE] Depois de ter adicionado o nome de domínio personalizado definido pelo registro ao seu site, você pode remover o registro CNAME de awverify usando as ferramentas fornecidas pelo seu registrador de domínios. No entanto, se você deseja adicionar A outro registro no futuro, será necessário recriar o awverify registro antes de poder associar o novo nome de domínio definido pelo novo um registro ao site.

## <a name="next-steps"></a>Próximas etapas

-   [Como gerenciar sites da web](/manage/services/web-sites/how-to-manage-websites/)

-   [Configurar um certificado SSL para Sites da Web](/develop/net/common-tasks/enable-ssl-web-site/)


<!-- Bookmarks -->

[Configure your web sites for shared mode]: #bkmk_configsharedmode
[Configure the CNAME on your domain registrar]: #bkmk_configurecname
[Configure a CNAME verification record on your domain registrar]: #bkmk_configurecname
[Configure an A record for the domain name]:#bkmk_configurearecord
[Set the domain name in management portal]: #bkmk_setcname

<!-- Links -->

[PricingDetails]: /pricing/details/
[portal]: http://manage.windowsazure.com
[digweb]: http://www.digwebinterface.com/
[cloudservicedns]: ../articles/custom-dns.md
[trafficmanager]: ../articles/app-service-web/web-sites-traffic-manager.md
[addendpoint]: ../articles/traffic-manager/traffic-manager-endpoints.md
[createprofile]: ../articles/traffic-manager/traffic-manager-manage-profiles.md

<!-- images -->

[setcname1]: ../media/dncmntask-cname-5.png


<!-- images -->
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png


[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png
