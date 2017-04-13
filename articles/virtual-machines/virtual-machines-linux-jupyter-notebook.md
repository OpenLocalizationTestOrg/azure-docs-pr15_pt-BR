<properties
    pageTitle="Criar um bloco de anotações de Jupyter/IPython | Microsoft Azure"
    description="Saiba como implantar o bloco de anotações de Jupyter/IPython em uma máquina virtual do Linux criada com o modelo de implantação do Gerenciador de recurso no Azure."
    services="virtual-machines-linux"
    documentationCenter="python"
    authors="crwilcox"
    manager="wpickett"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="python"
    ms.topic="article"
    ms.date="11/10/2015"
    ms.author="crwilcox"/>

# <a name="jupyter-notebook-on-azure"></a>Bloco de anotações de Jupyter no Azure

O [projeto Jupyter](http://jupyter.org), anteriormente o [projeto IPython](http://ipython.org)fornece um conjunto de ferramentas para computação científica usando poderosas conchas interativas que combinam execução de código com a criação de um documento de computação ao vivo. Esses arquivos de bloco de anotações podem conter texto aleatório, fórmulas matemáticas, código de entrada, resultados, gráficos, vídeos e qualquer outro tipo de mídia que um navegador da web modernos é capaz de exibir. Se você estiver absolutamente conhece Python e deseja sabê-lo em um ambiente divertido e interativo ou fazer algumas paralelo/técnicos graves computação, o bloco de anotações de Jupyter é uma excelente opção.

![Captura de tela](./media/virtual-machines-linux-jupyter-notebook/ipy-notebook-spectral.png) pacotes usando SciPy e Matplotlib para analisar a estrutura de uma gravação de som.


## <a name="jupyter-two-ways-azure-notebooks-or-custom-deployment"></a>Jupyter duas maneiras: Blocos de anotações Azure ou implantação personalizada
Azure fornece um serviço que você pode usar para [Iniciar rapidamente usando Jupyter ](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).  Usando o serviço de bloco de anotações do Azure, você pode facilmente acessar a interface da web acessível do Jupyter aos recursos de computação scalable com todos os recursos do Python e suas bibliotecas muitos.  Desde que a instalação é tratada pelo serviço, os usuários podem acessar esses recursos sem a necessidade de administração e configuração pelo usuário.

Se o serviço de bloco de anotações não funciona para seu cenário prossiga a leitura deste artigo que será mostrará como implantar o bloco de anotações de Jupyter no Microsoft Azure, usando Linux VMs (máquinas virtuais).

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="create-and-configure-a-vm-on-azure"></a>Criar e configurar uma máquina virtual no Azure

A primeira etapa é criar uma máquina virtual (VM) em execução no Azure.
Esta máquina virtual é um sistema operacional completo na nuvem e será usado para executar o bloco de anotações de Jupyter. Azure é capaz de executar máquinas virtuais Linux e Windows e abordaremos na configuração de Jupyter em ambos os tipos de máquinas virtuais.

### <a name="create-a-linux-vm-and-open-a-port-for-jupyter"></a>Criar uma VM Linux e abra uma porta para Jupyter

Siga as instruções fornecido [aqui] [ portal-vm-linux] para criar uma máquina virtual da distribuição *Ubuntu* . Este tutorial usa Ubuntu servidor 14.04 LTS. Vamos supor que o nome de usuário *azureuser*.

Depois que a máquina virtual implanta precisamos abrir uma regra de segurança no grupo de segurança de rede.  Azure no portal do, vá para **Grupos de segurança de rede** e abra a guia do grupo de segurança correspondente à sua máquina virtual. Você precisa adicionar uma regra de segurança de entrada com as seguintes configurações: **TCP** para o protocolo, **\*** para a porta de origem (público) e **9999** para a porta de destino (particular).

![Captura de tela](./media/virtual-machines-linux-jupyter-notebook/azure-add-endpoint.png)

Enquanto estiver no seu grupo de segurança de rede, clique em **Interfaces de rede** e anote o **Endereço IP público** , pois ele será necessário para se conectar à sua máquina virtual na próxima etapa.

## <a name="install-required-software-on-the-vm"></a>Instalar o software necessário na máquina virtual

Para executar o bloco de anotações de Jupyter em nossa máquina virtual, podemos instale Jupyter e suas dependências. Conectar ao seu linux máquina virtual usando ssh e o nome de usuário/senha emparelhar que você escolheu ao criar a máquina virtual. Neste tutorial, podemos será use Acabamento e conecte do Windows.

### <a name="installing-jupyter-on-ubuntu"></a>Instalando o Jupyter em Ubuntu
Instale o Anaconda, uma distribuição de python de ciência de dados populares, usando um dos links fornecidos a partir de [Análise de contínuo](https://www.continuum.io/downloads).  No momento da elaboração deste documento, os links a seguir são mais até versões de data.

#### <a name="anaconda-installs-for-linux"></a>Instalações de anaconda para Linux
<table>
  <th>Python 3.4</th>
  <th>Python 2.7</th>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh'>64 bits</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86_64.sh'>64 bits</href>
    </td>
  </tr>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86.sh'>32 bits</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86.sh'>32 bits</href>
    </td>  
  </tr>
</table>


#### <a name="installing-anaconda3-230-64-bit-on-ubuntu"></a>Instalando Anaconda3 2.3.0 64 bits em Ubuntu
Como exemplo, isso é como você pode instalar Anaconda no Ubuntu

    # install anaconda
    cd ~
    mkdir -p anaconda
    cd anaconda/
    curl -O https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh
    sudo bash Anaconda3-2.3.0-Linux-x86_64.sh -b -f -p /anaconda3

    # clean up home directory
    cd ..
    rm -rf anaconda/

    # Update Jupyter to the latest install and generate its config file
    sudo /anaconda3/bin/conda install jupyter -y
    /anaconda3/bin/jupyter-notebook --generate-config


![Captura de tela](./media/virtual-machines-linux-jupyter-notebook/anaconda-install.png)


### <a name="configuring-jupyter-and-using-ssl"></a>Configurando Jupyter e usando SSL
Após a instalação, precisamos demorar um pouco para configurar os arquivos de configuração para Jupyter. Se você enfrentar problemas com a configuração Jupyter pode ser útil examinar a [Documentação de Jupyter para executar um servidor de bloco de anotações](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html).

Próximo nós `cd` no diretório de perfil para criar nosso certificado SSL e editar o arquivo de configuração de perfis.

No Linux, use o comando a seguir.

    cd ~/.jupyter

Use o seguinte comando para criar o certificado SSL (Linux e Windows).

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Observe que, desde que estamos criando um certificado SSL autoassinado, quando a conexão com o bloco de anotações seu navegador você terá um aviso de segurança.  Para uso de produção a longo prazo, convém usar um certificado corretamente assinado associado à sua organização.  Como o gerenciamento de certificados é além do escopo desta demonstração, manteremos um certificado auto-assinado por agora.

Além de usar um certificado, você também deve fornecer uma senha para proteger o seu bloco de anotações do uso não autorizado.  Por razões de segurança Jupyter usa senhas criptografadas em seu arquivo de configuração, portanto você precisará criptografar sua senha primeiro.  IPython oferece um utilitário para fazê-lo; no prompt de comando, execute o seguinte comando.

    /anaconda3/bin/python -c "import IPython;print(IPython.lib.passwd())"

Isso solicitará uma senha e a confirmação e, em seguida, será impresso a senha. Observação Isso para a etapa seguinte.

    Enter password:
    Verify password:
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

Em seguida, podemos irá editar o arquivo de configuração do perfil, que é o `jupyter_notebook_config.py` arquivo no diretório que você estiver usando.  Observe que este arquivo pode não existir, basta criá-lo, se for o caso.  Este arquivo tem um número de campos e por padrão todos são comentados.  Você pode abrir esse arquivo com qualquer editor de texto de sua preferência e você deve garantir que ele tenha pelo menos o seguinte conteúdo. **Certifique-se de substituir o c.NotebookApp.password no config com o sha1 da etapa anterior**.

    c = get_config()

    # You must give the path to the certificate file.
    c.NotebookApp.certfile = u'/home/azureuser/.jupyter/mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### <a name="run-the-jupyter-notebook"></a>Executar o bloco de anotações de Jupyter

Neste ponto, estamos prontos para iniciar o bloco de anotações de Jupyter. Para fazer isso, navegue até a pasta que você deseja armazenar blocos de anotações no e iniciar o servidor de bloco de anotações de Jupyter com o comando a seguir.

    /anaconda3/bin/jupyter-notebook

Você agora deve conseguir acessar o seu bloco de anotações de Jupyter no endereço `https://[PUBLIC-IP-ADDRESS]:9999`.

Quando você primeiro acessar seu bloco de anotações, a página de login solicita sua senha. E depois efetuar login, você verá "Jupyter Notebook painel", que é o Centro de ontrole para todas as operações de bloco de anotações.  Nesta página, você pode criar novos notebooks e abrir existentes.

![Captura de tela](./media/virtual-machines-linux-jupyter-notebook/jupyter-tree-view.png)

### <a name="using-the-jupyter-notebook"></a>Usando o bloco de anotações de Jupyter

Se você clicar no botão **novo** , você verá a seguinte página de abertura.

![Captura de tela](./media/virtual-machines-linux-jupyter-notebook/jupyter-untitled-notebook.png)

A área marcada com um `In []:` é avisar a área de entrada e aqui você pode digitar qualquer código Python válido e ela será executada quando você pressionar `Shift-Enter` ou clique no ícone "Reproduzir" (o triângulo apontando na barra de ferramentas).

## <a name="a-powerful-paradigm-live-computational-documents-with-rich-media"></a>Um paradigma poderoso: live computação documentos com mídia avançada

O bloco de anotações em si deve se sentir muito natural para qualquer pessoa que usou Python e um processador de texto, porque ele está em algumas maneiras de uma combinação de ambas: você pode executar blocos de código Python, mas também é possível manter anotações e outro texto alterando o estilo de uma célula de "Código" a "Redução" usando o menu suspenso na barra de ferramentas.

Jupyter é muito mais do que um processador de texto, pois ele permite a combinação de computação e mídia avançada (texto, gráficos, vídeo e praticamente tudo o que um navegador da web moderna pode exibir). Você pode misturar, texto, código, vídeos e muito mais!

![Captura de tela](./media/virtual-machines-linux-jupyter-notebook/jupyter-editing-experience.png)

E com a potência do muitas bibliotecas excelentes de Python para científica e técnica computação, na captura de tela a seguir, que um cálculo simples pode ser executado com a mesma facilidade que uma análise de rede complexa, tudo em um ambiente.

Esse paradigma de combinação potência da web moderna com a computação live oferece muitas possibilidades e é ideal para a nuvem; o bloco de anotações pode ser usado:

* Como um bloco de anotações de computação gravar exploratório trabalhe em um problema.

* Para compartilhar resultados com colegas, no formulário de computação 'live' ou formatos de cópia impressa (HTML, PDF).

* Distribuir e apresentar ensino live materiais que envolvem computação, para que os alunos imediatamente podem experimentar o código real, modificá-lo em execução-la de forma interativa novamente.

* Fornecer "executáveis papers" apresentam os resultados de pesquisa de modo que pode ser imediatamente reproduzido, validado e estendido por outras pessoas.

* Como uma plataforma de computação colaborativa: vários usuários podem fazer logon no mesmo servidor de bloco de anotações para compartilhar uma sessão de computação ao vivo.


Se você acessar o [repositório][]do código de origem IPython, você encontrará um diretório inteiro com exemplos de bloco de anotações que você pode baixar e, em seguida, experimente na sua própria máquina virtual Jupyter do Azure.  Basta baixar o `.ipynb` arquivos do site e carregá-los em Painel de seu bloco de anotações máquina virtual do Azure (ou baixá-los diretamente para a máquina virtual).

## <a name="conclusion"></a>Conclusão

O bloco de anotações de Jupyter fornece uma interface poderosa para acessar interativamente potência do ecossistema Python no Azure.  Ele abrange uma ampla variedade de casos de uso, incluindo exploração simple e aprendizado Python, análises de dados e visualização, simulação e computação paralela. Os documentos de bloco de anotações resultantes contêm um registro completo dos cálculos que são executadas e pode ser compartilhado com outros usuários de Jupyter.  O bloco de anotações de Jupyter pode ser usado como um aplicativo local, mas é ideal para implantações de nuvem no Azure

Os principais recursos do Jupyter também estão disponíveis no Visual Studio por meio de [Ferramentas de Python para Visual Studio][] (PTVS). PTVS é um gratuito e integração de computação de código-fonte aberto plug-in da Microsoft que Visual Studio se transforma em um ambiente de desenvolvimento Python avançado que inclui um editor avançado com o IntelliSense, depuração, perfil e paralelos.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte o [Python Developer Center](/develop/python/).

[portal-vm-linux]: https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-tutorial-portal-rm/
[repositório]: https://github.com/ipython/ipython
[Ferramentas de Python para Visual Studio]: http://aka.ms/ptvs
