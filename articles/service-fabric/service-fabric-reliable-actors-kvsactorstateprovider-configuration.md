<properties
   pageTitle="Visão geral da configuração do Azure serviço tecidos confiável atores KVSActorStateProvider | Microsoft Azure"
   description="Saiba como configurar atores estados do Azure tecidos de serviço do tipo KVSActorStateProvider."
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
   ms.date="09/20/2016"
   ms.author="sumukhs"/>

# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Configurando atores confiáveis – KVSActorStateProvider
Você pode modificar a configuração padrão do KVSActorStateProvider alterando o arquivo Settings. XML que é gerado na raiz do pacote do Microsoft Visual Studio sob a pasta de configuração para o ator especificado.

O tempo de execução do Azure serviço tecidos procura nomes de seção predefinida no arquivo Settings. XML e consome os valores de configuração durante a criação de componentes de tempo de execução subjacentes.

>[AZURE.NOTE] Eu **não** excluir ou modificar os nomes de seção das configurações a seguir no arquivo Settings. XML que é gerado na solução Visual Studio.

## <a name="replicator-security-configuration"></a>Configuração de segurança do Replicator
Configurações de segurança do Replicator são usadas para proteger o canal de comunicação que é usado durante a replicação. Isso significa que os serviços não consegue ver uns dos outros tráfego de replicação, garantindo que os dados que são disponibilizados altamente também são seguros.
Por padrão, uma seção de configuração de segurança vazios impede que a segurança de replicação.

### <a name="section-name"></a>Nome da seção
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuração de Replicator
Configurações de Replicator Configurar o replicator que é responsável por tornando o estado de provedor de estado de ator altamente confiável.
A configuração padrão é gerada pelo modelo do Visual Studio e deve ser suficiente. Esta seção fala sobre configurações adicionais que estão disponíveis para ajustar o replicator.

### <a name="section-name"></a>Nome da seção
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nomes de configuração

|Nome|Unidade|Valor padrão|Comentários|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Segundos|0.015|Período de tempo para que o replicador no aguarda secundário após receber uma operação antes de enviar novamente uma confirmação no principal. Quaisquer outras confirmações sejam enviadas para operações processadas dentro deste intervalo são enviadas como uma resposta.|
|ReplicatorEndpoint|N/D|Nenhum padrão – parâmetro obrigatório|Endereço IP e porta que o replicator primário/secundário usará para se comunicar com outros replicadores na réplica definem. Isso deve fazer referência a um ponto de extremidade do recurso TCP no manifesto do serviço. Consulte [recursos de manifesto de serviço](service-fabric-service-manifest-resources.md) para ler mais sobre como definir os recursos de ponto de extremidade no manifesto do serviço. |
|RetryInterval|Segundos|5|Período de tempo após o qual o replicator novamente transmite uma mensagem se ele não receber uma confirmação para uma operação.|
|MaxReplicationMessageSize|Bytes|50 MB|Tamanho máximo de dados de replicação que podem ser transmitidos em uma única mensagem.|
|MaxPrimaryReplicationQueueSize|Número de operações|1024|Número máximo de operações na fila principal. Uma operação não precisa mais após o replicator primário recebe uma confirmação de todos os replicadores secundários. Este valor deve ser maior que 64 e uma potência de 2.|
|MaxSecondaryReplicationQueueSize|Número de operações|2048|Número máximo de operações na fila secundária. Uma operação não precisa mais após fazer seu estado altamente disponível por meio de persistência. Este valor deve ser maior que 64 e uma potência de 2.|

## <a name="store-configuration"></a>Configuração de armazenamento
Configurações do repositório são usadas para configurar o armazenamento local que é usado para manter o estado em que está sendo replicado.
A configuração padrão é gerada pelo modelo do Visual Studio e deve ser suficiente. Esta seção fala sobre configurações adicionais que estão disponíveis para ajustar o armazenamento local.

### <a name="section-name"></a>Nome da seção
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Nomes de configuração

|Nome|Unidade|Valor padrão|Comentários|
|----|----|-------------|-------|
|MaxAsyncCommitDelayInMilliseconds|Milissegundos|200|Define o máximo lotes intervalo para armazenamento local durável confirmações.|
|MaxVerPages|Número de páginas|16384|O número máximo de páginas de versão no local armazena banco de dados. Ele determina o número máximo de transações pendentes.|

## <a name="sample-configuration-file"></a>Arquivo de configuração de exemplo

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
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
