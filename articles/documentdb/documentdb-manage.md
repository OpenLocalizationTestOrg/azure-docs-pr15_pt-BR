<properties
    pageTitle="DocumentDB armazenamento e desempenho | Microsoft Azure" 
    description="Saiba mais sobre o armazenamento de dados e armazenamento de documentos DocumentDB e como você pode dimensionar DocumentDB para atender às necessidades de capacidade de seu aplicativo." 
    keywords="armazenamento de documentos"
    services="documentdb" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="cgronlun" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/18/2016" 
    ms.author="syamk"/>

# <a name="learn-about-storage-and-predictable-performance-provisioning-in-documentdb"></a>Saiba mais sobre o armazenamento e o desempenho previsível provisionamento em DocumentDB
DocumentDB Azure é um totalmente gerenciado scalable orientados a documentos NoSQL banco de dados serviço para documentos JSON. Com DocumentDB, você não precisa alugar máquinas virtuais, implantar o software ou monitorar bancos de dados. DocumentDB é operado e continuamente monitorada por engenheiros da Microsoft para fornecer disponibilidade de classe mundial, desempenho e proteção de dados.  

Você pode começar a usar DocumentDB criando [uma conta de banco de dados](documentdb-create-account.md) e um [banco de dados de DocumentDB](documentdb-create-database.md) por meio do [portal do Azure](https://portal.azure.com/). Bancos de dados de DocumentDB são oferecidos em unidades de unidade de estado sólido (SSD) feitas de armazenamento e a produtividade. Estas unidades de armazenamento são provisionadas [criando coleções de banco de dados](documentdb-create-collection.md) dentro de sua conta de banco de dados, cada conjunto com produtividade reservada que pode ser dimensionada para cima ou para baixo a qualquer momento para atender às exigências de seu aplicativo. 

Se seu aplicativo exceder sua produtividade reservada para um ou vários conjuntos, solicitações são limitadas em uma base por conjunto. Isso significa que algumas solicitações de aplicativo podem bem-sucedida enquanto outras pessoas podem ser limitadas.

Este artigo fornece uma visão geral dos recursos e métricas disponíveis para gerenciar capacidade e plano de armazenamento de dados. 

## <a name="database-account"></a>Conta de banco de dados
Como assinante Azure, você pode provisionar uma ou mais contas de banco de dados de DocumentDB para gerenciar seus recursos de banco de dados. Cada assinatura está associada uma única assinatura Azure. 

Contas de DocumentDB podem ser criadas por meio do [portal do Azure](documentdb-create-account.md), ou usando [um modelo ARM ou CLI do Azure](documentdb-automation-resource-manager-cli.md).

## <a name="databases"></a>Bancos de dados
Um único banco de dados de DocumentDB pode conter praticamente um volume ilimitado de armazenamento de documentos agrupado em conjuntos. Coleções fornecem isolamento de desempenho - cada conjunto pode ser provisionado com taxa de transferência que não é compartilhada com outros conjuntos do mesmo banco de dados ou a conta. Um banco de dados de DocumentDB é Elástico em tamanho, desde GB para armazenamento de documentos de TB da SSD feito e produtividade provisionada. Ao contrário de um banco de dados RDBMS tradicional, um banco de dados DocumentDB não é delimitado para um único computador e pode abranger várias máquinas ou clusters.  

Com DocumentDB, conforme necessário dimensionar seus aplicativos, você pode criar mais conjuntos, bancos de dados ou ambos. Bancos de dados podem ser criados por meio do [portal do Azure](documentdb-create-database.md) ou qualquer um dos [SDKs DocumentDB](documentdb-dotnet-samples.md).   

## <a name="database-collections"></a>Coleções de banco de dados
Cada banco de dados de DocumentDB pode conter um ou mais conjuntos. Coleções atuam como partições de dados altamente disponível para processamento e armazenamento de documento. Cada conjunto pode armazenar documentos com esquema heterogêneo. Recursos de consulta e indexação automática do DocumentDB permitem que você facilmente filtrar e recuperar documentos. Um conjunto fornece o escopo para execução de armazenamento e consulta do documento. Um conjunto também é um domínio de transação para todos os documentos contidos nela. Coleções são alocadas produtividade com base no valor definido no portal do Azure ou via SDK do. 

Coleções são particionadas automaticamente em um ou mais servidores físicos por DocumentDB. Quando você cria uma coleção, você pode especificar a produtividade provisionada em termos de unidades de solicitação por segundo e uma propriedade de chave de partição. O valor desta propriedade é usado pelo DocumentDB para distribuir documentos entre partições e rotear solicitações como consultas. O valor de chave de partição também funciona como o limite de transação para procedimentos armazenados e disparadores. Cada coleção tem uma quantidade reservada de produtividade específica para esse conjunto, que não é compartilhado com outros conjuntos na mesma conta. Portanto, você pode dimensionar seu aplicativo tanto em termos de produtividade e de armazenamento. 

Coleções podem ser criadas por meio do [portal do Azure](documentdb-create-collection.md) ou qualquer um dos [SDKs DocumentDB](documentdb-sdk-dotnet.md).   
 
## <a name="request-units-and-database-operations"></a>Solicitar unidades e operações de banco de dados

Quando você cria uma coleção, você reservar produtividade em termos de [unidades de solicitação (RU)](documentdb-request-units.md) por segundo. Em vez de pensando e gerenciamento de recursos de hardware, você pode pensar em uma **unidade de solicitação** como uma medida única para os recursos necessários para executar várias operações de banco de dados e uma solicitação de aplicativo de serviço. Uma leitura de um documento de 1 KB consome a mesma 1 RU independentemente o número de itens armazenados na coleção ou o número de solicitações simultâneas em execução ao mesmo tempo. Todas as solicitações contra DocumentDB, incluindo operações complexas que consultas SQL tem um valor de RU previsível que pode ser determinado em tempo de desenvolvimento. Se você souber o tamanho de seus documentos e a frequência de cada operação (leituras, gravações e consultas) para oferecer suporte para o seu aplicativo, você pode provisionar a quantidade exata de produtividade para atender às necessidades de seu aplicativo e dimensionar seu banco de dados para cima e para baixo, como seu desempenho mudam. 

Cada conjunto pode ser reservado com produtividade em blocos de 100 unidades de solicitação por segundo, de centenas até milhões de unidades de solicitação por segundo. A taxa de transferência provisionada pode ser ajustada durante o ciclo de vida de um conjunto de se adaptar às necessidades processamento e padrões do seu aplicativo de acesso. Para obter mais informações, consulte [níveis de desempenho de DocumentDB](documentdb-performance-levels.md). 

>[AZURE.IMPORTANT] Coleções são entidades faturáveis. O custo é determinado pela produtividade provisionada do conjunto de medida em unidades de solicitação por segundo junto com o total de armazenamento consumida em gigabytes. 

Quantas unidades de solicitação irá consumir uma determinada operação como inserir, excluir, consulta ou a execução do procedimento armazenado? Uma unidade de solicitação é uma medida normalizada de custo de processamento de solicitação. Uma leitura de um documento de 1 KB é 1 RU, mas uma solicitação para inserir, substituir ou excluir o mesmo documento irá consumir mais processamento do serviço e, portanto, mais unidades de solicitação. Cada resposta do serviço inclui um cabeçalho personalizado (`x-ms-request-charge`) que relata as unidades de solicitação consumidas para a solicitação. Esse cabeçalho é também acessível pelos [SDKs](documentdb-sdk-dotnet.md). No SDK do .NET, [RequestCharge](https://msdn.microsoft.com/library/azure/dn933057.aspx#P:Microsoft.Azure.Documents.Client.ResourceResponse`1.RequestCharge) é uma propriedade do objeto [ResourceResponse](https://msdn.microsoft.com/library/azure/dn799209.aspx) . Se você quiser estimar suas necessidades de produtividade antes de fazer uma chamada única, você pode usar o [Planejador de capacidade](documentdb-request-units.md#estimating-throughput-needs) para ajudar com essa estimativa. 

>[AZURE.NOTE] A linha de base de 1 unidade de solicitação para um documento de 1 KB corresponde a um GET simple do documento com [Consistência da sessão](documentdb-consistency-levels.md). 

Há diversos fatores que afetam as unidades de solicitação consumidas para uma operação em uma conta de banco de dados DocumentDB. Esses fatores incluem:

- Tamanho do documento. Como tamanhos do documento aumentam as unidades consumidas para ler ou escrever que os dados também aumentará.
- Contagem de propriedade. Supondo indexação de padrão de todas as propriedades, as unidades consumidas para escrever um documento aumentará como aumenta a contagem de propriedade.
- Consistência de dados. Ao usar níveis de consistência de dados de forte ou envelhecimento limitada, unidades adicionais serão consumidas para ler documentos.
- Propriedades indexadas. Uma política de índice em cada coleção determina quais propriedades são indexadas por padrão. Você pode reduzir o consumo de unidade de solicitação limitando o número de propriedades indexadas. 
- Indexação de documentos. Por padrão a que cada documento será automaticamente indexado, você irá consumir menos unidades de solicitação se você optar por não indexar alguns dos seus documentos.

Para obter mais informações, consulte [unidades de solicitação de DocumentDB](documentdb-request-units.md). 

Por exemplo, aqui está uma tabela que mostra quantas unidades de solicitação para provisionar em três tamanhos de outro documento (1KB, 4KB e 64KB) e em dois níveis diferentes de desempenho (500 leituras/segundo gravações/segundo 100 e 500 leituras/segundo + 500 gravações/segundo). A consistência de dados foi configurada na sessão e a política de indexação foi definida como nenhum.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Tamanho do documento</strong></p></td>
            <td valign="top"><p><strong>Leituras/segundo</strong></p></td>
            <td valign="top"><p><strong>Gravações/segundo</strong></p></td>
            <td valign="top"><p><strong>Unidades de solicitação</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1) + (100* 5) = 1.000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *5) + (100* 5) = 3.000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1.3) + (100* 7) = 1.350 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *1.3) + (500* 7) = 4,150 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *10) + (100* 48) = 9.800 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *10) + (500* 48) = 29.000 RU/s</p></td>
        </tr>
    </tbody>
</table>

Consultas, procedimentos armazenados e disparadores consumam unidades de solicitação com base na complexidade das operações estão sendo executadas. À medida que você desenvolve seu aplicativo, inspecione o cabeçalho de carga de solicitação para entender melhor como a cada operação está consumindo a capacidade de unidade de solicitação.  


## <a name="choice-of-consistency-level-and-throughput"></a>Opção de nível de consistência e produtividade
A opção de nível de consistência padrão tem impacto sobre a produtividade e a latência. Você pode definir o nível de consistência padrão programaticamente e por meio do portal Azure. Você também pode substituir o nível de consistência em uma base por solicitação. Por padrão, o nível de consistência é definido como **sessão**, que oferece monotônico de leitura/gravação e ler suas garantias de gravação. Consistência de sessão é ótima para aplicativos centrados em usuário e fornece um equilíbrio ideal entre desempenho e consistência concessões.    

Para obter instruções sobre como alterar o nível de consistência no portal do Azure, consulte [como gerenciar uma conta de DocumentDB](documentdb-manage-account.md#consistency). Ou, para obter mais informações sobre níveis de consistência, consulte [níveis de consistência usando](documentdb-consistency-levels.md).

## <a name="provisioned-document-storage-and-index-overhead"></a>Sobrecarga de índice e armazenamento de documento provisionado
DocumentDB suporta a criação de conjuntos de partição única e particionadas. Cada partição no DocumentDB suporta até 10 GB de armazenamento SSD feito. O 10GB de armazenamento de documento inclui os documentos mais armazenamento para o índice. Por padrão, um conjunto de DocumentDB está configurado para indexar todos os documentos automaticamente sem a necessidade de explicitamente qualquer índices secundários ou esquema. A sobrecarga de índice típico com base em aplicativos usando DocumentDB, é entre 2-20%. A tecnologia de indexação usada pelo DocumentDB garante que, independentemente dos valores das propriedades, a sobrecarga de índice não exceda mais de 80% do tamanho dos documentos com as configurações padrão. 

Por padrão todos os documentos são indexados por DocumentDB automaticamente. No entanto, se você quiser ajustar a sobrecarga de índice, opte por remover determinados documentos de sendo indexados no momento do inserindo ou substituindo um documento, conforme descrito em [DocumentDB políticas de indexação](documentdb-indexing-policies.md). Você pode configurar um conjunto de DocumentDB para excluir todos os documentos dentro do conjunto de sendo indexados. Você também pode configurar um conjunto de DocumentDB para indexar seletivamente somente determinadas propriedades ou caminhos com curingas de seus documentos JSON, conforme descrito em [Configurando a política de indexação de um conjunto](documentdb-indexing-policies.md#configuring-the-indexing-policy-of-a-collection). Excluir propriedades ou documentos também melhora a taxa de transferência de gravação – o que significa que você irá consumir menos unidades de solicitação.   

## <a name="next-steps"></a>Próximas etapas

Para continuar aprendendo sobre o funcionamento do DocumentDB, consulte [Partitioning e a escala do Azure DocumentDB](documentdb-partition-data.md).

Para obter instruções sobre como monitorar os níveis de desempenho no portal do Azure, consulte [Monitor uma conta de DocumentDB](documentdb-monitor-accounts.md). Para obter mais informações sobre como escolher níveis de desempenho para conjuntos, consulte [níveis de desempenho em DocumentDB](documentdb-performance-levels.md).
 
