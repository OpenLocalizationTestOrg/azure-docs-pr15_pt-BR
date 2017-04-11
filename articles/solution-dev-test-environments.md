<properties
   pageTitle="Ambientes de desenvolvimento e teste | Microsoft Azure"
   description="Saiba como usar modelos do Gerenciador de recursos do Azure para rapidamente e consistentemente criar e excluir desenvolvimento e ambientes de teste."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/22/2016"
   ms.author="tomfitz"/>

# <a name="development-and-test-environments-in-microsoft-azure"></a>Ambientes de desenvolvimento e teste no Microsoft Azure

Aplicativos personalizados são normalmente implantados para vários ambientes de desenvolvimento e testes antes da implantação de produção. Quando ambientes são criados no local, recursos de computação são adquiridos ou alocados para cada ambiente para cada aplicativo. Os ambientes geralmente incluem várias máquinas físicas ou virtuais com configurações específicas que são implantadas manualmente ou com scripts de automação complexas. Implantações muitas vezes levar horas e resultarem em Configurações inconsistentes em ambientes.

## <a name="scenario"></a>Cenário ##

Quando você provisionar ambientes de desenvolvimento e teste no Microsoft Azure, você paga apenas para os recursos que você usar.  Este artigo explica como rapidamente e consistentemente você pode criar, manter, excluir desenvolvimento e os ambientes de teste usando modelos do Gerenciador de recursos do Azure e arquivos de parâmetro, conforme ilustrado abaixo.

![Cenário](./media/solution-dev-test-environments/scenario.png)

Três ambientes de desenvolvimento e testes são mostradas acima.  Cada tem o aplicativo da web e os recursos de banco de dados SQL, que são especificados em um arquivo de modelo.  Os nomes do aplicativo e do banco de dados em cada ambiente são diferentes e são especificados em arquivos de parâmetro exclusiva para cada ambiente.  

Se você não estiver familiarizado com os conceitos do Gerenciador de recursos do Azure, é recomendável que você leia o artigo de [Visão geral do Gerenciador de recursos do Azure](azure-resource-manager/resource-group-overview.md) antes de ler este artigo.

Talvez você queira percorrer as etapas neste artigo primeiro conforme listado sem ler qualquer um dos artigos referenciados para obter rapidamente alguns experiência usando modelos do Gerenciador de recursos do Azure. Depois de você esteve pelas etapas uma vez, você poderá obter respostas a maioria das perguntas que surgiram seu primeiro tempo através de fazendo experiências mais com as etapas e lendo os artigos mencionados.

## <a name="plan-azure-resource-use"></a>Planejar o uso de recursos do Azure
Quando você tiver um design de alto nível do aplicativo, você pode definir:

- Quais recursos Azure seu aplicativo incluirá. Você pode criar seu aplicativo e implantá-la como um Azure Web App com um banco de dados do SQL Azure.  Você pode criar seu aplicativo em máquinas virtuais usando PHP e MySQL IIS e SQL Server ou outros componentes. O [serviço de aplicativo do Azure, serviços de nuvem e comparação de máquinas virtuais]( app-service-web/choose-web-site-cloud-service-vm.md) artigo ajuda você a decidir quais recursos Azure que talvez você queira utilizar para seu aplicativo.
- Quais requisitos de nível de serviço como disponibilidade, segurança e escala que o aplicativo atenderá.

## <a name="download-an-existing-template"></a>Baixar um modelo existente
Um modelo do Gerenciador de recursos do Azure define todos os recursos Azure que utiliza seu aplicativo. Vários modelos já existirem que você pode implantar diretamente no portal do Azure, ou baixar, modificar e salvar em um sistema de controle de origem com o código do seu aplicativo.  Conclua as etapas abaixo para baixar um modelo existente.

1. Procure modelos existentes no repositório GitHub [Modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/) . Na lista, você verá uma pasta de "[201-web-app--banco de dados sql](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)". Como muitos aplicativos personalizados incluem um aplicativo web e o banco de dados SQL, este modelo é usado como um exemplo no restante deste artigo para ajudá-lo a compreender como usar modelos. Ele está além do escopo deste artigo explicar tudo este modelo cria e configura, mas se você planeja usá-lo para criar ambientes reais em sua organização, você desejará compreendê-lo lendo o artigo [provisionar um aplicativo web com um banco de dados do SQL](app-service-web/app-service-web-arm-with-sql-database-provision.md) .
Observação: Este artigo foi escrito para a versão de dezembro de 2015 do modelo [201-web-app--banco de dados sql](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) . Os links abaixo que apontem para o modelo e arquivos de parâmetro são para essa versão do modelo.
2. Clique no arquivo [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json) na pasta 201-web-app--banco de dados sql para exibir seu conteúdo. Este é o arquivo de modelo do Gerenciador de recursos do Azure. 
3. No modo de exibição, clique no botão "[bruto](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json)". 
4. Com o mouse, selecione o conteúdo deste arquivo e salvá-los em seu computador como um arquivo denominado "TestApp1-Template.json." 
5. Examine o conteúdo do modelo e observe o seguinte:
 - Seção de **recursos** : Esta seção define os tipos de recursos Azure criados por este modelo. Entre outros tipos de recursos, este modelo cria recursos [Azure Web App](app-service-web/app-service-web-overview.md) e [Azure SQL Database](sql-database/sql-database-technical-overview.md) . Se você preferir executar e gerenciar web e SQL Server em máquinas virtuais, você pode usar os modelos de "[iis-2vm-sql-1vm](https://github.com/Azure/azure-quickstart-templates/tree/master/iis-2vm-sql-1vm)" ou "[lâmpada-app](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)", mas as instruções neste artigo são baseadas no modelo [201-web-app--banco de dados sql](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) .
 - Seção de **parâmetros** : Esta seção define os parâmetros que cada recurso pode ser configurado com. Alguns dos parâmetros especificados no modelo de têm propriedades de "ValorPadrão", enquanto outros não. Ao implantar recursos Azure com um modelo, você deve fornecer valores para todos os parâmetros que não têm ValorPadrão propriedades especificadas no modelo.  Se você não fornecer valores para parâmetros com defaultValue propriedades, o valor especificado para o parâmetro de ValorPadrão no modelo é usado.

Um modelo define quais recursos Azure são criados e os parâmetros de cada recurso pode ser configurado com. Você pode saber mais sobre como projetar sua própria lendo o artigo de [práticas recomendadas para criar modelos do Gerenciador de recursos do Azure](best-practices-resource-manager-design-templates.md) e modelos.

## <a name="download-and-customize-an-existing-parameter-file"></a>Baixar e personalizar um arquivo existente do parâmetro

Embora você provavelmente desejará a *mesma* Azure recursos criados em cada ambiente, você provavelmente desejará a configuração dos recursos a ser *diferentes* em cada ambiente.  Isso é onde entram arquivos de parâmetro. Crie arquivos de parâmetro que contenham valores exclusivos em cada ambiente realizando as etapas abaixo.   

1. Exiba o conteúdo do arquivo [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json) na pasta 201-web-app--banco de dados sql. Este é o arquivo de parâmetro para o arquivo de modelo que você salvou na seção anterior. 
2. No modo de exibição, clique no botão "[bruto](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json)". 
3. Com o mouse, selecione o conteúdo do arquivo e salvá-los em três arquivos separados em seu computador com os seguintes nomes:
 - TestApp1 de parâmetros de Development.json
 - TestApp1 de parâmetros de Test.json
 - TestApp1-parâmetros-pré-Production.json

3. Usando qualquer editor de texto ou JSON, edite o arquivo de parâmetro do ambiente de desenvolvimento criado na etapa 3, substituindo os valores listados à direita dos valores de parâmetro no arquivo com os *valores* listados à direita dos **parâmetros** abaixo: 
 - **nome do site**: *TestApp1DevApp*
 - **hostingPlanName**: *TestApp1DevPlan*
 - **siteLocation**: *Centro dos EUA*
 - **nomedoservidor**: *testapp1devsrv*
 - **serverLocation**: *Centro dos EUA*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *Substitua sua senha*
 - **databaseName**: *testapp1devdb*

4. Usando qualquer editor de texto ou JSON, edite o arquivo de parâmetro do ambiente de teste que você criou na etapa 3, substituindo os valores listados à direita dos valores de parâmetro no arquivo com os *valores* listados à direita dos **parâmetros** abaixo:
 - **nome do site**: *TestApp1TestApp*
 - **hostingPlanName**: *TestApp1TestPlan*
 - **siteLocation**: *Centro dos EUA*
 - **nomedoservidor**: *testapp1testsrv*
 - **serverLocation**: *Centro dos EUA*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *Substitua sua senha*
 - **databaseName**: *testapp1testdb*

5. Usando qualquer editor de texto ou JSON, edite o arquivo de pré-produção parâmetro que você criou na etapa 3.  Substitua todo o conteúdo do arquivo com o que está abaixo:

        {
          "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
          "contentVersion" : "1.0.0.0",
          "parameters" : {
        "administratorLogin" : {
          "value" : "testApp1Admin"
        },
        "administratorLoginPassword" : {
          "value" : "replace with your password"
        },
        "databaseName" : {
          "value" : "testapp1preproddb"
        },
        "hostingPlanName" : {
          "value" : "TestApp1PreProdPlan"
        },
        "serverLocation" : {
          "value" : "Central US"
        },
        "serverName" : {
          "value" : "testapp1preprodsrv"
        },
        "siteLocation" : {
          "value" : "Central US"
        },
        "siteName" : {
          "value" : "TestApp1PreProdApp"
        },
        "sku" : {
          "value" : "Standard"
        },
            "requestedServiceObjectiveName" : {
              "value" : "S1"
        }
          }
        }

No arquivo de parâmetros de pré-produção acima, os parâmetros **sku** e **requestedServiceObjectiveName** foram *adicionados*, enquanto elas não foram adicionadas nos arquivos de parâmetros de desenvolvimento e teste. Isso ocorre porque há valores padrão especificados para esses parâmetros no modelo e em ambientes de desenvolvimento e teste, os valores padrão são usados, mas na pré-produção valores do ambiente não padrão para esses parâmetros são usados.

O motivo não padrão valores são usados para esses parâmetros no ambiente de pré-produção é testar valores desses parâmetros que talvez você prefira para seu ambiente de produção para que eles também podem ser testados.  Esses parâmetros todos relacionam Azure [planos de hospedagem da Web App](https://azure.microsoft.com/pricing/details/app-service/), ou **sku** e Azure [SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)ou **requestedServiceObjectiveName** que são usadas pelo aplicativo.  Skus diferentes e nomes de objetivo de serviço tem custos diferentes e recursos e suportam métricas de nível de serviço diferente.

A tabela a seguir lista os valores padrão para esses parâmetros especificados no modelo e os valores que são usados em vez de valores padrão no arquivo de parâmetros de pré produção.

| Parâmetro | Valor padrão | Valor do parâmetro arquivo |
|---|---|---|
| **SKU** | Livre | Padrão |
| **requestedServiceObjectiveName** | S0 | S1 |

## <a name="create-environments"></a>Criar ambientes
Todos os recursos Azure devem ser criados em um [Grupo de recursos do Azure](azure-resource-manager/resource-group-overview.md). Grupos de recursos permitem agrupar recursos Azure, de modo que eles possam ser gerenciados coletivamente.  [Permissões](./active-directory/role-based-access-built-in-roles.md) podem ser atribuídas para grupos de recursos que pessoas específicas dentro de sua organização podem criar, modificar, excluir ou exibi-los e os recursos contido nelas.  Alertas e informações de cobrança para recursos no grupo de recursos podem ser exibidos no [Portal do Azure](https://portal.azure.com). Grupos de recursos são criados em uma [região](https://azure.microsoft.com/regions/)do Azure.  Neste artigo, todos os recursos são criados na região Centro dos EUA. Quando você começa a criar ambientes reais, você vai escolher a região que melhor atenda às suas necessidades. 

Crie grupos de recursos para cada ambiente usando qualquer um dos métodos abaixo.  Todos os métodos serão alcançar o mesmo resultado.

###<a name="azure-command-line-interface-cli"></a>Azure comando Interface de linha)

Certifique-se de que você tenha o CLI [instalado](xplat-cli-install.md) no Windows, OS X, ou computador Linux e que você tiver [conectado](xplat-cli-connect.md) sua [conta do Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (também chamado de uma conta corporativa ou escolar) à sua assinatura do Azure. Na linha de comando CLI, digite o comando a seguir para criar o grupo de recursos para o ambiente de desenvolvimento.

    azure group create "TestApp1-Development" "Central US"

O comando retornará o seguinte se tiver êxito:

    info:    Executing command group create
    + Getting resource group TestApp1-Development
    + Creating resource group TestApp1-Development
    info:    Created resource group TestApp1-Development
    data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
    data:    Name:                TestApp1-Development
    data:    Location:            centralus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Para criar o grupo de recursos para o ambiente de teste, digite o comando a seguir:

    azure group create "TestApp1-Test" "Central US"

Para criar o grupo de recursos para o ambiente de pré-produção, digite o comando a seguir:

    azure group create "TestApp1-Pre-Production" "Central US"

###<a name="powershell"></a>PowerShell

Certifique-se de que você tem Azure PowerShell 1.01 ou posterior instalado em um computador Windows e tiver conectado a sua [conta do Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (também chamado de uma conta corporativa ou escolar) à sua assinatura conforme detalhado no artigo [como instalar e configurar o PowerShell do Azure](powershell-install-configure.md) . A partir de um prompt de comando do PowerShell, digite o comando a seguir para criar o grupo de recursos para o ambiente de desenvolvimento.

    New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

O comando retornará o seguinte se tiver êxito:

    ResourceGroupName : TestApp1-Development
    Location          : centralus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

Para criar o grupo de recursos para o ambiente de teste, digite o comando a seguir:

    New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

Para criar o grupo de recursos para o ambiente de pré-produção, digite o comando a seguir:

    New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

###<a name="azure-portal"></a>Portal do Azure

1. Entrar no [portal do Azure](https://portal.azure.com) com uma conta do [Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (também chamada de trabalho ou escola). Clique em novo-gerenciamento--> grupo de recursos e insira "Desenvolvimento TestApp1" na caixa de nome de grupo de recursos, selecione a assinatura e selecione "Central-nos" na caixa de local do grupo de recursos, conforme mostrado na figura abaixo.
   ![Portal](./media/solution-dev-test-environments/rgcreate.png)
2. Clique no botão Criar para criar o grupo de recursos.
3. Clique em Procurar, role a lista para grupos de recursos e clique em grupos de recursos, conforme mostrado abaixo.
   ![Portal](./media/solution-dev-test-environments/rgbrowse.png) 
4. Depois de clicar em grupos de recursos, você verá a lâmina de grupos de recursos com seu novo grupo de recursos.
   ![Portal](./media/solution-dev-test-environments/rgview.png)
5. Crie o teste-TestApp1 e TestApp1-pré-produção recurso agrupa da mesma maneira que você criou o grupo de recursos de desenvolvimento TestApp1 acima.

##<a name="deploy-resources-to-environments"></a>Implantar recursos aos ambientes

Implante recursos Azure para os grupos de recursos para cada ambiente usando o arquivo de modelo para a solução e os arquivos de parâmetro para cada ambiente usando um dos métodos abaixo.  Os dois métodos serão alcançar o mesmo resultado.

###<a name="azure-command-line-interface-cli"></a>Azure comando Interface de linha)

Na linha de comando CLI, digite o comando a seguir para implantar recursos ao grupo de recursos criado para o ambiente de desenvolvimento, substituindo [caminho] com o caminho para os arquivos que salvos nas etapas anteriores.

    azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

Após ver uma mensagem de "Aguardando implantação concluir" por alguns minutos, o comando retornará o seguinte se tiver êxito:

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "Deployment1"
    + Waiting for deployment to complete
    data:    DeploymentName     : Deployment1
    data:    ResourceGroupName  : TestApp1-Development
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
    data:    Mode               : Incremental
    data:    Name                           Type          Value
    data:    -----------------------------  ------------  ----------------------------
    data:    siteName                       String        TestApp1DevApp
    data:    hostingPlanName                String        TestApp1DevPlan
    data:    siteLocation                   String        Central US
    data:    sku                            String        Free
    data:    workerSize                     String        0
    data:    serverName                     String        testapp1devsrv
    data:    serverLocation                 String        Central US
    data:    administratorLogin             String        testapp1Admin
    data:    administratorLoginPassword     SecureString  undefined
    data:    databaseName                   String        testapp1devdb
    data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
    data:    edition                        String        Standard
    data:    maxSizeBytes                   String        1073741824
    data:    requestedServiceObjectiveName  String        S0
    info:    group deployment create command OKx

Se o comando não funcionar, resolva quaisquer mensagens de erro e tente novamente.  Problemas comuns estão usando valores de parâmetro que não são compatíveis com restrições de nomenclatura de recursos Azure. Outras dicas de solução podem ser encontradas no artigo [implantações de grupo de recursos de solução de problemas no Azure](./resource-manager-troubleshoot-deployments-cli.md) .

Na linha de comando CLI, digite o comando a seguir para implantar recursos ao grupo de recursos criado para o ambiente de teste, substituindo [caminho] com o caminho para os arquivos que salvos nas etapas anteriores.

    azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

Na linha de comando CLI, digite o comando a seguir para implantar recursos ao grupo de recursos criado para o ambiente de pré-produção, substituindo [caminho] com o caminho para os arquivos que salvos nas etapas anteriores.

    azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
###<a name="powershell"></a>PowerShell

A partir de um prompt de comando do Azure PowerShell (versão 1.01 ou superior), digite o comando a seguir para implantar recursos ao grupo de recursos criado para o ambiente de desenvolvimento, substituindo [caminho] com o caminho para os arquivos que salvos nas etapas anteriores.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

Após vendo um cursor piscando por alguns minutos, o comando retornará o seguinte se tiver êxito:

    DeploymentName    : Deployment1
    ResourceGroupName : TestApp1-Development
    ProvisioningState : Succeeded
    Timestamp         : XX/XX/XXXX 2:44:48 PM
    Mode              : Incremental
    TemplateLink      : 
    Parameters        : 
                        Name             Type                       Value     
                        ===============  =========================  ==========
                        siteName         String                     TestApp1DevApp
                        hostingPlanName  String                     TestApp1DevPlan
                        siteLocation     String                     Central US
                        sku              String                     Free      
                        workerSize       String                     0         
                        serverName       String                     testapp1devsrv
                        serverLocation   String                     Central US
                        administratorLogin  String                     testapp1Admin
                        administratorLoginPassword  SecureString                         
                        databaseName     String                     testapp1devdb
                        collation        String                     SQL_Latin1_General_CP1_CI_AS
                        edition          String                     Standard  
                        maxSizeBytes     String                     1073741824
                        requestedServiceObjectiveName  String                     S0        
                        
    Outputs           :

  Se o comando não funcionar, resolva quaisquer mensagens de erro e tente novamente.  Problemas comuns estão usando valores de parâmetro que não são compatíveis com restrições de nomenclatura de recursos Azure. Outras dicas de solução podem ser encontradas no artigo [implantações de grupo de recursos de solução de problemas no Azure](./resource-manager-troubleshoot-deployments-powershell.md) .

  A partir de um prompt de comando do PowerShell, digite o comando a seguir para implantar recursos ao grupo de recursos criado para o ambiente de teste, substituindo [caminho] com o caminho para os arquivos que salvos nas etapas anteriores.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  A partir de um prompt de comando do PowerShell, digite o comando a seguir para implantar recursos ao grupo de recursos criado para o ambiente de pré-produção, substituindo [caminho] com o caminho para os arquivos que salvos nas etapas anteriores.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

Os arquivos de modelo e um parâmetro podem ser mantidos com seu código de aplicativo e de versão em um sistema de controle de origem.  Você também pode salvar os comandos acima para arquivos de script e salvá-los com seu código também.

> [AZURE.NOTE] Você pode implantar o modelo para o Azure diretamente clicando no botão de "Implantar ao Azure" no artigo do [provisionar um aplicativo Web com um banco de dados do SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) .  Você pode achar isso útil para saber mais sobre modelos, mas isso não permitem que você edite, versão e salve os valores de modelo e um parâmetro com o código do seu aplicativo, para que mais detalhes sobre esse método não é coberto neste artigo.

## <a name="maintain-environments"></a>Manter ambientes
Durante o desenvolvimento, a configuração dos recursos Azure em diferentes ambientes pode ser alterada inconsistente intencionalmente ou acidentalmente.  Isso pode causar desnecessárias de solução de problemas e resolução de problemas durante o ciclo de desenvolvimento do aplicativo.

1. Altere os ambientes abrindo o [portal do Azure](https://portal.azure.com). 
2. Entrar com a mesma conta usada para concluir as etapas acima. 
3. Como mostrado na figura abaixo, clique em Procurar--> grupos de recursos (talvez seja necessário rolar para baixo para ver os grupos de recurso).
   ![Portal](./media/solution-dev-test-environments/rgbrowse.png)
4. Após clicar em grupos de recursos na imagem acima, você verá a lâmina de grupos de recursos e os grupos de três recursos que você criou em uma etapa anterior, conforme mostrado na figura abaixo. Clique no grupo de recursos TestApp1 desenvolvimento e você verá a lâmina que lista os recursos criados pelo modelo na implantação de grupo do recurso de desenvolvimento TestApp1 que você concluiu em uma etapa anterior.  Exclua o recurso de TestApp1DevApp Web App clicando em TestApp1DevApp na lâmina recurso de desenvolvimento TestApp1 grupo e, em seguida, clicando em Excluir na lâmina TestApp1DevApp Web app.
   ![Portal](./media/solution-dev-test-environments/portal2.png)
5. Clique em "Sim" quando o portal exibirá uma mensagem perguntando se você tiver certeza de que deseja excluir o recurso. Fechando a lâmina de grupo de recursos de desenvolvimento de TestApp1 e reabrindo agora mostrará-sem o aplicativo da Web que você acabou de excluir.  O conteúdo do grupo de recursos agora é diferente do que deveriam. Além disso, você pode experimentar excluindo vários recursos de vários grupos de recursos ou até mesmo alterar definições de configuração para alguns dos recursos. Em vez de usar o portal do Azure para excluir um recurso de um grupo de recursos, você poderia usar o comando do PowerShell [Remover AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) ou ou comando de "Excluir recurso azure" da CLI para realizar a mesma tarefa.
6. Para obter todos os recursos e configuração que devem para ser nos grupos de recursos para o estado em que eles devem estar no, implantar novamente os ambientes para os grupos de recursos usando os mesmos comandos que você usou na seção [implantar recursos aos ambientes](#deploy-resources-to-environments) , mas substitua "Deployment1" com "Sensores2".
7.  Como mostrado na seção de resumo da lâmina TestApp1 desenvolvimento na imagem mostrada na etapa 4, você verá que o aplicativo Web excluídos no portal na etapa anterior existe novamente, assim como quaisquer outros recursos que você pode escolheu excluir. Se você alterou a configuração de qualquer um dos recursos, também é possível verificar que tenham sido configuradas novamente para os valores nos arquivos de parâmetro muito. Uma das vantagens de implantação de seus ambientes com modelos do Gerenciador de recursos do Azure é que você pode facilmente implantar novamente os ambientes de volta para um estado conhecido a qualquer momento.
8. Se você clicar no texto em "Última implantação" na imagem abaixo, você verá uma lâmina mostra o histórico de implantação do grupo de recursos.  Como você usou o nome "Deployment1" para a primeira implantação e "Sensores2" para a segunda implantação, você terá duas entradas.  Clicar em uma implantação exibirá um blade que mostra os resultados para cada implantação.
  ![Portal](./media/solution-dev-test-environments/portal3.png)



## <a name="delete-environments"></a>Excluir ambientes
Depois que terminar de usar um ambiente, convém excluí-la para que você não provoca encargos de uso para recursos Azure que já não estiver usando.  Excluir ambientes é ainda mais fácil do que criá-las.  Nas etapas anteriores, grupos de recursos individuais do Azure foram criados para cada ambiente e, em seguida, os recursos foram implantados em grupos de recursos. 

Exclua os ambientes usando qualquer um dos métodos abaixo.  Todos os métodos serão alcançar o mesmo resultado.

### <a name="azure-cli"></a>CLI Azure

Em um prompt CLI, digite o seguinte:

    azure group delete "TestApp1-Development"

Quando solicitado, digite y e pressione enter para remover o ambiente de desenvolvimento e todos os seus recursos. Após alguns minutos, o comando retornará o seguinte:

    info:    group delete command OK

Em um prompt CLI, digite o seguinte para excluir os ambientes restantes:

    azure group delete "TestApp1-Test"
    azure group delete "TestApp1-Pre-Production"
  
### <a name="powershell"></a>PowerShell

A partir de um prompt de comando do Azure PowerShell (versão 1.01 ou superior), digite o comando a seguir para excluir o grupo de recursos e todo o seu conteúdo.    

    Remove-AzureRmResourceGroup -Name TestApp1-Development

Quando solicitado se você tiver certeza de que deseja remover o grupo de recursos, digite y, seguido da tecla enter.

Digite o seguinte para excluir os ambientes restantes:

    Remove-AzureRmResourceGroup -Name TestApp1-Test
    Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

### <a name="azure-portal"></a>Portal do Azure

1. No portal do Azure, navegue para grupos de recursos que você fez nas etapas anteriores. 
2. Selecione o grupo de recursos de desenvolvimento TestApp1 e clique em Excluir na lâmina de grupo de recursos de desenvolvimento de TestApp1. Um novo blade aparecerão. Insira o nome do grupo de recursos e clique no botão Excluir.
![Portal](./media/solution-dev-test-environments/rgdelete.png)
3. Exclua o teste-TestApp1 e TestApp1-pré-produção recurso agrupa da mesma maneira que você excluiu o grupo de recursos de desenvolvimento TestApp1.

Independentemente do método utilizado, os grupos de recurso e todos os recursos que eles contidos deixará de existir e você não mais será provoca despesas de cobranças para os recursos.  

Para minimizar o Azure despesas de utilização do recurso você provoca durante o desenvolvimento de aplicativos que você pode usar [automação Azure](automation/automation-intro.md) para agendar trabalhos:

- Parar máquinas virtuais no final de cada dia e reiniciá-los no início de cada dia.
- Exclua ambientes inteiros no final de cada dia e recriá-las no início de cada dia.
 
Agora que você já passou como é fácil criar, manter, excluir o desenvolvimento e os ambientes de teste, você pode aprender mais sobre o que apenas você fez ainda mais experiências com as etapas acima e ler as referências contidas neste artigo.

## <a name="next-steps"></a>Próximas etapas

- [Delegar controle administrativo](./active-directory/role-based-access-control-configure.md) para diferentes recursos em cada ambiente atribuindo Microsoft Azure AD grupos ou usuários a funções específicas que têm a capacidade de executar um subconjunto de operações em recursos do Azure.
- [Atribuir marcas de formatação](resource-group-using-tags.md) para os grupos de recursos para cada ambiente e/ou os recursos individuais. Você pode adicionar uma marca de "Ambiente" a seus grupos de recursos e defina seu valor para correspondem aos nomes de seu ambiente. Marcas podem ser particularmente úteis quando você precisa organizar recursos para gerenciamento ou de cobrança.
- Monitorar alertas e cobrança para recursos de grupo de recursos no [portal do Azure](https://portal.azure.com).
