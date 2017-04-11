<properties
    pageTitle="Como depurar um aplicativo da web Node no serviço de aplicativo do Azure"
    description="Saiba como depurar um aplicativo da web Node no serviço de aplicativo do Azure."
    tags="azure-portal"
    services="app-service\web"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-debug-a-nodejs-web-app-in-azure-app-service"></a>Como depurar um aplicativo da web Node no serviço de aplicativo do Azure

Azure fornece diagnóstico interno para ajudar na depuração de aplicativos de Node hospedados nos aplicativos Web do [Serviço de aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) . Neste artigo, você aprenderá como habilitar o log de stdout e stderr, exibir informações de erro no navegador e como baixar e exibir arquivos de log.

Diagnóstico para aplicativos de Node hospedados no Azure é fornecido pelo [IISNode]. Embora este artigo descreve as configurações mais comuns para reunir informações de diagnóstico, ele não fornece uma referência completa para trabalhar com IISNode. Para obter mais informações sobre como trabalhar com IISNode, consulte o [Leiame de IISNode] no GitHub.

<a id="enablelogging"></a>
## <a name="enable-logging"></a>Habilitar o log

Por padrão, um serviço de aplicativo web app captura apenas as informações de diagnóstico sobre implantações, como quando você implantar um aplicativo web usando gito. Esta informação é útil se você estiver tendo problemas durante a implantação, como uma falha ao instalar um módulo referenciado em **package.json**, ou se você estiver usando um script de implantação personalizada.

Para habilitar o log de fluxos de stdout e stderr, você deve criar um arquivo de **IISNode.yml** na raiz do seu aplicativo node e adicione o seguinte:

    loggingEnabled: true

Isso permite que o log de stderr e stdout do seu aplicativo Node.

O arquivo de **IISNode.yml** também pode ser usado para controlar se erros amigáveis ou desenvolvedor são retornados ao navegador quando ocorre uma falha. Para habilitar erros de desenvolvedor, adicione a seguinte linha ao arquivo **IISNode.yml** :

    devErrorsEnabled: true

Quando essa opção estiver ativada, IISNode retornará o último 64K de informações enviadas para stderr em vez de um erro amigável, como "Ocorreu um erro interno do servidor".

> [AZURE.NOTE] Enquanto devErrorsEnabled é útil quando diagnosticar problemas durante o desenvolvimento, ativá-lo em um ambiente de produção pode resultar em erros de desenvolvimento está sendo enviados para os usuários finais.

Se o arquivo de **IISNode.yml** ainda não existir dentro de seu aplicativo, você deverá reiniciar seu aplicativo web após publicação o aplicativo atualizado. Se você está alterando simplesmente configurações em um arquivo de **IISNode.yml** existente que tenha sido publicado anteriormente, não é necessário reiniciar.

> [AZURE.NOTE] Se o seu aplicativo web foi criado usando as ferramentas de linha de comando do Azure ou Cmdlets do PowerShell do Azure, um arquivo de **IISNode.yml** padrão será criado automaticamente.

Para reiniciar o aplicativo da web, selecione o aplicativo da web no [Portal do Azure](https://portal.azure.com)e clique em botão **Reiniciar** :

![Reinicie o botão][restart-button]

Se as ferramentas de linha de comando do Azure estiverem instaladas no seu ambiente de desenvolvimento, você pode usar o seguinte comando para reiniciar o aplicativo web:

    azure site restart [sitename]

> [AZURE.NOTE] Enquanto loggingEnabled e devErrorsEnabled são os comumente usados com mais opções de configuração de IISNode.yml para capturar informações de diagnóstico, IISNode.yml pode ser usado para configurar uma variedade de opções para seu ambiente de hospedagem. Para obter uma lista completa das opções de configuração, consulte o arquivo [iisnode_schema.xml](https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml) .

<a id="viewlogs"></a>
## <a name="accessing-logs"></a>Acesse os logs

Logs de diagnóstico podem ser acessados de três maneiras; Usando o arquivo Transfer Protocol (FTP), baixando um arquivo Zip, ou como um live atualizado fluxo do log (também conhecido como um laço). Baixar o arquivo Zip dos arquivos de log ou visualização do fluxo ao vivo exigem as ferramentas de linha de comando do Azure. Elas podem ser instaladas usando o seguinte comando:

    npm install azure-cli -g

Depois de instalado, as ferramentas podem ser acessadas usando o comando 'azure'. As ferramentas de linha de comando primeiro devem ser configuradas para usar sua assinatura do Azure. Para obter informações sobre como realizar essa tarefa, consulte o **como baixar e importar configurações de publicação** seção do artigo [como usar o Azure de linha de comando ferramentas](../xplat-cli-connect.md) .

###<a name="ftp"></a>FTP

Para acessar as informações de diagnóstico por FTP, visite o [Portal do Azure](https://portal.azure.com), selecione seu aplicativo web e selecione o **Painel de controle**. Na seção **links rápidos** , os links de **FTP LOGS de diagnóstico** e **Os LOGS de diagnóstico FTPS** fornecem acesso aos logs usando o protocolo FTP.

> [AZURE.NOTE] Se você já não tiver configurado o nome de usuário e senha para FTP ou implantação, você pode fazer isso a partir da página de gerenciamento de **início rápido** selecionando **Configurar credenciais de implantação**.

A URL de FTP retornado no painel é para o diretório de **arquivos de log** , que irá conter os seguintes subdiretórios:

* [Método de implantação](web-sites-deploy.md) - se você usar um método de implantação como gito, um diretório de mesmo nome será criado e conterá informações relacionadas a implantações.

* nodejs - informações de Stdout e stderr capturadas de todas as instâncias do seu aplicativo (quando loggingEnabled for true.)

###<a name="zip-archive"></a>Arquivo zip

Para baixar um arquivo Zip de logs de diagnóstico, use o seguinte comando das ferramentas de linha de comando do Azure:

    azure site log download [sitename]

Isso irá baixar um **diagnostics.zip** na pasta atual. Este arquivo contém a estrutura de diretório a seguir:

* implantações - um log de informações sobre implantações de seu aplicativo

* Arquivos de log

    * [Método de implantação](web-sites-deploy.md) - se você usar um método de implantação como gito, um diretório de mesmo nome será criado e conterá informações relacionadas a implantações.

    * nodejs - informações de Stdout e stderr capturadas de todas as instâncias do seu aplicativo (quando loggingEnabled for true.)

###<a name="live-stream-tail"></a>Fluxo ao vivo (cauda)

Para exibir um fluxo de informações de log de diagnóstico ao vivo, use o seguinte comando das ferramentas de linha de comando do Azure:

    azure site log tail [sitename]

Isso retornará um fluxo de eventos de log que são atualizados conforme eles ocorrem no servidor. Este fluxo vai retornar informações sobre a implantação, bem como informações de stdout e stderr (quando loggingEnabled for true.)

<a id="nextsteps"></a>
## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como ativar e acessar as informações de diagnóstico do Azure. Enquanto essas informações serão útil em problemas de compreensão que ocorrem com seu aplicativo, ele pode apontar para um problema com um módulo que você estiver usando ou que a versão do Node usado pelo aplicativo de serviço Web Apps é diferente daquela usada no seu ambiente de implantação.

Para obter informações em trabalhar com módulos no Azure, consulte [Usando o Node módulos com aplicativos do Azure](../nodejs-use-node-modules-azure-apps.md).

Para obter informações sobre como especificar uma versão de Node para seu aplicativo, consulte [especificando uma versão Node em um aplicativo do Azure].

Para obter mais informações, consulte também o [Node Developer Center](/develop/nodejs/).

## <a name="whats-changed"></a>O que mudou
* Para um guia para a alteração de sites para o serviço de aplicativo consulte: [o serviço de aplicativo do Azure e seu impacto sobre serviços existentes do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

[IISNode]: https://github.com/tjanczuk/iisnode
[Leiame do IISNode]: https://github.com/tjanczuk/iisnode#readme
[How to Use The Azure Command-Line Interface]: ../xplat-cli-install.md
[Using Node.js Modules with Azure Applications]: ../nodejs-use-node-modules-azure-apps.md
[Especificando uma versão Node em um aplicativo do Azure]: ../nodejs-specify-node-version-azure-apps.md

[restart-button]: ./media/web-sites-nodejs-debug/restartbutton.png
 
