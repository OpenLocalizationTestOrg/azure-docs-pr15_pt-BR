<properties
    pageTitle="WordPress corporativo no serviço de aplicativo do Azure | Microsoft Azure"
    description="Saiba como hospedar um site de WordPress corporativo no serviço de aplicativo do Azure"
    services="app-service\web"
    documentationCenter=""
    authors="sunbuild"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.devlang="php"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="web"
    ms.date="10/24/2016"
    ms.author="sumuth"/>

# <a name="enterprise-class-wordpress-on-azure-app-service"></a>WordPress corporativo no serviço de aplicativo do Azure

Serviço de aplicativo do Azure fornece um ambiente escalável, seguro e fácil de usar para missão crítica grande escala [WordPress] [ wordpress] sites. Sites corporativos como o [Office] é executado própria Microsoft[ officeblog] e [Bing] [ bingblog] blogs. Este documento mostra como você pode usar o Azure aplicativo de serviço Web Apps para estabelecer e manter uma corporativos, o site de WordPress baseado em nuvem que pode lidar com um grande volume de visitantes.

## <a name="architecture-and-planning"></a>Planejamento e arquitetura

Uma instalação de WordPress básica tem apenas dois requisitos.

* **Banco de dados MySQL** - disponível por meio de [ClearDB no mercado Azure][cdbnstore], ou você pode gerenciar sua própria instalação MySQL em máquinas virtuais do Azure usando o [Windows] [ mysqlwindows] ou [Linux][mysqllinux].

  > [AZURE.NOTE] ClearDB fornece várias configurações de MySQL, com características diferentes de desempenho para cada configuração. Consulte o [Armazenamento do Azure] [ cdbnstore] para obter informações sobre ofertas fornecido por meio da loja Azure ou diretamente conforme visto no [site ClearDB](http://www.cleardb.com/pricing.view).

* **PHP 5.2.4 ou maior** -no momento, o serviço de aplicativo do Azure oferece [versões PHP 5.4, 5.5 e 5.6][phpwebsite].

    > [AZURE.NOTE] Recomendamos sempre a execução na versão mais recente do PHP para garantir que você tenha as correções de segurança mais recentes.

### <a name="basic-deployment"></a>Implantação básica

Usando apenas os requisitos básicos, você pode criar uma solução básica dentro de uma região Azure.

![um aplicativo do Azure web e o banco de dados MySQL hospedados em uma única região Azure][basic-diagram]

Enquanto isso permitirá que você a escala de seu aplicativo criando várias instâncias de aplicativos Web do site, tudo está hospedado dentro dos data centers em uma região geográfica específica. Visitantes do fora essa região poderá ver tempos de resposta lento ao usar o site, e se os centros de dados nesta região descem, portanto não faz seu aplicativo.


### <a name="multi-region-deployment"></a>Implantação de várias regiões

Usando o [Gerenciador de tráfego]do Azure[trafficmanager], é possível dimensionar seu site WordPress entre várias regiões geográficas enquanto fornecer somente uma URL para os visitantes. Todos os visitantes ocorrer em através do Gerenciador de tráfego e, em seguida, são roteados para uma região baseada na configuração de balanceamento de carga.

![aplicativo web Azure, hospedado em várias regiões, usando o roteador CDBR alta disponibilidade para rotear para MySQL em regiões][multi-region-diagram]

Dentro de cada região, site WordPress ainda poderia ser dimensionado em várias instâncias de aplicativos Web, mas esse dimensionamento é região específica; regiões de alto tráfego podem ser dimensionados diferente pouco tráfego aquelas.

Replicação e roteamento para vários bancos de dados MySQL podem ser feitas usando do ClearDB [CDBR alta disponibilidade roteador] [ cleardbscale] (mostrada à esquerda) ou [MySQL Cluster CGE][cge].

### <a name="multi-region-deployment-with-media-storage-and-caching"></a>Implantação de várias regiões com o armazenamento de mídia e armazenamento em cache
Se o site aceita carregamentos ou arquivos de mídia do host, use o armazenamento de Blob do Azure. Se precisar de cache, considere a possibilidade de [relacionada cache][rediscache], [Nuvem Memcache](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/)ou uma das outras ofertas cache da [Loja do Azure](http://azure.microsoft.com/gallery/store/).

![aplicativo web Azure, hospedado em várias regiões, usando o roteador CDBR alta disponibilidade para MySQL, com Cache gerenciado, armazenamento de Blob e CDN][performance-diagram]

Armazenamento de blob é geográfica-distribuídas regiões por padrão, portanto, você não precisa se preocupar com replicação de arquivos em todos os sites. Você também pode habilitar a [Rede de distribuição de conteúdo (CDN)] do Azure[ cdn] para armazenamento de Blob, que distribui arquivos para encerrar nós mais próximo para os visitantes.

### <a name="planning"></a>Planejamento

#### <a name="additional-requirements"></a>Requisitos adicionais

Para fazer isso... | Use isto...
------------------------|-----------
**Carregar ou armazenar arquivos grandes** | [Plug-in WordPress para usar o armazenamento de Blob][storageplugin]
**Enviar email** | [SendGrid] [ storesendgrid] e o [plug-in WordPress usando SendGrid][sendgridplugin]
**Nomes de domínio personalizado** | [Configurar um nome de domínio personalizado no serviço de aplicativo do Azure][customdomain]
**HTTPS** | [Habilitar HTTPS para um aplicativo web no serviço de aplicativo do Azure][httpscustomdomain]
**Validação de pré-produção** | [Configurar ambientes web Apps em um serviço de aplicativo do Azure temporários][staging] <p>Migrando um aplicativo web de estágios de produção também move a configuração de WordPress. Você deve garantir que todas as configurações são atualizadas para os requisitos de seu aplicativo de produção antes de trocar o aplicativo em estágios em produção.</p>
**Monitoramento e solução de problemas** | [Habilitar o log de diagnóstico para web apps em um serviço de aplicativo do Azure] [ log] e [Monitor Web Apps em um serviço de aplicativo do Azure][monitor]
**Implantar o seu site** | [Implantar um aplicativo web no serviço de aplicativo do Azure][deploy]

#### <a name="availability-and-disaster-recovery"></a>Disponibilidade e recuperação de desastres

Para fazer isso... | Use isto...
------------------------|-----------
**Sites de balanço de carga** ou **geográfica-distribuir sites** | [Rotear o tráfego com o Gerenciador de tráfego do Azure][trafficmanager]
**Fazer backup e restauração** | [Fazer backup de um aplicativo web no serviço de aplicativo do Azure] [ backup] e [restaurar um aplicativo web no serviço de aplicativo do Azure][restore]

#### <a name="performance"></a>Desempenho

Desempenho na nuvem é alcançado principalmente por meio de cache e escala-out; No entanto a memória, largura de banda e outros atributos de aplicativos Web de hospedagem devem ser considerados.

Para fazer isso... | Use isto...
------------------------|-----------
**Entender os recursos de instância do serviço de aplicativo** | [Detalhes de preços, incluindo recursos de níveis de serviço de aplicativo][websitepricing]
**Recursos de cache** | [Relacionada cache][rediscache], [Nuvem Memcache](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/)ou uma das outras ofertas cache da [Loja do Azure](/gallery/store/)
**Dimensionar o aplicativo** | [Dimensionar um aplicativo web no serviço de aplicativo do Azure] [ websitescale] [ClearDB alta disponibilidade roteamento]e[cleardbscale]. Se você escolher hospedar e gerenciar sua própria instalação MySQL, você deve considerar [MySQL Cluster CGE] [ cge] para fora de escala

#### <a name="migration"></a>Migração

Há dois métodos de migrar um site WordPress existente para o serviço de aplicativo do Azure.

* ** [Exportar WordPress] [ export] ** -Isso exporta o conteúdo do seu blog, que então pode ser importado para um novo site de WordPress no serviço de aplicativo do Azure usando o [plug-in do WordPress importador][import].

    > [AZURE.NOTE] Enquanto esse processo permite que você migrar o conteúdo, ele não migrar qualquer plug-ins, temas ou outras personalizações. Esses devem ser instalados novamente manualmente.

* **Migração manual** - [fazer backup de seu site] [ wordpressbackup] [banco de dados]e[wordpressdbbackup], em seguida, manualmente restaurá-lo para um aplicativo web no serviço de aplicativo do Azure e associados banco de dados MySQL para migrar sites altamente personalizados e evitar o tédio de instalar manualmente o plug-ins, temas e outras personalizações.

## <a name="step-by-step-instructions"></a>Instruções passo a passo

### <a name="create-a-wordpress-site"></a>Criar um site de WordPress

1. Use o [Azure Marketplace] [ cdbnstore] para criar um banco de dados MySQL do tamanho identificado na seção [planejamento e arquitetura](#planning) , nas regiões que você irá hospedar seu site.

2. Siga as etapas em [criar um aplicativo da web de WordPress no serviço de aplicativo do Azure] [ createwordpress] para criar um aplicativo da web de WordPress. Ao criar o aplicativo da web, selecione **usar um banco de dados MySQL existente** e selecione o banco de dados criado na etapa 1.

Se você estiver migrando um site existente do WordPress, consulte [migrar um site existente do WordPress no Azure](#Migrate-an-existing-WordPress-site-to-Azure) depois de criar um novo aplicativo web.

### <a name="migrate-an-existing-wordpress-site-to-azure"></a>Migrar um site WordPress existente para o Azure

Conforme mencionado na seção de [planejamento e arquitetura](#planning) , há duas maneiras de migrar um site WordPress.

* **Importar e exportar** - para sites sem a quantidade de personalizações, ou onde você quer apenas mover o conteúdo.

* **backup e restauração** para sites com muitas personalização onde você deseja mover tudo.

Use uma das seções a seguir para migrar seu site.

#### <a name="the-export-and-import-method"></a>O método de importação e exportação

1. Usar [Exportar WordPress] [ export] exportar seu site existente.

2. Crie um aplicativo web usando as etapas na seção [criar um site de WordPress](#Create-a-new-WordPress-site) .

3. Faça logon no seu site de WordPress nos aplicativos Web e clique em **Plug-ins** -> **Adicionar novo**. Procurar e instalar o plug-in **WordPress importador** .

4. Após a instalação do plug-in importador, clique em **Ferramentas de** -> de**importação**e selecione **WordPress** para usar o plug-in do WordPress importador.

5. Na página **Importar WordPress** , clique em **Escolher arquivo**. Navegue até o arquivo WXR exportado de seu site de WordPress existente e escolha **carregar arquivo e importar**.

6. Clique em **Enviar**. Você será solicitado que a importação foi bem-sucedido.

8. Quando tiver concluído todas essas etapas, reinicie o seu site de seu blade de aplicativo da web no [portal do Azure][mgmtportal].

Após importar o site, talvez você precise executar as seguintes etapas para habilitar configurações não contidas no arquivo de importação.

Se você estivesse usando isso … | Faça o seguinte …
------------------ | ----------
**Links permanentes** | No painel de WordPress do novo site, clique em **configurações** -> **links permanentes** e, em seguida, atualizar a estrutura de links permanentes
**links de imagem/mídia** | Para atualizar os links para o novo local, use o [plug-in veludo azuis atualizar URLs][velvet], uma ferramenta de pesquisa e substituição, ou manualmente no seu banco de dados
**Temas** | Acesse a **aparência** -> **tema** e atualizar o tema do site conforme necessário
**Menus** | Se seu tema suporta menus, links à home page ainda terá o diretório sub antigo incorporado a eles. Acesse a **aparência** -> **Menus** e atualizá-los

#### <a name="the-backup-and-restore-method"></a>O método de backup e restauração

1. Fazer backup de seu WordPress existente do site usando as informações em [backups WordPress][wordpressbackup].

2. Fazer backup de seu banco de dados existente usando as informações em [fazer backup de seu banco de dados][wordpressdbbackup].

3. Criar um banco de dados e restaurar o backup.

    1. Comprar um novo banco de dados do [Azure Marketplace][cdbnstore], ou a instalação de um banco de dados MySQL em um [Windows] [ mysqlwindows] ou [Linux] [ mysqllinux] máquina virtual.

    2. Usando um cliente de MySQL como o [Ambiente de trabalho MySQL][workbench], conectar-se ao banco de dados novo e importar seu banco de dados do WordPress.

    3. Atualize o banco de dados para alterar as entradas de domínio para o novo domínio de serviço de aplicativo do Azure. Por exemplo, mywordpress.azurewebsites.net. Use a [Pesquisar e substituir para Script de bancos de dados de WordPress] [ searchandreplace] com segurança alterar todas as instâncias.

4. Criar um aplicativo web no portal do Azure e publicar o backup WordPress.

    1. Criar um aplicativo web no [portal do Azure] [ mgmtportal] com um banco de dados usando o **novo** -> **Web + Mobile** -> **Azure Marketplace** -> **Web Apps** -> **Web app + SQL** (ou **aplicativo Web + MySQL**) -> **criar**. Configure todas as configurações necessárias para criar um aplicativo web vazia.

    2. No seu backup WordPress, localize o arquivo **wp-config.php** e abra-o em um editor. Substitua as seguintes entradas com as informações para seu novo banco de dados MySQL.

        * **DB_NAME** - o nome de usuário do banco de dados

        * **DB_USER** - o nome de usuário utilizado para acessar o banco de dados

        * **DB_PASSWORD** - a senha do usuário

        Depois de alterar essas entradas, salve e feche o arquivo **wp-config.php** .

    3. Use a [implantar um aplicativo web no serviço de aplicativo do Azure] [ deploy] informações para habilitar o método de implantação que deseja usar e, em seguida, implantar o backup WordPress em seu aplicativo web do serviço de aplicativo do Azure.

5. Depois que o site WordPress tiver sido implantado, você deve ser capaz de acessar o novo site (como um serviço de aplicativo web app) usando o *. azurewebsite.net URL do site.

### <a name="configure-your-site"></a>Configurar seu site

Após o WordPress site foi criado ou migrado, use as seguintes informações para melhorar o desempenho ou habilitar a funcionalidade adicional.

Para fazer isso... | Use isto...
------------- | -----------
**Definir o modo de plano de serviço de aplicativo, tamanho e habilitar dimensionamento** | [Dimensionar um aplicativo web no serviço de aplicativo do Azure][websitescale]
**Habilite as conexões de banco de dados persistente** | Por padrão, WordPress não usa conexões de banco de dados persistente, que podem causar sua conexão ao banco de dados se tornam acelerada após várias conexões. Para habilitar conexões persistentes, instale-o (conexões persistentes adaptador) [https://wordpress.org/plugins/persistent-database-connection-updater/installation/]. 
**Melhorar o desempenho** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Desabilitar o cookie ARR</a> - pode melhorar o desempenho quando executando WordPress em várias instâncias de aplicativos Web</p></li><li><p>Habilite o cache. <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Relacionada cache</a> (visualização) pode ser usada com o <a href="https://wordpress.org/plugins/redis-object-cache/">relacionada objeto cache WordPress plug-in</a>, ou usar uma das outras ofertas cache da <a href="/gallery/store/">Loja do Azure</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Como fazer WordPress mais rápido com Wincache</a> - Wincache é ativada por padrão para aplicativos Web</p></li><li><p><a href="../web-sites-scale/">Escala de um aplicativo web no serviço de aplicativo do Azure</a> e uso <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB alta disponibilidade roteamento</a> ou <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a></p></li></ul>
**Usar blobs para armazenamento** | <ol><li><p><a href="../storage-create-storage-account/">Criar uma conta de armazenamento do Azure</a></p></li><li><p>Saiba como <a href="../cdn-how-to-use/">usar a rede de distribuição de conteúdo (CDN)</a> para localização geográfica-distribuir dados armazenados em bolhas.</p></li><li><p>Instalar e configurar o <a href="https://wordpress.org/plugins/windows-azure-storage/">Armazenamento do Azure para WordPress plug-in</a>.</p><p>Configuração detalhadas e informações de configuração do plug-in, consulte o <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">Guia do usuário</a>.</p> </li></ol>
**Habilitar o email** | Habilite <a href="https://azure.microsoft.com/en-us/marketplace/partners/sendgrid/sendgrid-azure/">SendGrid</a> usar o armazenamento do Azure. Instale o <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified">plug-in SendGrid</a> para WordPress.
**Configurar um nome de domínio personalizado** | [Configurar um nome de domínio personalizado no serviço de aplicativo do Azure][customdomain]
**Habilitar HTTPS para um nome de domínio personalizado** | [Habilitar HTTPS para um aplicativo web no serviço de aplicativo do Azure][httpscustomdomain]
**Carregar saldo ou geográfica-distribuir seu site** | [Rotear o tráfego com o Gerenciador de tráfego do Azure][trafficmanager]. Se você estiver usando um domínio personalizado, consulte [Configurar um nome de domínio personalizado no serviço de aplicativo do Azure] [ customdomain] para obter informações sobre como usar o Gerenciador de tráfego com nomes de domínio personalizado
**Habilitar backups automatizados** | [Fazer backup de um aplicativo web no serviço de aplicativo do Azure][backup]
**Habilitar o log de diagnóstico** | [Habilitar o log de diagnóstico para web apps em um serviço de aplicativo do Azure][log]

## <a name="next-steps"></a>Próximas etapas

* [Otimização do WordPress](http://codex.wordpress.org/WordPress_Optimization)

* [Converter WordPress multissite no serviço de aplicativo do Azure](web-sites-php-convert-wordpress-multisite.md)

* [Assistente para Azure de atualização de ClearDB](http://www.cleardb.com/store/azure/upgrade)

* [Hospedagem WordPress em uma subpasta do seu aplicativo web do serviço de aplicativo do Azure](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Passo a passo: Criar um site de WordPress usando o Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Hospedar seu blog WordPress existente no Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Habilitando bonita links permanentes no WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [Como migrar e executar seu blog WordPress no serviço de aplicativo do Azure](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [Como executar WordPress no serviço de aplicativo do Azure gratuitamente](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress no Azure em 2 minutos ou menos](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Movendo um blog WordPress para Azure - parte 1: criar um blog WordPress no Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Movendo um blog WordPress para Azure - parte 2: transferir seu conteúdo](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Movendo um blog WordPress para Azure - parte 3: configurar seu domínio personalizado](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Movendo um blog WordPress para Azure - parte 4: quase links permanentes e regras reconfiguração de URL](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Movendo um blog WordPress para Azure - parte 5: mover de uma subpasta na raiz](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Como configurar um aplicativo da web de WordPress na sua conta do Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Propping backup WordPress no Azure](http://www.johnpapa.net/wordpress-on-azure/)

* [Dicas para WordPress no Azure](http://www.johnpapa.net/azurecleardbmysql/)

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

## <a name="whats-changed"></a>O que mudou
* Para um guia para a alteração de sites para o serviço de aplicativo consulte: [o serviço de aplicativo do Azure e seu impacto sobre serviços existentes do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- URL List -->

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: ../traffic-manager/traffic-manager-overview.md
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: https://azure.microsoft.com/documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]: ../virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md
[mysqllinux]: ../virtual-machines/virtual-machines-linux-classic-mysql-on-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: ../powershell-install-configure.md
[Azure CLI]: ../xplat-cli-install.md
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: ../cdn/cdn-overview.md
