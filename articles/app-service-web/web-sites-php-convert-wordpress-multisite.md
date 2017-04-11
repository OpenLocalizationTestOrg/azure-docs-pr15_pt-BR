<properties 
    pageTitle="Converter WordPress multissite no serviço de aplicativo do Azure" 
    description="Saiba como tirar existente WordPress aplicativo web criado por meio da galeria no Azure e convertê-la em WordPress multissite" 
    services="app-service\web" 
    documentationCenter="php" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>



# <a name="convert-wordpress-to-multisite-in-azure-app-service"></a>Converter WordPress multissite no serviço de aplicativo do Azure

## <a name="overview"></a>Visão geral

*Por [Manuel Lobaugh][ben-lobaugh], [Microsoft abrir tecnologias Inc.][ms-open-tech]*

Neste tutorial, você aprenderá como tirar existente WordPress aplicativo web criado por meio da galeria no Azure e convertê-la em uma instalação de WordPress multissite. Além disso, você aprenderá a atribuir um domínio personalizado para cada de subsites dentro de sua instalação.

Será considerado que você tenha uma instalação existente do WordPress. Se você não fizer isso, siga as orientações fornecidas em [criar um site do WordPress na Galeria do Azure][website-from-gallery].

Convertendo um WordPress existente único site instalar para multissite geralmente é bastante simple e muitas das etapas iniciais aqui vêm direto a [Criar uma rede] [ wordpress-codex-create-a-network] página no [Código de WordPress](http://codex.wordpress.org).

Então vamos começar.

## <a name="allow-multisite"></a>Permitir multissite

Primeiro você precisa ativar multissite através do `wp-config.php` arquivo com o **WP\_permitir\_MULTISSITE** constante. Há dois métodos para editar os arquivos de aplicativo web: a primeira é por meio de FTP e o segundo até gito. Se você estiver familiarizado com como configurar um desses métodos, consulte os seguintes tutoriais:

* [Site PHP com o MySQL e FTP][website-w-mysql-and-ftp-ftp-setup]

* [Site PHP com o MySQL e gito][website-w-mysql-and-git-git-setup]

Abrir o `wp-config.php` arquivo com o editor de sua escolha e adicione o seguinte acima a `/* That's all, stop editing! Happy blogging. */` linha.

    /* Multisite */

    define( 'WP_ALLOW_MULTISITE', true );

Certifique-se de salvar o arquivo e carregue-o para o servidor!

## <a name="network-setup"></a>Configuração de rede

Faça logon para a área de *wp-administrador* da sua web app e você verá um novo item no menu **Ferramentas** , chamado de **Configuração de rede**. Clique em **Configuração de rede** e preencha os detalhes da sua rede.

![Tela de configuração de rede][wordpress-network-setup]

Neste tutorial usa o esquema de site de *subdiretórios* porque ele sempre deve funcionar, e podemos vão configurar domínios personalizados para cada subsite posteriormente no tutorial. No entanto, deve ser possível configurar um subdomínio instalar se você mapear um domínio por meio do curinga e configuração do [Portal Azure](https://portal.azure.com) DNS corretamente.

Para obter mais informações sobre subdomínio vs configurações subpasta ver os [tipos de rede multissite] [ wordpress-codex-types-of-networks] artigo sobre o código de WordPress.

## <a name="enable-the-network"></a>Ativar a rede

A rede está configurada no banco de dados, mas há uma etapa restante para habilitar a funcionalidade de rede. Finalizar a `wp-config.php` configurações e garantir `web.config` corretamente rotear cada site.


Depois de clicar no botão **instalar** na página *Configuração de rede* , WordPress tentará atualizar o `wp-config.php` e `web.config` arquivos. No entanto, você sempre deve verificar os arquivos para garantir que as atualizações foram bem-sucedidas. Caso contrário, esta tela apresentará as atualizações necessárias. Editar e salvar os arquivos.


Após fazer essas atualizações que você precisará fazer check-out e fazer logon novamente no painel de administração de wp.

Agora deve haver um menu adicional em administrador barra rotulada **Meus Sites**. Esse menu permite que você controle sua nova rede pelo painel de **Administrador de rede** .

## <a name="adding-custom-domains"></a>Adicionando domínios personalizados

O [Mapeamento de domínio do WordPress MU] [ wordpress-plugin-wordpress-mu-domain-mapping] plug-in facilita adicionar domínios personalizados para qualquer site na sua rede. Ordem do plug-in funcione corretamente, você precisa fazer algumas configurações adicionais no Portal e também em seu registrador de domínio.

## <a name="enable-domain-mapping-to-the-web-app"></a>Habilitar o mapeamento de domínio para o aplicativo web

O modo de plano de [Serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) de **livre** não suporta a adição de domínios personalizados para aplicativos Web. Você precisa alternar para o modo **padrão** ou **compartilhado** . Para fazer isso:

* Faça logon no Portal do Azure e localize o aplicativo web. 
* Clique na guia **crescerão** em **configurações**.
* Em **Geral**, selecione *compartilhado* ou *padrão*
* Clique em **Salvar**

Você pode receber uma mensagem pedindo para verificar a alteração e confirmar que seu aplicativo web agora pode geram um custo, dependendo do uso e a outra configuração que você definir.

Demora alguns segundos para processar as novas configurações, agora é um bom momento para começar a configurar seu domínio.

## <a name="verify-your-domain"></a>Verificar seu domínio

Antes do Azure Web Apps permitirá mapear um domínio para o site, primeiro é necessário verificar se você tem a autorização para mapear o domínio. Para fazer isso, você deve adicionar um novo registro CNAME à sua entrada DNS.

* Faça logon no Gerenciador de DNS do seu domínio
* Criar um novo CNAME *awverify*
* Aponte *awverify* para *awverify. YOUR_DOMAIN.azurewebsites.NET*

Pode levar alguns minutos para que as alterações DNS para entrar em vigor, portanto, se as seguintes etapas não funcionarem imediatamente, faça uma xícara de café, e em seguida, voltar e tente novamente.

## <a name="add-the-domain-to-the-web-app"></a>Adicionar o domínio para o aplicativo web

Retornar ao seu aplicativo web por meio do Portal do Azure, clique em **configurações**e, em seguida, clique em **domínios personalizados e SSL**.

Quando as *configurações de SSL* são exibidas, você verá os campos onde você digitará todos os domínios que você deseja atribuir a seu aplicativo web. Se um domínio não estiver listado aqui, ele não estará disponível para mapeamento dentro WordPress, independentemente de como o DNS do domínio for configuração.

![Gerenciar a caixa de diálogo de domínios personalizados][wordpress-manage-domains]

Depois de digitar seu domínio na caixa de texto, o Azure verificará o registro CNAME que você criou anteriormente. Se o DNS não tiver propagado totalmente, mostrará um indicador vermelho. Se tiver sido bem-sucedido, você verá uma marca de seleção verde. 

Anote o endereço IP listado na parte inferior da caixa de diálogo. Será necessário configurar o registro do seu domínio.

## <a name="setup-the-domain-a-record"></a>Configurar o domínio de um registro

Se as outras etapas forem bem-sucedidas, agora você pode atribuir o domínio para o seu aplicativo web Azure por meio de um registro DNS A. 

É importante observar aqui que aplicativos web Azure aceitam CNAME e registros a, porém, você *deve* usar um registro para habilitar o mapeamento de domínio correto. Um CNAME não poderão ser encaminhada outro CNAME, que é o que Azure criados por você com YOUR_DOMAIN.azurewebsites.net.

Usando o endereço IP da etapa anterior, retorne ao seu gerente de DNS e configurar o registro para apontar para que IP.


## <a name="install-and-setup-the-plugin"></a>Instalar e configurar o plug-in

WordPress multissite atualmente não tem um método interno para mapear domínios personalizados. No entanto, há um plug-in chamado [WordPress MU domínio mapeamento] [ wordpress-plugin-wordpress-mu-domain-mapping] que adiciona funcionalidade para você. Login à parte de seu site do administrador de rede e instalar o plug-in **WordPress MU domínio mapeamento** .

Depois de instalar e ativar o plug-in, visite **configurações** > **Mapeamento de domínio** para configurar o plug-in. Na caixa de texto primeiro, *Endereço IP do servidor*, insira o endereço de IP é usada para configurar o registro para o domínio. Defina quaisquer *Opções de domínio* que você desejar (os padrões geralmente são bem) e clique em **Salvar**.

## <a name="map-the-domain"></a>Mapa de domínio

Visite o **Dashboard** para o site que você deseja mapear o domínio. Clique em **Ferramentas** > **Mapeamento de domínio** e digite o novo domínio na caixa de texto e clique em **Adicionar**.

Por padrão, o novo domínio será regravado no domínio do site gerado automaticamente. Se você quiser ter todo o tráfego enviado para o novo domínio, marque a caixa de *domínio primário para este blog* antes de salvar. Você pode adicionar um número ilimitado de domínios para um site, mas somente um pode estar principal.

## <a name="do-it-again"></a>Fazê-lo novamente

Azure Web Apps permitem que você adicione um número ilimitado de domínios para um aplicativo web. Para adicionar outro domínio, você precisará executar as seções de **confirmar seu domínio** e **Configurar o domínio de um registro** para cada domínio.  

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

## <a name="whats-changed"></a>O que mudou
* Para um guia para a alteração de sites para o serviço de aplicativo consulte: [o serviço de aplicativo do Azure e seu impacto sobre serviços existentes do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png

 
