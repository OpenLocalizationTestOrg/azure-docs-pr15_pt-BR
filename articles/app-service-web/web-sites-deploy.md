<properties
    pageTitle="Implantar o seu aplicativo de serviço de aplicativo do Azure"
    description="Saiba como implantar o seu aplicativo de serviço de aplicativo do Azure."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="cephalin;dariac"/>
    
# <a name="deploy-your-app-to-azure-app-service"></a>Implantar o seu aplicativo de serviço de aplicativo do Azure

Este artigo ajuda a determinar a melhor opção para implantar os arquivos para seu aplicativo web, back-end do aplicativo móvel ou aplicativo de API ao [Serviço de aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)e, em seguida, orienta você para recursos apropriados com instruções específicas para sua opção preferida.

## <a name="overview"></a>Visão geral de implantação do Azure serviço de aplicativo

Serviço de aplicativo do Azure mantém a estrutura do aplicativo para você (ASP.NET, PHP, Node, etc). Algumas estruturas são ativadas por padrão, enquanto outros, como Java e Python, talvez seja necessário uma configuração de marca de seleção simples para ativá-lo. Além disso, você pode personalizar a estrutura do seu aplicativo, como a versão PHP ou o número de bits de seu tempo de execução. Para obter mais informações, consulte [configurar seu aplicativo no serviço de aplicativo do Azure](web-sites-configure.md).

Como você não precisa se preocupar com a estrutura de servidor ou aplicativo da web, implantar seu aplicativo de serviço de aplicativo é uma questão de implantação do seu código, binários, arquivos de conteúdo e estrutura seus respectivos diretórios, para o [diretório **/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) no Azure (ou a **/site/wwwroot/App_Data/trabalhos/** diretório de WebJobs). Serviço de aplicativo suporta as seguintes opções de implantação: 

- [FTP ou FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol): Use seu favorito FTP ou FTPS ativado ferramenta para mover arquivos para o Azure, do [FileZilla](https://filezilla-project.org) para IDEs completos como [NetBeans](https://netbeans.org). Este terminantemente é um processo de carregamento de arquivo. Sem serviços adicionais são fornecidos pelo serviço de aplicativo, como o controle de versão, gerenciamento de estrutura de arquivos, etc. 

- [Kudu (gito/Mercurial ou OneDrive/Dropbox)](https://github.com/projectkudu/kudu/wiki/Deployment): Use o [mecanismo de implantação](https://github.com/projectkudu/kudu/wiki) no aplicativo de serviço. Coloque seu código Kudu diretamente de qualquer repositório. Kudu também fornece serviços adicionados sempre que o código é enviado a ele, incluindo o controle de versão, restauração de pacote, MSBuild e [conexões da web](https://github.com/projectkudu/kudu/wiki/Web-hooks) para implantação contínua e outras tarefas de automação. O mecanismo de implantação de Kudu suporta 3 tipos diferentes de fontes de implantação:   
    * Sincronizar conteúdo do OneDrive e Dropbox   
    * Implantação contínua baseada em repositório com sincronizar automaticamente do GitHub, Bitbucket e os serviços de equipe do Visual Studio  
    * Implantação baseada em repositório com sincronização manual de gito local  

- [Implantação da Web](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy): implantar código ao aplicativo de serviço diretamente da Microsoft seu favorito ferramentas como Visual Studio usando a mesma ferramentas que automatiza implantação aos servidores IIS. Essa ferramenta oferece suporte a implantação somente comparação, criação de banco de dados, transformações de cadeias de caracteres de conexão, etc. Implantar Web difere Kudu em que binários do aplicativo são criados antes de serem implantadas no Azure. Semelhante ao FTP, sem serviços adicionais são fornecidos pelo serviço de aplicativo.

Ferramentas de desenvolvimento populares da web oferece suporte a um ou mais desses processos de implantação. Enquanto a ferramenta que você escolher determina os processos de implantação, que você pode usufruir, a funcionalidade de DevOps real à sua disposição depende da combinação do processo de implantação e as ferramentas específicas que você escolher. Por exemplo, se você executar implantação da Web do [Visual Studio com o SDK do Azure](#vspros), embora você não obtiver automação de Kudu, receberá MSBuild automação e restauração de pacote no Visual Studio. 

>[AZURE.NOTE] Esses processos de implantação não realmente [provisionar os recursos Azure](../resource-group-template-deploy-portal.md) que seu aplicativo pode precisar. No entanto, na maioria dos artigos de instruções vinculados mostra como provisionar o aplicativo e implante seu código a ele ponta a ponta. Você também pode encontrar opções adicionais para provisionamento de recursos de Azure na seção [Automatize a implantação usando ferramentas de linha de comando](#automate) .
     
## <a name="ftp"></a>Implantar via FTP copiando arquivos para Azure manualmente
Se você estiver acostumado a copiar manualmente o conteúdo da web em um servidor web, você pode usar um utilitário de [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) para copiar arquivos, como o Windows Explorer ou [FileZilla](https://filezilla-project.org/).

Os profissionais de copiar arquivos manualmente são:

- Familiaridade e complexidade mínima para ferramentas de FTP. 
- Saber onde seus arquivos estão indo exatamente.
- Segurança adicional com FTPS.

Copiando arquivos manualmente os contras são:

- Tendo saber como implantar arquivos aos diretórios corretos no aplicativo de serviço. 
- Sem controle de versão para reversão quando ocorrem falhas.
- Nenhum histórico de implantação interno para solucionar problemas de implantação.
- Possível implantação longa tempo porque muitas ferramentas FTP não fornecer somente comparação copiando e simplesmente copiar todos os arquivos.  

### <a name="howtoftp"></a>Como implantar copiando arquivos para Azure manualmente
Copiando arquivos para o Azure envolve algumas etapas simples:

1. Presumindo que você já estabeleceu credenciais de implantação, obter as informações de conexão de FTP acessando **configurações** > **Propriedades**e, em seguida, copiando os valores para **Usuário FTP/implantação**, **Nome de Host de FTP**e **FTPS Host Name**. Copie o valor de usuário do **Usuário FTP/implantação** conforme exibido pelo Portal do Azure, incluindo o nome do aplicativo para fornecer o contexto apropriado para o servidor FTP.
2. A partir do seu cliente de FTP, use as informações de conexão reunidas para se conectar ao seu aplicativo.
3. Copie seus arquivos e sua estrutura de diretório respectivos no [diretório **/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) no Azure (ou a **/site/wwwroot/App_Data/trabalhos/** diretório de WebJobs).
4. Navegue até a URL do seu aplicativo para verificar que o aplicativo está funcionando corretamente. 

Para obter mais informações, consulte o seguinte recurso:

* [Criar um aplicativo da web PHP MySQL e implantar usando FTP](web-sites-php-mysql-deploy-use-ftp.md).

## <a name="dropbox"></a>Implantar por sincronizando com uma pasta de nuvem
Uma boa alternativa para [copiar os arquivos manualmente](#ftp) está sincronizando arquivos e pastas para o serviço de aplicativo de um serviço de armazenamento de nuvem como o OneDrive e o Dropbox. Sincronizando com uma pasta de nuvem utiliza o processo de Kudu de implantação (consulte [Visão geral dos processos de implantação](#overview)).

Os profissionais de sincronizando com uma pasta de nuvem são:

- Simplicidade de implantação. Serviços como o OneDrive e o Dropbox fornecem clientes de sincronização para área de trabalho, para que seu diretório de trabalho local também é seu diretório de implantação.
- Implantação de um único clique.
- Todas as funcionalidades do mecanismo de implantação de Kudu estão disponível (por exemplo, a restauração de pacote, automação).

Os contras da sincronizando com uma pasta de nuvem são:

- Sem controle de versão para reversão quando ocorrem falhas.
- Nenhuma implantação automatizada, sincronização manual é necessária.

### <a name="howtodropbox"></a>Como implantar por sincronizando com uma pasta de nuvem
No [Portal do Azure](https://portal.azure.com), você pode designar uma pasta para sincronização de conteúdo em seu armazenamento de nuvem OneDrive ou Dropbox, trabalhar com o código de aplicativo e o conteúdo nessa pasta e sincronizar com o serviço de aplicativo com o clique de um botão.

* [Sincronizar o conteúdo de uma pasta de nuvem para o serviço de aplicativo do Azure](app-service-deploy-content-sync.md). 

## <a name="continuousdeployment"></a>Implantar continuamente de um serviço de controle de origem baseado em nuvem
Se sua equipe de desenvolvimento usa um serviço de gerenciamento (SCM) de código fonte baseado em nuvem como o [Visual Studio Team Services](http://www.visualstudio.com/), [GitHub](https://www.github.com)ou [BitBucket](https://bitbucket.org/), você pode configurar o serviço de aplicativo para integrar com o seu repositório e implantar continuamente. 

Os profissionais de implantação de um serviço de controle de origem baseado em nuvem são:

- Controle de versão para habilitar a reversão.
- Capacidade de configurar a implantação contínua para gito (e Mercurial onde aplicável) repositórios. 
- Implantação de ramificação específicas, pode implantar ramificações diferentes para diferentes [slots](web-sites-staged-publishing.md).
- Todas as funcionalidades do mecanismo de implantação de Kudu estão disponível (por exemplo, controle de versão de implantação, reversão, restauração de pacote, automação).

Con de implantação de um serviço de controle de origem baseado em nuvem é:

- Alguns dados de conhecimento do serviço SCM respectivo obrigatório.

###<a name="vsts"></a>Como implantar continuamente de um serviço de controle de origem baseado em nuvem
No [Portal do Azure](https://portal.azure.com), você pode configurar contínua implantação do GitHub, Bitbucket e Visual Studio Team Services.

* [Implantação de contínua ao serviço de aplicativo do Azure](app-service-continuous-deployment.md). 

## <a name="localgitdeployment"></a>Implantar a partir gito local
Se sua equipe de desenvolvimento usa um serviço de gerenciamento (SCM) de código do local fonte local com base em gito, você pode configurar isso como uma fonte de implantação ao aplicativo de serviço. 

Os profissionais de implantação de gito local são:

- Controle de versão para habilitar a reversão.
- Implantação de ramificação específicas, pode implantar ramificações diferentes para diferentes [slots](web-sites-staged-publishing.md).
- Todas as funcionalidades do mecanismo de implantação de Kudu estão disponível (por exemplo, controle de versão de implantação, reversão, restauração de pacote, automação).

Con de implantação de gito local é:

- Alguns dados de conhecimento do sistema SCM respectivo obrigatório.
- Não há soluções turn-key para implantação contínua. 

###<a name="vsts"></a>Como implantar de gito local
No [Portal do Azure](https://portal.azure.com), você pode configurar a implantação gito local.

* [Implantação local gito Azure serviço de aplicativo](app-service-deploy-local-git.md). 
* [Publicando Web Apps de qualquer repo gito/hg](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html).  

## <a name="deploy-using-an-ide"></a>Implantar usando um IDE
Se você já estiver usando o [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) com um [SDK Azure](https://azure.microsoft.com/downloads/)ou outros pacotes IDE como [Xcode](https://developer.apple.com/xcode/) [Eclipse](https://www.eclipse.org)e [IntelliJ IDEIA](https://www.jetbrains.com/idea/), você pode implantar no Azure diretamente do seu IDE. Essa opção é ideal para um desenvolvedor individual.

Visual Studio oferece suporte a todos os processos de implantação três (FTP, gito e implantação da Web), dependendo de sua preferência, enquanto outros IDEs pode implantar o serviço de aplicativo se tiverem integração de FTP ou gito (consulte [Visão geral dos processos de implantação](#overview)).

Os profissionais de implantação usando um IDE são:

- Potencialmente minimize as ferramentas para o seu ciclo de vida do aplicativo de ponta a ponta. Desenvolver, depurar, controlar e implantar seu aplicativo em todos Azure sem mover fora do seu IDE. 

Os contras da implantação usando um IDE são:

- Complexidade adicional das ferramentas.
- Ainda requer um sistema de controle de origem para um projeto de equipe.

<a name="vspros"></a>Profissionais adicionais de implantação usando o Visual Studio com o SDK do Azure são:

- Azure SDK torna recursos Azure cidadãos de primeira classe no Visual Studio. Criar, excluir, editar, iniciar e parar de aplicativos, o banco de dados back-end SQL da consulta, live-depurar o aplicativo do Azure e muito mais. 
- Visualização edição de arquivos de código no Azure.
- Depuração de aplicativos de visualização no Azure.
- Integrado explorer Azure.
- Implantação somente comparação. 

###<a name="vs"></a>Como implantar diretamente do Visual Studio

* [Introdução ao Azure e ASP.NET](web-sites-dotnet-get-started.md). Como criar e implantar um projeto de web do ASP.NET MVC simple usando o Visual Studio e implantação da Web.
* [Como implantar o Azure WebJobs usando o Visual Studio](websites-dotnet-deploy-webjobs.md). Como configurar projetos de aplicativo de Console para que eles implantarem como WebJobs.  
* [Implantar um aplicativo de seguro ASP.NET MVC 5 com associação, OAuth e banco de dados SQL para aplicativos Web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Como criar e implantar um projeto de web do ASP.NET MVC com um banco de dados do SQL, usando o Visual Studio, implantação da Web e entidade Framework código primeiro migrações.
* [Implantação de Web do ASP.NET usando o Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Uma série de tutoriais 12-parte que abrange uma gama completa de tarefas de implantação que as outras nessa lista. Alguns recursos de implantação do Azure foram adicionados desde o tutorial foi escrito, mas anotações adicionadas posteriormente explicam o que está faltando.
* [Implantando um site da Web ASP.NET no Azure no Visual Studio 2012 de um repositório de gito diretamente](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Explica como implantar um projeto de web do ASP.NET no Visual Studio, usando o gito plug-in para confirmar o código gito e Azure conectando ao repositório gito. Iniciando no Visual Studio 2013, o suporte de gito é interno e não requer a instalação de um plug-in.

###<a name="aztk"></a>Como implantar usando os kits de ferramentas do Azure para Eclipse e IntelliJ IDEIA

Microsoft possibilita implantar Web Apps para o Azure diretamente do Eclipse e IntelliJ por meio do [Kit de ferramentas do Azure para Eclipse](../azure-toolkit-for-eclipse.md) e o [Kit de ferramentas do Azure para IntelliJ](../azure-toolkit-for-intellij.md). Os seguintes tutoriais ilustram as etapas envolvidas na implantação simples um "" Olá Web App no Azure usando qualquer um dos IDE:

*  [Crie um aplicativo de Web Olá mundo do Azure no Eclipse](./app-service-web-eclipse-create-hello-world-web-app.md). Este tutorial mostra como usar o Kit de ferramentas do Azure para Eclipse para criar e implantar um Hello World Web App para o Azure.
*  [Crie um aplicativo de Web Olá mundo do Azure em IntelliJ](./app-service-web-intellij-create-hello-world-web-app.md). Este tutorial mostra como usar o Kit de ferramentas do Azure para IntelliJ para criar e implantar um Hello World Web App para o Azure.


## <a name="automate"></a>Automatizar a implantação usando ferramentas de linha de comando

* [Automatizar a implantação com MSBuild](#msbuild)
* [Copiar arquivos com ferramentas FTP e scripts](#ftp)
* [Automatizar a implantação com o Windows PowerShell](#powershell)
* [Automatizar a implantação com a API de gerenciamento do .NET](#api)
* [Implantar do Azure Interface de linha (comando Azure)](#cli)
* [Implantar da linha de comando de implantação da Web](#webdeploy)
* [Usando Scripts em lotes de FTP](http://support.microsoft.com/kb/96269).
 
Outra opção de implantação é usar um serviço baseado em nuvem como [Polvo implantar](http://en.wikipedia.org/wiki/Octopus_Deploy). Para obter mais informações, consulte [implantar ASP.NET applications para Sites do Azure](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites).

###<a name="msbuild"></a>Automatizar a implantação com MSBuild

Se você usa o [IDE Visual Studio](#vs) para desenvolvimento, você pode usar [MSBuild](http://msbuildbook.com/) para automatizar a qualquer coisa que você pode fazer em seu IDE. Você pode configurar MSBuild para usar a [Implantação da Web](#webdeploy) ou [FTP/FTPS](#ftp) para copiar arquivos. Implantar Web também pode automatizar muitas outras implantação tarefas relacionadas, como implantando bancos de dados.

Para obter mais informações sobre a implantação de linha de comando usando MSBuild, consulte os seguintes recursos:

* [Implantação de Web do ASP.NET usando o Visual Studio: implantação de linha de comando](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Décimo uma série de tutoriais sobre implantação do Azure usando o Visual Studio. Mostra como usar a linha de comando para implantar após configurar perfis de publicação no Visual Studio.
* [Dentro do mecanismo de compilação Microsoft: usando MSBuild e Team Foundation Build](http://msbuildbook.com/). Livro de cópia impressa que inclua capítulos sobre como usar o MSBuild para implantação.

###<a name="powershell"></a>Automatizar a implantação com o Windows PowerShell

Você pode executar MSBuild ou FTP funções de implantação do [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx). Se você fizer isso, você também pode usar um conjunto de cmdlets do Windows PowerShell que facilitam a API de gerenciamento do restante do Azure chamar.

Para obter mais informações, consulte os seguintes recursos:

* [Implantar um aplicativo web vinculado a um repositório GitHub](app-service-web-arm-from-github-provision.md)
* [Provisionar um aplicativo web com um banco de dados do SQL](app-service-web-arm-with-sql-database-provision.md)
* [Provisionar e implantar microservices previsíveis no Azure](app-service-deploy-complex-application-predictably.md)
* [Criando aplicativos de nuvem do mundo Real com o Azure - automatizar tudo](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Capítulo de livro eletrônico que explica como o aplicativo de exemplo mostrado no livro eletrônico usa scripts do Windows PowerShell para criar um ambiente de teste Azure e implantar a ele. Consulte a seção de [recursos](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) para obter links para documentação adicional do PowerShell do Azure.
* [Usando Scripts do Windows PowerShell para publicar em ambientes de teste e desenvolvimento](../vs-azure-tools-publishing-using-powershell-scripts.md). Como usar o Windows PowerShell implantação scripts que Visual Studio gera.

###<a name="api"></a>Automatizar a implantação com a API de gerenciamento do .NET

Você pode escrever código c# para executar funções MSBuild ou FTP para implantação. Se você fizer isso, você pode acessar o gerenciamento de Azure API REST para executar funções de gerenciamento de site.

Para obter mais informações, consulte o seguinte recurso:

* [Automatizar tudo com as bibliotecas de gerenciamento do Azure e .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Introdução à API de gerenciamento do .NET e links para a documentação mais.

###<a name="cli"></a>Implantar do Azure Interface de linha (comando Azure)

Você pode usar a linha de comando em máquinas Windows, Mac ou Linux implantar usando FTP. Se você fizer isso, você também pode acessar a API utilizando a CLI Azure de gerenciamento do restante do Azure.

Para obter mais informações, consulte o seguinte recurso:

* [Ferramentas de linha de comando do azure](/downloads/#cmd-line-tools). Página do portal em Azure.com para obter informações de ferramenta de linha de comando.

###<a name="webdeploy"></a>Implantar da linha de comando de implantação da Web

[Implantação da Web](http://www.iis.net/downloads/microsoft/web-deploy) é o software da Microsoft para implantação do IIS que não apenas fornece recursos de sincronização de arquivos inteligente, mas também pode executar ou coordenar muitas outras implantação tarefas relacionadas que não podem ser automatizadas ao usar FTP. Por exemplo, a implantação da Web pode implantar um novo banco de dados ou atualizações de banco de dados junto com seu aplicativo web. Implantar Web também pode minimizar o tempo necessário para atualizar um site existente, desde que ele inteligente pode copiar somente os arquivos alterados. Microsoft Visual Studio e Team Foundation Server tem suporte para implantação internos da Web, mas você também pode usar implantação da Web diretamente da linha de comando para automatizar a implantação. Comandos de implantação da Web são muito poderosos, mas a curva de aprendizado pode ser acentuada.

Para obter mais informações, consulte o seguinte recurso:

* [Aplicativos Web simples: implantação](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog por David Ebbo sobre uma ferramenta que ele escreveu para tornar mais fácil usar a implantação da Web.
* [Ferramenta de implantação da web](http://technet.microsoft.com/library/dd568996). Documentação oficial no site da Microsoft TechNet. Data mas ainda um bom lugar para começar.
* [Usando Web implantar](http://www.iis.net/learn/publish/using-web-deploy). Documentação oficial no site da Microsoft IIS.NET. Também data mas um bom lugar para começar.
* [Implantação de Web do ASP.NET usando o Visual Studio: implantação de linha de comando](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild é o mecanismo de compilação usado pelo Visual Studio e também podem ser usado na linha de comando para implantar aplicativos da web para aplicativos Web. Este tutorial faz parte de uma série que é principalmente sobre a implantação do Visual Studio.

##<a name="nextsteps"></a>Próximas etapas

Em alguns cenários talvez você queira ser capaz de alternar facilmente trocadas entre um teste e uma versão de produção do aplicativo. Para obter mais informações, consulte [Implantação testado nos aplicativos Web](web-sites-staged-publishing.md).

Ter um plano de backup e restauração no lugar é uma parte importante de qualquer fluxo de trabalho de implantação. Para obter informações sobre o serviço de aplicativo de backup e restaurar recurso, consulte [Backups de aplicativos Web](web-sites-backup.md).  

Para obter informações sobre como usar o controle de acesso baseado em função do Azure para gerenciar o acesso a implantação do serviço de aplicativo, consulte [RBAC e publicação de aplicativo da Web](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/).


 
