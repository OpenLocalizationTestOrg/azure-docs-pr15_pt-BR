<properties 
    pageTitle="Como instalar o Apache Qpid massa do próton-C em uma VM Linux | Microsoft Azure"
    description="Como criar uma máquina virtual Linux de CentOS usando máquinas virtuais do Azure e como criar e instalar a biblioteca Apache Qpid massa do próton-C."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="install-apache-qpid-proton-c-on-an-azure-linux-vm"></a>Instalar o Apache Qpid massa do próton-C em uma VM Linux Azure

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Esta seção mostra como criar uma máquina virtual Linux de CentOS usando máquinas virtuais do Azure e como baixar, criar e instalar a biblioteca Apache Qpid massa do próton-C juntamente com as ligações de idioma Python e PHP. Após concluir essas etapas, você poderá executar as amostras de Python e PHP incluídas neste guia.

A primeira etapa é executada usando o [portal de clássico Azure][]. A captura de tela a seguir mostra como uma máquina virtual de CentOS denominada "scott-centos" é criada:

![Massa do próton em uma VM Linux Azure][0]

Após a configuração, o portal exibe o seguinte:

![Massa do próton em uma VM Linux Azure][1]

Para fazer logon no computador, você deve saber a porta de ponto de extremidade para o SSH. Você pode obter esse valor a partir do [portal clássico Azure][] selecionando a máquina virtual recém-criado e clicando na guia **pontos de extremidade** . A captura de tela a seguir mostra a porta SSH pública para este computador está 57146.

![Massa do próton em uma VM Linux Azure][2]

Agora você pode se conectar ao computador usando o SSH. Este exemplo usa a ferramenta de acabamento, como a captura de tela a seguir:

![Massa do próton em uma VM Linux Azure][3]

Para aplicativos Python e PHP, este exemplo usa as bibliotecas de cliente de massa do próton do Apache. Essas bibliotecas estão disponíveis para download de [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). O arquivo Leiame no pacote de distribuição explica as etapas necessárias para instalar as dependências e construa massa do próton. Aqui está um resumo das etapas:

1.  Editar o arquivo de configuração de yum (/ etc/yum.conf) e comente a exclusão atualizações a cabeçalhos de núcleo (\# excluir = núcleo\*). Isso é necessário para instalar o compilador gcc.

2.  Instale os pacotes de pré-requisito:

    ```
    # required dependencies 
    yum install gcc cmake libuuid-devel
    
    # dependencies needed for ssl support
    yum install openssl-devel
    
    # dependencies needed for bindings
    yum install swig python-devel ruby-devel php-devel java-1.6.0-openjdk
    
    # dependencies needed for python docs
    yum install epydoc
    ```

1.  Baixe a biblioteca de massa do próton:

    ```
    [azureuser@this-user ~]$ wget http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
    --2016-04-17 14:45:03--  http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
    Resolving apache.panu.it (apache.panu.it)... 81.208.22.71
    Connecting to apache.panu.it (apache.panu.it)|81.208.22.71|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 868226 (848K) [application/x-gzip]
    Saving to: ‘qpid-proton-0.9.tar.gz’
    
    qpid-proton-0.9.tar.gz                               
    
    100%[====================================================================================================================>] 847.88K   102KB/s    in 8.4s    
    
    2016-04-17 14:45:12 (101 KB/s) - ‘qpid-proton-0.9.tar.gz’ saved [868226/868226]
    ```

1.  Extrai o código de massa do próton o arquivamento de distribuição:

    ```
    tar xvfz qpid-proton-0.9.tar.gz
    ```

1.  Criar e instalar o código usando as seguintes etapas, tiradas de arquivo Leiame:

    ```
    From the directory where you found this README file:    
    
    mkdir build cd build
            
    # Set the install prefix. You may need to adjust depending on your      
    # system.       
    cmake -DCMAKE\_INSTALL\_PREFIX=/usr ..
            
    # Omit the docs target if you do not wish to build or install       
    # documentation.        
    make all docs
            
    # Note that this step will require root privileges.     
    make install
    ```

Após executar essas etapas, massa do próton é instalado no computador e pronto para uso.

## <a name="next-steps"></a>Próximas etapas

Pronto para saber mais? Visite o link a seguir:

- [Visão geral do serviço barramento AMQP][]

[Visão geral do serviço barramento AMQP]: service-bus-amqp-overview.md
[0]: ./media/service-bus-amqp-apache/amqp-apache-1.png
[1]: ./media/service-bus-amqp-apache/amqp-apache-2.png
[2]: ./media/service-bus-amqp-apache/amqp-apache-3.png
[3]: ./media/service-bus-amqp-apache/amqp-apache-4.png

[Azure portal clássico]: http://manage.windowsazure.com


