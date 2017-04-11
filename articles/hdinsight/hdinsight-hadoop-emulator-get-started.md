<properties
    pageTitle="Use uma área restrita Hadoop para saber mais sobre Hadoop | Microsoft Azure"
    description="Para começar a aprender sobre como usar o ecossistema Hadoop, você pode configurar uma área restrita Hadoop de Hortonworks em uma máquina virtual Azure. "
    keywords="emulador de Hadoop, área restrita do hadoop"
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    authors="nitinme"
    documentationCenter=""
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="nitinme"/>

# <a name="get-started-in-the-hadoop-ecosystem-with-a-hadoop-sandbox-on-a-virtual-machine"></a>Começar a usar o ecossistema Hadoop com um modo de seguro de Hadoop em uma máquina virtual

Saiba como instalar a área restrita Hadoop de Hortonworks em uma máquina virtual para saber mais sobre o ecossistema do Hadoop. A área restrita fornece um ambiente de desenvolvimento local para saber mais sobre Hadoop, o sistema de arquivo distribuído da Hadoop (HDFS) e o envio de trabalho.

## <a name="prerequisites"></a>Pré-requisitos

* [Oracle VirtualBox](https://www.virtualbox.org/)

Quando você estiver familiarizado com Hadoop, você pode começar a usar Hadoop no Azure, criando um cluster de HDInsight. Para obter mais informações sobre como começar, consulte [Introdução ao Hadoop em HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

## <a name="download-and-install-the-virtual-machine"></a>Baixe e instale a máquina virtual

1. De [http://hortonworks.com/downloads/#sandbox](http://hortonworks.com/downloads/#sandbox), selecione o item de __DOWNLOAD para VIRTUALBOX__ para 2,4 HDP na área restrita Hortonworks. Você será solicitado a registrar Hortonworks antes de começa o download.

    ![Imagem de link para baixar a área restrita de Hortonworks para VirtualBox](./media/hdinsight-hadoop-emulator-get-started/download-sandbox.png)

2. Da página da web, selecione o __Guia de instalação do VirtualBox__ para 2,4 HDP na área restrita Hortonworks. Isso irá baixar um PDF que contém instruções de instalação da máquina virtual.

    ![Exibir o guia de instalação](./media/hdinsight-hadoop-emulator-get-started/view-install-guide.png)

## <a name="start-the-virtual-machine"></a>Iniciar a máquina virtual

1. Iniciar VirtualBox, selecione a área restrita Hortonworks, selecione __Iniciar__e, em seguida, __Começar a Normal__.

    ![Iniciar normal](./media/hdinsight-hadoop-emulator-get-started/normal-start.png)

2. Quando a máquina virtual tiver terminado o processo de inicialização, ela exibirá instruções de logon. Abra um navegador da web e navegue até o URL exibido (geralmente http://127.0.0.1:8888).

## <a name="set-passwords"></a>Defina as senhas

1. Na etapa __Introdução__ da página de área restrita Hortonworks, selecione __Opções avançadas do modo de exibição__. Use as informações desta página para fazer login na área restrita usando SSH. Use o nome e a senha fornecidos.

    > [AZURE.NOTE] Se você não tiver um cliente SSH instalado, você pode usar o SSH baseado na web fornecido da máquina virtual em __http://localhost:4200 /__.

    Na primeira vez que você se conectar usando SSH, você será solicitado a alterar a senha da conta raiz. Insira uma nova senha, que será usada quando você fizer logon usando SSH no futuro.

2. Uma vez conectado, digite o seguinte comando:

        ambari-admin-password-reset
    
    Quando solicitado, forneça uma senha para a conta de administrador Ambari. Isso será usado quando você acessa a interface do usuário do Ambari Web.

## <a name="use-the-hive-command"></a>Use o comando de seção

1. Em uma conexão SSH ao modo seguro, use o seguinte comando para iniciar o shell de seção:

        hive

2. Depois que o shell for iniciado, use o seguinte para exibir as tabelas que são fornecidas com o modo seguro:

        show tables;

3. Use o seguinte para recuperar 10 linhas do `sample_07` tabela:

        select * from sample_07 limit 10;

## <a name="next-steps"></a>Próximas etapas

* [Saiba como usar o Visual Studio com a área restrita Hortonworks](hdinsight-hadoop-emulator-visual-studio.md)
* [Os ropes da área de segurança Hortonworks de aprendizagem](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Tutorial do Hadoop - Introdução ao HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)