<properties
   pageTitle="O que é um depósito de dados do SQL Azure? | Microsoft Azure"
   description="Corporativo distribuído com o banco de dados capaz de processar petabyte volumes de dados relacionais e não relacionais. Ele é data warehouse de nuvem primeiro do setor com crescer, reduzir e pausar em segundos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/27/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;kevin"/>


# <a name="what-is-azure-sql-data-warehouse"></a>O que é um depósito de dados do SQL Azure?

Depósito de dados SQL Azure é um baseado em nuvem escala-out banco de dados capaz de processar grandes volumes de dados relacionais e não relacionais. Baseado em nossa arquitetura de processamento altamente paralelo (MPP), o SQL Data Warehouse pode manipular sua carga de trabalho do enterprise.

Depósito de dados do SQL:

- Combina o banco de dados relacional do SQL Server com recursos de dimensionamento de nuvem Azure. Você pode aumentar, diminuir, pausar ou continuar a computação em segundos. Economizar custos dimensionamento CPU quando precisar e recortando volta uso durante horários de pico.
- Utiliza a plataforma Windows Azure. É fácil de implantar, perfeitamente mantidos e totalmente tolerante devido backups automáticos.
- Complementa ecossistema do SQL Server. Você pode desenvolver com ferramentas e SQL familiar Server Transact-SQL (T-SQL).

Este artigo descreve os recursos principais do SQL Data Warehouse.

## <a name="massively-parallel-processing-architecture"></a>Arquitetura de processamento paralelas em massa

SQL Data Warehouse é que um processamento altamente paralelo (MPP) distribuído com o sistema de banco de dados. Dividindo dados e capacidade de processamento em vários nós, SQL Data Warehouse podem oferecer enorme escalabilidade - muito além de qualquer sistema único.  Nos bastidores, o SQL Data Warehouse páginas seus dados formando armazenamento nada compartilhado e unidades de processamento. Os dados são armazenados na Premium localmente redundantes e vinculados para calcular nós para execução da consulta. Com essa arquitetura, o SQL Data Warehouse utiliza uma abordagem de "divida e conquiste" executando cargas e consultas complexas. Solicitações são recebidas pelo nó controle, otimizadas e então passadas para os nós de computação para fazer seu trabalho em paralelo.

Combinando arquitetura MPP e recursos de armazenamento do Azure, SQL Data Warehouse pode:

- Aumente ou diminua independentes de computação de armazenamento.
- Aumente ou diminua computação sem mover dados.
- Capacidade de computação pausar mantendo dados intactos.
- Currículo calcular capacidade no momento.

O diagrama a seguir mostra a arquitetura de forma mais detalhada.

![Arquitetura de depósito de dados do SQL][1]


**Nó de controle:** O nó de controle gerencia e otimiza consultas. É o front-end que interage com todos os aplicativos e conexões. No SQL Data Warehouse, o nó de controle é ativado por banco de dados do SQL e se conectar a ele procura e parece o mesmo. Sob a superfície, o nó de controle coordenadas todas a movimentação de dados e a computação necessários para executar consultas em paralelo em seus dados distribuídos. Quando você envia uma consulta de T-SQL depósito de dados do SQL, o nó de controle transforma em consultas separadas que são executadas em cada nó de computação em paralelo.

**Nós de computadores:** Os nós de computação servem como potência atrás de um depósito de dados do SQL. Eles são bancos de dados SQL que armazenam seus dados e processar a sua consulta. Quando você adiciona dados, o SQL Data Warehouse distribui linhas a seus nós de computação. Os nós de computação são os trabalhadores executar as consultas paralelas em seus dados. Após o processamento, eles passam os resultados de volta para o nó de controle. Para concluir a consulta, o nó de controle agrega os resultados e retorna o resultado final.

**Armazenamento:** Seus dados são armazenados no armazenamento de Blob do Azure. Quando nós de computação interagem com seus dados, eles escrever e ler diretamente para e do armazenamento de blob. Como o armazenamento do Azure expande transparente e amplamente, SQL Data Warehouse pode fazer o mesmo. Como computação e armazenamento são independentes, SQL Data Warehouse pode dimensionar automaticamente armazenamento separadamente do dimensionamento computação e vice-versa. Armazenamento de Blob do Azure também é totalmente tolerância e otimiza o processo de backup e restauração.

**Serviço de movimentação de dados:** Serviço de movimentação de dados (DMS) move dados entre os nós. DMS fornece os nós de computação acessem a dados de que precisam para junções e agregações. DMS não é um serviço Azure. É um serviço do Windows que é executado junto com o banco de dados SQL em todos os nós. Como DMS executa nos bastidores, não interagir com ele diretamente. No entanto, quando você examinar planos de consulta, você notará incluírem algumas operações DMS desde que a movimentação de dados é necessária executar cada consulta em paralelo.


## <a name="optimized-for-data-warehouse-workloads"></a>Otimizado para cargas de trabalho de depósito de dados

A abordagem MPP é facilitada por um número de data warehouse otimizações de desempenho específico, incluindo:

- Um otimizador distribuído e conjunto de estatísticas complexos em todos os dados. Usando informações sobre o tamanho de dados e de distribuição, o serviço é capaz de otimizar consultas avaliando o custo das operações de consulta distribuída específico.

- Algoritmos avançados e técnicas integrados o processo de movimentação de dados com eficiência mover dados entre os recursos de computação conforme necessário para executar a consulta. Essas operações de movimentação de dados são criadas e todas as otimizações ao serviço de movimentação de dados acontecem automaticamente.

- Índices de **columnstore** clusterizados por padrão. Usando o armazenamento baseado em coluna, o SQL Data Warehouse obtém em média 5 ganhos de compactação x sobre tradicionais de armazenamento orientado por linha e, até o x 10 ou mais ganhos de desempenho de consulta. Análise de consultas que precisam digitalizar um grande número de linhas trabalho excelente em columnstore índices.


## <a name="predictable-and-scalable-performance"></a>Desempenho previsível e escalável

SQL Data Warehouse separa armazenamento e computação, que permite que cada dimensionar de maneira independente. SQL Data Warehouse pode rapidamente e simplesmente dimensionar para adicionar recursos de computação adicionais no momento. Para completar esta é o uso de armazenamento de Blob do Azure. BLOBs fornecem não apenas armazenamento estável e replicado, mas também a infraestrutura para fácil expansão a baixo custo. Usando essa combinação de armazenamento de escala nuvem e computação Azure, SQL Data Warehouse permite que você pagar por armazenamento e desempenho de consulta quando precisar. Alterar a quantidade de computação é tão simple quanto mover um controle deslizante no portal do Azure para a esquerda ou direita, ou ela também pode ser agendada usando T-SQL e PowerShell.

Juntamente com a capacidade de controlar a quantidade de computação independentemente dos armazenamento totalmente, SQL Data Warehouse permite pausar totalmente seu data warehouse, o que significa que você não pagar computação quando não precisar. Mantendo seu armazenamento no lugar, todos os computadores é lançado para o pool principal do Azure, economizando. Quando necessário, simplesmente retomar a computação e ter seus dados e calcular disponíveis para sua carga de trabalho.

## <a name="data-warehouse-units"></a>Unidades de depósito de dados

Alocação de recursos para o SQL Data Warehouse é medida em unidades de dados de depósito (DWUs). DWUs são uma medida de recursos subjacentes como CPU, memória, IOPS, que estão alocados para seu Data Warehouse SQL. Aumentar o número de DWUs aumenta o desempenho e recursos. Especificamente, DWUs ajudar a garantir que:

- Você será capaz de dimensionar seu data warehouse facilmente, sem se preocupar o software ou o hardware subjacente.

- Você pode prever aprimoramento do desempenho para um nível DWU antes de alterar o tamanho de seu data warehouse.

- O hardware e software da sua instância subjacente podem alterar ou mova sem afetar o desempenho da carga de trabalho.

- Microsoft pode fazer ajustes a arquitetura subjacente do serviço sem afetar o desempenho de sua carga de trabalho.

- Microsoft rapidamente pode melhorar o desempenho em SQL Data Warehouse, de maneira que seja scalable e uniformemente efeitos do sistema.

Unidades de dados de depósito fornecem uma medida de três avaliações precisas que são altamente correlacionadas com data warehouse desempenho de carga de trabalho. O objetivo é que as seguintes métricas de carga de trabalho chave acompanhará linear com os DWUs que você escolheu para seu data warehouse.

**Digitalização/agregação:** Esta métrica de carga de trabalho leva um padrão de data warehouse consulta que examina um grande número de linhas e, em seguida, executa uma agregação complexa. Esta é uma operação de intenso s e CPU.

**Carga:** Esta métrica avalia a capacidade de incluir dados no serviço. Cargas são preenchidas com PolyBase carregando um representante de conjunto de dados do armazenamento de Blob do Azure. Esta métrica destina-se à rede de carga e aspectos de CPU do serviço.

**Criar tabela como selecionar (CTAS):** CTAS avalia a capacidade de copiar uma tabela. Isso envolve a leitura de dados do armazenamento, distribuí-lo em nós do aparelho e escrevendo-lo novamente ao armazenamento. É uma operação de intenso CPU, IO e rede.

## <a name="pause-and-scale-on-demand"></a>Pausar e escala sob demanda

Quando você precisar resultados mais rápidos, aumentar sua DWUs e pagar para obter melhor desempenho. Quando precisar menos poder computação, diminuir sua DWUs e pagar somente para o que é necessário. Você pode pensar sobre como alterar sua DWUs nesses cenários:

- Quando você não precisa executar consultas, talvez na noite ou fins de semana, desativar, suas consultas. Pause seus recursos de computação para evitar pagando por DWUs quando não precisar.

- Quando o seu sistema tiver baixa demanda, considere a redução DWU para um tamanho pequeno. Você ainda pode acessar os dados, mas uma economia significativa.

- Ao executar uma operação de carregamento ou transformação de dados intenso, talvez você queira dimensionar para cima, para que seus dados estão disponíveis mais rapidamente.

Para entender o que é o valor DWU ideal, tente dimensionamento para cima e para baixo e executando algumas consultas após carregar seus dados. Como o dimensionamento é rápido, você pode tentar um número de níveis diferentes de desempenho em uma hora ou menos.  Lembre-se que SQL Data Warehouse foi projetado para processar grandes quantidades de dados e ver seus recursos true para dimensionamento, especialmente nas escalas maiores que oferecemos, você desejará utilizar um grande conjunto de dados que se aproxima ou excede 1 TB.


## <a name="built-on-sql-server"></a>Criado no SQL Server

SQL Data Warehouse baseia-se no mecanismo de banco de dados relacionais do SQL Server e inclui muitos dos recursos esperado de um depósito de dados corporativos. Se você já sabe T-SQL, é fácil transferir seu conhecimento depósito de dados do SQL. Se você estiver avançada ou começando a, os exemplos entre a documentação ajudará a começar. Em geral, você pode pensar da maneira que podemos já construída os elementos de linguagem do SQL Data Warehouse da seguinte maneira:

- SQL Data Warehouse usa sintaxe de T-SQL para muitas operações. Ele também dá suporte a uma ampla variedade de construções SQL tradicionais, como procedimentos armazenados, funções definidas pelo usuário, partição de tabela, índices e agrupamentos.

- SQL Data Warehouse também contém um número de recursos mais recentes do SQL Server, incluindo: agrupada **columnstore** índices, integração de PolyBase e dados de auditoria (completo com avaliação de ameaças).

- Determinados elementos de linguagem T-SQL que são menos comum para data warehouse cargas de trabalho ou mais recente para o SQL Server, podem não estar disponíveis no momento. Para obter mais informações, consulte a [documentação de migração][].

Com o comum Transact-SQL e recurso entre o SQL Server, SQL Data Warehouse, banco de dados SQL e sistema de plataforma de análise, você pode desenvolver uma solução que atenda às suas necessidades de dados. Você pode decidir onde manter seus dados, com base em requisitos de escala, segurança e desempenho e transferi-dados conforme necessário entre diferentes sistemas.

## <a name="data-protection"></a>Proteção de dados

SQL Data Warehouse armazena todos os dados em um armazenamento localmente redundante Azure Premium. Várias cópias síncronas dos dados são mantidas no data center local para garantir a proteção de dados transparente no caso de falhas localizadas. Além disso, o SQL Data Warehouse backup automaticamente seus bancos de dados (não pausados) ativos em intervalos regulares usando instantâneos de armazenamento do Azure. Para saber mais sobre como fazer backup e restauração funciona, consulte [Visão geral de Backup e restauração][].

## <a name="integrated-with-microsoft-tools"></a>Integrada com ferramentas Microsoft

SQL Data Warehouse também se integra muitas das ferramentas que os usuários podem estar familiarizados com do SQL Server. Eles incluem:

**Ferramentas tradicional do SQL Server:** SQL Data Warehouse é totalmente integrado com SQL Server Analysis Services, Integration Services e Reporting Services.

**Ferramentas baseadas em nuvem:** SQL Data Warehouse pode ser usado junto com um número de novas ferramentas no Azure, incluindo Data Factory, a análise de fluxo, aprendizado de máquina e Power BI. Para obter uma lista completa, consulte [Visão geral de ferramentas integradas][].

**Ferramentas de terceiros:** Um grande número de provedores de ferramenta de terceiros tê certified integração de suas ferramentas com SQL Data Warehouse. Para obter uma lista completa, consulte [parceiros de solução de depósito de dados do SQL][].

## <a name="hybrid-data-sources-scenarios"></a>Cenários de fontes de dados híbridos

Usando o SQL Data Warehouse com PolyBase permite que os usuários sem precedentes para mover dados entre seu ecossistema, desbloqueando a capacidade de configuração híbrida avançado cenários com não-relacionais e fontes de dados locais.

Polybase permite que você aproveite os dados de diferentes fontes usando comandos familiares de T-SQL. Polybase permite que você para consultar dados não-relacionais contidos em armazenamento de Blob do Azure como se fossem uma tabela normal. Use Polybase para dados não-relacionais de consulta, ou para importar dados não-relacionais para SQL Data Warehouse.

- PolyBase usa tabelas externas para acessar dados não-relacionais. As definições de tabela são armazenadas em SQL Data Warehouse e você pode acessá-los usando o SQL e ferramentas como você faria acessar dados relacionais normais.

- Polybase é independente na sua integração. Ele expõe os mesmos recursos e funcionalidades para todas as fontes que ele suporta. Os dados lidos pelo Polybase podem ser em uma variedade de formatos, incluindo arquivos delimitados ou ORC.

- PolyBase pode ser usado para acessar o armazenamento de blob que também está sendo usado como armazenamento para um cluster de HDInsight. Isso lhe dá acesso aos mesmos dados com ferramentas e relacionais não.

## <a name="sla"></a>SLA

SQL Data Warehouse oferece um produto nível contrato de serviço (SLA) como parte do Microsoft Online Services SLA. Para obter mais informações, visite [SLA para depósito de dados do SQL][]. Para obter informações de SLA sobre todos os outros produtos, você pode visitar o Azure de [Contratos de nível de serviço] de página ou baixá-las na página de [Licenciamento por Volume][] . 

## <a name="next-steps"></a>Próximas etapas

Agora que você conhece um pouco SQL Data Warehouse, saiba como rapidamente [criar um depósito de dados do SQL][] e [carregar dados de amostra][]. Se você estiver começando a usar o Azure, você descobrirá o [Azure glossário][] úteis à medida que encontrar nova terminologia. Ou, dê uma olhada alguns desses outros recursos de depósito de dados SQL.  

- [Histórias de sucesso do cliente]
- [Blogs]
- [Solicitações de recursos]
- [Vídeos]
- [Blogs da equipe comunicado de cliente]
- [Criar tíquete]
- [Fórum do MSDN]
- [Fórum de estouro de pilha]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Criar tíquete]: ./sql-data-warehouse-get-started-create-support-ticket.md
[carregar dados de amostra]: ./sql-data-warehouse-load-sample-databases.md
[criar um depósito de dados do SQL]: ./sql-data-warehouse-get-started-provision.md
[Documentação de migração]: ./sql-data-warehouse-overview-migrate.md
[Parceiros de solução de depósito de dados do SQL]: ./sql-data-warehouse-partner-business-intelligence.md
[Visão geral de ferramentas integradas]: ./sql-data-warehouse-overview-integrate.md
[Visão geral de backup e restauração]: ./sql-data-warehouse-restore-database-overview.md
[Glossário do Azure]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Histórias de sucesso do cliente]: https://customers.microsoft.com/search?sq=&ff=story_products_services%26%3EAzure%2FAzure%2FAzure%20SQL%20Data%20Warehouse%26%26story_product_families%26%3EAzure%2FAzure%26%26story_product_categories%26%3EAzure&p=0
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs da equipe comunicado de cliente]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Solicitações de recursos]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Fórum de estouro de pilha]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA para depósito de dados do SQL]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Licenciamento por volume]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Contratos de nível de serviço]: https://azure.microsoft.com/en-us/support/legal/sla/
