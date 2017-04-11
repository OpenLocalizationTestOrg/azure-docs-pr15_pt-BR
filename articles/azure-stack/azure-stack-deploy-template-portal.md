<properties
    pageTitle="Implantar modelos com o portal na pilha do Azure | Microsoft Azure"
    description="Saiba como usar o portal de pilha do Azure para implantar modelos."
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

# <a name="deploy-templates-using-the-azure-stack-portal"></a>Implantar modelos usando o portal de pilha do Azure

Use o portal para implantar modelos de Gerenciador de recursos do Azure para o Azure pilha VDC.

Modelos do Gerenciador de recursos implantar e provisionar todos os recursos para seu aplicativo em uma operação única e coordenado.

1.  Faça logon no portal do, clique em **novo**, clique em **personalizado**e, em seguida, clique em **implantação do modelo**.

2.  Clique em **Editar modelo**, e em seguida, cole o código de modelo JSON na lâmina e clique em **Salvar**.

3.  Clique em **Editar parâmetros**, digite valores para os parâmetros listados e clique em **Okey**.

4.  Clique em **assinatura**, escolha a assinatura que você deseja usar e clique em **Okey**.

5.  Clique em **grupo de recursos**, escolha um grupo de recursos existente ou crie um novo e clique em **Okey**.

6.  Clique em **criar**. Um novo bloco no painel rastreia o progresso da sua implantação do modelo.

## <a name="next-steps"></a>Próximas etapas

[Implantar modelos com o PowerShell](azure-stack-deploy-template-powershell.md)
