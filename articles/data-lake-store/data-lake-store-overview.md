<properties
   pageTitle="Visão geral do Azure dados Lucerne Store | Microsoft Azure"
   description="Entender o que é o armazenamento de Lucerne de dados do Azure e o valor que ele oferece sobre outros armazenamentos de dados"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="overview-of-azure-data-lake-store"></a>Visão geral do Azure Lucerne de repositório de dados

Azure Data Lucerne Store é um repositório de hyper escala de toda a empresa para analítico cargas de trabalho de grande volume. Lucerne de dados do Azure permite capturar dados de qualquer velocidade de tamanho, tipo e inclusão em um único lugar para análise operacional e exploratório.

> [AZURE.TIP] Use o [caminho de aprendizagem do armazenamento de dados de Lucerne](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) para começar a explorar o serviço de armazenamento de Lucerne de dados do Azure.

Armazenamento de Lucerne de dados do Azure podem ser acessado da Hadoop (disponível com HDInsight cluster) usando as APIs REST de WebHDFS compatível. Ele foi projetado especificamente para habilitar a análise de dados armazenados e é ajustado para desempenho para cenários de análise de dados. Prontos para uso, ele inclui todos os recursos de nível empresarial — segurança, capacidade de gerenciamento, escalabilidade, confiabilidade e disponibilidade — essenciais empresariais reais em casos de uso.


![Lucerne de dados do Azure](./media/data-lake-store-overview/data-lake-store-concept.png)

Alguns dos principais recursos da Lucerne de dados do Azure incluem o seguinte.

### <a name="built-for-hadoop"></a>Foram criados para Hadoop

O armazenamento do Azure dados Lucerne é um sistema de arquivo Hadoop Apache compatível com o sistema de arquivo distribuído da Hadoop (HDFS) e funciona com o ecossistema Hadoop.  Seus existente HDInsight aplicativos ou serviços que usam a API WebHDFS podem facilmente integrar com dados Lucerne Store. Armazenamento de dados de Lucerne também expõe uma interface compatível com o WebHDFS REST para aplicativos

Dados armazenados no repositório de Lucerne de dados podem ser analisados facilmente usando estruturas de analítico Hadoop como MapReduce ou seção. Microsoft Azurehdinsight clusters podem ser provisionados e configurados para acessar diretamente os dados armazenados no repositório de Lucerne de dados.

### <a name="unlimited-storage-petabyte-files"></a>Armazenamento ilimitado, arquivos de petabyte

Armazenamento de Lucerne de dados do Azure fornece armazenamento ilimitado e é adequado para armazenar uma variedade de dados para análise. Ele não impõe qualquer limites em tamanhos de conta, tamanhos de arquivo ou a quantidade de dados que podem ser armazenados em um Lucerne de dados. Arquivos individuais podem variar de quilobytes para petabytes tamanho tornando uma excelente opção para armazenar qualquer tipo de dados. Os dados são armazenados permanentemente fazendo várias cópias e não há nenhum limite a duração de tempo para o qual os dados podem ser armazenados em Lucerne os dados.

### <a name="performance-tuned-for-big-data-analytics"></a>Desempenho ajustado para análise de dados grande

Armazenamento de Lucerne de dados do Azure baseia-se para execução de sistemas analíticos que exigem produtividade grande para consultar e analisar grandes quantidades de dados de grande escala. A Lucerne dados páginas partes de um arquivo em um número de servidores de armazenamento individual. Isso melhora a produtividade leitura ao ler o arquivo em paralelo para executar a análise de dados.


### <a name="enterprise-ready-highly-available-and-secure"></a>Pronto para Enterprise: Altamente disponível e seguro

Armazenamento de Lucerne de dados do Azure fornece confiabilidade e disponibilidade de padrão da indústria. Seus ativos de dados são armazenados permanentemente, fazendo cópias redundantes para se proteger contra qualquer falhas inesperadas. Empresas podem usar Lucerne de dados do Azure em suas soluções como uma parte importante da sua plataforma de dados existente.

Armazenamento de dados de Lucerne também fornece segurança de nível empresarial para os dados armazenados. Para obter mais informações, consulte [Protegendo dados Azure dados Lucerne Store](#DataLakeStoreSecurity).


### <a name="all-data"></a>Todos os dados

Armazenamento de Lucerne de dados do Azure podem armazenar dados em seu formato nativo, como está, sem a necessidade de quaisquer transformações anteriores. Armazenamento de dados de Lucerne não exige um esquema para ser definido antes que os dados são carregados, deixando-o para cima até o framework analítico individual para interpretar os dados e definir um esquema no momento da análise. Ser capaz de armazenar arquivos de formatos e tamanhos aleatório possibilita dados Lucerne Store lidar com dados estruturados, semiestruturados e não estruturados.

Contêineres de armazenamento de Lucerne de dados do Azure para os dados são essencialmente pastas e arquivos. Você operam sobre os dados armazenados usando SDKs, o Portal do Azure e o Azure Powershell. Desde que você coloca seus dados na loja usando essas interfaces e usando os contêineres apropriados, você pode armazenar qualquer tipo de dados. Armazenamento de dados de Lucerne não executará nenhuma manipulação especial de dados com base no tipo de dados que ele armazena.


## <a name="DataLakeStoreSecurity"></a>Proteção de dados no repositório de Lucerne de dados do Azure

Armazenamento de Lucerne de dados do Azure usa Azure Active Directory para autenticação e listas de controle de acesso (ACLs) para gerenciar o acesso aos seus dados.

| Recurso                                 | Descrição                              |
|-----------------------------------------|------------------------------------------|
| Autenticação | Armazenamento de Lucerne de dados do Azure integra-se com o Azure Active Directory (AAD) para gerenciamento de identidades e acesso de todos os dados armazenados no repositório de Lucerne de dados do Azure. Como resultado a integração, benefícios do Azure dados Lucerne de todos os recursos AAD incluindo autenticação multifator, acesso condicional, controle de acesso baseado em função, monitoramento de uso de aplicativos, segurança monitoração e alertas, etc. Armazenamento de Lucerne de dados do Azure suporta o protocolo OAuth 2.0 para autenticação com na interface REST. |
| Controle de acesso                          | Armazenamento de Lucerne de dados do Azure fornece controle de acesso, permissões de estilo POSIX expostas pelo protocolo WebHDFS de suporte. Na versão atual, ACLs podem ser ativadas na pasta raiz, subpastas, bem como arquivos individuais. As ACLs que aplicar à pasta raiz irá também aplicável a todos os filhos pastas/arquivos também.|

Quer saber mais sobre como proteger os dados no armazenamento de Lucerne de dados. Siga os links abaixo.

* Para obter instruções sobre como proteger os dados no repositório de Lucerne de dados, consulte [Protegendo dados Azure dados Lucerne Store](data-lake-store-secure-data.md).
* Prefere vídeos? [Assista a este vídeo](https://mix.office.com/watch/1q2mgzh9nn5lx) sobre como proteger os dados armazenados no repositório de Lucerne de dados.

## <a name="applications-compatible-with-azure-data-lake-store"></a>Aplicativos compatíveis com o armazenamento de Lucerne de dados do Azure

Azure Data Lucerne Store é compatível com os componentes de fonte abertos mais no ecossistema do Hadoop. Ele também se integra perfeitamente com outros serviços do Azure. Isso tornará o armazenamento de dados de Lucerne uma opção ideal para suas necessidades de armazenamento de dados. Siga os links abaixo para saber mais sobre como o armazenamento de dados de Lucerne pode ser usado tanto com componentes de fonte aberta, bem como outros serviços do Azure.

* Consulte [aplicativos e serviços compatíveis com o armazenamento de Lucerne de dados do Azure](data-lake-store-compatible-oss-other-applications.md) para uma lista de aplicativos de abrir origem interoperabilidade com dados Lucerne Store.
* Consulte [integrando com outros serviços do Azure](data-lake-store-integrate-with-other-services.md) para entender como o armazenamento de dados de Lucerne pode ser usado com outros serviços do Azure para habilitar uma ampla variedade de cenários.
* Consulte [cenários para uso do armazenamento de dados de Lucerne](data-lake-store-data-scenarios.md) para aprender a usar o armazenamento de Lucerne de dados em cenários como a inclusão de dados, processamento de dados, baixando dados e visualização de dados.

## <a name="what-is-azure-data-lake-store-file-system-adl"></a>O que é o sistema de arquivos de armazenamento do Azure dados Lucerne (adl: / /)?

Armazenamento de Lucerne de dados podem ser acessado através do novo sistema de arquivos, o AzureDataLakeFilesystem (adl: / /), em ambientes de Hadoop (disponíveis com cluster HDInsight). Aplicativos e serviços que usam adl: / / são capazes de aproveitar ainda mais otimização de desempenho que não estão disponíveis atualmente no WebHDFS. Como resultado, armazenamento de dados de Lucerne lhe flexibilidade para avaliar ou o melhor desempenho com a opção recomendada do uso adl: / / ou manter o código existente ao continuar a usar a API WebHDFS diretamente. Azure HDInsight totalmente utiliza o AzureDataLakeFilesystem para fornecer o melhor desempenho no repositório de Lucerne de dados.

Você pode acessar seus dados no repositório de Lucerne dados usando `adl://<data_lake_store_name>.azuredatalakestore.net`. Para obter mais informações sobre como acessar os dados na loja de Lucerne de dados, consulte [Exibir propriedades dos dados armazenados](data-lake-store-get-started-portal.md#properties)

## <a name="how-do-i-start-using-azure-data-lake-store"></a>Como para começar a usar o armazenamento de Lucerne de dados do Azure?

Consulte [Introdução ao armazenamento de Lucerne de dados usando o Portal do Azure](data-lake-store-get-started-portal.md), sobre como provisionar um armazenamento de Lucerne de dados usando o Portal do Azure. Depois que você tiver provisionado Lucerne de dados do Azure, você pode Saiba como usar ofertas de dados grande como Azure dados Lucerne Analytics ou Azurehdinsight com armazenamento de Lucerne de dados. Você também pode criar um aplicativo .NET para criar uma conta de armazenamento de Lucerne de dados do Azure e executar operações como dados de carregamento, baixar dados, etc.

- [Introdução ao Azure dados Lucerne Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com dados Lucerne Store](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Introdução ao armazenamento de Lucerne de dados do Azure usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)


## <a name="data-lake-store-videos"></a>Vídeos de armazenamento de Lucerne de dados

Se você preferir assistindo a vídeos para aprender, dados Lucerne Store fornece vídeos em um intervalo de recursos.

* [Criar uma conta de armazenamento de Lucerne de dados do Azure](https://mix.office.com/watch/1k1cycy4l4gen)
* [Use o Explorador de dados para gerenciar dados no repositório de Lucerne de dados do Azure](https://mix.office.com/watch/icletrxrh6pc)
* [Conectar dados Azure Lucerne Analytics para armazenamento de Lucerne de dados do Azure](https://mix.office.com/watch/qwji0dc9rx9k)
* [Armazenamento de Lucerne de dados do Access Azure via Lucerne a análise de dados](https://mix.office.com/watch/1n0s45up381a8)
* [Conectar o Azure HDInsight ao armazenamento de Lucerne de dados do Azure](https://mix.office.com/watch/l93xri2yhtp2)
* [Armazenamento de Lucerne de dados do Access Azure por meio de seção e porco](https://mix.office.com/watch/1n9g5w0fiqv1q)
* [Usar DistCp (cópia distribuído Hadoop) para copiar dados para e do repositório de Lucerne de dados do Azure](https://mix.office.com/watch/1liuojvdx6sie)
* [Usar Apache Sqoop para mover dados entre o armazenamento de Lucerne de dados do Azure e fontes relacionais](https://mix.office.com/watch/1butcdjxmu114)
* [Organização de dados usando o Azure dados fábrica para armazenamento de Lucerne de dados do Azure](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Proteção de dados no repositório de Lucerne de dados do Azure](https://mix.office.com/watch/1q2mgzh9nn5lx)



