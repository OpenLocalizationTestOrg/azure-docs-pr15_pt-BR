<properties
    pageTitle="Configurar um nome de domínio para seu ponto de extremidade de armazenamento de Blob | Microsoft Azure"
    description="Saiba como mapear um domínio de usuário personalizadas para o ponto de extremidade do armazenamento de Blob para uma conta de armazenamento do Azure no Portal de clássico do Azure."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Configurar um nome de domínio personalizado para seu ponto de extremidade de armazenamento de Blob

## <a name="overview"></a>Visão geral

Você pode configurar um domínio personalizado para acessar os dados de blob em sua conta de armazenamento do Azure. O ponto de extremidade padrão para o armazenamento de Blob é `<storage-account-name>.blob.core.windows.net`. Se você mapear um domínio personalizado e o subdomínio como **www.contoso.com** para o ponto de extremidade do blob para sua conta de armazenamento, em seguida, seu os usuários podem também dados de blob do access em sua conta de armazenamento usando esse domínio.

>[AZURE.IMPORTANT] Armazenamento do Azure ainda não suporta HTTPS com domínios personalizados. Estamos Saiba que os clientes estiver interessados nesse recurso, ele estará disponível em uma versão futura.

Há duas maneiras para apontar seu domínio personalizado para o ponto de extremidade do blob para sua conta de armazenamento. A maneira mais simples é criar um registro CNAME mapeamento de seu domínio personalizado e o subdomínio para o ponto de extremidade do blob. Um registro CNAME é um recurso DNS que mapeia um domínio de origem para um domínio de destino. Nesse caso, o domínio de origem é seu personalizado domínio e subdomínio, observe que o subdomínio sempre é necessário. O domínio de destino é seu ponto de extremidade do serviço de Blob.

O processo de mapeamento de seu domínio personalizado para seu ponto de extremidade do blob no entanto, pode resultar em um breve período de tempo de inatividade para o domínio enquanto você estiver registrando o domínio no [Portal de clássico do Azure](https://manage.windowsazure.com). Se seu domínio personalizado está atualmente oferece suporte a um aplicativo com um contrato de nível de serviço (SLA) que requer o sem tempo de inatividade, você pode usar o subdomínio Azure **asverify** para fornecer uma etapa intermediária de registro para que os usuários poderão acessar seu domínio durante o mapeamento acontece de DNS.

A tabela a seguir mostra as URLs de amostra para acessar dados de blob em uma conta de armazenamento denominada **mystorageaccount**. O domínio personalizado registrado para a conta de armazenamento está **www.contoso.com**:

Tipo de recurso|Formatos de URL
---|---
Conta de armazenamento|**URL padrão:** http://mystorageaccount.blob.core.windows.net<p>**URL de domínio personalizado:** http://www.contoso.com</td>
Blob|**URL padrão:** http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<p>**URL de domínio personalizado:** http://www.contoso.com/mycontainer/myblob
Contêiner raiz|**URL padrão:** http://mystorageaccount.blob.core.windows.net/myblob ou http://mystorageaccount.blob.core.windows.net/$ raiz/myblob<p>**URL de domínio personalizado:** http://www.contoso.com/myblob ou http://www.contoso.com/$ raiz/myblob

## <a name="register-a-custom-domain-for-your-storage-account"></a>Registrar um domínio personalizado para sua conta de armazenamento

Use este procedimento para registrar seu domínio personalizado se você não tem preocupações tendo o domínio a ser temporariamente indisponível aos usuários, ou se o seu domínio personalizado atualmente não hospeda um aplicativo.

Se seu domínio personalizado no momento é oferecer suporte a um aplicativo que não pode ter qualquer tempo de inatividade, use o procedimento descrito em <a href="#register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain">registrar um domínio personalizado para sua conta de armazenamento usando o subdomínio de asverify intermediário</a>.

Para configurar um nome de domínio personalizado, você deve criar um novo registro CNAME com seu registrador de domínio. O registro CNAME Especifica um alias para um nome de domínio; Nesse caso ele mapeia o endereço do seu domínio personalizado para o ponto de extremidade do armazenamento de Blob para sua conta de armazenamento.

Cada registrador tem um método semelhante, mas ligeiramente diferentes especificar um registro CNAME, mas o conceito é o mesmo. Observe que muitos pacotes de registro de domínio básico não oferecem a configuração de DNS, para que você talvez precise atualizar seu pacote de registro de domínio antes de criar o registro CNAME.

1.  No [Portal de clássico do Azure](https://manage.windowsazure.com), navegue até a guia de **armazenamento** .

2.  Na guia **armazenamento** , clique no nome da conta de armazenamento para o qual você deseja mapear o domínio personalizado.

3.  Clique na guia **Configurar** .

4.  Na parte inferior da tela, clique em **Gerenciar domínio** para exibir a caixa de diálogo **Gerenciar domínio personalizado** . O texto na parte superior da caixa de diálogo, você verá informações sobre como criar o registro CNAME. Neste procedimento, ignore o texto que se refere ao subdomínio do **asverify** .

5.  Faça logon no site do registrador DNS e vá para a página de gerenciamento de DNS. Você pode encontrar isso em uma seção como o **Nome de domínio**, **DNS**ou **Gerenciamento de servidor de nome**.

6.  Localize a seção de gerenciamento CNAMEs. Você pode precisar ir para uma página de configurações avançadas e procure pelas palavras **CNAME**, **Alias**ou **subdomínios**.

7.  Criar um novo registro CNAME e forneça um alias de subdomínio, como **www** ou **fotos**. Forneça um nome de host, que é a sua empresa de serviço de Blob, no formato **mystorageaccount.blob.core.windows.net** (onde **mystorageaccount** é o nome da sua conta de armazenamento). O nome de host para usar é fornecido para você no texto da caixa de diálogo **Gerenciar domínio personalizado** .

8.  Após ter criado o registro CNAME, retorne à caixa de diálogo **Gerenciar domínio personalizado** e insira o nome do seu domínio personalizado, incluindo o subdomínio, no campo **Nome de domínio personalizado** . Por exemplo, se seu domínio está **contoso.com** e seu subdomínio é **www**, insira **www.contoso.com**; Se um subdomínio estiver **fotos**, digite **photos.contoso.com**. Observe que o subdomínio é necessário.

9. Clique no botão **registrar** para registrar seu domínio personalizado.

    Se o registro for bem-sucedido, você verá a mensagem de **seu domínio personalizado está ativo**. Os usuários podem agora exibir blob dados em seu domínio personalizado, desde que eles têm as permissões apropriadas.

## <a name="register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain"></a>Registrar um domínio personalizado para sua conta de armazenamento usando o subdomínio de asverify intermediário

Use este procedimento para registrar sua personalizado domínio se seu domínio personalizado têm suporte no momento um aplicativo com um SLA que requer que haja sem tempo de inatividade. Criando um CNAME que aponta de asverify. &lt;subdomínio&gt;. &lt;customdomain&gt; para asverify. &lt;storageaccount&gt;. blob.core.windows.net, você pode previamente registrar seu domínio com o Azure. Você pode criar uma segunda CNAME que aponta de &lt;subdomínio&gt;. &lt;customdomain&gt; para &lt;storageaccount&gt;. blob.core.windows.net, ponto em que o tráfego para seu domínio personalizado será direcionado para seu ponto de extremidade do blob.

O subdomínio asverify é um subdomínio especial reconhecido pelo Azure. Anexando **asverify** ao seu próprio subdomínio, você permitir Azure reconhecer seu domínio personalizado sem modificar o registro DNS para o domínio. Depois que você modificar o registro DNS do domínio, ele será mapeado para o ponto de extremidade do blob sem tempo de inatividade.

1.  No [Portal de clássico do Azure](https://manage.windowsazure.com), navegue até a guia de **armazenamento** .

2.  Na guia **armazenamento** , clique no nome da conta de armazenamento para o qual você deseja mapear o domínio personalizado.

3.  Clique na guia **Configurar** .

4.  Na parte inferior da tela, clique em **Gerenciar domínio** para exibir a caixa de diálogo **Gerenciar domínio personalizado** . O texto na parte superior da caixa de diálogo, você verá informações sobre como criar o registro CNAME usando o subdomínio de **asverify** .

5.  Faça logon no site do registrador DNS e vá para a página de gerenciamento de DNS. Você pode encontrar isso em uma seção como o **Nome de domínio**, **DNS**ou **Gerenciamento de servidor de nome**.

6.  Localize a seção de gerenciamento CNAMEs. Você pode precisar ir para uma página de configurações avançadas e procure pelas palavras **CNAME**, **Alias**ou **subdomínios**.

7.  Criar um novo registro CNAME e forneça um alias de subdomínio que inclui o subdomínio asverify. Por exemplo, o subdomínio que você especificar será no formato **asverify.www** ou **asverify.photos**. Forneça um nome de host, que é a sua empresa de serviço de Blob, no formato **asverify.mystorageaccount.blob.core.windows.net** (onde **mystorageaccount** é o nome da sua conta de armazenamento). O nome de host para usar é fornecido para você no texto da caixa de diálogo **Gerenciar domínio personalizado** .

8.  Após ter criado o registro CNAME, retorne à caixa de diálogo **Gerenciar domínio personalizado** e digite o nome do seu domínio personalizado no campo **Nome de domínio personalizado** . Por exemplo, se seu domínio está **contoso.com** e seu subdomínio é **www**, insira **www.contoso.com**; Se um subdomínio estiver **fotos**, digite **photos.contoso.com**. Observe que o subdomínio é necessário.

9.  Clique na caixa de seleção que diz **Avançado: Use o subdomínio 'asverify' para preregister meu domínio personalizado**.

10. Clique no botão **registrar** para preregister seu domínio personalizado.

    Se o pré-registro for bem-sucedido, você verá a mensagem de **seu domínio personalizado está ativo**.

11. Neste ponto, seu domínio personalizado tenha sido verificado por Azure, mas o tráfego para o seu domínio ainda não está sendo roteado para sua conta de armazenamento. Para concluir o processo, retorne ao site do seu registrador de DNS e criar outro registro CNAME que mapeia um subdomínio para o ponto de extremidade do serviço de Blob. Por exemplo, especifique o subdomínio como **www** ou **fotos**e o nome de host como **mystorageaccount.blob.core.windows.net** (onde **mystorageaccount** é o nome da sua conta de armazenamento). Esta etapa, o registro de seu domínio personalizado está concluído.

12. Por fim, você pode excluir o registro CNAME criado usando **asverify**, como ela estava necessária apenas como uma etapa intermediária.

Os usuários podem agora exibir blob dados em seu domínio personalizado, desde que eles têm as permissões apropriadas.

## <a name="verify-that-the-custom-domain-references-your-blob-service-endpoint"></a>Verifique se o domínio personalizado faz referência a seu ponto de extremidade do serviço de Blob

Para verificar que o seu domínio personalizado realmente é mapeado para o ponto de extremidade do serviço de Blob, crie um blob em um contêiner de público dentro de sua conta de armazenamento. Em seguida, em um navegador da web, use um URI no seguinte formato para acessar o blob:

-   http://<;*subdomain.customdomain*>/<*mycontainer*>/<*myblob*>

Por exemplo, você pode usar o seguinte URI para acessar um formulário da web por meio de um subdomínio personalizado **photos.contoso.com** que mapeia para um blob em seu contêiner de **myforms** :

-   http://Photos.contoso.com/myforms/ApplicationForm.htm

## <a name="unregister-a-custom-domain-from-your-storage-account"></a>Cancelar o registro de um domínio personalizado de sua conta de armazenamento

Para cancelar o registro de um domínio personalizado, siga estas etapas: 

1. Entre [Portal de clássico Azure](https://manage.windowsazure.com). 

2. No painel de navegação, clique em **armazenamento**. 

3. Na página de **armazenamento** , clique no nome da conta de armazenamento para exibir o painel de controle. 

5. Na faixa de opções, clique em **Gerenciar domínio**. 

6. Na caixa de diálogo **Gerenciar domínio personalizado** , clique em **Cancelar registro**. 


## <a name="additional-resources"></a>Recursos adicionais

-   [Como mapear domínio personalizado para o ponto de extremidade de rede de entrega de conteúdo (CDN)](../cdn/cdn-map-content-to-custom-domain.md)
