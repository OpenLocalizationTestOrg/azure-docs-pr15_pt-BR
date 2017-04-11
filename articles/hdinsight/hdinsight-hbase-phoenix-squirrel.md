<properties 
   pageTitle="Use Apache rio e esquilo em HDInsight | Microsoft Azure" 
   description="Saiba como usar rio Apache no HDInsight e como instalar e configurar esquilo em sua estação de trabalho para se conectar a um cluster de HBase em HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="use-apache-phoenix-and-squirrel-with-windows-based-hbase-clusters-in-hdinsight"></a>Use Apache rio e esquilo com clusters baseados no Windows HBase no HDinsight  

Saiba como usar [Rio Apache](http://phoenix.apache.org/) no HDInsight e como instalar e configurar esquilo em sua estação de trabalho para se conectar a um cluster de HBase em HDInsight. Para obter mais informações sobre rio, consulte [Rio em 15 minutos ou menos](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Para a gramática rio, consulte [Rio gramática](http://phoenix.apache.org/language/index.html).

>[AZURE.NOTE] As informações de versão do rio no HDInsight, consulte [Novidades nas versões Hadoop cluster fornecidas pelo HDInsight?] [hdinsight-versions].
>
> As informações neste documento são específicas para clusters HDInsight baseados no Windows. Para obter informações sobre como usar rio em HDInsight baseado em Linux, consulte [Usar Apache rio com clusters baseados em Linux HBase no HDinsight](hdinsight-hbase-phoenix-squirrel-linux.md).

##<a name="use-sqlline"></a>Use SQLLine
[SQLLine](http://sqlline.sourceforge.net/) é um utilitário de linha de comando para executar SQL. 

###<a name="prerequisites"></a>Pré-requisitos
Antes de poder usar SQLLine, você deve ter o seguinte:

- **HBase um cluster em HDInsight**. Para obter informações sobre como provisionar HBase cluster, consulte [Introdução ao Apache HBase em HDInsight][hdinsight-hbase-get-started].
- **Conectar-se ao cluster HBase através do protocolo de desktop remoto**. Para obter instruções, consulte [Gerenciar Hadoop clusters em HDInsight usando o Portal de clássico do Azure][hdinsight-manage-portal].

**Para descobrir o nome do host**

1. Abrir a **linha de comando do Hadoop** da área de trabalho.
2. Execute o seguinte comando para obter o sufixo DNS:

        ipconfig

    Anote o **Sufixo DNS específico da Conexão**. Por exemplo, *myhbasecluster.f5.internal.cloudapp.net*. Quando você se conectar a um cluster de HBase, você precisará se conectar a um as Zookeepers usando o FQDN. Cada cluster HDInsight possui 3 Zookeepers. Eles são *zookeeper0*, *zookeeper1*e *zookeeper2*. O FQDN será algo como *zookeeper2.myhbasecluster.f5.internal.cloudapp.net*.

**Usar SQLLine**

1. Abrir a **linha de comando do Hadoop** da área de trabalho.
2. Execute os seguintes comandos para abrir SQLLine:

        cd %phoenix_home%\bin
        sqlline.py [The FQDN of one of the Zookeepers]

    ![hdinsight hbase rio sqlline][hdinsight-hbase-phoenix-sqlline]

    Os comandos usados na amostra:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
        
        !tables
        
        UPSERT INTO Company VALUES(1, 'Microsoft');
        
        SELECT * FROM Company;

Para obter mais informações, consulte o [manual de SQLLine](http://sqlline.sourceforge.net/#manual) e a [Gramática de rio](http://phoenix.apache.org/language/index.html).


















##<a name="use-squirrel"></a>Use esquilo

[Esquilo SQL Client](http://squirrel-sql.sourceforge.net/) é um programa Java gráfico que permitirá que você veja a estrutura de um banco de dados compatíveis com JDBC, navegue os dados nas tabelas, emitir comandos SQL etc. Ele pode ser usado para se conectar ao Apache rio em HDInsight.

Esta seção mostra como instalar e configurar esquilo na estação de trabalho para se conectar a um cluster de HBase no HDInsight via VPN. 

###<a name="prerequisites"></a>Pré-requisitos

Antes de seguir os procedimentos, você deve ter o seguinte:

- Um cluster de HBase implantado em uma rede virtual Azure com uma máquina virtual DNS.  Para obter instruções, consulte [clusters de provisionar HBase em rede Virtual do Azure][hdinsight-hbase-provision-vnet]. 

    >[AZURE.IMPORTANT] Você deve instalar um servidor DNS para a rede virtual. Para obter instruções, consulte [Configurar DNS entre duas redes virtuais Azure](hdinsight-hbase-geo-replication-configure-dns.md)

- Obtenha o sufixo DNS de Conexão específicas do cluster de cluster HBase. Para obtê-lo, RDP em cluster e depois execute IPConfig.  O sufixo DNS é semelhante a:

        myhbase.b7.internal.cloudapp.net
- Baixe e instale o [Microsoft Visual Studio Express 2013 para área de trabalho do Windows](https://www.visualstudio.com/products/visual-studio-express-vs.aspx) em sua estação de trabalho. Você precisará makecert do pacote para criar seu certificado.  
- Baixe e instale o [Ambiente de tempo de execução Java](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html) na estação de trabalho.  Esquilo SQL versão 3.0 e superior do cliente requer JRE versão 1,6 ou superior.  


###<a name="configure-a-point-to-site-vpn-connection-to-the-azure-virtual-network"></a>Configurar uma conexão ponto-a-Site VPN rede virtual do Azure

Há 3 etapas envolvidas Configurando uma conexão de VPN ponto-a-site:

1. [Configurar uma rede virtual e um gateway de roteamento dinâmico](#Configure-a-virtual-network-and-a-dynamic-routing-gateway)
2. [Criar seus certificados](#Create-your-certificates)
3. [Configure seu cliente VPN](#Configure-your-VPN-client)

Consulte [Configurar uma conexão ponto-a-Site VPN a uma rede Virtual do Azure](../vpn-gateway/vpn-gateway-point-to-site-create.md) para obter mais informações.

#### <a name="configure-a-virtual-network-and-a-dynamic-routing-gateway"></a>Configurar uma rede virtual e um gateway de roteamento dinâmico

Garantir que você tiver provisionado um cluster de HBase em uma rede virtual Azure (consulte os pré-requisitos para esta seção). A próxima etapa é configurar uma conexão ponto-a-site.

**Para configurar a conectividade de ponto-a-site**

1. Entre [Portal de clássico Azure][azure-portal].
2. À esquerda, clique em **redes**.
3. Clique na rede virtual que você criou (consulte [clusters provisionar HBase em rede Virtual do Azure][hdinsight-hbase-provision-vnet]).
4. Clique em **Configurar** da parte superior.
5. Na seção de **conectividade de ponto-a-site** , selecione **Configurar ponto-a-site conectividade**. 
6. Configure **IP inicial** e **CIDR** para especificar o intervalo de endereços IP do qual seus clientes VPN receberão um endereço IP quando conectado. O intervalo não pode sobrepor com qualquer um dos intervalos localizados na sua rede local e a Azure você estará conectado a uma rede virtual. Por exemplo. Se você selecionou 10.0.0.0/20 para a rede virtual, você pode selecionar 10.1.0.0/24 para o espaço de endereço do cliente. Consulte a [Conectividade de ponto-a-Site] [ vnet-point-to-site-connectivity] página para obter mais informações.
7. Na seção de espaços de endereço de rede virtual, clique em **Adicionar sub-rede do gateway**.
7. Clique em **Salvar** na parte inferior da página.
8. Clique em **Sim** para confirmar a alteração. Aguarde até que o sistema terminou fará a alteração antes de prosseguir para o próximo procedimento.


**Para criar um gateway de roteamento dinâmico**

1. No Portal do Azure clássico, clique em **DASHBOARD** da parte superior da página.
2. Clique em **Criar GATEWAY** da parte inferior da página.
3. Clique em **Sim** para confirmar. Aguarde até que o gateway é criado.
4. Clique em **Painel de controle** da parte superior.  Você verá um diagrama visual da rede virtual:

    ![Diagrama de virtual do Azure rede virtual ponto-a-site][img-vnet-diagram] 

    O diagrama mostra 0 conexões de cliente. Após fazer uma conexão com a rede virtual, o número será atualizado para um. 

#### <a name="create-your-certificates"></a>Criar seus certificados

Uma maneira de criar um certificado x. 509 é usando a ferramenta de criação de certificado (makecert.exe) que vem com o [Microsoft Visual Studio Express 2013 para área de trabalho do Windows](https://www.visualstudio.com/products/visual-studio-express-vs.aspx). 


**Para criar um certificado auto-assinado**

1. Em sua estação de trabalho, abra uma janela do prompt de comando.
2. Navegue até a pasta de ferramentas do Visual Studio. 
3. O comando a seguir no exemplo a seguir criará e instalar um certificado raiz no repositório de certificados pessoais na estação de trabalho e também criar um arquivo. cer correspondente que você carregará posteriormente para o Portal de clássico do Azure. 

        makecert -sky exchange -r -n "CN=HBaseVnetVPNRootCertificate" -pe -a sha1 -len 2048 -ss My "C:\Users\JohnDole\Desktop\HBaseVNetVPNRootCertificate.cer"

    Mude para o diretório que deseja que o arquivo. cer a ser localizado em, onde HBaseVnetVPNRootCertificate é o nome que você deseja usar para o certificado. 

    Não feche o prompt de comando.  Você precisará no próximo procedimento.

    >[AZURE.NOTE] Porque você criou um certificado raiz da qual serão gerados certificados de cliente, você talvez queira exportar este certificado juntamente com sua chave privada e salvá-lo em um local seguro onde ela pode ser recuperada. 

**Para criar um certificado de cliente**

- No prompt de comando mesmo (ele deve ser no mesmo computador onde você criou o certificado raiz. O certificado de cliente deve ser gerado a partir do certificado raiz), execute o seguinte comando:

        makecert.exe -n "CN=HBaseVnetVPNClientCertificate" -pe -sky exchange -m 96 -ss My -in "HBaseVnetVPNRootCertificate" -is my -a sha1

    HBaseVnetVPNRootCertificate é o nome de certificado raiz.  Ele deve corresponder ao nome de certificado raiz.  

    O certificado raiz e o certificado de cliente são armazenados no seu armazenamento de certificado pessoal no seu computador. Use certmgr. msc para verificar.

    ![Certificado de ponto-a-site vpn Azure rede virtual][img-certificate]

    Um certificado de cliente deve ser instalado em cada computador que você deseja conectar à rede virtual. Recomendamos que você crie cliente exclusivo certificados para cada computador que você deseja conectar à rede virtual. Para exportar os certificados de cliente, use certmgr. msc. 

**Para carregar o certificado raiz no Portal de clássico do Azure**

1. No Portal do Azure clássico, clique em **rede** à esquerda.
2. Clique na rede virtual onde seu cluster HBase é implantado.
3. Clique em **certificados** da parte superior.
4. Clique em **carregar** da parte inferior e especifique o arquivo de certificado raiz que você criou no procedimento antes do último. Aguarde até que o certificado obtido importado.
5. Clique em **Painel de controle** na parte superior.  O diagrama virtual mostra o status.


#### <a name="configure-your-vpn-client"></a>Configure seu cliente VPN



**Baixe e instale o pacote VPN do cliente**

1. No painel página da rede virtual, na seção rapidamente, clique em **baixar o pacote de VPN do cliente de 64 bits** ou **baixar o pacote de VPN do cliente de 32 bits** com base em sua versão do sistema operacional de estação de trabalho.
2. Clique em **Executar** para instalar o pacote.
3. No prompt de segurança, clique em **mais informações**e, em seguida, clique em **Executar mesmo assim**.
4. Clique em **Sim** duas vezes.

**Para se conectar à VPN**

1. Na área de trabalho da estação de trabalho, clique no ícone de redes na barra de tarefas. Você deverá ver uma conexão VPN com seu nome de rede virtual.
2. Clique no nome da conexão VPN.
3. Clique em **Conectar**.

**Para testar a resolução de nome de domínio e de conexão VPN**

- Em estação de trabalho, abra um prompt de comando e ping em um dos seguintes nomes determinados sufixo DNS do cluster HBase é myhbase.b7.internal.cloudapp.net:

        zookeeper0.myhbase.b7.internal.cloudapp.net
        zookeeper0.myhbase.b7.internal.cloudapp.net
        zookeeper0.myhbase.b7.internal.cloudapp.net
        headnode0.myhbase.b7.internal.cloudapp.net
        headnode1.myhbase.b7.internal.cloudapp.net
        workernode0.myhbase.b7.internal.cloudapp.net

###<a name="install-and-configure-squirrel-on-your-workstation"></a>Instalar e configurar esquilo em sua estação de trabalho

**Para instalar o esquilo**

1. Baixe o arquivo de jar de cliente do SQL esquilo do [http://squirrel-sql.sourceforge.net/#installation](http://squirrel-sql.sourceforge.net/#installation).
2. Abrir/executar o arquivo jar. Ele exige o [Ambiente de execução de Java](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html).
3. Clique duas vezes em **Avançar** .
4. Especifique o caminho onde você tem a permissão de gravação e clique em **Avançar**.
    >[AZURE.NOTE] A pasta de instalação padrão é na pasta C:\Program Files\squirrel-sql-3.6.  Para gravar este caminho, o instalador deverá ter o privilégio de administrador. Você pode abrir um prompt de comando como administrador, navegue até a pasta de compartimento do Java e, em seguida, executar 
    >
    >     java.exe -jar [the path of the SQuirreL jar file] 
5. Clique em **Okey** para confirmar a criação do diretório de destino.
6. A configuração padrão é instalar a Base e pacotes padrão.  Clique em **Avançar**.
7. Clique duas vezes em **Avançar** e, em seguida, clique em **concluído**.


**Para instalar o driver de rio**

O arquivo de jar de driver rio está localizado no cluster HBase. O caminho é semelhante à seguinte com base nas versões:

    C:\apps\dist\phoenix-4.0.0.2.1.11.0-2316\phoenix-4.0.0.2.1.11.0-2316-client.jar
Você precisa copiá-lo para sua estação de trabalho em [esquilo pasta de instalação] / lib caminho.  A maneira mais fácil é RDP em cluster e, em seguida, use o arquivo copiar/colar (CTRL + C e CTRL + V) copiá-lo para sua estação de trabalho.

**Para adicionar um driver de rio para esquilo**

1. Abra esquilo SQL Client de sua estação de trabalho.
2. Clique na guia **Driver** à esquerda.
2. No menu de **Drivers** , clique em **Novo Driver**.
3. Insira as seguintes informações:

    - **Nome**: rio
    - **Exemplo de URL**: jdbc:phoenix:zookeeper2.contoso-hbase-eu.f5.internal.cloudapp.net
    - **Nome da classe**: org.apache.phoenix.jdbc.PhoenixDriver

    >[AZURE.WARNING] Usuário todas as letras minúsculas a URL de exemplo. Você pode usar eles quorum zookeeper completo no caso de um para baixo.  O nome de host é zookeeper0, zookeeper1 e zookeeper2.

    ![Driver de HDInsight HBase rio esquilo][img-squirrel-driver]
4. Clique em **Okey**.

**Para criar um alias para o cluster HBase**

1. De esquilo, clique na guia **Aliases** à esquerda.
2. No menu **Aliases** , clique em **Novo Alias**.
3. Insira as seguintes informações:

    - **Nome**: O nome do cluster HBase ou qualquer nome que você preferir.
    - **Driver**: rio.  Isso deve corresponder ao nome do driver que você criou no último procedimento.
    - **URL**: A URL é copiada da configuração do driver. Verifique se a usuário todas as letras minúsculas.
    - **Nome de usuário**: pode ser qualquer texto.  Como você pode usar conectividade VPN aqui, o nome de usuário não é usado em todos os.
    - **Senha**: pode ser qualquer texto.

    ![Driver de HDInsight HBase rio esquilo][img-squirrel-alias]
4. Clique em **Testar**. 
5. Clique em **Conectar**. Quando faz a conexão, esquilo aparência:

    ![Rio de HBase esquilo][img-squirrel]

**Para executar um teste**

1. Clique na guia **SQL** diretamente ao lado da guia de **objetos** .
2. Copie e cole o seguinte código:

        CREATE TABLE IF NOT EXISTS us_population (state CHAR(2) NOT NULL, city VARCHAR NOT NULL, population BIGINT  CONSTRAINT my_pk PRIMARY KEY (state, city))
3. Clique no botão Executar.

    ![Rio de HBase esquilo][img-squirrel-sql]
4. Alternar para o guia de **objetos** .
5. Expanda o nome de alias e, em seguida, expanda a **tabela**.  Você deverá ver a nova tabela listada em.
 
##<a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como usar rio Apache no HDInsight.  Para saber mais, consulte

- [Visão geral de HDInsight HBase][hdinsight-hbase-overview]: HBase é um Apache, código-fonte aberto, NoSQL banco de dados criado no Hadoop que fornece acesso aleatório e consistência forte para grandes quantidades de dados não estruturados e semi-estruturados.
- [Provisionar HBase clusters em rede Virtual do Azure][hdinsight-hbase-provision-vnet]: com integração de rede virtual, HBase clusters podem ser implantados à mesma rede virtual como seus aplicativos para que os aplicativos possam se comunicar com HBase diretamente.
- [Replicação de configurar HBase em HDInsight](hdinsight-hbase-geo-replication.md): Saiba como configurar HBase replicação entre dois centros de dados Azure. 
- [Analisar sentimento Twitter com HBase em HDInsight][hbase-twitter-sentiment]: Saiba como executar [análise sentimento](http://en.wikipedia.org/wiki/Sentiment_analysis) em tempo real de dados grandes usando HBase em um cluster de Hadoop no HDInsight.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 
