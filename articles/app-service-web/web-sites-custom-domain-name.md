<properties
    pageTitle="Mapear um nome de domínio personalizado para um aplicativo do Azure"
    description="Saiba como mapear um nome de domínio personalizado (domínio banido) para seu aplicativo no serviço de aplicativo do Azure."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="jimbe"
    tags="top-support-issue"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/27/2016"
    ms.author="cephalin"/>

# <a name="map-a-custom-domain-name-to-an-azure-app"></a>Mapear um nome de domínio personalizado para um aplicativo do Azure

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

Este artigo mostra como mapear manualmente um nome de domínio personalizado para seu aplicativo web, back-end do aplicativo móvel ou aplicativo de API do [Serviço de aplicativo do Azure](../app-service/app-service-value-prop-what-is.md). 

Seu aplicativo já vem com um subdomínio exclusivo do azurewebsites.net. Por exemplo, se o nome do seu aplicativo for **contoso**, seu nome de domínio é **contoso.azurewebsites.net**. No entanto, você pode mapear um domínio personalizado nome do aplicativo assim que sua URL, tais como `www.contoso.com`, reflete sua marca.

>[AZURE.NOTE] Obtenha ajuda do Azure especialistas nos [fóruns do Azure](https://azure.microsoft.com/support/forums/). Para ainda mais alto nível de suporte, vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter suporte**.

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## <a name="buy-a-new-custom-domain-in-azure-portal"></a>Comprar um novo domínio personalizado no portal do Azure

Se você ainda não tiver adquirido um nome de domínio personalizado, você pode comprar um e gerenciá-lo diretamente em configurações do seu aplicativo no [portal do Azure](https://portal.azure.com). Esta opção torna mais fácil mapear um domínio personalizado para seu aplicativo, se seu aplicativo usa o [Gerenciador de tráfego do Azure](web-sites-traffic-manager-custom-domain-name.md) ou não. 

Para obter instruções, consulte [comprar um nome de domínio personalizado para o serviço de aplicativo](custom-dns-web-site-buydomains-web-app.md).

## <a name="map-a-custom-domain-you-purchased-externally"></a>Mapear um domínio personalizado que você comprou externamente

Se você já tiver adquirido um domínio personalizado do [Azure DNS](https://azure.microsoft.com/services/dns/) ou de um provedor de terceiros, há três etapas principais para mapear o domínio personalizado para seu aplicativo:

1. [Endereço IP do aplicativo Get *(somente um registro)* ](#vip).
2. [Criar os registros DNS que mapeiam seu domínio para o seu aplicativo](#createdns). 
    - **Onde**: sua ferramenta de gerenciamento do registrador do domínio (por exemplo, Azure DNS, GoDaddy, etc.).
    - **Por que**: para que seu registrador de domínio Saiba ser resolvida o domínio personalizado desejado para seu aplicativo do Azure.
1. [Habilitar o nome de domínio personalizado para seu aplicativo do Azure](#enable).
    - **Onde**: o [portal do Azure](https://portal.azure.com).
    - **Por que**: para que seu aplicativo saiba responder às solicitações feitas para o nome de domínio personalizado.
3. [Verificar o DNS propagação](#verify).

### <a name="types-of-domains-you-can-map"></a>Tipos de domínios que você possa mapear

Serviço de aplicativo do Azure lhe permite mapear as seguintes categorias de domínios personalizados para seu aplicativo.

- **Domínio raiz** - nome do domínio que você reservou com o registrador (representado pela `@` hospedar o registro, normalmente). Por exemplo, **contoso.com**.
- **Subdomínio** - qualquer domínio que está em seu domínio raiz. Por exemplo, **www.contoso.com** (representado pela `www` registro do host).  Você pode mapear subdomínios diferentes do mesmo domínio raiz para diferentes aplicativos no Azure.
- **Domínio curinga** - [qualquer subdomínio cujo rótulo DNS mais à esquerda é `*` ](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (por exemplo, hospedar registros `*` e `*.blogs`). Por exemplo, ** \*. contoso.com**.

### <a name="types-of-dns-records-you-can-use"></a>Tipos de registros DNS, que você pode usar

Dependendo das suas necessidades, você pode usar dois tipos de registros DNS padrão para mapear seu domínio personalizado: 

- [R](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) - mapas seu nome de domínio personalizado para virtual IP do aplicativo Azure endereço diretamente. 
- [CNAME](https://en.wikipedia.org/wiki/CNAME_record) - mapeia seu nome de domínio personalizado para o nome de domínio Azure do seu aplicativo, * *&lt;*appname*>. azurewebsites.net**. 

A vantagem de CNAME é que ele persista entre alterações de endereço IP. Se você excluir e recria seu aplicativo ou alterar de um nível de preços superior voltar para a camada de **compartilhado** , endereço IP virtual do seu aplicativo pode mudar. Por meio de alteração, um registro CNAME ainda estiver válido, enquanto um registro requer uma atualização. 

O tutorial mostra as etapas para usar o registro e também para usar o registro CNAME.

>[AZURE.IMPORTANT] Não crie um registro CNAME para o seu domínio raiz (por exemplo, "registro raiz"). Para obter mais informações, consulte [por que um registro CNAME não pode ser usado no domínio raiz](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain).
Para mapear um domínio raiz para seu aplicativo do Azure, use um registro.

<a name="vip"></a>
## <a name="step-1-a-record-only-get-apps-ip-address"></a>Etapa 1. *(Somente um registro)* Obter o endereço IP do aplicativo
Para mapear um nome de domínio personalizado usando um registro, você precisa de endereço IP do seu aplicativo Azure. Se você irá mapear usando um registro CNAME em vez disso, pule esta etapa e mover para a próxima seção.

1.  Faça logon no [portal do Azure](https://portal.azure.com).

2.  Clique em **Serviços de aplicativo** no menu à esquerda.

4.  Clique em seu aplicativo, clique em **domínios personalizados**.

6.  Tome nota do endereço IP acima seção de nomes de host..

    ![Nome de domínio personalizado do mapa com um registro: endereço IP Obtenha para seu aplicativo de serviço de aplicativo do Azure](./media/web-sites-custom-domain-name/virtual-ip-address.png)

7.  Este portal blade mantenha aberta. Você irá voltar a ele depois de criar os registros DNS.

<a name="createdns"></a>
## <a name="step-2-create-the-dns-records"></a>Etapa 2. Criar os registros DNS

Faça logon no seu registrador de domínio e usar a ferramenta para adicionar um registro a ou registro CNAME. Interface de usuário do cada registrador é um pouco diferente, portanto você deve consultar a documentação do provedor. No entanto, aqui estão algumas diretrizes gerais.

1.  Localize a página de gerenciamento de registros de DNS. Procure links ou áreas do site rotulada **Nome de domínio**, **DNS**ou **Gerenciamento de servidor de nome**. Muitas vezes, você pode encontrar o link exibindo informações da sua conta e, em seguida, procurando um link como **Meus domínios**.
2.  Procure por um link que permite adicionar ou editar registros DNS. Isso pode ser um **arquivo de zona** ou **Registros DNS** link ou um link de configuração **avançada** .
3.  Crie o registro e salve suas alterações.
    - [Instruções para um registro estão aqui](#a).
    - [Instruções para um registro CNAME estão aqui](#cname).

<a name="a"></a>
### <a name="create-an-a-record"></a>Criar um registro

Para usar um registro para mapear para o endereço IP do seu aplicativo Azure, você realmente precisa criar um registro e um registro TXT. O registro é para a resolução DNS em si, estando o registro TXT do Azure para confirmar que você é proprietário do nome de domínio personalizado. 

Configurar o registro da seguinte maneira (@ geralmente representa o domínio raiz):
 
<table cellspacing="0" border="1">
  <tr>
    <th>Exemplo FQDN</th>
    <th>Um Host</th>
    <th>Um valor</th>
  </tr>
  <tr>
    <td>Contoso.com (raiz)</td>
    <td>@</td>
    <td>Endereço IP do <a href="#vip">etapa 1</a></td>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>www</td>
    <td>Endereço IP do <a href="#vip">etapa 1</a></td>
  </tr>
  <tr>
    <td>*. contoso.com (curinga)</td>
    <td>*</td>
    <td>Endereço IP do <a href="#vip">etapa 1</a></td>
  </tr>
</table>

Seu registro TXT adicional assume a convenção de mapas do &lt; *subdomínio*>. &lt; *rootdomain*> para &lt; *appname*>. azurewebsites.net. Configure seu registro TXT da seguinte maneira:

<table cellspacing="0" border="1">
  <tr>
    <th>Exemplo FQDN</th>
    <th>TXT Host</th>
    <th>Valor TXT</th>
  </tr>
  <tr>
    <td>Contoso.com (raiz)</td>
    <td>@</td>
    <td>&lt;<i>appname</i>>. azurewebsites.net</td>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>www</td>
    <td>&lt;<i>appname</i>>. azurewebsites.net</td>
  </tr>
  <tr>
    <td>*. contoso.com (curinga)</td>
    <td>*</td>
    <td>&lt;<i>appname</i>>. azurewebsites.net</td>
  </tr>
</table>

<a name="cname"></a>
###Criar um registro CNAME

Se você usar um registro CNAME para mapear para o nome de domínio padrão do seu aplicativo Azure, não é necessário um registro TXT adicional como faz com um registro. 

>[AZURE.IMPORTANT] Não crie um registro CNAME para o seu domínio raiz (por exemplo, "registro raiz"). Para obter mais informações, consulte [por que um registro CNAME não pode ser usado no domínio raiz](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain).
Para mapear um domínio raiz para seu aplicativo do Azure, use um [registro](#a) .

Configurar o registro CNAME assim (@ geralmente representa o domínio raiz):

<table cellspacing="0" border="1">
  <tr>
    <th>Exemplo FQDN</th>
    <th>Host CNAME</th>
    <th>Valor CNAME</th>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>www</td>
    <td>&lt;<i>appname</i>>. azurewebsites.net</td>
  </tr>
  <tr>
    <td>*. contoso.com (curinga)</td>
    <td>*</td>
    <td>&lt;<i>appname</i>>. azurewebsites.net</td>
  </tr>
</table>

<a name="enable"></a>
##Etapa 3. Ativar o nome de domínio personalizado para o aplicativo

Novamente na lâmina **Domínios personalizados** no portal do Azure (consulte a [etapa 1](#vip)), você precisa adicionar o nome de domínio totalmente qualificado (FQDN) do seu domínio personalizado à lista.

1.  Se você ainda não tiver feito isso, log no [portal do Azure](https://portal.azure.com).

2.  No portal do Azure, clique em **Serviços de aplicativo** no menu à esquerda.

3.  Clique em seu aplicativo, clique em **domínios personalizados** > **Adicionar hostname**.

4.  Adicione o FQDN do seu domínio personalizado à lista (por exemplo, **www.contoso.com**).

    ![Mapear um nome de domínio personalizado para um aplicativo do Azure: adicionar à lista de nomes de domínio](./media/web-sites-custom-domain-name/add-custom-domain.png)

    >[AZURE.NOTE] Azure tentará verificar o nome do domínio que você usar aqui. Certifique-se de que é o mesmo nome de domínio para o qual você criou um registro DNS na [etapa 2](#createdns). 

5.  Clique em **Validar**.

6.  Após clicar em **Validar** Azure para Iniciar fluxo de trabalho de verificação de domínio. Isto irá verificar a propriedade do domínio, bem como sucesso de disponibilidade e relatório do nome do host ou erro detalhadas com dirigidas diretriz sobre como corrigir o erro.    

7.  Durante a validação bem-sucedida **hostname adicionar** botão se tornará ativo e você poderá ao nome do host atribuir. 

8.  Quando Azure termina configurando seu novo nome de domínio personalizado, navegue até seu nome de domínio personalizado em um navegador. O navegador deve abrir seu aplicativo Azure, o que significa que o seu nome de domínio personalizado está configurado corretamente.

> [AZURE.NOTE] Se o registro DNS já estiver em uso (cenário de tráfego de serviços de domínio do active) e você precisa vincular preventivamente seu aplicativo web-lo para verificação de domínio, em seguida, simplesmente crie registros TXT como exemplos mostrados na tabela a seguir. Seu registro TXT adicional assume a convenção de mapas do &lt; *subdomínio*>. &lt; *rootdomain*> para &lt; *appname*>. azurewebsites.net. 
> <table cellspacing="0" border="1">
  <tr>
    <th>Exemplo FQDN</th>
    <th>TXT Host</th>
    <th>Valor TXT</th>
  </tr>
  <tr>
    <td>Contoso.com (raiz)</td>
    <td>awverify.contoso.com</td>
    <td>&lt;<i>appname</i>>. azurewebsites.net</td>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>awverify.www.contoso.com</td>
    <td>&lt;<i>appname</i>>. azurewebsites.net</td>
  </tr>
    <tr>
    <td>*. contoso.com (sub)</td>
    <td>awverify.*.contoso.com</td>
    <td>&lt;<i>appname</i>>. azurewebsites.net</td>
  </tr>
</table>
Depois de criado este registro DNS, volte ao portal Azure e adicionar seu nome de domínio personalizado ao seu aplicativo web.
 

<a name="verify"></a>
##Verifique se a propagação de DNS

Após concluir as etapas de configuração, pode levar algum tempo para que as alterações para se propagar, dependendo do seu provedor DNS. Você pode verificar se a propagação de DNS está funcionando conforme esperado usando [http://digwebinterface.com/](http://digwebinterface.com/). Depois que você navegue até o site, especifique o nome de host na caixa de texto e clique em **Pesquisar**. Verificar os resultados para confirmar se as alterações recentes entraram em vigor.  

![Mapear um nome de domínio personalizado para um aplicativo do Azure: verificar propagação de DNS](./media/web-sites-custom-domain-name/1-digwebinterface.png)

> [AZURE.NOTE] A propagação das entradas DNS pode levar até 48 horas (às vezes, mais). Se você tiver configurado tudo corretamente, você ainda precisa esperar até que a propagação tenha êxito.

## <a name="next-steps"></a>Próximas etapas
Saiba como proteger seu nome de domínio personalizado com HTTPS [comprando um certificado SSL no Azure](web-sites-purchase-ssl-web-site.md) ou [usando um certificado SSL de qualquer outro lugar](web-sites-configure-ssl-certificate.md).

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

[Introdução ao Azure DNS](../dns/dns-getstarted-create-dnszone.md)  
[Criar registros de DNS para um aplicativo web em um domínio personalizado](../dns/dns-web-sites-custom-domain.md)  
[Domínio de representante ao Azure DNS](../dns/dns-domain-delegation.md)


<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png
