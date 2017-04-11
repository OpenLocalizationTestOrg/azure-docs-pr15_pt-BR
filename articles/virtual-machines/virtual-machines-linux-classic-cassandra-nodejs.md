<properties
    pageTitle="Executar Cassandra com Linux no Azure | Microsoft Azure"
    description="Como executar um cluster Cassandra no Linux em máquinas virtuais do Azure a partir de um aplicativo de Node"
    services="virtual-machines-linux"
    documentationCenter="nodejs"
    authors="hanuk"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="hanuk;robmcm"/>

# <a name="running-cassandra-with-linux-on-azure-and-accessing-it-from-nodejs"></a>Executando Cassandra com Linux no Azure e acessá-lo de Node

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Saiba como [executar essas etapas usando o modelo do Gerenciador de recursos](https://azure.microsoft.com/documentation/templates/datastax-on-ubuntu/).

## <a name="overview"></a>Visão geral
Microsoft Azure é uma plataforma de nuvem aberta que executa o Microsoft como bem como software não-Microsoft que inclui sistemas operacionais, servidores de aplicativos, middleware mensagens, bem como bancos de dados SQL e NoSQL de ambos os modelos comerciais e de fonte aberta. Criação de serviços e apresentam resiliência em nuvens públicas, incluindo Azure requer cuidado para planejamento e arquitetura proposital para os servidores de aplicativos como camadas de armazenamento bem. Arquitetura de armazenamento distribuído do Cassandra naturalmente ajuda na criação de sistemas altamente disponíveis que estão tolerância de falhas de cluster. Cassandra é uma escala de nuvem banco de dados NoSQL mantida pela Apache Software Foundation no cassandra.apache.org; Cassandra escrito em Java e, portanto, é executado em ambos em Windows, bem como Linux plataformas.

O foco deste artigo é mostrar implantação Cassandra na Ubuntu como um cluster único e vários data center aproveitando máquinas virtuais do Microsoft Azure e redes virtuais. A implantação de cluster para cargas de trabalho de produção otimizada está fora do escopo deste artigo, pois ela requer configuração disco vários nós, design de topologia de anel apropriado e dados de modelagem para oferecer suporte a replicação necessária, a consistência de dados, a produtividade e a requisitos de alta disponibilidade.

Este leva artigo uma abordagem fundamental para mostrar o que está envolvida na criação de cluster Cassandra comparados Docker, chefe ou Puppet que pode facilitar a implantação de infraestrutura muito.  

## <a name="the-deployment-models"></a>Os modelos de implantação
Rede do Microsoft Azure permite a implantação de clusters particulares isolados, o acesso dos quais pode ser restrito para atingir a segurança de rede fino refinadas.  Como este artigo é sobre mostrando a implantação Cassandra em um nível fundamental, nos concentraremos não no nível consistência e o design de armazenamento ideal para a produtividade. Aqui está a lista de requisitos de nosso cluster hipotética de rede:

- Sistemas externos não podem acessar Cassandra banco de dados de dentro ou fora do Azure
- Cluster Cassandra tem que estar atrás de um balanceador de carga de tráfego e mais Próspero banco
- Implantar nós Cassandra em dois grupos em cada data center para disponibilidade um cluster aprimorado
- Bloquear o cluster assim que somente o farm de servidores de aplicativo tem acesso ao banco de dados diretamente
- Nenhum ponto de extremidade de redes públicos diferente SSH
- Cada nó Cassandra precisa de um endereço IP interno fixo

Cassandra pode ser implantado para uma única região Azure ou para várias regiões com base em natureza distribuída da carga de trabalho. Modelo de implantação de várias regiões pode ser utilizado para atender aos usuários finais perto um determinado geografia por meio de infraestrutura de Cassandra do mesma. Nó internos replicação cuida do Cassandra da sincronização de vários mestres grava provenientes de vários data centers e apresenta uma exibição consistente dos dados para aplicativos. Implantação de várias regiões também pode ajudar a redução de riscos das interrupções no serviço Azure mais amplas. Topologia de replicação e consistência ajustável do Cassandra ajudará a atender às necessidades dos diversas RPO de aplicativos.

### <a name="single-region-deployment"></a>Implantação de única região
Abordaremos começar com uma implantação única região e colher as lições na criação de um modelo de várias regiões. Rede virtual Azure será usada para criar sub-redes isoladas para que os requisitos de segurança de rede mencionados acima podem ser atendidos.  O processo descrito na criação de implantação única região usa Ubuntu 14.04 LTS e Cassandra 2.08; No entanto, o processo facilmente pode ser adotado para outras variantes do Linux. A seguir estão algumas das características sistemática da implantação única região.  

**Alta disponibilidade:** Os nós de Cassandra mostrados na Figura 1 são implantados dois conjuntos de disponibilidade para que os nós são distribuídos entre vários domínios de falhas para alta disponibilidade. VMs anotadas com cada conjunto de disponibilidade é mapeado para 2 domínios de falha.  Usos do Microsoft Azure o conceito de domínio de falhas para gerenciar o tempo planejado para baixo (por exemplo, falhas de hardware ou software) enquanto o conceito de domínio de atualização (por exemplo, host ou convidado SO patches/atualizações, atualizações de aplicativo) é usado para gerenciar agendado tempo de inatividade. Consulte [recuperação de dados e alta disponibilidade para aplicativos do Azure](http://msdn.microsoft.com/library/dn251004.aspx) para a função da falha e domínios em alta disponibilidade de obtenção de atualização.

![Implantação de única região](./media/virtual-machines-linux-classic-cassandra-nodejs/cassandra-linux1.png)

Figura 1: Única implantação de região

Observe que, no momento da redação deste artigo, Azure não permite o mapeamento explícito de um grupo de VMs para um domínio de falhas específica; Consequentemente, mesmo com o modelo de implantação mostrado na Figura 1, é estatística provável que todas as máquinas virtuais pode ser mapeadas para dois domínios de falha em vez de quatro.

**e mais Próspero banco tráfego de balanceamento de carga:** Bibliotecas de cliente e mais Próspero banco dentro do servidor de web se conectar ao cluster por meio de um balanceador de carga interno. Isso requer o processo de adicionar o balanceador de carga interno à sub-rede "dados" (consulte a Figura 1) no contexto do serviço de nuvem cluster Cassandra de hospedagem. Assim que o balanceador de carga interna é definido, cada nó requer o ponto de extremidade de balanceamento de carga a ser adicionado com as anotações de um conjunto de balanceamento de carga com nome de Balanceador de carga definido anteriormente. Para obter mais detalhes, consulte [Balanceamento de carga interno do Azure ](../load-balancer/load-balancer-internal-overview.md).

**Cluster sementes:** É importante selecionar os maioria de nós altamente disponíveis para sementes como os novos nós se comunicará conosco de propagação detecte a topologia do cluster. Um nó de cada conjunto de disponibilidade é designado como nós de propagação para evitar ponto único de falha.

**Fator de replicação e nível de consistência:** Compilação de dados e alta disponibilidade durabilidade do Cassandra é caracterizada pelo fator de replicação (FR - número de cópias de cada linha armazenados no cluster) e nível de consistência (número de réplicas sejam lidos/gravados antes de retornar o resultado ao chamador). Fator de replicação for especificado durante a criação de KEYSPACE (semelhante a um banco de dados relacional) enquanto o nível de consistência for especificado durante a consulta CRUD de emissão. Consulte a documentação de Cassandra em [Configurando consistência](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) para obter detalhes de consistência e a fórmula para computação de quorum.

Cassandra aceita dois tipos de modelos de integridade de dados – consistência e consistência Eventual; o fator de replicação e o nível de consistência juntos determinará se os dados serão consistentes assim que uma operação de gravação é concluída ou estarão eventualmente consistente. Por exemplo, especificando QUORUM conforme o nível de consistência serão sempre garante dados consistência enquanto qualquer nível de consistência, abaixo do número de réplicas sejam gravados conforme necessário para atingir QUORUM (por exemplo, um) resulta em dados sendo eventualmente consistentes.

O cluster de 8 nós mostrado acima, com um fator de replicação de 3 e QUORUM (2 nós são lidos ou gravados consistência) nível de consistência de leitura/gravação, poderá sobreviver à perda teórica de no máximo 1 nó por grupo de replicação antes do início de aplicativo perceba a falha. Isso pressupõe que todos os espaços de chave também têm equilibrada leitura/gravação solicitações.  Estes são os parâmetros que usaremos para o cluster implantado:

Configuração de cluster Cassandra única região:

| Parâmetro de cluster | Valor | Comentários |
| ----------------- | ----- | ------- |
| Número de nós (N) | 8   | Número total de nós do cluster |
| Fator de replicação (FR) | 3 | Número de réplicas de uma determinada linha |
| Nível de consistência (gravação) | QUORUM[(RF/2) +1) = 2] o resultado da fórmula será arredondado para baixo | Grava no máximo 2 réplicas antes da resposta é enviada ao chamador; 3º réplica escrita de uma maneira consistente eventualmente. |
| Nível de consistência (leitura) | QUORUM [(RF/2) +1 = 2] o resultado da fórmula será arredondado para baixo | Lê 2 réplicas antes de enviar resposta ao chamador. |
| Estratégia de replicação | NetworkTopologyStrategy consulte [Replicação de dados](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) na documentação de Cassandra para obter mais informações | Compreenda a topologia de implantação e coloca réplicas em nós para que todas as réplicas não terminam no mesmo rack |
| Snitch | GossipingPropertyFileSnitch consulte [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) na documentação de Cassandra para obter mais informações | NetworkTopologyStrategy usa um conceito de snitch para compreender a topologia. GossipingPropertyFileSnitch oferece melhor controle no mapeamento de cada nó para o Centro de dados e rack. O cluster usa fofocas para propagar essas informações. Isso é muito mais simples na configuração de IP dinâmica relativo PropertyFileSnitch |


**Azure considerações para Cluster Cassandra:** Capacidade de máquinas virtuais do Microsoft Azure usa o armazenamento de Blob do Azure para persistência de disco; Armazenamento do Azure salva 3 réplicas de cada disco de alta durabilidade. Isso significa que cada linha de dados inseridos em uma tabela de Cassandra já está armazenada duplicações 3 e, portanto, consistência de dados é já resolvida mesmo se o fator de replicação (FR) é 1. O principal problema com fator de replicação sendo 1 é que o aplicativo terá tempo de inatividade, mesmo se um único nó Cassandra falha. No entanto, se um nó for pressionada para os problemas (por exemplo, hardware, falhas de software do sistema) reconhecidos pelo controlador de tecidos do Azure, ele será provisionar um novo nó em seu lugar usando as mesmas unidades de armazenamento. Provisionar um novo nó para substituir o antigo pode levar alguns minutos.  Da mesma forma para atividades de manutenção planejada como as alterações de sistema operacional de convidado, Cassandra atualizações e alterações de aplicativo do Azure tecidos controlador executa atualizações de nós contínuas no cluster.  Atualizações sem interrupção também pode ser tomadas alguns nós por vez e, portanto, cluster pode experiência breve tempo de inatividade para algumas partições. No entanto, os dados não serão perdidos devido a redundância de armazenamento do Azure interna.  

Para sistemas implantados em Azure que não exige alta disponibilidade (por exemplo, em torno de 99,9 que equivale ao 8.76 h/ano; consulte [Alta disponibilidade](http://en.wikipedia.org/wiki/High_availability) para obter detalhes) você pode ser capaz de executar com fr = 1 e nível de consistência = um.  Para aplicativos com requisitos de alta disponibilidade, fr = 3 e nível de consistência = QUORUM tolerarão o tempo de inatividade de um de nós uma das réplicas. FR = 1 no tradicional implantações (por exemplo, no local) não podem ser usadas devido a perda de dados possíveis resultantes de problemas como falhas no disco.   

## <a name="multi-region-deployment"></a>Implantação de várias regiões
Centro-reconhecimento de dados replicação e consistência modelo do Cassandra descrito acima ajuda com a implantação de várias regiões prontos para uso sem a necessidade de qualquer ferramentas externas. Isso é bem diferente dos bancos de dados relacionais tradicionais onde a configuração para espelhamento de banco de dados para vários mestres gravações pode ser bem complexa. Cassandra em uma região vários configurar pode ajudar com os cenários de uso, incluindo o seguinte:

**Proximidade com base em implantação:** Aplicativos de vários locatários, com Limpar mapeamento de usuários de locatário-para-região, pode ser obteve benefícios ao menos latências do cluster várias regiões. Por exemplo um gerenciamento de aprendizagem sistemas para instituições de ensino podem implantar um cluster distribuído em regiões EUA Leste e Oeste EUA para servir os respectivos campus para transações, além de análise. Os dados podem ser localmente consistentes no tempo leituras e gravações e podem ser eventualmente consistentes entre as duas regiões. Há outros exemplos como distribuição de mídia, comércio e qualquer coisa e tudo o que serve o usuário de localização geográfica concentrado base é um bom caso de uso esse modelo de implantação.

**Alta disponibilidade:** Redundância é um fator importante na obtenção de alta disponibilidade de software e hardware; Consulte sistemas de nuvem confiáveis de construção no Microsoft Azure para obter detalhes. No Microsoft Azure, a maneira confiável somente de atingir redundância true é implantando um cluster de várias regiões. Aplicativos podem ser implantados em um modo ativa ou ativo-passivo e se uma das regiões for pressionada, Gerenciador de tráfego do Azure pode redirecionar o tráfego para a região ativa.  Com a implantação de região única, se a disponibilidade é 99,9, uma implantação de região de dois pode atingir uma disponibilidade de 99.9999 calculado pela fórmula: (1-(1-0.999) *(1-0,999))*100); Consulte o documento acima para obter detalhes.

**Recuperação:** Cluster de Cassandra várias regiões, se criada adequadamente, pode suportar interrupções de centro de dados grave. Se uma região for pressionada, pode iniciar o aplicativo implantado em outras regiões servir os usuários finais. Como as outras implementações de continuidade de negócios, o aplicativo deve ser tolerância a alguns perda de dados resultante dos dados no pipeline de assíncrona. Entretanto, Cassandra torna a recuperação muito swifter que o tempo gasto por processos de recuperação de banco de dados tradicionais. Figura 2 mostra o modelo de implantação de várias regiões típica com oito nós em cada região. Ambas as regiões são imagens de espelho uns dos outros para a mesma de simetria; designs de reais dependem do tipo de carga de trabalho (por exemplo, transações ou analítico), RPO, RTO, consistência de dados e requisitos de disponibilidade.

![Implantação de região de vários](./media/virtual-machines-linux-classic-cassandra-nodejs/cassandra-linux2.png)

Figura 2: Implantação de várias regiões Cassandra

### <a name="network-integration"></a>Integração de rede
Conjuntos de máquinas virtuais implantados em redes privadas localizados em duas regiões se comunica com os outros usando um túnel VPN. Túnel VPN conecta dois gateways de software provisionados durante o processo de implantação de rede. Ambas as regiões têm arquitetura de rede semelhante em termos de sub-redes "web" e "dados"; Rede Azure permite a criação de sub-redes quantas conforme necessário e aplique ACLs conforme necessário pelo network security. Ao projetar a topologia de cluster inter latência de comunicação do Centro de dados e o impacto econômico o tráfego de rede precisa ser considerado.

### <a name="data-consistency-for-multi-data-center-deployment"></a>Consistência de dados em vários Data centers
Distribuído implantações precisa estar ciente sobre o impacto de topologia de cluster em alta disponibilidade e a produtividade. O nível de consistência e fr precisam ser selecionado de forma que o quorum não depende da disponibilidade de todos os centros de dados.
Para um sistema que precisa de consistência alta, um LOCAL_QUORUM de nível de consistência (para leituras e gravações) será Certifique-se de que as leituras locais e gravações sejam atendidas de nós do locais enquanto os dados são replicados assíncrona os centros de dados remoto.  Tabela 2 resume os detalhes de configuração para o cluster de várias regiões descritas posteriormente a gravação para cima.

**Configuração de cluster Cassandra dois região**


| Parâmetro de cluster | Valor | Comentários |
| ----------------- | ----- | ------- |
| Número de nós (N) | 8 + 8 | Número total de nós do cluster |
| Fator de replicação (FR) | 3 | Número de réplicas de uma determinada linha |
| Nível de consistência (gravação) | LOCAL_QUORUM [(sum(RF)/2) +1) = 4] o resultado da fórmula será arredondado para baixo | 2 nós serão gravados o primeiro data center sincronia; os nós de 2 adicionais necessários para quorum serão gravados assíncrona ao data center 2a. |
| Nível de consistência (leitura) | LOCAL_QUORUM ((RF/2) + 1) = 2, o resultado da fórmula será arredondado para baixo | Solicitações de leitura são satisfeitas de apenas uma região; 2 nós são lidos antes da resposta é enviada para o cliente. |
| Estratégia de replicação | NetworkTopologyStrategy consulte [Replicação de dados](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) na documentação de Cassandra para obter mais informações | Compreenda a topologia de implantação e coloca réplicas em nós para que todas as réplicas não terminam no mesmo rack |
| Snitch | GossipingPropertyFileSnitch consulte [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) na documentação de Cassandra para obter mais informações | NetworkTopologyStrategy usa um conceito de snitch para compreender a topologia. GossipingPropertyFileSnitch oferece melhor controle no mapeamento de cada nó para o Centro de dados e rack. O cluster usa fofocas para propagar essas informações. Isso é muito mais simples na configuração de IP dinâmica relativo PropertyFileSnitch |


##<a name="the-software-configuration"></a>A CONFIGURAÇÃO DE SOFTWARE
As seguintes versões de software são usadas durante a implantação:

<table>
<tr><th>Software</th><th>Fonte</th><th>Versão</th></tr>
<tr><td>JRE </td><td>[JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA </td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/2.0.8/apache-cassandra-2.0.8-bin.tar.gz)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu  </td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTS</td></tr>
</table>

Como fazer o download do JRE requer aceitação manual da licença do Oracle, para simplificar a implantação, baixe todos os softwares necessários na área de trabalho para carregamento mais tarde para a imagem de modelo Ubuntu que podemos criarão como uma precursora para a implantação de cluster.

Baixe o software acima em um diretório de download conhecidos (por exemplo, %TEMP%/downloads no Windows ou ~/Downloads na maioria das distribuições Linux ou Mac) no computador local.

### <a name="create-ubuntu-vm"></a>CRIAR MÁQUINA VIRTUAL DO UBUNTU
Nesta etapa do processo de nós criará Ubuntu imagem com o software de pré-requisito para que a imagem pode ser reutilizada para provisionamento de vários nós de Cassandra.  
####<a name="step-1-generate-ssh-key-pair"></a>Etapa 1: Gerar o par de chaves de SSH
Azure necessidades um X509 chave pública PEM ou DER codificada no momento provisionamento. Gere um par de chaves pública/particular usando as instruções localizadas em como usar SSH com Linux no Azure. Se você planeja usar putty.exe como um cliente SSH no Windows ou Linux, você terá que converter o PEM codificado chave privada RSA em formato PPK usando puttygen.exe; as instruções para isso podem ser encontradas na página da web acima.

####<a name="step-2-create-ubuntu-template-vm"></a>Etapa 2: Criar o modelo de Ubuntu máquina virtual
Para criar o modelo de máquina virtual, faça logon no portal de clássico do Azure e use a seguinte sequência: clique em novo, computação, máquina VIRTUAL, de GALERIA, UBUNTU, Ubuntu servidor 14.04 LTS e, em seguida, clique na seta à direita. Para um tutorial que descreve como criar uma VM Linux, consulte Criar uma máquina Virtual executando Linux.

Insira as seguintes informações na tela "configuração de máquina Virtual" #1:

<table>
<tr><th>NOME DO CAMPO              </td><td>       VALOR DO CAMPO               </td><td>         COMENTÁRIOS                </td><tr>
<tr><td>DATA DO LANÇAMENTO DE VERSÃO    </td><td> Selecione uma data do drow para baixo</td><td></td><tr>
<tr><td>NOME DE MÁQUINA VIRTUAL    </td><td> modelo de cass                </td><td> Este é o nome de host da máquina virtual </td><tr>
<tr><td>CAMADA                     </td><td> PADRÃO                        </td><td> Deixe o padrão              </td><tr>
<tr><td>TAMANHO                     </td><td> A1                              </td><td>Selecione a máquina virtual com base nas necessidades IO; para essa finalidade deixe o padrão </td><tr>
<tr><td> NOVO NOME DE USUÁRIO           </td><td> localadmin                      </td><td> "administrador" é um nome de usuário reservada Ubuntu 12. xx e depois</td><tr>
<tr><td> AUTENTICAÇÃO      </td><td> Clique em caixa de seleção                 </td><td>Verifique se você deseja proteger com uma chave SSH </td><tr>
<tr><td> CERTIFICADO             </td><td> nome do arquivo de certificado de chave pública </td><td> Use a chave pública gerada anteriormente</td><tr>
<tr><td> Nova senha   </td><td> senha forte </td><td> </td><tr>
<tr><td> Confirmar senha   </td><td> senha forte </td><td></td><tr>
</table>

Insira as seguintes informações na tela "configuração de máquina Virtual" #2:

<table>
<tr><th>NOME DO CAMPO             </th><th> VALOR DO CAMPO                       </th><th> COMENTÁRIOS                                 </th></tr>
<tr><td> SERVIÇO DE NUVEM  </td><td> Criar um novo serviço de nuvem    </td><td>Serviço de nuvem é um recursos de computação de contêiner como máquinas virtuais</td></tr>
<tr><td> NOME DE DNS DE SERVIÇO DE NUVEM </td><td>Ubuntu-template.cloudapp.net   </td><td>Dê um nome de Balanceador de carga independente de máquina</td></tr>
<tr><td> REGIÃO/GRUPO DE AFINIDADE/REDE VIRTUAL </td><td>    Oeste EUA </td><td> Selecione uma região de onde seus aplicativos web acessam o cluster de Cassandra</td></tr>
<tr><td>CONTA DE ARMAZENAMENTO </td><td>   Usar padrão </td><td>Use a conta de armazenamento padrão ou uma conta de armazenamento pré-criado em uma região específica</td></tr>
<tr><td>CONJUNTO DE DISPONIBILIDADE </td><td>  Nenhum </td><td>  Deixe em branco</td></tr>
<tr><td>PONTOS DE EXTREMIDADE   </td><td>Usar padrão </td><td>  Usar a configuração de SSH padrão </td></tr>
</table>

Seta para a direita, mantenha os padrões na tela #3 e clique no botão "seleção" para concluir a processo de provisionamento da VM. Após alguns minutos, a máquina virtual com o nome "ubuntu-modelo" deve estar em um status de "executando".

###<a name="install-the-necessary-software"></a>INSTALAR O SOFTWARE NECESSÁRIO
####<a name="step-1-upload-tarballs"></a>Etapa 1: Carregar tarballs
Usando scp ou pscp, copie o software baixado anteriormente ao diretório ~/downloads usando o seguinte formato de comando:

#####<a name="pscp-server-jre-8u5-linux-x64targz-localadminhk-cas-templatecloudappnethomelocaladmindownloadsserver-jre-8u5-linux-x64targz"></a>pscp servidor-jre-8u5-linux-x64.tar.gzlocaladmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz

Repita o comando acima para JRE bem para os bits de Cassandra.

####<a name="step-2-prepare-the-directory-structure-and-extract-the-archives"></a>Etapa 2: Preparar a estrutura de diretório e extrair os arquivos mortos
Faça logon na máquina virtual e criar a estrutura de diretório e extrair software como um usuário super usando o script bash abaixo:

    #!/bin/bash
    CASS_INSTALL_DIR="/opt/cassandra"
    JRE_INSTALL_DIR="/opt/java"
    CASS_DATA_DIR="/var/lib/cassandra"
    CASS_LOG_DIR="/var/log/cassandra"
    DOWNLOADS_DIR="~/downloads"
    JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
    CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
    SVC_USER="localadmin"

    RESET_ERROR=1
    MKDIR_ERROR=2

    reset_installation ()
    {
       rm -rf $CASS_INSTALL_DIR 2> /dev/null
       rm -rf $JRE_INSTALL_DIR 2> /dev/null
       rm -rf $CASS_DATA_DIR 2> /dev/null
       rm -rf $CASS_LOG_DIR 2> /dev/null
    }
    make_dir ()
    {
       if [ -z "$1" ]
       then
          echo "make_dir: invalid directory name"
          exit $MKDIR_ERROR
       fi

       if [ -d "$1" ]
       then
          echo "make_dir: directory already exists"
          exit $MKDIR_ERROR
       fi

       mkdir $1 2>/dev/null
       if [ $? != 0 ]
       then
          echo "directory creation failed"
          exit $MKDIR_ERROR
       fi
    }

    unzip()
    {
       if [ $# == 2 ]
       then
          tar xzf $1 -C $2
       else
          echo "archive error"
       fi

    }

    if [ -n "$1" ]
    then
       SVC_USER=$1
    fi

    reset_installation
    make_dir $CASS_INSTALL_DIR
    make_dir $JRE_INSTALL_DIR
    make_dir $CASS_DATA_DIR
    make_dir $CASS_LOG_DIR

    #unzip JRE and Cassandra
    unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
    unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR

    #Change the ownership to the service credentials

    chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
    chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
    echo "edit /etc/profile to add JRE to the PATH"
    echo "installation is complete"


Se você colar esse script em janela vim, certifique-se de remover o retorno de carro ('\r ") usando o seguinte comando:

    tr -d '\r' <infile.sh >outfile.sh

####<a name="step-3-edit-etcprofile"></a>Etapa 3: Editar etc/perfil
Acrescente o seguinte ao final:

    JAVA_HOME=/opt/java/jdk1.8.0_05
    CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
    PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
    export JAVA_HOME
    export CASS_HOME
    export PATH

####<a name="step-4-install-jna-for-production-systems"></a>Etapa 4: Instalar JNA para sistemas de produção
Use a sequência de comando a seguir: O comando a seguir serão instalar jna 3.2.7.jar e jna-plataforma-3.2.7.jar ao diretório /usr/share.java chance sudo get instalar libjna-java

Crie links simbólicos no diretório CASS_HOME/biblioteca $ para que o script de inicialização de Cassandra pode encontrar essas jars:

    ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

    ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

####<a name="step-5-configure-cassandrayaml"></a>Etapa 5: Configurar cassandra.yaml
Edite cassandra.yaml em cada máquina virtual para refletir configuração necessária para todas as máquinas virtuais [nós será ajustar isso durante o provisionamento real]:

<table>
<tr><th>Nome do campo   </th><th> Valor  </th><th> Comentários </th></tr>
<tr><td>nome_do_cluster </td><td>  "CustomerService"   </td><td> Use o nome que reflita a sua implantação</td></tr>
<tr><td>listen_address  </td><td>[deixe em branco]   </td><td> Excluir "host local" </td></tr>
<tr><td>rpc_addres   </td><td>[deixe em branco]  </td><td> Excluir "host local" </td></tr>
<tr><td>sementes   </td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8" </td><td>Lista de todos os endereços IP que estão designadas como sementes.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> Isso é usado pela NetworkTopologyStrateg para inferir do data center e rack da máquina virtual</td></tr>
</table>

####<a name="step-6-capture-the-vm-image"></a>Etapa 6: Capturar a imagem de máquina virtual
Faça logon na máquina virtual usando o nome de host (Hong Kong-autoridades de certificação-template.cloudapp.net) e a chave privada SSH criado anteriormente. Veja como usar SSH com Linux no Azure para obter detalhes sobre como efetuar logon usando o comando ssh ou putty.exe.

Execute sequência de ações para capturar a imagem a seguir:
#####<a name="1-deprovision"></a>1. deprovision
Use o comando "sudo waagent – deprovision + usuário" para remover informações específicas de instância de máquina Virtual. Consulte [como capturar uma máquina Virtual Linux](virtual-machines-linux-classic-capture-image.md) para usar como um modelo mais detalhes sobre o processo de captura de imagem.

#####<a name="2-shutdown-the-vm"></a>2: desligar a máquina virtual
Certifique-se de que a máquina virtual está realçada e clique no link de desligamento da parte inferior da barra de comando.

#####<a name="3-capture-the-image"></a>3: capturar uma imagem
Certifique-se de que a máquina virtual está realçada e clique no link de captura na parte inferior da barra de comando. Na tela seguinte, dê um nome de imagem (por exemplo, hk-cas-2-08-ub-14-04-2014071), apropriado descrição da imagem e clique na marca de "seleção" para concluir o processo de captura.

Isso levará alguns segundos e a imagem deve estar disponível na seção Minhas imagens da Galeria de imagem. Fonte de máquina virtual será automaticamente delated depois que a imagem for capturada com êxito.

##<a name="single-region-deployment-process"></a>Processo de implantação de única região
**Etapa 1: criar a rede Virtual** Faça logon no portal de clássico do Azure e crie uma rede Virtual com Mostrar atributos na tabela. Consulte [Configurar uma rede Virtual Cloud-Only no portal de clássico do Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md) para obter etapas detalhadas do processo.      

<table>
<tr><th>Nome do atributo de máquina virtual</th><th>Valor</th><th>Comentários</th></tr>
<tr><td>Nome</td><td>vnet-cass-Oeste-us</td><td></td></tr>
<tr><td>Região</td><td>Oeste EUA</td><td></td></tr>
<tr><td>Servidores DNS </td><td>Nenhum</td><td>Ignorar isso como não estamos usando um servidor DNS</td></tr>
<tr><td>Configurar uma VPN de ponto-a-site</td><td>Nenhum</td><td> Ignorar isso</td></tr>
<tr><td>Configurar uma VPN to-site</td><td>Nnone</td><td> Ignorar isso</td></tr>
<tr><td>Espaço de endereço</td><td>10.1.0.0/16</td><td></td></tr>
<tr><td>IP inicial</td><td>10.1.0.0</td><td></td></tr>
<tr><td>CIDR </td><td>/ 16 (65531)</td><td></td></tr>
</table>

Adicione as seguintes sub-redes:

<table>
<tr><th>Nome</th><th>IP inicial</th><th>CIDR</th><th>Comentários</th></tr>
<tr><td>Web</td><td>10.1.1.0</td><td>/ 24 (251)</td><td>Sub-rede do web farm</td></tr>
<tr><td>dados</td><td>10.1.2.0</td><td>/ 24 (251)</td><td>Sub-rede para os nós de banco de dados</td></tr>
</table>

Dados e sub-redes Web podem ser protegidos por meio de grupos de segurança de rede a cobertura dos quais estiver fora do escopo deste artigo.  

**Etapa 2: provisionar máquinas virtuais** Usando a imagem criada anteriormente, podemos criará as seguintes máquinas virtuais do servidor de nuvem "Hong Kong-c-svc-Oeste" e vinculá-las a respectivas sub-redes, conforme mostrado abaixo:

<table>
<tr><th>Nome da máquina    </th><th>Sub-rede </th><th>Endereço IP </th><th>Conjunto de disponibilidade</th><th>DC/Rack</th><th>Propagar?</th></tr>
<tr><td>Hong Kong-c1-Oeste-us   </td><td>dados   </td><td>10.1.2.4   </td><td>Hong Kong-c-um conjunto-1    </td><td>DC = rack WESTUS = rack1 </td><td>Sim</td></tr>
<tr><td>Hong Kong-c2-Oeste-us   </td><td>dados   </td><td>10.1.2.5   </td><td>Hong Kong-c-um conjunto-1    </td><td>DC = rack WESTUS = rack1 </td><td>Não </td></tr>
<tr><td>Hong Kong-c3-Oeste-us   </td><td>dados   </td><td>10.1.2.6   </td><td>Hong Kong-c-um conjunto-1    </td><td>DC = rack WESTUS = rack2 </td><td>Sim</td></tr>
<tr><td>Hong Kong-c4-Oeste-us   </td><td>dados   </td><td>10.1.2.7   </td><td>Hong Kong-c-um conjunto-1    </td><td>DC = rack WESTUS = rack2 </td><td>Não </td></tr>
<tr><td>Hong Kong-c5-Oeste-us   </td><td>dados   </td><td>10.1.2.8   </td><td>Hong Kong-c-um conjunto-2    </td><td>DC = rack WESTUS = rack3 </td><td>Sim</td></tr>
<tr><td>Hong Kong-c6-Oeste-us   </td><td>dados   </td><td>10.1.2.9   </td><td>Hong Kong-c-um conjunto-2    </td><td>DC = rack WESTUS = rack3 </td><td>Não </td></tr>
<tr><td>Hong Kong-c7-Oeste-us   </td><td>dados   </td><td>10.1.2.10  </td><td>Hong Kong-c-um conjunto-2    </td><td>DC = rack WESTUS = rack4 </td><td>Sim</td></tr>
<tr><td>Hong Kong-c8-Oeste-us   </td><td>dados   </td><td>10.1.2.11  </td><td>Hong Kong-c-um conjunto-2    </td><td>DC = rack WESTUS = rack4 </td><td>Não </td></tr>
<tr><td>Hong Kong-B1-Oeste-us   </td><td>Web    </td><td>10.1.1.4   </td><td>Hong Kong-w-um conjunto-1    </td><td>                       </td><td>N/D</td></tr>
<tr><td>Hong Kong-w2-Oeste-us   </td><td>Web    </td><td>10.1.1.5   </td><td>Hong Kong-w-um conjunto-1    </td><td>                       </td><td>N/D</td></tr>
</table>

Criação de lista acima de VMs requer o seguinte processo:

1.  Criar um serviço de nuvem vazias em uma região específica
2.  Criar uma máquina virtual da imagem capturada anteriormente e anexá-lo à rede virtual criada anteriormente; Repita esse procedimento para todas as VMs
3.  Adicionar um balanceador de carga interno ao serviço de nuvem e anexá-lo à sub-rede "dados"
4.  Para cada máquina virtual criado anteriormente, adicione um ponto de extremidade de balanceamento de carga de tráfego e mais Próspero banco através de um conjunto de balanceamento de carga conectado ao balanceador de carga interno criado anteriormente

O processo acima pode ser executado usando o portal de clássico Azure; usar um computador Windows (use uma máquina virtual em Azure se você não tiver acesso a um computador Windows), use o seguinte script do PowerShell para provisionar todas 8 VMs automaticamente.

**Lista 1: Script do PowerShell para provisionamento de máquinas virtuais**

        #Tested with Azure Powershell - November 2014
        #This powershell script deployes a number of VMs from an existing image inside an Azure region
        #Import your Azure subscription into the current Powershell session before proceeding
        #The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. crate a list of VMs from the template

        #fundamental variables - change these to reflect your subscription
        $country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
        $numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
        $subscriptionName = "Azure_subscription_name";
        $vmSize="ExtraSmall"; $imageName="your_linux_image_name"
        $ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"

        #generated variables
        $serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"

        $vmNames = @()
        for ($i=0; $i -lt $numVMs; $i++)
        {
           $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
        }

        #select an Azure subscription already imported into Powershell session
        Select-AzureSubscription -SubscriptionName $subscriptionName -Current
        Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount

        #create an empty cloud service
        New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
        Write-Host "Created $serviceName"

        $VMList= @()   # stores the list of azure vm configuration objects
        #create the list of VMs
        foreach($vmName in $vmNames)
        {
           $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
           Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
           Set-AzureSubnet "data"
        }

        New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList

        #Create internal load balancer
        Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
        Write-Host "Created $ilbName"
        #Add add the thrift endpoint to the internal load balancer for all the VMs
        foreach($vmName in $vmNames)
        {
            Get-AzureVM -ServiceName $serviceName -Name $vmName |
                Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName |
                Update-AzureVM

            Write-Host "created $vmName"     
        }

**Etapa 3: Configurar Cassandra em cada máquina virtual**

Faça logon na máquina virtual e faça o seguinte:

* Edite $CASS_HOME/conf/cassandra-rackdc.properties para especificar as propriedades de rack e da Central de dados:

       dc =EASTUS, rack =rack1

* Edite cassandra.yaml para configurar nós de propagação como abaixo:

       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Etapa 4: Iniciar VMs e testar o cluster**

Faça logon em um de nós (por exemplo, Hong Kong-c1-Oeste-EUA) e execute o seguinte comando para ver o status do cluster:

       nodetool –h 10.1.2.4 –p 7199 status

Você verá a exibição semelhante ao abaixo para um cluster de 8 nós:

<table>
<tr><th>Status</th><th>Endereço  </th><th>Carga   </th><th>Tokens </th><th>Possui </th><th>Identificação do host  </th><th>Rack</th></tr>
<tr><th>UN  </td><td>10.1.2.4   </td><td>87.81 KB   </td><td>256    </td><td>38.0%  </td><td>GUID (removido)</td><td>rack1</td></tr>
<tr><th>UN  </td><td>10.1.2.5   </td><td>41.08 KB   </td><td>256    </td><td>68.9%  </td><td>GUID (removido)</td><td>rack1</td></tr>
<tr><th>UN  </td><td>10.1.2.6   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (removido)</td><td>rack2</td></tr>
<tr><th>UN  </td><td>10.1.2.7   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (removido)</td><td>rack2</td></tr>
<tr><th>UN  </td><td>10.1.2.8   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (removido)</td><td>rack3</td></tr>
<tr><th>UN  </td><td>10.1.2.9   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (removido)</td><td>rack3</td></tr>
<tr><th>UN  </td><td>10.1.2.10  </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (removido)</td><td>rack4</td></tr>
<tr><th>UN  </td><td>10.1.2.11  </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (removido)</td><td>rack4</td></tr>
</table>

## <a name="test-the-single-region-cluster"></a>Testar o Cluster única região
Use as etapas a seguir para testar o cluster:

1.    Usando o comando de Get-AzureInternalLoadbalancer do Powershell commandlet, obter o endereço IP do balanceador de carga interna (por exemplo  10.1.2.101). A sintaxe do comando é mostrada abaixo: Get-AzureLoadbalancer – ServiceName "Hong Kong-c-svc-Oeste-us" [exibe os detalhes de Balanceador de carga interno junto com seu endereço IP]
2.  Faça login no web farm máquina virtual (por exemplo, Hong Kong-B1-Oeste-EUA) usando acabamento ou ssh
3.  Executar $CASS_HOME/bin/cqlsh 10.1.2.101 9160
4.  Use os seguintes comandos CQL para verificar se o cluster está funcionando:

        CREATE KEYSPACE customers_ks WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 3 };
        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');

        SELECT * FROM Customers;

Você deverá ver uma exibição como aquele abaixo:

<table>
  <tr><th> customer_id </th><th> nome </th><th> Sobrenome </th></tr>
  <tr><td> 1 </td><td> John </td><td> Cunha </td></tr>
  <tr><td> 2 </td><td> Laura </td><td> Cunha </td></tr>
</table>

Observe que o keyspace criado na etapa 4 usa SimpleStrategy com um replication_factor de 3. SimpleStrategy é recomendado para único data center implantações enquanto NetworkTopologyStrategy para vários data center implantações. Um replication_factor de 3 lhe dará tolerância de falhas de nó.

##<a id="tworegion"> </a>Processo de implantação de várias regiões
Será aproveitar a implantação única região concluída e repita o mesmo processo de instalação do segundo região. A principal diferença entre a implantação de região de uma ou várias é a configuração de túnel VPN para comunicação entre região; Abordaremos começar com a instalação de rede, provisionar VMs e configurar Cassandra.

###<a name="step-1-create-the-virtual-network-at-the-2nd-region"></a>Etapa 1: Criar a rede Virtual na região das 2a
Faça logon no portal de clássico do Azure e crie uma rede Virtual com Mostrar atributos na tabela. Consulte [Configurar uma rede Virtual Cloud-Only no portal de clássico do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para obter etapas detalhadas do processo.      

<table>
<tr><th>Nome do atributo    </th><th>Valor    </th><th>Comentários</th></tr>
<tr><td>Nome    </td><td>vnet-cass-Leste-us</td><td></td></tr>
<tr><td>Região  </td><td>Leste EUA</td><td></td></tr>
<tr><td>Servidores DNS     </td><td></td><td>Ignorar isso como não estamos usando um servidor DNS</td></tr>
<tr><td>Configurar uma VPN de ponto-a-site</td><td></td><td>     Ignorar isso</td></tr>
<tr><td>Configurar uma VPN to-site</td><td></td><td>      Ignorar isso</td></tr>
<tr><td>Espaço de endereço   </td><td>10.2.0.0/16</td><td></td></tr>
<tr><td>IP inicial </td><td>10.2.0.0   </td><td></td></tr>
<tr><td>CIDR    </td><td>/ 16 (65531)</td><td></td></tr>
</table>

Adicione as seguintes sub-redes:
<table>
<tr><th>Nome    </th><th>IP inicial    </th><th>CIDR   </th><th>Comentários</th></tr>
<tr><td>Web </td><td>10.2.1.0   </td><td>/ 24 (251)  </td><td>Sub-rede do web farm</td></tr>
<tr><td>dados    </td><td>10.2.2.0   </td><td>/ 24 (251)  </td><td>Sub-rede para os nós de banco de dados</td></tr>
</table>


###<a name="step-2-create-local-networks"></a>Etapa 2: Criar redes locais
Uma rede Local na rede virtual Azure é um espaço de endereço de proxy que mapeia para um local remoto, incluindo uma nuvem privada ou outra região Azure. Este espaço de endereço proxy está vinculado a um gateway remoto para roteamento de rede para os destinos de rede à direita. Consulte [Configurar um VNet para Conexão de VNet](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) para obter as instruções sobre como estabelecer conexão VNET para VNET.

Crie duas redes locais pelos seguintes detalhes:

| Nome de rede | Endereço de Gateway VPN | Espaço de endereço | Comentários |
| ------------ | ------------------- | ------------- | ------- |
| HK-lnet-Map-to-East-US | 23.1.1.1  | 10.2.0.0/16   | Ao criar a rede Local dar um espaço reservado para endereço do gateway. O endereço do gateway real é preenchido depois que o gateway é criado. Certificar-se de que o espaço de endereço corresponde exatamente o respectivo VNET remoto; Nesse caso, o VNET criado na região Leste EUA. |
| HK-lnet-Map-to-West-US | 23.2.2.2  | 10.1.0.0/16   | Ao criar a rede Local dar um espaço reservado para endereço do gateway. O endereço do gateway real é preenchido depois que o gateway é criado. Certificar-se de que o espaço de endereço corresponde exatamente o respectivo VNET remoto; Nesse caso, o VNET criado na região Oeste EUA. |


###<a name="step-3-map-local-network-to-the-respective-vnets"></a>Etapa 3: Map "Local" network para os respectivos VNETs
Azure clássico no portal do, selecione cada vnet, clique em "Configurar", marque "Conectar à rede local" e selecione as redes Local pelos seguintes detalhes:


| Rede virtual | Rede local |
| --------------- | ------------- |
| Hong Kong-vnet-Oeste-us | HK-lnet-Map-to-East-US |
| Hong Kong-vnet-Leste-us | HK-lnet-Map-to-West-US |


###<a name="step-4-create-gateways-on-vnet1-and-vnet2"></a>Etapa 4: Criar Gateways em VNET1 e VNET2
No painel de ambas as redes virtuais, clique em criar GATEWAY, que acionará o gateway VPN processo de provisionamento. Após alguns minutos no painel de cada rede virtual deverá exibir o endereço do gateway real.

###<a name="step-5-update-local-networks-with-the-respective-gateway-addresses"></a>Etapa 5: Redes de "Local" atualização com os endereços de respectivos "Gateway"###
Edite as redes locais para substituir o endereço IP do gateway de espaço reservado para o endereço IP real dos gateways apenas provisionados. Use o mapeamento a seguir:

<table>
<tr><th>Rede local    </th><th>Gateway de rede virtual</th></tr>
<tr><td>HK-lnet-Map-to-East-US </td><td>Gateway de Hong Kong-vnet-Oeste-us</td></tr>
<tr><td>HK-lnet-Map-to-West-US </td><td>Gateway de Hong Kong-vnet-Leste-us</td></tr>
</table>

###<a name="step-6-update-the-shared-key"></a>Etapa 6: Atualizar a chave compartilhada
Use o seguinte script do Powershell para atualizar a chave IPSec de cada gateway VPN [usar a mesma tecla para ambos os gateways]: hk-lnet-map-to-west-us - LocalNetworkSiteName Set-AzureVNetGatewayKey - VNetName Hong Kong-vnet-Leste-us - SharedKey hk-lnet-map-to-east-us - LocalNetworkSiteName D9E76BKK Set-AzureVNetGatewayKey - VNetName Hong Kong-vnet-Oeste-us - SharedKey D9E76BKK

###<a name="step-7-establish-the-vnet-to-vnet-connection"></a>Etapa 7: Estabelecer a conexão de VNET para VNET
Azure clássico no portal do, use o menu de "Painel" das redes virtuais estabelecer conexão de gateway para gateway. Use os itens de menu "Conectar" na barra de ferramentas inferior. Após alguns minutos o painel deve exibir os detalhes de conexão graficamente.

###<a name="step-8-create-the-virtual-machines-in-region-2"></a>Etapa 8: Criar as máquinas virtuais na região #2
Criar a imagem de Ubuntu conforme descrito na implantação de região #1, seguindo o mesmo etapas ou copiar o arquivo VHD de imagem para a conta de armazenamento do Azure localizado na região #2 e criar a imagem. Use esta imagem e criar a seguinte lista de máquinas virtuais em um novo serviço de nuvem Hong Kong-c-svc-Leste-us:


| Nome da máquina | Sub-rede | Endereço IP | Conjunto de disponibilidade | DC/Rack | Propagar? |
| ------------ | ------ | ---------- | ---------------- | ------- | ----- |
| Hong Kong-c1-Leste-us | dados  | 10.2.2.4   | Hong Kong-c-um conjunto-1      | DC = rack EASTUS = rack1 | Sim |
| Hong Kong-c2-Leste-us | dados  | 10.2.2.5   | Hong Kong-c-um conjunto-1      | DC = rack EASTUS = rack1 | Não  |
| Hong Kong-c3-Leste-us | dados  | 10.2.2.6   | Hong Kong-c-um conjunto-1      | DC = rack EASTUS = rack2 | Sim |
| Hong Kong-c5-Leste-us | dados  | 10.2.2.8   | Hong Kong-c-um conjunto-2      | DC = rack EASTUS = rack3 | Sim |
| Hong Kong-c6-Leste-us | dados  | 10.2.2.9   | Hong Kong-c-um conjunto-2      | DC = rack EASTUS = rack3 | Não  |
| Hong Kong-c7-Leste-us | dados  | 10.2.2.10  | Hong Kong-c-um conjunto-2      | DC = rack EASTUS = rack4 | Sim |
| Hong Kong-c8-Leste-us | dados  | 10.2.2.11  | Hong Kong-c-um conjunto-2      | DC = rack EASTUS = rack4 | Não  |
| Hong Kong-B1-Leste-us | Web   | 10.2.1.4   | Hong Kong-w-um conjunto-1      | N/D                    | N/D |
| Hong Kong-w2-Leste-us | Web   | 10.2.1.5   | Hong Kong-w-um conjunto-1      | N/D                    | N/D |


Siga as mesmas instruções como região #1, mas use o espaço de endereço de 10.2.xxx.xxx.

###<a name="step-9-configure-cassandra-on-each-vm"></a>Etapa 9: Configurar Cassandra em cada máquina virtual
Faça logon na máquina virtual e faça o seguinte:

1. Editar $CASS_HOME/conf/cassandra-rackdc.properties para especificar as propriedades de rack e da Central de dados no formato: dc = rack EASTUS = rack1
2. Editar cassandra.yaml para configurar nós de propagação: sementes: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"

###<a name="step-10-start-cassandra"></a>Etapa 10: Iniciar Cassandra
Faça logon em cada máquina virtual e inicie Cassandra no plano de fundo executando o seguinte comando: $CASS_HOME/bin/cassandra

## <a name="test-the-multi-region-cluster"></a>Testar o Cluster várias regiões
Agora Cassandra foi implantado 16 nós com 8 nós em cada região Azure. Esses nós estão no mesmo cluster pelo nome de cluster comum e a configuração de nó de propagação. Use o seguinte processo para testar o cluster:

###<a name="step-1-get-the-internal-load-balancer-ip-for-both-the-regions-using-powershell"></a>Etapa 1: Obter o IP de Balanceador de carga interno para ambas as regiões usando o PowerShell
- Get-AzureInternalLoadbalancer - ServiceName "Hong Kong-c-svc-Oeste-us"
- Get-AzureInternalLoadbalancer - ServiceName "Hong Kong-c-svc-Leste-us"  

    Observe os endereços IP (por exemplo, oeste - 10.1.2.101 Leste - 10.2.2.101) exibidos.

###<a name="step-2-execute-the-following-in-the-west-region-after-logging-into-hk-w1-west-us"></a>Etapa 2: Execute o seguinte na região Oeste após fazer logon no Hong Kong-B1-Oeste-us
1.    Executar $CASS_HOME/bin/cqlsh 10.1.2.101 9160
2.  Execute os seguintes comandos CQL:

        CREATE KEYSPACE customers_ks
        WITH REPLICATION = { 'class' : 'NetworkToplogyStrategy', 'WESTUS' : 3, 'EASTUS' : 3};
        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
        SELECT * FROM Customers;

Você deverá ver uma exibição como aquele abaixo:

| customer_id | nome | Sobrenome |
| ----------- | --------- | -------- |
| 1           | John      | Cunha      |
| 2           | Laura      | Cunha      |


###<a name="step-3-execute-the-following-in-the-east-region-after-logging-into-hk-w1-east-us"></a>Etapa 3: Execute o seguinte na região Leste após fazer logon no Hong Kong-B1-Leste-us:
1.    Executar $CASS_HOME/bin/cqlsh 10.2.2.101 9160
2.  Execute os seguintes comandos CQL:

        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
        SELECT * FROM Customers;

Você deverá ver a mesma exibição conforme visto para a região Oeste:


| customer_id | nome | Sobrenome   |
|------------ | --------- | ---------- |
| 1           | John      | Cunha        |
| 2           | Laura      | Cunha        |


Executar inserções mais algumas e ver que aqueles replicados para Oeste-conosco parte do cluster.

## <a name="test-cassandra-cluster-from-nodejs"></a>Cluster de Cassandra de teste do Node
Usando uma das VMs Linux criada na "web" nível anteriormente, podemos executará um script Node simples para ler os dados inseridos anteriormente

**Etapa 1: Instalar o cliente de Cassandra e Node**

1. Instalar node e npm
2. Instale o pacote "cassandra-cliente de nó" usando npm
3. Execute o seguinte script no prompt shell que exibe a cadeia de caracteres json dos dados recuperados:

        var pooledCon = require('cassandra-client').PooledConnection;
        var ksName = "custsupport_ks";
        var cfName = "customers_cf";
        var hostList = ['internal_loadbalancer_ip:9160'];
        var ksConOptions = { hosts: hostList,
                             keyspace: ksName, use_bigints: false };

        function createKeyspace(callback){
           var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
           var sysConOptions = { hosts: hostList,  
                                 keyspace: 'system', use_bigints: false };
           var con = new pooledCon(sysConOptions);
           con.execute(cql,[],function(err) {
           if (err) {
             console.log("Failed to create Keyspace: " + ksName);
             console.log(err);
           }
           else {
             console.log("Created Keyspace: " + ksName);
             callback(ksConOptions, populateCustomerData);
           }
           });
           con.shutdown();
        }

        function createColumnFamily(ksConOptions, callback){
          var params = ['customers_cf','custid','varint','custname',
                        'text','custaddress','text'];
          var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
        var con =  new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) {
                 console.log("Failed to create column family: " + params[0]);
                 console.log(err);
              }
              else {
                 console.log("Created column family: " + params[0]);
                 callback();
              }
          });
          con.shutdown();
        }

        //populate Data
        function populateCustomerData() {
           var params = ['John','Infinity Dr, TX', 1];
           updateCustomer(ksConOptions,params);

           params = ['Tom','Fermat Ln, WA', 2];
           updateCustomer(ksConOptions,params);
        }

        //update will also insert the record if none exists
        function updateCustomer(ksConOptions,params)
        {
          var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) console.log(err);
              else console.log("Inserted customer : " + params[0]);
          });
          con.shutdown();
        }

        //read the two rows inserted above
        function readCustomer(ksConOptions)
        {
          var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,[],function(err,rows) {
              if (err)
                 console.log(err);
              else
                 for (var i=0; i<rows.length; i++)
                    console.log(JSON.stringify(rows[i]));
            });
           con.shutdown();
        }

        //exectue the code
        createKeyspace(createColumnFamily);
        readCustomer(ksConOptions)


## <a name="conclusion"></a>Conclusão
Microsoft Azure é uma plataforma flexível que permite a execução da Microsoft, bem como software de fonte aberta como demonstrou por este exercício. Clusters de Cassandra altamente disponíveis podem ser implantados em um único data center por meio da disseminação de nós de cluster em vários domínios de falhas. Clusters Cassandra também podem ser implantados em várias regiões Azure geograficamente distantes para sistemas de prova de desastres. Azure e Cassandra juntos possibilita a construção de altamente flexível e altamente disponível e serviços de nuvem recuperáveis desastres necessário pela internet hoje dimensionar serviços.  

##<a name="references"></a>Referências##
- [http://Cassandra.apache.org](http://cassandra.apache.org)
- [http://www.datastax.com](http://www.datastax.com)
- [http://www.nodejs.org](http://www.nodejs.org)
