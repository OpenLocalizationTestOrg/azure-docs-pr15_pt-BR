
<properties 
   pageTitle="Notas de versão do SDK do Azure para .NET 2,8" 
   description="Notas de versão do SDK do Azure para .NET 2,8" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/17/2016"
   ms.author="juliako"/>
 
# <a name="azure-sdk-for-net-28-281-and-282"></a>SDK do Azure para .NET 2,8, 2.8.1 e 2.8.2

##<a name="overview"></a>Visão geral
 
Este artigo contém as notas de versão (que inclui problemas conhecidos e alterações significativas) para o SDK do Azure para .NET 2,8, 2.8.1 e 2.8.2 versões. 

Para obter uma lista completa dos novos recursos e as atualizações feitas nesta versão, consulte o anúncio de [2,8 de SDK do Azure para Visual Studio 2013 e o Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) . 

##  <a name="azure-sdk-for-net-28"></a>SDK do Azure para .NET 2,8

### <a name="download-azure-sdk-for-net-28"></a>Baixe o SDK do Azure para .NET 2,8

[SDK do Azure para .NET 2,8 para Visual Studio de 2015](http://go.microsoft.com/fwlink/?LinkId=699285) 

[SDK do Azure para .NET 2,8 para Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=699287)
 
### <a name="net-452-support"></a>.NET 4.5.2 suporte 

####<a name="known-issues"></a>Problemas conhecidos

2,8 de SDK do Azure .NET permite que você crie .NET 4.5.2 pacotes de serviço de nuvem. No entanto 4.5.2 do .NET framework não será instalado no padrão lançamento de imagens de SO convidado até janeiro de 2016 SO convidado. Antes que, 4.5.2 o .NET framework estará disponível por meio de uma versão de lançamento de SO convidado separada – novembro de 2015-02. Consulte a página de [versões de sistemas operacionais de convidado do Azure e matriz de compatibilidade do SDK](../cloud-services/cloud-services-guestos-update-matrix.md) para controlar quando a imagem será lançada.  Depois que a imagem de 2015-02 de novembro é lançada você pode optar por usar imagem atualizando seu arquivo de configuração serviço de nuvem (.cscfg). Na configuração de serviço arquivo definir o atributo de osVersion do elemento ServiceConfiguration à cadeia de caracteres "WA-convidado-SO-4.26_201511-02". Se você optar por desativar essa opção para usar esta imagem, então você não poderá mais receber atualizações automáticas para o sistema operacional convidado. Para obter as atualizações automáticas osVersion deve estar definida como "*" e .NET 4.5.2 só estará disponível por meio de atualizações automáticas no janeiro de 2016.

###<a name="azure-data-factory"></a>Fábrica de dados do Azure

####<a name="known-issues"></a>Problemas conhecidos 

Durante a criação de projeto um **Modelo de fábrica de dados** envolvendo dados de exemplo, script do shell de energia azure pode falhar se a versão do shell do power azure instalado na máquina estiver após 0.9.8.

Para criar esse tipo de projeto com êxito, é necessário instalar a [versão do azure power shell 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).


### <a name="azure-resource-manager-tools"></a>Ferramentas do Gerenciador de recurso Azure 

####<a name="breaking-changes"></a>Alterações significativas

O script do PowerShell fornecido pelo projeto de grupo de recursos do Azure foi atualizado nesta versão para trabalhar com cmdlets do PowerShell do Azure novato versão 1.0.  Este novo script não funcionará de com o Visual Studio ao usar uma versão do SDK antes de 2,8.  

Scripts de projetos criados em versões anteriores do SDK não serão executado de dentro do Visual Studio ao usar o SDK 2,8.  Todos os scripts vão continuar a trabalhar fora do Visual Studio com a versão apropriada do cmdlets do PowerShell do Azure.  

O SDK 2,8 requer versão 1.0 de cmdlets do PowerShell do Azure.  Todas as outras versões do SDK requerem versão 0.9.8 de cmdlets do PowerShell do Azure.  Para obter mais informações, consulte [Este](http://go.microsoft.com/fwlink/?LinkID=623011) blog.

###<a name="web-tools-extensions"></a>Extensões de ferramentas da Web

####<a name="known-issues"></a>Problemas conhecidos

Os seguintes problemas conhecidos serão tratados na versão seguinte.

- Serviço de aplicativo relacionado nuvem e Server Explorer gesto para ambientes de não-produção (como China Azure ou clientes do Azure pilha) não funcionam. Para clientes nessas áreas afetados, baixando o perfil de publicação a partir do portal do Azure permitirá capacidade de publicação. Uma versão futura irá reparar gestos como "Anexar depurador" e "Exibir Logs Streaming" Azure China e clientes de pilha. 
- Os clientes podem ver erros durante o serviço de aplicativo de criação quando a obtenção de informações de aplicativo instância à qual eles estão implantando está em uma região diferente de EUA Leste. Nesses cenários, a criação de um serviço de aplicativo no portal e baixar o perfil de publicação permitirá cenários de publicação. 

###<a name="azure-hdinsight-tools"></a>Ferramentas do Azure HDInsight

####<a name="new-updates"></a>Novas atualizações

- Você pode executar a consulta de seção no cluster por HiveServer2 com quase nenhum sobrecarga e ver que o trabalho logs tempo real.
- Usando a nova seção execução exibição da tarefa que você pode aprofundar o trabalho mais profundo, encontrar mais detalhes e identificar possíveis problemas.

Para obter informações, consulte [2,8 de SDK do Azure para Visual Studio 2013 e o Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/). 

## <a name="azure-sdk-for-net-281"></a>SDK do Azure para .NET 2.8.1

### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Problemas conhecidos para Visual Studio 2013 e o Visual Studio de 2015
 
1. WebJob disparada publica slots irá mostrar e erro e não definir uma agenda, mas ele fará com que o WebJob no Azure. Os clientes que se precisar de um trabalho agendado, em seguida, podem usar o Portal do Azure para configurar a agenda para o WebJob. 
2. Clientes de Python poderá ter problemas de depurador. Equipe de serviço é implantar uma correção para isso, mas se os clientes são afetados, fale Microsoft souber nos fóruns ou no blog do comunicado ou seção de comentários de notas de lançamento. 
3. Clientes em determinadas regiões (como Sul Índia) terão erros de provisionamento do serviço de aplicativo. Isso é consistente com o portal e clientes que esse problema podem usar o portal do Azure para solicitar acesso para publicar essas regiões de localização geográfica. Depois que eles solicitam acesso a essas regiões usando o Azure provisionamento portal deve funcionar. 

##<a name="azure-sdk-for-net-282"></a>SDK do Azure para .NET 2.8.2

Após a instalação do 2.8.2 ferramentas, os clientes podem enfrentar o problema a seguir.         

- Se você estiver usando o Windows 10 e não tiver instalado o Internet Explorer, você pode receber um erro de "Internet Explorer não encontrado".
Para resolver o problema, instale o Internet Explorer usando a caixa de diálogo Adicionar ou remover componentes do Windows.

Se você observar esse problema, use o recurso enviar um sorriso relatá-lo.

Para obter mais informações, consulte [Esta](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) postagem.
##<a name="other-updates"></a>Outras atualizações

Para outras atualizações, consulte [comunicado Azure SDK 2,8 postar](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

##<a name="also-see"></a>Consulte também

[Postagem de lançamento do Azure SDK 2,8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Suporte e informações de aposentadoria para o SDK do Azure para .NET e APIs](https://msdn.microsoft.com/library/azure/dn479282.aspx)

