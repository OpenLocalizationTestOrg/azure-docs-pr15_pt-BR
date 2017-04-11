<properties
   pageTitle="Configurando um cluster de estrutura de serviço usando o Visual Studio | Microsoft Azure"
   description="Descreve como configurar um cluster de estrutura de serviço usando o Gerenciador de recursos do Azure modelo criado por um projeto de grupo de recursos do Azure no Visual Studio"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/06/2016"
   ms.author="karolz@microsoft.com"/>

# <a name="set-up-a-service-fabric-cluster-by-using-visual-studio"></a>Configurar um cluster de estrutura de serviço usando o Visual Studio
Este artigo descreve como configurar um cluster de estrutura de serviço do Azure usando o Visual Studio e um modelo do Gerenciador de recursos do Azure. Usaremos um projeto de grupo de recursos do Azure do Visual Studio para criar o modelo. Depois que o modelo tiver sido criado, ele pode ser implantado diretamente para o Azure do Visual Studio. Ele também pode ser usado a partir de um script ou como parte do recurso de CI (integração contínua).

## <a name="create-a-service-fabric-cluster-template-by-using-an-azure-resource-group-project"></a>Criar um modelo de cluster tecidos de serviço usando um projeto de grupo de recursos Azure
Para começar, abra o Visual Studio e criar um projeto de grupo de recursos Azure (ele está disponível na pasta de **nuvem** ):

![Caixa de diálogo Nova projeto com o projeto de grupo de recursos do Azure selecionado][1]

Você pode criar uma nova solução do Visual Studio para esse projeto ou adicioná-la a uma solução existente.

>[AZURE.NOTE] Se você não vir o projeto de grupo de recursos Azure sob o nó nuvem, você não tem o SDK do Azure instalado. Iniciar o Web Platform Installer ([instalá-la agora](http://www.microsoft.com/web/downloads/platform.aspx) se você ainda não tiver) e procure por "Azure SDK para .NET" e instalar a versão que seja compatível com sua versão do Visual Studio.

Depois de você pressionar o botão Okey, Visual Studio solicitará que você selecione o modelo do Gerenciador de recursos que você deseja criar:

![Selecione a caixa de diálogo modelo do Azure com o modelo de Cluster de estrutura de serviço selecionado][2]

Selecione o modelo de Cluster de estrutura de serviço e pressionar o botão Okey novamente. O projeto e o modelo do Gerenciador de recursos agora foram criados.

## <a name="prepare-the-template-for-deployment"></a>Preparar o modelo para implantação
Antes do modelo é implantado para criar o cluster, você deve fornecer valores para os parâmetros de modelo necessários. Esses valores de parâmetro são lidos da `ServiceFabricCluster.parameters.json` arquivo, que está na `Templates` pasta do projeto do grupo de recursos. Abra o arquivo e forneça os seguintes valores:

|Nome do parâmetro           |Descrição|
|-----------------------  |--------------------------|
|adminUserName            |O nome da conta de administrador para máquinas tecidos de serviço (nós).|
|certificateThumbprint    |A impressão digital do certificado que protege cluster.|
|sourceVaultResourceId    |A *identificação do recurso* do cofre chave onde o certificado que protege cluster está armazenado.|
|certificateUrlValue      |A URL do certificado de segurança do cluster.|

O modelo do Gerenciador de recursos do Visual Studio serviço tecidos cria um cluster seguro que está protegido por um certificado. Este certificado é identificado pelos parâmetros de modelo últimas três (`certificateThumbprint`, `sourceVaultValue`, e `certificateUrlValue`), e ele deve existir em um **Cofre de chave do Azure**. Para obter mais informações sobre como criar o certificado de segurança de cluster, consulte o artigo de [serviço tecidos cluster cenários de segurança](service-fabric-cluster-security.md#x509-certificates-and-service-fabric) .

## <a name="optional-change-the-cluster-name"></a>Opcional: alterar o nome de cluster
Cada cluster de estrutura de serviço tem um nome. Quando um cluster de tecidos é criado no Azure, o nome de cluster determina (junto com a região Azure) o nome do sistema de nome de domínio (DNS) para o cluster. Por exemplo, se você nomear seu cluster `myBigCluster`e o local (Azure região) do grupo de recursos que hospedará o novo cluster é Leste EUA, o nome DNS do cluster será `myBigCluster.eastus.cloudapp.azure.com`.

Por padrão, o nome do cluster é gerado automaticamente e feito exclusivo anexando um sufixo aleatório para um prefixo "cluster". Isso facilita muito a usar o modelo como parte de um sistema de **integração contínua** CI (). Se você quiser usar um nome para o seu cluster específico, um que seja significativo para você, defina o valor da `clusterName` variável no arquivo de modelo de Gerenciador de recursos (`ServiceFabricCluster.json`) para seu nome escolhido. É a primeira variável definida no arquivo.

## <a name="optional-add-public-application-ports"></a>Opcional: adicionar portas de aplicativo pública
Talvez você também queira alterar as portas do aplicativo público para o cluster antes de implantá-lo. Por padrão, o modelo abre apenas dois portas TCP públicas (80 e 8081). Se precisar de mais para seus aplicativos, modificar a definição de Balanceador de carga do Azure no modelo. A definição é armazenada no arquivo de modelo principal (`ServiceFabricCluster.json`). Abra o arquivo e pesquise por `loadBalancedAppPort`. Cada porta está associada a três artefatos:

1. Uma variável de modelo que define o valor de porta TCP para a porta:

    ```json
    "loadBalancedAppPort1": "80"
    ```

2. Um *teste* que define a frequência e quanto tempo a carga Azure balanceador tenta usar um nó de serviço tecidos específico antes de falhar em outro. Os testes fazem parte do recurso balanceador de carga. Aqui está a definição de teste para a primeira porta de aplicativo padrão:

    ```json
    {
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[variables('loadBalancedAppPort1')]",
            "protocol": "Tcp"
        }
    }
    ```

3. Uma *regra de balanceamento de carga* que une a porta e o teste, que permite balanceamento de carga entre um conjunto de nós de cluster tecidos de serviço:

    ```json
    {
        "name": "AppPortLBRule1",
        "properties": {
            "backendAddressPool": {
                "id": "[variables('lbPoolID0')]"
            },
            "backendPort": "[variables('loadBalancedAppPort1')]",
            "enableFloatingIP": false,
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPort": "[variables('loadBalancedAppPort1')]",
            "idleTimeoutInMinutes": 5,
            "probe": {
                "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
            },
            "protocol": "Tcp"
        }
    }
    ```
Se os aplicativos que você planeja implantar o cluster precisam de mais portas, você pode adicioná-los pela criação de teste adicional e definições de regra de balanceamento de carga. Para obter mais informações sobre como trabalhar com balanceador de carga do Azure através de modelos do Gerenciador de recursos, consulte [começar a criar um balanceador de carga interno usando um modelo](../load-balancer/load-balancer-get-started-ilb-arm-template.md).

## <a name="deploy-the-template-by-using-visual-studio"></a>Implantar o modelo usando o Visual Studio
Depois de salvar todos os valores de parâmetro obrigatório na`ServiceFabricCluster.param.dev.json` arquivo, você estará pronto para implantar o modelo e criar seu cluster de estrutura de serviço. Clique com botão direito o projeto de grupo de recursos no Visual Studio Solution Explorer e escolha **implantar | Nova implantação …**. Se necessário, o Visual Studio mostrará a caixa de diálogo **implantar ao grupo de recursos** , pedindo para autenticar Azure:

![Implantar a caixa de diálogo de grupo de recursos][3]

A caixa de diálogo permite que você escolha um grupo de recursos do Gerenciador de recursos existente para o cluster e fornece a opção para criar um novo. Normalmente faz sentido usar um grupo de recursos separado para um cluster de estrutura de serviço.

Após você pressionar o botão de implantar, o Visual Studio solicitará que você confirme os valores de parâmetro de modelo. Clique no botão **Salvar** . Um parâmetro não tem um valor persistente: a senha da conta administrativa para o cluster. Você precisa fornecer um valor de senha quando o Visual Studio solicitar um.

>[AZURE.NOTE] Começando com o Azure SDK 2,9, Visual Studio oferece suporte a senhas de leitura do **Azure chave cofre** durante a implantação. Na caixa de diálogo de parâmetros de modelo, observe que o `adminPassword` caixa de texto de parâmetro tem um pequeno ícone de "chave" à direita. Esse ícone permite que você selecione um segredo cofre chave existente como a senha de administrador para cluster. Apenas certifique-se ao primeiro habilitar acesso do Gerenciador de recursos do Azure para implantação de modelo nas políticas de acesso avançado do seu cofre chave. 

Você pode monitorar o andamento do processo de implantação na janela de saída do Visual Studio. Depois que a implantação do modelo é concluída, o novo cluster está pronto para uso!

>[AZURE.NOTE] Se PowerShell nunca foi usado para administrar Azure da máquina que você está usando agora, você precisa fazer um pouco manutenção.
>1. Ativar os scripts do PowerShell executando o [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx) comando. Para máquinas de desenvolvimento, a política de "irrestrita" é geralmente aceitável.
>2. Decida se deseja permitir que o conjunto de dados de diagnóstico de comandos do PowerShell do Azure e execute [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) ou [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx) conforme necessário. Isso irá evitar prompts desnecessárias durante a implantação do modelo.

Se houver erros, acesse o [portal do Azure](https://portal.azure.com/) e abra o grupo de recursos que você distribuiu para. Clique em **todas as configurações**, clique **implantações** na lâmina configurações. Uma implantação de grupo de recursos falha deixa informações detalhadas de diagnóstico lá.

>[AZURE.NOTE] Serviço tecidos clusters exigem um determinado número de nós deve ser para cima para manter a disponibilidade e preservar o estado - denominado "manter quorum". Portanto, não é segura para desligar a todas as máquinas no cluster, a menos que primeiro você executou um [backup completo do seu estado](service-fabric-reliable-services-backup-restore.md).

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre a configuração de cluster de estrutura de serviço usando o portal do Azure](service-fabric-cluster-creation-via-portal.md)
- [Saiba como gerenciar e implantar aplicativos de serviço tecidos usando o Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png
