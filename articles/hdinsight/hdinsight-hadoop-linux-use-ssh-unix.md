<properties
   pageTitle="Use as teclas SSH com baseados em Linux Hadoop dos X, Unix ou Linux | Microsoft Azure"
   description=" Você pode acessar HDInsight baseados em Linux usando Secure Shell (SSH). Este documento fornece informações sobre como usar o SSH com HDInsight de clientes OS X, Unix ou Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/13/2016"
   ms.author="larryfr"/>

#<a name="use-ssh-with-linux-based-hadoop-on-hdinsight-from-linux-unix-or-os-x"></a>Use SSH com baseados em Linux Hadoop em HDInsight dos X, Unix ou Linux

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) permite que você remotamente executar operações em clusters HDInsight baseados em Linux usando uma interface de linha de comando. Este documento fornece informações sobre como usar o SSH com HDInsight de clientes OS X, Unix ou Linux.

> [AZURE.NOTE] As etapas neste artigo consideram que você está usando um cliente OS X, Unix ou Linux. Estas etapas podem ser executadas em um cliente baseado no Windows, se você tiver instalado um pacote que fornece `ssh` e `ssh-keygen`, como [Bash no Ubuntu no Windows](https://msdn.microsoft.com/commandline/wsl/about).
>
> Se você não tiver SSH instalado em seu cliente baseado no Windows, use as etapas em [Use SSH com baseados em Linux HDInsight (Hadoop) do Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obter informações sobre como instalar e usar acabamento.

##<a name="prerequisites"></a>Pré-requisitos

* **SSH keygen** e **ssh** para clientes Linux, Unix e OS X. Este utilitários geralmente são fornecidos com o seu sistema operacional, ou disponíveis através do sistema de gerenciamento de pacote.

* Um navegador da web moderna que ofereça suporte a HTML5.

OR

* [CLI azure](../xplat-cli-install.md).

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

##<a name="what-is-ssh"></a>O que é SSH?

SSH é um utilitário para fazer logon no e executando remotamente, comandos em um servidor remoto. Com HDInsight baseados em Linux, SSH estabelece uma conexão criptografada para o headnode de cluster e fornece uma linha de comando que você usa para digite os comandos. Comandos são executados diretamente no servidor.

###<a name="ssh-user-name"></a>Nome de usuário SSH

Um nome de usuário SSH é o nome que você usa para se autenticar no cluster HDInsight. Quando você especifica um nome de usuário SSH durante a criação do cluster, esse usuário é criado em todos os nós do cluster. Depois que o cluster é criado, você pode usar esse nome de usuário para se conectar ao headnodes de cluster do HDInsight. De headnodes, você pode conectar a nós trabalhador individual.

###<a name="ssh-password-or-public-key"></a>Senha SSH ou chave pública

Um usuário SSH pode usar uma senha ou a chave pública para autenticação. Uma senha é apenas uma cadeia de caracteres de texto que você compõem, enquanto uma chave pública faz parte de uma chave criptográfica gerada para identificá-lo exclusivamente.

Uma chave é mais segura do que uma senha, no entanto, ele requer etapas adicionais para gerar a chave e você deve manter os arquivos que contém a chave em um local seguro. Se alguém obtiver acesso aos arquivos principais, eles acessem sua conta. Ou, se você perder os arquivos de chave, você não poderá fazer logon em sua conta.

Um par de chaves consiste em uma chave pública (que é enviada para o servidor de HDInsight) e uma chave privada (que é mantida em sua máquina do cliente.) Quando você se conecta ao servidor HDInsight usando SSH, cliente SSH usará a chave privada em sua máquina para autenticar com o servidor.

##<a name="create-an-ssh-key"></a>Criar uma chave SSH

Se você planeja usar chaves SSH com o cluster, use as informações a seguir. Se você planeja usar uma senha, você poderá ignorar esta seção.

1. Abra uma sessão de terminal e use o seguinte comando para ver se você tiver quaisquer chaves SSH existentes:

        ls -al ~/.ssh

    Procure os seguintes arquivos na lista de diretórios. Estas são nomes comuns para as chaves públicas SSH.

    * ID\_dsa.pub
    * ID\_ecdsa.pub
    * ID\_ed25519.pub
    * ID\_rsa.pub

2. Se você não deseja usar um arquivo existente, ou você tiver sem chaves SSH existentes, use o seguinte para gerar um novo arquivo:

        ssh-keygen -t rsa

    Você será solicitado para as seguintes informações:

    * O local do arquivo - o local padrão é ~/.ssh/id\_rsa.
    * Uma frase secreta - você será solicitado a inserir isso novamente.

        > [AZURE.NOTE] É altamente recomendável que você use uma senha segura para a chave. No entanto, se você esquecer a senha, é possível recuperá-lo.

    Depois que o comando estiver concluída, você terá dois novos arquivos, a chave privada (por exemplo, **id\_rsa**) e a chave pública (por exemplo, **id\_rsa.pub**).

##<a name="create-a-linux-based-hdinsight-cluster"></a>Criar um cluster HDInsight baseados em Linux

Ao criar um cluster HDInsight baseados em Linux, você deve fornecer a chave pública criada anteriormente. De clientes OS X, Unix ou Linux, existem duas maneiras de criar um cluster HDInsight:

* **Portal do Windows azure** - usa um portal baseado na web para criar o cluster.

* **CLI do Windows azure para Mac, Linux e Windows** - usa comandos de linha de comando para criar o cluster.

Cada um desses métodos irão requerer uma senha ou uma chave pública. Para obter informações completas sobre como criar um cluster HDInsight baseados em Linux, consulte [clusters de HDInsight baseados em Linux de provisão](hdinsight-hadoop-provision-linux-clusters.md).

###<a name="azure-portal"></a>Portal do Azure

Ao usar o [Portal do Windows Azure] [ preview-portal] para criar um cluster HDInsight baseados em Linux, você deve digitar um **Nome de usuário SSH**e selecione para inserir uma **senha** ou a **Chave pública SSH**.

Se você selecionar a **Chave pública SSH**, você pode cole a chave pública (contida no arquivo com a extensão **. pub** ) no campo __PublicKey SSH__ ou selecione __Selecionar um arquivo__ para procurar e selecione o arquivo de chave pública.

![Imagem de formulário solicitando de chave pública](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE] O arquivo de chave é simplesmente um arquivo de texto. O conteúdo deve aparecer semelhante ao seguinte:
> ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

Isso cria um logon para o usuário especificado, usando a senha ou a chave pública que você fornecer.

###<a name="azure-command-line-interface-for-mac-linux-and-windows"></a>Interface de linha de comando Azure para Mac, Linux e Windows

Você pode usar o [Windows Azure CLI para Mac, Linux e Windows](../xplat-cli-install.md) para criar um novo cluster usando o `azure hdinsight cluster create` comando.

Para obter mais informações sobre como usar este comando, consulte [clusters de provisão Hadoop Linux em HDInsight usando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md).

##<a name="connect-to-a-linux-based-hdinsight-cluster"></a>Conectar a um cluster HDInsight baseados em Linux

A partir de uma sessão de terminal, use o comando SSH para se conectar ao headnode cluster, fornecendo o nome de usuário e endereço:

* **Endereço SSH** - existem dois endereços que podem ser usados para se conectar a um cluster usando SSH:

    * **Conectar-se ao headnode**: O nome do cluster, seguido por **-ssh.azurehdinsight.net**. Por exemplo, **meucluster-ssh.azurehdinsight.net**.
    
    * **Conectar até o nó de borda**: se o cluster for Server R em HDInsight, o cluster irá conter também um nó de borda que pode ser acessado usando **RServer.CLUSTERNAME.ssh.azurehdinsight.net**, onde __CLUSTERNAME__ é o nome do cluster.

* **Nome de usuário** - o SSH nome de usuário fornecidas quando você criou o cluster.

O exemplo a seguir se conectará para o headnode principal de **meucluster** como o usuário **me**:

    ssh me@mycluster-ssh.azurehdinsight.net

Se você usou uma senha da conta de usuário, você será solicitado a inserir a senha.

Se você usou uma chave SSH é protegida com uma senha, você deverá inserir a senha. Caso contrário, SSH tentará autenticar automaticamente usando uma das chaves particulares locais em seu cliente.

> [AZURE.NOTE] Se SSH não autentica automaticamente com a chave particular correta, use o parâmetro **-i** e especifique o caminho para a chave privada. O exemplo a seguir carregará a chave privada do `~/.ssh/id_rsa`:
>
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

Se você está se conectando usando o endereço para o headnode e nenhuma porta for especificada, SSH será definido como a porta 22, que se conectará ao headnode principal no cluster HDInsight. Se você usar a porta 23, você se conectará ao secundário. Para saber mais sobre o headnodes, consulte [disponibilidade e a confiabilidade dos clusters Hadoop em HDInsight](hdinsight-high-availability-linux.md).

###<a name="connect-to-worker-nodes"></a>Conecte-se a nós do trabalhador

Os nós de trabalho não são diretamente acessíveis de fora do datacenter do Windows Azure, mas eles podem ser acessados no headnode cluster via SSH.

Se você usar uma chave SSH para autenticar sua conta de usuário, você deve concluir as etapas a seguir em seu cliente:

1. Usando um editor de texto, abra `~/.ssh/config`. Se esse arquivo não existir, você pode criá-la, digitando `touch ~/.ssh/config` no terminal.

2. Adicione o seguinte no arquivo. Substitua *CLUSTERNAME* o nome do cluster HDInsight.

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    Isto configura o agente SSH encaminhamento para o cluster HDInsight.

3. Testar o agente SSH encaminhamento usando o seguinte comando do terminal:

        echo "$SSH_AUTH_SOCK"

    Isso deve retornar informações semelhante ao seguinte:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Se nada for retornado, isso indica que esses **ssh agente** não está em execução. Consulte a documentação do sistema operacional para conhecer etapas específicas de instalar e configurar **ssh agente**ou consulte [usando ssh-agente com ssh](http://mah.everybody.org/docs/ssh).

4. Depois que você tiver verificado que **ssh agent** esteja em execução, use o seguinte para adicionar a chave privada do SSH para o operador:

        ssh-add ~/.ssh/id_rsa

    Se a sua chave privada é armazenada em um arquivo diferente, substitua `~/.ssh/id_rsa` com o caminho para o arquivo.

Use as etapas a seguir para conectar a nós do trabalhador para o cluster.

> [AZURE.IMPORTANT] Se você usar uma chave SSH para autenticar sua conta, você deve concluir as etapas anteriores para verificar que esses operadores encaminhamento está funcionando.

1. Conecte-se ao cluster HDInsight usando SSH, conforme descrito anteriormente.

2. Quando você estiver conectado, use o seguinte para recuperar uma lista de nós no cluster. Substitua *ADMINPASSWORD* a senha para sua conta de administrador de cluster. Substitua *CLUSTERNAME* o nome do cluster.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    Isso irá retornar informações no formato JSON para os nós do cluster, incluindo `host_name`, que contém o nome de domínio totalmente qualificado (FQDN) para cada nó. A seguir está um exemplo de um `host_name` entrada retornada pelo comando **curl** :

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

3. Depois que você tiver uma lista de nós de trabalhador que você deseja se conectar, use o seguinte comando da sessão SSH para o servidor para abrir uma conexão a um nó de trabalho:

        ssh USERNAME@FQDN

    Substitua o *nome de usuário* com seu nome de usuário SSH e *FQDN* com o FQDN para o nó de trabalhador. Por exemplo, `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.

    > [AZURE.NOTE] Se você usar uma senha para a autenticação sua sessão SSH, você será solicitado a inserir a senha novamente. Se você usar uma chave SSH, a conexão deve terminar sem quaisquer prompts.

4. Depois que a sessão tiver sido estabelecida, o prompt do terminal deixará de ser `username@hn#-clustername` para `username@wk#-clustername` para indicar que você está conectado ao nó trabalhador. Quaisquer comandos que você executa neste ponto serão executado no nó do trabalhador.

4. Quando terminar a execução de ações no nó profissional, use o `exit` comando fechar a sessão para o nó de trabalhador. Isso retornará ao `username@hn#-clustername` prompt.

## <a name="connect-to-a-domain-joined-hdinsight-cluster"></a>Conectar a um cluster HDInsight associados a um domínio

[Domínio HDInsight](hdinsight-domain-joined-introduction.md) integra Kerberos Hadoop em HDInsight. Como o usuário SSH não é um usuário de domínio do Active Directory, essa conta de usuário não pode executar Hadoop comandos do shell SSH em um cluster de domínio diretamente. Você deve executar *kinit* pela primeira vez. 

**Para executar o Hive consultas em um cluster de domínio HDInsight usando SSH**

1. Conecte-se a um cluster HDInsight domínio usando SSH.  Para instrocutions, consulte [conectar-se a um cluster HDInsight baseados em Linux](#connect-to-a-linux-based-hdinsight-cluster).
2. Execute kinit. Ele pedirá um nome de usuário de domínio e a senha de usuário de domínio. Para saber mais sobre configurar usuários do domínio para o domínio HDInsight clusters, consulte [Configurar domínio HDInisight clusters](hdinsight-domain-joined-configure.md).

    ![HDInsight Hadoop domínio kinit](./media/hdinsight-hadoop-linux-use-ssh-unix/hdinsight-domain-joined-hadoop-kinit.png)
3. Abra o Hive insira console por:

        hive

    Em seguida, você pode executar comandos de Hive.

##<a name="add-more-accounts"></a>Adicionar mais contas

1. Gere uma nova chave pública e a chave privada para a nova conta de usuário, conforme descrito na seção [criar uma chave SSH](#create-an-ssh-key-optional) .

    > [AZURE.NOTE] A chave privada deve ser gerada ou em um cliente que o usuário usará para se conectar ao cluster ou com segurança transferida para tal um cliente após a criação.

1. A partir de uma sessão SSH ao cluster, adicione novo usuário com o seguinte comando:

        sudo adduser --disabled-password <username>

    Isso irá criar uma nova conta de usuário, mas desabilitará a autenticação de senha.

2. Crie o diretório e os arquivos a serem mantenha pressionada a tecla usando os seguintes comandos:

        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

3. Quando o editor de nano for aberto, copie e cole o conteúdo da chave pública para a nova conta de usuário. Finalmente, use **Ctrl-X** para salvar o arquivo e saia do editor.

    ![imagem do editor de nano com chave de exemplo](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. Use o comando a seguir para alterar a propriedade da pasta .ssh e conteúdo para a nova conta de usuário:

        sudo chown -hR <username>:<username> /home/<username>/.ssh

5. Agora você deve ser capaz de autenticar para o servidor com a nova conta de usuário e a chave privada.

##<a id="tunnel"></a>Encapsulamento SSH

SSH pode ser usado para solicitações de locais, como solicitações da web, ao cluster HDInsight de túnel. A solicitação, em seguida, será roteada para o recurso solicitado como se tinha originou no headnode de cluster HDInsight.

> [AZURE.IMPORTANT] Um túnel SSH é um requisito para acessar a interface do usuário web para alguns serviços Hadoop. Por exemplo, o histórico de trabalho da interface do usuário ou o Gerenciador de recursos de interface do usuário só pode ser acessado usando um túnel SSH.

Para obter mais informações sobre como criar e usar um túnel SSH, consulte [Uso SSH encapsulamento para acessar a IU da web Ambari, ResourceManager, JobHistory, NameNode, Oozie e outro web na interface do usuário](hdinsight-linux-ambari-ssh-tunnel.md).

##<a name="next-steps"></a>Próximas etapas

Agora que você entende como autenticar usando uma chave SSH, saiba como usar MapReduce com Hadoop em HDInsight.

* [Use o Hive com HDInsight](hdinsight-use-hive.md)

* [Use porco com HDInsight](hdinsight-use-pig.md)

* [Use MapReduce trabalhos com HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/
