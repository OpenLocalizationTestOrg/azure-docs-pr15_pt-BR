<properties
    pageTitle="Configurando o Active Directory do Azure para o gerenciamento de acesso do aplicativo de autoatendimento | Microsoft Azure"
    description="Gerenciamento de grupo de autoatendimento permite que os usuários criar e gerenciar grupos de segurança ou grupos do Office 365 no Active Directory do Azure e oferece aos usuários a possibilidade de grupo de segurança de solicitação ou associações de grupo do Office 365"
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
    ms.topic="get-started-article"
    ms.date="08/10/2016"
    ms.author="curtand"/>

# <a name="setting-up-azure-active-directory-for-self-service-group-management"></a>Configurar o Active Directory do Azure para gerenciamento de grupo de autoatendimento

Gerenciamento de grupo de autoatendimento permite aos usuários criar e gerenciar grupos de segurança ou grupos do Office 365 no Azure Active Directory (AD Azure). Os usuários também podem solicitar o grupo de segurança ou associações de grupo do Office 365 e, em seguida, o proprietário do grupo pode aprovar ou negar associação. Dessa maneira, controle diária de associações de grupo pode ser delegado para as pessoas que entendam o contexto de negócios para que a participação. Recursos de gerenciamento de grupo de autoatendimento estão disponíveis apenas para os grupos de segurança e grupos do Office 365, mas não para grupos de segurança habilitados para email ou listas de distribuição.

Gerenciamento de grupo de autoatendimento atualmente consiste em dois cenários essenciais: delegado gerenciamento de grupo e grupo pessoal.

- **Delegado gerenciamento de grupo** 
   um exemplo é um administrador que está gerenciando o acesso a um aplicativo de SaaS que a empresa está usando. Gerenciar esses direitos de acesso está se tornando complicado, para que esse administrador solicita o proprietário da empresa para criar um novo grupo. O administrador atribui acesso para o aplicativo para o novo grupo e adiciona ao grupo todas as pessoas que já acessando para o aplicativo. Proprietário da empresa, em seguida, pode adicionar mais usuários e esses usuários são provisionados automaticamente para o aplicativo. Proprietário da empresa não precisa esperar o administrador para gerenciar o acesso dos usuários. Se o administrador conceder as mesmas permissões para um gerente de um grupo de negócios diferentes, essa pessoa também pode gerenciar acesso para seus próprios usuários. Proprietário da empresa nem o gerente pode exibir ou gerenciar uns dos outros usuários. O administrador ainda pode ver todos os usuários que têm acesso ao aplicativo e direitos de acesso do bloco se necessário.

- **Gerenciamento de grupo de autoatendimento** 
   um exemplo desse cenário é dois usuários que têm sites do SharePoint Online que eles configurados de maneira independente. Eles desejam dar acesso de equipes uns dos outros aos seus sites. Para fazer isso, eles podem criar um grupo no Azure AD e no SharePoint Online, cada um deles seleciona esse grupo para fornecer acesso aos seus sites. Quando alguém quer acesso, eles solicitação-los do painel de acesso, e depois da aprovação obtêm acesso a ambos os sites do SharePoint Online automaticamente. Posteriormente, um deles decide que todas as pessoas acessando o site também devem obter acesso a um determinado aplicativo SaaS. O administrador do aplicativo SaaS pode adicionar direitos de acesso para o aplicativo para o site do SharePoint Online. Daí em seguida diante, todas as solicitações que tiverem aprovadas fornece acesso para os dois sites do SharePoint Online e também para este aplicativo SaaS.

## <a name="making-a-group-available-for-end-user-self-service"></a>Disponibilizar um grupo para o usuário final autoatendimento

1. No [portal de clássico Azure](https://manage.windowsazure.com), abra seu diretório Azure AD.

2. Na guia **Configurar** , defina **o gerenciamento de grupo delegado** para ativado.

3. Configurar **os usuários podem criar grupos de segurança** ou **os usuários podem criar grupos do Office** como ativado.

Quando **que os usuários podem criar grupos de segurança** está habilitado, todos os usuários no seu diretório têm permissão para criar novos grupos de segurança e adicionar membros a esses grupos. Esses novos grupos também aparecerão no painel de acesso para todos os outros usuários. Se a configuração de política no grupo permitir, outros usuários podem criar solicitações para ingressar esses grupos. Se **os usuários podem criar grupos de segurança** estiver desabilitada, os usuários não podem criar grupos e não é possível alterar grupos existentes para que eles são um proprietário. No entanto, ainda podem gerenciar os membros desses grupos e aprovar solicitações de outros usuários para participar de seus grupos.

Você também pode usar **os usuários quem podem usar autoatendimento para grupos de segurança** alcançar um controle de acesso mais refinado sobre o gerenciamento de grupo de autoatendimento para seus usuários. Quando **que os usuários podem criar grupos** está habilitado, todos os usuários no seu diretório são permitidos para criar novos grupos e adicionar membros a esses grupos. Definindo também **usuários quem podem usar autoatendimento para grupos de segurança** de alguns, está restringindo gerenciamento a apenas um grupo limitado de usuários do grupo. Quando essa opção estiver definida para algumas, você deve adicionar usuários ao grupo SSGMSecurityGroupsUsers antes de criar novos grupos e adicionar membros a elas. Definindo **usuários quem podem usar autoatendimento para grupos de segurança** para todos, você pode habilitar todos os usuários no seu diretório para criar novos grupos.

Você também pode usar a caixa de **grupo que pode usar o autoatendimento para grupos de segurança** para especificar um nome personalizado para um grupo cujos membros podem usar autoatendimento.

## <a name="additional-information"></a>Informações adicionais

Estes artigos fornecem informações adicionais sobre Active Directory do Azure.

* [Gerenciando o acesso aos recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)

* [Azure cmdlets do Active Directory para definir as configurações de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)

* [O que é Azure Active Directory?](active-directory-whatis.md)

* [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
