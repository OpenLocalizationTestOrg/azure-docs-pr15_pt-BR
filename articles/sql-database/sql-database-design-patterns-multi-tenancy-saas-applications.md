<properties
   pageTitle="Projetar padrões para vários locatários aplicativos SaaS e Azure SQL Database | Microsoft Azure"
   description="Este artigo discute os requisitos e padrões de arquitetura de dados comuns vários locatários do banco de aplicativos em execução em um ambiente de nuvem deve ser considerado e as compensações vários associadas a esses padrões. Ele também explica como Azure SQL Database, com seus pools Elástico e ferramentas elástica, ajudar atender a esses requisitos de maneira inflexível."
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-design"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="design-patterns-for-multitenant-saas-applications-and-azure-sql-database"></a>Padrões para vários locatários aplicativos SaaS e Azure SQL Database de design

Neste artigo, você pode aprender sobre os requisitos e dados comuns padrões de arquitetura de software vários locatários como um aplicativos de banco de dados de serviço (SaaS) que são executados em um ambiente de nuvem. Ele também explica os fatores que você precisa considerar e as compensações de padrões de design diferente. Elástico pools e elástica ferramentas no Azure SQL Database podem ajudá-lo a atender a necessidades específicas sem comprometer outros objetivos.

Às vezes, desenvolvedores fazer escolhas que funcionam em relação a seus interesses a longo prazo quando criam modelos de aluguel para as camadas de dados de aplicativos vários locatários. Inicialmente, pelo menos, um desenvolvedor pode perceber facilidade de desenvolvimento e inferiores custos de provedor de serviços de nuvem como mais importante do que locatário isolamento ou a escalabilidade de um aplicativo. Essa opção pode levar a questões de satisfação do cliente e uma correção de curso dispendiosa mais tarde.

Um aplicativo de vários locatários é um aplicativo hospedado em um ambiente de nuvem e que fornece o mesmo conjunto de serviços para centenas ou milhares de locatários quem não compartilhar ou consulte uns dos outros dados. Um exemplo é um aplicativo de SaaS que fornece serviços para locatários em um ambiente hospedado na nuvem.

## <a name="multitenant-applications"></a>Aplicativos de vários locatários

Em aplicativos de vários locatários, dados e a carga de trabalho podem ser particionados facilmente. Você pode partição dados e a carga de trabalho, por exemplo, em limites de locatário, porque a maioria das solicitações ocorrem dentro dos limites de um locatário. Essa propriedade é inerente os dados e a carga de trabalho, e ele melhora os padrões de aplicativo abordados neste artigo.

Desenvolvedores usam esse tipo de aplicativo em todo o espectro de aplicativos na nuvem, incluindo:

- Aplicativos de banco de dados de parceiros que estão sendo fizeram a transição para a nuvem como aplicativos SaaS
- Aplicativos de SaaS criados para a nuvem desde o início
- Aplicativos diretos, voltados para o cliente
- Aplicativos de corporativos voltado para o funcionário

SaaS projetados para a nuvem e aqueles com raízes como aplicativos de banco de dados do parceiro geralmente são vários locatários aplicativos. Esses aplicativos SaaS entregar um aplicativo de software especializado como um serviço para seus locatários. Locatários podem acessar o serviço de aplicativo e tiver a propriedade completa de dados associados armazenados como parte do aplicativo. Mas, para usufruir dos benefícios de SaaS, locatários devem capitular algum controle sobre seus próprios dados. Eles confiam o provedor de serviços SaaS para manter seus dados seguros e isolada de dados dos outros locatários. Exemplos desse tipo de aplicativo de SaaS vários locatários são MYOB, SnelStart e Salesforce.com. Cada um desses aplicativos pode ser particionada ao longo de limites de locatário e suporte a padrões discutimos neste artigo de design do aplicativo.

Aplicativos que fornecem um serviço direto para clientes ou funcionários em uma organização (costumam ser chamadas de usuários, ao invés de locatários) são outra categoria na variedade de aplicativo vários locatários. Os clientes se inscrever no serviço e não possui os dados que o provedor de serviços coleta e armazena. Provedores de serviços têm menos rigorosos requisitos para manter os dados de seus clientes isolados uns dos outros além dos regulamentos de privacidade governamentais. Exemplos desse tipo de aplicativo de vários locatários voltados para o cliente são provedores de conteúdo de mídia como Netflix Spotify e Xbox LIVE. Outros exemplos de aplicativos facilmente particionáveis são voltados para o cliente, aplicativos de escala de Internet ou aplicativos de Internet das coisas (IoT) em que cada pode cliente ou dispositivo servem como uma partição. Limites de partição podem separar usuários e dispositivos.

Nem todos os partição de aplicativos facilmente ao longo de uma única propriedade como locatário, cliente, usuário ou dispositivo. Um recurso da empresa complexa planejando aplicativo de (ERP), por exemplo, tem produtos, pedidos e clientes. Normalmente, ele tem um esquema complexo com milhares de tabelas altamente interligadas.

Nenhuma estratégia de partição única pode aplicar a todas as tabelas e trabalhar em toda a carga de trabalho de um aplicativo. Este artigo se concentra nos aplicativos vários locatários que têm cargas de trabalho e dados facilmente particionáveis.

## <a name="multitenant-application-design-trade-offs"></a>Aplicativo vários locatários design concessões

O padrão de design que um desenvolvedor de aplicativos vários locatários escolhe normalmente é baseado em uma consideração dos seguintes fatores:

-   **Isolamento de locatário**. O desenvolvedor precisa garantir que nenhuma locatário tenha indesejado acesso aos dados dos outros locatários. O requisito de isolamento estende a outras propriedades, como fornecendo proteção de ruído vizinhos, sendo possível restaurar os dados de um locatário e implementar as personalizações específicas do locatário.
-   **Custo de recurso de nuvem**. Um aplicativo de SaaS precisa ser competitiva de custo. Um desenvolvedor de aplicativos vários locatários pode optar por otimizar para reduzir o custo do uso de recursos de nuvem, como armazenamento e calcular os custos.
-   **Facilidade de DevOps**. Um desenvolvedor de aplicativos vários locatários precisa incorporar proteção de isolamento, manter e monitorar a integridade dos seus aplicativos e o esquema de banco de dados e solucionar problemas de locatário. Complexidade no desenvolvimento de aplicativos e operação traduz diretamente para o aumento de custo e os desafios com satisfação do locatário.
-   **Escalabilidade**. A capacidade de adicionar forma incremental mais locatários e capacidade para locatários que precisam de ele é fundamental para uma operação de SaaS bem-sucedida.

Cada um desses fatores tem concessões em comparação com outra. Nuvem menor custo oferecendo podem não oferecer a experiência de desenvolvimento mais conveniente. É importante para um desenvolvedor fazer escolhas informadas sobre essas opções e suas concessões durante o processo de design do aplicativo.

Um padrão de desenvolvimento populares é vários locatários de pacote em um ou alguns bancos de dados. Os benefícios dessa abordagem são um custo menor, pois você pagar alguns bancos de dados e a simplicidade relativa de trabalhar com um número limitado de bancos de dados. Mas ao longo do tempo, um desenvolvedor de aplicativo vários locatários SaaS obterá que essa opção tem desvantagens substanciais em isolamento de locatário e escalabilidade. Se isolamento de locatário ficar importante, esforço adicional é necessário para proteger os dados de locatário em armazenamento compartilhado contra acesso não autorizado ou vizinhos ruídos. Este esforço adicional significativamente pode impulsionar esforços de desenvolvimento e os custos de manutenção de isolamento. Da mesma forma, se for necessário adicionar locatários, esse padrão de design normalmente requer experiência para redistribuir locatário dados em bancos de dados adequadamente dimensionar a camada de dados de um aplicativo.  

Isolamento de locatário geralmente é um requisito fundamental em aplicativos de vários locatários SaaS que atendem às empresas e organizações. Um desenvolvedor pode ser tentado vantagens simplicidade e custo sobre isolamento de locatário e escalabilidade. Essa desvantagem pode provar complexo e caro conforme o serviço cresce e requisitos de isolamento de locatário se tornam mais importantes e gerenciada na camada de aplicativo. No entanto, em vários locatários aplicativos que fornecem um serviço voltados para o consumidor direto aos clientes, isolamento de locatário pode ser uma prioridade mais baixa que otimização de custo de recurso de nuvem.

## <a name="multitenant-data-models"></a>Modelos de dados de vários locatários

Práticas de design comuns para inserir dados de locatário Siga três modelos distintos, mostrados na Figura 1.


  ![Modelos de dados de aplicativo vários locatários](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png)
 Figura 1: práticas comuns de design para modelos de dados de vários locatários

-   **Banco de dados por locatário**. Cada locatário tem seu próprio banco de dados. Todos os dados específicos do locatário é restrito ao banco de dados do locatário e isolada de outros locatários e seus dados.
-   **Sharded de banco de dados compartilhado**. Vários locatários compartilham um dos vários bancos de dados. Um conjunto distinto de locatários é atribuído a cada banco de dados usando uma estratégia de partição como hash, intervalo ou lista partição. Essa estratégia de distribuição de dados geralmente é conhecida como fragmentação.
-   **Banco de dados compartilhado - single**. Um banco de dados único, às vezes, grande, contém dados para todos os locatários, que são ambiguidade removidos em uma coluna de identificação de locatário.

> [AZURE.NOTE] Um desenvolvedor de aplicativos pode optar por colocar locatários diferentes em esquemas de banco de dados diferente e use o nome do esquema a ambiguidade os locatários diferentes. Não recomendamos essa abordagem porque ele geralmente requer o uso de SQL dinâmico, e não poderá ser eficaz em cache de plano. O restante deste artigo, vamos nos concentrar na abordagem tabela compartilhada para essa categoria de aplicativo vários locatários.

## <a name="popular-multitenant-data-models"></a>Modelos de dados de vários locatários populares

É importante avaliar os diferentes tipos de modelos de dados de vários locatários em termos os aplicativo design concessões que já identificamos. Esses fatores ajudam a definir os três modelos de dados de vários locatários mais comuns descritos anteriormente e seu uso de banco de dados conforme mostrado na Figura 2.

-   **Isolamento**. O grau de isolamento entre locatários pode ser uma medida do quanto isolamento de locatário atinge um modelo de dados.
-   **Custo de recurso de nuvem**. A quantidade de compartilhamento de recursos entre locatários pode otimizar o custo de recurso de nuvem. Um recurso pode ser definido como o custo de computação e armazenamento.
-   **Custo de DevOps**. Facilidade de desenvolvimento de aplicativos, implantação e gerenciamento reduz o custo geral de operação de SaaS.  

Na Figura 2, o eixo Y mostra o nível de isolamento de locatário. O eixo X mostra o nível de compartilhamento de recursos. Cinza, seta diagonal no meio indica a direção de custos DevOps, tentar aumentar ou diminuir.

![Padrões de design de aplicativo de vários locatários populares](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png) Figura 2: modelos de dados de vários locatários populares

Quadrante inferior direito da Figura 2 mostra um padrão de aplicativo que usa um potencialmente grandes, único banco de dados compartilhado e a tabela compartilhada (ou esquema separado) abordagem. É bom recurso Compartilhamento porque todos os locatários usam os mesmos recursos de banco de dados (CPU, memória, entrada/saída) em um banco de dados único. Mas locatário isolamento está limitado. Talvez seja necessário executar etapas adicionais para proteger locatários uns dos outros na camada de aplicativo. Estas etapas adicionais podem aumentar significativamente o custo de DevOps de desenvolvimento e gerenciamento do aplicativo. Escalabilidade é limitada pela escala do hardware que hospeda o banco de dados.

Quadrante inferior esquerdo na Figura 2 ilustra vários locatários sharded pelos vários bancos de dados (geralmente, hardware diferente dimensionar unidades). Cada banco de dados hospeda um subconjunto de locatários, que corrige o problema de escalabilidade de outros padrões. Se mais capacidade for necessária para locatários mais, você pode colocar facilmente os locatários no novos bancos de dados alocados para novas unidades de escala de hardware. No entanto, a quantidade de compartilhamento de recursos é reduzida. Somente locatários colocado nas mesmas unidades de escala compartilhar recursos. Essa abordagem oferece pouco aperfeiçoamento para isolamento de locatários porque muitos locatários ainda são proximidade da localização sem automaticamente protegido contra ações uns dos outros. Complexidade de aplicativo permanece alta.

Quadrante superior esquerdo na Figura 2 é a terceira abordagem. Ele coloca dados do cada locatário em seu próprio banco de dados. Essa abordagem tem propriedades de isolamento locatário boa mas limites de compartilhamento de recursos quando cada banco de dados tem seus próprio recursos dedicados. Essa abordagem é bom se todos os locatários tem cargas de trabalho previsíveis. Se cargas de trabalho de locatário se tornam menos previsíveis, o provedor não pode otimizar o compartilhamento de recursos. Imprevisibilidade é comum para aplicativos SaaS. O provedor deve em excesso provisionar para cumprir exigências ou recursos inferiores. Qualquer uma das ações resulta em custos mais altos ou inferior satisfação do locatário. Maior grau de recurso compartilhamento entre locatários se torna desejável fazer a solução mais econômico. Aumentar o número de bancos de dados também aumenta DevOps custo para implantar e manter o aplicativo. Apesar dessas preocupações, este método fornece o isolamento melhor e mais fácil para locatários.

Esses fatores também influenciam o padrão de design que escolhe um cliente:

-   **A propriedade de dados de Inquilino**. Um aplicativo no qual locatários reter a propriedade de seus próprios dados prefere o padrão de um banco de dados único por locatário.
-   **Escala**. Um aplicativo que os destinos centenas de milhares ou milhões de locatários prefere abordagens como fragmentação de compartilhamento de banco de dados. Requisitos de isolamento ainda podem representar os desafios.
-   **Valor e modelo de negócios**. Se um aplicativo por-locatário receita se pequenas (menos de um dólar), requisitos de isolamento se tornam menos essenciais e um banco de dados compartilhado faz sentido. Se a receita por locatário é alguns dólares ou mais, um modelo de banco de dados por locatário é mais viável. Isso pode ajudar a reduzir os custos de desenvolvimento.

Considerando os design concessões mostrados na Figura 2, um modelo de vários locatários ideal precisa incorporar as propriedades de isolamento de locatário bom recurso ideal compartilhamento entre locatários. Esse modelo se encaixa na categoria descrita no quadrante superior direito da Figura 2.

## <a name="multitenancy-support-in-azure-sql-database"></a>Suporte multitenancy no banco de dados do SQL Azure

Banco de dados do SQL Azure oferece suporte a todos os padrões de aplicativo vários locatários descritos na Figura 2. Com pools Elástico, ele também dá suporte a um padrão de aplicativo que combina bom recurso de compartilhamento e os benefícios de isolamento do banco de dados por locatário abordagem (consulte quadrante superior direito na Figura 3). Ferramentas de banco de dados elástica e recursos no banco de dados do SQL ajudam a reduzir o custo para desenvolver e operar um aplicativo que tem muitos bancos de dados (mostrados na área sombreada na Figura 3). Essas ferramentas podem ajudá-lo a criar e gerenciar aplicativos que usam um dos padrões de vários bancos de dados.

![Padrões em Azure SQL Database](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png) Figura 3: padrões de aplicativo vários locatários no banco de dados do SQL Azure

## <a name="database-per-tenant-model-with-elastic-pools-and-tools"></a>Modelo de banco de dados por locatário com ferramentas e pools Elástico

Elástico pools no banco de dados SQL combinam locatário isolamento com recurso compartilhamento entre bancos de dados de locatário para melhor suporte a abordagem de banco de dados por locatário. Banco de dados SQL é uma solução de nível de dados para provedores de SaaS que criam aplicativos vários locatários. A carga do recurso compartilhamento entre locatários desloca da camada de aplicativos para a camada de serviço do banco de dados. A complexidade de gerenciamento e consultando em escala em bancos de dados é simplificada com trabalhos Elástico, consulta elástica, transações elástica e a biblioteca de cliente elástica banco de dados.

| Requisitos do aplicativo | Recursos de banco de dados SQL |
| ------------------------ | ------------------------- |
| Isolamento de locatário e compartilhamento de recursos | [Pools Elástico](sql-database-elastic-pool.md): alocar um pool de recursos de banco de dados SQL e compartilhar recursos entre vários bancos de dados. Além disso, os bancos de dados individuais podem desenhar tantos recursos do pool conforme necessário para acomodar picos de demanda de capacidade devido às alterações nas cargas de trabalho de locatário. O próprio pool Elástico pode ser dimensionado para cima ou para baixo conforme necessário. Pools Elástico também fornecem facilidade de gerenciamento e monitoramento e solução de problemas no nível do pool. |
| Facilidade de DevOps em bancos de dados | [Pools Elástico](sql-database-elastic-pool.md): conforme mencionado anteriormente.|
||[Consulta elástica](sql-database-elastic-query-horizontal-partitioning.md): consulta em bancos de dados para relatórios ou entre-locatário análise.|
||[Trabalhos Elástico](sql-database-elastic-jobs-overview.md): pacote e implantar confiavelmente operações de manutenção de banco de dados ou as alterações de esquema de banco de dados em vários bancos de dados.|
||[Transações elástica](sql-database-elastic-transactions-overview.md): processo altera a vários bancos de dados de uma maneira atômica e isolada. Transações elástica são necessários quando aplicativos precisam garantias "tudo ou nada" ao longo de várias operações de banco de dados. |
||[Biblioteca de cliente do banco de dados elástica](sql-database-elastic-database-client-library.md): gerenciar distribuições de dados e mapear locatários aos bancos de dados. |

## <a name="shared-models"></a>Modelos compartilhados

Conforme descrito anteriormente, para a maioria dos provedores de SaaS, uma abordagem de modelo compartilhado pode representar problemas com problemas de isolamento de locatário e complexidades com o desenvolvimento de aplicativos e a manutenção. No entanto, para vários locatários aplicativos que fornecem um serviço diretamente aos clientes, requisitos de isolamento de locatário podem não estar como uma prioridade alta como minimizar os custos. Eles poderão locatários de pacote em um ou mais bancos de dados em uma alta densidade para reduzir custos. Modelos de banco de dados compartilhados usando um banco de dados único ou vários bancos de dados sharded podem resultar em eficiência adicional no custo de recurso de compartilhamento e geral. Banco de dados do SQL Azure fornece alguns recursos que ajudam os clientes construir isolamento para segurança aprimorada e gerenciamento em escala na camada de dados.

| Requisitos do aplicativo | Recursos de banco de dados SQL |
| ------------------------ | ------------------------- |
| Recursos de isolamento de segurança | [Segurança em nível de linha](https://msdn.microsoft.com/library/dn765131.aspx) |
|| [Esquema de banco de dados](https://msdn.microsoft.com/library/dd207005.aspx) |
| Facilidade de DevOps em bancos de dados | [Consulta elástica](sql-database-elastic-query-horizontal-partitioning.md) |
|| [Trabalhos Elástico](sql-database-elastic-jobs-overview.md) |
|| [Transações elástica](sql-database-elastic-transactions-overview.md) |
|| [Biblioteca de cliente do banco de dados elástica](sql-database-elastic-database-client-library.md) |
|| [Mesclar e dividir banco de dados elástica](sql-database-elastic-scale-overview-split-and-merge.md) |

## <a name="summary"></a>Resumo

Requisitos de isolamento de locatário são importantes para a maioria dos aplicativos de vários locatários SaaS. A melhor opção para fornecer isolamento muito olhar em direção a abordagem de banco de dados por locatário. As duas abordagens exigem investimentos em camadas de aplicativo complexo que requerem que a equipe de desenvolvimento competente para fornecer isolamento, o que aumenta significativamente o custo e o risco. Se os requisitos de isolamento não forem considerados no início o desenvolvimento do serviço, aperfeiçoamento-los pode ser ainda mais caro nos modelos de duas primeiras. As principais desvantagens associadas com o modelo de banco de dados por locatário estão relacionadas a custos de recursos de nuvem maior devido a redução de compartilhamento e manutenção e gerenciar muitos bancos de dados. Os desenvolvedores de aplicativos SaaS geralmente dificuldades quando eles fizerem essas compensações.

Embora concessões podem ser principais obstáculos com a maioria dos provedores de serviço de banco de dados de nuvem, o Azure SQL Database elimina os obstáculos com seus recursos de banco de dados elástica e elástica pool. Desenvolvedores de SaaS podem combinar as características de isolamento de um modelo de banco de dados por locatário e otimizar o compartilhamento de recursos e melhorias de gerenciamento de vários bancos de dados usando pools Elástico e ferramentas associadas.

Provedores de aplicativo vários locatários que têm requisitos de isolamento sem locatário e quem pode empacotar locatários em um banco de dados em uma alta densidade podem achar que dados compartilhados modela resultado na eficiência adicional no compartilhamento de recursos e reduzem o custo total. Ferramentas de banco de dados elástica de banco de dados do SQL Azure, bibliotecas de fragmentação e recursos de segurança ajudam provedores SaaS criar e gerenciar aplicativos vários locatários.

## <a name="next-steps"></a>Próximas etapas

[Introdução às ferramentas de banco de dados elástica](sql-database-elastic-scale-get-started.md) com um aplicativo de amostra que demonstra a biblioteca de cliente.

Crie um [Painel de controle personalizado para SaaS elástica pool](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) com um aplicativo de exemplo que usa pools Elástico para uma solução de banco de dados econômica e flexível.

Use as ferramentas de banco de dados do Azure SQL para [Migrar bancos de dados existentes de dimensionar](sql-database-elastic-convert-to-use-elastic-tools.md).

Exiba nosso tutorial sobre como [criar um pool de Elástico](sql-database-elastic-pool-create-portal.md).  

Saiba como [monitorar e gerenciar um pool Elástico](sql-database-elastic-pool-manage-portal.md).

## <a name="additional-resources"></a>Recursos adicionais

- [O que é um pool de Elástico Azure?](sql-database-elastic-pool.md)
- [Dimensionamento com o banco de dados do SQL Azure](sql-database-elastic-scale-introduction.md)
- [Vários locatários aplicativos com ferramentas de banco de dados elástica e segurança em nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md)
- [Autenticação nos aplicativos vários locatários usando o Active Directory do Azure e OpenID conectar](../guidance/guidance-multitenant-identity-authenticate.md)
- [Aplicativo de pesquisas Tailspin](../guidance/guidance-multitenant-identity-tailspin.md)
- [Solução rápida começa](sql-database-solution-quick-starts.md)

## <a name="questions-and-feature-requests"></a>Perguntas e solicitações de recursos

Para perguntas, localize-no [Fórum de banco de dados SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Adicione uma solicitação de recurso no [Fórum de comentários do banco de dados SQL](https://feedback.azure.com/forums/217321-sql-database/).
