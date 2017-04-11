<properties
    pageTitle="Conceder permissões de usuário às políticas de laboratório específico | Microsoft Azure"
    description="Saiba como conceder permissões de usuário às políticas de laboratório específico nos exercícios DevTest com base nas necessidades de cada usuário"
    services="devtest-lab,virtual-machines,visual-studio-online"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="tarcher"/>

# <a name="grant-user-permissions-to-specific-lab-policies"></a>Conceder permissões de usuário às políticas de laboratório específico

## <a name="overview"></a>Visão geral

Este artigo ilustra como usar o PowerShell para conceder aos usuários permissões para uma política de laboratório específico. Dessa maneira, podem ser aplicadas permissões com base nas necessidades de cada usuário. Por exemplo, você talvez queira conceder a capacidade de alterar as configurações de política de máquina virtual, mas não as políticas de custo de um usuário específico.

## <a name="policies-as-resources"></a>Políticas como recursos

Conforme discutido no artigo [Controle de acesso baseado em função do Azure](../active-directory/role-based-access-control-configure.md) , RBAC permite gerenciamento de acesso refinados de recursos para o Azure. Usando RBAC, você pode separar os deveres dentro de sua equipe DevOps e conceder somente a quantidade de acesso aos usuários que precisam para realizar seus trabalhos.

Nos exercícios DevTest, uma política é um tipo de recurso que permite a ação de RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Cada diretiva de laboratório é um recurso no tipo de recurso de política e pode ser atribuída como um escopo para uma função RBAC.

Por exemplo, para conceder aos usuários leitura/gravação permissão para a política de **Tamanhos de máquina virtual permitidos** , você criaria uma função personalizada que funciona com o **Microsoft.DevTestLab/labs/policySets/policies/** * ação e atribuir os usuários apropriados para esta função personalizada no escopo da * *Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Para saber mais sobre funções personalizadas RBAC, consulte o [controle de acesso de funções personalizadas](../active-directory/role-based-access-control-custom-roles.md).

##<a name="creating-a-lab-custom-role-using-powershell"></a>Criando uma função personalizada de laboratório usando o PowerShell
Para começar, você precisará ler o seguinte artigo, que mostrará como instalar e configurar os cmdlets do PowerShell do Azure: [https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre).

Depois que você configurou os cmdlets do PowerShell do Azure, você pode executar as seguintes tarefas:

- Lista todas as operações/ações para um provedor de recursos
- Ações de lista em uma função específica:
- Crie uma função personalizada

O seguinte script do PowerShell ilustra exemplos de como realizar estas tarefas:

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

##<a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Atribuir permissões a um usuário para uma política de específico usando funções personalizadas
Depois que você definiu suas funções personalizadas, você pode atribuí-las aos usuários. Para atribuir uma função personalizada para um usuário, você deve primeiro obter a **ID do objeto** representando esse usuário. Para fazer isso, use o cmdlet **Get-AzureRmADUser** .

No exemplo a seguir, a **ID do objeto** do usuário *SomeUser* é 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Quando você tiver o **ID do objeto** para o usuário e um nome de função personalizada, você pode atribuir função ao usuário com o cmdlet **New-AzureRmRoleAssignment** :

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/policies/AllowedVmSizesInLab

No exemplo anterior, a política de **AllowedVmSizesInLab** é usada. Você pode usar qualquer um dos seguintes políticas:

- MaxVmsAllowedPerUser
- MaxVmsAllowedPerLab
- AllowedVmSizesInLab
- LabVmsShutdown

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas

Uma vez que você concedeu permissões de usuário às políticas de laboratório específico, aqui estão algumas etapas a serem consideradas:

- [Acesso seguro a um laboratório](devtest-lab-add-devtest-user.md).

- [Definir políticas de laboratório](devtest-lab-set-lab-policy.md).

- [Criar um modelo de laboratório](devtest-lab-create-template.md).

- [Criar artefatos personalizados para suas VMs](devtest-lab-artifact-author.md).

- [Adicionar uma máquina virtual com artefatos para um laboratório](devtest-lab-add-vm-with-artifacts.md).
