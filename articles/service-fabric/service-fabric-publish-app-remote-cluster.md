<properties
    pageTitle="Publicar um aplicativo em um cluster remoto com o Visual Studio | Microsoft Azure"
    description="Saiba como publicar um aplicativo para um cluster de estrutura de serviço remoto usando Visual Studio."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="07/29/2016"
    ms.author="cawa" />

# <a name="publish-an-application-to-a-remote-cluster-by-using-visual-studio"></a>Publicar um aplicativo para um cluster remoto, usando o Visual Studio

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-deploy-remove-applications.md)
- [O Visual Studio](service-fabric-publish-app-remote-cluster.md)

<br/>

Extensão da estrutura de serviço do Azure para Visual Studio oferece uma maneira fácil, repetitiva e programável para publicar um aplicativo para um cluster de estrutura de serviço.

## <a name="the-artifacts-required-for-publishing"></a>Os artefatos necessários para publicação

### <a name="deploy-fabricapplicationps1"></a>FabricApplication.ps1 implantar

Este é um script do PowerShell que usa um caminho de perfil de publicar como um parâmetro para publicando aplicativos de serviço tecidos. Como esse script é parte do seu aplicativo, você está bem-vindo ao modificá-lo conforme necessário para seu aplicativo.

### <a name="publish-profiles"></a>Perfis de publicação

Uma pasta do projeto de aplicativo de serviço tecidos chamado **PublishProfiles** contém arquivos XML que armazenam informações essenciais para publicar um aplicativo, tais como:

- Parâmetros de conexão do serviço tecidos cluster
- Caminho para um arquivo de parâmetro de aplicativo
- Configurações de atualização

Por padrão, o aplicativo incluirá dois perfis de publicação: Local.xml e Cloud.xml. Você pode adicionar mais perfis copiando e colando um dos arquivos padrão.

### <a name="application-parameter-files"></a>Arquivos de parâmetro de aplicativo

Uma pasta do projeto de aplicativo de serviço tecidos chamado **ApplicationParameters** contém arquivos XML para valores de parâmetro manifesto de aplicativo especificado pelo usuário. Arquivos de manifesto do aplicativo podem ser parametrizados para que você possa usar valores diferentes para as configurações de implantação. Para saber mais sobre parametrização seu aplicativo, consulte [gerenciar vários ambientes em estrutura de serviço](service-fabric-manage-multiple-environment-app-configuration.md).

>[AZURE.NOTE] Para serviços de ator, você deve criar o projeto antes de tentar editar o arquivo em um editor ou por meio da caixa de diálogo Publicar. Isso ocorre porque parte dos arquivos do manifesto será gerado durante a compilação.

## <a name="to-publish-an-application-by-using-the-publish-service-fabric-application-dialog-box"></a>Publicar um aplicativo usando a caixa de diálogo Publicar aplicativo de estrutura de serviço

As etapas a seguir demonstram como publicar um aplicativo usando a caixa de diálogo **Publicar aplicativo de serviço de tecidos** fornecida pelo Visual Studio serviço tecidos Tools.

1. No menu de atalho do projeto aplicativo de tecidos do serviço, escolha **Publicar...** Para exibir a caixa de diálogo **Publicar aplicativo de serviço de tecidos** .

    ![O * * a caixa de diálogo Publicar serviço tecidos aplicativo * *][0]

    O arquivo selecionado na caixa de lista suspensa de **perfil de destino** é onde todas as configurações, exceto **manifesto versões**, são salvos. Você pode reutilizar um perfil existente ou crie um novo escolhendo **< … para gerenciar perfis >** na caixa de lista suspensa de **perfil de destino** . Quando você escolhe um perfil de publicação, seu conteúdo aparece nos campos correspondentes da caixa de diálogo. Para salvar suas alterações a qualquer momento, escolha o link de **Salvar perfil** .    

2. Na seção de **ponto de extremidade de Conexão** , especifique publicação ponto de extremidade de um local ou remoto tecidos de serviço do cluster. Para adicionar ou alterar o ponto de extremidade de conexão, clique na lista suspensa de **Ponto de extremidade de Conexão** . A lista mostra o cluster de estrutura de serviço disponível conexão pontos de extremidade para o qual você pode publicar com base em suas inscrições Azure. Observe que se você não já efetuou Visual Studio, você será solicitado a fazer isso.

    Use a caixa de diálogo de seleção de cluster para escolher do conjunto de assinaturas disponíveis e clusters.

    ![O * * a caixa de diálogo Selecionar serviço tecidos Cluster * *][1]

    >[AZURE.NOTE] Se você quiser publicar em um ponto de extremidade aleatório (como um cluster de festa), consulte a seção de **publicação a um ponto de extremidade de cluster aleatório** abaixo.

    Depois que você escolher um ponto de extremidade, o Visual Studio valida a conexão ao cluster tecidos de serviço selecionado. Se o cluster não seguro, Visual Studio possa conectá-lo imediatamente. No entanto, se o cluster estiver protegido, você precisará instalar um certificado no computador local antes de prosseguir. Veja [como configurar conexões seguras](service-fabric-visualstudio-configure-secure-connections.md) para obter mais informações. Quando terminar, escolha o botão de **Okey** . Cluster selecionado aparece na caixa de diálogo **Publicar aplicativo de serviço de tecidos** .

3. Na caixa de lista suspensa de **Arquivo de parâmetro de aplicativo** , navegue até um arquivo de parâmetro de aplicativo. Um arquivo de parâmetro de aplicativo contém valores especificados pelo usuário para os parâmetros no arquivo de manifesto do aplicativo. Para adicionar ou alterar um parâmetro, escolha o botão **Editar** . Inserir ou alterar o valor do parâmetro na grade **Parameters** . Quando terminar, escolha o botão **Salvar** .

    ![O * * a caixa de diálogo Editar parâmetros * *][2]

4. Use a caixa de seleção **atualizar o aplicativo** para especificar se isso publicar ação é uma atualização. Atualização publicar ações diferir normal publicar ações. Consulte a [Atualização do serviço tecidos aplicativo](service-fabric-application-upgrade.md) para obter uma lista das diferenças. Para definir configurações de atualização, escolha o link **Definir configurações de atualização** . O editor de parâmetro atualização aparece. Consulte [Configurar a atualização de um aplicativo de serviço tecidos](service-fabric-visualstudio-configure-upgrade.md) para saber mais sobre os parâmetros de atualização.

5. Escolha do **manifesto versões...** botão para exibir a caixa de diálogo **Editar versões** . Você precisa atualizar o aplicativo e versões de serviço para uma atualização ocorra. Consulte [Atualizar tutorial de aplicativo de serviço tecidos](service-fabric-application-upgrade-tutorial.md) para saber como o aplicativo e serviço manifestam versões impacto um processo de atualização.

    ![O * * a caixa de diálogo Editar versões * *][3]

    Se as versões de serviço e o aplicativo usam o controle de versão semântico como 1.0.0 ou valores numéricos no formato de 1.0.0.0, selecione a opção de **Atualizar automaticamente o aplicativo e versões de serviço** . Quando você escolher essa opção, o serviço e números de versão do aplicativo são atualizados automaticamente sempre que um código, config ou dados pacote versão são atualizados. Se você preferir editar as versões manualmente, desmarque a caixa de seleção para desabilitar esse recurso.

    >[AZURE.NOTE] Para todas as entradas de pacote apareça para um projeto de ator, primeiro crie o projeto para gerar as entradas nos arquivos de manifesto de serviço.

6. Quando você terminar especificando todas as configurações necessárias, escolha o botão de **Publicar** para publicar seu aplicativo para cluster tecidos de serviço selecionado. As configurações que você especificou são aplicadas para o processo de publicação.

## <a name="publish-to-an-arbitrary-cluster-endpoint-including-party-clusters"></a>Publicar em um ponto de extremidade de cluster aleatório (incluindo clusters de terceiros)

A experiência de publicação do Visual Studio é otimizada para publicação para clusters remotos associados a uma das suas assinaturas Azure. No entanto, é possível publicar para pontos de extremidade aleatório (como clusters de festa de serviço tecidos) editando o perfil de publicação XML diretamente. Conforme descrito acima, dois publicar perfis são fornecidos por padrão –**Local.xml** e **Cloud.xml**, mas é bem-vindo ao criar perfis adicionais para ambientes diferentes. Por exemplo, talvez você queira criar um perfil da publicação para clusters de terceiros, talvez denominada **Party.xml**.

Se você estiver se conectando a um cluster desprotegido, tudo que é necessário é o ponto de extremidade de conexão de cluster, como `partycluster1.eastus.cloudapp.azure.com:19000`. Nesse caso, o ponto de extremidade de conexão no perfil publicar teria esta aparência:

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  Se você estiver se conectando a um cluster seguro, você também precisa fornecer os detalhes do certificado do cliente do armazenamento local a ser usado para autenticação. Para obter mais detalhes, consulte [Configurando conexões seguras para um cluster de estrutura de serviço](service-fabric-visualstudio-configure-secure-connections.md).

  Depois de configurar o seu perfil de publicação, você pode fazer referência-la na caixa de diálogo Publicar conforme mostrado abaixo.

  ![Novo perfil de publicação na caixa de diálogo Publicar][4]

  Observe que, nesse caso, o novo perfil de publicar aponta para um dos arquivos de parâmetro de aplicativo padrão. Isso é adequado se você deseja publicar a mesma configuração de aplicativo para um número de ambientes. Por outro lado, em casos onde você deseja ter diferentes configurações para cada ambiente que você deseja publicar, faria sentido para criar um arquivo de parâmetro de aplicativo correspondente.

## <a name="next-steps"></a>Próximas etapas

Para saber como automatizar o processo de publicação em um ambiente de integração contínua, consulte [Configurar integração contínua de estrutura de serviço](service-fabric-set-up-continuous-integration.md).


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png
