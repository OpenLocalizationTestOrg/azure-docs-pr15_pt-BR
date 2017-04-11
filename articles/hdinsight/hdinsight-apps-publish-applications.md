<properties
    pageTitle="Publicar aplicativos de HDInsight | Microsoft Azure"
    description="Aprenda a criar e publicar aplicativos de HDInsight."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/18/2016"
    ms.author="jgao"/>

# <a name="publish-hdinsight-applications-into-the-azure-marketplace"></a>Publicar aplicativos de HDInsight em do Azure Marketplace

Um aplicativo de HDInsight é um aplicativo que os usuários podem instalar em um cluster de HDInsight baseados em Linux. Esses aplicativos podem ser desenvolvidos pela Microsoft, fornecedores de software independentes (ISV) ou por si mesmo. Neste artigo, você aprenderá como publicar um aplicativo de HDInsight em do Azure Marketplace.  Para obter informações gerais sobre como publicar do Azure Marketplace, consulte [publicar uma oferta para o Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

Aplicativos de HDInsight usam o modelo de *Trazer seu próprio licença (BYOL)* , onde o provedor de aplicativo é responsável por licenciamento o aplicativo para usuários finais e usuários finais somente cobrados por Azure para os recursos criar, como cluster HDInsight e seus VMs/nós. No momento, cobrança para o próprio aplicativo não é feita através do Azure.

Outro aplicativo HDInsight relacionados artigo:

- [HDInsight instalar aplicativos](hdinsight-apps-install-applications.md): Saiba como instalar um aplicativo de HDInsight para seus clusters.
- [Instalar aplicativos personalizados de HDInsight](hdinsight-apps-install-custom-applications.md): Saiba como instalar e testar aplicativos personalizados de HDInsight.

 
## <a name="prerequisites"></a>Pré-requisitos

Para enviar seu aplicativo personalizado para o marketplace, você deve ter criado e testado seu aplicativo personalizado. Consulte os seguintes artigos:

- [Instalar aplicativos personalizados de HDInsight](hdinsight-apps-install-custom-applications.md): Saiba como instalar e testar aplicativos personalizados de HDInsight.

Você também deve ter registrar sua conta de desenvolvedor. Consulte [criar uma conta do Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)e [publicar uma oferta para o Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) .

## <a name="define-application"></a>Definir o aplicativo

Há duas etapas envolvidas para publicação de aplicativos do Azure Marketplace.  Primeiro você definir um arquivo de **createUiDef.json** para indicar quais clusters seu aplicativo é compatível com; e, em seguida, você pode publicar o modelo do portal do Azure. O que vem a seguir é um exemplo de arquivo de createUiDef.json.

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


|Campo  | Descrição   | Valores possíveis|
|-------|---------------|----------------|
|tipos de  | Os tipos de cluster que o aplicativo é compatível com.    |Hadoop, HBase, tempestade, Spark (ou qualquer combinação desses)|
|níveis  | Os níveis de cluster que o aplicativo é compatível com.    |Padrão, Premium (ou ambos)|
|versões|  Os tipos de cluster HDInsight que o aplicativo é compatível com.    |3.4|

## <a name="package-application"></a>Aplicativo de pacote

Crie um arquivo zip que contém todos os arquivos necessários para instalar os aplicativos de HDInsight. Você precisará o arquivo zip no [aplicativo de publicar](#publish-application).

- [createUiDefinition.json](#define-application).
- mainTemplate.json. Veja uma amostra ao [instalar aplicativos personalizados de HDInsight](hdinsight-apps-install-custom-applications.md).

    >[AZURE.IMPORTANT] O nome dos nomes de script de instalação de aplicativo deve ser exclusivo para um determinado cluster com o formato abaixo. Além disso qualquer instalação e a desinstalação ações de script deve ser idempotente, significando que os scripts pode ser chamado repeatly enquanto se produz os mesmos resultados.
    
    >   nome":" [concat (' matiz-instalação-v0 ','-', uniquestring('applicationName')] "
        
    >Observação Existem três partes para o nome do script:
        
    >   1. Um prefixo de nome de script, que deve incluir o nome do aplicativo ou um nome relevante para o aplicativo.
    >   2. A "-" para fins de legibilidade.
    >   3. Uma função de cadeia de caracteres exclusiva com o nome do aplicativo como o parâmetro.

    >   Um exemplo é a acima acaba se tornando: Matiz-instalação-v0-4wkahss55hlas na lista de ação script persistentes. Para uma carga JSON de amostra, consulte [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).

- Todos os necessários scripts.

> [AZURE.NOTE] Os arquivos de aplicativo (incluindo arquivos de aplicativos web, se houver alguma) pode estar localizado em qualquer ponto de extremidade publicamente acessível.

## <a name="publish-application"></a>Publicar aplicativo

Siga as etapas a seguintes para publicar um aplicativo de HDInsight:

1. Entre [portal de publicação do Azure](https://publish.windowsazure.com/).
2. Clique em **modelos de solução** da esquerda para criar um novo modelo de solução.
3. Insira um título e, em seguida, clique em **criar um novo modelo de solução**.
3. Clique em **Centro de desenvolvimento de criar conta e participar do programa Azure** para registrar sua empresa se você ainda não tiver feito isso.  Consulte [criar uma conta do Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
4. Clique em **definir algumas topologias para começar**. Um modelo de solução é um "parent" a todas as suas topologias. Você pode definir várias topologias de um modelo de oferta/solução. Quando uma oferta será enviada para preparação, ele é enviado todas as suas topologias. 
4. Insira um nome de topologia e, em seguida, clique no sinal de adição.
5. Insira uma nova versão e, em seguida, clique no sinal.
6. Carregue o arquivo zip preparado no [aplicativo de pacote](#package-application).  
7. Clique em **solicitação de certificação**. A equipe de certificação Microsoft analisará os arquivos e certificar a topologia.

## <a name="next-steps"></a>Próximas etapas

- [HDInsight instalar aplicativos](hdinsight-apps-install-applications.md): Saiba como instalar um aplicativo de HDInsight para seus clusters.
- [Instalar aplicativos personalizados de HDInsight](hdinsight-apps-install-custom-applications.md): Saiba como implantar um aplicativo de HDInsight cancelamento publicado ao HDInsight.
- [Clusters baseados em Personalizar Linux HDInsight usando a ação de Script](hdinsight-hadoop-customize-cluster-linux.md): Saiba como usar a ação de Script para instalar aplicativos adicionais.
- [Hadoop baseados em Linux criar clusters em HDInsight usando o Gerenciador de recursos do Azure modelos](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Saiba como chamar modelos do Gerenciador de recursos para criar clusters de HDInsight.
- [Usar nós de borda vazio no HDInsight](hdinsight-apps-use-edge-node.md): Saiba como usar um nó de borda vazia para acessar HDInsight cluster, testando aplicativos de HDInsight e HDInsight aplicativos de hospedagem.

