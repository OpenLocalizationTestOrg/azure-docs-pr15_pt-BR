<properties
    pageTitle="Trabalhando com módulos Node"
    description="Aprenda a trabalhar com Node módulos usando o serviço de aplicativo do Azure ou serviços de nuvem."
    services=""
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>


# <a name="using-nodejs-modules-with-azure-applications"></a>Usando o Node módulos com aplicativos do Azure

Este documento fornece orientação sobre como usar módulos Node com aplicativos hospedados no Azure. Ele fornece orientação sobre garantindo que o aplicativo usa uma versão específica do módulo, bem como usar módulos nativos com o Azure.

Se você já estiver familiarizado com usando Node módulos, arquivos **package.json** e **npm-shrinkwrap.json** , a seguir é um resumo rápido das quais é discutido neste artigo:

* Serviço de aplicativo do Azure entende arquivos **package.json** e **shrinkwrap.json npm** e podem instalar módulos com base nas entradas esses arquivos.
* Serviços de nuvem Azure esperar todos os módulos esteja instalado no ambiente de desenvolvimento e o **nó\_módulos** directory para ser incluídos como parte do pacote de implantação. É possível ativar o suporte para instalar módulos usando **package.json** ou **shrinkwrap.json npm** arquivos em serviços de nuvem, mas isso requer personalização dos scripts padrão usado por projetos de serviço de nuvem. Para um exemplo de como fazer isso, consulte [a execução npm instalar para evitar Implantando módulos de nó de tarefa de inicialização do Azure](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [AZURE.NOTE] Azure máquinas virtuais não são discutidos neste artigo, como a experiência de implantação em uma máquina virtual serão dependente no sistema operacional hospedado pela Máquina Virtual.

##<a name="nodejs-modules"></a>Módulos Node

Módulos são possa ser carregados pacotes de JavaScript que fornecem recursos específicos do aplicativo. Um módulo é geralmente instalado usando a ferramenta de linha de comando **npm** , porém alguns (como o módulo http) são fornecidos como parte do pacote Node core.

Quando módulos são instalados, eles são armazenados na **nó\_módulos** diretório na raiz da sua estrutura de diretório do aplicativo. Cada módulo dentro do **nó\_módulos** directory mantém sua própria **nó\_módulos** diretório que contém todos os módulos que ela depende e isso repete novamente para cada módulo totalmente pressionada cadeia de dependência. Isso permite que cada módulo instalado para ter seus próprios requisitos de versão para os módulos depende, porém ele pode resultar em bastante uma estrutura de diretório grande.

Ao implantar o **nó\_módulos** pasta como parte do seu aplicativo, ele aumentará o tamanho da implantação em comparação ao uso de um arquivo de **package.json** ou **shrinkwrap.json npm** ; No entanto, ela garante que a versão dos módulos usados na produção são semelhantes aos usados em desenvolvimento.

###<a name="native-modules"></a>Módulos nativos

Enquanto a maioria dos módulos são arquivos de JavaScript simplesmente texto sem formatação, alguns módulos são imagens binárias específico da plataforma. Esses módulos são compilados no tempo de instalação, geralmente usando Python e nó gyp. Desde que dependem de serviços de nuvem do Azure a **nó\_módulos** pasta sendo implantada como parte do aplicativo, qualquer nativo módulo incluído como parte dos módulos instalados deverá trabalhar em um serviço de nuvem, desde que ele foi instalado e compilado em um sistema de desenvolvimento do Windows.

Serviço de aplicativo do Azure não dá suporte a todos os módulos nativos e pode falhar em compilar aqueles com pré-requisitos muito específicos. Enquanto alguns módulos populares como MongoDB têm dependências nativas opcionais e trabalho bem sem eles, duas soluções alternativas demonstrou bem-sucedida com quase todos os módulos nativos disponíveis hoje:

* Execute **npm instalar** em um computador Windows que tem pré-requisitos de todos os nativo do módulo instalados. Em seguida, implantar criaram **nó\_módulos** pasta como parte do aplicativo de serviço de aplicativo do Azure.
* Serviço de aplicativo Azure pode ser configurado para executar bash personalizado ou scripts do shell durante a implantação, dando a você a oportunidade de executar comandos personalizados e configurar com precisão a maneira **npm instalar** está sendo executada. Para um vídeo mostrando como fazer isso, consulte [Scripts de implantação de site personalizada com Kudu].

###<a name="using-a-packagejson-file"></a>Usando um arquivo de package.json

O arquivo **package.json** é uma maneira de especificar as dependências de nível superior requer o seu aplicativo para que a plataforma de hospedagem possa instalar as dependências, ao invés de exigir que você incluir o **nó\_pacotes** pasta como parte da implantação. Após o aplicativo ter sido implantado, o comando **npm instalar** é usado para analisar o arquivo **package.json** e instale todas as dependências listadas.

Durante o desenvolvimento, você pode usar o **-Salvar**, **-Salvar-desenvolvimento**, ou **-opcional de salvar** parâmetros ao instalar módulos para adicionar uma entrada para o módulo para o seu arquivo de **package.json** automaticamente. Para obter mais informações, consulte [instalar npm](https://docs.npmjs.com/cli/install).

Um possível problema com o arquivo de **package.json** é que ela só especifica a versão para dependências de nível superior. Cada módulo instalado pode ou não é possível especificar a versão dos módulos depende e então, é possível que você pode acabar com uma cadeia de dependência diferente que o usado em desenvolvimento.

> [AZURE.NOTE]
> Ao implantar o serviço de aplicativo do Azure, se seu arquivo de <b>package.json</b> referencia um módulo nativo você verá um erro semelhante à seguinte quando o aplicativo usando gito de publicação:

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


###<a name="using-a-npm-shrinkwrapjson-file"></a>Usando um arquivo de npm-shrinkwrap.json

O arquivo de **shrinkwrap.json npm** é uma tentativa de resolver as limitações de controle de versão do módulo do arquivo **package.json** . Enquanto o arquivo de **package.json** inclui apenas as versões para os módulos de nível superior, o arquivo de **shrinkwrap.json npm** contém os requisitos de versão para a cadeia de dependência de módulo completo.

Quando seu aplicativo está pronto para produção, você pode bloquear-baixo requisitos de versão e criar um arquivo de **shrinkwrap.json npm** usando o comando **npm shrinkwrap** . Isso usará as versões atualmente instaladas no **nó\_módulos** pasta e gravar esses arquivos para o arquivo **npm-shrinkwrap.json** . Depois que o aplicativo foi implantado o ambiente de hospedagem, o comando **npm instalar** é usado para analisar o arquivo **shrinkwrap.json npm** e instale todas as dependências listadas. Para obter mais informações, consulte [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [AZURE.NOTE]
>Ao implantar o serviço de aplicativo do Azure, se seu arquivo <b>npm shrinkwrap.json</b> referencia um módulo nativo você verá um erro semelhante à seguinte quando o aplicativo usando gito de publicação:

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


##<a name="next-steps"></a>Próximas etapas

Agora que você compreender como usar módulos Node com o Azure, saiba como [especificar a versão Node], [criar e implantar um aplicativo de web Node]e [como usar a Interface de linha do Azure para Mac e Linux].

Para obter mais informações, consulte o [Node Developer Center](/develop/nodejs/).

[especificar a versão Node]: nodejs-specify-node-version-azure-apps.md
[Como usar a Interface de linha do Azure para Mac e Linux]: xplat-cli-install.md
[criar e implantar um aplicativo de web Node]: web-sites-nodejs-develop-deploy-mac.md
[Node.js Web Application with Storage on MongoDB (MongoLab)]: store-mongolab-web-sites-nodejs-store-data-mongodb.md
[Build and deploy a Node.js application to an Azure Cloud Service]: cloud-services-nodejs-develop-deploy-app.md
[Scripts de implantação de site personalizada com Kudu]: /documentation/videos/custom-web-site-deployment-scripts-with-kudu/
