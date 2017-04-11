<properties
    pageTitle="O que fazer em caso de uma interrupção de armazenamento do Azure | Microsoft Azure"
    description="O que fazer em caso de uma interrupção de armazenamento do Azure"
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>


# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>O que fazer se ocorrer uma falha de armazenamento do Azure

Na Microsoft, podemos faz trabalhar irreversível para garantir que nossos serviços estão sempre disponíveis. Às vezes, força além do nosso impacto de controle nos de maneiras que causam interrupções planejadas de serviço em uma ou mais regiões. Para ajudá-lo a lidar com essas ocorrências raras, fornecemos as seguintes diretrizes de alto nível para serviços de armazenamento do Azure.

## <a name="how-to-prepare"></a>Como preparar 

É fundamental para cada cliente preparar seu próprios plano de recuperação de desastres. O esforço para recuperar de uma falha de armazenamento normalmente envolve a equipe de operações e procedimentos automatizados para reativar os aplicativos em um estado de funcionamento. Consulte a documentação do Azure abaixo para criar seu próprio plano de recuperação de desastres:

-   [Recuperação de dados e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

-   [Orientações técnicas resiliência Azure](../resiliency/resiliency-technical-guidance.md)

-   [Serviço de recuperação de Site Azure](https://azure.microsoft.com/services/site-recovery/)

-   [Azure replicação de armazenamento](storage-redundancy.md)

-   [Serviço de Backup Azure](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Como detectar 

A maneira recomendada para determinar o status do serviço Azure é inscrever-se para o [Painel de integridade de serviço do Azure](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>O que fazer se ocorrer uma falha de armazenamento

Se um ou mais serviços de armazenamento estão disponíveis no momento em uma ou mais regiões, existem duas opções para você considerar. Se você quiser acesso imediato a seus dados, considere a opção 2.

### <a name="option-1-wait-for-recovery"></a>Opção 1: Espere a recuperação

Nesse caso, nenhuma ação de sua parte é necessária. Estamos trabalhando cuidadosamente para restaurar a disponibilidade do serviço Azure. Você pode monitorar o status do serviço no [Painel de integridade de serviço do Azure](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>Opção 2: Copiar dados de secundário

Se você escolheu o [acesso de leitura geográfica - redundantes (ar-GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (recomendado) para suas contas de armazenamento, você terá acesso de leitura aos seus dados da região secundário. Você pode usar ferramentas como [AzCopy](storage-use-azcopy.md) [PowerShell do Azure](storage-powershell-guide-full.md)e a [biblioteca de movimentação de dados do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) para copiar os dados da região secundário para outra conta de armazenamento em uma região unimpacted e aponte seus aplicativos para essa conta de armazenamento para ambos ler e gravar disponibilidade.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>O que esperar se ocorrer um failover de armazenamento

Se você escolheu [armazenamento geográfica redundantes (GRS)](storage-redundancy.md#geo-redundant-storage) ou [acesso de leitura geográfica - redundantes (ar-GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (recomendado), o armazenamento do Azure manterá seus dados durável em duas regiões (primário e secundário). Em ambas as regiões, o armazenamento do Azure constantemente mantém várias réplicas dos seus dados.

Quando um desastre regional afeta sua região primária, nós primeiro tentará restaurar o serviço nessa região. Depende da natureza de desastre e seus impactos, em algumas ocasiões raros talvez não seja possível restaurar a região principal. Neste ponto, podemos executará um failover de localização geográfica. A replicação de dados entre região é um processo assíncrono que pode envolver um atraso, portanto, é possível que as alterações que ainda não foram replicadas para a região secundária podem ser perdidas. Você pode consultar a ["hora da última sincronização" da sua conta de armazenamento](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) para obter detalhes sobre o status da replicação.

Alguns pontos sobre a experiência de localização geográfica failover de armazenamento:

-   Armazenamento geográfica failover só será acionada pela equipe de armazenamento do Azure – não há nenhuma ação de cliente necessária.

-   Seus pontos de extremidade de serviço armazenamento existente para blobs, tabelas, filas e arquivos permanecerá igual após o failover; a entrada DNS precisa ser atualizado para alternar da região primário para a região secundária.

-   Antes e durante a localização geográfica-tolerância a falhas, você não tem acesso de gravação que sua conta de armazenamento devido ao impacto do desastre, mas você ainda poderá ler o secundário se sua conta de armazenamento tiver sido configurada como ar GRS.

-   Quando a localização geográfica failover foi concluída e as alterações DNS propagado, o acesso de leitura e gravação à sua conta de armazenamento serão retomados. Você pode consultar ["última localização geográfica Failover hora" da sua conta de armazenamento](https://msdn.microsoft.com/library/azure/ee460802.aspx) para obter mais detalhes.

-   Após o failover, sua conta de armazenamento será totalmente funcional, mas em um status "degradado", como ele é realmente hospedada em uma região autônoma com nenhum possíveis de replicação geográfica. Para reduzir esse risco, abordaremos restaurar região primária original e execute um failback de localização geográfica para restaurar o estado original. Se a região primária original for irrecuperável, podemos será alocar outra região secundário.
Para obter mais detalhes sobre a infraestrutura de replicação de localização geográfica do armazenamento do Azure, consulte o artigo no blog da equipe de armazenamento sobre [Opções de redundância e ar GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

##<a name="best-practices-for-protecting-your-data"></a>Práticas recomendadas para proteger seus dados

Há algumas abordagens recomendadas para fazer backup de seus dados de armazenamento regularmente.

-   Discos de máquina virtual – usar o [serviço de Backup do Azure](https://azure.microsoft.com/services/backup/) para fazer backup dos discos de máquina virtual usados pelo suas máquinas virtuais Azure.

-   Bloco blobs – criar um [instantâneo](https://msdn.microsoft.com/library/azure/hh488361.aspx) de cada blob de bloco ou copiar os blobs ao armazenamento de outra conta em outro região usando [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)ou a [biblioteca de movimentação de dados do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).

-   Tabelas – use [AzCopy](storage-use-azcopy.md) para exportar os dados da tabela para outra conta de armazenamento em outra região.

-   Arquivos – use [AzCopy](storage-use-azcopy.md) ou [PowerShell do Azure](storage-powershell-guide-full.md) para copiar seus arquivos para outra conta de armazenamento em outra região.
