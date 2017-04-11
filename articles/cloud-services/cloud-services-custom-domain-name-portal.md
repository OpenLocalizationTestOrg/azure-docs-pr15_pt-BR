<properties
    pageTitle="Configurar um nome de domínio personalizado nos serviços de nuvem | Microsoft Azure"
    description="Saiba como expor seu aplicativo do Azure ou seus dados com a internet em um domínio personalizado com configurações de DNS.  Esses exemplos usam o portal do Azure."
    services="cloud-services"
    documentationCenter=".net"
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="adegeo"/>

# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Configurando um nome de domínio personalizado para um serviço de nuvem do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](cloud-services-custom-domain-name-portal.md)
- [Azure portal clássico](cloud-services-custom-domain-name.md)

Quando você cria um serviço na nuvem, Azure atribui a um subdomínio do **cloudapp.net**. Por exemplo, se seu serviço de nuvem é chamado "contoso", os usuários poderão acessar seu aplicativo em uma URL, como http://contoso.cloudapp.net. Azure também atribui um endereço IP virtual.

No entanto, você também pode expor seu aplicativo em seu próprio nome de domínio, como **contoso.com**. Este artigo explica como reservar ou definir um nome de domínio personalizado para funções do serviço de nuvem da web.

Fazer você já undestand quais registros CNAME e A são? [Salto passaram explicação](#add-a-cname-record-for-your-custom-domain).

> [AZURE.NOTE]
> Os procedimentos nesta tarefa se aplicam aos serviços de nuvem do Azure. Para serviços de aplicativo, consulte [Este](../app-service-web/web-sites-custom-domain-name.md). Para contas de armazenamento, consulte [Este](../storage/storage-custom-domain-name.md).

<p/>

> [AZURE.TIP]
> Familiarize-se com mais rapidez – use o Azure novo [passo a passo](http://support.microsoft.com/kb/2990804)!  Faz associar um nome de domínio personalizado e proteção da comunicação (SSL) com serviços de nuvem do Azure ou sites do Azure um piscar de olhos.

## <a name="understand-cname-and-a-records"></a>Compreender os registros CNAME e A

CNAME (ou registros de alias) e os dois registros permitem que você associar um nome de domínio um servidor específico (ou nesse caso, de serviço) no entanto eles funcionam de maneira diferente. Também existem algumas considerações específicas ao usar registros com serviços de nuvem do Azure que você deve considerar antes de decidir qual você deseja usar.

### <a name="cname-or-alias-record"></a>Registro CNAME ou Alias

Um registro CNAME mapeia um domínio *específico* , como **contoso.com** ou **www.contoso.com**, para um nome de domínio canônico. Nesse caso, o nome de domínio canônica é a **[myapp] .cloudapp .net** nome de domínio do seu Azure hospedado no aplicativo. Uma vez criado, o CNAME cria um alias para o **[myapp] .cloudapp .net**. A entrada CNAME resolverá para o endereço IP do seu **[myapp] .cloudapp .net** de serviço automaticamente, para que se altera o endereço IP do serviço de nuvem, você não terá qualquer ação.

> [AZURE.NOTE]
> Alguns registradores de domínio só permitem mapear subdomínios ao usar um registro CNAME, como www.contoso.com e não nomes de raiz, como contoso.com. Para obter mais informações sobre registros CNAME, consulte a documentação fornecida pelo seu registrador de domínios, [na entrada Wikipedia no registro CNAME](http://en.wikipedia.org/wiki/CNAME_record)ou o documento de [Nomes de domínio IETF - implementação e especificação](http://tools.ietf.org/html/rfc1035) .

### <a name="a-record"></a>Um registro

*Um registro* de um domínio, como **contoso.com** ou **www.contoso.com**, *ou um domínio curinga* mapas como ** \*. contoso.com**, para um endereço IP. No caso de um serviço de nuvem do Azure, o IP virtual do serviço. Portanto, o principal benefício de um registro ao longo de um registro CNAME é que você pode ter uma entrada que usa um curinga, como \* **. contoso.com**, que seria lidar com solicitações de vários subdomínios como **mail.contoso.com**, **login.contoso.com**ou **www.contso.com**.

> [AZURE.NOTE]
> Desde que um registro é mapeado para um endereço IP estático, ele não poderá resolver automaticamente as alterações para o endereço IP do seu serviço de nuvem. O endereço IP usado pelo seu serviço de nuvem é alocado na primeira vez que você implanta em um slot vazio (produção ou preparação.) Se você excluir a implantação do slot, o endereço IP é lançado pela Azure e qualquer implantações futuras ao slot podem receber um novo endereço IP.
>
> Convenientemente, o endereço IP de um slot de determinada implantação (produção ou preparação) é mantido quando trocar entre preparação e implantações de produção ou realizar uma atualização in-loco de uma implantação existente. Para obter mais informações sobre como executar essas ações, consulte [como gerenciar serviços de nuvem](cloud-services-how-to-manage.md).


## <a name="add-a-cname-record-for-your-custom-domain"></a>Adicionar um registro CNAME para seu domínio personalizado

Para criar um registro CNAME, adicione uma nova entrada na tabela de DNS do seu domínio personalizado usando as ferramentas fornecidas pelo seu registrador de domínios. Cada registrador tem um método semelhante, mas ligeiramente diferentes especificar um registro CNAME, mas os conceitos são os mesmos.

1. Use um destes métodos para localizar o **. cloudapp.net** nome de domínio atribuída ao seu serviço de nuvem.

    * Faça logon no [portal do Azure], selecione seu serviço de nuvem, examine a seção **Essentials** e localize a entrada de **URL do Site** .

        ![seção de rapidamente mostrando a URL do site][csurl]
            
        **OU**
  
    * Instalar e configurar [O Powershell do Azure](../powershell-install-configure.md)e, em seguida, use o seguinte comando:

        ```powershell
        Get-AzureDeployment -ServiceName yourservicename | Select Url
        ```
    
    Salve o nome de domínio usado na URL retornado por qualquer um dos métodos, pois você precisará-lo ao criar um registro CNAME.

1.  Faça logon no site do registrador DNS e vá para a página de gerenciamento de DNS. Procure links ou áreas do site rotulado como **Nome de domínio**, **DNS**ou **Gerenciamento de servidor de nome**.

2.  Agora, localize onde você pode selecionar ou inserir do CNAME. Você pode precisar selecionar o tipo de registro de uma queda ou ir para uma página de configurações avançadas. Você deve procurar palavras **CNAME**, **Alias**ou **subdomínios**.

3.  Você também deve fornecer o alias de domínio ou subdomínio para o CNAME, como **www** se desejar criar um alias para **www.customdomain.com**. Se você quiser criar um alias para o domínio raiz, ele pode estar listado como a '**@**' símbolo em Ferramentas DNS do seu registrador de domínios.

4. Em seguida, você deve fornecer um nome de host canônico, que é o domínio de **cloudapp.net** do seu aplicativo nesse caso.

Por exemplo, o seguinte registro CNAME encaminha todo o tráfego de **www.contoso.com** para **contoso.cloudapp.net**, o nome de domínio personalizado do seu aplicativo implantado:

| Nome de alias/Host/subdomínio | Domínio canônico     |
| ------------------------- | -------------------- |
| www                       | Contoso.cloudapp.NET |

> [AZURE.NOTE]
Um visitante de **www.contoso.com** nunca verá o host true (contoso.cloudapp.net), para que o processo de encaminhamento é invisível para o usuário final.

> O exemplo acima se aplica somente tráfego no subdomínio **www** . Desde que você não pode usar caracteres curinga com registros CNAME, você deve criar um CNAME para cada domínio/subdomínio. Se você deseja direcionar o tráfego de subdomínios, como *. contoso.com, para o endereço de cloudapp.net, você pode configurar um * *URL redirecionar* * ou * *Encaminhar URL** entrada em suas configurações de DNS, ou criar um registro.


## <a name="add-an-a-record-for-your-custom-domain"></a>Adicionar um registro a para seu domínio personalizado

Para criar um registro, você deve primeiro encontrar o endereço IP virtual do seu serviço de nuvem. Adicione uma nova entrada na tabela de DNS para seu domínio personalizado usando as ferramentas fornecidas pelo seu registrador de domínios. Cada registrador tem um método semelhante, mas ligeiramente diferentes especificar um registro, mas os conceitos são os mesmos.

1. Use um dos seguintes métodos para obter o endereço IP do seu serviço de nuvem.

    * Faça logon no [portal do Azure], selecione o seu serviço de nuvem, examine a seção **Essentials** e encontre a entrada de **endereços IP públicos** .

        ![seção de rapidamente mostrando o VIP][vip]

        **OU**

    * Instalar e configurar [O Powershell do Azure](../powershell-install-configure.md)e, em seguida, use o seguinte comando:

        ```powershell
        get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
        ```
    
    Salve o endereço IP, pois você precisará ao criar um registro.

1.  Faça logon no site do registrador DNS e vá para a página de gerenciamento de DNS. Procure links ou áreas do site rotulado como **Nome de domínio**, **DNS**ou **Gerenciamento de servidor de nome**.

2.  Agora, localize onde você pode selecionar ou inserir um registro. Você pode precisar selecionar o tipo de registro de uma queda ou ir para uma página de configurações avançadas.

3. Selecione ou insira o domínio ou o subdomínio que usará este registro. Por exemplo, selecione **www** se desejar criar um alias para **www.customdomain.com**. Se você quiser criar uma entrada de curinga para todos os subdomínios, digite '__*__'. Isso assumirá todos os subdomínios como **mail.customdomain.com**, **login.customdomain.com**e **www.customdomain.com**.

    Se você quiser criar um registro para o domínio raiz, ele pode estar listado como a '**@**' símbolo em Ferramentas DNS do seu registrador de domínios.

4. Insira o endereço IP do seu serviço de nuvem no campo fornecido. Isto associa a entrada de domínio usada no registro com o endereço IP da sua implantação do serviço de nuvem.

Por exemplo, a seguinte que um registro encaminha todo o tráfego de **contoso.com** para **137.135.70.239**, o endereço IP do seu aplicativo implantado:

| Nome de host/subdomínio | Endereço IP     |
| ------------------- | -------------- |
| @                   | 137.135.70.239 |


Este exemplo demonstra Criando um registro para o domínio raiz. Se você desejar criar uma entrada de curinga para cobrir todos os subdomínios, você digitaria '__*__' como o subdomínio.

>[AZURE.WARNING]
>Endereços IP no Azure são dinâmicos por padrão. Você provavelmente desejará usar um [endereço IP reservado](../virtual-network/virtual-networks-reserved-public-ip.md) para garantir que seu endereço IP não é alterado.

## <a name="next-steps"></a>Próximas etapas

* [Como gerenciar serviços de nuvem](cloud-services-how-to-manage.md)
* [Como mapear CDN conteúdo para um domínio personalizado](../cdn/cdn-map-content-to-custom-domain.md)
* [Configuração geral do seu serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Saiba como [implantar um serviço na nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configure [certificados ssl](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Portal do Azure]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png
 