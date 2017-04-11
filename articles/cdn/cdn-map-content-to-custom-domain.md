<properties
     pageTitle="Como mapear o conteúdo de rede (CDN) do Azure fornecimento de conteúdo para um domínio personalizado | Microsoft Azure"
     description="Este tópico demonstram como mapear um conteúdo CDN para um domínio personalizado."
     services="cdn"
     documentationCenter=""
     authors="camsoper"
     manager="erikre"
     editor=""/>
<tags
     ms.service="cdn"
     ms.workload="media"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="article"
    ms.date="07/28/2016"
     ms.author="casoper"/>

# <a name="how-to-map-custom-domain-to-content-delivery-network-cdn-endpoint"></a>Como mapear domínio personalizado para o ponto de extremidade de rede de entrega de conteúdo (CDN)
Você pode mapear um domínio personalizado para um ponto de extremidade CDN para poder usar seu próprio nome de domínio em URLs para conteúdo em cache em vez de usar um subdomínio do azureedge.net.

Há duas maneiras de mapear seu domínio personalizado para um ponto de extremidade CDN:

1. [Criar um registro CNAME com seu registrador de domínio e mapear seu domínio personalizado e o subdomínio para o ponto de extremidade CDN](#register-a-custom-domain-for-an-azure-cdn-endpoint)

    Um registro CNAME é um recurso DNS que mapeia um domínio de origem, como `www.contosocdn.com` ou `cdn.contoso.com`, para um domínio de destino. Nesse caso, o domínio de origem é seu domínio personalizado e o subdomínio (um subdomínio, como **www** ou **cdn** sempre é necessária). O domínio de destino é seu ponto de extremidade de CDN.  

    O processo de mapeamento de seu domínio personalizado para seu ponto de extremidade de CDN no entanto, pode resultar em um breve período de tempo de inatividade para o domínio enquanto você estiver registrando o domínio no Portal do Azure.

2. [Adicionar uma etapa intermediária registro com **cdnverify**](#register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain)

    Se seu domínio personalizado está atualmente oferece suporte a um aplicativo com um contrato de nível de serviço (SLA) que requer o sem tempo de inatividade, você pode usar o subdomínio Azure **cdnverify** para fornecer uma etapa intermediária de registro para que os usuários poderão acessar seu domínio durante o mapeamento acontece de DNS.  

Depois que você registrar seu domínio personalizado usando um dos procedimentos acima, deseja [Verificar se o subdomínio personalizado faz referência a seu ponto de extremidade de CDN](#verify-that-the-custom-subdomain-references-your-cdn-endpoint).

> [AZURE.NOTE] Você deve criar um registro CNAME com seu registrador de domínio para mapear seu domínio para o ponto de extremidade CDN. Registros CNAME mapeiam subdomínios específicos como `www.contoso.com` ou `cdn.contoso.com`. Não é possível mapear um registro CNAME para um domínio raiz, tais como `contoso.com`.
>    
> Um subdomínio só pode ser associado um ponto de extremidade CDN. O registro CNAME que você criar encaminhará todo o tráfego endereçado ao subdomínio para o ponto de extremidade especificado.  Por exemplo, se você associar `www.contoso.com` com seu ponto de extremidade CDN, em seguida, você não pode associá-lo a outros pontos de extremidade Azure, como um ponto de extremidade de conta de armazenamento ou um ponto de extremidade do serviço de nuvem. No entanto, você pode usar subdomínios diferentes do mesmo domínio para pontos de extremidade do serviço diferente. Você também pode mapear subdomínios diferentes para o mesmo ponto de extremidade CDN.
>
> Para pontos de extremidade do **Azure CDN da Verizon** (padrão e Premium), observe que ele ocupa a **90 minutos** para alterações de domínio personalizado para se propagarem para nós de borda CDN.

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint"></a>Registrar um domínio personalizado para um ponto de extremidade do Azure CDN

1.  Log no [Portal do Azure](https://portal.azure.com/).
2.  Clique em **Procurar**, então **CDN perfis**, em seguida, o perfil CDN com o ponto de extremidade que você deseja mapear para um domínio personalizado.  
3.  Na lâmina **CDN perfil** , clique na extremidade CDN com o qual você deseja associar o subdomínio.
4.  Na parte superior da lâmina ponto de extremidade, clique no botão **Adicionar domínio personalizado** .  A lâmina de **Adicionar um domínio personalizado** , você verá o nome de host do ponto de extremidade, derivado de seu ponto de extremidade CDN, usar para criar um novo registro CNAME. O formato do endereço de nome de host aparecerá como ** &lt;EndpointName >. azureedge.net**.  Você pode copiar esse nome de host usar para criar o registro CNAME.  
5.  Navegue até o site da web do seu registrador de domínio e localize a seção sobre como criar registros DNS. Você pode encontrar isso em uma seção como o **Nome de domínio**, **DNS**ou **Gerenciamento de servidor de nome**.
6.  Localize a seção de gerenciamento CNAMEs. Você pode precisar ir para uma página de configurações avançadas e procure pelas palavras CNAME, Alias ou subdomínios.
7.  Crie um novo registro CNAME que mapeia um subdomínio escolhido (por exemplo, " **www** " ou " **cdn**") para o nome de host fornecido na lâmina **Adicionar um domínio personalizado** .
8.  Retornar para a lâmina de **Adicionar um domínio personalizado** e insira seu domínio personalizado, incluindo o subdomínio, na caixa de diálogo. Por exemplo, insira o nome de domínio no formato `www.contoso.com` ou `cdn.contoso.com`.   

    Azure verificará a existência de um registro CNAME para o nome de domínio que você inseriu. Se o CNAME estiver correto, seu domínio personalizado é validado.  Para pontos de extremidade do **Azure CDN da Verizon** (padrão e Premium), ele pode levar até 90 minutos para as configurações de domínio personalizado para se propagarem para todos os nós de borda CDN, no entanto.  

    Observe que em alguns casos pode levar tempo para o registro CNAME para se propagarem para os servidores de nomes na Internet. Se seu domínio não é validado imediatamente e você acha que o registro CNAME está correto, aguarde alguns minutos e tente novamente.


## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain"></a>Registrar um domínio personalizado para um ponto de extremidade de CDN Azure usando o subdomínio de cdnverify intermediário  

1. Log no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Procurar**, então **CDN perfis**, em seguida, o perfil CDN com o ponto de extremidade que você deseja mapear para um domínio personalizado.  
3. Na lâmina **CDN perfil** , clique na extremidade CDN com o qual você deseja associar o subdomínio.
4. Na parte superior da lâmina ponto de extremidade, clique no botão **Adicionar domínio personalizado** .  A lâmina de **Adicionar um domínio personalizado** , você verá o nome de host do ponto de extremidade, derivado de seu ponto de extremidade CDN, usar para criar um novo registro CNAME. O formato do endereço de nome de host aparecerá como ** &lt;EndpointName >. azureedge.net**.  Você pode copiar esse nome de host usar para criar o registro CNAME.
5. Navegue até o site da web do seu registrador de domínio e localize a seção sobre como criar registros DNS. Você pode encontrar isso em uma seção como o **Nome de domínio**, **DNS**ou **Gerenciamento de servidor de nome**.
6. Localize a seção de gerenciamento CNAMEs. Você pode precisar ir para uma página de configurações avançadas e procure pelas palavras **CNAME**, **Alias**ou **subdomínios**.
7. Criar um novo registro CNAME e forneça um alias de subdomínio que inclui o subdomínio **cdnverify** . Por exemplo, o subdomínio que você especificar será no formato **cdnverify.www** ou **cdnverify.cdn**. Fornecer o nome do host, que é o ponto de extremidade CDN, no formato **cdnverify.&lt; EndpointName >. azureedge.net**.
8. Retornar para a lâmina de **Adicionar um domínio personalizado** e insira seu domínio personalizado, incluindo o subdomínio, na caixa de diálogo. Por exemplo, insira o nome de domínio no formato `www.contoso.com` ou `cdn.contoso.com`. Observe que nesta etapa, você não precisa preceda o subdomínio com **cdnverify**.  

    Azure verificará a existência de um registro CNAME para o nome de domínio de cdnverify que você inseriu.
9. Neste ponto, seu domínio personalizado tenha sido verificado por Azure, mas o tráfego para o seu domínio ainda não está sendo roteado para seu ponto de extremidade de CDN. Depois que espera longa o suficiente para permitir que as configurações de domínio personalizado para se propagarem para os nós de borda CDN (90 minutos para **Azure CDN da Verizon**, 1-2 minutos para **Azure CDN do Akamai**), retornar ao site do registrador DNS e criar outro registro CNAME que mapeia um subdomínio para o ponto de extremidade de CDN. Por exemplo, especificar o subdomínio como **www** ou **cdn**e o nome de host como ** &lt;EndpointName >. azureedge.net**. Esta etapa, o registro de seu domínio personalizado está concluído.
10. Por fim, você pode excluir o registro CNAME criado usando **cdnverify**, como ela estava necessária apenas como uma etapa intermediária.  


## <a name="verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>Verifique se o subdomínio personalizado faz referência a seu ponto de extremidade de CDN

- Depois de concluir o registro do seu domínio personalizado, você pode acessar o conteúdo que é armazenados em cache seu ponto de extremidade de CDN usando o domínio personalizado.
Primeiro, certifique-se de que você tenha conteúdo público armazenados em cache no ponto de extremidade. Por exemplo, se seu ponto de extremidade de CDN estiver associado uma conta de armazenamento, a CDN armazena conteúdo em contêineres de blob pública. Para testar o domínio personalizado, certifique-se de que seu contêiner estiver configurado para permitir acesso público e contém pelo menos um blob.
- No seu navegador, navegue até o endereço do blob usando o domínio personalizado. Por exemplo, se seu domínio personalizado é `cdn.contoso.com`, a URL para um blob em cache será semelhante a seguinte URL: http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg

## <a name="see-also"></a>Consulte também

[Como habilitar a rede de distribuição de conteúdo (CDN) do Azure](./cdn-create-new-endpoint.md)  
