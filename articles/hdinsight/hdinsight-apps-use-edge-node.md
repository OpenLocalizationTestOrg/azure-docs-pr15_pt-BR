<properties
    pageTitle="Usar borda vazia nós em HDInsight | Microsoft Azure"
    description="Como adicionar um nó de borda ampty à HDInsight cluster que pode ser usado como um cliente e teste/host seus aplicativos de HDInsight."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    tags="azure-portal"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="use-empty-edge-nodes-in-hdinsight"></a>Usar borda vazia nós em HDInsight

Saiba como adicionar um nó de borda vazia para um cluster de HDInsight baseados em Linux. Um nó de borda vazia é uma máquina virtual do Linux com as mesmas ferramentas de cliente instalado e configurado como o headnodes, mas com nenhum serviços hadoop em execução. Você pode usar o nó de borda para acessar o cluster, teste seus aplicativos de cliente e hospedar seus aplicativos de cliente. 

Você pode adicionar um nó de borda vazia para um cluster de HDInsight existente, para um novo cluster quando você cria o cluster. A adição de um nó de borda vazia é feita usando o modelo do Gerenciador de recursos do Azure.  O exemplo a seguir demonstra como isso é feito usando um modelo:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "[variables('clusterApiVersion')]",
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "[parameters('edgeNodeSize')]"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Como mostrado na amostra, opcionalmente, você pode chamar uma [ação de script](hdinsight-hadoop-customize-cluster-linux.md) para realizar configurações adicionais, como instalar o [Matiz Apache](hdinsight-hadoop-hue-linux.md) no nó de borda.

Após ter criado um nó de borda, você pode conectar-se para o nó de borda usando SSH e execute as ferramentas de cliente para acessar o cluster Hadoop em HDInsight.

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Adicionar um nó de borda a um cluster existente

Nesta seção, você pode usar um modelo de Gerenciador de recursos para adicionar um nó de borda a um cluster de HDInsight existente.  O modelo do Gerenciador de recursos pode ser encontrado no [GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode). O modelo do Gerenciador de recursos chama um script de ação de script localizado em https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. O script não realizar nenhuma ação.  Isso é demonstrar ação de script de chamada de um modelo do Gerenciador de recursos.

**Adicionar um nó vazio borda a um cluster existente**

1. Crie um cluster de HDInsight se você ainda não tiver um.  Consulte [Hadoop tutorial: Introdução ao uso baseados em Linux Hadoop em HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Clique na imagem a seguir para entrar Azure e abra o modelo do Gerenciador de recursos do Azure no portal do Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Configure as seguintes propriedades:

    - CLUSTERNAME: Insira o nome de um cluster de HDInsight existente.
    - EDGENODESIZE: Selecione um dos tamanhos de máquina virtual.
    - EDGENODEPREFIX: O valor padrão é **novo**.  Usando o valor padrão, o nome de nó de borda é **novo edgenode**.  Você pode personalizar o prefixo a partir do portal. Você também pode personalizar o nome completo do modelo.


4. Clique em **Okey** para salvar as alterações.
5. No **grupo de recursos**, selecione um grupo de recursos.
6. Clique em **termos legais de revisão**e, em seguida, clique em **comprar**.
7. Selecione **Fixar no painel de controle**e clique em **criar**.

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Adicionar um nó de borda ao criar um cluster

Nesta seção, você pode usar um modelo de Gerenciador de recursos para criar HDInsight cluster com um nó de borda.  O modelo do Gerenciador de recursos pode ser encontrado em um [contêiner de armazenamento de Blob do Azure](http://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json)de público. O modelo do Gerenciador de recursos chama um script de ação de script localizado em https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. O script não realizar nenhuma ação.  Isso é demonstrar ação de script de chamada de um modelo do Gerenciador de recursos.

**Adicionar um nó vazio borda a um cluster existente**

1. Crie um cluster de HDInsight se você ainda não tiver um.  Consulte [Hadoop tutorial: Introdução ao uso baseados em Linux Hadoop em HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Clique na imagem a seguir para entrar Azure e abra o modelo do Gerenciador de recursos do Azure no portal do Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Configure as seguintes propriedades:
        
    - CLUSTERNAME: Insira um nome para o novo cluster criar.
    - CLUSTERLOGINUSERNAME: Insira o nome de usuário do Hadoop HTTP.  O nome padrão é **admin**.
    - CLUSTERLOGINPASSWORD: Insira a senha do usuário Hadoop HTTP.
    - SSHUSERNAME: Insira o nome de usuário SSH. O nome padrão é **sshuser**.
    - SSHPASSWORD: Insira a senha do usuário SSH.
    - LOCAL: Insira o local do nome do grupo de recursos, o cluster e a conta de armazenamento padrão.
    - CLUSTERTYPE: O valor padrão é **hadoop**.
    - CLUSTERWORKERNODECOUNT: O valor padrão é 2.
    - EDGENODESIZE: Selecione um dos tamanhos de máquina virtual.
    - EDGENODEPREFIX: O valor padrão é **novo**.  Usando o valor padrão, o nome de nó de borda é **novo edgenode**.  Você pode personalizar o prefixo a partir do portal. Você também pode personalizar o nome completo do modelo.

4. Clique em **Okey** para salvar as alterações.
5. No **grupo de recursos**, digite um novo nome de grupo de recursos.
6. Clique em **termos legais de revisão**e, em seguida, clique em **comprar**.
7. Selecione **Fixar no painel de controle**e clique em **criar**. 


## <a name="access-an-edge-node"></a>Acessar um nó de borda

O nó de borda ssh ponto de extremidade é <EdgeNodeName>. <ClusterName>-ssh.azurehdinsight.net:22.  Por exemplo, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

O nó de borda aparece como um aplicativo no portal do Azure.  O portal fornece as informações para acessar o nó de borda usando o SSH.

**Para verificar o ponto de extremidade SSH de nó de borda**

1. Entre [portal do Azure](https://portal.azure.com).
2. Abra o cluster HDInsight com um nó de borda.
3. Clique em **aplicativos** da lâmina do cluster. Você deverá ver o nó de borda.  O nome padrão é **novo edgenode**.
4. Clique no nó de borda. Você deverá ver o ponto de extremidade SSH.

**Usar a seção no nó borda**

5. Use SSH para conectar o nó de borda.  Consulte [usar SSH com baseado em Linux Hadoop em HDInsight do Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md) ou [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
6. Depois que você tiver conectado para o nó de borda usando SSH, use o comando a seguir para abrir o console de seção:

        hive
7. Execute o seguinte comando para mostrar as tabelas de seção no cluster:

        show tables;

## <a name="delete-an-edge-node"></a>Excluir um nó de borda

Você pode excluir um nó de borda do portal do Azure.

**Para acessar um nó de borda**

1. Entre [portal do Azure](https://portal.azure.com).
2. Abra o cluster HDInsight com um nó de borda.
3. Clique em **aplicativos** da lâmina do cluster. Você deverá ver uma lista de nós de borda.  
4. Clique com botão direito no nó de borda que você deseja excluir e clique em **Excluir**.
5. Clique em **Sim** para confirmar.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como adicionar um nó de borda e como acessar o nó de borda. Para saber mais, consulte os seguintes artigos:

- [HDInsight instalar aplicativos](hdinsight-apps-install-applications.md): Saiba como instalar um aplicativo de HDInsight para seus clusters.
- [Instalar aplicativos personalizados de HDInsight](hdinsight-apps-install-custom-applications.md): Saiba como implantar um aplicativo de HDInsight não publicado ao HDInsight.
- [HDInsight publicar aplicativos](hdinsight-apps-publish-applications.md): aprender a publicar HDInsight aplicativos personalizados ao Azure Marketplace.
- [MSDN: instalar um aplicativo de HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Aprenda a definir HDInsight aplicativos.
- [Clusters baseados em Personalizar Linux HDInsight usando a ação de Script](hdinsight-hadoop-customize-cluster-linux.md): Saiba como usar a ação de Script para instalar aplicativos adicionais.
- [Hadoop baseados em Linux criar clusters em HDInsight usando o recurso Gerenciador de modelos](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Saiba como chamar modelos do Gerenciador de recursos para criar clusters de HDInsight.

