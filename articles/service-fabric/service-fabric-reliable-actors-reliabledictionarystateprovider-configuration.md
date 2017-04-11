<properties
   pageTitle="Visão geral da configuração do Azure serviço tecidos confiável atores ReliableDictionaryActorStateProvider | Microsoft Azure"
   description="Saiba como configurar atores estados do Azure tecidos de serviço do tipo ReliableDictionaryActorStateProvider."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/18/2016"
   ms.author="sumukhs"/>

# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Configurando atores confiáveis – ReliableDictionaryActorStateProvider
Você pode modificar a configuração padrão do ReliableDictionaryActorStateProvider alterando o arquivo Settings. XML gerado na raiz do pacote do Visual Studio sob a pasta de configuração para o ator especificado.

O tempo de execução do Azure serviço tecidos procura nomes de seção predefinida no arquivo Settings. XML e consome os valores de configuração durante a criação de componentes de tempo de execução subjacentes.

>[AZURE.NOTE] Eu **não** excluir ou modificar os nomes de seção das configurações a seguir no arquivo Settings. XML que é gerado na solução Visual Studio.

Também existem configurações globais que afetam a configuração de ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Configuração global

A configuração global é especificada no manifesto do cluster para o cluster na seção KtlLogger. Ele permite a configuração da localização do log compartilhado e tamanho além dos limites de memória global usados pelo agente de log. Observe que as alterações no manifesto cluster afetam todos os serviços que usam ReliableDictionaryActorStateProvider e serviços de estado confiáveis.

O manifesto de cluster é um único arquivo XML que contém definições e configurações que se aplicam a todos os nós e serviços no cluster. O arquivo normalmente é chamado ClusterManifest.xml. Você pode ver o cluster manifesto para o seu cluster usando o comando Get-ServiceFabricClusterManifest do powershell.

### <a name="configuration-names"></a>Nomes de configuração

|Nome|Unidade|Valor padrão|Comentários|
|----|----|-------------|-------|
|WriteBufferMemoryPoolMinimumInKB|Quilobytes.|8388608|Número mínimo de KB alocar no modo de núcleo para o pool de memória de buffer de gravação do agente de log. Este pool de memória é usada para armazenar em cache informações de estado antes de gravar em disco.|
|WriteBufferMemoryPoolMaximumInKB|Quilobytes.|Sem limite|Tamanho máximo para o qual o agente de log escrever pool de memória de buffer pode crescer.|
|SharedLogId|GUID|""|Especifica um GUID exclusivo serem usados para identificar o arquivo de log compartilhado padrão usado por todos os serviços confiáveis em todos os nós no cluster que não especificar o SharedLogId em suas configurações específicas do serviço. Se SharedLogId for especificada, SharedLogPath também deve ser especificado.|
|SharedLogPath|Nome do caminho totalmente qualificado|""|Especifica o caminho totalmente qualificado onde o arquivo de log compartilhado usado por todos os serviços confiáveis em todos os nós no cluster que não especificar o SharedLogPath em suas configurações específicas do serviço. No entanto, se SharedLogPath for especificado, em seguida, SharedLogId também deve ser especificado.|
|SharedLogSizeInMB|Megabytes|8192|Especifica o número de MB de espaço em disco estática alocar para o log de compartilhado. O valor deve ser 2048 ou maior.|

### <a name="sample-cluster-manifest-section"></a>Seção de manifesto de cluster de amostra
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Comentários
Agente de log tem um pool global de memória alocada da memória de núcleo não-paginável que está disponível para todos os serviços confiáveis em um nó do cache de dados de estado antes que estão sendo gravados no log de dedicado associados a réplica de serviço confiável. O tamanho do pool é controlado pelas configurações WriteBufferMemoryPoolMinimumInKB e WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB Especifica o tamanho inicial do pool de memória e o tamanho menor para o qual o pool de memória pode reduzir. WriteBufferMemoryPoolMaximumInKB é o tamanho maior para o qual o pool de memória pode crescer. Cada réplica de serviço confiável que é aberta pode aumentar o tamanho do pool de memória por um valor de sistema determinado até WriteBufferMemoryPoolMaximumInKB. Se houver demanda mais de memória do pool de memória que está disponível, solicitações de memória serão atrasadas até que a memória está disponível. Portanto, se o pool de memória de buffer de gravação é muito pequeno para uma configuração específica desempenho pode sofrer.

As configurações de SharedLogId e SharedLogPath sempre são usadas juntos para definir o GUID e local para o log de compartilhada padrão para todos os nós no cluster. O log de compartilhada padrão é usado para todos os serviços confiáveis que não especificar as configurações a Settings. XML para o serviço específico. Para obter melhor desempenho, arquivos de log compartilhados devem ser colocados em discos que são usados apenas para o arquivo de log compartilhada para reduzir disputa.

SharedLogSizeInMB Especifica a quantidade de espaço em disco para pré-alocar para o log de compartilhada padrão em todos os nós.  SharedLogId e SharedLogPath não precisam ser especificados em ordem para SharedLogSizeInMB deve ser especificado.

## <a name="replicator-security-configuration"></a>Configuração de segurança do Replicator
Configurações de segurança do Replicator são usadas para proteger o canal de comunicação que é usado durante a replicação. Isso significa que os serviços não consegue ver uns dos outros tráfego de replicação, que garante que os dados que são disponibilizados altamente também são seguros.
Por padrão, uma seção de configuração de segurança vazios impede que a segurança de replicação.

### <a name="section-name"></a>Nome da seção
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuração de Replicator
Configurações de Replicator são usadas para configurar o replicator que é responsável por fazer com que o estado de provedor de estado de ator altamente confiável por replicar e manter o estado localmente.
A configuração padrão é gerada pelo modelo do Visual Studio e deve ser suficiente. Esta seção fala sobre configurações adicionais que estão disponíveis para ajustar o replicator.

### <a name="section-name"></a>Nome da seção
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nomes de configuração

|Nome|Unidade|Valor padrão|Comentários|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Segundos|0.015|Período de tempo para que o replicador no aguarda secundário após receber uma operação antes de enviar novamente uma confirmação no principal. Quaisquer outras confirmações sejam enviadas para operações processadas dentro deste intervalo são enviadas como uma resposta.||
|ReplicatorEndpoint|N/D|Nenhum padrão – parâmetro obrigatório|Endereço IP e porta que o replicator primário/secundário usará para se comunicar com outros replicadores na réplica definem. Isso deve fazer referência a um ponto de extremidade do recurso TCP no manifesto do serviço. Consulte [recursos de manifesto de serviço](service-fabric-service-manifest-resources.md) para ler mais sobre como definir os recursos de ponto de extremidade no manifesto de serviço. |
|MaxReplicationMessageSize|Bytes|50 MB|Tamanho máximo de dados de replicação que podem ser transmitidos em uma única mensagem.|
|MaxPrimaryReplicationQueueSize|Número de operações|8192|Número máximo de operações na fila principal. Uma operação não precisa mais após o replicator primário recebe uma confirmação de todos os replicadores secundários. Este valor deve ser maior que 64 e uma potência de 2.|
|MaxSecondaryReplicationQueueSize|Número de operações|16384|Número máximo de operações na fila secundária. Uma operação não precisa mais após fazer seu estado altamente disponível por meio de persistência. Este valor deve ser maior que 64 e uma potência de 2.|
|CheckpointThresholdInMB|MB|200|Quantidade de espaço de arquivo de log após o qual o estado é marcado.|
|MaxRecordSizeInKB|KB|1024|Maior tamanho de registro que o replicator pode gravar no log. Este valor deve ser um múltiplo de 4 e maiores que 16.|
|OptimizeLogForLowerDiskUsage|Booliano|verdadeiro|Quando for verdadeiro, o log está configurado para que o arquivo de log dedicado da réplica é criado usando um arquivo esparso NTFS. Isso reduz o uso do espaço de disco real para o arquivo. Quando for falso, o arquivo é criado com alocações fixas, que fornecem que o melhor desempenho de gravação.|
|SharedLogId|GUID|""|Especifica um guid exclusivo serem usados para identificar o arquivo de log compartilhado usado com esta réplica. Normalmente, os serviços não devem usar essa configuração. No entanto, se SharedLogId for especificado, em seguida, SharedLogPath também deve ser especificado.|
|SharedLogPath|Nome do caminho totalmente qualificado|""|Especifica o caminho totalmente qualificado onde o arquivo de log compartilhada desta réplica será criado. Normalmente, os serviços não devem usar essa configuração. No entanto, se SharedLogPath for especificado, em seguida, SharedLogId também deve ser especificado.|


## <a name="sample-configuration-file"></a>Arquivo de configuração de exemplo

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```

## <a name="remarks"></a>Comentários
A latência de replicação de controles de parâmetro BatchAcknowledgementInterval. Um valor de '0' resulta em menor latência possível, ao custo de produtividade (como mensagens de confirmação de mais devem ser enviadas e processadas, cada um contendo confirmações menos).
Quanto maior o valor BatchAcknowledgementInterval, quanto maior a replicação produtividade geral, ao custo mais alta latência de operação. Isso se traduz diretamente para a latência de confirmações de transações.

O parâmetro CheckpointThresholdInMB controla a quantidade de espaço de disco que o replicador pode usar para armazenar informações de estado no arquivo de log da réplica dedicado. Aumentar isso para um valor maior que o padrão pode resultar em tempos de reconfiguração quando uma nova réplica é adicionada ao conjunto. Isso é devido a transferência de estado parcial que ocorrerá devido a disponibilidade de histórico mais de operações no log. Potencialmente, isso pode aumentar o tempo de recuperação de uma réplica após uma falha.

Se você definir OptimizeForLowerDiskUsage como true, espaço de arquivo de log será provisionamento excessivo para que cópias ativas podem armazenar informações de estado de mais em seus arquivos de log, enquanto réplicas inativas usará menos espaço em disco. Isso torna possível hospedar mais réplicas em um nó. Se você definir OptimizeForLowerDiskUsage como false, as informações de estado escritas aos arquivos de log mais rapidamente.

A configuração de MaxRecordSizeInKB define o tamanho máximo de um registro que pode ser gravado pelo replicator no arquivo de log. Na maioria dos casos, o tamanho de 1024 KB registro padrão é ideal. No entanto, se o serviço está causando itens maiores de dados sejam parte das informações de estado, em seguida, esse valor talvez precise ser aumentado. Há pouco benefício em tornar MaxRecordSizeInKB menor que 1024, como registros menores usam somente o espaço necessário para o registro menor. Esperamos que esse valor precisaria ser alterada somente em alguns casos raros.

As configurações de SharedLogId e SharedLogPath sempre são usadas juntos para garantir um serviço usar um log de compartilhada separado do log compartilhada padrão para o nó. Para melhor eficiência, como muitos serviços possível devem especificar o mesmo log compartilhado. Arquivos de log compartilhados devem ser colocados em discos que são usados apenas para o arquivo de log compartilhado, reduzir disputa de movimentação de cabeça. Esperamos que esses valores precisaria ser alterada somente em alguns casos raros.
