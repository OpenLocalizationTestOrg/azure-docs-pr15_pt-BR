<properties
   pageTitle="Instale o .NET em uma função de serviço de nuvem | Microsoft Azure"
   description="Este artigo descreve como instalar manualmente o .NET framework na Web de serviço de nuvem e funções de trabalho"
   services="cloud-services"
   documentationCenter=".net"
   authors="thraka"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/10/2016"
   ms.author="adegeo"/>

# <a name="install-net-on-a-cloud-service-role"></a>Instale o .NET em uma função de serviço de nuvem 

Este artigo descreve como instalar o .NET framework em funções de trabalho e da Web de serviços de nuvem. Você pode usar essas etapas para instalar o .NET 4.6.1 no Azure convidado SO família 4. As informações mais recentes sobre versões de SO convidado consulte [lançamentos de sistema operacional de convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).

O processo de instalação do .NET em suas funções da web e trabalhador envolve incluindo o pacote de instalação do .NET como parte do seu projeto de nuvem e iniciar o instalador como parte da função tarefas de inicialização.  

## <a name="add-the-net-installer-to-your-project"></a>Adicionar o instalador do .NET ao seu projeto
- Baixar o instalador do web para o .NET framework que você deseja instalar
    - [.NET 4.6.1 web Installer](http://go.microsoft.com/fwlink/?LinkId=671729)
- Para uma função de Web
  1. No **Solution Explorer**, sob nas **funções** do projeto de serviço de nuvem clique com botão direito sua função e selecione **Adicionar > nova pasta**. Crie uma pasta chamada *bin*
  2. Clique com o botão direito na pasta **bin** e selecione **Adicionar > Item existente**. Selecione o instalador do .NET e adicioná-lo para a pasta bin.
- Para uma função de trabalho
  1. Clique com o botão direito em sua função e selecione **Adicionar > Item existente**. Selecione o instalador do .NET e adicioná-lo à função. 

Arquivos adicionados dessa maneira para a pasta de conteúdo de função automaticamente será adicionada ao pacote de serviço de nuvem e implantada em um local consistente na máquina virtual. Repita esse processo para todas as funções de trabalho e da web em seu serviço de nuvem para que todas as funções têm uma cópia do instalador.

> [AZURE.NOTE] Você deve instalar .NET 4.6.1 em sua função de serviço de nuvem mesmo se seu aplicativo destinos .NET 4.6. O sistema operacional convidado do Azure inclui atualizações [3098779](https://support.microsoft.com/kb/3098779) e [3097997](https://support.microsoft.com/kb/3097997). Instalar o .NET 4.6 sobre essas atualizações pode causar problemas durante a execução de seus aplicativos .NET, para que você deve instalar diretamente .NET 4.6.1 em vez de .NET 4.6. Para obter mais informações, consulte [3118750 KB](https://support.microsoft.com/kb/3118750).

![Função conteúdo com arquivos do instalador][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definir tarefas de inicialização para suas funções
Tarefas de inicialização permitem que você execute operações antes de inicia uma função. Instalando o .NET Framework como parte da tarefa inicialização garantirá que o framework está instalado antes de qualquer um dos seu código de aplicativo é executado. Para obter mais informações sobre inicialização tarefas consulte: [Executar tarefas de inicialização no Azure](cloud-services-startup-tasks.md). 

1. Adicione o seguinte ao arquivo *ServiceDefinition.csdef* sob o nó **WebRole** ou **WorkerRole** para todas as funções:
    
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```

    A configuração acima executará o comando do console *cmd* com privilégios de administrador para que ele possa instalar o .NET framework. A configuração também cria uma LocalStorage com o nome *NETFXInstall*. O script de inicialização definirá a pasta temp para usar este recurso de armazenamento local para que o instalador do .NET framework será baixado e instalado deste recurso. É importante definir o tamanho deste recurso para pelo menos 1024MB para garantir que a estrutura será instalado corretamente. Para obter mais informações sobre a inicialização tarefas consulte: [tarefas de inicialização de serviço de nuvem comuns](cloud-services-startup-tasks-common.md) 

2. Criar um arquivo **cmd** e adicioná-la a todas as funções pelo botão direito do mouse na função e selecionando **Adicionar > Item existente...**. Para que todas as funções agora devem ter o arquivo do instalador .NET, bem como os arquivos de cmd.
    
    ![Função conteúdo com todos os arquivos][2]

    > [AZURE.NOTE] Use um editor de texto simples como o bloco de notas para criar o arquivo. Se você usa o Visual Studio para criar um arquivo de texto e, em seguida, renomeá-la 'cmd' o arquivo ainda pode conter uma marca de ordem de Byte UTF-8 e executando a primeira linha do script resultará em um erro. Se você usar Visual Studio para criar o deixe o arquivo adicionar um REM (comentário) para a primeira linha do arquivo para que ele é ignorado quando executar. 

3. Adicione o seguinte script ao arquivo **cmd** :

    ```
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    set netfx="NDP461"
    
    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."
    
    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit
    
    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%
    
    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set "netfxinstallfile=NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp
    
    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp
    
    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp
    
    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"
    
    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
    echo %log% >> %startuptasklog%
    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%
    
    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed
    
    REM ***** Installing .NET *****
    echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
    if %ERRORLEVEL%== 0 goto installed
        echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%   
        if %ERRORLEVEL%== 3010 goto restart
        if %ERRORLEVEL%== 1641 goto restart
        echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
    
    :restart
    echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
    EXIT /B %ERRORLEVEL%
    
    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%
    
    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
    
    :exit
    EXIT /B 0
    ```
        
    O script de instalação verifica se a versão do .NET framework especificado já está instalado na máquina consultando o registro. Se a versão de .NET não está instalada e o .net Web Installer é iniciado. Para ajudar na solução de problemas que o script registrará todas as atividades em um arquivo denominado *startuptasklog-(currentdatetime). txt* armazenado em um local *InstallLogs* .

    > [AZURE.NOTE] O script ainda mostra como instalar .NET 4.5.2 ou 4.6 .NET para continuidade. Não é necessário instalar .NET 4.5.2 manualmente, pois já está disponível no sistema operacional de convidado do Azure. Em vez de instalar .NET 4.6, você deve instalar diretamente .NET 4.6.1 devido a [3118750 KB](https://support.microsoft.com/kb/3118750).
      

## <a name="configure-diagnostics-to-transfer-the-startup-task-logs-to-blob-storage"></a>Configurar diagnósticos para transferir os registros de tarefa de inicialização para armazenamento de blob 
Para simplificar a quaisquer problemas de instalação, você pode configurar o diagnóstico do Azure para transferir quaisquer arquivos de log gerados pelo script de inicialização ou o instalador do .NET ao armazenamento de blob. Com essa abordagem, você pode exibir os logs simplesmente baixar os arquivos de log do armazenamento de blob em vez de ter de área de trabalho remota à função.

Para configurar o diagnóstico abrir o *diagnostics.wadcfgx* e adicione o seguinte sob o nó de **diretórios** : 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Isto configurará diagnóstico do azure para transferir todos os arquivos no diretório de *log* abaixo do recurso de *NETFXInstall* para a conta de armazenamento de diagnóstico no contêiner de blob *netfx instalar* .

## <a name="deploying-your-service"></a>Implantando o seu serviço 
Quando você implanta o seu serviço as tarefas de inicialização serão executado e instalar o .NET framework se ela já não estiver instalada. As funções será no estado ocupado enquanto o framework está instalando e pode até mesmo reiniciar se for necessário para a instalação do framework. 

## <a name="additional-resources"></a>Recursos adicionais

- [Instalando o .NET Framework][]
- [Como: determinar quais versões do .NET Framework estão instalados][]
- [Solucionar problemas de instalações do .NET Framework][]

[Como: determinar quais versões do .NET Framework estão instalados]: https://msdn.microsoft.com/library/hh925568.aspx
[Instalando o .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Solucionar problemas de instalações do .NET Framework]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

 
