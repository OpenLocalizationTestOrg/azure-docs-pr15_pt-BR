<properties
    pageTitle="Visão geral de HDInsight de seguro | Microsoft Azure"
    description="Aprenda..."
    services="hdinsight"
    documentationCenter=""
    authors="saurinsh"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/24/2016"
    ms.author="saurinsh"/>

# <a name="introduce-domain-joined-hdinsight-clusters-preview"></a>Apresentar domínio HDInsight clusters (prévia)

Azure HDInsight até hoje com suporte apenas um único administrador usuários locais. Isso funcionou muito bem para equipes de aplicativos ou departamentos menores. Como Hadoop com base em cargas de trabalho ganhou popularidade mais no setor enterprise, a necessidade de recursos de nível empresarial como active directory baseado autenticação, suporte a vários usuários e controle de acesso baseado em função se tornou cada vez mais importante. Usando o domínio HDInsight clusters, você pode criar um cluster de HDInsight associado a um domínio do Active Directory, configurar uma lista de funcionários da empresa que pode autenticar por meio do Active Directory do Azure para fazer logon no cluster HDInsight. Qualquer pessoa fora da empresa não consigo fazer logon ou acessar o cluster HDInsight. O administrador da empresa pode configurar o controle de acesso baseado em função para seção segurança usando [Apache Ranger](http://hortonworks.com/apache/ranger/), assim como restringir o acesso aos dados apenas tanto quanto necessário. Por fim, o administrador pode auditar o acesso a dados por funcionários e qualquer alteração feita às políticas de controle de acesso, portanto, alcançar um alto grau de gestão de seus recursos corporativos.

[AZURE.NOTE]> Novos recursos descritos nesta visualização estão disponíveis apenas em clusters baseados em Linux HDInsight para carga de trabalho de seção. Outras cargas de trabalho, como HBase, Spark, tempestade e Kafka, serão habilitadas em versões futuras. 

## <a name="benefits"></a>Benefícios

Segurança corporativa contém quatro pilares grande – segurança de perímetro, autenticação, autorização e criptografia.

![Domínio ingressou HDInsight clusters pilares de benefícios](./media/hdinsight-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

### <a name="perimeter-security"></a>Segurança de perímetro

Segurança de perímetro no HDInsight é alcançada usando redes virtuais e o serviço de Gateway. Hoje, um administrador da empresa pode criar um cluster de HDInsight dentro de uma rede virtual e usar grupos de segurança de rede (regras de firewall de entrada ou saída) para restringir o acesso à rede virtual. Somente os endereços IP definidos nas regras de firewall de entrada será capazes de se comunicar com o cluster HDInsight, proporcionando segurança de perímetro. Outra camada de segurança de perímetro é obtida usando o serviço de Gateway. O Gateway é o serviço que atua como primeira linha de defesa para qualquer solicitação de entrada ao cluster HDInsight. Ele aceita a solicitação, valida e só então permite que a solicitação passar para os outros nós em cluster, proporcionando a segurança de perímetro para outros nós de nome e dados no cluster.

### <a name="authentication"></a>Autenticação

Com essa visualização pública, um administrador de empresa pode provisionar um cluster de domínio HDInsight, em uma [rede virtual](https://azure.microsoft.com/services/virtual-network/). Os nós do cluster HDInsight ingressarão no domínio gerenciado pela empresa. Isso é feito através do uso do [Azure Active Directory Domain Services](https://technet.microsoft.com/library/cc770946.aspx). Todos os nós no cluster fazem parte de um domínio que gerencia a empresa. Com essa configuração, os funcionários da empresa podem fazer logon em nós de cluster usando suas credenciais de domínio. Eles também podem usar suas credenciais de domínio para autenticar com outros pontos de extremidade aprovados como o matiz, modos de exibição de Ambari, ODBC, JDBC, PowerShell e APIs REST para interagir com o cluster. O administrador tem controle total sobre limitando o número de usuários interagindo com cluster por esses pontos de extremidade.

### <a name="authorization"></a>Autorização

Uma prática recomendada seguida a maioria das empresas é que não cada funcionário tem acesso a todos os recursos da empresa. Da mesma forma, com esta versão, o administrador pode definir diretivas de controle de acesso baseado em função para os recursos de cluster. Por exemplo, o administrador pode configurar [Apache Ranger](http://hortonworks.com/apache/ranger/) para definir diretivas de controle de acesso para seção. Esta funcionalidade garante que os funcionários poderão acessar somente quantos dados precisam ser bem-sucedida seus trabalhos. Acesso SSH ao cluster também é restrito somente para o administrador.


### <a name="auditing"></a>Auditoria

Além de proteger os recursos de cluster HDInsight de usuários não autorizados e proteger os dados, é necessária para controlar o acesso não autorizado ou não intencional dos recursos de auditoria de acesso a todos os recursos de cluster e os dados. Com essa visualização, o administrador pode visualizar e relatar todo o acesso aos recursos de cluster HDInsight e dados. O administrador também pode exibir e relatar todas as alterações para as políticas de controle de acesso feitas em pontos de extremidade de Ranger Apache com suporte. Um cluster de domínio HDInsight usa familiar Apache Ranger interface do usuário para pesquisar os logs de auditoria. No back-end, Ranger usa [Apache Solr]( http://hortonworks.com/apache/solr/) para armazenar e pesquisa os logs.

### <a name="encryption"></a>Criptografia

Proteção de dados é importante para a reunião organizacional requisitos de segurança e conformidade e juntamente com restringindo o acesso aos dados de funcionários não autorizados, ele também deve ser protegido criptografando-o. Os armazenamentos de dados para clusters, Blob de armazenamento do Azure e armazenamento de Lucerne de dados do Azure HDInsight suportam transparente servidor [criptografia de dados](../storage/storage-service-encryption.md) inativos. Proteger HDInsight clusters perfeitamente funcionarão com essa criptografia de lado do servidor de dados em capacidade restante.

## <a name="next-steps"></a>Próximas etapas

- Para configurar um cluster de domínio HDInsight, consulte [Configurar domínio HDInsight clusters](hdinsight-domain-joined-configure.md).
- Para gerenciar clusters um domínio HDInsight, consulte [clusters de gerenciar domínio HDInsight](hdinsight-domain-joined-manage.md).
- Para configurar políticas de seção e executar consultas de seção, consulte [Configurar seção políticas para clusters de domínio HDInsight](hdinsight-domain-joined-run-hive.md).
- Para executar consultas de seção usando SSH em clusters de domínio HDInsight, consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-domain-joined-hdinsight-cluster).