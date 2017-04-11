
<properties 
   pageTitle="Notas de versão do Azure SDK para .NET 2.7 e .NET 2.7.1" 
   description="Notas de versão do Azure SDK para .NET 2.7 e .NET 2.7.1" 
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

# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Notas de versão do Azure SDK para .NET 2.7 e .NET 2.7.1

##<a name="overview"></a>Visão geral

Este documento contém as notas de versão para o SDK do Azure para .NET 2.7 lançamento. 

O documento também contêm as notas de versão para o SDK do Azure para .NET 2.7.1 de lançamento.

Azure SDK 2.7 só é suportado no Visual Studio 2015 e Visual Studio 2013. [Azure SDK 2.6](https://azure.microsoft.com/downloads/) é que o último suportados SDK para Visual Studio 2012.

Para obter informações detalhadas sobre esta versão, consulte [comunicado Azure SDK 2.7 postar](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) e [postar comunicado SDK do Azure 2.7.1](http://go.microsoft.com/fwlink/?LinkId=623850).

##<a name="azure-sdk-for-net-27"></a>SDK do Azure para .NET 2.7

###<a name="sign-in-improvements-for-visual-studio-2015"></a>Entrar no aprimoramentos para Visual Studio de 2015

2.7 SDK do Azure para Visual Studio 2015 suporta os novos recursos de gerenciamento de identidade no Visual Studio 2015.  Isso inclui suporte para contas acessando Azure por meio de controle de acesso com base em função, provedores de solução de nuvem, DreamSpark e outros tipos de conta e assinatura.

Os aperfeiçoamentos entrar incluídos com o Azure SDK 2.7 só estão disponíveis no Visual Studio 2015. Suporte para Visual Studio 2013 está incluído no SDK do Azure 2.7.1.


###<a name="mobile-sdk"></a>SDK móvel

Atualizar modelos de **Aplicativos móveis** para refletir o processo de instalação e [pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) mais recente.

###<a name="service-bus"></a>Barramento de serviço 

Aprimoramentos e correções de erros gerais. Para detalhes sobre atualizações e recursos, consulte as notas de versão do mais recente [Serviço barramento NuGet](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).

###<a name="hdinsight-tools"></a>Ferramentas de HDInsight 

Nesta versão, as seguintes atualizações foram feitas. Essas atualizações estão na visualização. Para obter mais informações, consulte [Este blog](http://go.microsoft.com/fwlink/?LinkId=619108).

- Gráficos de seção para a seção em trabalhos de Tez
- Suporte completo a seção DML IntelliSense
- Suporte de modelo de porco
- Modelos de tempestade para os serviços do Azure

####<a name="breaking-changes"></a>Alterações significativas

- Projeto de **tempestade** antigo deve ser atualizado quando esta versão das ferramentas. Para obter mais informações, consulte [Este blog](http://go.microsoft.com/fwlink/?LinkId=619108).
- Visual Studio Web Express não é mais suportado. Para obter mais informações, consulte [Este blog](http://go.microsoft.com/fwlink/?LinkId=619108).

###<a name="azure-app-service-tools"></a>Ferramentas de serviço de aplicativo Azure

Nesta versão, as seguintes atualizações foram feitas extensões de ferramentas da Web. Para obter mais informações, consulte [Este](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) blog. 

- Suporte para contas de DreamSpark adicionadas
- Total de alterações para ferramentas do Azure feitas para suportar as novas APIs de gerenciamento de recursos do Azure
- Suporte para o serviço de aplicativo do Azure adicionado ao [Explorador de nuvem](#cloud_explorer)

####<a name="known-issues"></a>Problemas conhecidos

Nós de slot de implantação de aplicativo Web não aparecem sob o nó de Slots no Server Explorer e nós filhos do Web App implantação slot não são carregadas em nuvem Explorer. Esse problema foi resolvido e preparado para o próximo lançamento do SDK. 


###<a name="cloud_explorer"></a>Explorador de nuvem para Visual Studio de 2015

Azure 2.7 SDK inclui Explorer de nuvem para Visual Studio de 2015 que permite exibir seus recursos Azure, inspecionar suas propriedades e executar ações de chave do desenvolvedor de dentro do Visual Studio. 

Explorador de nuvem suporta o seguinte:

- Grupo de recursos e o tipo de recurso modos de exibição de seus recursos Azure 
- Pesquisar recursos por nome (disponível no modo de exibição do tipo de recurso)
- Suporte para assinaturas e recursos que têm a função com base em acesso controle (RBAC) aplicado 
- Painel Ação integrado que mostra voltada para desenvolvedores ações específicas para recursos selecionados. Por exemplo: Anexar depurador remoto para máquinas virtuais criadas usando a pilha de Gerenciador de recursos, exibir dados de diagnóstico para máquinas virtuais etc.
- Painel de propriedades integrado que mostra propriedades voltada para desenvolvedores comumente necessárias durante o desenvolvimento/teste 
- Alternância rápida da conta usar quando enumeração de recursos (use o comando configurações na barra de ferramentas) 
- Filtragem de assinaturas para usar quando enumeração de recursos (use o comando configurações na barra de ferramentas) 
- Links profunda do portal do Azure para gerenciamento de recursos e grupos de recursos 
 
 
###<a name="azure-resource-manager-tools"></a>Ferramentas do Gerenciador de recurso Azure 

As ferramentas do Gerenciador de recursos do Azure foram atualizadas para trabalhar com novos tipos de assinatura e controle de acesso baseado da função (RBAC).  A capacidade de usar novas contas de armazenamento, além de armazenamento clássico, armazenar artefatos durante a implantação está incluído com essas alterações.  

Se você estiver usando um projeto de grupo de recursos do Azure de uma versão anterior do SDK com o SDK 2.7, um novo script de implantação é necessária para implantar usando uma nova conta de armazenamento em vez de armazenamento clássico.  Você será solicitado antes que as alterações sejam feitas ao seu projeto para adicionar o novo script.  O script antigo será renomeado e você precisará fazer manualmente as modificações para o novo script.
 
 
###<a name="storage-explorer-tools"></a>Ferramentas do Explorador de armazenamento 

- Suporte para a visualização Blobs acrescentar. Mais informações [nesta](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx)postagem de blog. 
- Suporte para a visualização de contas de armazenamento de Premium através do Server Explorer. Server Explorer só exibirá blobs de página para contas de armazenamento premium conforme eles são o único tipo suportado para contas de armazenamento premium.

###<a name="azure-data-factory-tools-for-visual-studio"></a>Ferramentas de fábrica de dados do Azure para Visual Studio 

Apresentando **Ferramentas de fábrica de dados do Azure** para Visual Studio. Abaixo estão os recursos habilitados. Consulte [Este blog](http://go.microsoft.com/fwlink/?LinkId=617530) para obter mais informações.

- **Criação de modelo com base**: selecione causado por uso com base em modelos, modelos de movimentação de dados ou modelos de processamento de dados implantar uma solução de integração de dados de ponta a ponta e começar a usar práticos rapidamente com dados fábrica. 
- **Integração com o Explorer de solução de criação e implantação entidades de fábrica de dados**: criar e implantar canais e entidades relacionadas como projetos do Visual Studio. 
- **Integração com o diagrama exibir para interação visual durante a criação**: autor visualmente canais e conjuntos de dados com o auxílio do modo de exibição Diagrama. 
- **Integração com o Explorador de servidor para navegação e a interação com entidades já implantadas**: aproveitar no Server Explorer para procurar já implantada dados fábricas e entidades correspondentes. Importe uma fábrica de dados implantado ou qualquer entidade (Pipeline, serviço vinculadas, conjuntos de dados) para o seu projeto. 
- **Edição de JSON com validação de esquema e o intellisense rich**: configurar com eficiência e editar documentos JSON de entidades de fábrica de dados com validação avançada do intellisense e esquema 
- **Publicação de ambiente vários**: publicar criados canais para desenvolvimento, teste ou ambiente de produção criando arquivos config separada para cada ambiente.
- **Suporte de processamento de dados com base em porco, seção e .net**: suporte porco e Scripts de seção no projeto de fábrica de dados. Suporte para a referência de projeto c# para gerenciar .net atividade.

##<a name="azure-sdk-for-net-271"></a>SDK do Azure para .NET 2.7.1

A seção a seguir contém atualizações foram introduzidas com o SDK do Azure para .NET 2.7.1 de lançamento.
###<a name="hdinsight-tools"></a>Ferramentas de HDInsight 

Para obter uma explicação mais detalhada sobre as atualizações de ferramentas de HDInsight, consulte [Este blog](http://go.microsoft.com/fwlink/?LinkId=623831).

- Seção modo de exibição de operador de trabalho (um novo recurso)

    Para ajudá-lo a compreender sua consulta de seção melhor, o recurso de modo de exibição de seção operador foi adicionado. Para ver todos os operadores dentro de um vértice, clique duas vezes nos vértices do gráfico de trabalho. Para exibir mais detalhes de um operador específico, passe o mouse sobre esse operador.
- Marcador de erro de seção (um novo recurso)

    Para que você possa exibir os erros de gramática instantaneamente, o recurso de marcador de erro seção foi adicionado. Além disso, mensagens de erro foram aprimoradas e agora você pode ver erros de gramática detalhadas instantaneamente (até nesta versão, você tinha que enviar um script de seção ao cluster e aguarde alguns instantes até obter detalhes sobre sua mensagem de erro).  
- Gráfico de topologia de tempestade (um novo recurso)

    Visualizando é muito importante quando você quiser ver se sua topologia está funcionando conforme esperado. Nesta versão, adicionamos visualização para gráficos de tempestade. Você pode visualizar as métricas importantes para a sua topologia (por exemplo, uma cor indica o clima um determinado raio é "ocupado" ou não). Você pode também clicar duas vezes o raio/Spout para exibir mais detalhes.

- Suporte para clusters de HDInsight que foram criados no Portal do Azure (uma correção de erros)

    Agora você pode usar o Visual Studio para exibir e enviar trabalhos para todos os seus clusters de HDInsight, não importa onde o cluster foram criados.

- Mais suporte IntelliSense & com mais rapidez metadados seção carregando (um aprimoramento)

    Podemos aprimorados o IntelliSense adicionando mais sugestões de amigável. Por exemplo, alias de tabela pode agora também sugerida no IntelliSense portanto você pode escrever sua consulta mais facilmente. Além disso, podemos aprimorados os metadados de seção Carregando para que apenas levará vários segundos para listar todos os bancos de dados, tabelas e colunas de sua metastore de seção.

Para obter uma explicação mais detalhada sobre as atualizações de ferramentas de HDInsight, consulte [Este blog](http://go.microsoft.com/fwlink/?LinkId=623831).

###<a name="improvements-in-visual-studio-2013"></a>Melhorias no Visual Studio 2013

- Azure SDK 2.7.1 permite Visual Studio de 2013 acessar contas do Azure e inscrições via Dreamspark, provedores de solução de nuvem e controle de acesso com base em função.
- Com o SDK do Azure 2.7.1, a nova janela de ferramenta do Explorador de nuvem agora também está disponível no Visual Studio 2013.

###<a name="known-issues"></a>Problemas conhecidos

Instalando o Azure SDK 2.6 ou 2.7.1 para 2013 de comunidade do Visual Studio em um não - inglês OS exibirá um aviso de que os recursos de inglês e inglês do Visual Studio podem ser incompatíveis. Este aviso pode ser descartado com segurança. Ele só ocorrerá se a máquina não continha uma instalação anterior do 2013 de comunidade do Visual Studio e você estiver instalando o SDK em um não - inglês OS. O aviso é exibido depois que o pacote de idiomas aplica-se os recursos RTM ao Visual Studio, mas antes de aplicar atualização 4. Dispensar o aviso permitirá que o pacote de idiomas continuar e concluir aplicando a versão de atualização 4 do conteúdo do pacote de idioma.

Projetos do LightSwitch não são compatibile com esta versão. Esse problema será resolvido com o próximo lançamento do SDK.

##<a name="also-see"></a>Consulte também
[Azure SDK 2.7.1 postagem de lançamento](http://go.microsoft.com/fwlink/?LinkId=623850)

[Postagem de lançamento do Azure SDK 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Suporte e informações de aposentadoria para o SDK do Azure para .NET e APIs](https://msdn.microsoft.com/library/azure/dn479282.aspx/)
