<properties
    pageTitle="Implantar o conjunto de escala de máquina Virtual usando o Visual Studio | Microsoft Azure"
    description="Implantar conjuntos de escala de máquina Virtual usando o Visual Studio e um modelo do Gerenciador de recursos"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/13/2016"
    ms.author="guybo"/>

# <a name="deploy-virtual-machine-scale-set-using-visual-studio"></a>Implantar o conjunto de escala de máquina Virtual usando o Visual Studio

Este artigo mostra como implantar um Azure Máquina Virtual escala definida usando uma implantação de grupo do Visual Studio recurso.


[Conjuntos de escala de máquina Virtual do Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) são um recurso de computação do Azure para implantar e gerenciar um conjunto de máquinas virtuais semelhantes com opções facilmente integradas para dimensionar automaticamente e balanceamento de carga. Você pode provisionar e implantar conjuntos de escala de máquina virtual usando [modelos do Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates). Modelos de BRAÇO podem ser implantados usando o restante do Azure CLI, PowerShell e também diretamente do Visual Studio. Visual Studio fornece um conjunto de exemplo modelos que podem ser implantados como parte de um projeto de implantação de grupo de recursos do Azure.

Grupo de recursos do Azure implantações é uma maneira de agrupar e publicar um conjunto de recursos Azure relacionados em uma operação de implantação único. Você pode saber mais sobre eles aqui: [Criando e implantando grupos de recursos Azure por meio do Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Pré-requisitos

Para começar a implantar conjuntos de escala de máquina virtual no Visual Studio, você precisa do seguinte:

- Visual Studio 2013 ou 2015
- Azure SDK 2.7, 2,8 ou 2,9

Observação: Estas instruções pressupõem que você estiver usando o Visual Studio 2015 com [Azure SDK 2,8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Criando um projeto

1. Criar um novo projeto no Visual Studio 2015 escolhendo **arquivo | Novo | Projeto**.

    ![Arquivo novo][file_new]

2. Em **Visual c# | Nuvem**, escolha **Gerenciador de recursos do Azure** para criar um projeto para implantar um modelo de ARM.

    ![Criar projeto][create_project]

3.  Na lista de modelos, selecione o modelo de conjunto de escala do Windows Máquina Virtual ou Linux.

    ![Selecione modelo][select_Template]

4. Uma vez criado o projeto você verá scripts de implantação do PowerShell, um modelo de Gerenciador de recursos do Azure e um arquivo de parâmetro para o conjunto de escala de máquina Virtual.

    ![Gerenciador de soluções][solution_explorer]

## <a name="customize-your-project"></a>Personalizar seu projeto

Agora você pode editar o modelo para personalizá-lo para necessidades de seu aplicativo, como adicionar propriedades de extensão de máquina virtual ou editar regras de balanceamento de carga. Por padrão, os modelos de conjunto de escala de máquina virtual são configurados para implantar a extensão de AzureDiagnostics que torna mais fácil adicionar regras de escala automática. Ele também implanta um balanceador de carga com um endereço IP público, configurado com regras de NAT de entrada que permitem que você se conecta às instâncias de máquina virtual com SSH (Linux) ou RDP (Windows) – o intervalo de portas de front-end começa em 50000, que significa no caso de Linux, se você SSH à porta 50000 do endereço IP público (ou nome de domínio) serão roteados para porta 22 da máquina virtual primeiro no conjunto de escala. Conectando a porta 50001 será roteado para porta 22 da segunda máquina virtual e assim por diante.

 Uma boa maneira de editar seus modelos com o Visual Studio é usar a estrutura de tópicos de JSON para organizar os parâmetros, variáveis e recursos. Com uma compreensão do esquema de Visual Studio pode apontar erros no seu modelo antes de implantá-lo.

![JSON Explorer][json_explorer]

## <a name="deploy-the-project"></a>Implantar o projeto

6. Implante o modelo de ARM Azure para criar o recurso de conjunto de escala de máquina virtual. Clique com o botão direito no nó do projeto, escolha **implantar | Nova implantação**.

    ![Implantar o modelo][5deploy_Template]

7. Selecione sua assinatura na caixa de diálogo "Implantar ao grupo de recursos".

    ![Implantar o modelo][6deploy_Template]

8. A partir daqui, você também pode criar um novo grupo de recursos do Azure para implantar o seu modelo.

    ![Novo grupo de recursos][new_resource]

9. Em seguida, selecione o botão **Editar parâmetros** para inserir parâmetros que serão passados para seu modelo, determinados valores, como o nome de usuário e senha para o sistema operacional são necessárias para criar a implantação. Se você não tiver o PowerShell ferramentas para Visual Studio instalada, é recomendável verificar "salvar senhas" para evitar um prompt de linha de comando do PowerShell oculto ou use [keyvault de suporte](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).

    ![Editar parâmetros][edit_parameters]

10. Agora, clique em **implantar**. Na janela de **saída** mostrará o progresso de implantação. Observe que o a ação está executando o script de **Implantação AzureResourceGroup.ps1** .

    ![Janela de saída][output_window]

## <a name="exploring-your-vm-scale-set"></a>Explorando seu conjunto de escala de máquina virtual

Uma vez concluída a implantação, você pode exibir o novo conjunto de escala de máquina virtual no Visual Studio **Nuvem Explorer** (Atualizar lista). Nuvem Explorer permite que você gerencie recursos Azure no Visual Studio ao desenvolvimento de aplicativos. Você também pode exibir o seu conjunto de escala de máquina virtual no [Portal do Azure](https://portal.azure.com) e [Azure Resource Explorer](https://resources.azure.com/).

![Explorador de nuvem][cloud_explorer]

 O portal fornece a melhor maneira de gerenciar sua infraestrutura Azure com um navegador da web, enquanto o Azure Resource Explorer fornece uma maneira fácil de explorer visualmente e depurar Azure recursos, dando uma janela até a exibição"instância" e também mostrando comandos do PowerShell para os recursos que você está analisando. Enquanto são conjuntos de escala de máquina virtual na visualização, Resource Explorer mostrará a maioria dos detalhes para os conjuntos de escala de máquina virtual.

## <a name="next-steps"></a>Próximas etapas

Assim que tiver implantado com êxito conjuntos de escala de máquina virtual por meio do Visual Studio que você pode personalizar seu projeto para atender às suas necessidades de aplicativo. Por exemplo Configurando escala automática adicionando um recurso de ideias, adicionando infraestrutura ao seu modelo como autônomo VMs ou implantando aplicativos usando a extensão de script personalizado. Uma boa fonte de modelos de exemplo pode ser encontrada no repositório [Modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates) GitHub (procure por "vmss").

[file_new]: ./media/virtual-machine-scale-sets-vs-create/1-FileNew.png
[create_project]: ./media/virtual-machine-scale-sets-vs-create/2-CreateProject.png
[select_Template]: ./media/virtual-machine-scale-sets-vs-create/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machine-scale-sets-vs-create/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machine-scale-sets-vs-create/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/6-DeployTemplate.png
[new_resource]: ./media/virtual-machine-scale-sets-vs-create/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machine-scale-sets-vs-create/8-EditParameter.png
[output_window]: ./media/virtual-machine-scale-sets-vs-create/9-Output.png
[cloud_explorer]: ./media/virtual-machine-scale-sets-vs-create/12-CloudExplorer.png
