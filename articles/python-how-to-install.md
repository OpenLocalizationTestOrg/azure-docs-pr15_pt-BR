<properties
    pageTitle="Instalar o Python e o SDK - Azure"
    description="Saiba como instalar Python e o SDK para usar com o Azure."
    services=""
    documentationCenter="python"
    authors="lmazuel"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="lmazuel"/>

# <a name="installing-python-and-the-sdk"></a>Instalando Python e o SDK

Python é fácil de configurar no Windows e vem pré-instalado em [Bash para Windows](https://msdn.microsoft.com/commandline/wsl/about), Linux e Mac. Este guia orientará e instalação sua máquina prontos para uso com o Azure.

## <a name="whats-in-the-python-azure-sdk"></a>O que há no Python SDK Azure?

O SDK do Azure para Python inclui componentes que permitem que você desenvolver, implantar e gerenciar aplicativos de Python Azure. Especificamente, o SDK do Azure para Python inclui o seguinte:

* **Bibliotecas de gerenciamento**. Essas bibliotecas de classe fornecem uma interface de gerenciamento de recursos do Azure, como contas do armazenamento, máquinas virtuais.

* **Bibliotecas de tempo de execução**. Essas bibliotecas de classe fornecem uma interface para acessar recursos Azure, como barramento de serviço e armazenamento.

## <a name="which-python-and-which-version-to-use"></a>Quais Python e qual versão usar

Há vários tipos de interpretadores Python disponíveis - exemplos incluem:

* CPython - o intérprete de Python padrão e mais comumente usado
* PyPy - implementação alternativa rápida e compatível para CPython
* IronPython - intérprete Python que é executada no CLR do .net
* Jython - intérprete Python que é executado na máquina Virtual Java

**CPython** v2.7 ou v3.3 + PyPy 5.4.0 testado e são suportados para o SDK do Azure Python.

## <a name="where-to-get-python"></a>Onde obter Python?

Há várias maneiras de obter CPython:

* Diretamente da [www.python.org][]
* De uma distribuição respeitável como [www.continuum.io][], [www.enthought.com][] ou [www.activestate.com][]
* Construir de fonte!

A menos que tenha uma necessidade específica, recomendamos as duas primeiras opções.

## <a name="sdk-installation-on-windows-linux-and-macos-client-libraries-only"></a>Instalação de SDK no Windows, Linux e MacOS (somente para bibliotecas de cliente)

Se você já tiver Python instalado, você pode usar pip para instalar um pacote de todas as bibliotecas de cliente no seu ambiente de Python 3.3 + ou 2.7 de Python existente. Isso fará o download os pacotes do [Índice de pacote de Python][] (PyPI).

Talvez seja necessário direitos de administrador:

- Linux e MacOS, use o `sudo` comando: `sudo pip install azure-mgmt-compute`.
- Windows: Abra o prompt de comando do PowerShell como administrador

Você pode instalar individualmente cada biblioteca para cada serviço Azure:

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

Pacotes de visualização podem ser instalados usando o `--pre` sinalizador:

```console
   $ pip install --pre azure-mgmt-compute # will install only the latest Compute Management library
```

Você também pode instalar um conjunto de bibliotecas Azure em uma única linha usando o `azure` meta-package. Como nem todos os pacotes neste pacote de meta são publicados como estável ainda, o `azure` meta-package ainda está em Visualizar. No entanto, os pacotes de núcleo, de perspectivas de qualidade/integridade de código podem ser considerados "estáveis" neste momento
- ela será oficialmente rotulada como tal em sincronia com outros idiomas assim que possível. Podemos não estiver planejando quaisquer alterações ainda mais importantes até lá.

Como é uma versão de visualização, você precisa usar o `--pre` sinalizador:

```console
   $ pip install --pre azure
```
   
ou diretamente

```console
   $ pip install azure==2.0.0rc6
```

## <a name="getting-more-packages"></a>Obtendo mais pacotes

O [Índice de pacote de Python][] (PyPI) tem uma seleção avançada de bibliotecas de Python.  Se você decidiu instalar uma distribuição, você já terá a maioria dos bits interessantes para vários cenários de desenvolvimento da web para computação técnica.


## <a name="python-tools-for-visual-studio"></a>Ferramentas de Python para Visual Studio

[Ferramentas de Python para Visual Studio][] (PTVS) é um plug-in livre/OSS da Microsoft, o que transforma VS em um IDE Python completo:

![How-a-instalação-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Usando PTVS é opcional, mas recomenda-se a como oferece suporte Python e projeto/solução da Web, depuração, perfil, janela interativa, edição do modelo e Intellisense.

PTVS também torna mais fácil implantar à Microsoft Azure, com suporte para a implantação em [sites][]e [Serviços de nuvem][] .

PTVS funciona com sua 2013 Studio Visual existente ou instalação de 2015.  Para documentação, downloads e discussões, consulte [Python ferramentas para Visual Studio].  

## <a name="python-azure-scenarios-for-linux-and-macos"></a>Python Azure cenários para Linux e MacOS

Para Linux ou MacOS, principais cenários Azure que são suportados:

1. Consumindo serviços Azure usando as bibliotecas de cliente para Python

2. Executando seu aplicativo em uma máquina virtual Linux

3. Desenvolvendo e publicação de sites do Azure usando gito

O primeiro cenário permite criar aplicativos web avançados que aproveitar os recursos do Azure PaaS como o [armazenamento de blob][], [armazenamento de fila][], o [armazenamento de tabela][] etc via conteúdos Pythonic adicionais para as APIs REST do Azure. Estas funciona idêntico em Windows, Mac e Linux.  Você também pode usar essas bibliotecas de cliente da sua máquina de desenvolvimento local ou um Linux VM em execução no Azure.

Para o cenário de máquina virtual, basta iniciar uma VM Linux de sua escolha (Ubuntu, CentOS, Suse) e executar/gerenciar o que você gosta.  Como exemplo, você pode executar [IPython][] REPL/bloco de anotações no computador Mac/Windows/Linux e aponte seu navegador para Linux ou Windows máquina virtual do multi-proc executando o mecanismo de IPython no Azure. Consulte o tutorial de [Bloco de anotações de IPython no Azure][] para obter mais informações.

Para obter informações sobre como configurar uma VM Linux, consulte o tutorial de [criar uma máquina Virtual executando Linux][] .

Usando gito implantação, você pode desenvolver um aplicativo da web de Python e publicá-lo para um site do Azure de qualquer sistema operacional.  Quando você pressiona seu repositório no Azure, ele criará automaticamente um ambiente virtual e pip instale os pacotes necessários.

Para obter mais informações sobre o desenvolvimento e Azure sites de publicação, consulte os tutoriais para [Criação de sites com Django][], [Criação de sites com garrafa][]e [Criação de sites com Flask][]. Para obter informações gerais sobre como usar qualquer estrutura compatível com WSGI, consulte [Configurando Python com sites do Azure][].


## <a name="additional-software-and-resources"></a>Software adicional e recursos:

* [Azure SDK para Python ReadTheDocs](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [Azure SDK para Python Github](https://github.com/Azure/azure-sdk-for-python)
* [Exemplos de Azure oficiais para Python](https://azure.microsoft.com/documentation/samples/?platform=python)
* [Distribuição de Python Analytics contínuo][]
* [Distribuição de Python Enthought][]
* [Distribuição de Python ActiveState][]
* [SciPy - um conjunto de bibliotecas de Python científico][]
* [NumPy - uma biblioteca de caracteres alfanuméricos para Python][]
* [Projeto de Django - um web desenvolvida framework/CMS][]
* [IPython - um avançado REPL/bloco de anotações para Python][]
* [Bloco de anotações de IPython no Azure][]
* [Ferramentas de Python para Visual Studio no GitHub][]
* [Central de desenvolvedores do Python](/develop/python/)

[Distribuição de Python Analytics contínuo]: http://continuum.io
[Distribuição de Python Enthought]: http://www.enthought.com
[Distribuição de Python ActiveState]: http://www.activestate.com
[www.Python.org]: http://www.python.org
[www.continuum.IO]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.ActiveState.com]: http://www.activestate.com
[SciPy - um conjunto de bibliotecas de Python científico]: http://www.scipy.org
[NumPy - uma biblioteca de caracteres alfanuméricos para Python]: http://www.numpy.org
[Projeto de Django - um web desenvolvida framework/CMS]: http://www.djangoproject.com
[IPython - um avançado REPL/bloco de anotações para Python]: http://ipython.org
[IPython]: http://ipython.org
[Bloco de anotações de IPython no Azure]: virtual-machines-linux-jupyter-notebook.md
[Serviços de nuvem]: cloud-services-python-ptvs.md
[Sites]: web-sites-python-ptvs-django-mysql.md
[Ferramentas de Python para Visual Studio]: http://aka.ms/ptvs
[Ferramentas de Python para Visual Studio no GitHub]: https://github.com/microsoft/ptvs
[Índice de pacote de Python]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Criar uma máquina Virtual executando Linux]: virtual-machines-linux-quick-create-cli.md
[Criação de sites com Django]: web-sites-python-create-deploy-django-app.md
[Criação de sites com garrafa]: web-sites-python-create-deploy-bottle-app.md
[Criação de sites com Flask]: web-sites-python-create-deploy-flask-app.md
[Configurando Python com sites do Azure]: web-sites-python-configure.md
[armazenamento de tabela]: storage-python-how-to-use-table-storage.md
[armazenamento de fila]: storage-python-how-to-use-queue-storage.md
[armazenamento de blob]: storage-python-how-to-use-blob-storage.md
