<properties
   pageTitle="Implantação do Azure máquina virtual com chefe | Microsoft Azure"
   description="Saiba como usar chefe fazer automatizada máquina virtual implantação e configuração no Microsoft Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="diegoviso"
   manager="timlt"
   tags="azure-service-management,azure-resource-manager"
   editor=""/>

<tags ms.service="virtual-machines-windows" ms.workload="infrastructure-services"
ms.tgt_pltfrm="vm-multiple"
ms.devlang="na"
ms.topic="article"
ms.date="05/19/2015"
ms.author="diviso"/>

# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatizar a implantação do Azure máquina virtual com chefe

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Chefe é uma ótima ferramenta para fornecer automação e configurações de estado de desejado.

Com nossa versão mais recente do api de nuvem, o chefe fornece integração perfeita com o Azure, dando a você a capacidade de provisionar e implantar estados de configuração por meio de um único comando.

Neste artigo, eu mostraremos como configurar seu ambiente chefe para provisionar Azure máquinas virtuais e orientá-lo por meio da criação de uma política ou "Livro de receitas" e depois implantá este livro de receitas em uma máquina virtual Azure.

Vamos começar!

## <a name="chef-basics"></a>Noções básicas do chefe

Antes de começar, sugerimos que você examine os conceitos básicos do chefe. Não há ótimo material <a href="http://www.chef.io/chef" target="_blank">aqui</a> e recomendamos que você ter uma rápida leitura antes de tentar este passo a passo. No entanto, eu será novamente as Noções básicas antes de começar.

O diagrama a seguir ilustra a arquitetura de chefe de alto nível.

![][2]

Chefe tem três componentes principais de arquiteturais: servidor chefe, chefe cliente (nó) e chefe estação de trabalho.

O servidor de chefe é nosso ponto de gerenciamento e há duas opções para o servidor de chefe: uma solução hospedada ou uma solução local. Usaremos uma solução hospedada.

O cliente de chefe (nó) é o agente que reside nos servidores que você está gerenciando.

A estação de trabalho chefe é nossa estação de trabalho do administrador onde criamos nossas políticas e executar nossos comandos de gerenciamento. Podemos executar o comando **faca** a estação de trabalho chefe para gerenciar nossa infraestrutura.

Há também o conceito de "Manuais de" e "Receitas". Estes são efetivamente as políticas que definimos e aplicar a nossos servidores.

## <a name="preparing-the-workstation"></a>Preparando a estação de trabalho

Primeiro, permite preparar a estação de trabalho. Estou usando uma estação de trabalho padrão do Windows. Precisamos criar um diretório para armazenar nossos arquivos de configuração e manuais.

Primeiro, crie um diretório chamado C:\chef.

Crie uma segunda pasta chamada c:\chef\cookbooks.

Agora, precisamos baixar nosso arquivo de configurações do Azure para que chefe possa se comunicar com nossa assinatura do Azure.

Baixar suas configurações de [aqui](https://manage.windowsazure.com/publishsettings/)publicação.

Salve o arquivo de configurações de publicação no C:\chef.

##<a name="creating-a-managed-chef-account"></a>Criando uma conta de chefe gerenciada

Inscrever-se para uma conta de chefe hospedada [aqui](https://manage.chef.io/signup).

Durante o processo de inscrição, você será solicitado a criar uma nova organização.

![][3]

Depois que sua organização for criada, baixe o starter kit.

![][4]

> [AZURE.NOTE] Se você receber um aviso informando que suas chaves serão redefinidas, é okey prossiga como não temos nenhuma infraestrutura existente como ainda configurada.

Este arquivo de zip starter kit contém arquivos de configuração de organização e chaves.

##<a name="configuring-the-chef-workstation"></a>Configurando a estação de trabalho do chefe

Extrai o conteúdo do chefe-starter.zip para C:\chef.

Copie todos os arquivos em chefe-starter\chef-repo\.chefe ao seu diretório de c:\chef.

Seu diretório deve agora parecer com o exemplo a seguir.

![][5]

Agora você deve ter quatro arquivos, incluindo o arquivo de publicação Azure na raiz do c:\chef.

Os arquivos PEM contêm sua organização e o administrador chaves particulares para comunicação enquanto o arquivo knife.rb contém a configuração de Faca. Precisamos editar o arquivo knife.rb.

Abra o arquivo no editor de sua preferência e modificar o "cookbook_path" Removendo o /.. / do caminho para que ela apareça como mostrado a seguir.

    cookbook_path  ["#{current_dir}/cookbooks"]

Também adicione a seguinte linha refletindo o nome do seu Azure publica arquivo de configurações.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Seu arquivo de knife.rb agora deve ser semelhante ao seguinte exemplo.

![][6]

Essas linhas garantirá que faca referencia diretório manuais em c:\chef\cookbooks e também usa o nosso arquivo de configurações de publicação do Azure durante operações Azure.

## <a name="installing-the-chef-development-kit"></a>Instalar o Kit de desenvolvimento de chefe

Próximo [Baixe e instale](http://downloads.getchef.com/chef-dk/windows) o ChefDK (chefe Development Kit) para configurar a sua estação de trabalho do chefe.

![][7]

Instale o local padrão do c:\opscode. Esta instalação levará cerca de 10 minutos.

Confirme que a variável PATH contém entradas para C:\opscode\chefdk\bin; C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

Se não estiverem lá, verifique se que você adicionar esses caminhos!

*OBSERVE QUE A ORDEM DO CAMINHO É IMPORTANTE!* Se seus caminhos de opscode não estão na ordem correta, você terá problemas.

Reinicie sua estação de trabalho antes de continuar.

Em seguida, podemos serão instalados a extensão do Azure Faca. Isso fornece faca com o "Azure plug-in".

Execute o seguinte comando.

    chef gem install knife-azure ––pre

> [AZURE.NOTE] O argumento – pre garante que você está recebendo a última versão RC do plug-in do Azure faca que fornece acesso ao conjunto mais recente de APIs.

É provável que um número de dependências também será instalado ao mesmo tempo.

![][8]


Para garantir que tudo esteja configurado corretamente, execute o seguinte comando.

    knife azure image list

Se tudo estiver configurado corretamente, você verá uma lista de imagens Azure disponíveis rolar.

Parabéns. A estação de trabalho está configurada!

##<a name="creating-a-cookbook"></a>Criando um livro de receitas

Um livro de receitas é usado pelo chefe para definir um conjunto de comandos que você deseja executar no seu cliente gerenciado. Criar um livro de receitas é simples e usamos o comando **chefe gerar livro de receitas** gerar nosso modelo de livro de receitas. Posso será chamado Meu servidor de web livro de receitas como eu gostaria que uma política que implanta automaticamente IIS.

Em seu diretório C:\Chef executar o comando a seguir.

    chef generate cookbook webserver

Isso irá gerar um conjunto de arquivos sob o diretório C:\Chef\cookbooks\webserver. Agora, precisamos definir o conjunto de comandos que podemos gostaria nosso cliente chefe para executar em nossa máquina virtual gerenciada.

Os comandos são armazenados em default.rb o arquivo. Nesse arquivo, eu será definindo um conjunto de comandos que instala o IIS, inicia IIS e copia um arquivo de modelo na pasta wwwroot.

Modificar o arquivo C:\chef\cookbooks\webserver\recipes\default.rb e adicione as seguintes linhas.

    powershell_script 'Install IIS' do
        action :run
        code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
        action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
        source 'Default.htm.erb'
        rights :read, 'Everyone'
    end

Quando terminar, salve o arquivo.

## <a name="creating-a-template"></a>Criando um modelo

Conforme mencionado anteriormente, precisamos gerar um arquivo de modelo que será usado como nossa página Default.

Execute o seguinte comando para gerar o modelo.

    chef generate template webserver Default.htm

Agora, navegue até o arquivo C:\chef\cookbooks\webserver\templates\default\Default.htm.erb. Edite o arquivo adicionando um código de "Olá, mundo" HTML simples e salve o arquivo.



## <a name="upload-the-cookbook-to-the-chef-server"></a>Carregar o livro de receitas no servidor chefe

Nesta etapa, estamos fazendo uma cópia do livro de receitas que criamos em nossa máquina local e carregá-la no servidor de hospedada chefe. Uma vez carregado, o livro de receitas aparecerão na guia de **política** .

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Implantar uma máquina virtual com faca Azure

Agora podemos implantar uma máquina virtual Azure e aplicar o livro de receitas "Servidor Web" que serão instalados nossa IIS página da web padrão e de serviço web.

Para fazer isso, use o comando **faca azure server crie** .

Sou exemplo do comando aparece próxima.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Os parâmetros são auto-explicativos. Substituir as variáveis específicas e executar.

> [AZURE.NOTE] Por meio da linha de comando, eu estou também automatizar minhas regras de filtro de rede de ponto de extremidade usando o parâmetro – tcp pontos de extremidade. As portas 80 e 3389 para fornecer acesso a minha página da web e sessão RDP aberto.

Depois que você executa o comando, acesse o portal do Azure e você verá sua máquina começar a provisionar.

![][13]

Prompt de comando aparece próxima.

![][10]

Uma vez concluída a implantação, nós deve ser capazes de se conectar ao serviço web pela porta 80 como podemos tinha aberto a porta quando podemos provisionado a máquina virtual com o comando faca Azure. Como esta máquina virtual é a única máquina virtual em meu serviço de nuvem, eu será conecte-o com a url do serviço de nuvem.

![][11]

Como você pode ver, eu obtive criativo com meu código HTML.

Não se esqueça de que nós também podem se conectar por meio de uma sessão de RDP do Azure clássico portal através da porta 3389.

Espero que isso tenha sido útil! Vá e iniciar sua infraestrutura como jornada de código com o Azure hoje!


<!--Image references-->
[2]: ./media/virtual-machines-windows-chef-automation/2.png
[3]: ./media/virtual-machines-windows-chef-automation/3.png
[4]: ./media/virtual-machines-windows-chef-automation/4.png
[5]: ./media/virtual-machines-windows-chef-automation/5.png
[6]: ./media/virtual-machines-windows-chef-automation/6.png
[7]: ./media/virtual-machines-windows-chef-automation/7.png
[8]: ./media/virtual-machines-windows-chef-automation/8.png
[9]: ./media/virtual-machines-windows-chef-automation/9.png
[10]: ./media/virtual-machines-windows-chef-automation/10.png
[11]: ./media/virtual-machines-windows-chef-automation/11.png
[13]: ./media/virtual-machines-windows-chef-automation/13.png


<!--Link references-->
