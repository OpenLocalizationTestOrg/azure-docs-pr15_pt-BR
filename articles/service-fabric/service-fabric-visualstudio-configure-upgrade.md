<properties
   pageTitle="Configurar a atualização de um aplicativo de serviço tecidos | Microsoft Azure"
   description="Saiba como definir as configurações para atualizar um aplicativo de serviço tecidos usando o Microsoft Visual Studio."
   services="service-fabric"
   documentationCenter="na"
   authors="cawaMS"
   manager="paulyuk"
   editor="tglee" />
<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/29/2016"
   ms.author="cawa" />

# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Configurar a atualização de um aplicativo de serviço tecidos no Visual Studio

O Visual Studio tools tecidos de serviço do Azure oferecem suporte atualização da publicação para clusters locais ou remotos. Há duas vantagens para atualizar seu aplicativo para uma versão mais recente em vez de substituir o aplicativo durante o teste e depuração:

- Dados de aplicativo não sejam perdidos durante a atualização.
- Disponibilidade permanece alta para que não haverá qualquer interrupção do serviço durante a atualização, se houver que suficientes instâncias de serviço distribuídas em domínios de atualização.

Testes podem ser executados em relação a um aplicativo enquanto ele está sendo atualizado.

## <a name="parameters-needed-to-upgrade"></a>Parâmetros necessários para atualizar

Você pode escolher entre dois tipos de implantação: regular ou atualização. Uma implantação normal apaga qualquer anterior informações de implantação e dados em cluster, enquanto uma implantação atualização preserva-lo. Quando você atualiza um aplicativo de serviço tecidos no Visual Studio, você precisa fornecer políticas de verificação de integridade e parâmetros de atualização do aplicativo. Parâmetros de atualização do aplicativo ajudam a controlar a atualização, enquanto as diretivas de verificação de integridade determinam se a atualização foi bem-sucedida. Consulte [atualização do aplicativo de serviço tecidos: atualizar os parâmetros](service-fabric-application-upgrade-parameters.md) para obter mais detalhes.

Há três modos de atualização: *monitorados*, *UnmonitoredAuto*e *UnmonitoredManual*.

  - Uma atualização de monitorados automatiza a atualização e verificação de integridade do aplicativo.

  - Uma atualização de UnmonitoredAuto automatiza a atualização, mas ignora a verificação de integridade do aplicativo.

  - Quando você fizer uma atualização UnmonitoredManual, você precisa atualizar manualmente cada domínio de atualização.

Cada modo de atualização requer diferentes conjuntos de parâmetros. Consulte [parâmetros de atualização do aplicativo](service-fabric-application-upgrade-parameters.md) para saber mais sobre as opções de atualização disponíveis.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Atualizar um aplicativo de serviço tecidos no Visual Studio

Se você estiver usando as ferramentas de estrutura de serviço do Visual Studio para atualizar um aplicativo de serviço tecidos, você pode especificar um processo de publicação para ser uma atualização em vez de uma implantação normal marcando a caixa de seleção **atualizar o aplicativo** .

### <a name="to-configure-the-upgrade-parameters"></a>Para configurar os parâmetros de atualização

1. Clique no botão **configurações** ao lado da caixa de seleção. Caixa de diálogo **Editar parâmetros de atualização** é exibida. Caixa de diálogo **Editar atualizar parâmetros** suporta os modos de atualização monitorados, UnmonitoredAuto e UnmonitoredManual.

2. Selecione o modo de atualização que você deseja usar e, em seguida, preencha a grade de parâmetro.

    Cada parâmetro tem os valores padrão. O parâmetro opcional *DefaultServiceTypeHealthPolicy* leva uma entrada de tabela de hash. Aqui está um exemplo de formato de entrada de tabela hash para *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* é outro parâmetro opcional que leva uma entrada de tabela de hash no seguinte formato:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Aqui está um exemplo da vida real:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```

3. Se você selecionar o modo de atualização de UnmonitoredManual, você deve iniciar manualmente um console do PowerShell para continuar e concluir o processo de atualização. Consulte [atualização do aplicativo de serviço tecidos: tópicos avançados](service-fabric-application-upgrade-advanced.md) para aprender a atualização manual como funciona.

## <a name="upgrade-an-application-by-using-powershell"></a>Atualizar um aplicativo usando o PowerShell

Você pode usar cmdlets do PowerShell para atualizar um aplicativo de serviço tecidos. Consulte o [aplicativo de serviço tecidos tutorial de atualização](service-fabric-application-upgrade-tutorial.md) e [Início-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/mt125975.aspx) para obter informações detalhadas.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Especificar uma política de verificação de integridade no arquivo de manifesto do aplicativo

Cada serviço em um aplicativo de serviço tecidos pode ter seus próprios parâmetros de política de integridade que substituem os valores padrão. Você pode fornecer esses valores de parâmetro no arquivo de manifesto do aplicativo.

O exemplo a seguir mostra como aplicar uma política de verificação de integridade exclusivo para cada serviço no manifesto do aplicativo.

```
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre como implantar um aplicativo, consulte [implantar um aplicativo existente em estrutura de serviço do Azure](service-fabric-deploy-existing-app.md).
