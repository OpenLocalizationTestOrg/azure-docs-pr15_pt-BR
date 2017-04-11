<properties
    pageTitle="Replicação de armazenamento Azure | Microsoft Azure"
    description="Dados em sua conta de armazenamento do Microsoft Azure são replicados para durabilidade e alta disponibilidade. Opções de replicação incluem localmente redundantes (LRS), zona redundantes do armazenamento (ZRS), armazenamento geográfica redundantes (GRS) e acesso de leitura geográfica-redundantes (ar-GRS)."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="tamram"/>

# <a name="azure-storage-replication"></a>Azure replicação de armazenamento

Os dados em sua conta de armazenamento do Microsoft Azure sempre são replicados para garantir durabilidade e alta disponibilidade. Replicação copia seus dados, dentro do mesmo data center ou em um segundo data center, dependendo de qual opção de replicação que você escolher. Replicação protege seus dados e preserva seu aplicativo tempo em caso de falhas de hardware temporárias. Se seus dados forem replicados para um segundo data center, que também protege seus dados contra uma falha grave no local principal.

Replicação garante que sua conta de armazenamento atende o [Contrato de nível de serviço (SLA) para armazenamento](https://azure.microsoft.com/support/legal/sla/storage/) mesmo diante de falhas. Consulte que o SLA para obter informações sobre o armazenamento do Azure garante durabilidade e disponibilidade. 

Quando você cria uma conta de armazenamento, você pode selecionar uma das opções de replicação a seguir:  

- [Armazenamento localmente redundante (LRS)](#locally-redundant-storage)
- [Armazenamento de zona redundantes (ZRS)](#zone-redundant-storage)
- [Armazenamento de localização geográfica redundantes (GRS)](#geo-redundant-storage)
- [Acesso de leitura geográfica-redundantes (ar-GRS)](#read-access-geo-redundant-storage)

Acesso de leitura geográfica-redundantes (ar-GRS) é a opção padrão quando você cria uma nova conta de armazenamento.

A tabela a seguir fornece uma rápida visão geral das diferenças entre LRS, ZRS, GRS e ar-GRS, enquanto subsequentes seções abordam cada tipo de replicação mais detalhadamente.

| Estratégia de replicação                                                               | LRS | ZRS | GRS | AR-GRS |
|:----------------------------------------------------------------------------------|:---|:---|:---|:------|
| Dados são replicados vários data centers.                                     | Não  | Sim | Sim | Sim    |
| Dados podem ser lidos local secundário, bem como do local principal. | Não  | Não  | Não  | Sim    |
| Número de cópias dos dados mantidos em nós separados.                             | 3   | 3   | 6   | 6      |

Consulte [Preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) para informações de preço para as opções de redundância diferentes.

>[AZURE.NOTE] Armazenamento de Premium oferece suporte apenas localmente redundantes (LRS). Para obter informações sobre o armazenamento de Premium, consulte [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](storage-premium-storage.md).

## <a name="locally-redundant-storage"></a>Armazenamento redundante localmente

Armazenamento localmente redundante (LRS) replica seus dados três vezes em uma unidade de escala de armazenamento que está hospedado em um Data Center na região em que você criou sua conta de armazenamento. Uma solicitação de gravação com êxito retornará somente depois que ele tiver sido escrito para todas as três réplicas. Essas três réplicas residem em domínios de falhas separada e atualização dentro de unidade de escala de um armazenamento. 

Uma unidade de escala de armazenamento é uma coleção de racks de nós de armazenamento. Um domínio de falha (FD) é um grupo de nós que representam uma unidade física de falha e pode ser considerado como nós pertencentes ao mesmo rack físico. Um domínio de atualização (UD) é um grupo de nós que são atualizados em conjunto durante o processo de uma atualização do serviço (implementação). As três réplicas são distribuídas entre UDs e FDs dentro de uma unidade de escala de armazenamento para garantir que os dados estejam disponíveis mesmo se falha de hardware afeta um único rack ou quando nós forem atualizados durante uma implementação. 

LRS é a opção de custo mais baixa e oferece durabilidade menos comparada a outras opções. Em caso de um desastre de nível de data center (acionará, saturação etc) todas as três réplicas podem ser perdida ou irrecuperável. Para reduzir esse risco geográfica redundantes armazenamento (GRS) é recomendado para a maioria dos aplicativos.

Armazenamento localmente redundante ainda pode ser desejável em determinados cenários: 

- Fornece maior largura de banda máxima de opções de replicação de armazenamento do Azure.

- Se seu aplicativo armazena dados que podem ser facilmente reconstruídos, você pode optar por LRS.

- Alguns aplicativos são restritos a replicação de dados apenas dentro de um país devido a requisitos de gestão de dados. Pode ser uma região par em outro país; consulte [regiões Azure](https://azure.microsoft.com/regions/) para obter informações sobre pares de região.

## <a name="zone-redundant-storage"></a>Armazenamento redundante de zona

Armazenamento de zona redundantes (ZRS) replica seus dados assíncrona através dos data centers dentro de uma ou duas regiões além de armazenar três réplicas semelhantes ao LRS, proporcionando durabilidade maior que LRS. Dados armazenados em ZRS são duráveis mesmo se do data center principal não está disponível ou irrecuperável.
Os clientes que planejam usar ZRS devem estar cientes que: 

- ZRS está disponível somente para blobs bloco em contas de armazenamento de finalidade geral e é suportado apenas nas versões de serviço de armazenamento de 2014-02-14 e posterior. 

- Como replicação assíncrona envolve um atraso, em caso de um desastre local é possível que as alterações que ainda não foram replicadas para o secundário serão perdidas se os dados não poderão ser recuperados do principal.

- A réplica pode não estar disponível até Microsoft inicia failover para o secundário.

- Contas ZRS não podem ser convertidas posteriormente LRS ou GRS. Da mesma forma, uma conta existente do LRS ou GRS não pode ser convertida para uma conta ZRS.

- Contas ZRS não têm métricas ou recurso de log. 

## <a name="geo-redundant-storage"></a>Armazenamento redundante de localização geográfica

Armazenamento de localização geográfica redundantes (GRS) replica seus dados para uma região secundária que é centenas de milhas longe da região primária. Se sua conta de armazenamento tem GRS habilitado, seus dados serão duráveis mesmo no caso de falta de regionais concluída ou um desastre em que a região primária não é recuperável.

Para uma conta de armazenamento com GRS habilitado, uma atualização primeiro está empenhada em região principal, onde ele é replicado três vezes. Em seguida, a atualização é replicada assíncrona para a região secundária, onde ele é replicado também três vezes. 

Com GRS as regiões primárias e secundárias gerenciar réplicas em domínios separados falhas e atualizar domínios dentro de uma unidade de escala de armazenamento conforme descrito com LRS.

Considerações sobre:

- Como replicação assíncrona envolve um atraso, em caso de um desastre regional é possível que as alterações que ainda não foram replicadas para a região secundária serão perdidas se os dados não poderão ser recuperados da região primária.

- A réplica não está disponível a menos que a Microsoft inicia o failover para a região secundário.

- Se um aplicativo deseja ler a partir da região secundária o usuário deve habilitar GRS ar. 

Quando você cria uma conta de armazenamento, selecione a região principal da conta. A região secundária é determinada com base na região primário e não pode ser alterada. A tabela a seguir mostra os pares de região primário e secundário.

| Principal             | Secundário           |
|---------------------|---------------------|
| Centro Norte dos EUA    | Centro Sul dos EUA    |
| Centro Sul dos EUA    | Centro Norte dos EUA    |
| Leste EUA             | Oeste EUA             |
| Oeste EUA             | Leste EUA             |
| Leste dos EUA 2           | Centro dos EUA          |
| Centro dos EUA          | Leste dos EUA 2           |
| Norte da Europa        | Europa Ocidental         |
| Europa Ocidental         | Norte da Europa        |
| Sudeste da Ásia     | Da Ásia Oriental           |
| Da Ásia Oriental           | Sudeste da Ásia     |
| China Leste          | Norte da China         |
| Norte da China         | China Leste          |
| Japão Leste          | Japão Oeste          |
| Japão Oeste          | Japão Leste          |
| Brasil Sul        | Centro Sul dos EUA    |
| Austrália Oriental      | Austrália Sudeste |
| Austrália Sudeste | Austrália Oriental      |
| Índia Sul         | Índia Central       |
| Índia Central       | Índia Sul         |
| EUA Gov Iowa         | EUA Gov Virgínia     |
| EUA Gov Virgínia     | EUA Gov Iowa         |
| Canadá Central      | Canadá Oriental          |
| Canadá Leste         | Canadá Central      |
| Oeste do Reino Unido             | Sul do Reino Unido            |
| Sul do Reino Unido            | Oeste do Reino Unido             |
| Central de Alemanha     | Alemanha nordeste   |
| Alemanha nordeste   | Central de Alemanha     |
| Oeste EUA 2           | Central Oeste EUA     |
| Central Oeste EUA     | Oeste EUA 2           |

Para obter informações atualizadas sobre regiões têm suporte no Azure, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).
 
## <a name="read-access-geo-redundant-storage"></a>Armazenamento de localização geográfica redundantes de acesso de leitura

Acesso de leitura geográfica-redundantes (ar-GRS) maximiza a disponibilidade de sua conta de armazenamento, fornecendo acesso somente leitura aos dados no local secundário, além da replicação entre duas regiões fornecidos pelo GRS. 

Quando você habilita o acesso somente leitura aos seus dados na região secundário, seus dados estão disponíveis em um ponto de extremidade secundário, além do principal ponto de extremidade para a sua conta de armazenamento. O ponto de extremidade secundário é semelhante ao ponto de extremidade principal, mas acrescenta o sufixo `–secondary` ao nome da conta. Por exemplo, se o seu principal ponto de extremidade do serviço Blob é `myaccount.blob.core.windows.net`, então o ponto de extremidade secundário é `myaccount-secondary.blob.core.windows.net`. As teclas de acesso para sua conta de armazenamento são as mesmas para ambas as extremidades primárias e secundárias.

Considerações sobre:

- Seu aplicativo tem que gerenciar a extremidade que ele está interagindo com ao usar GRS ar. 

- AR-GRS destina-se para fins de alta disponibilidade. Para obter orientação escalabilidade, examine a [lista de verificação de desempenho](storage-performance-checklist.md).

## <a name="next-steps"></a>Próximas etapas

- [Preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/)
- [Sobre contas de armazenamento do Azure](storage-create-storage-account.md)
- [Metas de desempenho e escalabilidade de armazenamento do Azure](storage-scalability-targets.md)
- [Opções de redundância de armazenamento do Microsoft Azure e acesso de leitura geográfica armazenamento redundante](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)  
- [Papel SOSP - armazenamento do Azure: Um serviço de armazenamento de nuvem altamente disponível com consistência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  
