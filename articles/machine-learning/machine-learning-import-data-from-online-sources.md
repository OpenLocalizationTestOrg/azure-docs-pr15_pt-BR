<properties
    pageTitle="Importar dados para Studio de aprendizado de máquina de fontes de dados online | Microsoft Azure"
    description="Como importar seus dados de treinamento do Azure Studio de aprendizado de máquina de várias origens onlinehttps."
    keywords="Importar dados, formato de dados, tipos de dados, fontes de dados, dados de treinamento"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="bradsev;garye" />


# <a name="import-data-into-azure-machine-learning-studio-from-various-online-data-sources-with-the-import-data-module"></a>Importar dados para o Azure Studio de aprendizado de máquina de várias fontes de dados online com o módulo de importar dados

Este artigo descreve o suporte para importar dados on-line de várias fontes e as informações necessárias para mover dados dessas fontes para uma experiência de aprendizado de máquina do Azure.

> [AZURE.NOTE] Este artigo fornece informações gerais sobre como [Importar dados] [ import-data] módulo. Para obter mais informações sobre os tipos de dados que você pode acessar, formatos, parâmetros e respostas a perguntas comuns, consulte o tópico de referência de módulo para [Importar dados] [ import-data] módulo.

<!-- -->

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

## <a name="introduction"></a>Introdução

Você pode acessar dados de dentro do Studio de aprendizado de máquina Azure uma das várias fontes de dados on-line enquanto seu experimento está em execução usando os [Dados de importação] [ import-data] módulo:

- Uma URL da Web usando HTTP
- Hadoop usando HiveQL
- Armazenamento de blob do Microsoft Azure
- Tabela do Microsoft Azure
- Banco de dados do SQL Azure ou o SQL Server na máquina virtual do Azure
- Banco de dados local do SQL Server
- Um provedor, atualmente OData feeds de dados
 
O fluxo de trabalho para conduzir experiências no Studio de aprendizado de máquina Azure consiste em componentes de arrastar e soltar para a tela. Para acessar fontes de dados online, adicione os [Dados de importação] [ import-data] módulo para seu experimento, selecione a **fonte de dados**e fornecer os parâmetros necessários para acessar os dados. As fontes de dados online com suporte são detalhadas na tabela abaixo. Esta tabela também resume os formatos de arquivo com suporte e os parâmetros que são usados para acessar os dados.

Observe que porque esses dados de treinamento são acessados enquanto seu experimento está em execução, ele só está disponível nesse experimento. Por comparação, os dados que foi armazenados em um módulo de conjunto de dados estão disponíveis para qualquer experimento em seu espaço de trabalho.

> [AZURE.IMPORTANT] Atualmente, os [Dados de importação] [ import-data] e [Exportar dados] [ export-data] módulos podem ler e gravar dados somente a partir de armazenamento do Azure criado usando o modelo de implantação do clássico. Em outras palavras, o novo tipo de conta de armazenamento de Blob do Azure que oferece um nível de acesso de armazenamento quente ou o nível de acesso de armazenamento interessantes ainda não é suportado. 

> Em geral, todas as contas de armazenamento do Azure que você possa ter criado antes dessa opção de serviço se tornou disponível não deve ser afetado. Se você precisar criar uma nova conta, selecione **clássico** para o modelo de implantação, ou use o Gerenciador de recursos e para **o tipo de conta**, selecione o **propósito geral** em vez de **armazenamento de Blob**. 

> Para obter mais informações, consulte [armazenamento de Blob do Azure: quente e interessantes níveis de armazenamento](../storage/storage-blob-storage-tiers.md).



## <a name="supported-online-data-sources"></a>Suporte a fontes de dados online
Módulo de aprendizado de máquina **Importar dados** Azure suporta as seguintes fontes de dados:

Fonte de dados | Descrição | Parâmetros |
---|---|---|
URL da Web via HTTP |Lê dados em valores separados por vírgula (CSV), valores separados por tabulações (TSV), o formato de arquivo de relação de atributo (ARFF) e formatos de suporte vetor máquinas (SVM-claro), de qualquer URL da web que usa HTTP | <b>URL</b>: Especifica o nome completo do arquivo, incluindo a URL do site e o nome de arquivo, com qualquer extensão. <br/><br/><b>Formato de dados</b>: especifica um dos dados com suporte formatos: CSV, TSV, ARFF ou luz SVM. Se os dados tiverem uma linha de cabeçalho, ele é usado para atribuir nomes de coluna.|
Hadoop/HDFS|Lê dados de armazenamento distribuído no Hadoop. Especificar os dados que você deseja usando HiveQL, uma linguagem de consulta SQL semelhante. HiveQL também pode ser usado para agregar dados e executar filtragem antes de adicionar os dados ao Studio de aprendizado de máquina de dados.|<b>Seção consulta de banco de dados</b>: Especifica a consulta de seção usada para gerar os dados.<br/><br/><b>URI do servidor de HCatalog</b> : especificado o nome do seu cluster usando o formato * &lt;nome do seu cluster&gt;. azurehdinsight.net.*<br/><br/><b>Nome de conta de usuário do Hadoop</b>: Especifica o nome de conta de usuário do Hadoop usado para configurar o cluster.<br/><br/><b>Senha de conta de usuário do Hadoop</b> : especifica as credenciais usadas ao provisionar o cluster. Para obter mais informações, consulte [criar Hadoop clusters no HDInsight](../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Local dos dados de saída</b>: Especifica se os dados são armazenados em um sistema de arquivo distribuído de Hadoop (HDFS) ou no Azure. <br/><ul>Se você armazenar dados de saída em HDFS, especifique o URI do servidor HDFS. (Certifique-se de usar o nome de cluster HDInsight sem o prefixo HTTPS://). <br/><br/>Se você armazenar seus dados de saída no Azure, especifique o nome da conta de armazenamento do Azure, a tecla de acesso de armazenamento e o nome do contêiner de armazenamento.</ul>|
Banco de dados SQL |Lê dados armazenados em um banco de dados do SQL Azure ou em um banco de dados do SQL Server em execução em uma máquina virtual Azure. | <b>Nome do servidor de banco de dados</b>: Especifica o nome do servidor no qual o banco de dados está em execução.<br/><ul>No caso de banco de dados do SQL Azure, insira o nome do servidor que é gerado. Geralmente, ela contém o formulário * &lt;generated_identifier&gt;. database.windows.net.* <br/><br/>No caso de um SQL server hospedado em uma máquina Virtual do Azure insira *tcp:&lt;nome de DNS de máquina Virtual&gt;, 1433*</ul><br/><b>Nome do banco de dados </b>: Especifica o nome do banco de dados no servidor. <br/><br/><b>Nome de conta de usuário do servidor</b>: especifica um nome de usuário para uma conta que tenha permissões de acesso para o banco de dados. <br/><br/><b>Senha de conta de usuário do servidor</b>: Especifica a senha da conta de usuário.<br/><br/><b>Aceitar qualquer certificado do servidor</b>: Use esta opção (menos segura) se desejar ignorar revisando o certificado de site antes de ler seus dados.<br/><br/><b>Consulta de banco de dados</b>: insira uma instrução SQL que descreve os dados que você deseja ler.|
Banco de dados local SQL |Lê os dados que são armazenados em um banco de dados do SQL no local. | <b>Gateway de dados</b>: Especifica o nome do Gateway de gerenciamento de dados instalado no computador onde ele pode acessar seu banco de dados do SQL Server. Para obter informações sobre como configurar o gateway, consulte [Executar avançados analytics com aprendizado de máquina do Azure usando dados de um SQL server local](machine-learning-use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Nome do servidor de banco de dados</b>: Especifica o nome do servidor no qual o banco de dados está em execução.<br/><br/><b>Nome do banco de dados </b>: Especifica o nome do banco de dados no servidor. <br/><br/><b>Nome de conta de usuário do servidor</b>: especifica um nome de usuário para uma conta que tenha permissões de acesso para o banco de dados. <br/><br/><b>Nome de usuário e senha</b>: clique em <b>valores de Enter</b> para inserir suas credenciais de banco de dados. Você pode usar autenticação integrada do Windows ou a autenticação do SQL Server dependendo de como seu local SQL Server está configurado.<br/><br/><b>Consulta de banco de dados</b>: insira uma instrução SQL que descreve os dados que você deseja ler.|
Tabela do Microsoft Azure|Lê dados do serviço de tabela no armazenamento do Azure.<br/><br/>Se você ler grandes quantidades de dados raramente, use o serviço de tabela do Azure. Ele fornece um flexível, não-relacionais (NoSQL), solução de armazenamento amplamente escaláveis, barato e altamente disponível.| As opções na **Importação de dados** mudam dependendo se você está acessando informações públicas ou uma conta de armazenamento privado que solicitar credenciais de login. Isso é determinado pelo <b>Tipo de autenticação</b> que pode ter o valor de "PublicOrSAS" ou "Conta", cada um deles tem seu próprio conjunto de parâmetros. <br/><br/><b>Público ou compartilhado acesso assinatura (SAS) URI</b>: os parâmetros são:<br/><br/><ul><b>URI de tabela</b>: Especifica o público ou a URL de SAS para a tabela.<br/><br/><b>Especifica as linhas para procurar nomes de propriedade</b>: os valores são <i>TopN</i> para examinar o número especificado de linhas ou <i>ScanAll</i> para obter todas as linhas na tabela. <br/><br/>Se os dados são previsíveis e homogêneos, é recomendável que você selecione *TopN* e insira um número para N. Para tabelas grandes, isso pode resultar em tempos de leitura mais rápidos.<br/><br/>Se os dados são estruturados com conjuntos de propriedades que variam com base na profundidade e a posição da tabela, escolha a opção de *ScanAll* para examinar todas as linhas. Isso garante a integridade da sua propriedade resultante e conversão de metadados.<br/><br/></ul><b>Conta de armazenamento particular</b>: os parâmetros são: <br/><br/><ul><b>Nome da conta</b>: Especifica o nome da conta que contém a tabela de ler.<br/><br/><b>Chave da conta</b>: Especifica a chave de armazenamento associada à conta.<br/><br/><b>Nome da tabela</b> : Especifica o nome da tabela que contém os dados de ler.<br/><br/><b>Linhas de verificação de nomes de propriedade</b>: os valores são <i>TopN</i> para examinar o número especificado de linhas ou <i>ScanAll</i> para obter todas as linhas na tabela.<br/><br/>Se os dados são previsíveis e homogêneos, é recomendável que você selecione *TopN* e insira um número para N. Para tabelas grandes, isso pode resultar em tempos de leitura mais rápidos.<br/><br/>Se os dados são estruturados com conjuntos de propriedades que variam com base na profundidade e a posição da tabela, escolha a opção de *ScanAll* para examinar todas as linhas. Isso garante a integridade da sua propriedade resultante e conversão de metadados.<br/><br/>|
Armazenamento de blob do Microsoft Azure | Lê dados armazenados no serviço de Blob no armazenamento do Azure, incluindo imagens, texto não estruturado ou dados binários.<br/><br/>Você pode usar o serviço de Blob para expor dados publicamente ou em particular armazenar dados de aplicativo. Você pode acessar seus dados de praticamente qualquer lugar usando conexões HTTP ou HTTPS. | Alterar as opções no módulo **Importar dados** dependendo se você está acessando informações públicas ou uma conta de armazenamento privado que solicitar credenciais de login. Isso é determinado pelo <b>Tipo de autenticação</b> que pode ter um valor do "PublicOrSAS" ou de "Conta".<br/><br/><b>Público ou compartilhado acesso assinatura (SAS) URI</b>: os parâmetros são:<br/><br/><ul><b>URI</b>: Especifica o público ou a URL de SAS para o blob de armazenamento.<br/><br/><b>Formato de arquivo</b>: Especifica o formato dos dados no serviço Blob. Os formatos com suporte são CSV, TSV e ARFF.<br/><br/></ul><b>Conta de armazenamento particular</b>: os parâmetros são: <br/><br/><ul><b>Nome da conta</b>: Especifica o nome da conta que contém o blob que você deseja ler.<br/><br/><b>Chave da conta</b>: Especifica a chave de armazenamento associada à conta.<br/><br/><b>Caminho para o contêiner, diretório ou blob</b> : Especifica o nome do blob que contém os dados de ler.<br/><br/><b>Formato de arquivo do blob</b>: Especifica o formato dos dados no serviço blob. Os formatos de dados com suporte são CSV, TSV, ARFF, CSV com uma codificação especificada e no Excel. <br/><br/><ul>Se o formato é TSV ou CSV, certifique-se de indicar se o arquivo contém uma linha de cabeçalho.<br/><br/>Você pode usar a opção do Excel para ler dados de pastas de trabalho do Excel. Na opção de <i>formato de dados do Excel</i> , indicar se os dados são em um intervalo de planilha do Excel, ou em uma tabela do Excel. Na opção de <i>planilha do Excel ou tabela inserida </i>, especifique o nome da planilha ou tabela que você deseja ler.</ul><br/>|
Provedor de Feed de dados | Lê dados de um provedor de feed com suporte. Atualmente somente o formato Open Data Protocol (OData) é suportado. | <b>Tipo de conteúdo de dados</b>: Especifica o formato de OData.<br/><br/><b>URL de origem</b>: Especifica a URL completa para o feed de dados. <br/>Por exemplo, a URL a seguir lê do banco de dados de exemplo Northwind: http://services.odata.org/northwind/northwind.svc/|


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/
