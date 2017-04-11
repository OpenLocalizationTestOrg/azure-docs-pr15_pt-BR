<properties
    pageTitle="Instalar o RStudio com o servidor de R em HDInsight (visualização) | Microsoft Azure"
    description="Como instalar o RStudio com o servidor de R em HDInsight (visualização)."
    services="hdinsight"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/16/2016"
   ms.author="jeffstok"/>


# <a name="installing-rstudio-with-r-server-on-hdinsight-preview"></a>Instalação RStudio com o servidor de R em HDInsight (prévia)

Há vários ambientes de desenvolvimento integrado (IDE) disponíveis para R hoje, incluindo da Microsoft recentemente anunciado [R ferramentas para Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS), uma família de ferramentas de desktop e servidor de [RStudio](https://www.rstudio.com/products/rstudio-server/)ou de baseadas no Eclipse do Walware [StatET](http://www.walware.de/goto/statet). Entre os mais populares no Linux é o uso do [Servidor de RStudio](https://www.rstudio.com/products/rstudio-server/) que fornece um IDE baseadas no navegador para uso por clientes remotos.  Instalando o servidor de RStudio no nó borda de um cluster de HDInsight Premium fornece uma experiência IDE completa para o desenvolvimento e a execução de scripts de R com R servidor no cluster e pode ser consideravelmente mais produtiva do que o uso padrão do Console R.

Neste artigo, você aprenderá como instalar a versão de comunidade (gratuito) do servidor de RStudio no nó borda de um cluster usando um script personalizado. Se você preferir a versão de Pro comercialmente licenciada do servidor de RStudio, você deve seguir as instruções de instalação do [Servidor de RStudio](https://www.rstudio.com/products/rstudio/download-server/).

> [AZURE.NOTE] As etapas neste documento exigem um servidor de R em cluster HDInsight e não funcionarão corretamente se você estiver usando um cluster de HDInsight onde R foi instalado usando a [Ação de Script R instalar](hdinsight-hadoop-r-scripts-linux.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de Azurehdinsight com o servidor de R instalado. Para obter instruções, consulte [Introdução ao servidor de R em clusters de HDInsight](hdinsight-hadoop-r-server-get-started.md).
* Um cliente SSH. Para distribuições Linux e Unix ou Macintosh OS X, o `ssh` comando é fornecido com o sistema operacional. Para Windows, é recomendável [Cygwin](http://www.redhat.com/services/custom/cygwin/) com a [opção de OpenSSH](https://www.youtube.com/watch?v=CwYSvvGaiWU)ou [Acabamento](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  


## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>Instalar o RStudio em cluster usando um script personalizado

1. Identifica o nó de borda do cluster. Para um cluster de HDInsight com o servidor de R, o seguinte é a convenção de nomenclatura para nó principal e o nó de borda.

    * Nó de cabeça-`CLUSTERNAME-ssh.azurehdinsight.net`
    * Nó de borda-`R-Server.CLUSTERNAME-ssh.azurehdinsight.net` 

2. SSH para o nó de borda do cluster usando o padrão de nomenclatura acima. 
 
    * Se você estiver conectando a partir de um cliente Linux, consulte [conectar a um cluster de HDInsight baseados em Linux](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).
    * Se você estiver conectando a partir de um cliente do Windows, consulte [conectar a um cluster de HDInsight baseados em Linux usando acabamento](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster).

3. Quando você estiver conectado, se tornar um usuário raiz no cluster. Na sessão SSH, use o comando a seguir.

        sudo su -

4. Baixe o script personalizado para instalar o RStudio. Use o comando a seguir.

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. Alterar as permissões no arquivo de script personalizado e execute o script. Use os seguintes comandos.

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh

6. Se você usou uma senha SSH ao criar um cluster de HDInsight com o servidor de R, você pode ignorar esta etapa e passar para a próxima. Se você usar uma chave SSH para criar o cluster, você deve definir uma senha para o usuário SSH. Você precisará dessa senha ao conectar com RStudio. Execute os seguintes comandos. Quando for solicitado **senha Kerberos atual**, pressione **ENTER**.  Observe que você deve substituir `USERNAME` com um usuário SSH para o seu cluster HDInsight.

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:
        
    Se sua senha está definida com êxito, você verá uma mensagem como esta.

        passwd: password updated successfully


    Sair da sessão SSH.

7. Criar um túnel SSH ao cluster mapeando `localhost:8787` no cluster HDInsight para o computador cliente. Você deve criar um túnel SSH antes de abrir uma nova sessão do navegador.

    * Em um cliente Linux ou um cliente Windows com [Cygwin](http://www.redhat.com/services/custom/cygwin/) , em seguida, abra uma sessão de terminal e use o comando a seguir.

            ssh -L localhost:8787:localhost:8787 USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net
            
        Substitua o **nome de usuário** com um usuário SSH para o seu cluster HDInsight e **CLUSTERNAME** com o nome do seu cluster HDInsight você também pode usar uma chave SSH em vez de uma senha, adicionando`-i id_rsa_key`     

    * Se usando um cliente Windows com acabamento então

        1.  Abra Acabamento e insira suas informações de conexão. Se você não estiver familiarizado com acabamento, consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obter informações sobre como usá-lo com HDInsight.
        2.  Na seção **categoria** à esquerda da caixa de diálogo, expanda a **Conexão**, expanda **SSH**e selecione **túneis**.
        3.  Forneça as seguintes informações no formulário de **Opções que controlam o encaminhamento de porta SSH** :

            * **Porta de origem** - a porta no cliente que você deseja encaminhar. Por exemplo, **8787**.
            * **Destino** - o destino que deve ser mapeado para o computador cliente local. Por exemplo, **localhost:8787**.

            ![Criar um túnel SSH] (./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "Criar um túnel SSH")

        4. Clique em **Adicionar** para adicionar as configurações e clique em **Abrir** para abrir uma conexão SSH.
        5. Quando solicitado, faça logon no servidor. Isso estabelecer uma sessão SSH e habilitar o túnel.

8. Abra um navegador da web e digite a URL a seguir com base na porta inserido para o túnel.

        http://localhost:8787/ 

9. Você será solicitado a digitar o nome de usuário SSH e a senha para se conectar ao cluster. Se você usou uma chave SSH durante a criação do cluster, você deve digitar a senha que você criou na etapa 5 acima.

    ![Conectar-se ao R Studio] (./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "Criar um túnel SSH")

10. Para testar se a instalação de RStudio foi bem-sucedida, você pode executar um teste script que executa R com base MapReduce e Spark trabalhos em cluster. Volte ao console SSH e digite os seguintes comandos para baixar o script de teste para ser executado em RStudio.

    * Se você criou um cluster de Hadoop com R, use este comando.
        
            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r

    * Se você criou um cluster Spark com R, use este comando.

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

11. RStudio, você verá o script de teste que você baixou. Clique duas vezes no arquivo para abri-lo, selecione o conteúdo do arquivo e clique em **Executar**. Você deve ver o resultado no painel do **Console** .
 
    ![Teste a instalação] (./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "Teste a instalação")

Outra opção seria digitar `source(testhdi.r)` ou `source(testhdi_spark.r)` para executar o script.

## <a name="see-also"></a>Consulte também

- [Calcular opções de contexto do servidor de R em clusters de HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)

- [Azure opções de armazenamento do servidor de R em HDInsight premium](hdinsight-hadoop-r-server-storage.md)


 
