#<a name="how-to-use-the-azure-command-line-tools-for-mac-and-linux"></a>Como usar as ferramentas de linha de comando do Azure para Mac e Linux

Este guia descreve como usar as ferramentas de linha de comando do Azure para Mac e Linux para criar e gerenciar serviços no Azure. Os cenários cobertos incluem **instalar as ferramentas**, **importando suas configurações de publicação**, **criar e gerenciar sites do Azure**e **criar e gerenciar máquinas virtuais do Azure**. Para documentação de referência abrangente, consulte a [ferramenta de linha de comando do Azure para Mac e documentação Linux][reference-docs]. 

##<a name="table-of-contents"></a>Sumário
* [Quais são as ferramentas de linha de comando do Azure para Mac e Linux](#Overview)
* [Como instalar as ferramentas de linha de comando do Azure para Mac e Linux](#Download)
* [Como criar uma conta do Azure](#CreateAccount)
* [Como fazer o download e importar configurações de publicação](#Account)
* [Como criar e gerenciar um Site do Azure](#WebSites)
* [Como criar e gerenciar uma máquina de Virtual do Azure](#VMs)


<h2><a id="Overview"></a>Quais são as ferramentas de linha de comando do Azure para Mac e Linux</h2>

As ferramentas de linha de comando do Azure para Mac e Linux são um conjunto de ferramentas de linha de comando para implantar e gerenciar serviços do Azure.
 
As tarefas com suporte incluem o seguinte:

* Importe configurações de publicação.
* Criar e gerenciar sites do Azure.
* Criar e gerenciar máquinas virtuais do Azure.

Para obter uma lista completa de comandos com suporte, digite `azure -help` na linha de comando depois de instalar as ferramentas, ou consulte a [documentação de referência][reference-docs].

<h2><a id="Download">Como instalar as ferramentas de linha de comando do Azure para Mac e Linux</a></h2>

A lista a seguir contém informações para instalar as ferramentas de linha de comando, dependendo do sistema operacional:

* **Mac**: baixar o [instalador do Azure SDK][mac-installer]. Abra o arquivo. pkg baixado e conclua as etapas de instalação quando for solicitado.

* **Linux**: instalar a versão mais recente do [Node] [ nodejs-org] (consulte [Instalar Node via Gerenciador de pacote][install-node-linux]), em seguida, execute o seguinte comando:

        npm install azure-cli -g

    **Observação**: talvez seja necessário executar este comando com privilégios elevados:

        sudo npm install azure-cli -g

* **Windows**: executar o instalador Winows (arquivo. msi), que está disponível aqui: [Ferramentas de linha de comando do Azure][windows-installer].


Para testar a instalação, digite `azure` no prompt de comando. Se a instalação foi bem-sucedida, você verá uma lista de todas as disponíveis `azure` comandos.

<h2><a id="CreateAccount"></a>Como criar uma conta do Azure</h2>

Para usar as ferramentas de linha de comando do Azure para Mac e Linux, você precisará de uma conta do Azure.

Abra um navegador da web e navegue até [http://www.windowsazure.com] [ windowsazuredotcom] e clique em **avaliação gratuita** no canto superior direito.

![Site do Azure][Azure Web Site]

Siga as instruções para a criação de uma conta.

<h2><a id="Account"></a>Como fazer o download e importar configurações de publicação</h2>

Para começar, você precisa primeiro baixar e importar suas configurações de publicação. Isso permitirá que você use as ferramentas para criar e gerenciar serviços do Azure. Para baixar suas configurações de publicação, use o `account download` comando:

    azure account download

Será aberto o seu navegador padrão e solicitar que você entrar no Portal de gerenciamento. Depois de entrar, seu `.publishsettings` arquivo será baixado. Anote onde esse arquivo é salvo.

Em seguida, importe os `.publishsettings` arquivo executando o seguinte comando, substituindo `{path to .publishsettings file}` com o caminho para sua `.publishsettings` arquivo:

    azure account import {path to .publishsettings file}

Você pode remover todas as informações armazenadas pela <code>import</code> comando usando o <code>account clear</code> comando:

    azure account clear

Para ver uma lista de opções para `account` comandos, use o `-help` opção:

    azure account -help

Após importar suas configurações de publicação, você deve excluir os `.publishsettings` arquivo por razões de segurança.

> [AZURE.NOTE] Quando você importa configurações de publicação, as credenciais para acessar sua assinatura do Azure são armazenadas dentro de sua `user` pasta. Seu `user` está protegida pelo seu sistema operacional. No entanto, é recomendável que você execute etapas adicionais para criptografar sua `user` pasta. Você pode fazer isso das seguintes maneiras:    
> 
> - No Windows, modifique as propriedades da pasta ou usar o BitLocker.
> - No Mac, ative FileVault para a pasta.
> - No Ubuntu, use o recurso de diretório inicial criptografado. Outras distribuições Linux oferecem recursos equivalentes.

Agora você está pronto para ser criar e gerenciar sites do Azure e máquinas virtuais do Azure.  

<h2><a id="WebSites"></a>Como criar e gerenciar um site do Azure</h2>

###<a name="create-a-website"></a>Criar um site

Para criar um site do Azure, primeiro crie um diretório vazio chamado `MySite` e navegue para o diretório.

Em seguida, execute o seguinte comando:

    azure site create MySite --git

A saída desse comando conterá a URL padrão para o site recém-criado. O `--git` opção permite que você use gito para publicar seu site criando gito repositórios em ambos os seu diretório de aplicativo local e no Centro de dados do seu site. Observe que, se sua pasta local já é um repositório de gito, o comando adicionará uma nova remota para o repositório existente, apontando para o repositório no Centro de dados do seu site.

Observe que você pode executar o `azure site create` comando com qualquer uma das seguintes opções:

* `--location [location name]`. Essa opção permite que você especifique o local do data center no qual seu site é criado (por exemplo, "Oeste EUA"). Se você omitir esta opção, você será solicitado a escolher um local.
* `--hostname [custom host name]`. Essa opção permite que você especifique um nome de host personalizado para seu site.

Em seguida, você pode adicionar conteúdo ao seu diretório de site. Usar o fluxo de gito regulares (`git add`, `git commit`) para confirmar seu conteúdo. Use o seguinte comando gito por push o conteúdo do site para Azure: 

    git push azure master

###<a name="set-up-publishing-from-github"></a>Configurar a publicação do GitHub

Para configurar a publicação contínua de um repositório GitHub, use o `--GitHub` opção ao criar um site:

    auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

Se você tiver uma cópia local de um repositório GitHub ou se você tiver um repositório com uma única referência remota a um repositório GitHub, este comando automaticamente publica código no repositório GitHub ao seu site. Daí em seguida diante, quaisquer alterações enviadas ao repositório GitHub serão automaticamente publicadas ao seu site.

Quando você configura a publicação do GitHub, a ramificação padrão usada é a ramificação mestre. Para especificar uma ramificação diferente, execute o seguinte comando no seu repositório local:

    azure site repository <branch name>

###<a name="configure-app-settings"></a>Definir configurações de aplicativo

Configurações de aplicativo são pares de chave-valor que estão disponíveis para seu aplicativo no tempo de execução. Quando definido para um site do Azure, valores de configuração do aplicativo substituirão configurações com a mesma chave definidos no arquivo de Web. config do seu site. Para aplicativos node e PHP, configurações de aplicativo estão disponíveis como variáveis de ambiente. O exemplo a seguir mostra como definir um par chave-valor:

    azure site config add <key>=<value> 

Para ver uma lista de todos os pares de chave/valor, use o seguinte:

    azure site config list 

Ou, se você souber a tecla e deseja ver o valor, você pode usar:

    azure site config get <key> 

Se você quiser alterar o valor de uma chave existente, você deve primeiro desmarcar a chave existente e, em seguida, adicioná-la novamente. O comando Limpar é:

    azure site config clear <key> 

###<a name="list-and-show-sites"></a>Sites de lista e slides

Para listar os seus sites, use o seguinte comando:

    azure site list

Para obter informações detalhadas sobre um site, use o `site show` comando. O exemplo a seguir mostra detalhes de `MySite`:

    azure site show MySite

###<a name="stop-start-or-restart-a-site"></a>Parar, iniciar ou reiniciar um site

Você pode parar, iniciar ou reiniciar um site com o `site stop`, `site start`, ou `site restart` comandos:

    azure site stop MySite
    azure site start MySite
    azure site restart MySite

###<a name="delete-a-site"></a>Excluir um site

Por fim, você pode excluir um site com o `site delete` comando:

    azure site delete MySite

Observe que, se você estiver executando qualquer um dos acima comandos de dentro da pasta onde você executou `site create`, você não precisa especificar o nome do site `MySite` como o último parâmetro.

Para ver uma lista completa de `site` comandos, use o `-help` opção:

    azure site -help 

<h2><a id="VMs"></a>Como criar e gerenciar uma máquina de Virtual do Azure</h2>

uma máquina Virtual de Azure é criado a partir de uma imagem de máquina virtual (um arquivo. vhd) que você fornecer ou que está disponível na Galeria de imagem. Para ver imagens que estão disponíveis, use o `vm image list` comando:

    azure vm image list

Você pode provisionar e iniciar uma máquina virtual de uma das imagens disponíveis com o `vm create` comando. O exemplo a seguir mostra como criar uma máquina virtual Linux (chamado `myVM`) de uma imagem na Galeria de imagem (CentOS 6.2). O nome de usuário de raiz e a senha para a máquina virtual são `myusername` e `Mypassw0rd` respectivamente. (Observe que o `--location` parâmetro especifica o Centro de dados no qual a máquina virtual é criada. Se você omitir o `--location` parâmetro, você será solicitado a escolher um local.)

    azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd myusername --location "West US"

Você pode considerar passando o `--ssh` sinalizador (Linux) ou `--rdp` sinalizador (Windows) para `vm create` para habilitar conexões remotas à máquina virtual recém-criado.

Se você preferir seria provisionar uma máquina virtual de uma imagem personalizada, você pode criar uma imagem de um arquivo. vhd com o `vm image create` comando e, em seguida, use o `vm create` comando provisionar a máquina virtual. O exemplo a seguir mostra como criar uma imagem de Linux (chamado `myImage`) de um arquivo. vhd local. (O `--location` parâmetro especifica os dados no qual a imagem está armazenada.)

    azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

Em vez de criar uma imagem de um VHD local, você pode criar uma imagem de um VHD armazenados em armazenamento de Blob do Azure. Você pode fazer isso com a `blob-url` parâmetro:

    azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

Depois de criar uma imagem, você pode provisionar uma máquina virtual da imagem usando `vm create`. O comando a seguir cria uma máquina virtual chamada `myVM` da imagem criada acima (`myImage`).

    azure vm create myVM myImage myusername --location "West US"

Depois que você tiver configurado uma máquina virtual, talvez você queira criar pontos de extremidade para permitir o acesso remoto à sua máquina virtual (por exemplo). O exemplo a seguir usa a `vm create endpoint` comando para abrir a porta externa 22 e porta local 22 em `myVM`:

    azure vm endpoint create myVM 22 22

Você pode obter informações detalhadas sobre uma máquina virtual (incluindo o endereço IP, nome DNS e informações de ponto de extremidade) com o `vm show` comando:

    azure vm show myVM

Desligar, iniciar, ou reiniciar a máquina virtual, use um dos seguintes comandos:

    azure vm shutdown myVM
    azure vm start myVM
    azure vm restart myVM

E finalmente, para excluir a máquina virtual, use o `vm delete` comando:

    azure vm delete myVM

Para obter uma lista completa dos comandos para criar e gerenciar máquinas virtuais, use o `-h` opção:

    azure vm -h

<!-- IMAGES -->
[Azure Web Site]: ./media/crossplat-cmd-tools/freetrial.png

<!-- LINKS -->
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/?LinkID=275464
[reference-docs]: http://go.microsoft.com/fwlink/?LinkId=252246
[windowsazuredotcom]: http://www.windowsazure.com

