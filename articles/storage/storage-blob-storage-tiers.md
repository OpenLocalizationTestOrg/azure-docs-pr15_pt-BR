<properties
    pageTitle="Armazenamento interessante do Azure para blobs | Microsoft Azure"
    description="Níveis de armazenamento para armazenamento de Blob do Azure oferecem armazenamento econômico para dados de objeto com base em padrões de acesso. O nível de armazenamento interessantes é otimizado para dados acessados com menos frequência."
    services="storage"
    documentationCenter=""
    authors="michaelhauss"
    manager="vamshik"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="mihauss"/>


# <a name="azure-blob-storage-hot-and-cool-storage-tiers"></a>Armazenamento de Blob do Azure: Quente e interessantes níveis de armazenamento

## <a name="overview"></a>Visão geral

Agora, o armazenamento do Azure oferece dois níveis de armazenamento para armazenamento de Blob (armazenamento de objeto), para que você possa armazenar seus dados mais econômico dependendo de como você usá-lo. O Azure **nível de armazenamento quente** é otimizada para armazenar dados acessados com frequência. O **nível de armazenamento interessantes** de Azure é otimizada para armazenar dados raramente acessados e vida longa. Dados na camada de armazenamento interessantes podem suportar uma disponibilidade ligeiramente inferior, mas ainda requerem alta durabilidade e tempo semelhante para acessar e características de produtividade como dados quente. Para dados interessantes, um pouco inferior SLA de disponibilidade e custos mais altos de acesso são aceitáveis concessões para muito menores custos de armazenamento.

Atualmente, os dados armazenados na nuvem estão aumentando em um ritmo exponencial. Para gerenciar custos para suas necessidades de armazenamento expansão, é útil organizar seus dados com base em atributos como frequência de acesso e o período de retenção planejada. Dados armazenados na nuvem podem ser bastante diferentes em termos de como ele é gerado, processado e acessado pela sua vida útil. Alguns dados ativamente são acessados e modificados em todo o seu ciclo de vida. Alguns dados são acessados com muita frequência adiantado no seu ciclo de vida, com acesso soltando drasticamente como os anos de dados. Alguns dados permanecem ociosos na nuvem e é raramente, se nunca, acessado uma vez armazenados.

Cada um desses cenários de acesso de dados descrito acima benefícios de um nível diferenciado de armazenamento otimizado para um padrão de acesso específico. Com a introdução dos níveis de armazenamento quente e interessante, o Azure Blob storage agora lida com essa necessidade de níveis de armazenamento diferenciados com separe preços modelos.

## <a name="blob-storage-accounts"></a>Contas de armazenamento de blob

**Contas de armazenamento de blob** são armazenamento especializadas contas para armazenar seus dados não estruturados como blobs (objetos) no armazenamento do Azure. Com contas de armazenamento de Blob, você pode escolher entre níveis de armazenamento quente e interessantes para armazenar seus dados interessantes menos acessados em um armazenamento de menor custo e armazenam dados quente mais acessados em um acesso inferior de custo. Contas de armazenamento de blob são semelhantes às suas contas de armazenamento geral existentes e compartilham todos o durabilidade excelente, disponibilidade, escalabilidade e recursos de desempenho que você usa atualmente, incluindo 100% de consistência API para blobs de bloco e acrescentar blobs.

> [AZURE.NOTE] Contas de armazenamento de blob suportam apenas bloco e acrescentar blobs e não blobs de página.

Contas de armazenamento de blob expõem o atributo de **Nível de acesso** , que permitem que você especificar o nível de armazenamento como **quente** ou **interessantes** dependendo dos dados armazenados na conta. Se houver uma alteração no padrão de uso dos dados, você também pode alternar entre esses níveis de armazenamento a qualquer momento.

> [AZURE.NOTE] Alterar o nível de armazenamento pode resultar em encargos adicionais. Consulte a seção de [preços e cobrança](storage-blob-storage-tiers.md#pricing-and-billing) para obter mais detalhes.

Cenários de uso de exemplo para o nível de armazenamento quente incluem:

- Dados que está em uso ativo ou esperado para serem acessados (lidas do e gravados) com frequência.
- Dados transferidos para a migração de processamento e eventual para o nível de armazenamento interessantes.

Cenários de uso de exemplo para o nível de armazenamento interessantes incluem:

- Backup, arquivamento e conjuntos de dados de recuperação de desastres.
- Conteúdo de mídia mais antigo não visualizadas com frequência mais, mas é esperado esteja disponível imediatamente quando acessado.
- Grandes conjuntos de dados que precisam ser armazenado custo efetivamente enquanto mais dados estão sendo reunidos para processamento futuro. (*por exemplo*, o armazenamento de longo prazo de dados científicos, dados de telemetria bruto de um recurso de fabricação)
- Dados originais (brutos) que devem ser preservados, mesmo depois que ela foi processada em formulário utilizável final. (*por exemplo*, arquivos de mídia bruto após transcodificação em outros formatos)
- Conformidade e arquivamento dados que precisam ser armazenada por um longo tempo e quase nunca são acessados. (*por exemplo*, filmagem em câmera de segurança, antigo X-Rays/relatórios para as organizações de saúde, gravações de áudio e transcrições de chamadas de atendimento ao cliente para serviços financeiros)

Consulte [contas de armazenamento do Azure sobre](storage-create-storage-account.md) para obter mais informações sobre contas de armazenamento.

Para aplicativos que exigem apenas bloquear ou acrescentar armazenamento de blob, é recomendável usar contas de armazenamento de Blob, aproveitar o modelo de preços diferenciado de armazenamento hierárquico. No entanto, entendemos que isso pode não ser possível em determinadas circunstâncias onde usando contas de armazenamento geral seria a maneira de ir, tais como:

- Você precisa usar tabelas, filas ou arquivos e quer que seus blobs armazenados na mesma conta de armazenamento. Observe que não há nenhuma vantagem técnica para armazenar essas na mesma conta diferente de ter os mesmos chaves compartilhadas.
- Ainda é necessário usar o modelo de implantação do clássico. Contas de armazenamento de blob só estão disponíveis por meio do modelo de implantação do Gerenciador de recursos do Azure.
- Você precisará usar blobs de página. Contas de armazenamento de blob não suportam blobs de página. Geralmente recomendamos usando blobs de bloco, a menos que tenha uma necessidade específica para blobs de página.
- Você usa uma versão da [API de REST de serviços de armazenamento](https://msdn.microsoft.com/library/azure/dd894041.aspx) anterior 2014-02-14 ou uma biblioteca de cliente com uma versão menor do que 4. x e não é possível atualizar o aplicativo.

> [AZURE.NOTE] Contas de armazenamento de blob são aceitos atualmente na maioria das regiões Azure com mais de acompanhar. Você pode encontrar a lista atualizada de regiões disponíveis na página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services) .

## <a name="comparison-between-the-storage-tiers"></a>Comparação entre os níveis de armazenamento

A tabela a seguir destaca a comparação entre os dois níveis de armazenamento:

<table border="1" cellspacing="0" cellpadding="0" style="border: 1px solid #000000;">
<col width="250">
<col width="250">
<col width="250">
<tbody>
<tr>
    <td><strong><center></center></strong></td>
    <td><strong><center>Nível de armazenamento quente</center></strong></td>
    <td><strong><center>Nível de armazenamento interessantes</center></strong></td
</tr>
<tr>
    <td><strong><center>Disponibilidade</center></strong></td>
    <td><center>99,9%</center></td>
    <td><center>99%</center></td>
</tr>
<tr>
    <td><strong><center>Disponibilidade<br>(Leituras de ar GRS)</center></strong></td>
    <td><center>99,99%</center></td>
    <td><center>99,9%</center></td>
</tr>
<tr>
    <td><strong><center>Encargos de uso</center></strong></td>
    <td><center>Custos mais altos de armazenamento<br>Custos de acesso e transação</center></td>
    <td><center>Custos de armazenamento<br>Custos mais altos de acesso e transação</center></td>
</tr>
<tr>
    <td><strong><center>Tamanho mínimo de objeto<center></strong></td>
    <td colspan="2"><center>N/D</center></td>
</tr>
<tr>
    <td><strong><center>Duração de armazenamento mínima<center></strong></td>
    <td colspan="2"><center>N/D</center></td>
</tr>
<tr>
    <td><strong><center>Latência<br>(Tempo para primeiro byte)<center></strong></td>
    <td colspan="2"><center>milissegundos</center></td>
</tr>
<tr>
    <td><strong><center>Destinos de desempenho e escalabilidade<center></strong></td>
    <td colspan="2"><center>Mesmo que contas de armazenamento geral</center></td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Contas de armazenamento de blob suportam o mesmo desempenho e os destinos de escalabilidade como contas de armazenamento geral. Para obter mais informações, consulte [metas de desempenho e escalabilidade de armazenamento do Azure](storage-scalability-targets.md) .

## <a name="pricing-and-billing"></a>Preços e cobrança

Contas de armazenamento de blob usam um novo modelo de preços para armazenamento de blob com base no nível de armazenamento. Ao usar uma conta de armazenamento de Blob, aplicam as seguintes considerações de cobranças:

- **Os custos de armazenamento**: além da quantidade de dados armazenados, o custo do armazenamento de dados varia dependendo do nível de armazenamento. O custo por gigabyte é menor para o nível de armazenamento interessantes que para o nível de armazenamento quente.
- **Custos de acesso a dados**: para dados em nível de armazenamento interessantes, você será cobrado um encargo de acesso de dados por gigabyte para leituras e gravações.
- **Os custos de transação**: não há um custo por transação para ambas as camadas. No entanto, o custo por transação para o nível de armazenamento interessantes seja maior que para o nível de armazenamento quente.
- **Os custos de transferência de dados de replicação geográfica**: isso se aplica somente a contas com geográfica replicação configurada, incluindo GRS e ar GRS. Transferência de dados de replicação geográfica gera um custo por gigabyte.
- **Dados de saída transferir custos**: transferências de dados de saída (dados que são transferidos fora uma região Azure) provocam cobrança para uso de largura de banda numa base por gigabyte, consistentes com contas de armazenamento geral.
- **Alterar o nível de armazenamento**: alterar o nível de armazenamento de legal para quente provocará um encargo igual a todos os dados existentes na conta de armazenamento para cada transição de leitura. Por outro lado, alterando o nível de armazenamento de alta para legal será gratuito.

> [AZURE.NOTE] Para permitir que usuários experimentar os novos níveis de armazenamento e validar funcionalidade após o lançamento, o encargo para alterar o armazenamento camada de legal para quente será cobrada desativar até de 2016 30 de junho. Iniciando de 2016 1º de julho, o encargo será aplicado a todas as transições de interessante em funcionamento. Para obter mais detalhes sobre o modelo de preços para armazenamento de Blob contas, consulte a página de [Preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) . Para obter mais detalhes sobre os dados de saída encargos de transferência, consulte a página de [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/) .

## <a name="quick-start"></a>Início rápido

Nesta seção demonstraremos cenários a seguir usando o portal de Azure:

- Como criar uma conta de armazenamento de Blob.
- Como gerenciar uma conta de armazenamento de Blob.

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

#### <a name="create-a-blob-storage-account-using-the-azure-portal"></a>Criar uma conta de armazenamento de Blob usando o portal do Azure

1. Entrar no [portal do Azure](https://portal.azure.com).

2. No menu Hub, selecione **novo** > **dados + armazenamento** > **conta de armazenamento**.

3. Insira um nome para a sua conta de armazenamento.

    Esse nome deve ser exclusivo; ele é usado como parte da URL usada para acessar os objetos na conta de armazenamento.  

4. Selecione **Gerenciador de recursos** como o modelo de implantação.

    Armazenamento hierárquico só pode ser usado com contas de armazenamento do Gerenciador de recursos; Este é o modelo de implantação recomendada para novos recursos. Para obter mais informações, confira a [Visão geral do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md).  

5. Na lista suspensa tipo de conta, selecione o **Armazenamento de Blob**.

    Isso é onde você seleciona o tipo de conta de armazenamento. Armazenamento hierárquico não está disponível no armazenamento de uso geral; ele está disponível somente na conta do tipo de armazenamento de Blob.    

    Observe que, quando você seleciona essa opção, o nível de desempenho é definido como padrão. Armazenamento hierárquico não está disponível com o nível de desempenho Premium.

6. Selecione a opção de replicação para a conta de armazenamento: **LRS**, **GRS**ou **GRS ar**. O padrão é **GRS ar**.

    LRS = armazenamento localmente redundante; GRS = armazenamento geográfica redundante (2 regiões); AR-GRS é armazenamento geográfica redundantes de acesso de leitura (2 regiões com acesso de leitura para a segunda).

    Para obter mais detalhes sobre as opções de replicação de armazenamento do Azure, confira [replicação de armazenamento do Azure](storage-redundancy.md).

7. Selecione o nível de armazenamento certo para as suas necessidades: defina o **nível de acesso** para **interessantes** ou **quente**. O padrão é **quente**.

8. Selecione a assinatura na qual você deseja criar a nova conta de armazenamento.

9. Especificar um novo grupo de recursos ou selecione um grupo de recursos existente. Para obter mais informações sobre grupos de recursos, consulte [Visão geral do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

10. Selecione a região para a sua conta de armazenamento.

11. Clique em **criar** para criar a conta de armazenamento.

#### <a name="change-the-storage-tier-of-a-blob-storage-account-using-the-azure-portal"></a>Alterar o nível de armazenamento de uma conta de armazenamento de Blob usando o portal do Azure

1. Entrar no [portal do Azure](https://portal.azure.com).

2. Para navegar para sua conta de armazenamento, selecione todos os recursos, em seguida, selecione sua conta de armazenamento.

3. Na lâmina configurações, clique em **configuração** para exibir e/ou alterar a configuração de conta.

4. Selecione o nível de armazenamento certo para as suas necessidades: defina o **nível de acesso** para **interessantes** ou **quente**.

5. Clique em Salvar na parte superior da lâmina.

> [AZURE.NOTE] Alterar o nível de armazenamento pode resultar em encargos adicionais. Consulte a seção de [preços e cobrança](storage-blob-storage-tiers.md#pricing-and-billing) para obter mais detalhes.

## <a name="evaluating-and-migrating-to-blob-storage-accounts"></a>Avaliando e migração para contas de armazenamento de Blob

A finalidade desta seção é ajudar os usuários a fazer uma transição suave usando contas de armazenamento de Blob. Há dois cenários de usuário:

- Você tiver uma conta existente do armazenamento de uso geral e deseja avaliar uma alteração em uma conta de armazenamento de Blob com o nível de armazenamento certo.
- Você decidiu usar uma conta de armazenamento de Blob ou já tiver um e deseja avaliar se você deve usar o nível de armazenamento de alta ou legais.

Em ambos os casos, a primeira coisa é estimar o custo de armazenar e acessar os dados armazenados em uma conta de armazenamento de Blob e compare-os em relação aos custos atuais.

### <a name="evaluating-blob-storage-account-tiers"></a>Avaliar níveis de conta de armazenamento de Blob

Para estimar o custo de armazenar e acessar dados armazenados em uma conta de armazenamento de Blob, você precisará avaliar seu padrão de uso existente ou aproximado seu padrão de uso esperado. Em geral, você desejará saber:

- O consumo de armazenamento - como quantidade de dados estiverem sendo armazenado e como isso altera mensalmente?
- Seu padrão de acesso de armazenamento - a quantidade de dados está sendo ler e escrita à conta (incluindo novos dados)? Quantas transações são usadas para acesso a dados e que tipos de transações são?

#### <a name="monitoring-existing-storage-accounts"></a>Monitoramento contas de armazenamento existentes

Para monitorar suas contas de armazenamento existentes e reunir esses dados, você pode fazer uso da análise de armazenamento do Azure que executa o log e fornece métricas dados para uma conta de armazenamento.
Análise de armazenamento pode armazenar métricas que incluam dados de estatísticas e a capacidade de transação agregado sobre solicitações para o serviço de armazenamento de Blob para contas de armazenamento geral, bem como as contas de armazenamento de Blob.
Esses dados são armazenados em tabelas conhecidas na mesma conta de armazenamento.

Para obter mais detalhes, consulte [Sobre métricas de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343258.aspx) e [Esquema de tabela de métricas de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [AZURE.NOTE] Contas de armazenamento de blob expõem o ponto de extremidade do serviço de tabela somente para armazenar e acessar os dados de métricas para essa conta.

Para monitorar o consumo de armazenamento para o serviço de armazenamento de Blob, você precisará habilitar as métricas de capacidade.
Com isso habilitado, os dados de capacidade são registrados diária para o serviço de Blob de uma conta de armazenamento e gravadas como uma entrada de tabela escrito à tabela *$MetricsCapacityBlob* na mesma conta de armazenamento.

Para monitorar o padrão de acesso de dados para o serviço de armazenamento de Blob, você precisará habilitar as métricas de transação por hora em um nível de API.
Com esta opção habilitada, por API transações são agregadas a cada hora e registradas como uma entrada de tabela escrito à tabela *$MetricsHourPrimaryTransactionsBlob* na mesma conta de armazenamento. A tabela *$MetricsHourSecondaryTransactionsBlob* registra as transações para o ponto de extremidade secundário no caso de contas de armazenamento de ar GRS.

> [AZURE.NOTE] Caso você tenha uma conta de armazenamento geral no qual você armazenou blobs de página e discos de máquina virtual junto com o bloco e anexe dados blob, este processo de previsão não é aplicável. Isso ocorre porque você não terá nenhuma maneira de capacidade diferenciar e métricas de transação com base no tipo de blob para somente bloquear e acrescentar blobs que podem ser migradas para uma conta de armazenamento de Blob.

Para obter uma boa aproximação do consumo de dados e o acesso padrão, recomendamos que você escolha um período de retenção para as métricas que represente de seu uso regular e expande.
Uma opção é manter os dados de métricas por 7 dias e coletar os dados de cada semana, para análise no final do mês.
Outra opção é manter os dados de métricas para últimos 30 dias e coletar e analisar os dados no final do período de 30 dias.

Para obter detalhes sobre como ativar, coletar e exibir dados de métricas, consulte, [permitindo que o armazenamento do Azure métricas e exibir dados de métricas](storage-enable-and-view-metrics.md).

> [AZURE.NOTE] Armazenar, acessar e baixar dados de análise também é cobrado assim como os dados de usuário regular.

#### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Utilizando métricas de uso para estimar custos

##### <a name="storage-costs"></a>Custos de armazenamento

A entrada mais recente na capacidade métricas tabela *$MetricsCapacityBlob* com a chave de linha *'dados'* mostra a capacidade de armazenamento consumida pelos dados do usuário.
A entrada mais recente na capacidade métricas tabela *$MetricsCapacityBlob* com a chave de linha *'analytics'* mostra a capacidade de armazenamento consumida pelos logs de análise.

Esta capacidade total consumida por ambos os dados de usuário e logs de análise (se habilitado), em seguida, podem ser usados para estimar o custo do armazenamento de dados na conta de armazenamento.
O mesmo método também podem ser usados para calcular os custos de armazenamento de bloco e acrescentar blobs em contas de armazenamento geral.

##### <a name="transaction-costs"></a>Custos de transação

A soma de *'TotalBillableRequests'*, todas as entradas para uma API na tabela de métricas de transações indica o número total de transações para essa determinada API. *por exemplo*, o número total de transações de *'GetBlob'* em um determinado período pode ser calculado pela soma do total de solicitações faturáveis para todas as entradas com a chave de linha *' usuário; GetBlob'*.

Para estimar custos de transação para contas de armazenamento de Blob, você precisará divida as transações em três grupos, desde que eles têm preços de forma diferente.

- Escreva transações como *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'*e *'CopyBlob'*.
- Exclua transações como *'DeleteBlob'* e *'DeleteContainer'*.
- Todas as outras transações.

Para estimar custos de transação para contas de armazenamento geral, você precisa agregar todas as transações independentemente da operação/API.

##### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Custos de transferência de dados de acesso e localização geográfica replicação de dados

Embora a análise de armazenamento não fornecer a quantidade de dados ler e gravadas em uma conta de armazenamento, pode ser estimada aproximadamente olhando a tabela de métricas de transação.
A soma de *'TotalIngress'* todas as entradas para uma API na tabela de métricas de transações indica a quantidade total de dados de ingresso em bytes para essa determinada API.
Da mesma forma a soma de *'TotalEgress'* indica a quantidade total de dados de saída, em bytes.

Para estimar os custos de acesso de dados para contas de armazenamento de Blob, você precisará divida as transações em dois grupos.

- A quantidade de dados recuperados da conta de armazenamento pode ser estimada examinando a soma de *'TotalEgress'* principalmente das operações *'GetBlob'* e *'CopyBlob'* .
- A quantidade de dados gravados para a conta de armazenamento pode ser estimada examinando a soma de *'TotalIngress'* para principalmente *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* e *'AppendBlock'* operações.

O custo de transferência de dados de replicação geográfica para contas de armazenamento de Blob também pode ser calculado usando a estimativa para a quantidade de dados gravados no caso de uma conta de armazenamento GRS ou GRS ar.

> [AZURE.NOTE] Para obter um exemplo mais detalhado sobre como calcular os custos para usar o nível de armazenamento de alta ou interessantes, dê uma olhada em perguntas frequentes sobre o intitulado *'quais são os níveis de acesso quente e interessante e como para determinar qual delas use?'* no [armazenamento do Azure preços página](https://azure.microsoft.com/pricing/details/storage/).

### <a name="migrating-existing-data"></a>Migrar dados existentes

Uma conta de armazenamento de Blob é especializado para armazenar apenas bloco e acrescentar blobs. Contas de armazenamento geral existentes, que permitem armazenar tabelas, filas, arquivos e discos, bem como blobs, não podem ser convertidas em contas de armazenamento de Blob. Para usar os níveis de armazenamento, você precisará criar novas contas de armazenamento de Blob e migrar os dados existentes para as contas recém-criadas.
Você pode usar os seguintes métodos para migrar os dados existentes para contas de armazenamento de Blob de dispositivos de armazenamento no local, provedores de armazenamento de nuvem de terceiros ou de suas contas de armazenamento geral existentes no Azure:

#### <a name="azcopy"></a>AzCopy

AzCopy é um utilitário de linha de comando do Windows projetado para alto desempenho copiando dados de e armazenamento do Azure. Você pode usar AzCopy para copiar dados para sua conta de armazenamento de Blob de suas contas de armazenamento geral existente, ou para carregar dados de dispositivos de armazenamento local na sua conta de armazenamento de Blob.

Para obter mais detalhes, consulte [transferir dados com o utilitário de AzCopy de linha de comando](storage-use-azcopy.md).

#### <a name="data-movement-library"></a>Biblioteca de movimentação de dados

Azure biblioteca de movimentação de dados de armazenamento para .NET se baseia a estrutura de movimentação de dados de núcleo que alimenta AzCopy. A biblioteca destina-se a operações de transferência de dados de alto desempenho, confiável e fácil semelhante a AzCopy. Isso possibilita aproveitar todos os benefícios dos recursos fornecidos pelo AzCopy em seu aplicativo nativo sem ter de lidar com execução e monitoramento externas instâncias de AzCopy.

Para obter mais detalhes, consulte [Biblioteca de movimentação de dados do Azure armazenamento para .net](https://github.com/Azure/azure-storage-net-data-movement)

#### <a name="rest-api-or-client-library"></a>API REST ou a biblioteca de cliente

Você pode criar um aplicativo personalizado para migrar seus dados para uma conta de armazenamento de Blob usando uma das bibliotecas do cliente Azure ou os serviços de armazenamento do Azure API REST. Armazenamento do Azure fornece bibliotecas de cliente avançado para vários idiomas e plataformas como .NET, Java, C++, Node, PHP, Ruby e Python. As bibliotecas de cliente oferecem recursos avançados como lógica de repetição, log e carregamentos paralelos. Você também pode desenvolver diretamente com a API REST, que pode ser chamado por qualquer idioma que faz solicitações HTTP/HTTPS.

Para obter mais detalhes, consulte [Introdução ao armazenamento de Blob do Azure](storage-dotnet-how-to-use-blobs.md).

> [AZURE.NOTE] BLOBs criptografadas usando criptografia do lado do cliente armazenam metadados relacionados à criptografia armazenados com o blob. É fundamental que qualquer mecanismo de cópia deve garantir que os metadados de blob e principalmente relacionados à criptografia metadados, é preservado. Se você copiar os blobs sem esses metadados, o conteúdo blob não será recuperável novamente. Para obter mais detalhes sobre metadados relacionados à criptografia, consulte [criptografia de lado do cliente de armazenamento do Azure](storage-client-side-encryption.md).

## <a name="faqs"></a>Perguntas frequentes

1. **Existentes contas de armazenamento continuam disponíveis?**

    Sim, contas de armazenamento existentes ainda estão disponíveis e são alteradas na preços ou funcionalidade.  Eles não têm a capacidade de escolher um nível de armazenamento e não terão recursos hierárquico no futuro.

2. **Por que e quando devo para começar a usar contas de armazenamento de Blob?**

    Contas de armazenamento de blob são especializadas para armazenar blobs e nos permitem introduzir novos recursos centrados em blob. Ao prosseguir, contas de armazenamento de Blob são a maneira recomendada para armazenar blobs, como futuros recursos como armazenamento hierárquico e hierarquia será apresentado com base nesse tipo de conta. No entanto, ela é até quando você gostaria de migrar com base nas necessidades de negócios.

3. **Pode converter minha conta de armazenamento existente para uma conta de armazenamento de Blob?**

    Não. Conta de armazenamento de blob é um tipo diferente de conta de armazenamento e você precisará criá-lo novo e migrar os dados conforme explicado acima.

4. **Pode armazenar objetos em ambos os níveis de armazenamento na mesma conta?**

    O atributo *' camada de acesso '* que indica o nível de armazenamento é definido em um nível de conta e aplica-se a todos os objetos nessa conta. Você não pode definir o atributo de nível de acesso em nível de objeto.

5. **Posso alterar o nível de armazenamento de minha conta de armazenamento de Blob?**

    Sim. Você poderá alterar o nível de armazenamento, definindo o atributo *' camada de acesso '* na conta de armazenamento. Alterar o nível de armazenamento será aplicada a todos os objetos armazenados na conta. Alterar o nível de armazenamento de alta legal não provocará qualquer encargos, durante a alteração de legal para quente provocará um custo GB para ler todos os dados na conta por.

6. **Frequência posso alterar o nível de armazenamento de minha conta de armazenamento de Blob?**

    Enquanto não podemos impor uma limitação na frequência o nível de armazenamento pode ser alterado, esteja ciente de que alterar o nível de armazenamento de legal para quente provocará encargos significativos. Não é recomendável alterando o nível de armazenamento com frequência.

7. **Os blobs na camada de armazenamento interessantes irá se comportar diferente aquelas na camada de armazenamento quente?**

    BLOBs na camada de armazenamento quente têm a mesma latência como blobs em contas de armazenamento geral. BLOBs na camada de armazenamento interessantes têm uma latência semelhante (em milissegundos) como blobs em contas de armazenamento geral.

    BLOBs na camada de armazenamento interessantes terá um levemente menor nível de serviço de disponibilidade (SLA) que os blobs armazenados no nível de armazenamento quente. Para obter mais detalhes, consulte [SLA para armazenamento](https://azure.microsoft.com/support/legal/sla/storage).

8. **Pode armazenar blobs de página e discos de máquina virtual em contas de armazenamento de Blob?**

    Contas de armazenamento de blob suportam apenas bloco e acrescentar blobs e não blobs de página. Azure máquina virtual discos contam blobs de página e como resultado contas de armazenamento de Blob não podem ser usadas para armazenar discos de máquina virtual. No entanto é possível armazenar backups dos discos máquina virtual como blobs de bloco de uma conta de armazenamento de Blob.

9. **Será necessário alterar meus aplicativos existentes para usar contas de armazenamento de Blob?**

    Contas de armazenamento de blob são 100% API consistente com contas de armazenamento geral para bloco e acrescentar blobs. Enquanto seu aplicativo estiver usando bloquear blobs ou acrescentar blobs e você está usando a versão de 2014-02-14 da [API de REST de serviços de armazenamento](https://msdn.microsoft.com/library/azure/dd894041.aspx) ou maior e seu aplicativo apenas deve funcionar. Se você estiver usando uma versão mais antiga do protocolo, você precisará atualizar seu aplicativo para usar a nova versão para trabalhar de forma integrada com os dois tipos de contas de armazenamento. Em geral, sempre recomendamos usando a versão mais recente, independentemente de qual conta de armazenamento tipo usamos.

10. **Haverá uma alteração na experiência do usuário?**

    Contas de armazenamento de blob são muito semelhantes a um contas de armazenamento geral para armazenar bloco acrescentar blobs e oferecer suporte a todos os recursos principais do armazenamento do Azure, incluindo alta durabilidade e disponibilidade, escalabilidade, desempenho e segurança. Além os recursos e restrições específicas para contas de armazenamento de Blob e seus níveis de armazenamento que foram explicados acima, tudo permanece o mesmo.

## <a name="next-steps"></a>Próximas etapas

### <a name="evaluate-blob-storage-accounts"></a>Avaliar as contas de armazenamento de Blob

[Verificar a disponibilidade de contas de armazenamento de Blob por região](https://azure.microsoft.com/regions/#services)

[Avaliar o uso de suas contas de armazenamento atual habilitando métricas de armazenamento do Azure](storage-enable-and-view-metrics.md)

[Verificar preços de armazenamento de Blob por região](https://azure.microsoft.com/pricing/details/storage/)

[Transferências de dados de seleção preços](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-blob-storage-accounts"></a>Comece a usar contas de armazenamento de Blob

[Introdução ao armazenamento de Blob do Azure](storage-dotnet-how-to-use-blobs.md)

[Mover os dados para e do armazenamento do Azure](storage-moving-data.md)

[Transferir dados com o utilitário de AzCopy de linha de comando](storage-use-azcopy.md)

[Navegue e explore suas contas de armazenamento](http://storageexplorer.com/)
