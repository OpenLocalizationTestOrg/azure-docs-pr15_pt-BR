<properties
    pageTitle="Gerenciando o acesso aos recursos com grupos do Active Directory do Azure | Microsoft Azure"
    description="Como usar grupos no Active Directory do Azure para gerenciar o acesso de usuário locais e aplicativos em nuvem e recursos."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""
/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="curtand"/>


# <a name="managing-access-to-resources-with-azure-active-directory-groups"></a>Gerenciando o acesso aos recursos com grupos do Active Directory do Azure

Azure Active Directory (AD Azure) é uma solução de gerenciamento abrangente para identidades e acesso que fornece um conjunto robusto de recursos para gerenciar o acesso a locais e aplicativos de nuvem e recursos incluindo serviços online da Microsoft como o Office 365 e um mundo de aplicativos não - Microsoft SaaS. Este artigo fornece uma visão geral, mas se você quiser começar a usar grupos Azure AD agora, siga as instruções em [grupos de segurança Gerenciando no Azure AD](active-directory-accessmanagement-manage-groups.md). Se você quiser ver como você pode usar o PowerShell para gerenciar grupos do Azure Active directory, você pode ler mais em [cmdlets de visualização do Azure Active Directory para gerenciamento de grupo](active-directory-accessmanagement-groups-settings-v2-cmdlets.md).


> [AZURE.NOTE] Para usar o Active Directory do Azure, é necessário uma conta do Azure. Se você não tiver uma conta, você pode [inscrever-se para uma conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).


No Azure AD, um dos principais recursos é a capacidade de gerenciar o acesso aos recursos. Esses recursos podem fazer parte do diretório, como no caso de permissões para gerenciar objetos por meio de funções do diretório ou recursos externos para o diretório, como aplicativos SaaS, os serviços do Azure e sites do SharePoint ou em recursos de local. Há quatro maneiras de que um usuário pode ser atribuído direitos de acesso a um recurso:


1. Atribuição direta

    Os usuários podem ser atribuídos diretamente a um recurso pelo proprietário do recurso.

2. Associações de grupo

    Um grupo pode ser atribuído a um recurso pelo proprietário do recurso e por isso, concedendo os membros do acesso ao recurso a esse grupo. Membros do grupo, em seguida, podem ser gerenciado pelo proprietário do grupo. Na verdade, o proprietário do recurso delegue a permissão para atribuir usuários aos seus recursos para o proprietário do grupo.

3. Baseada em regra

    O proprietário do recurso pode usar uma regra para expressar quais usuários devem ser atribuídos acesso a um recurso. O resultado da regra depende dos atributos usados na regra e seus valores para usuários específicos, e por isso, o proprietário do recurso efetivamente delegue à direita para gerenciar o acesso aos seus recursos para a fonte confiável para os atributos que são usados na regra. O proprietário do recurso ainda gerencia a regra e determina quais atributos e valores fornecem acesso aos seus recursos.

4. Autoridade externa

    O acesso a um recurso é derivado de uma fonte externa; Por exemplo, um grupo que está sincronizado de uma fonte autorizada como um diretório local ou um aplicativo de SaaS como dia de trabalho. O proprietário do recurso atribui o grupo para fornecer acesso ao recurso e fonte externa gerencia os membros do grupo.

  ![Visão geral de diagrama de gerenciamento de acesso](./media/active-directory-access-management-groups/access-management-overview.png)


## <a name="watch-a-video-that-explains-access-management"></a>Assista a um vídeo que explica o gerenciamento de acesso

Você pode assistir a um vídeo curto que explica mais sobre isto:

**Azure AD: Introdução a associação dinâmica para grupos**

> [AZURE.VIDEO azure-ad--introduction-to-dynamic-memberships-for-groups]

## <a name="how-does-access-management-in-azure-active-directory-work"></a>Como acessar o gerenciamento no trabalho Azure Active Directory?
No centro do Azure AD solução de gerenciamento de acesso é o grupo de segurança. Usar um grupo de segurança para gerenciar o acesso aos recursos é um paradigma conhecido, que permite uma maneira flexível e fácil de entender fornecer acesso a um recurso para o grupo pretendido de usuários. O proprietário do recurso (ou o administrador do diretório) pode atribuir um grupo para fornecer um determinado acesso direita para os recursos que eles possuem. Os membros do grupo serão fornecidos o acesso e o proprietário do recurso pode delegar o direito para gerenciar a lista de membros de um grupo para outra pessoa, como um gerente do departamento ou um administrador de suporte técnico.

![Diagrama de gerenciamento de acesso do Azure Active Directory](./media/active-directory-access-management-groups/active-directory-access-management-works.png)

O proprietário de um grupo também pode disponibilizar esse grupo para solicitações de autoatendimento. Isso, um usuário final pode pesquisar e localizar o grupo e fazer com que uma solicitação para ingressar, efetivamente buscando permissão para acessar os recursos que são gerenciados por meio do grupo. O proprietário do grupo pode configurar o grupo para que as solicitações de junção serão aprovadas automaticamente ou exigem aprovação do proprietário do grupo. Quando um usuário faz uma solicitação para ingressar em um grupo, a solicitação de associação é encaminhada para os proprietários do grupo. Se um dos proprietários aprovar a solicitação, o usuário solicitando é notificado e o usuário está associado ao grupo. Se um dos proprietários nega a solicitação, o usuário solicitando é notificado mas não ingressar no grupo.


## <a name="getting-started-with-access-management"></a>Introdução ao gerenciamento de acesso
Pronto para começar? Você deve experimentar algumas das tarefas básicas que você pode fazer com grupos Azure AD. Use esses recursos para fornecer acesso especializado para diferentes grupos de pessoas para recursos diferentes em sua organização. Uma lista de etapas básicas a primeira estão listadas abaixo.

* [Criar uma regra simple para configurar associações dinâmicas para um grupo](active-directory-accessmanagement-manage-groups.md#how-can-i-manage-the-membership-of-a-group-dynamically)

* [Usar um grupo para gerenciar o acesso aos aplicativos SaaS](active-directory-accessmanagement-group-saasapps.md)

* [Disponibilizar um grupo para o usuário final autoatendimento](active-directory-accessmanagement-self-service-group-management.md)

* [Sincronização de um grupo de locais no Azure usando o Azure AD Connect](active-directory-aadconnect.md)

* [Gerenciar proprietários de um grupo](active-directory-accessmanagement-managing-group-owners.md)


## <a name="next-steps-for-access-management"></a>Próximas etapas para o gerenciamento de acesso
Agora que você tenha compreendido as Noções básicas de gerenciamento de acesso, aqui estão alguns recursos avançados de adicionais disponíveis no Active Directory do Azure para gerenciar o acesso aos seus aplicativos e recursos.

* [Usando atributos para criar regras avançadas](active-directory-accessmanagement-groups-with-advanced-rules.md)

* [Gerenciando grupos de segurança no Azure AD](active-directory-accessmanagement-manage-groups.md)

* [Configurar grupos dedicados no Azure AD](active-directory-accessmanagement-dedicated-groups.md)

* [Referência de Graph API para grupos](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#GroupFunctions)

* [Azure cmdlets do Active Directory para definir as configurações de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
