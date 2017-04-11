<properties
    pageTitle="Azure AD e aplicativos: atribuindo grupos para um aplicativo | Microsoft Azure"
    description="Como implementar a atribuição de grupo para aplicativos do Azure."
    services="active-directory"
    documentationCenter=""
    authors="IHenkel"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/03/2015"
    ms.author="inhenk"/>

# <a name="azure-ad-and-applications-assigning-groups-to-an-application"></a>Azure AD e aplicativos: atribuindo grupos para um aplicativo
Antes de atribuir usuários e grupos para um aplicativo, você deve exigir atribuição de usuário. Para saber como exigir a atribuição de usuário, consulte o artigo [Exigindo a atribuição de usuário](active-directory-applications-guiding-developers-requiring-user-assignment.md) .

Este artigo pressupõe que você já tiver criado grupos no active directory que você está usando para este aplicativo.

## <a name="assigning-groups-to-an-application"></a>Atribuindo grupos para um aplicativo
1. Faça logon no portal do Azure com uma conta de administrador.
2. Clique no item de **Todos os itens** no menu principal.
3. Escolha o diretório que você está usando para o aplicativo.
4. Clique na guia **aplicativos** .
5. Selecione o aplicativo na lista de aplicativos associados a este diretório.
6. Clique na guia **Usuários e grupos** .
7. Filtre a lista de grupos do active directory usando a lista suspensa de **grupos** .
8. Selecione o grupo.
9. Clique em **ATRIBUIR**.
10. Clique em **Sim** quando solicitado.

## <a name="next-steps"></a>Próximas etapas
[AZURE.INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
