<properties
    pageTitle="Gerenciar permissões para recursos por usuário na pilha de Azure (administrador de serviço e locatário) | Microsoft Azure"
    description="Como administrador do serviço ou Locatário, saiba como gerenciar permissões para recursos por usuário."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="manage-user-permissions"></a>Gerenciar permissões de usuário

Um usuário no Azure pilha pode ser um leitor, proprietário ou colaborador para cada instância de uma assinatura, grupo de recursos ou serviço. Por exemplo, usuário pode ter permissões de leitor para 1 de assinatura, mas tem permissões de proprietário para 7 de máquina Virtual.

-   Reader: O usuário pode exibir tudo, mas não pode fazer qualquer alteração.

-   Colaborador: Usuário pode gerenciar tudo, exceto o acesso aos recursos.

-   Proprietário: Usuário pode gerenciar tudo, inclusive acesso aos recursos.


## <a name="set-access-permissions-for-a-user"></a>Definir permissões de acesso para um usuário

1.  Entrar com uma conta que tenha permissões de proprietário para o recurso que você deseja gerenciar.

2.  Na lâmina para o recurso, clique no ícone de **acesso** ![](media/azure-stack-manage-permissions/image1.png).

3.  Na lâmina **usuários** , clique em **funções**.

4.  Na lâmina **funções** , clique em **Adicionar** para adicionar permissões do usuário.

## <a name="next-steps"></a>Próximas etapas

[Adicionar um locatário pilha do Azure](azure-stack-add-new-user-aad.md)
