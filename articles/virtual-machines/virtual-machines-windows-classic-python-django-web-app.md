<properties
    pageTitle="Python web app com Django | Microsoft Azure"
    description="Este tutorial ensina a hospedar um site com base em Django no Azure usando uma máquina virtual do Windows Server 2012 R2 análise usando o modelo clássico de implantação."
    services="virtual-machines-windows"
    documentationCenter="python"
    authors="huguesv"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>


<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="web" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="08/04/2015" 
    ms.author="huvalo"/>


# <a name="django-hello-world-web-application-on-a-windows-server-vm"></a>Django aplicativo Olá, mundo da web em uma máquina de virtual do Windows Server

> [AZURE.SELECTOR]
- [Windows](virtual-machines-windows-classic-python-django-web-app.md)
- [Mac/Linux](virtual-machines-linux-python-django-web-app.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

Este tutorial descreve como hospedar um site com base em Django no Microsoft Azure um Windows Server na máquina virtual. Este tutorial supõe que você tenha sem experiência anterior usando o Azure. Depois de concluir este tutorial, você terá um aplicativo baseado em Django para cima e em execução na nuvem.

Você aprenderá como:

* Configure uma máquina virtual Azure ao host Django. Enquanto este tutorial explica como fazer isso no Windows Server, a mesma também poderia ser feita com uma máquina virtual hospedado no Azure Linux.
* Crie um novo aplicativo de Django do Windows.

Seguindo este tutorial, você criará um aplicativo de web Hello World simples. O aplicativo será hospedado em uma máquina virtual Azure.

Uma captura de tela da aplicação concluída aparece próxima.

![Uma janela do navegador exibindo a página do mundo de saudação no Azure][1]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Criar e configurar uma máquina virtual Azure ao host Django

1. Siga as instruções fornecido [aqui](virtual-machines-windows-classic-tutorial.md) para criar uma máquina virtual Azure da distribuição do Windows Server 2012 R2 data center.

1. Instrua Azure para direcionar o tráfego de 80 porta da web à porta 80 na máquina virtual:
 - Navegue até sua máquina virtual recém-criado no portal de clássico do Azure e clique na guia **pontos de EXTREMIDADE** .
 - Clique no botão **Adicionar** na parte inferior da tela.
    ![Adicionar ponto de extremidade](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-addendpoint.png)

 - Abrir o protocolo **TCP** é **público porta 80** como **particular porta 80**.
![][port80]
1. Na guia do **painel** , clique em **conectar-se** para usar a **Área de trabalho remota** para fazer logon remotamente na máquina virtual Azure recém-criado.  

**Observação importante:** Todas as instruções abaixo pressupõem que você fez na máquina virtual corretamente e são emissão comandos lá em vez de sua máquina local.

## <a id="setup"> </a>Instalando Python, Django, WFastCGI

**Observação:** Para baixar usando o Internet Explorer, talvez você precise configurar definições de IE ESC (início/administrativa ferramentas/servidor Manager/Local Server, clique em **Configuração de segurança aprimorada do IE**, defina para desativado).

1. Instale as últimas 2.7 Python ou 3.4 de [python.org][].
1. Instale os pacotes wfastcgi e django usando pip.

    Para Python 2.7, use o comando a seguir.

        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django

    Para Python 3.4, use o comando a seguir.

        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="installing-iis-with-fastcgi"></a>Instalando o IIS com FastCGI

1. Instale o IIS com suporte a FastCGI.  Isso pode levar alguns minutos para executar.

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="creating-a-new-django-application"></a>Criando um novo aplicativo de Django

1.  Do *C:\inetpub\wwwroot*, digite o seguinte comando para criar um novo projeto de Django:

    Para Python 2.7, use o comando a seguir.

        C:\Python27\Scripts\django-admin.exe startproject helloworld

    Para Python 3.4, use o comando a seguir.

        C:\Python34\Scripts\django-admin.exe startproject helloworld

    ![O resultado do comando New-AzureService](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-cmd-new-azure-service.png)

1.  O comando **django-administrador** gera uma estrutura básica para sites baseada em Django:

  -   **helloworld\manage.py** ajuda você a começar a hospedagem e parar hospedando seu site com base em Django
  -   **helloworld\helloworld\settings.py** contém configurações de Django para seu aplicativo.
  -   **helloworld\helloworld\urls.py** contém o código de mapeamento entre cada url e seu modo de exibição.

1.  Crie um novo arquivo chamado **views.py** no diretório *C:\inetpub\wwwroot\helloworld\helloworld* . Isto irá conter o modo de exibição que processa a página "Olá". Inicie o editor e insira o seguinte:

        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

1.  Substitua o conteúdo do arquivo urls.py com o seguinte.

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )

## <a name="configuring-iis"></a>Configurando o IIS

1. Desbloqueie a seção de manipuladores na applicationHost global.  Isso permitirá o uso de manipulador de python na Web. config.

        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers

1. Habilite WFastCGI.  Isso adicionará um aplicativo para o applicationHost global que se refere a seu intérprete de Python executável e o script wfastcgi.py.

    Python 2.7:

        c:\python27\scripts\wfastcgi-enable

    Python 3.4:

        c:\python34\scripts\wfastcgi-enable

1. Crie um arquivo de Web. config no *C:\inetpub\wwwroot\helloworld*.  O valor da `scriptProcessor` atributo deve corresponder a saída de etapa anterior.  Consulte a página de [wfastcgi][] em pypi para mais configurações wfastcgi ativado.

    Python 2.7:

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

    Python 3.4:

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

1. Atualize a localização do IIS Site da Web padrão para apontar para a pasta do projeto django.

        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"

1. Por fim, carregue a página da web no seu navegador.

![Uma janela do navegador exibindo a página do mundo de saudação no Azure][1]


## <a name="shutting-down-your-azure-virtual-machine"></a>Desligando sua máquina virtual Azure

Quando você terminar este tutorial, desligar e/ou remover sua máquina virtual Azure recém-criado para liberar recursos para outros tutoriais e evitar o uso Azure cobrado.

[1]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi
