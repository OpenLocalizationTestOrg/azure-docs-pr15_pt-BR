<properties
    pageTitle="Hospedar um Ruby no site de Rails em uma VM Linux | Microsoft Azure"
    description="Configurar e hospedar um Ruby no site baseado em Rails no Azure usando um computador virtual Linux."
    services="virtual-machines-linux"
    documentationCenter="ruby"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="web"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Ruby no aplicativo da Web de Rails em uma máquina virtual do Azure

Este tutorial mostra como hospedar um Ruby no site de Rails no Azure usando um computador virtual Linux.  

Este tutorial foi validado usando Ubuntu servidor 14.04 LTS. Se você usa uma distribuição Linux diferente, talvez seja necessário modificar as etapas para instalar Rails.

> [AZURE.IMPORTANT] Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de recursos e clássico](../../../resource-manager-deployment-model.md).  Este artigo aborda usando o modelo clássico de implantação. Recomendamos que mais novas implantações de usam o modelo do Gerenciador de recursos.

## <a name="create-an-azure-vm"></a>Criar uma máquina virtual Azure

Comece criando uma máquina virtual do Azure com uma imagem de Linux.

Para criar a máquina virtual, você pode usar o portal de clássico Azure ou a Interface de linha de comando (CLI) do Azure.

### <a name="azure-management-portal"></a>Portal de gerenciamento do Azure

1. Entrar no do [Azure portal clássico](http://manage.windowsazure.com)
2. Clique em **novo** > **Calcular** > **Máquina Virtual** > **criação rápida**. Selecione uma imagem de Linux.
3. Digite uma senha.

Depois que a máquina virtual está provisionada, clique no nome da máquina virtual e clique em **painel**. Localize o ponto de extremidade SSH, listado em **Detalhes de SSH**.

### <a name="azure-cli"></a>CLI Azure

Siga as etapas em [criar uma máquina Virtual executando Linux][vm-instructions].

Depois que a máquina virtual está provisionada, você pode obter o ponto de extremidade SSH executando o seguinte comando:

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Instalar o Ruby on Rails

1. Use SSH para conectar-se para a máquina virtual.

2. Da sessão SSH, use os seguintes comandos para instalar o Ruby na máquina virtual:

        sudo apt-get update -y
        sudo apt-get upgrade -y
        sudo apt-get install ruby ruby-dev build-essential libsqlite3-dev zlib1g-dev nodejs -y

    A instalação pode levar alguns minutos. Quando terminar, use o seguinte comando para verificar se Ruby está instalado:

        ruby -v

    Isso retorna a versão do Ruby que foi instalado.

3. Use o seguinte comando para instalar Rails:

        sudo gem install rails --no-rdoc --no-ri -V

    Use-sinalizadores não rdoc e – não-ri para ignorar instalando a documentação, que é mais rápida.
    Este comando provavelmente levará muito tempo para executar, portanto, adicionar -V exibirá informações sobre o andamento da instalação.

## <a name="create-and-run-an-app"></a>Criar e executar um aplicativo

Enquanto ainda conectado via SSH, execute os seguintes comandos:

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

O [novo](http://guides.rubyonrails.org/command_line.html#rails-new) comando cria um novo aplicativo Rails. O comando [server](http://guides.rubyonrails.org/command_line.html#rails-server) inicia o servidor da web WEBrick que vem com Rails. (Para uso de produção, você provavelmente desejaria usar um servidor diferente, como unicórnio ou passageiro.)

Você deve ver saída semelhante à seguinte.

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Adicionar um ponto extremo

1. Acesse o [portal clássico Azure] [ management-portal] e selecione sua máquina virtual.

    ![lista de máquina virtual][vmlist]

2. Selecione **pontos de EXTREMIDADE** na parte superior da página e, em seguida, clique em **+ ponto de EXTREMIDADE de adicionar** na parte inferior da página.

    ![página pontos de extremidade][endpoints]

3. Na caixa de diálogo **Adicionar ponto final** , selecione "Adicionar um ponto de extremidade autônomo" e clique na seta **próxima** .

    ![caixa de diálogo Novo ponto de extremidade][new-endpoint1]

3. Na próxima página de diálogo, insira as seguintes informações:

    * **Nome**: HTTP

    * **Protocolo**: TCP

    * **Porta pública**: 80

    * **Porta de particular**: 3000

    Isso criará uma pública porta de 80 que vai rotear o tráfego à porta particular do 3000, onde o servidor Rails é listening.

    ![caixa de diálogo Novo ponto de extremidade][new-endpoint]

4. Clique na marca de seleção para salvar o ponto de extremidade.

5. Deverá aparecer uma mensagem afirmando **Atualização em andamento**. Depois que esta mensagem desaparece, o ponto de extremidade está ativo. Agora você pode testar seu aplicativo navegando até o nome de sua máquina virtual DNS. O site deve aparecer semelhante ao seguinte:

    ![página de rails padrão][default-rails-cloud]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você já fez a maioria das etapas manualmente. Em um ambiente de produção, você poderia escrever seu aplicativo em uma máquina de desenvolvimento e implantá-lo para a máquina virtual do Azure. Além disso, a maioria dos ambientes de produção hospedar o aplicativo de Rails em conjunto com outro processo de servidor como Apache ou NginX, que controla solicitar o roteamento para várias instâncias do aplicativo Rails e servir recursos estáticos. Para obter mais informações, consulte http://rubyonrails.org/deploy/.

Para saber mais sobre Ruby on Rails, visite o [Ruby on Rails guias][rails-guides].

Para usar os serviços do Azure do seu aplicativo Ruby, consulte:

* [Armazenar dados não estruturados usando blobs][blobs]

* [Pares de chave/valor de repositório usando tabelas][tables]

* [Servir conteúdo de largura de banda alta com a rede de entrega de conteúdo][cdn-howto]

<!-- WA.com links -->
[blobs]: ../../../storage/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]: https://azure.microsoft.com/develop/ruby/app-services/
[management-portal]: https://manage.windowsazure.com/
[tables]: ../../../storage/storage-ruby-how-to-use-table-storage.md
[vm-instructions]: ../../virtual-machines-linux-classic-createportal.md

<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/
[sqlite3]: http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
