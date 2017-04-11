<properties
  pageTitle="Gerenciando o acesso aos aplicativos usando o Azure AD |  Microsoft Azure"
  description="Descreve como o Active Directory do Azure permite que as organizações especificar os aplicativos aos quais cada usuário tem acesso."
  services="active-directory"
  documentationCenter=""
  authors="femila"
  manager="femila"
  editor=""/>

 <tags
  ms.service="active-directory"
  ms.workload="identity"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="10/13/2016"
  ms.author="femila"/>


# <a name="managing-access-to-apps"></a>Gerenciando o acesso aos aplicativos

Gerenciamento de acesso contínuo, avaliação de uso e relatórios continuam a ser um desafio depois de um aplicativo é integrado ao sistema de identidade da sua organização. Em muitos casos, os administradores de TI ou suporte técnico necessário realizar uma função ativa em andamento em gerenciar o acesso aos seus aplicativos. Às vezes, a atribuição é realizada por uma equipe de TI geral ou divisões. Muitas vezes, a decisão de atribuição destina-se a ser delegada para as decisões de negócios, que exigem aprovação antes de IT torna a atribuição.  Outras organizações investirem na integração com um existente identidades e acesso gerenciamento sistema automatizado, como o controle de acesso baseado em função (RBAC) ou o controle de acesso baseado em atributo (ABAC). A integração e o desenvolvimento de regra tendem a ser especializadas e caro. Monitoramento ou gerar relatórios sobre o gerenciamento de abordagem são seu próprio investimento separado, dispendioso e complexo.

## <a name="how-does-azure-active-directory-help"></a>Como o Active Directory do Azure ajuda?

 Azure AD compatível com gerenciamento de acesso extensivo para aplicativos configurados, permitindo que as organizações alcançar facilmente as políticas de acesso correto variando de atribuição automática, com base no atributo (cenários ABAC ou RBAC) através da delegação e incluindo gerenciamento de administrador. Com o Azure AD você pode facilmente obter políticas complexas, combinando vários modelos de gerenciamento de um único aplicativo e ainda pode usar novamente regras de gerenciamento entre aplicativos com os mesmos audiências.

 - [Adicionar novo ou aplicativos existentes](active-directory-sso-integrate-saas-apps.md)


 Atribuição de aplicativo do Azure AD se concentra em dois modos de atribuição principal:

- **Atribuição individual** Um administrador de TI com permissões de Administrador Global do diretório pode selecionar contas de usuário individual e conceder-lhes acesso ao aplicativo.
- **Atribuição baseada em grupo (paga Azure AD somente)** Um administrador de TI com permissões de Administrador Global do diretório pode atribuir um grupo para o aplicativo. Acesso de usuários específicos é determinado por sejam membros do grupo no momento em que tentarem acessar o aplicativo. Em outras palavras, um administrador efetivamente pode criar uma regra de atribuição informando "qualquer membro do grupo atribuído atual tem acesso ao aplicativo". Usando essa opção de atribuição, os administradores podem beneficiar qualquer uma das opções de gerenciamento de grupo do Azure AD, incluindo [grupos dinâmicos baseado em atributos](active-directory-accessmanagement-manage-groups.md), grupos de sistema externo (por exemplo, do Active Directory local ou DIATRABALHO) ou grupos gerenciadas pelo administrador ou auto-auto-atendimento gerenciados. Um único grupo pode ser atribuído facilmente para vários aplicativos, garantindo que aplicativos com afinidade de atribuição podem compartilhar as regras de atribuição, reduzindo a complexidade de gerenciamento geral. Observe esse grupo aninhado associações não são suportadas para atribuição baseada em grupo para aplicativos neste momento.

Usar esses modos de dois atribuição, os administradores podem obter qualquer abordagem de gerenciamento de atribuição desejável.

Com o Azure AD, uso e relatório de atribuição é totalmente integrado, permitindo que os administradores facilmente relatório sobre o estado da atribuição, erros de atribuição e uso par.

## <a name="complex-application-assignment-with-azure-ad"></a>Atribuição de aplicativo complexo com Azure AD

Considere a possibilidade de um aplicativo como o Salesforce. Em muitas organizações, Salesforce é usado principalmente pelas organizações de vendas e marketing. Muitas vezes, os membros da equipe de marketing têm altamente privilegiado acesso a equipe de vendas, enquanto os membros da equipe de vendas têm acesso limitado. Em muitos casos, uma ampla população de operadores de informações têm acesso restrito para o aplicativo. Exceções a essas regras complicam. Geralmente é o prerogative das equipes de liderança de marketing ou vendas para conceder acesso a um usuário ou alterar suas funções independentemente dessas regras genéricas.

Com o Azure AD, aplicativos como o Salesforce podem ser pré-configurado para logon único (SSO) e provisionamento automatizado. Depois que o aplicativo é configurado, um administrador pode levar a ação única para criar e atribuir grupos apropriados. Neste exemplo, um administrador pode executar as atribuições a seguir:

- [Grupos dinâmicos](active-directory-accessmanagement-manage-groups.md) podem ser definidos para representar automaticamente todos os membros das equipes de vendas e marketing usando atributos como departamento ou função:

    - Todos os membros de grupos de marketing seriam atribuídos à função "marketing" no Salesforce

    - Todos os membros da equipe de vendas grupos seriam atribuídos à função "venda" no Salesforce. Um refinamento adicional poderia usar vários grupos que representam as equipes de vendas regionais atribuídas às funções de Salesforce diferentes.

- Para habilitar o mecanismo de exceção, um grupo de autoatendimento poderia ser criado para cada função. Por exemplo, o grupo "Exceção de marketing a equipe de vendas" pode ser criado como um grupo pessoal. O grupo pode ser atribuído à função de marketing de Salesforce e pode ser feita a equipe de marketing liderança proprietários. Membros da equipe do liderança marketing podem adicionar ou remover usuários, definir uma política de associação, ou até mesmo aprovar ou negar solicitações de usuários individuais para ingressar. Isso é suportado por meio de uma experiência de trabalho apropriado de informações que não exige treinamento especializado para proprietários ou membros.

Nesse caso, todos os usuários atribuídos poderiam ser automaticamente provisionados à equipe de vendas, conforme elas são adicionadas para grupos diferentes que sua atribuição de função será atualizada no Salesforce. Os usuários poderão descobrir e acessar Salesforce através do painel de acesso de aplicativo do Microsoft, clientes do Office web, ou até mesmo por meio de navegação para sua página de login de Salesforce organizacional. Os administradores poderão facilmente exibir status de atribuição e de uso usando Azure AD de relatório.

Os administradores podem utilizam [acesso condicional do Azure AD](active-directory-conditional-access.md) para definir as políticas de acesso para funções específicas. Essas políticas podem incluir se acesso é permitido fora ambiente corporativo e até mesmo requisitos autenticação multifator ou dispositivo para obter acesso em vários casos.

## <a name="how-can-i-get-started"></a>Como posso podem começar a?

Primeiro, se você não estiver usando AD Azure e você for um administrador de TI:

 - [Experimente!](https://azure.microsoft.com/trial/get-started-active-directory/) -Você pode inscrever-se para uma avaliação gratuita de 30 dias hoje e implantar sua solução de nuvem primeira em menos de 5 minutos usando esse link

Os recursos do Azure AD que habilitar o compartilhamento de conta incluem:

- [Atribuição de grupo](active-directory-accessmanagement-self-service-group-management.md)
- Adicionando aplicativos ao Azure AD
- Introdução ao atribuição
- Atribuição de aplicativo perguntas Frequentes
- [Painel/relatórios de uso do aplicativo](active-directory-passwords-get-insights.md)

## <a name="where-can-i-learn-more"></a>Onde posso saber mais?

- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
- [Protegendo aplicativos com acesso condicional](active-directory-conditional-access.md)
- [Gerenciamento de grupo de autoatendimento/SSAA](active-directory-accessmanagement-self-service-group-management.md)
