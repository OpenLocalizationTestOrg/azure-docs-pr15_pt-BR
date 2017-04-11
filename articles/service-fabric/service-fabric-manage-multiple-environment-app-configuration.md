<properties
   pageTitle="Gerenciar vários ambientes em estrutura de serviço | Microsoft Azure"
   description="Aplicativos de serviço tecidos podem ser executados em clusters que variam de tamanho de um computador para milhares de máquinas. Em alguns casos, você desejará configurar seu aplicativo de forma diferente para esses ambientes variados. Este artigo aborda como definir parâmetros de aplicativo diferente por ambiente."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/19/2016"
   ms.author="seanmck"/>

# <a name="manage-application-parameters-for-multiple-environments"></a>Gerenciar parâmetros de aplicativo para vários ambientes

Você pode criar clusters de estrutura de serviço do Azure usando qualquer lugar de um a milhares de máquinas. Enquanto binários do aplicativo podem ser executado sem modificação entre essa ampla variedade de ambientes, muitas vezes você desejará configurar o aplicativo de maneira diferente, dependendo do número de máquinas que você está implantando.

Como um exemplo simples, considere `InstanceCount` para um serviço sem estado. Quando você estiver executando aplicativos no Azure, geralmente você desejará definir este parâmetro como o valor especial de "-1". Isso garante que o seu serviço está em execução em cada nó no cluster. No entanto, essa configuração não é adequada para um cluster de computador único porque você não pode ter vários processos listening na mesma empresa em uma única máquina. Em vez disso, você normalmente definirá `InstanceCount` para "1".

## <a name="specifying-environment-specific-parameters"></a>Especificação de parâmetros específicos do ambiente

A solução para este problema de configuração é um conjunto de serviços padrão com parâmetros e arquivos de parâmetro de aplicativo que preencham esses valores de parâmetro para um determinado ambiente. Parâmetros de aplicativos e serviços padrão são configurados nos manifestos de aplicativo e serviços. A definição de esquema para os arquivos ServiceManifest.xml e ApplicationManifest.xml é instalada com o serviço tecidos SDK e ferramentas para *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

### <a name="default-services"></a>Serviços padrão

Aplicativos de serviço tecidos são compostos de uma coleção de instâncias do serviço. Embora seja possível para você criar um aplicativo vazio e, em seguida, criar dinamicamente todas as instâncias de serviço, a maioria dos aplicativos têm um conjunto de serviços básicos que sempre devem ser criados quando o aplicativo é instanciado. Estes são denominados "serviços padrão". Eles são especificados no manifesto do aplicativo, com espaços reservados para configuração de per ambiente incluído entre colchetes:

    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type"
                TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]"
                MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]"
                    LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>

Cada um dos parâmetros nomeados deve ser definida dentro do elemento de parâmetros de manifesto do aplicativo:

    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>

O atributo DefaultValue Especifica o valor a ser usado na ausência de um parâmetro mais específicas para um determinado ambiente.

>[AZURE.NOTE] Nem todos os parâmetros de instância do serviço são adequados para configuração per ambiente. No exemplo acima, os valores LowKey e HighKey para o esquema de partição do serviço explicitamente são definidos para todas as instâncias do serviço como o intervalo de partição é uma função do domínio dados, não o ambiente.


### <a name="per-environment-service-configuration-settings"></a>Configurações de serviço por ambiente

O [modelo de aplicativo de serviço tecidos](service-fabric-application-model.md) habilita serviços incluir pacotes de configuração que contêm pares de chave-valor personalizados que podem ser lidos em tempo de execução. Os valores dessas configurações também podem ser diferenciados por ambiente especificando uma `ConfigOverride` no manifesto do aplicativo.

Suponha que você tenha a seguinte configuração no arquivo de Config\Settings.xml a `Stateful1` serviço:


    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

Para substituir esse valor para um par de aplicativo/ambiente específico, crie um `ConfigOverride` quando você importa o manifesto de serviço no manifesto do aplicativo.

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>

Este parâmetro, em seguida, pode ser configurado pelo ambiente conforme mostrado acima. Você pode fazer isso declarando-o na seção parâmetros de manifesto do aplicativo e especificando valores específicos do ambiente nos arquivos de parâmetro do aplicativo.

>[AZURE.NOTE] No caso de configurações de serviço, há três locais onde o valor de uma chave pode ser definido: o pacote de configuração do serviço, o manifesto do aplicativo e o arquivo de parâmetros de aplicativo. Serviço tecidos sempre irá escolher o parâmetro do arquivo de aplicativo primeiro (se especificada), em seguida, o manifesto do aplicativo e, por fim, o pacote de configuração.


### <a name="application-parameter-files"></a>Arquivos de parâmetro de aplicativo

O projeto de aplicativo de serviço tecidos pode incluir um ou mais arquivos de parâmetro de aplicativo. Cada um deles define os valores específicos para os parâmetros definidos no manifesto do aplicativo:

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

Por padrão, um novo aplicativo inclui dois arquivos de parâmetro de aplicativo, chamados Local.xml e Cloud.xml:

![Arquivos de parâmetro de aplicativo no Solution Explorer][app-parameters-solution-explorer]

Para criar um novo arquivo de parâmetro, basta copiar e colar uma já existente e dar a ele um novo nome.

## <a name="identifying-environment-specific-parameters-during-deployment"></a>Identificando parâmetros específicos de ambiente durante a implantação

No momento da implantação, você precisa escolher o arquivo de parâmetro apropriado para aplicar com seu aplicativo. Você pode fazer isso por meio de caixa de diálogo Publicar no Visual Studio ou PowerShell.

### <a name="deploy-from-visual-studio"></a>Implantar do Visual Studio

Você pode escolher na lista de arquivos de parâmetros disponíveis quando você publica seu aplicativo no Visual Studio.

![Escolher um arquivo de parâmetro na caixa de diálogo Publicar][publishdialog]

### <a name="deploy-from-powershell"></a>Implantar do PowerShell

O `Deploy-FabricApplication.ps1` script do PowerShell incluído no modelo de projeto aplicativo aceita um perfil de publicação como um parâmetro e o PublishProfile contém uma referência para o arquivo de parâmetros de aplicativo.

  ```PowerShell
    ./Deploy-FabricApplication -ApplicationPackagePath <app_package_path> -PublishProfileFile <publishprofile_path>
  ```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre alguns dos principais conceitos descritos neste tópico, consulte [Visão geral técnica do serviço tecidos](service-fabric-technical-overview.md). Para obter informações sobre outros recursos de gerenciamento de aplicativo que estão disponíveis no Visual Studio, consulte [gerenciar seus aplicativos de serviço tecidos no Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
