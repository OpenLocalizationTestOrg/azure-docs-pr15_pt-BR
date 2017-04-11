<properties
    pageTitle="Configurar o Apache Tomcat em uma máquina virtual Linux | Microsoft Azure"
    description="Saiba como configurar Apache Tomcat7 usando uma Azure virtuais de máquina executando Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="NingKuang"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="ningk"/>

#<a name="how-to-set-up-tomcat7-on-a-linux-virtual-machine-with-microsoft-azure"></a>Como configurar Tomcat7 em um computador de Virtual Linux com o Microsoft Azure

Apache Tomcat (ou simplesmente Tomcat, antigo também Jakarta Tomcat) é um servidor da web de código-fonte aberto e o contêiner de servlet desenvolvido pela Apache Software Foundation ASF. Tomcat implementa Servlet Java e as especificações de JavaServer Pages (JSP) da Microsystems DOM e fornece um ambiente de servidor de web HTTP Java puro no qual executar código Java. Na configuração mais simples, Tomcat é executado em um processo único sistema operacional. Esse processo é executado em uma máquina virtual de Java (JVM). Cada solicitação HTTP de um navegador para Tomcat é processada como um segmento separado no processo Tomcat.  

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Neste guia, você irá instalar tomcat7 em uma imagem de Linux e implantá-lo no Microsoft Azure.  

Você aprenderá:  

-   Como criar uma máquina virtual no Azure.
-   Como preparar a máquina virtual para tomcat7.
-   Como instalar tomcat7.

Ele será considerado que o leitor já tenha uma assinatura do Azure.  Se não você pode se inscrever para uma avaliação gratuita em [http://azure.microsoft.com](https://azure.microsoft.com/). Se você tiver uma assinatura do MSDN, consulte [preços especiais do Microsoft Azure: MSDN, MPN e benefícios de Bizspark](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Para saber mais sobre o Azure, consulte [o que é Azure?](https://azure.microsoft.com/overview/what-is-azure/).

Este tópico pressupõe que você tenha conhecimento de trabalho básico do tomcat e Linux.  

##<a name="phase-1-create-an-image"></a>Fase 1: Criar uma imagem
Nesta fase, você irá criar uma máquina virtual usando uma imagem de Linux no Azure.  

###<a name="step-1-generate-an-ssh-authentication-key"></a>Etapa 1: Gerar uma chave de autenticação SSH
SSH é uma ferramenta importante para administradores do sistema. Entretanto, configurando a segurança do access com base em uma senha determinado humanos não é uma prática recomendada. Usuários mal-intencionados podem quebrar em seu sistema com base em um nome de usuário e uma senha fraca.

A boa notícia é que há uma maneira de deixar o acesso remoto aberta e não precisa se preocupar sobre senhas. O método consiste em autenticação com criptografia assimétrica. Chave privada do usuário é aquele que concede a autenticação. Você ainda pode bloquear a conta do usuário para não permitir a autenticação de senha completamente.

Outra vantagem desse método é que você não precisa senhas diferentes para fazer logon para servidores diferentes. Você pode autenticar usando a chave privada pessoa em todos os servidores, que impede que você precisar se lembrar de várias senhas.

Também é possível fazer logon com exigir uma senha com este método.

Siga estas etapas para gerar a chave de autenticação SSH.

1.  Baixe e instale o puttygen do seguinte local: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.  Execute PUTTYGEN. EXE.
3.  Clique em **Gerar** para gerar chaves. No processo, você pode aumentar aleatoriedade movendo o mouse sobre a área em branco na janela.  
![][1]
4.  Após o processo de gerar, Puttygen.exe mostrará sua chave gerada. Por exemplo:  
![][2]
5.  Selecione e copie a chave pública na **chave** e salvá-lo em um arquivo denominado publicKey.pem. Não clique em **Salvar chave pública**, porque o formato de arquivo da chave pública salvo é diferente da chave pública que queremos.
6.  Clique em **Salvar chave privada** e salve-o em um arquivo denominado privateKey.ppk.

###<a name="step-2-create-the-image-in-the-azure-portal"></a>Etapa 2: Crie a imagem no portal do Azure.
No [portal do Azure](https://portal.azure.com/), clique em **novo** na barra de tarefas para criar uma imagem, escolhendo a imagem de Linux com base nas suas necessidades. O exemplo a seguir usa a imagem 14.04 Ubuntu.
![][3]

Para **Nome do Host** especifique o nome para a URL que clientes de Internet e você usará para acessar esta máquina virtual. Defina a última parte do nome do DNS, por exemplo tomcatdemo, e Azure gerará a URL como tomcatdemo.cloudapp.net.  

Para **Chave de autenticação SSH**, copie o valor da chave do arquivo **publicKey.pem** , que contém a chave pública gerada pelo puttygen.  
![][4]

Definir outras configurações, conforme necessário e clique em criar.  

##<a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>Fase 2: Preparar sua máquina virtual para Tomcat7
Nesta fase, você irá configurar um ponto de extremidade para o tráfego de tomcat e conecte-se à sua nova máquina virtual.
###<a name="step-1-open-the-http-port-to-allow-web-access"></a>Etapa 1: Abrir a porta HTTP para permitir o acesso de web
Pontos de extremidade no Azure consistem em um protocolo (TCP ou UDP), junto com uma porta pública e privada. A porta particular é a porta que o serviço é listening na máquina virtual. A porta pública é a porta que o serviço de nuvem Azure está aguardando no externamente tráfego de entrada, baseados na Internet.  

Porta 8080 TCP é o número da porta padrão na qual ouve tomcat. Abrir essa porta com um ponto de extremidade Azure permitirá que você e outro clientes acesso de Internet tomcat páginas.  

1.  No portal do Azure, clique em **Procurar** -> **Máquina Virtual**e clique na máquina virtual que você criou.  
![][5]
2.  Para adicionar um ponto de extremidade à sua máquina virtual, clique na caixa de **pontos de extremidade** .
![][6]
3.  Clique em **Adicionar**.  
    1.  O **ponto de extremidade**, digite um nome para o ponto de extremidade no ponto de extremidade e, em seguida, digite 80 na **Porta pública**.  

        Se você definir a 80, não é necessário incluir o número da porta no URL que permite que você acesse tomcat. Por exemplo, http://tomcatdemo.cloudapp.net.    

        Se você configurá-lo para outro valor, como 81, você precisa adicionar o número da porta para a URL para acessar o tomcat. Por exemplo, http://tomcatdemo.cloudapp.net:81 /.
    2.  Digite 8080 porta particular. Por padrão, tomcat ouve na porta 8080 TCP. Se você tiver alterado o padrão ouvir a porta do tomcat, você deve atualizar porta particular para ser que a mesma que tomcat ouvir a porta.  
    ![][7]

4.  Clique em **Okey** para adicionar o ponto de extremidade à sua máquina virtual.



###<a name="step-2-connect-to-the-image-you-created"></a>Etapa 2: Conecte-se para a imagem que você criou.
Você pode escolher qualquer ferramenta SSH para se conectar à sua máquina virtual. Neste exemplo, usamos acabamento.  

Primeiro, obtenha o nome DNS da sua máquina virtual do portal do Azure. **Clique em Procurar** -> o nome da sua máquina virtual ->**máquinas virtuais** -> **Propriedades**e então veja no campo **Nome de domínio** do ladrilho **Propriedades** .  

Obter o número da porta para conexões SSH do campo **SSH** . Aqui está um exemplo.  
![][8]

Baixe o acabamento da [aqui](http://www.putty.org/) .  

Após o download, clique no arquivo executável ACABAMENTO. EXE. Configurar as opções básicas com o nome de host e porta número obtido das propriedades de sua máquina virtual. Aqui está um exemplo:  
![][9]

No painel esquerdo, clique em **Conexão** -> **SSH** -> **Auth** e clique em **Procurar** para especificar o local do arquivo **privateKey.ppk** , que contém a chave privada gerados pelo puttygen na fase 1: criar uma imagem. Aqui está um exemplo:  
![][10]

Clique em **Abrir**. Você pode ser alertado por uma caixa de mensagem. Se você configurou o nome DNS e número da porta corretamente, clique em **Sim**.
![][11]  


Você verá o seguinte:  
![][12]

Insira o nome de usuário especificado quando você criou a máquina virtual na fase 1: criar uma imagem. Você verá algo semelhante ao seguinte:  
![][13]





##<a name="phase-3-install-software"></a>Fase 3: Instalar o software
Nesta fase, você pode instalar o ambiente de runtime Java, tomcat e outros componentes do tomcat.  

###<a name="java-runtime-environment"></a>Ambiente de tempo de execução Java
Tomcat é escrito em Java. Existem dois tipos de Kits de desenvolvimento de Java (JDKs) (OpenJDK e Oracle JDK) e você pode escolher aquele que deseja.  

>AZURE. Observação: As duas JDKs têm quase o mesmo código para as classes na API do Java, mas o código para a máquina virtual é realmente diferente. Quando se trata de bibliotecas, OpenJDK tende a usar bibliotecas abertas enquanto Oracle tende a usar aquelas fechada. Mas JDK Oracle tem mais classes e alguns fixo bugs e Oracle JDK é mais estável que OpenJDK.

Os seguintes comandos baixar os JDKs diferentes.  

Abrir jdk   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  

jdk Oracle  

-   Para baixar o JDK de site do Oracle:  

        wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  

-   Para criar uma pasta para conter os arquivos JDK:  

        sudo mkdir /usr/lib/jvm  

-   Para extrair os arquivos JDK no/usr/biblioteca/jvm/diretório:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  

-   Para definir JDK Oracle como o padrão JVM:  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  
        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

####<a name="test"></a>Teste:
Você pode usar um comando semelhante ao seguinte para testar se o ambiente de tempo de execução Java está instalado corretamente:  

    java -version  

Se você instalou jdk abrir, você verá uma mensagem como a seguinte:![][14]

Se você instalou oracle jdk, você verá uma mensagem como a seguinte:![][15]

###<a name="tomcat7"></a>Tomcat7
Usando o seguinte comando para instalar o tomcat7:  

    sudo apt-get install tomcat7  

Se você não estiver usando tomcat7, use a variação apropriada deste comando.  

####<a name="test"></a>Teste:

Para verificar se tomcat7 está instalado com êxito, navegue até o nome DNS do seu servidor tomcat (http://tomcatexample.cloudapp.net/ é a URL de exemplo neste artigo). Se você vir uma página como a seguir, você terá tomcat7 instalado correto.
![][16]


###<a name="install-other-tomcat-components"></a>Instalar outros componentes Tomcat
Há outros componentes tomcat opcionais que você também pode instalar.  

Use o comando **tomcat7 de pesquisa de cache de chance de sudo** para ver todos os componentes disponíveis. Os comandos a seguir são exemplos para instalar algumas partes úteis.  

    sudo apt-get install tomcat7-admin      #admin web applications
    sudo apt-get install tomcat7-user         #tools to create user instances  

##<a name="phase-4-configure-tomcat"></a>Etapa 4: Configurar Tomcat
Nesta fase, você administrar tomcat.
###<a name="start-and-stop-tomcat7"></a>Iniciar e parar tomcat7
O servidor de tomcat7 iniciará automaticamente durante a instalação. Você também pode iniciá-lo por conta própria com o seguinte comando:   

    sudo /etc/init.d/tomcat7 start

Para parar de tomcat7:  

    sudo /etc/init.d/tomcat7 stop

Para exibir o status de tomcat7:  

    sudo /etc/init.d/tomcat7 status

Para reiniciar serviços tomcat:  

    sudo /etc/init.d/tomcat7 restart

###<a name="tomcat-administration"></a>Administração de Tomcat
Você pode editar o arquivo de configuração de usuário do Tomcat para configurar suas credenciais de administrador com o seguinte comando:  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Aqui está um exemplo:  
![][17]  

>AZURE. Observação: Crie uma senha forte para o nome de usuário de administrador.  

Depois de editar este arquivo, você deve reiniciar serviços de tomcat7 com o seguinte comando para garantir que as alterações entrem em vigor:  

    sudo /etc/init.d/tomcat7 restart  

Abra o navegador e digite a URL **http://<your tomcat server DNS name>/gerente/html**. Para o exemplo neste artigo, a URL é http://tomcatexample.cloudapp.net/manager/html.  

Depois de conectar, você verá algo semelhante ao seguinte:  
![][18]

##<a name="common-issues"></a>Problemas comuns

###<a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Não é possível acessar a máquina virtual com Tomcat e Moodle da Internet

-   **Sintoma**  
Tomcat está em execução, mas você não consegue ver a página de padrão Tomcat com seu navegador.
-   **Possível causa**   
    1.  A porta de ouvir tomcat não é igual a porta particular do ponto de extremidade da sua máquina virtual para o tráfego de tomcat.  

        Verifique suas configurações de ponto de extremidade de porta pública e privada e verifique se que a porta de particular é que igual o tomcat ouvir a porta. Consulte a fase 1: Criar uma imagem para obter instruções sobre como configurar pontos de extremidade para sua máquina virtual.  

        Para determinar a porta de ouvir tomcat, abra /etc/httpd/conf/httpd.conf (lançamento Red Hat) ou /etc/tomcat7/server.xml (lançamento Debian). Por padrão, a porta de ouvir tomcat é 8080. Aqui está um exemplo:  

            <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"  URIEncoding="UTF-8"            redirectPort="8443" />  

        Se você estiver usando uma máquina virtual como Debian ou Ubuntu e você deseja alterar a porta do padrão de Tomcat ouvir (por exemplo 8081), você também deve abrir a porta para o sistema operacional. Primeiro, abra o perfil:  

            sudo vi /etc/default/tomcat7  

        Em seguida, remova os comentários da última linha e altere "não" para "Sim".  

            AUTHBIND=yes

    2.  O firewall desabilitou a porta ouvir do tomcat.

        Se você só pode ver a página de padrão Tomcat do host local, o problema é mais provável que a porta que é ouviu por tomcat está bloqueada pelo firewall. Você pode usar a ferramenta de w3m para procurar a página da web. Os seguintes comandos instalar w3m e navegue até a página de padrão Tomcat:  

            sudo yum  install w3m w3m-img
            w3m http://localhost:8080  

-   **Solução**
    1. Se o tomcat ouvir porta não é igual a porta particular do ponto de extremidade para o tráfego na máquina virtual, você precisa alterar a porta particular para ser que a mesma que tomcat ouvir a porta.   

    2.  Se o problema é causado pelo firewall/iptables, adicione as seguintes linhas para /etc/sysconfig/iptables:  

            -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
            -A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

        Observe que a segunda linha só é necessária para tráfego https.  

        Verifique se é acima de todas as linhas que globalmente seriam restringir o acesso, como o seguinte:  

            -A INPUT -j REJECT --reject-with icmp-host-prohibited  

        Para recarregar a iptables, execute o seguinte comando:  

            service iptables restart  

        Isso foi testado no CentOS 6.3.

###<a name="permission-denied-when-upload-you-project-files-to-varlibtomcat7webapps"></a>Permissão negado quando carregar arquivos para /var/lib/tomcat7/webapps você projeto /  

-   **Sintoma**  
Quando você usa qualquer cliente SFTP (como FileZilla) para conectar-se a sua máquina virtual e navegue até /var/lib/tomcat7/webapps/publicar seu site, você receberá uma mensagem de erro semelhante ao seguinte:  

        status: Listing directory /var/lib/tomcat7/webapps
        Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
        Error:  /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
        Error:  File transfer failed

-   **Possível causa** Você tem sem permissões para acessar a pasta de /var/lib/tomcat7/webapps.  
-   **Solução**  
Você precisa obter permissão de conta raiz. Você pode alterar a propriedade dessa pasta da raiz para o nome de usuário que você usou ao provisionar máquina. Aqui está um exemplo com o nome da conta de azureuser:  

        sudo chown azureuser -R /var/lib/tomcat7/webapps

    Use a opção -R para aplicar as permissões para todos os arquivos dentro de um diretório muito.  

    Observe que este comando também funciona para diretórios. A opção -R altera as permissões para todos os arquivos e pastas dentro do diretório. Aqui está um exemplo:  

        sudo chown -R username:group directory  

    Este comando altera a propriedade (usuário e grupo) para todos os arquivos e pastas dentro de diretório e em si.  

    O comando a seguir somente altera a permissão do diretório pasta, mas deixa os arquivos e pastas dentro do diretório sozinho.  

        sudo chown username:group directory



[1]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png
