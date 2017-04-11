<properties
    pageTitle="O que é o SDK do .NET Azure"
    description="Saiba o que está incluído no SDK do .NET Azure."
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor="mollybos"
    services=""/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="rachelap"/>

# <a name="what-is-the-azure-sdk-for-net"></a>O que é o SDK do Azure para .NET?

## <a name="overview"></a>Visão geral

O SDK do Azure para .NET é um conjunto de ferramentas do Visual Studio, ferramentas de linha de comando, binários de runtime e bibliotecas de cliente que ajudam você desenvolvem, testar e implantar aplicativos que são executadas no Azure. Este artigo detalha o que você obtém quando você instala o SDK. Você pode baixar o SDK na [página Downloads do Azure](https://azure.microsoft.com/downloads/).

O SDK do Azure para .NET também consiste em [bibliotecas de cliente para consumir serviços Azure](http://go.microsoft.com/fwlink/?LinkId=510472). Essas bibliotecas são instaladas separadamente usando NuGet.

##<a id="included"></a>O que está incluído no SDK do Azure para .NET

O SDK do Azure para .NET instala os seguintes produtos:

- [Edição de comunidade do Visual Studio de 2015](#vwd)
- [Emulador de armazenamento do Microsoft Azure](#stgemulator)
- [Ferramentas de armazenamento do Microsoft Azure](#stgtools)
- [Ferramentas de criação do Microsoft Azure](#auth)
- [Emulador do Microsoft Azure](#emulator)
- [Ferramentas de HDInsight para Visual Studio e Microsoft seção Driver ODBC](#hdinsight)
- [Bibliotecas do Microsoft Azure para .NET](#libraries)
- [SDK do aplicativo móvel do Windows Azure](#mobile)
- [PowerShell do Microsoft Azure](#ps)
- [Ferramentas do Microsoft Azure para Microsoft Visual Studio](#tools)
- [Microsoft ASP.NET e ferramentas da Web para Visual Studio](#wte)
- [Ferramentas do Microsoft Azure dados Lucerne para Visual Studio](#datalake)

###<a id="vwd"></a>Edição de comunidade do Visual Studio de 2015

Se você não tiver o Visual Studio no seu computador, o SDK irá instalar [Visual Studio comunidade Edition 2015](https://www.visualstudio.com/products/visual-studio-community-vs).

###<a id="stgemulator"></a>Emulador de armazenamento do Microsoft Azure

O [Emulador de armazenamento do Azure](http://msdn.microsoft.com/library/hh403989.aspx) usa uma instância do SQL Server e o sistema de arquivo local para simular (filas, tabelas, blobs), o armazenamento do Azure para que você possa testar localmente.

###<a id="stgtools"></a>Ferramentas de armazenamento do Microsoft Azure

Isso instala [AzCopy](http://aka.ms/AzCopy), uma ferramenta de linha de comando que você pode usar para transferir dados dentro e fora de uma conta de armazenamento do Azure.

###<a id="auth"></a>Ferramentas de criação do Microsoft Azure

Isso inclui o seguinte:

* A [ferramenta de linha de comando CSPack](http://msdn.microsoft.com/library/gg432988.aspx) para criar pacotes de implantação.
* a [ferramenta de linha de comando CSEncrypt](http://msdn.microsoft.com/library/hh404001.aspx) para criptografar senhas que são usadas para acessar instâncias de função de serviço de nuvem por meio de uma conexão de área de trabalho remota.
* Binários de runtime que projetos de serviço de nuvem requerem para se comunicar com seu ambiente de tempo de execução e diagnóstico. Esses binários não estão disponíveis nos pacotes do NuGet.

###<a id="emulator"></a>Emulador do Microsoft Azure

O [Azure emulador](http://msdn.microsoft.com/library/dn339018.aspx) simula o ambiente de serviço de nuvem para que você possa testar projetos de serviço de nuvem localmente em seu computador antes de implantá-los no Azure.

###<a id="hdinsight"></a>Ferramentas de HDInsight para Visual Studio e Microsoft seção Driver ODBC

Ferramentas de HDInsight no Explorador de servidor permitem que você navegue bancos de dados de seção e contas de armazenamento vinculado para clusters de HDInsight, criar tabelas e criar e enviar consultas de seção. Para obter mais informações, consulte [Introdução ao uso de ferramentas de Hadoop HDInsight para Visual Studio](hdinsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

###<a id="libraries"></a>Bibliotecas do Microsoft Azure para .NET

Isso inclui o seguinte:

* Pacotes do NuGet para armazenamento do Azure, barramento de serviço e cache que estão armazenados em seu computador para que o Visual Studio pode criar nuvem novos projetos de serviço enquanto offline.
* Um Visual Studio plug-in que permite projetos de [Cache na função](http://msdn.microsoft.com/library/dn386103.aspx) executar localmente no Visual Studio.

###<a id="mobile"></a>SDK do aplicativo móvel do Windows Azure

Ferramentas para trabalhar com [Aplicativos do Azure aplicativo serviço Mobile](app-service-mobile/app-service-mobile-value-prop.md).

###<a id="ps"></a>PowerShell do Microsoft Azure

PowerShell Azure permite que você para [automatizar a criação de ambiente Azure e implantação](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

###<a id="tools"></a>Ferramentas do Microsoft Azure para Microsoft Visual Studio

Isso permite que você trabalhe com recursos Azure, principalmente os serviços de nuvem e máquinas virtuais:

* [Criar, abrir e publicar projetos de serviço de nuvem](cloud-services/cloud-services-dotnet-get-started.md).
* [Criar pacotes de implantação para projetos de serviço de nuvem](http://msdn.microsoft.com/library/ff683672.aspx).
* [Criar máquinas virtuais do Azure durante a criação de novos projetos da web](virtual-machines/virtual-machines-windows-classic-web-app-visual-studio.md).
* [Scripts do PowerShell criar durante a criação de novas máquinas virtuais](http://msdn.microsoft.com/library/dn642480.aspx).
* [Exibir e gerenciar configurações de projeto de serviço de nuvem no windows de propriedades de projeto do Visual Studio](http://msdn.microsoft.com/library/ee405486.aspx).
* Exibir e gerenciar [Serviços de nuvem](http://msdn.microsoft.com/library/ff683675.aspx), [máquinas virtuais](http://msdn.microsoft.com/library/jj131259.aspx)e [Barramento de serviço](http://msdn.microsoft.com/library/jj149828.aspx) no Server Explorer.
* [Executar no modo de depuração remotamente para serviços de nuvem e máquinas virtuais](http://msdn.microsoft.com/library/ff683670.aspx).
* [Automatizar provisionamento de recursos usando projetos de implantação de grupo de recursos do Azure](https://msdn.microsoft.com/library/dn872471.aspx)

###<a id="wte"></a>Ferramentas de serviço de aplicativo do Microsoft Visual Studio

Isso permite que você trabalhar com sites do Azure:

* [Publicar projetos de web para sites do Azure](app-service-web/web-sites-dotnet-get-started.md).
* [Projetos de aplicativo de console publicar para WebJobs do Azure](app-service-web/websites-dotnet-deploy-webjobs.md).
* [Recursos de criar Azure site e banco de dados SQL ao criar um novo projeto web ou ao publicar um projeto da web](app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
* [Scripts de implantação do PowerShell criar durante a criação de novos sites](http://msdn.microsoft.com/library/dn642480.aspx).
* [Gerenciar e solucionar problemas de sites do Azure no Server Explorer](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#sitemanagement).
* [Executar no modo de depuração remotamente para sites e WebJobs](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug).

>[AZURE.NOTE] Não é necessário instalar o SDK do Azure para .NET usar esses recursos; Eles também são incluídos no Visual Studio atualizações.

##<a id="datalake"></a>Ferramentas do Microsoft Azure dados Lucerne para Visual Studio

Para obter mais informações, consulte [Tutorial: desenvolver scripts U-SQL usando ferramentas de Lucerne de dados para o Visual Studio](data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

##<a id="notincluded"></a>O que não está incluído quando você instala o SDK do Azure para .NET

Há algumas coisas que você pode querer para desenvolvimento Azure que não são incluídas quando você instala o SDK. As mais importantes são as seguintes:

* [Bibliotecas de cliente](http://go.microsoft.com/fwlink/?LinkId=510472).

    O SDK do Azure inclui bibliotecas de cliente para consumir serviços Azure, mas não todos eles são instalados quando você instala o SDK. Se seu aplicativo precisar de uma biblioteca de cliente que não instala o SDK, você pode obtê-lo de [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Se o aplicativo usa uma biblioteca de cliente que o SDK instalar, é recomendável atualizá-lo com a versão atual em NuGet.org.

    **Cópias locais de bibliotecas de cliente.** O SDK do Azure para .NET copia para seu computador os pacotes NuGet para algumas bibliotecas de cliente Azure, como armazenamento, barramento de serviço e cache. Essas bibliotecas de cliente são incluídas automaticamente em novos projetos de serviço de nuvem, para que os pacotes NuGet locais habilitar o Visual Studio criar projetos, mesmo se não estiver conectado à Internet. Bibliotecas de cliente geralmente são atualizadas com mais frequência do são lançadas novas versões SDK, para que as bibliotecas de cliente em NuGet.org são geralmente mais atual do que o que você obtém com o SDK.

    **Modelos de projeto que incluem bibliotecas de cliente.** Somente modelos de projeto [Serviço de nuvem do Azure](cloud-services/cloud-services-dotnet-get-started.md) e de serviço de aplicativo do Azure [Aplicativos Mobile](./app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) incluem automaticamente algumas bibliotecas de cliente. Para outras bibliotecas ou outros modelos, instale os [pacotes do NuGet de biblioteca de cliente](http://go.microsoft.com/fwlink/?LinkId=510472) que você precisa.

* [Modelos de projeto de aplicativos do Mobile](./app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

    Modelos de aplicativos móveis estão disponíveis apenas no Visual Studio 2013 atualização 2 e posterior. Eles não estão disponíveis no Visual Studio 2012 ou versões anteriores e não no Visual Studio 2013 atualização 1 ou anterior, mesmo se você instalar o SDK do Azure para .NET.

##<a id="faq"></a>Perguntas frequentes

- [Muitos recursos Azure já estão no Visual Studio. Eu preciso instalar o SDK do Azure para .NET?](#azinvs)
- [Quero uma biblioteca de cliente. É necessário instalar o SDK do Azure para .NET para colocá-lo?](#clientlib)
- [Onde posso encontrar versões mais antigas do SDK do Azure para .NET?](#olderversions)
- [Qual é a política de ciclo de vida de versões do SDK do Azure para .NET?](#lifecycle)
- [Quais versões do sistema operacional de convidado é compatível com a SDK do Azure para .NET?](#guestos)
- [Como desinstalar o SDK do Azure para .NET?](#uninstall)

###<a id="azinvs"></a>Muitos recursos Azure já estão no Visual Studio. Eu preciso instalar o SDK do Azure para .NET?

É recomendável instalar o SDK se você quiser desenvolver para Azure usando as ferramentas mais recentes. Se você prefere preferir não instalar o SDK, você pode fazer isso se as seguintes condições são verdadeiras:

* Você instalou a última [Atualização do Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5).
* Você está desenvolvendo somente para sites do Azure ou serviços de celular, não para os serviços de nuvem ou máquinas virtuais.
* Seu aplicativo não usar o armazenamento, ou usa o armazenamento, mas não é necessário o emulador de armazenamento ou a ferramenta de AzCopy.

###<a id="clientlib"></a>Quero uma biblioteca de cliente. É necessário instalar o SDK do Azure para .NET para colocá-lo?

O SDK instala bibliotecas de cliente somente para poder criar nuvem projetos serviço mesmo se não estiver conectado à Internet. Bibliotecas de cliente atuais estão disponíveis em pacotes do NuGet em [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Para obter mais informações, consulte [o que não está incluído quando você instala o SDK do Azure para .NET](#notincluded) anteriormente neste documento.

###<a id="olderversions"></a>Onde posso encontrar versões mais antigas do SDK do Azure para .NET?

Para versões anteriores, consulte que o [SDK do Azure para .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) página downloads.

###<a id="lifecycle"></a>Qual é a política de ciclo de vida de versões do SDK do Azure para .NET?

Consulte a [política de ciclo de vida de suporte de serviços de nuvem do Microsoft Azure](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq).

###<a id="guestos"></a>Quais versões do sistema operacional de convidado é compatível com a SDK do Azure para .NET?

Consulte [versões de sistemas operacionais de convidado Azure e matriz de compatibilidade SDK](http://msdn.microsoft.com/library/ee924680.aspx).

###<a id="uninstall"></a>Como desinstalar o SDK do Azure para .NET?

Cada item mostrado neste artigo em [que está incluído no SDK do Azure para .NET](#included) é um programa separado na lista de programas instalados no painel de controle do Windows **programas e recursos**.  Não há nenhuma maneira de desinstalá-los como um grupo; Você precisa desinstalar cada programa individualmente.

Quando você tiver o SDK do Azure para .NET já instalado e você instala uma nova versão, não é geralmente necessário desinstalar o antigo. Na maioria dos casos, a instalação de SDK atualiza um programa existente em vez de adicionar um novo e deixando antigo.

No entanto, se você quiser remover não mais-necessário restante de uma versão anterior, desinstale somente os programas que especificar o número da versão mais antigo e desinstalá-los somente se houver o mesmo programa com uma versão mais recente. Por exemplo, após a atualização do 2.5 para 2.6, você poderá ver versões de 2,5 e 2,6 de "Ferramentas do Microsoft Azure para Microsoft Visual Studio 2013", e você pode desinstalar a versão 2.5. Mas você só pode ver a versão 2.5 "Ferramentas de criação de Azure Microsoft" e não seria segura para desinstalar que.

Observe que os números de versão em títulos de programa mostrados em **programas e recursos** podem ser falsos.  Por exemplo, o SDK versão 2.6 inclui "Microsoft Azure Mobile aplicativo SDK v 1.0" Se você instalar o SDK do Visual Studio 2013 e "Microsoft Azure Mobile aplicativo SDK v 2.0" de 2015 do Visual Studio; Nesse caso, a versão não a versão do SDK, mas um indicador de qual versão do Visual Studio o programa aplica-se ao.

Este artigo não lista todos os programas que cada versão anterior do SDK do Azure incluído; Existem outros programas que você pode desinstalar de versões anteriores do SDK, porque versões anteriores do SDK algumas vezes incluíam programas que foram omitidos versões posteriores. Por exemplo, versão 2.5 instala "Azure recurso Gerenciador de ferramentas para Visual Studio" mas que não está na lista deste artigo porque ele não está mais aparece como um programa separado em **programas e recursos**.  Este artigo lista apenas os programas que estão incluídos no SDK do Azure para .NET versão 2.6.

> **Observação:** Alguns dos programas que inclui o SDK também podem ser instalados separadamente em outros contextos e podem ser necessário mesmo se não precisar o SDK completo. O mesmo pode ser verdadeiro dos programas que foram instalados pelo versões anteriores do SDK, mas foram omitidos versões posteriores de SDK. Quando você desinstalar programas, tenha cuidado para evitar remover algo que ainda são necessários em seu computador.



##<a id="versions"></a>Versões

Para ver qual versão atual ou para baixar versões mais antigas, consulte a página de [SDK do Azure para .NET histórico de versão](https://azure.microsoft.com/downloads/archive-net-downloads/) .

##<a id="resources"></a>Recursos

Para baixar o SDK do Azure atual para .NET ou uma biblioteca de cliente, consulte a [página de Downloads do Azure](https://azure.microsoft.com/downloads/).

Para o SDK do Azure para código-fonte .NET, incluindo bibliotecas de cliente, consulte [GitHub.com/Azure](https://github.com/azure/).

Para documentação de referência da biblioteca de cliente Azure, consulte [Referência de .NET do Azure](https://azure.microsoft.com/documentation/api/).
