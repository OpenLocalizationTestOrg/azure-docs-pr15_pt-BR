<properties
    pageTitle="Funções de trabalho e da web PHP | Microsoft Azure"
    description="Um guia para a criação de funções de trabalho e da web PHP em um serviço de nuvem Azure e configurando o tempo de execução do PHP."
    services=""
    documentationCenter="php"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

#<a name="how-to-create-php-web-and-worker-roles"></a>Como criar funções de trabalho e da web do PHP

## <a name="overview"></a>Visão geral

Este guia mostrará como criar PHP web ou trabalhador funções em um ambiente de desenvolvimento do Windows, escolha uma versão específica de PHP as versões "internas" disponíveis, alterar a configuração de PHP, habilitar extensões e por fim, implantar para o Azure. Ele também descreve como configurar uma função da web ou de trabalho para usar um tempo de execução PHP (com configuração personalizada e extensões) que você fornecer.

## <a name="what-are-php-web-and-worker-roles"></a>O que são funções de trabalho e da web PHP?

Azure fornece três modelos para execução de aplicativos de computação: serviço de aplicativo do Azure, máquinas virtuais do Azure e os serviços de nuvem do Azure. Todos os modelos de três suportam PHP. Serviços de nuvem, que inclui funções da web e trabalhador, fornece *plataforma como um serviço (PaaS)*. Dentro de um serviço de nuvem, uma função web fornece um servidor de web de serviços de informações da Internet (IIS) dedicado para hospedar aplicativos da web front-end. Uma função de trabalho pode executar tarefas permanentes ou demorada assíncronas, independentemente de interação com o usuário ou entrada.

Para obter mais informações sobre essas opções, consulte [Opções de hospedagem fornecidas pelo Azure de computação](./cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Baixe o SDK do Azure para PHP

O [SDK do Azure para PHP] consiste em vários componentes. Este artigo utilizará dois deles: PowerShell do Azure e os emuladores Azure. Esses dois componentes podem ser instalados via o Microsoft Web Platform Installer. Para obter mais informações, consulte [como instalar e configurar o PowerShell do Azure](powershell-install-configure.md).

## <a name="create-a-cloud-services-project"></a>Criar um projeto de serviços de nuvem

A primeira etapa na criação de uma função de web ou colega PHP é criar um projeto de serviço do Azure. um projeto de serviço do Azure serve como um contêiner lógico para funções da web e trabalhador e contém os arquivos do projeto [definição de serviço (.csdef)] e [configuração do serviço (.cscfg)] .

Para criar um novo projeto de serviço do Azure, execute o Azure PowerShell como administrador e execute o seguinte comando:

    PS C:\>New-AzureServiceProject myProject

Este comando criará uma nova pasta (`myProject`) ao qual você pode adicionar funções da web e de trabalho.

## <a name="add-php-web-or-worker-roles"></a>Adicionar funções de web ou colega PHP

Para adicionar uma função de web PHP a um projeto, execute o seguinte comando no diretório raiz do projeto:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Para uma função de trabalho, use este comando:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [AZURE.NOTE] O `roleName` parâmetro é opcional. Se ele for omitido, o nome da função será gerado automaticamente. A primeira função web criada será `WebRole1`, a segunda será `WebRole2`e assim por diante. A primeira função de trabalho criada será `WorkerRole1`, a segunda será `WorkerRole2`e assim por diante.

## <a name="specify-the-built-in-php-version"></a>Especificar a versão PHP interna

Quando você adiciona uma função de web ou colega PHP a um projeto, arquivos de configuração do projeto são modificados para que PHP será instalado em cada instância da web ou de trabalho do seu aplicativo quando ele for implantado. Para ver a versão do PHP que será instalado por padrão, execute o seguinte comando:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

A saída do comando acima terá aparência semelhante à que é mostrada abaixo. Neste exemplo, o `IsDefault` sinalizador estiver definido como `true` para PHP 5.3.17, indicando que será a versão PHP padrão instalada.

    Runtime Version     PackageUri                      IsDefault
    ------- -------     ----------                      ---------
    Node 0.6.17         http://nodertncu.blob.core...   False
    Node 0.6.20         http://nodertncu.blob.core...   True
    Node 0.8.4          http://nodertncu.blob.core...   False
    IISNode 0.1.21      http://nodertncu.blob.core...   True
    Cache 1.8.0         http://nodertncu.blob.core...   True
    PHP 5.3.17          http://nodertncu.blob.core...   True
    PHP 5.4.0           http://nodertncu.blob.core...   False

Você pode definir a versão de runtime PHP a qualquer uma das versões PHP que estão listadas. Por exemplo, para definir a versão PHP (para uma função com o nome `roleName`) 5.4.0, use o seguinte comando:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [AZURE.NOTE] Versões PHP disponíveis podem mudar no futuro.

## <a name="customize-the-built-in-php-runtime"></a>Personalizar o tempo de execução do PHP interno

Você tem controle total sobre a configuração do runtime PHP que é instalado quando você seguir as etapas acima, incluindo modificação de `php.ini` configurações e a habilitação de extensões.

Para personalizar o tempo de execução do PHP interno, siga estas etapas:

1. Adicionar uma nova pasta, chamada `php`, para o `bin` diretório de sua função web. Para uma função de trabalho, adicione-o ao diretório de raiz da função.
2. No `php` pasta, crie outra pasta chamada `ext`. Colocar qualquer `.dll` arquivos de extensão (por exemplo, `php_mongo.dll`) que você deseja habilitar nesta pasta.
3. Adicionar uma `php.ini` arquivo para o `php` pasta. Habilitar as extensões personalizadas e defina quaisquer diretivas PHP neste arquivo. Por exemplo, se você quiser ativar `display_errors` em e habilitar o `php_mongo.dll` extensão, o conteúdo de sua `php.ini` arquivo seria assim:

        display_errors=On
        extension=php_mongo.dll

> [AZURE.NOTE] Todas as configurações que você não definir explicitamente na `php.ini` arquivo que você fornecer será automaticamente ser definidas para seus valores padrão. No entanto, lembre-se que você pode adicionar uma completa `php.ini` arquivo.

## <a name="use-your-own-php-runtime"></a>Usar seu próprio runtime PHP
Em alguns casos, em vez de selecionar um runtime PHP interno e configurá-lo conforme descrito acima, você talvez queira fornecer seu próprio runtime PHP. Por exemplo, você pode usar o mesmo tempo de execução do PHP em uma função da web ou de trabalho que você usa no seu ambiente de desenvolvimento. Isso facilita garantir que o aplicativo não serão alterados comportamento no seu ambiente de produção.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Configurar uma função de web para usar seu próprio tempo de execução do PHP

Para configurar uma função de web para usar um tempo de execução do PHP que você fornecer, siga estas etapas:

1. Criar um projeto de serviço do Azure e adicione uma função de web PHP conforme descrito anteriormente neste tópico.
2. Criar um `php` pasta no `bin` pasta que está no diretório raiz da sua função web e adicione o tempo de execução do PHP (todos os binários, arquivos de configuração, subpastas, etc.) para o `php` pasta.
3. (OPCIONAL) Se o tempo de execução do PHP usa os [Drivers do Microsoft para PHP para SQL Server][sqlsrv drivers], você precisará configurar sua função web para instalar o [SQL Server Native Client 2012] [ sql native client] quando ela está provisionada. Para fazer isso, adicione o [instalador sqlncli.msi x64] a `bin` pasta no diretório raiz da sua função web. O script de inicialização descrito na próxima etapa silenciosamente executará o instalador quando a função está provisionada. Se o tempo de execução do PHP não usar os Drivers Microsoft para PHP para o SQL Server, você pode remover a seguinte linha do script mostrado na próxima etapa:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Definir uma tarefa de inicialização que configura [Serviços de informações da Internet (IIS)] [ iis.net] usar o tempo de execução do PHP para lidar com solicitações de `.php` páginas. Para fazer isso, abra o `setup_web.cmd` arquivo (no `bin` arquivo da pasta raiz da sua função web) em um editor de texto e substitua o seu conteúdo com o script a seguir:

        @ECHO ON
        cd "%~dp0"

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
        SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"

5. Adicione seus arquivos de aplicativo para diretório raiz da sua função web. Este será o diretório de raiz do servidor web.

6. Publicar seu aplicativo conforme descrito na seção [publicar seu aplicativo](#how-to-publish-your-application) abaixo.

> [AZURE.NOTE] O `download.ps1` script (no `bin` pasta do diretório de raiz da função web) pode ser excluído após seguir as etapas descritas acima para usar seu próprio tempo de execução do PHP.

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Configurar uma função de trabalho para usar seu próprio tempo de execução do PHP

Para configurar uma função de trabalho para usar um tempo de execução do PHP que você fornecer, siga estas etapas:

1. Criar um projeto de serviço do Azure e adicione uma função de trabalho PHP conforme descrito anteriormente neste tópico.
2. Criar um `php` pasta no diretório de raiz da função operador e adicione o tempo de execução do PHP (todos os binários, arquivos de configuração, subpastas, etc.) para o `php` pasta.
3. (OPCIONAL) Se o tempo de execução do PHP usa [Drivers Microsoft para PHP para SQL Server][sqlsrv drivers], você precisará configurar sua função de trabalho para instalar o [SQL Server Native Client 2012] [ sql native client] quando ela está provisionada. Para fazer isso, adicione o [instalador sqlncli.msi x64] ao diretório de raiz da função trabalhador. O script de inicialização descrito na próxima etapa silenciosamente executará o instalador quando a função está provisionada. Se o tempo de execução do PHP não usar os Drivers Microsoft para PHP para o SQL Server, você pode remover a seguinte linha do script mostrado na próxima etapa:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Definir uma tarefa de inicialização que adiciona sua `php.exe` executável a variável de ambiente de caminho da função trabalhador quando a função está provisionada. Para fazer isso, abra o `setup_worker.cmd` arquivo (no diretório de raiz da função trabalhador) em um editor de texto e substitua o seu conteúdo com o script a seguir:

        @echo on

        cd "%~dp0"

        echo Granting permissions for Network Service to the web root directory...
        icacls ..\ /grant "Network Service":(OI)(CI)W
        if %ERRORLEVEL% neq 0 goto error
        echo OK

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        setx Path "%PATH%;%~dp0php" /M

        if %ERRORLEVEL% neq 0 goto error

        echo SUCCESS
        exit /b 0

        :error

        echo FAILED
        exit /b -1

5. Adicione seus arquivos de aplicativo para diretório raiz do seu cargo trabalho.

6. Publicar seu aplicativo conforme descrito na seção [publicar seu aplicativo](#how-to-publish-your-application) abaixo.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Executar o aplicativo nos emuladores de computação e armazenamento

Os emuladores Azure fornecem um ambiente local em que você pode testar seu aplicativo do Azure antes de implantá-lo na nuvem. Há algumas diferenças entre os emuladores e o ambiente do Azure. Para compreender isso melhor, consulte [usar o emulador de armazenamento do Azure para desenvolvimento e teste](./storage/storage-use-emulator.md).

Observe que você deve ter PHP instalado localmente para usar o emulador de computação. O emulador de computação usará sua instalação local do PHP para executar o aplicativo.

Para executar o seu projeto nos emuladores, execute o seguinte comando do diretório raiz do seu projeto:

    PS C:\MyProject> Start-AzureEmulator

Você verá a saída semelhante a esta:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Você pode ver o seu aplicativo em execução no emulador abrindo um navegador da web e navegando até o endereço local mostrado no resultado (`http://127.0.0.1:81` na saída do exemplo acima).

Para interromper os emuladores, execute este comando:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publicar seu aplicativo

Para publicar seu aplicativo, você precisa primeiro importar suas configurações de publicação usando o cmdlet [AzurePublishSettingsFile de importação](https://msdn.microsoft.com/library/azure/dn790370.aspx) . Em seguida, você pode publicar seu aplicativo usando o cmdlet [Publicar-AzureServiceProject](https://msdn.microsoft.com/library/azure/dn495166.aspx) . Para obter informações sobre como entrar no, veja [como instalar e configurar o Azure PowerShell](powershell-install-configure.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte o [PHP Developer Center](/develop/php/).

[SDK do Azure para PHP]: /develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[definição de serviço (.csdef)]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[configuração de serviço (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[installer SQLNCLI.msi x64]: http://go.microsoft.com/fwlink/?LinkID=239648
