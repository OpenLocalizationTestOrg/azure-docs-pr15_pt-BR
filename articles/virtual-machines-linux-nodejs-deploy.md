<properties
   pageTitle="Implantar um aplicativo de Node para máquinas virtuais Linux no Azure"
   description="Saiba como implantar um aplicativo Node no Linux máquinas virtuais do Azure."
   services=""
   documentationCenter="nodejs"
   authors="stepro"
   manager="dmitryr"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/02/2016"
   ms.author="stephpr"/>

# <a name="deploy-a-nodejs-application-to-linux-virtual-machines-in-azure"></a>Implantar um aplicativo de Node para máquinas virtuais Linux no Azure

Este tutorial mostra como fazer um aplicativo node e implantá-lo em máquinas virtuais de Linux em execução no Azure. As instruções neste tutorial podem ser seguidas em qualquer sistema operacional que é capaz de executar Node.

Você vai aprender como:

- Bifurcação e clonar um repositório de GitHub contendo um aplicativo TODO simples;
- Criar e configurar duas máquinas virtuais de Linux no Azure para executar o aplicativo;
- Iteramos sobre o aplicativo pressionando atualizações à máquina virtual web front-end.

> [AZURE.NOTE]
> Para concluir este tutorial, você precisa de uma conta de GitHub e uma conta do Microsoft Azure e a capacidade de usar gito de uma máquina de desenvolvimento.

> Se você não tiver uma conta de GitHub, você pode se inscrever [aqui](https://github.com/join).

> Se você não tiver uma conta [Do Microsoft Azure](https://azure.microsoft.com/) , você pode inscrever um gratuito avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/). Isso também levará você pelo processo de inscrição para uma [Conta da Microsoft](http://account.microsoft.com) se você ainda não tiver um. Como alternativa, se você for um assinante do Visual Studio, você pode [ativar os benefícios do MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> Se você não tiver gito em sua máquina de desenvolvimento, se você estiver usando um computador Macintosh ou Windows, instale gito [aqui](http://www.git-scm.com). Se você estiver usando Linux, instale gito usando o mecanismo mais adequado para você, tais como `sudo apt-get install git`.

## <a name="forking-and-cloning-the-todo-application"></a>Divisão e clonagem o aplicativo TODO

O aplicativo de TODO usado por este tutorial implementa um frontend web simples sobre uma instância de MongoDB que controla os uma lista de tarefas Pendentes. Depois de entrar GitHub, vá [aqui](https://github.com/stepro/node-todo) para encontrar o aplicativo e bifurcação-la usando o link na parte superior direita. Isso deve criar um repositório em sua conta denominada *accountname*/node-todo.

Agora em sua máquina de desenvolvimento, clone este repositório:

    git clone https://github.com/accountname/node-todo.git

Usaremos este clonar local do repositório um pouco mais tarde quando fizer alterações ao código-fonte.

## <a name="creating-and-configuring-the-linux-virtual-machines"></a>Criar e configurar máquinas virtuais Linux

Azure possui excelente suporte para computação bruto usando máquinas virtuais Linux. Essa parte do tutorial mostra como você pode facilmente duas máquinas virtuais de Linux de rotação e implantar o aplicativo TODO neles, executando frontend na web em um nó e a instância de MongoDB na outra.

### <a name="creating-virtual-machines"></a>Criação de máquinas virtuais

A maneira mais fácil de criar uma nova máquina virtual no Azure é usar o Portal do Azure. Clique [aqui](https://portal.azure.com) para entrar e iniciar o Portal do Azure no navegador da web. Assim que o Portal do Azure tem carregada, conclua as seguintes etapas:

- Clique no link "+ nova";
- Escolha a categoria de "Computação" e escolha "Ubuntu servidor 14.04 LTS";
- Selecione o modelo de implantação de "gerente de recursos" e clique em "Criar";
- Preencha as Noções básicas seguindo estas diretrizes:
  - Especifique um nome que você pode identificar facilmente mais tarde;
  - Para este tutorial, escolha a autenticação de senha;
  - Crie um novo grupo de recursos com um nome de identificação.
- Para o tamanho de máquina Virtual, o "A1 padrão" é uma opção razoável para este tutorial.
- Para realizar configurações adicionais, verifique o tipo de disco está "Padrão" e aceite todos os padrões restantes.
- Iniciar a criação na página de resumo.

Execute o processo acima duas vezes para criar duas máquinas virtuais de Linux, uma para a frontend web e outra para a instância de MongoDB. Criação de máquinas virtuais levará cerca de 5 a 10 minutos.

### <a name="assigning-a-dns-entry-for-virtual-machines"></a>Atribuindo uma entrada de DNS para máquinas virtuais

Máquinas virtuais criadas no Azure são por padrão somente acessado por meio de um endereço IP público como 1.2.3.4. Vamos fazer as máquinas mais facilmente identificáveis designando-lhes entradas DNS.

Depois que o portal indica que as máquinas virtuais foram criadas, clique no link "Máquinas virtuais" na barra de navegação à esquerda e localize seus computadores. Para cada máquina:

- Localize a guia Essentials e clique no endereço IP público;
- Na configuração de endereço IP pública, atribua um rótulo de nome do DNS e salve.

O portal garantirá que o nome que você especificar está disponível. Depois de salvar a configuração, suas máquinas virtuais terão nomes de host semelhantes a `machinename.region.cloudapp.azure.com`.

### <a name="connecting-to-the-virtual-machines"></a>Conectar-se às máquinas virtuais

Quando suas máquinas virtuais foram provisionadas, foram previamente configurados para permitir conexões remotas via SSH. Este é o mecanismo que usaremos para configurar as máquinas virtuais. Se você estiver usando o Windows para o desenvolvimento, você precisará obter um cliente SSH se você ainda não tiver um. Uma opção comum aqui é acabamento, que pode ser obtido [aqui](http://www.chiark.greenend.org.uk/~sgtatham/putty/). Macintosh e Linux OSes vêm com uma versão do SSH pré-instalado.

### <a name="configuring-the-web-frontend-virtual-machine"></a>Configuração da máquina do Web Frontend Virtual

SSH na máquina de frontend web criados usando acabamento, ssh linha de comando ou outra ferramenta SSH favorita. Você verá uma mensagem de boas-vindas seguida por um prompt de comando.

Primeiro, vamos certificar-se de que gito e nó estão instalados:

    sudo apt-get install -y git
    curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
    sudo apt-get install -y nodejs
    
Como front-end do aplicativo web depende de alguns módulos Node nativos, precisamos também instalar o conjunto essencial de ferramentas de construção:

    sudo apt-get install -y build-essential

Finalmente, vamos instalar um aplicativo de Node chamado *indefinidamente*, que ajuda a executar aplicativos de servidor de node:

    sudo npm install -g forever
    
Estas são todas as dependências necessárias nesta máquina virtual sejam capazes de executar front-end do aplicativo da web, então vamos começar que a execução. Para fazer isso, primeiro vamos criar uma clonar vazio do repositório GitHub que você bifurcada anteriormente para que você pode facilmente publicar atualizações na máquina virtual (abordaremos neste cenário de atualização posteriormente) e, em seguida, clonar a clonar vazio para fornecer uma versão do repositório que realmente pode ser executada.

Iniciando a partir do diretório inicial (~), execute os seguintes comandos (substituindo *accountname* com seu nome de conta de usuário do GitHub):

    git clone --bare https://github.com/accountname/node-todo.git
    git clone node-todo.git

Agora, insira o diretório de nó todo e executar esses comandos:

    npm install
    forever start server.js
    
Agora está executando o front-end do aplicativo da web, no entanto há mais uma etapa antes de acessar o aplicativo de um navegador da web. A máquina virtual que você criou é protegida por um recurso Azure chamado de um *grupo de segurança de rede*, que foi criado quando você provisionado na máquina virtual. Atualmente, esse recurso permite apenas solicitações externas à porta 22 ser roteados para a máquina virtual, que permite a comunicação SSH com o computador, mas nada mais. Portanto para exibir o aplicativo TODO, que é configurado para executar na porta 8080, esta porta também precisa ser aberta.

Retorne ao Portal do Azure e conclua as etapas a seguir:

- Clique em "Grupos de recursos" na barra de navegação à esquerda;
- Selecione o grupo de recursos que contém sua máquina virtual;
- Na lista resultante de recursos, selecione o grupo de segurança de rede (aquele com um ícone de proteção);
- Nas propriedades, escolha "regras de segurança de entrada";
- Na barra de ferramentas, clique em "Adicionar";
- Forneça um nome como "padrão-permitir-todo";
- Definir o protocolo como "TCP";
- Definir o intervalo de porta de destino para "8080";
- Clique em Okey e aguarde a regra de segurança a ser criado.

Após criar esta regra de segurança, o aplicativo TODO é publicamente visível na internet e pode abri-la, por exemplo, usando uma URL, como:

    http://machinename.region.cloudapp.azure.com:8080

Você notará que, apesar de nós ainda não tiver configurado máquina virtual MongoDB, o aplicativo TODO parece estar muito funcional. Isso ocorre porque o repositório de origem é codificados para usar uma instância de MongoDB pré-instalado. Depois que temos configurado máquina virtual MongoDB, podemos será voltar e altere o código-fonte para utilizar nossa instância MongoDB particular, em vez disso.

### <a name="configuring-the-mongodb-virtual-machine"></a>Configuração da máquina Virtual de MongoDB

SSH para a segunda máquina criados usando acabamento, ssh linha de comando ou outra ferramenta SSH favorita. Após ver a mensagem de boas-vindas e o prompt de comando, instale o MongoDB (estas instruções foram obtidas [aqui](https://docs.mongodb.org/master/tutorial/install-mongodb-on-ubuntu/)):

    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927
    echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list
    sudo apt-get update
    sudo apt-get install -y mongodb-org

Por padrão, MongoDB está configurado para que só podem ser acessado localmente. Para este tutorial, podemos configurará MongoDB para que ele possa ser acessado de máquina de virtual do aplicativo. Em um contexto sudo, abra o arquivo /etc/mongod.conf e localize o `# network interfaces` seção. Alterar o `net.bindIp` valor de configuração para `0.0.0.0`.

> [AZURE.NOTE]
> Esta configuração é para os fins deste tutorial somente. **Não** é uma prática recomendada de segurança e não deve ser usado em ambientes de produção.

Agora, verifique se o serviço foi iniciado o MongoDB:

    sudo service mongod restart

MongoDB opera em porta 27017 por padrão. Portanto, da mesma forma que precisamos abrir porta 8080 na máquina virtual web frontend, precisamos abrir porta 27017 na máquina virtual MongoDB.

Retorne ao Portal do Azure e conclua as etapas a seguir:

* Clique em "Grupos de recursos" na barra de navegação à esquerda;
* Selecione o grupo de recursos que contém máquina virtual MongoDB;
* Na lista resultante de recursos, selecione o grupo de segurança de rede (aquele com um ícone de proteção) com o mesmo nome atribuído à máquina virtual MongoDB;
* Nas propriedades, escolha "regras de segurança de entrada";
* Na barra de ferramentas, clique em "Adicionar";
* Forneça um nome como "padrão-permitir-mongo";
* Definir o protocolo como "TCP";
* Definir o intervalo de porta de destino para "27017";
* Clique em Okey e aguarde a regra de segurança a ser criado.

## <a name="iterating-on-the-todo-application"></a>Iteração no aplicativo TODO
Até agora, podemos tiver provisionado duas máquinas virtuais de Linux: um que está executando o aplicativo web frontend e outro que está executando uma instância de MongoDB. Mas há um problema - frontend a web realmente não está usando a instância MongoDB provisionada ainda. Vamos corrigir isso atualizando o código de frontend web para usar uma variável de ambiente em vez de uma instância embutida.

### <a name="changing-the-todo-application"></a>Alterar o aplicativo TODO

Em sua máquina de desenvolvimento onde você clonar repositório nó todo primeiro, abra o `node-todo/config/database.js` de arquivo no seu editor favorito e altere o valor de url do valor embutido como `mongodb://...` para `process.env.MONGODB`.

Confirmar suas alterações e enviar para o mestre GitHub:

    git commit -am "Get MongoDB instance from env"
    git push origin master

Infelizmente, isso não publica a alteração à máquina virtual web front-end. Vamos fazer mais algumas alterações para aquela máquina virtual para ativar um mecanismo simple, mas eficaz para publicar as atualizações para que você pode observar rapidamente o efeito das alterações que ocorreram no ambiente ao vivo.

### <a name="configuring-the-web-frontend-virtual-machine"></a>Configuração da máquina do Web Frontend Virtual
Lembre-se de que anteriormente criamos uma clonar vazio do repositório do nó todo na máquina virtual web front-end. Acontece que esta ação criado um novo gito remoto aos quais alterações podem ser enviadas. No entanto, simplesmente insistem remoto nesse não bastante permite que o modelo de iteração rápida que os desenvolvedores procuram ao trabalhar em seu código.

O que queremos sejam capazes de fazer é garantir que, quando ocorre um envio para o repositório remoto na máquina virtual, o aplicativo TODO em execução é atualizado automaticamente. Felizmente, isso é fácil atingir com gito.

Gito expõe um número de fixação que são chamados em momentos específicos para reagir às ações tomadas no repositório. Estes são especificados usando scripts do shell do repositório `hooks` pasta. O gancho que é mais aplicável para o cenário de atualização automática é o `post-update` evento.

Em uma sessão SSH à máquina virtual web frontend, altere para o `~/node-todo.git/hooks` diretório e adicione o seguinte conteúdo em um arquivo denominado `post-update` (substituindo `machinename` e `region` com suas informações de máquina virtual do MongoDB):

    #!/bin/bash
    
    forever stopall
    unset 'GIT_DIR'
    export MONGODB="mongodb://machinename.region.cloudapp.azure.com:27017/tododb"
    cd ~/node-todo && git fetch origin && git pull origin master && npm install && forever start ~/node-todo/server.js
    exec git update-server-info
    
Certifique-se de que esse arquivo é executável executando o seguinte comando:

    chmod 755 post-update

Esse script garante que o aplicativo atual do servidor for interrompido, o código no repositório clonado é atualizado para o mais recente, quaisquer dependências atualizadas são satisfeitas e o servidor é reiniciado. Ele também garante que o ambiente tiver sido configurado em preparação para o recebimento de nossa primeira atualização do aplicativo para obter a instância MongoDB de uma variável de ambiente.

### <a name="configuring-your-development-machine"></a>Configurando sua máquina de desenvolvimento
Agora vamos em sua máquina de desenvolvimento conectada à máquina virtual web front-end. Isso é tão simple quanto adicionar repositório vazio na máquina virtual como um controle remoto. Execute o seguinte comando para fazer isso (substituindo *usuário* com seu nome de login de máquina virtual do web front-end e o *nome do computador* e a *região* conforme apropriado):

    git remote add azure user@machinename.region.cloudapp.azure.com:node-todo.git

Isso é tudo o que é necessário para habilitar insistem ou em vigor publicando, mude para máquina virtual web front-end.

### <a name="publishing-updates"></a>Atualizações de publicação

Vamos publicar uma alteração que foi feita até agora para que o aplicativo usará a nossa própria instância MongoDB:

    git push azure master

Você verá a saída semelhante a esta:

    Counting objects: 4, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (3/3), done.
    Writing objects: 100% (4/4), 406 bytes | 0 bytes/s, done.
    Total 4 (delta 1), reused 0 (delta 0)
    remote: info:    Forever stopped processes:
    remote: data:        uid  command         script    forever pid  id logfile                         uptime
    remote: data:    [0] 0Lyh /usr/bin/nodejs server.js 9064    9301    /home/username/.forever/0Lyh.log 0:0:3:17.487
    remote: From /home/username/node-todo
    remote:    5f31fd7..5bc7be5  master     -> origin/master
    remote: From /home/username/node-todo
    remote:  * branch            master     -> FETCH_HEAD
    remote: Updating 5f31fd7..5bc7be5
    remote: Fast-forward
    remote:  config/database.js | 2 +-
    remote:  1 file changed, 1 insertion(+), 1 deletion(-)
    remote: npm WARN package.json node-todo@0.0.0 No repository field.
    remote: npm WARN package.json node-todo@0.0.0 No license field.
    remote: warn:    --minUptime not set. Defaulting to: 1000ms
    remote: warn:    --spinSleepTime not set. Your script will exit if it does not stay up for at least 1000ms
    remote: info:    Forever processing file: /home/username/node-todo/server.js
    To username@machinename.region.cloudapp.azure.com:node-todo.git
    5f31fd7..5bc7be5  master -> master

Após concluir este comando, tente atualizar o aplicativo em um navegador da web. Você deve ser capaz de ver que a lista TODO apresentada aqui está vazia e não está mais associado à instância MongoDB implantada compartilhada.

Para concluir o tutorial, vamos alterar outro, mais visíveis. Em sua máquina de desenvolvimento, abra o arquivo de node-todo/public/index.html usando o editor favorito. Localize o cabeçalho de jumbotron e alterar o título de "Mantenho um Todo-aholic" para "Mantenho um Todo-aholic no Azure!".

Agora vamos confirmar:

    git commit -am "Azurify the title"

Esta vez, vamos publicar a alteração no Azure antes de enviar para voltar para a repo GitHub:

    git push azure master

Uma vez concluída a esse comando, atualize a página da web e você verá as alterações. Desde que elas forem adequadas, envio a alteração de volta para a origem remoto: 

    git push origin master

## <a name="next-steps"></a>Próximas etapas
Este artigo mostrava como fazer um aplicativo node e implantá-lo em máquinas virtuais de Linux em execução no Azure. Para saber mais sobre máquinas virtuais de Linux no Azure, consulte [Introdução aos Linux no Azure](/documentation/articles/virtual-machines-linux-introduction/).
    
Para obter mais informações sobre como desenvolver aplicativos Node no Azure, consulte o [Node Developer Center](/develop/nodejs/).
