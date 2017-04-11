<properties
pageTitle="Usar túnel SSH para acessar Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie e do outra web interface do usuário"
description="Saiba como usar um túnel SSH para procurar com segurança recursos da web hospedados em seu nós HDInsight baseados em Linux."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/17/2016"
ms.author="larryfr"/>

# <a name="use-ssh-tunneling-to-access-ambari-web-ui-jobhistory-namenode-oozie-and-other-web-uis"></a>Usar túnel SSH acessem web Ambari UI, JobHistory, NameNode, Oozie e do outra web interface do usuário

Clusters de HDInsight baseados em Linux fornecem acesso à web Ambari UI pela Internet, mas alguns recursos da interface do usuário não estão. Por exemplo, a interface do usuário web para outros serviços que forem reproduzidos por meio de Ambari. Para todas as funcionalidades da web Ambari da interface do usuário, você deve usar um túnel SSH ao cabeçalho cluster.

## <a name="what-requires-an-ssh-tunnel"></a>O que requer um túnel SSH?

Várias dos menus de Ambari não totalmente preencherá sem um túnel SSH, como eles dependem de sites e serviços expostos por outros serviços de Hadoop em execução no cluster. Muitas vezes, esses sites não são seguras, portanto, não é segura para expô-las diretamente na internet. Às vezes, o serviço executa o site em outro nó de cluster como um nó Zookeeper.

Estes são os serviços que usa web Ambari da interface do usuário, que não podem ser acessados sem um túnel SSH:

* JobHistory,
* NameNode,
* Pilhas de thread
* Web Oozie da interface do usuário
* Interface do usuário HBase mestre e Logs

Se você usar ações de Script para personalizar o seu cluster, todos os serviços ou utilitários instalados que expõem uma interface do usuário da web exigem um túnel SSH. Por exemplo, se você instalar matiz usando uma ação de Script, você deve usar um túnel SSH para acessar a web matiz interface do usuário.

## <a name="what-is-an-ssh-tunnel"></a>O que é um túnel SSH?

[Túnel SSH (Secure Shell)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) rotear o tráfego enviado a uma porta em sua estação de trabalho local, por meio de uma conexão SSH para seu nó principal do cluster de HDInsight, onde a solicitação é resolvida como se ela se originou no nó principal. A resposta é roteada depois volta pelo túnel sua estação de trabalho.

## <a name="prerequisites"></a>Pré-requisitos

Ao usar um túnel SSH para tráfego da web, você deve ter o seguinte:

* Um cliente SSH. Para distribuições Linux e Unix ou Macintosh OS X, o `ssh` comando é fornecido com o sistema operacional. Para Windows, é recomendável [Acabamento](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

    > [AZURE.NOTE] Se você deseja usar um cliente SSH diferente `ssh` ou acabamento, consulte a documentação de seu cliente sobre como estabelecer um túnel SSH.

* Um navegador da web que pode ser configurado para usar um proxy SOCKS

## <a name="usessh"></a>Criar um túnel usando o comando SSH

Use o seguinte comando para criar um SSH túnel usando o `ssh` comando. Substitua o __nome de usuário__ com um usuário SSH para o seu cluster HDInsight e __CLUSTERNAME__ com o nome do seu cluster HDInsight

    ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

Isso cria uma conexão que rotear o tráfego para a porta local 9876 ao cluster via SSH. As opções são:

* **9876 D** - a porta local que vai rotear o tráfego através do túnel.

* **C** - compactar todos os dados, pois o tráfego da web é principalmente texto.

* **2** - forçar SSH para experimentar o protocolo somente a versão 2.

* **p** - modo silencioso.

* **T** - alocação de tty pseudo-índice desativar, desde que estamos apenas encaminhando uma porta.

* **n** - impede a leitura dos STDIN, desde que estamos apenas encaminhando uma porta.

* **N** - não executar um comando remoto, desde que estamos apenas encaminhando uma porta.

* **f** - executado em segundo plano.

Se você configurou o cluster com uma chave SSH, talvez seja necessário usar o `-i` parâmetro e especifique o caminho para a chave SSH particular.

Após a conclusão do comando, o tráfego enviado a porta 9876 no computador local será roteada sobre Secure Sockets Layer (SSL) ao cluster nó de cabeçalho e parecem se originar lá.

## <a name="useputty"></a>Criar um túnel usando acabamento

Use as seguintes etapas para criar um túnel SSH usando acabamento.

1. Abra Acabamento e insira suas informações de conexão. Se você não estiver familiarizado com acabamento, consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obter informações sobre como usá-lo com HDInsight.

2. Na seção **categoria** à esquerda da caixa de diálogo, expanda a **Conexão**, expanda **SSH**e selecione **túneis**.

3. Forneça as seguintes informações no formulário de **Opções que controlam o encaminhamento de porta SSH** :

    * **Porta de origem** - a porta no cliente que você deseja encaminhar. Por exemplo, **9876**.

    * Endereço de **destino** - o SSH para o cluster HDInsight baseados em Linux. Por exemplo, **meucluster-ssh.azurehdinsight.net**.

    * **Dinâmico** - proxy SOCKS dinâmico permite que o roteamento.

    ![imagem de túnel opções](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Clique em **Adicionar** para adicionar as configurações e clique em **Abrir** para abrir uma conexão SSH.

5. Quando solicitado, faça logon no servidor. Isso estabelecer uma sessão SSH e habilitar o túnel.

## <a name="use-the-tunnel-from-your-browser"></a>Use o túnel em seu navegador

> [AZURE.NOTE] As etapas desta seção usam o navegador FireFox, como ela está disponível gratuitamente para sistemas Linux, Unix, Macintosh OS X e Windows. Outros navegadores modernos que suportam usando um proxy SOCKS funcionará bem.

1. Configure o navegador para usar **localhost:9876** como um proxy **SOCKS v5** . Aqui está a aparência as configurações do Firefox. Se você usou uma porta diferente 9876, altere a porta para usado:

    ![imagem das configurações do Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/socks.png)

    > [AZURE.NOTE] Selecionar **DNS remoto** resolverá solicitações de sistema de nome de domínio (DNS) usando o cluster HDInsight. Se esta for desmarcada, DNS será resolvido localmente.

2. Verifique se que o tráfego está sendo roteado pelo túnel por vising um site como [http://www.whatismyip.com/](http://www.whatismyip.com/) com as configurações de proxy habilitado e desabilitado no Firefox. Embora as configurações estão habilitadas, o endereço IP será para um computador no Microsoft Azure data center.

##<a name="verify-with-ambari-web-ui"></a>Verificar com Ambari web interface do usuário

Depois que o cluster tiver sido estabelecido, use as seguintes etapas para confirmar que você pode acessar web de serviço interfaces do usuário da Ambari Web:

1. No seu navegador, vá para http://headnodehost:8080. O `headnodehost` endereço será enviada pela túnel ao cluster e resolver para o headnode Ambari em execução no. Quando solicitado, digite o nome de usuário de administrador (admin) e a senha para o seu cluster. Você pode ser solicitado uma segunda vez por web Ambari da interface do usuário. Em caso afirmativo, digite novamente as informações.
    
    > [AZURE.NOTE] Ao usar o endereço de http://headnodehost:8080 para se conectar ao cluster, você está se conectando diretamente pela túnel para o nó principal que Ambari está em execução usando HTTP e comunicação é protegida usando o túnel SSH. ao se conectar pela internet sem o uso de um túnel, comunicação é protegida usando HTTPS. Para se conectar pela internet usando HTTPS, use https://CLUSTERNAME.azurehdinsight.net, onde __CLUSTERNAME__ é o nome do cluster.

2. Na interface do usuário do Ambari da Web, selecione HDFS na lista à esquerda da página.

    ![Imagem com HDFS selecionado](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. Quando as informações de serviço HDFS for exibidas, selecione __Links rápidos__. Será exibida uma lista de nós de cabeça de cluster. Selecione um de nós cabeça e selecione __NameNode UI__.

    ![Imagem com o menu de links rápidos expandido](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

    > [AZURE.NOTE] Se você tiver uma conexão de internet lenta ou o nó principal está muito ocupado, você pode obter um indicador de espera em vez de um menu quando você selecionar __Links rápidos__. Em caso afirmativo, aguarde um ou dois minutos para os dados a ser recebida do servidor e tente a lista novamente.
    >
    > Se você tiver um monitor com resolução inferior ou janela do navegador não estiver maximizada, algumas entradas no menu __Links rápidos__ podem ser cortadas pelo lado direito da tela. Em caso afirmativo, expanda o menu usando o mouse e use a tecla de seta para a direita para rolar a tela para a direita para ver o resto do menu.

4. Deverá aparecer uma página semelhante ao seguinte:

    ![Imagem da interface do usuário NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

    > [AZURE.NOTE] Observe que a URL para esta página; ele deve ser semelhante a __http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster__. Isso está usando o nome de domínio totalmente qualificado interno (FQDN) do nó e não é acessível sem usar um túnel SSH.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar e usar um túnel SSH, veja a seguir para obter informações sobre monitoramento e gerenciamento de seu cluster usando Ambari:

* [Gerenciar clusters HDInsight usando Ambari](hdinsight-hadoop-manage-ambari.md)

Para obter mais informações sobre como usar SSH com HDInsight, consulte o seguinte:

* [Use SSH com baseado em Linux Hadoop em HDInsight Linux, Unix ou dos X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
