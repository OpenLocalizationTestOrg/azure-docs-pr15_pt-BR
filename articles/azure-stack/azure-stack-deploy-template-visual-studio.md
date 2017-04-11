<properties
    pageTitle="Implantar modelos com o Visual Studio na pilha do Azure | Microsoft Azure"
    description="Saiba como implantar modelos com o Visual Studio na pilha do Azure."
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Implantar modelos na pilha de Azure usando o Visual Studio

Use o Visual Studio para implantar modelos de Gerenciador de recursos do Azure para o Azure pilha VDC.

Modelos do Gerenciador de recursos implantar e provisionar todos os recursos para seu aplicativo em uma operação única e coordenado.

1.  Abra a atualização de 2015 do Visual Studio 1.

2.  Clique em **arquivo**, clique em **novo**e, na caixa de diálogo **Novo projeto** , clique em **Grupo de recursos do Azure**.

3.  Insira um **nome** para o novo projeto e clique em **Okey**.

4.  Na caixa de diálogo **Selecionar modelo do Azure** , clique em **Máquina Virtual do Windows**e clique em **Okey**.

  Em seu novo projeto, você pode ver uma lista dos modelos disponíveis expandindo o nó de **modelos** no painel **Solution Explorer** .

5.  No painel **Solution Explorer** , clique com botão direito no nome do seu projeto, clique em **implantar**e clique em **Nova implantação**.

6.  Na caixa de diálogo **implantar ao grupo de recursos** , no menu suspenso **assinatura** , selecione sua assinatura do Microsoft Azure pilha.

7.  Na lista do **Grupo de recursos** , escolha um grupo de recursos existente ou crie um novo.

8.  Na lista **local do grupo de recursos** , escolha um local e, em seguida, clique em **implantar**.

9.  Na caixa de diálogo **Editar parâmetros** , insira valores para os parâmetros (que variam por modelo) e clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

[Implantar modelos com a linha de comando](azure-stack-deploy-template-command-line.md)
