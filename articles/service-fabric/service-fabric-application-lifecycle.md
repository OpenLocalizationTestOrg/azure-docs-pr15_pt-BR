<properties
   pageTitle="Ciclo de vida do aplicativo no serviço tecidos | Microsoft Azure"
   description="Descreve desenvolvendo, implantando, testes, atualização, manter e remover aplicativos de serviço tecidos."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>


<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# <a name="service-fabric-application-lifecycle"></a>Ciclo de vida de aplicativo de serviço tecidos
Como com outras plataformas, um aplicativo no Azure serviço tecidos geralmente percorre as fases a seguir: design, desenvolvimento, teste, implantação, atualização, manutenção e remoção. Serviço tecidos fornece suporte de primeira classe para o ciclo de vida do aplicativo completo de aplicativos de nuvem, de desenvolvimento por meio de implantação, gerenciamento diário e manutenção para encerrar o eventual. O modelo de serviço permite várias funções diferentes participar de maneira independente do ciclo de vida do aplicativo. Este artigo fornece uma visão geral das APIs e como elas são usadas pelas funções diferentes em todas as fases do ciclo de vida de aplicativo de serviço tecidos.

## <a name="service-model-roles"></a>Funções do modelo de serviço
As funções de modelo de serviço são:

- **Desenvolvedor do serviço**: desenvolve modulares e genéricos serviços que podem ser usados em vários aplicativos do mesmo tipo ou diferentes tipos e redefinidos. Por exemplo, um serviço de fila pode ser usado para a criação de um aplicativo de tíquetes (suporte técnico) ou um aplicativo de comércio (carrinho de compras).

- **Desenvolvedor do aplicativo**: cria aplicativos ao integrar um conjunto de serviços para atender a determinadas necessidades específicas ou cenários. Por exemplo, um site de comércio pode integrar "JSON serviço front-end sem estado", "leilão com informações de estado" e "Fila estado serviço" criar uma solução auctioning.

- **Administrador do aplicativo**: toma decisões sobre a configuração de aplicativo (preenchendo os parâmetros de modelo de configuração), a implantação (mapeamento de recursos disponíveis) e a qualidade do serviço. Por exemplo, um administrador de aplicativo decide a localidade do idioma (inglês para os Estados Unidos) ou japonês para Japão, por exemplo do aplicativo. Um aplicativo implantado diferentes pode ter diferentes configurações.

- **Operador**: implanta requisitos especificados pelo administrador do aplicativo e aplicativos com base na configuração do aplicativo. Por exemplo, um operador provisiona e implanta o aplicativo e garante que ele seja executado no Azure. Operadores monitoram as informações de integridade e o desempenho do aplicativo e mantenham a infraestrutura física conforme necessário.


## <a name="develop"></a>Desenvolver
1. Um *desenvolvedor de serviço* desenvolve tipos diferentes de serviços usando o modelo de programação [Atores confiável](service-fabric-reliable-actors-introduction.md) ou [Serviços confiáveis](service-fabric-reliable-services-introduction.md) .
2. Um *desenvolvedor de serviço de* maneira declarativa descreve os tipos de serviço desenvolvida em um arquivo de manifesto de serviço consiste em um ou mais pacotes de código, configuração e dados.
3. Um *desenvolvedor de aplicativos* cria um aplicativo usando diferentes tipos de serviço.
4. Um *desenvolvedor de aplicativos* de maneira declarativa descreve o tipo de aplicativo em um manifesto de aplicativo referenciando os manifestos de serviço dos componentes serviços e adequadamente substituindo e parametrização diferentes configurações de configuração e implantação dos serviços de componentes.

Consulte [Introdução ao atores confiável](service-fabric-reliable-actors-get-started.md) e [começar a usar serviços confiáveis](service-fabric-reliable-services-quick-start.md) para obter exemplos.

## <a name="deploy"></a>Implantar
1. Um *administrador do aplicativo* adapta o tipo de aplicativo a um aplicativo específico a ser implantado para um cluster de estrutura de serviço especificando os parâmetros apropriados do elemento **ApplicationType** no manifesto do aplicativo.

2. Um *operador* carrega o pacote de aplicativos para o armazenamento de imagens de cluster usando o [método de **CopyApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) ou o [cmdlet **ServiceFabricApplicationPackage de cópia** ](https://msdn.microsoft.com/library/azure/mt125905.aspx). O pacote de aplicativo contém o manifesto do aplicativo e a coleção de pacotes de serviço. Serviço tecidos implanta aplicativos do pacote de aplicativo armazenadas no repositório imagem, que pode ser um armazenamento de blob do Microsoft Azure ou o serviço do sistema serviço tecidos.

3. O *operador* provisiona, em seguida, o tipo de aplicativo do cluster de destino do pacote de aplicativo carregados usando o [método **ProvisionApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), o [cmdlet **Register-ServiceFabricApplicationType** ](https://msdn.microsoft.com/library/azure/mt125958.aspx)ou a [operação de **provisionar um aplicativo** restante](https://msdn.microsoft.com/library/azure/dn707672.aspx).

4. Após a configuração do aplicativo, um *operador* inicia o aplicativo com os parâmetros fornecidos pelo *administrador do aplicativo* usando o [método **CreateApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync.aspx), o [cmdlet **New-ServiceFabricApplication** ](https://msdn.microsoft.com/library/azure/mt125913.aspx)ou a [operação de **Criar aplicativo** restante](https://msdn.microsoft.com/library/azure/dn707676.aspx).

5. Após o aplicativo ter sido implantado, um *operador* usa o [método **CreateServiceAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.createserviceasync.aspx), o [cmdlet **New-ServiceFabricService** ](https://msdn.microsoft.com/library/azure/mt125874.aspx)ou a [operação de **Serviço criar** restante](https://msdn.microsoft.com/library/azure/dn707657.aspx) para criar novas instâncias de serviço para o aplicativo com base nos tipos de serviço disponíveis.

6. O aplicativo agora é executado no cluster tecidos de serviço.

Consulte [implantar um aplicativo](service-fabric-deploy-remove-applications.md) para obter exemplos.

## <a name="test"></a>Teste
1. Após implantar o cluster de desenvolvimento local ou um cluster de teste, um *desenvolvedor de serviço* executa o cenário de teste de failover interna, usando as classes [**FailoverTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenarioparameters.aspx) e [**FailoverTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenario.aspx) ou o [cmdlet **Chamar ServiceFabricFailoverTestScenario** ](https://msdn.microsoft.com/library/azure/mt644783.aspx). O cenário de teste de failover executa um serviço especificado transições importantes e failovers para garantir que ele seja ainda disponíveis e trabalhando.

2. O *desenvolvedor do serviço* executa o cenário de teste de caos internos usando as classes [**ChaosTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenarioparameters.aspx) e [**ChaosTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenario.aspx) ou o [cmdlet de **Chamar ServiceFabricChaosTestScenario** ](https://msdn.microsoft.com/library/azure/mt644774.aspx). O cenário de teste caos aleatoriamente induz vários nó, pacote de código e falhas de réplica em cluster.

3. *Desenvolvedor de serviço de* [comunicação de serviço para testes](service-fabric-testability-scenarios-service-communication.md) criando cenários de teste que mover réplicas primárias ao redor de cluster.

Para obter mais informações, consulte [Introdução ao serviço de análise de falhas](service-fabric-testability-overview.md) .

## <a name="upgrade"></a>Atualizar
1. Um *desenvolvedor de serviço* atualiza os serviços partes do aplicativo instâncias e/ou correções de bugs e fornece uma nova versão do manifesto do serviço.

2. Um *desenvolvedor de aplicativos* substitui e parameterizes as configurações de configuração e implantação dos serviços consistentes e fornece uma nova versão do manifesto do aplicativo. Em seguida, o desenvolvedor do aplicativo incorpora as novas versões dos manifestos serviço do aplicativo e fornece uma nova versão do tipo de aplicativo em um pacote de aplicativo atualizado.

3. Um *administrador do aplicativo* incorpora a nova versão do tipo de aplicativo o aplicativo de destino atualizando os parâmetros apropriados.

5. Um *operador* carrega o pacote de aplicativo atualizado para o armazenamento de imagens de cluster usando o [método de **CopyApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) ou o [cmdlet **ServiceFabricApplicationPackage de cópia** ](https://msdn.microsoft.com/library/azure/mt125905.aspx). O pacote de aplicativo contém o manifesto do aplicativo e a coleção de pacotes de serviço.

6. Um *operador* provisiona a nova versão do aplicativo do cluster de destino, usando o [método **ProvisionApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), o [cmdlet **Register-ServiceFabricApplicationType** ](https://msdn.microsoft.com/library/azure/mt125958.aspx)ou a [operação de **provisionar um aplicativo** restante](https://msdn.microsoft.com/library/azure/dn707672.aspx).

7. Um *operador* atualiza o aplicativo de destino para a nova versão usando o [método **UpgradeApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.upgradeapplicationasync.aspx), o [cmdlet **Start-ServiceFabricApplicationUpgrade** ](https://msdn.microsoft.com/library/azure/mt125975.aspx)ou a [operação de **atualizar um aplicativo** restante](https://msdn.microsoft.com/library/azure/dn707633.aspx).

8. Um *operador* verifica o andamento da atualização usando o [método **GetApplicationUpgradeProgressAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.getapplicationupgradeprogressasync.aspx), o [cmdlet **Get-ServiceFabricApplicationUpgrade** ](https://msdn.microsoft.com/library/azure/mt125988.aspx)ou a [operação de **Andamento de atualização de aplicativo obter** restante](https://msdn.microsoft.com/library/azure/dn707631.aspx).

9. Se necessário, o *operador* modifica e reaplica os parâmetros da atualização do aplicativo atual usando o [método **UpdateApplicationUpgradeAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.updateapplicationupgradeasync.aspx), o [cmdlet **Update-ServiceFabricApplicationUpgrade** ](https://msdn.microsoft.com/library/azure/mt126030.aspx)ou a [operação de **Atualização do aplicativo de atualização** restante](https://msdn.microsoft.com/library/azure/mt628489.aspx).

10. Se necessário, o *operador* desfaz a atualização do aplicativo atual usando o [método **RollbackApplicationUpgradeAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.rollbackapplicationupgradeasync.aspx), o [cmdlet **Start-ServiceFabricApplicationRollback** ](https://msdn.microsoft.com/library/azure/mt125833.aspx)ou a [operação de **Atualização de aplicativo com a reversão** restante](https://msdn.microsoft.com/library/azure/mt628494.aspx).

11. Serviço tecidos atualiza o aplicativo de destino em execução no cluster sem perder a disponibilidade de qualquer um dos seus serviços de componentes.

Consulte o [tutorial de atualização do aplicativo](service-fabric-application-upgrade-tutorial.md) para obter exemplos.

## <a name="maintain"></a>Manter
1. Obter atualizações do sistema operacional e correções, serviço tecidos interfaces com infraestrutura do Azure para garantir a disponibilidade de todos os aplicativos executados no cluster.

2. Para atualizações e patches para a plataforma de serviço tecidos, serviço tecidos atualiza os próprio sem perder a disponibilidade de qualquer um dos aplicativos em execução no cluster.

3. Um *administrador do aplicativo* aprova a adição ou remoção de nós de um cluster após analisar dados de utilização de capacidade histórica e projetada demanda futura.

4. Um *operador* adiciona e remove nós especificado pelo *administrador do aplicativo*.

5. Quando novos nós são adicionados à ou nós existentes são removidos do cluster, serviço tecidos equilibra automaticamente a carga os aplicativos em execução em todos os nós no cluster para atingir o desempenho ideal.

## <a name="remove"></a>Remover
1. Um *operador* pode excluir uma instância específica de um serviço em execução no cluster sem remover o aplicativo inteiro usando o [método **DeleteServiceAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync.aspx), o [cmdlet **Remove-ServiceFabricService** ](https://msdn.microsoft.com/library/azure/mt126033.aspx)ou a [operação de **Serviço excluir** restante](https://msdn.microsoft.com/library/azure/dn707687.aspx).  

2. Um *operador* também pode excluir uma instância de aplicativo e todos os seus serviços usando o [método **DeleteApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync.aspx), o [cmdlet **Remove-ServiceFabricApplication** ](https://msdn.microsoft.com/library/azure/mt125914.aspx)ou a [operação de **Aplicativo excluir** restante](https://msdn.microsoft.com/library/azure/dn707651.aspx).

3. Depois que os aplicativos e serviços foram interrompidas, o *operador* pode remover o provisionamento o tipo de aplicativo usando o [método **UnprovisionApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync.aspx), o [cmdlet **ServiceFabricApplicationType cancelar registro** ](https://msdn.microsoft.com/library/azure/mt125885.aspx)ou a [operação de REST **desconfiguração um aplicativo** ](https://msdn.microsoft.com/library/azure/dn707671.aspx). Desconfiguração o tipo de aplicativo não remova o pacote de aplicativo a ImageStore. Você deve remover manualmente o pacote de aplicativos.

4. Um *operador* remove o ImageStore usando o [método de **RemoveApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage.aspx) ou o [cmdlet **ServiceFabricApplicationPackage remover** ](https://msdn.microsoft.com/library/azure/mt163532.aspx)o pacote de aplicativos.

Consulte [implantar um aplicativo](service-fabric-deploy-remove-applications.md) para obter exemplos.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como desenvolver, teste e gerenciar aplicativos de serviço tecidos e serviços, consulte:

- [Atores confiáveis](service-fabric-reliable-actors-introduction.md)
- [Serviços confiáveis](service-fabric-reliable-services-introduction.md)
- [Implantar um aplicativo](service-fabric-deploy-remove-applications.md)
- [Atualização do aplicativo](service-fabric-application-upgrade.md)
- [Visão geral de capacidade de teste](service-fabric-testability-overview.md)
- [Exemplo de ciclo de vida do aplicativo baseado em REST](service-fabric-rest-based-application-lifecycle-sample.md)
