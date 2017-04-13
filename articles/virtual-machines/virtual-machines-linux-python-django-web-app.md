<properties 
    pageTitle="Python web app com Django no Linux | Microsoft Azure" 
    description="Saiba como hospedar um aplicativo web baseado em Django no Azure usando um computador virtual Linux." 
    services="virtual-machines-linux" 
    documentationCenter="python" 
    authors="huguesv" 
    manager="wpickett" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="web" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="11/17/2015" 
    ms.author="huvalo"/>
    
# <a name="django-hello-world-web-application-on-a-linux-vm"></a>Django aplicativo Olá, mundo da web em uma VM Linux

> [AZURE.SELECTOR]
- [Windows](virtual-machines-windows-classic-python-django-web-app.md)
- [Mac/Linux](virtual-machines-linux-python-django-web-app.md)

<br>

Este tutorial descreve como hospedar um site com base em Django no Microsoft Azure usando um computador virtual Linux. Este tutorial supõe que você tenha sem experiência anterior usando o Azure. Após concluir este guia, você terá um aplicativo baseado em Django para cima e em execução na nuvem.

Você aprenderá como:

* Configure uma máquina virtual Azure ao host Django. Enquanto este tutorial explica como fazer isso em **Linux**, a mesma também poderia ser feita com uma máquina de virtual do Windows Server hospedado no Azure. 
* Crie um novo aplicativo de Django do Linux.

Seguindo este tutorial, você criará um aplicativo de web Hello World simples. O aplicativo será hospedado em uma máquina virtual Azure.

Uma captura de tela da aplicação concluída está abaixo:

![Uma janela do navegador exibindo a página do mundo de saudação no Azure](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Criar e configurar uma máquina virtual Azure ao host Django

1. Siga as instruções fornecido [aqui](virtual-machines-linux-quick-create-portal.md) para criar uma máquina virtual Azure da distribuição *Ubuntu servidor 14.04 LTS* .  Se preferir, você pode escolher a autenticação de senha em vez de chave pública SSH.

1. Editar o grupo de segurança de rede para permitir o tráfego de http de entrada para a porta 80 usando as instruções [aqui](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).

1. Por padrão, a sua nova máquina virtual não tem um nome de domínio totalmente qualificado (FQDN).  Você pode criar uma seguindo as instruções [aqui](virtual-machines-linux-portal-create-fqdn.md).  Esta etapa é opcional para concluir este tutorial.

## <a id="setup"> </a>Configurando o ambiente de desenvolvimento

**Observação:** Se você precisa instalar Python ou gostaria de usar as bibliotecas de cliente, consulte o [Guia de instalação do Python](../python-how-to-install.md).

A máquina virtual Linux de Ubuntu já vem com 2.7 Python pré-instalado, mas ela não tem Apache ou django instalado.  Siga estas etapas para conectar-se a sua máquina virtual e instalar Apache e django.

1.  Inicie uma nova janela de **Terminal** .
    
1.  Digite o seguinte comando para conectar-se para a máquina virtual do Azure.  Se você não criar um FQDN, você pode se conectar usando o endereço IP público exibido na máquina virtual resumo no portal de clássico do Azure.

        $ ssh yourusername@yourVmUrl

1.  Digite os seguintes comandos para instalar o django:

        $ sudo apt-get install python-setuptools python-pip
        $ sudo pip install django

1.  Insira o seguinte comando para instalar o Apache com mod-wsgi:

        $ sudo apt-get install apache2 libapache2-mod-wsgi


## <a name="creating-a-new-django-application"></a>Criando um novo aplicativo de Django

1.  Abra a janela de **Terminal** que é usado na seção anterior para ssh em sua máquina virtual.
    
1.  Insira os seguintes comandos para criar um novo projeto de Django:

        $ cd /var/www
        $ sudo django-admin.py startproject helloworld

    O script **django admin.py** gera uma estrutura básica para sites baseada em Django:
    -   **HelloWorld/Manage.py** ajuda você a começar a hospedagem e parar hospedando seu site com base em Django
    -   **HelloWorld/HelloWorld/Settings.py** contém configurações de Django para seu aplicativo.
    -   **HelloWorld/HelloWorld/URLs.py** contém o código de mapeamento entre cada url e seu modo de exibição.

1.  Crie um novo arquivo chamado **views.py** no diretório **/var/www/helloworld/helloworld** . Isto irá conter o modo de exibição que processa a página "Olá". Inicie o editor e insira o seguinte:
        
        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

1.  Agora, substitua o conteúdo do arquivo **urls.py** com o seguinte:

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )


## <a name="setting-up-apache"></a>Configurando Apache

1.  Crie um de arquivo de configuração de host virtual Apache **/etc/apache2/sites-available/helloworld.conf**. Defina o conteúdo para o seguinte e substitua *yourVmName* com o nome real da máquina que você estiver usando (por exemplo *pyubuntu*).

        <VirtualHost *:80>
        ServerName yourVmName
        </VirtualHost>
        WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
        WSGIPythonPath /var/www/helloworld

1.  Habilite o site com o seguinte comando:

        $ sudo a2ensite helloworld

1.  Reinicie Apache com o seguinte comando:

        $ sudo service apache2 reload

1.  Por fim, carregue a página da web no seu navegador:

    ![Uma janela do navegador exibindo a página do mundo de saudação no Azure](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)


## <a name="shutting-down-your-azure-virtual-machine"></a>Desligando sua máquina virtual Azure

Quando você terminar com este tutorial, desligamento e/ou remover sua máquina virtual Azure recém-criado para liberar recursos para outros tutoriais e evitar o uso Azure cobrado.
