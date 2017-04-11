<properties
    pageTitle="Perguntas Frequentes sobre bancos de dados ClearDB MySql com o serviço de aplicativo do Azure | Microsoft Azure"
    description="Respostas a perguntas comuns sobre o uso de bancos de dados ClearDB MySQL com o serviço de aplicativo do Azure."
    documentationCenter="php"
    services=""
    authors="sunbuild"
    manager="yochayk"
    editor=""
    tags="mysql"/>

<tags
    ms.service="multiple"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="sumuth"/>

# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Perguntas Frequentes sobre bancos de dados ClearDB MySql com o serviço de aplicativo do Azure

Estas perguntas Frequentes respondem dúvidas comuns sobre como usar e adquirir ClearDB MySQL bancos de dados para aplicativos Web do Azure.

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Quais são as opções que tenho para MySQL no Azure?

Você tem várias opções:

* [Banco de dados MySQL compartilhados de ClearDB](/marketplace/partners/cleardb/databases/)

* [Clusters ClearDB MySQL Premium](/marketplace/partners/cleardb-clusters/cluster/)

* [Cluster MySQL em execução em uma máquina virtual do Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)

* [Única instância do MySQL em execução em uma máquina virtual do Azure](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md)

ClearDB é um serviço de hospedagem de MySQL e gerencia a infraestrutura de MySQL para você. Quando você executa seu próprio cluster MySQL ou banco de dados em uma máquina Virtual do Azure, você precisa configurar o servidor MySQL e mantê-lo atualizado com patches.

## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>É necessário um cartão de crédito do Web app + modelo MySQL do Azure Marketplace?

Isso depende do tipo de assinatura que você está usando. Aqui estão alguns tipos de assinatura comumente usados:

* [Pré-pago](/offers/ms-azr-0003p/): requer um cartão de crédito e, quando você compra um banco de dados MySQL pago seu cartão de crédito é cobrado.

* [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/): inclui créditos para uso com o Microsoft Azure services, mas não permite a compra de recursos de terceiros. A compra de serviços de terceiros ou um banco de dados MySQL pago que você precisa usar uma assinatura de cartão de crédito ativado. Para aplicativos Web, você pode criar um banco de dados MySQL de ClearDB gratuito.

* [Assinatura do MSDN](/pricing/member-offers/msdn-benefits/) e **pagar de teste de desenvolvimento do MSDN como você vá**: semelhante a avaliação gratuita, uma assinatura MSDN requer que você tenha um cartão de crédito para adquirir uma solução de MySQL paga do ClearDB.

* [Enterprise Agreement (EA)](/pricing/enterprise-agreement/): os clientes do EA são cobrados contra seu EA cada trimestre para todas as suas compras de (terceiros) do Azure Marketplace em uma fatura consolidada, separada. Você será cobrado fora o compromisso monetário para qualquer compras marketplace. Observe que, no momento, Azure Store não está disponível para clientes registrados no Azerbaijão, Croácia, Noruega e Porto Rico. 

*  [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): você pode criar somente ClearDB gratuito bancos de dados para aplicativos Web. Não há nenhum limite no número de bancos de dados MySQL gratuito de ClearDB que você pode criar. Observe que gratuito bancos de dados não deve ser usado para aplicativos da web de produção, pois esse serviço se destina somente para avaliação.

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Por que eu foi cobrado $3.50 para um aplicativo Web + MySQL do Azure Marketplace?

A opção de banco de dados padrão é Titan, que é r $3.50. Podemos não mostram o custo durante a criação de banco de dados e por engano, você pode comprar um banco de dados que você não pretende. Estamos tentando encontrar uma maneira para melhorar a experiência, mas até então você deve verificar todos os seus níveis de preços selecionados para web app e o banco de dados antes de clicando em **criar** e iniciar a implantação dos recursos.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Estou executando o MySQL em minha máquina virtual Azure. Posso conectar meu aplicativo web Azure para meu banco de dados?

Sim. Você pode conectar seu aplicativo web ao seu banco de dados, desde que sua máquina virtual do Azure lhe concedeu acesso remoto para seu aplicativo web. Para obter mais informações, consulte [Instalar MySQL em uma máquina virtual](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md).

## <a name="in-which-countries-are-cleardb-premium-mysql-clusters-supported"></a>Nas quais países são clusters ClearDB Premium MySQL com suporte?

[Clusters de ClearDB Premium MySQL](/marketplace/partners/cleardb-clusters/cluster/) estão disponíveis em todas as regiões Azure em todo o mundo excepto Índia, Austrália, Brasil Sul e China.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Posso criar um novo cluster antes de criar um banco de dados com a solução de cluster ClearDB premium?

Não, não há suporte para criando clusters de ClearDB vazias. O portal do Azure permite que você crie bancos de dados em um cluster, o que pode criar um novo cluster ao mesmo tempo.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Será eu avisado se tentar excluir um banco de dados ClearDB MySQL que está em uso por um dos meus aplicativos?

Não, Azure não avisará se você excluir uma compra marketplace que depende de seu aplicativo.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Quais são as regiões pode criar ClearDB bancos de dados?

Azure Marketplace não está disponível para clientes registrados no Azerbaijão, Croácia, Noruega ou Porto Rico. ClearDB não está disponível nessas regiões.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Quais níveis de preços deve escolher um aplicativo da web de produção e o banco de dados?

Use Basic ou um nível de preços superior para aplicativos Web. Para ClearDB, recomendamos um Saturno ou Júpiter plano. Examine os recursos e limitações de cada nível de preços para [Aplicativos Web](/pricing/details/app-service/) e [bancos de dados ClearDB MySQL](/marketplace/partners/cleardb/databases/) escolher aquele que atenda às suas necessidades.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Como faço para atualizar meu banco de dados ClearDB de um plano para outro?

No [portal do Azure](https://portal.azure.com), você pode dimensionar o ClearDB hospedagem banco de dados compartilhado. Leia este [artigo](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) para saber mais. Atualmente, não suportamos atualização para ClearDB Premium clusters no portal do Azure.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Não consigo ver meu banco de dados ClearDB no portal Azure?

Se podemos criar banco de dados de ClearDB usando o Gerenciador de recursos do Azure ou [Novo Portal do Azure](https://portal.azure.com), ele não ficará visível no [Portal do Azure antigo](https://manage.windowsazure.com). Para contornar isso é vincular seu banco de dados manualmente o aplicativo web. Da mesma forma se criar banco de dados de ClearDB no [portal antigo](https://manage.windowsazure.com) não será capaz de ver seu banco de dados no [Novo Portal do Azure](https://portal.azure.com). Não há nenhuma solução alternativa para o último cenário.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Quem contatar para obter suporte quando meu banco de dados está desativado?

Contato de [suporte de ClearDB](https://www.cleardb.com/developers/help/support) para qualquer banco de dados, problemas relacionados ao. Prepare-se para fornecer informações sobre sua assinatura Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Pode criar usuários adicionais para minha solução de cluster de banco de dados ClearDB MySQL? 

Não. Não é possível criar usuários adicionais, mas você pode criar bancos de dados adicionais em seu cluster de banco de dados ClearDB.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Pode série Basic/Pro bancos de dados ser atualizado in-loco semelhante aos planos planetários hoje no portal de ClearDB?

Sim, série básica bancos de dados podem ser atualizado in-loco (60 básica por meio de 500 básico). Série de Pro pode ser atualizados no local (125 Pro e Pro 1000), com exceção de 60 Pro. Não há suporte para atualização de banco de dados de 60 Pro atualmente. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Quando eu migrar meus recursos de uma assinatura para outro, meu banco de dados ClearDB MySQL obter migrado também? 

Quando você executa a migração de recurso em assinaturas, algumas [limitações](./app-service-web/app-service-move-resources.md) se aplicam. Um banco de dados ClearDB MySQL é um serviço de terceiros e, portanto, não será migrado durante a migração de assinatura Azure. Se você não gerencia a migração de seu banco de dados MySQL antes da migração de recursos Azure, seus bancos de dados ClearDB MySQL podem ser desabilitados. Migrar manualmente seus bancos de dados pela primeira vez e então executar a migração de assinatura Azure para seu aplicativo web. 

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>É possível transferir um banco de dados ClearDB de uma assinatura de cartão de crédito para uma assinatura de EA?

Bancos de dados existentes ClearDB usam o cartão de crédito associado com as assinaturas existentes. Para usar uma assinatura de EA você precisa migrar seus dados para um novo banco de dados:

* Compre um novo banco de dados de ClearDB com sua assinatura EA.
* Migre seus dados para o seu novo banco de dados.
* Atualize seu aplicativo para usar o novo banco de dados.
* Exclua seu banco de dados antigo ClearDB.

Quando você cria um novo aplicativo web com MySQL (ClearDB) ou cria um banco de dados MySQL (ClearDB), a assinatura que você escolher determina como você pagarão para o serviço. Com uma assinatura de EA, podemos não bloqueará a aquisição dos serviços de terceiros como ClearDB no portal do Azure. Assinaturas de EA são cobradas fora do compromisso monetárias e são cobradas trimestrais e com atraso de pagamento. O cliente EA teria que configurar um método de pagamento como um cartão de crédito para pagar quaisquer serviços de terceiros marketplace.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Onde posso ver as cobranças para recursos de ClearDB em uma assinatura de EA?

Para clientes do EA direto, encargos do Azure Marketplace estão visíveis no Portal Empresarial. Observe que todas as compras de marketplace e consumo são cobrados fora do compromisso monetárias e são cobrados trimestral e com atraso de pagamento. Os clientes do EA precisam pagar diretamente para os provedores de serviços de terceiros e poderá fazer isso habilitando um método de pagamento como um cartão de crédito com sua conta EA.

Clientes EA indiretos podem encontrar suas assinaturas do Azure Marketplace na página **Gerenciar assinaturas** do Portal Empresarial, mas preços estiver oculto. Os clientes devem contatar seu LSP para obter informações sobre encargos marketplace.

Acesso ao Azure Marketplace para serviços de terceiros pode ser gerenciado por seus administradores de inscrição EA Azure. Eles podem desabilitar ou habilitar novamente acesso à aquisição de festa 3º do repositório em gerenciar contas e assinaturas sob a seção de contas no Portal Empresarial.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Quem contatar para perguntas sobre minha fatura para serviços de ClearDB em minha assinatura EA?

Contate o [Suporte de cliente Enterprise](http://aka.ms/AzureEntSupport) com relação a cobrança em seu registro de EA. A equipe de suporte do Portal de EA deverá responder sua pergunta ou ajudar a resolver seu problema.

 



## <a name="more-information"></a>Mais informações

[Perguntas Frequentes do Azure Marketplace](/marketplace/faq/)
