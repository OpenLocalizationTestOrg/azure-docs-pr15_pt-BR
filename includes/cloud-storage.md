#<a name="data-management-and-business-analytics"></a>Gerenciamento de dados e análise de negócios

Gerenciar e analisar dados na nuvem são tão importante assim como qualquer outro lugar. Para que você possa fazer isso, o Azure fornece uma variedade de tecnologias para trabalhar com dados relacionais e não relacionais. Este artigo apresenta cada uma das opções. 

##<a name="table-of-contents"></a>Sumário      

- [Armazenamento de blob](#blob)
- [Executando um DBMS em uma máquina Virtual](#dbinvm)
- [Banco de dados SQL](#sqldb)
    - [Sincronização de dados do SQL](#datasync)
    - [Relatórios de dados do SQL usando máquinas virtuais](#datarpt)
- [Armazenamento de tabela](#tblstor)
- [Hadoop](#hadoop)

## <a name="blob"></a>Armazenamento de blob

A palavra "blob" é a abreviação de "objeto binário grande" e descreve exatamente quais um blob: um conjunto de informações binárias. Ainda mesmo que elas são simples, blobs são bastante útil. [Figura 1](#Fig1) ilustra as Noções básicas do armazenamento de Blob do Azure.

<a name="Fig1"></a>![Diagrama de Blobs][blobs]
 
**Figura 1: O armazenamento de Blob do Azure armazena dados binários - blobs - em contêineres.**

Para usar blobs, você primeiro criar uma *conta de armazenamento*do Azure. Como parte desse, você especificar o Azure data center que armazenará os objetos que você cria usando esta conta. Onde quer que ele reside, cada blob que criar pertence a alguns contêiner na sua conta de armazenamento. Para acessar um blob, um aplicativo fornece uma URL com o formulário:

http://&lt;*StorageAccount*&gt;.blob.core.windows.net/&lt;*contêiner*&gt;/&lt;*BlobName*&gt;

&lt;*StorageAccount* &gt; é um identificador exclusivo atribuído quando uma nova conta de armazenamento é criada, enquanto &lt; *contêiner* &gt; e &lt; *BlobName* &gt; são os nomes de um contêiner específico e um blob dentro desse contêiner. 

Azure fornece dois tipos diferentes de blobs. As opções são:

- *Bloco* blobs, cada um dos quais pode conter até 200 gigabytes de dados. Como o nome sugere, um blob de bloco é subdividido em algum número de blocos. Se ocorrer uma falha ao transferir um blob de bloco, retransmissão pode continuar com o bloco mais recente em vez de enviar o blob inteiro novamente. Bloco blobs são uma abordagem bastante geral ao armazenamento e elas são o tipo de blob mais comumente usados hoje.

- Blobs de *página* , que podem ser tão grandes em um terabytes. Blobs de página foram projetados para acesso aleatório e, portanto, cada um é dividido em algum número de páginas. Um aplicativo é gratuito ler e gravar páginas individuais aleatoriamente no blob. No Azure máquinas virtuais por exemplo, VMs criar usam blobs de página como armazenamento persistente para ambos OS discos e dados.

Se você escolher bloco blobs ou blobs de página, aplicativos podem acessar dados blob de várias maneiras diferentes. As opções incluem o seguinte:

- Diretamente por meio de um RESTful (ou seja, baseada em HTTP) acessar protocolo. Aplicativos do Azure e aplicativos externos, incluindo aplicativos em execução no local, podem usar esta opção.
- Usando a biblioteca de cliente de armazenamento do Azure, que fornece uma interface de desenvolvedor mais amigável sobre o protocolo de acesso bruto blob RESTful. Novamente, aplicativos do Azure e aplicativos externos podem acessar blobs usando essa biblioteca.
- Usando o Azure unidades, uma opção que permite que um aplicativo do Azure trate um blob de página como uma unidade local com um sistema de arquivo NTFS. Para o aplicativo, o blob de página se parece com um sistema de arquivos do Windows ordinário acessado usando e/s de arquivo padrão. Na verdade, lê e grava são enviadas para o blob de página subjacente que implementa a unidade do Azure. 

Para proteger contra falhas de hardware e melhorar a disponibilidade, cada blob é duplicada entre três computadores em um data center Azure. Gravar um blob atualiza todas as três cópias, portanto leituras posteriores não verá resultados inconsistentes. Você também pode especificar que os dados de um blob devem ser copiados para outro data center Azure no mesma geográfica, mas pelo menos 500 milhas ausente. Essa cópia, chamado *replicação geográfica*, acontece em poucos minutos de uma atualização para o blob e é útil para recuperação de dados.

Dados em bolhas também podem ser disponibilizados via a *Rede de entrega de conteúdo (CDN)*do Azure. Armazenando cópias de dados de blob em dezenas de servidores em todo o mundo, a CDN pode acelerar o acesso às informações que são acessadas repetidamente. 

Simples como estão, blobs são a escolha certa em várias situações. Armazenar e streaming de áudio e vídeo estão exemplos óbvios, como backups e outros tipos de arquivamento de dados. Os desenvolvedores também podem usar blobs para conter qualquer tipo de dados não estruturados desejarem. Ter uma maneira simples para armazenar e acessar dados binários pode ser obviamente úteis.


## <a name="dbinvm"></a>Executando um DBMS em uma máquina Virtual

Muitos aplicativos hoje dependem de algum tipo de sistema de gerenciamento de banco de dados (DBMS). Sistemas relacionais como o SQL Server são escolha usados com mais frequência, mas não relacionais abordagens, geralmente conhecidas como tecnologias de *NoSQL* , obtenha mais populares diariamente. Para permitir que aplicativos em nuvem usar essas opções de gerenciamento de dados, máquinas virtuais do Azure permite que você execute um DBMS (relacional ou NoSQL) em uma máquina virtual. [Figura 2](#Fig2) mostra como isso se parece com o SQL Server.

<a name="Fig2"></a>![Diagrama do SQL Server em uma máquina Virtual][SQLSvr-vm]
 
**Figura 2: Azure máquinas virtuais permite executar um DBMS uma máquina virtual, com persistência fornecida pelo blobs.**

Para desenvolvedores e administradores de banco de dados, este cenário parece ser executado o mesmo software em seu próprio data center. No exemplo mostrado aqui, por exemplo, quase todos os recursos do SQL Server podem ser usados, e você tem acesso administrativo total ao sistema. Você também tem a responsabilidade de gerenciar o servidor de banco de dados, é claro, como se fosse executado localmente.

Como mostra a [Figura 2](#Fig2) , seus bancos de dados parecem estar armazenados no disco local da máquina virtual execução do servidor. Nos bastidores, no entanto, cada um desses discos é gravada um Azure blob. (É semelhante ao uso de uma SAN em seu próprio data center, com um blob muito atuando como um LUN). Como com qualquer Azure blob, os dados que ele contém são replicados três vezes em um data center e, se você solicitar, replicado geográfica para outra data center na mesma região. Também é possível usar opções como espelhamento para maior confiabilidade de banco de dados do SQL Server. 

Outra maneira de usar o SQL Server em uma máquina virtual é criar um aplicativo híbrido, onde os dados residem no Azure enquanto a lógica do aplicativo é executado no local. Por exemplo, isso pode fazer sentido quando aplicativos executados em vários locais ou em vários dispositivos móveis devem compartilhar os mesmos dados. Para tornar a comunicação entre a mais simples de lógica nuvem banco de dados e local, uma organização pode usar uma rede Virtual do Azure para criar uma conexão de rede virtual privada (VPN) entre um data center Azure e sua própria data center local.


## <a name="sqldb"></a>Banco de dados SQL

Para muitas pessoas, executar um DBMS uma máquina virtual é a primeira opção que vem à mente para o gerenciamento de dados estruturados na nuvem. Ele é não é a única opção, porém, nem sempre é a melhor opção. Em alguns casos, o gerenciamento de dados usando uma plataforma como uma abordagem de serviço (PaaS) faz mais sentido. Azure fornece uma tecnologia de PaaS chamada banco de dados SQL que permite fazer isso para dados relacionais. [Figura 3](#Fig3) ilustra essa opção. 

<a name="Fig3"></a>![Diagrama de banco de dados SQL][SQL-db]
 
**Figura 3: O banco de dados SQL fornece um serviço de armazenamento relacional PaaS compartilhado.**

Banco de dados SQL não dá a cada cliente sua própria física instância do SQL Server. Em vez disso, ele oferece um serviço de vários locatário, com um servidor de banco de dados SQL lógico para cada cliente. Todos os clientes compartilhem a capacidade de computação e armazenamento que fornece o serviço. E como com armazenamento de Blob, todos os dados no banco de dados SQL é armazenada em três computadores separados em um data center Azure, dando a seus bancos de dados interna alta disponibilidade (HA).

Para um aplicativo, o banco de dados SQL é muito parecida do SQL Server. Aplicativos podem emitir consultas SQL em tabelas relacionais, usar procedimentos armazenados T-SQL e executar transações em várias tabelas. E porque aplicativos acessem o banco de dados do SQL usando o protocolo de fluxo de dados Tabular (TDS), o mesmo protocolo usado para acessar o SQL Server, eles podem trabalhar com dados usando a estrutura de entidades, ADO.NET, JDBC e outras interfaces de acesso de dados conhecidos. 

Mas como banco de dados SQL é um serviço de nuvem em execução em Azure data centers, você não precisa gerenciar qualquer um dos aspectos físicos do sistema, como o uso do disco. Você também não precisa se preocupar sobre atualização de software ou outras tarefas administrativas raio de tratamento. Cada organização do cliente ainda controla seus próprios bancos de dados, é claro, incluindo seus esquemas e o logon do usuário, mas muitas das tarefas administrativas comuns são feitas por você. 

Enquanto o banco de dados SQL é muito parecida com SQL Server para aplicativos, ele não se comporta exatamente como um DBMS em execução em um computador virtual ou físico. Porque ele é executado em hardware compartilhado, seu desempenho variará com a carga colocada em que o hardware por todos os seus clientes. Isso significa que o desempenho de, digamos, um procedimento armazenado no banco de dados SQL pode variar de um dia para o próximo. 

Atualmente, o banco de dados SQL permite que você crie um banco de dados mantém até 150 gigabytes. Se você precisa trabalhar com bancos de dados maiores, o serviço oferece uma opção chamada *federação*. Para fazer isso, um administrador de banco de dados cria duas ou mais *membros de Federação*, cada um dos quais é um banco de dados separado com seu próprio esquema. Dados estiver distribuídos entre esses membros, algo que muitas vezes é conhecido como *fragmentação*, com cada membro atribuído uma única *chave de Federação*. Um aplicativo problemas SQL consultas esses dados, especificando a chave de federação que identifica o membro de Federação a consulta devem direcionar. Isso permite usando uma abordagem de relacional tradicional com grandes quantidades de dados. Como sempre, há concessões; consultas nem transações podem abranger membros de federação, por exemplo. Mas quando um serviço de PaaS relacional é a melhor opção e essas compensações são aceitáveis, usando a federação de SQL pode ser uma boa solução.

Banco de dados SQL podem ser usado por aplicativos em execução no Azure ou em outro local, como no seu centro de dados local. Isso é útil para aplicativos de nuvem que precisam de dados relacionais, bem como aplicativos que podem se beneficiar armazenar dados na nuvem local. Um aplicativo móvel pode depender de banco de dados SQL para gerenciar compartilhados dados relacionais, por exemplo, como pode um aplicativo de estoque que é executado em vários revendedores em todo o mundo.

Pensar sobre o banco de dados SQL gera um problema óbvio (e importante): quando você deve executar SQL Server em uma máquina virtual e quando é banco de dados SQL a melhor opção? Normalmente, há concessões e então depende de qual abordagem é melhor suas necessidades. 

É uma maneira simples de pensar sobre ele exibir o banco de dados SQL como sendo de novos aplicativos, enquanto o SQL Server em uma máquina virtual é uma opção melhor quando você estiver movendo um existente local aplicativo na nuvem. Ele também pode ser útil observar essa decisão de forma mais refinada, no entanto. Por exemplo, o banco de dados SQL é fácil de usar, já que não há mínimo instalação e administração. Mas executando o SQL Server em uma máquina virtual pode ter desempenho mais previsível - não é um serviço compartilhado - e também é compatível com bancos de dados maiores não federado que banco de dados do SQL. Ainda, o banco de dados SQL fornece internos de replicação de dados e processamento, efetivamente dando a você um DBMS de alta disponibilidade com muito pouco trabalho. Enquanto o SQL Server oferece mais controle e um pouco mais amplo conjunto de opções, o banco de dados SQL é mais simples configurar e significativamente menos trabalho para gerenciar.

Finalmente, é importante destacar que o banco de dados SQL não é o único serviço de dados de PaaS disponível no Azure. Parceiros Microsoft fornecem outras opções também. Por exemplo, ClearDB oferece uma MySQL PaaS oferecendo, enquanto Cloudant vende uma opção de NoSQL. Serviços de dados de PaaS são a solução certa em muitas situações, e então essa abordagem de gerenciamento de dados é uma parte importante do Azure.


### <a name="datasync"></a>Sincronização de dados do SQL

Enquanto o banco de dados SQL manter três cópias de cada banco de dados em um data center de Azure único, ela automaticamente não replica dados entre dos data centers do Azure. Em vez disso, ele oferece sincronização de dados do SQL, um serviço que você pode usar para fazer isso. [Figura 4](#Fig4) mostra como isso é a aparência.

<a name="Fig4"></a>![Diagrama de sincronização de dados do SQL][SQL-datasync]
 
**Figura 4: Sincronização de dados do SQL sincroniza dados no banco de dados SQL com os dados em outro Azure e data centers locais.**

Como mostra o diagrama, a sincronização de dados do SQL pode sincronizar dados em diferentes locais. Suponha que você está executando um aplicativo em vários data centers Azure, por exemplo, com dados armazenados no banco de dados SQL. Você pode usar a sincronização de dados do SQL para manter os dados sincronizados. Sincronização de dados do SQL também pode sincronizar dados entre um data center Azure e uma instância do SQL Server em execução em um data center local. Isso pode ser útil para manter uma cópia local dos dados usados por aplicativos de local e uma cópia de nuvem usado por aplicativos em execução no Azure. E, embora ele não for mostrado na figura, a sincronização de dados do SQL também podem ser usada para sincronizar dados entre o banco de dados SQL e executar uma máquina virtual no Azure ou em outro lugar do SQL Server.

Sincronização pode ser bidirecional e determinar exatamente quais dados são sincronizados e frequência ele é feito. (Sincronização entre bancos de dados não é atômica, porém - sempre há pelo menos alguns atraso) E no entanto, ele é usado, configurando a sincronização com a sincronização de dados do SQL é totalmente orientada por configurações; Não há nenhum código para escrever.


### <a name="datarpt"></a>Relatórios de dados do SQL usando máquinas virtuais

Depois de um banco de dados contiver dados, alguém provavelmente desejará criar relatórios usando dados. Azure pode executar SQL Server Reporting Services (SSRS) no Azure máquinas virtuais, que equivale funcionalidade à execução do SQL Server Reporting Services no local. Em seguida, você pode usar SSRS para executar relatórios sobre dados armazenados em um banco de dados do SQL Azure.  [Figura 5](#Fig5) mostra como funciona o processo.

<a name="Fig5"></a>![Diagrama de relatórios do SQL][SQL-report]
 
**Figura 5: O SQL Server Reporting Services em execução em um máquinas virtuais do Azure fornece serviços de relatórios de dados no banco de dados SQL. .**

Antes de um usuário pode ver um relatório, alguém define esse relatório deve aparência (etapa 1). Com SSRS em uma máquina virtual, isso pode ser feito usando uma das duas ferramentas: ferramentas de dados do SQL Server, parte do SQL Server 2012 ou seu antecessor, o Business Intelligence (BI) Development Studio. Como com SSRS, estas definições de relatório são expressas na linguagem de definição de relatório (RDL). Após os arquivos RDL de um relatório tiveram sido criados, eles são carregados para uma máquina virtual na nuvem (etapa 2). A definição de relatório agora está pronta para usar.

Em seguida, um usuário do aplicativo acessa o relatório (etapa 3). O aplicativo passa a solicitação para o SSRS VM (etapa 4), que o banco de dados do SQL ou outras fontes de dados para obter os dados necessários de contatos (etapa 5). SSRS usa esses dados e os arquivos RDL relevantes para renderizar o relatório (etapa 6), em seguida, retorna o relatório para o aplicativo (etapa 7), que exibe para o usuário (etapa 8).

Inserção de um relatório em um aplicativo, o cenário mostrado aqui, não é a única opção. Também é possível exibir relatórios no Gerenciador de relatórios na máquina virtual, SharePoint na máquina virtual ou de outras maneiras. Relatórios também podem ser combinados, com um relatório contendo um link para outro.

SSRS em uma máquina virtual do Azure fornece todas as funcionalidades como uma solução de relatório na nuvem. Relatórios podem usar qualquer fonte de dados com suporte pelo SSRS. Aplicativos e relatórios podem incluir código inserido ou conjuntos para oferecer suporte a comportamentos personalizados. Processamento e a execução do relatório são rápidos porque o mecanismo e o conteúdo do servidor de relatório executados juntos no mesmo servidor virtual.



## <a name="tblstor"></a>Armazenamento de tabela

Dados relacionais são útil em muitas situações, mas nem sempre é a escolha certa. Se seu aplicativo precisa de acesso rápido e simple a grandes quantidades de dados estruturados livremente, por exemplo, talvez o banco de dados relacional não funcionar bem. Uma tecnologia NoSQL é provavelmente a melhor opção.

Armazenamento de tabela do Azure é um exemplo desse tipo de abordagem NoSQL. Apesar do nome, o armazenamento de tabela não dá suporte a tabelas relacionais padrão. Em vez disso, ele oferece o que é conhecido como *chave/valor armazenar*associando um conjunto de dados uma determinada chave, em seguida, permitindo que um aplicativo acesso esses dados, fornecendo a chave. [Figura 6](#Fig6) ilustra as Noções básicas.

<a name="Fig6"></a>![Diagrama de armazenamento de tabela][SQL-tblstor]
 
**Figura 6: Azure armazenamento de tabela é um armazenamento de chave/valor que fornece rápido e acesso simples às grandes quantidades de dados.**

Como blobs, cada tabela está associada uma conta de armazenamento do Azure. Tabelas são também denominadas muito como blobs, com uma URL do formulário

http://&lt;*StorageAccount*&gt;.table.core.windows.net/&lt;*nome de tabela*&gt;

Conforme mostrado na figura, cada tabela é dividida em algum número de partições, cada uma delas pode ser armazenada em uma máquina separada. (Isso é uma forma de fragmentação, como com a federação de SQL.) Aplicativos do Azure e aplicativos em execução em outro lugar podem acessar uma tabela usando o protocolo OData RESTful ou a biblioteca de cliente de armazenamento do Azure.

Cada partição em uma tabela contém algum número de *entidades*, cada um contendo até 255 *Propriedades*. Cada propriedade tem um nome, um tipo (como binário, Bool, DateTime, Int ou cadeia de caracteres) e um valor. Ao contrário de armazenamento relacional, essas tabelas não tem nenhum esquema fixa, e entidades tão diferentes na mesma tabela podem conter propriedades com tipos diferentes. Uma entidade pode ter apenas uma propriedade de cadeia de caracteres contendo um nome, por exemplo, enquanto outra entidade na mesma tabela tem duas propriedades Int que contém um número de identificação de cliente e uma classificação de crédito.

Para identificar uma entidade específica dentro de uma tabela, um aplicativo fornece chave dessa entidade. A tecla tem duas partes: uma *chave de partição* que identifica uma partição específica e uma *tecla de linha* que identifica uma entidade dessa partição. Na [Figura 6](#Fig6), por exemplo, o cliente solicita a entidade com chaves de partição A e linha 3 e armazenamento de tabela retorna entidade, incluindo todas as propriedades que ele contém.

Essa estrutura permite tabelas ser grande - uma única tabela pode conter até 100 terabytes de dados - e permite acesso rápido para os dados que elas contêm. Ele também apresenta limitações, no entanto. Por exemplo, há não há suporte para atualizações de transações que se estendem por tabelas ou até mesmo partições em uma única tabela. Um conjunto de atualizações a uma tabela só pode ser agrupado em uma transação atômica se todas as entidades envolvidas estiverem na mesma partição. Há também nenhuma maneira de uma tabela de consulta se baseia no valor de suas propriedades, nem há suporte para junções em várias tabelas. E ao contrário de bancos de dados relacionais, tabelas têm não há suporte para procedimentos armazenados.

Armazenamento de tabela do Azure é uma boa opção para aplicativos que precisam de acesso rápido e barato a grandes quantidades de dados estruturados livremente. Por exemplo, um aplicativo da Internet que armazena informações de perfil para muitos usuários pode usar tabelas. Acesso rápido é importante nessa situação, e o aplicativo não precisa a capacidade do SQL. Desistir essa funcionalidade para obter a velocidade e o tamanho, às vezes, pode fazer sentido, e então o armazenamento de tabela é apenas a solução certa para alguns problemas.


## <a name="hadoop"></a>Hadoop

Organizações tiveram sido Criando depósitos de dados por décadas. Essas coleções de informações, com mais frequência armazenadas em tabelas relacionais, permitir que as pessoas trabalham com e aprender com dados de muitas maneiras diferentes. Com o SQL Server, por exemplo, é comum usar ferramentas como SQL Server Analysis Services para fazer isso.

Mas suponha que você deseja fazer a análise de dados não-relacionais. Seus dados podem demorar vários formulários: informações de sensores ou marcas RFID, arquivos de log em farms do servidor, dados de sequência de cliques produzidos por aplicativos web, imagens de dispositivos de diagnósticos médicos e muito mais. Esses dados também podem ser realmente grande, grande demais para ser usado efetivamente com um data warehouse tradicional. Problemas de grande volume assim, raros apenas alguns anos atrás, agora tornaram bastante comum.

Para analisar esse tipo de dados grandes, a nossa indústria amplamente chegou em uma única solução: a tecnologia de código-fonte aberto Hadoop. Hadoop é executado em um cluster de máquinas físicas ou virtuais, difundir os dados que ele funciona nessas máquinas e processamento em paralelo. Mais máquinas Hadoop tem usar, mais rápido ele pode completar tudo o que ele trabalhar está fazendo.

Esse tipo de problema é um ajuste natural para a nuvem pública. Em vez de manter um VMs Exército dos servidores que talvez inativo grande parte do tempo, executando Hadoop na nuvem permite que você crie local (e remuneração por) somente quando você precisar deles. Melhor, mais os dados de grande que você deseja analisar com Hadoop é criada na nuvem, economizando o problema de movê-lo ao redor. Para ajudá-lo a explorar estas sinergias, a Microsoft fornece um serviço de Hadoop no Azure. [Figura 7](#Fig7) mostra os componentes mais importantes desse serviço.

<a name="Fig7"></a>![Diagrama do hadoop][hadoop]

**Figura 7: Hadoop no Azure executa trabalhos de MapReduce que processam dados em paralelo usando várias máquinas virtuais.**

Para usar Hadoop no Azure, você primeiro peça essa plataforma de nuvem para criar um cluster de Hadoop, especificando o número de VMs necessárias. Configurando um cluster de Hadoop por conta própria é uma tarefa incomum, e então permitindo que o Azure fazer isso por você faz sentido. Quando você terminar usando o cluster, você fechá-lo. Não é necessário para pagar recursos de computação que não estiver usando.

Um aplicativo do Hadoop, normalmente chamado de um *trabalho*, usa um modelo de programação conhecido como *MapReduce*. Conforme mostrado na figura, a lógica para um trabalho MapReduce é executada simultaneamente em várias VMs. Processando dados em paralelo, Hadoop pode analisar dados muito mais rapidamente que o computador único soluções.

No Azure, os dados de um MapReduce trabalho funciona em normalmente mantida no armazenamento de blob. Em Hadoop, no entanto, MapReduce trabalhos esperam dados sejam armazenados no *Sistema de arquivo distribuído da Hadoop (HDFS)*. HDFS é semelhante ao armazenamento de Blob de algumas maneiras; ele replica dados entre vários servidores físicos, por exemplo. Em vez de duplicar essa funcionalidade, Hadoop no Azure expõe em vez disso, o armazenamento de Blob por meio da API de HDFS, como mostra a figura. Enquanto a lógica em um trabalho MapReduce pensa que está acessando arquivos HDFS comuns, o trabalho na verdade está trabalhando com dados transmitidos-lo de blobs. E para oferecer suporte no caso onde vários trabalhos são executados sobre os mesmos dados, Hadoop no Azure também permitir copiando dados do blobs para HDFS completo executado em VMs. 

Trabalhos de MapReduce comumente escritos em Java hoje, uma abordagem que dá suporte a Hadoop no Azure. A Microsoft também adicionou suporte para a criação de trabalhos MapReduce em outros idiomas, incluindo c#, F # e JavaScript. O objetivo é tornar essa tecnologia de grande volume mais acessíveis a um grupo maior de desenvolvedores.

Além de HDFS e MapReduce, Hadoop inclui outras tecnologias que permitem que as pessoas analisar dados sem escrever um trabalho MapReduce próprios. Por exemplo, porco é uma linguagem de alto nível desenvolvida para análise de dados grandes, enquanto seção oferece uma linguagem de SQL semelhante chamada HiveQL. Porco e seção realmente geram trabalhos de MapReduce que processam dados HDFS, mas eles ocultam essa complexidade de seus usuários. Ambos são fornecidos com Hadoop no Azure.

A Microsoft também fornece um driver de HiveQL para o Excel. Usando um suplemento do Excel, analistas de negócios podem criar consultas de HiveQL (e, portanto, MapReduce trabalhos) diretamente no Excel, e em seguida, processar e visualizar os resultados usando o PowerPivot e outras ferramentas do Excel. Hadoop no Azure inclui outras tecnologias também, como a bibliotecas Mahout, o sistema de mineração de gráfico Pegasus e mais de aprendizado de máquina.

Análise de dados grande é importante, e então Hadoop também é importante. Fornecendo Hadoop como um serviço gerenciado no Azure, juntamente com links para ferramentas conhecidas como o Excel, Microsoft tem como objetivo fazer com que essa tecnologia acessível para um conjunto mais amplo de usuários.

Amplamente, dados de todos os tipos são importantes. É por isso Azure inclui uma variedade de opções para análise de negócios e gerenciamento de dados. Qualquer aplicativo que você está tentando criar, é provável que você encontrará algo nesta plataforma de nuvem que funcione para você.

[blobs]: ./media/cloud-storage/Data_01_Blobs.png
[SQLSvr-vm]: ./media/cloud-storage/Data_02_SQLSvrVM.png
[SQL-db]: ./media/cloud-storage/Data_03_SQLdb.png
[SQL-datasync]: ./media/cloud-storage/Data_04_SQLDataSync.png
[SQL-report]: ./media/cloud-storage/Data_05_SQLReporting.png
[SQL-tblstor]: ./media/cloud-storage/Data_06_TblStorage.png
[hadoop]: ./media/cloud-storage/Data_07_Hadoop.png
