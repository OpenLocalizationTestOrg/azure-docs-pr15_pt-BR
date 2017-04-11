<properties 
    pageTitle="Como usar io.js com aplicativos do Azure aplicativo de serviço Web" 
    description="Saiba como usar um aplicativo web no serviço de aplicativo do Azure com io.js." 
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
    ms.author="robmcm" />

# <a name="how-to-use-iojs-with-azure-app-service-web-apps"></a>Como usar io.js com aplicativos do Azure aplicativo de serviço Web

O popular nó bifurcação [io.js] recursos várias diferenças projeto de Node do Joyent, incluindo um modelo de governança mais aberto, um ciclo de lançamento mais rápido e uma adoção mais rápida dos recursos de JavaScript novos e experimentais.

Enquanto o [Serviço de aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps tem várias versões de Node pré-instalado, ele também permite um binário Node fornecido pelo usuário. Este artigo discute dois métodos habilitando o uso de io.js no aplicativo de serviço Web Apps: O uso de um script de implantação estendido, que configura automaticamente o Azure para usar a versão mais recente do io.js disponíveis, bem como o carregamento manual de um io.js binário. 

<a id="deploymentscript"></a>
## <a name="using-a-deployment-script"></a>Usando um Script de implantação

Mediante implantação de um aplicativo de Node, o aplicativo de serviço Web Apps executa um número de comandos pequenos para garantir que o ambiente está configurado corretamente. Usando um script de implantação, esse processo pode ser personalizado para incluir o download e a configuração de io.js.

O [io.js Script de implantação](https://github.com/felixrieseberg/iojs-azure) está disponível no GitHub. Para habilitar io.js em seu aplicativo web, simplesmente copiar **.deployment**, **Deploy** e **IISNode.yml** para a pasta raiz do aplicativo e implantar em aplicativos Web.  

O primeiro arquivo, **.deployment**, instrui Web Apps para executar **Deploy** mediante implantação. Esse script executa todas as etapas usuais para um aplicativo Node, mas também baixa a versão mais recente do io.js. Por fim, **IISNode.yml** configura Web Apps para usar apenas o io.js baixado binário em vez de um binário Node pré-instalados.

> [AZURE.NOTE] Para atualizar o binário io.js usadas, basta reimplantar seu aplicativo - o script serão baixadas uma nova versão do io.js toda vez que o aplicativo for implantado.

<a id="manualinstallation"></a>
## <a name="using-manual-installation"></a>Usando a instalação Manual

A instalação manual de uma versão de io.js personalizado inclui apenas duas etapas. Primeiro, baixe o **win-x64** binário diretamente da [distribuição de io.js]. Obrigatório são dois arquivos - **iojs.exe** e **iojs.lib**. Salve os dois arquivos em uma pasta dentro de seu aplicativo web, por exemplo, em **bin/iojs**.

Para configurar aplicativos da Web para usar **iojs.exe** em vez de uma versão de nó pré-instalada, crie um arquivo de **IISNode.yml** na raiz do seu aplicativo e adicione a seguinte linha.

    nodeProcessCommandLine: "D:\home\site\wwwroot\bin\iojs\iojs.exe"

<a id="nextsteps"></a>
## <a name="next-steps"></a>Próximas etapas

Neste artigo que você aprendeu a usar o io.js com o aplicativo de serviço Web Apps, usando os dois fornecido scripts de implantação instalação bem como manual. 

> [AZURE.NOTE] IO.js está em desenvolvimento pesado e atualizado mais do que Node. Um número de módulos Node pode não funcionar com io.js - por favor, consulte [io.js no GitHub] para solução de problemas.

## <a name="whats-changed"></a>O que mudou
* Para um guia para a alteração de sites para o serviço de aplicativo consulte: [o serviço de aplicativo do Azure e seu impacto sobre serviços existentes do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

[IO.js]: https://iojs.org
[distribuição de IO.js]: https://iojs.org/dist/
[IO.js no GitHub]: https://github.com/iojs/io.js
[io.js Deployment Script]: https://github.com/felixrieseberg/iojs-azure
 