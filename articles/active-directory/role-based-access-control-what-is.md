<properties
    pageTitle="Controle de acesso baseado em função | Microsoft Azure"
    description="Começar a usar o gerenciamento de acesso com controle de acesso baseado em função Azure no Portal do Azure. Use atribuições de função para atribuir permissões no diretório."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="get-started-with-access-management-in-the-azure-portal"></a>Introdução ao gerenciamento de acesso no portal do Azure

Empresas orientadas a segurança devem se concentrar em concedendo as permissões exatas que precisam de funcionários. Permissões de muitos expõe uma conta a ataques. Permissões de poucos significa que os funcionários não conseguem acessar o seu trabalho com eficiência. Azure controle de acesso baseado em função (RBAC) ajuda a resolver esse problema, proporcionando gerenciamento de acesso refinados do Azure.

Usando RBAC, você pode separar os deveres dentro de sua equipe e conceder somente a quantidade de acesso aos usuários que precisam para realizar seus trabalhos. Em vez de concedendo todos irrestrito permissões na sua assinatura do Azure ou recursos, você pode permitir que somente determinadas ações. Por exemplo, use RBAC para permitir que um funcionário gerenciar máquinas virtuais em uma assinatura, enquanto outro pode gerenciar bancos de dados SQL dentro da mesma assinatura.

## <a name="basics-of-access-management-in-azure"></a>Noções básicas de gerenciamento de acesso no Azure
Cada assinatura Azure está associada um diretório do Azure Active Directory (AD). Usuários, grupos e aplicativos daquele diretório podem gerenciar recursos na assinatura do Azure. Atribua esses direitos de acesso usando o portal do Azure, ferramentas de linha de comando Azure e APIs de gerenciamento do Azure.

Conceder acesso atribuindo a função RBAC adequada para usuários, grupos e aplicativos em um determinado escopo. O escopo de uma atribuição de função pode ser uma assinatura, um grupo de recursos ou um único recurso. Uma função atribuída em um escopo pai também concede acesso aos filhos contidos nela. Por exemplo, um usuário com acesso a um grupo de recursos pode gerenciar todos os recursos que ele contém, como sites, máquinas virtuais e sub-redes.

![Relação entre elementos do Active Directory do Azure - diagrama](./media/role-based-access-control-what-is/rbac_aad.png)

A função RBAC que você atribui determina quais recursos do usuário, o grupo ou o aplicativo pode gerenciar dentro desse escopo.

## <a name="built-in-roles"></a>Funções internas
RBAC Azure tem três funções básicas que se aplicam a todos os tipos de recursos:

- **Proprietário** tem acesso total a todos os recursos, inclusive o direito de acesso de representante que outras pessoas.
- **Colaborador** pode criar e gerenciar todos os tipos de recursos Azure, mas não pode conceder acesso a outras pessoas.
- **Leitor** pode exibir recursos Azure existentes.

O restante das funções RBAC no Azure permitir o gerenciamento de recursos específicos do Azure. Por exemplo, a função Colaborador de máquina Virtual permite ao usuário criar e gerenciar máquinas virtuais. Ele não lhes conceder acesso à rede virtual ou sub-rede que se conecta a máquina virtual.

[Funções internas RBAC](role-based-access-built-in-roles.md) lista as funções disponíveis no Azure. Especifica as operações e escopo que cada função interna concede aos usuários. Se você estiver procurando para definir suas próprias funções para controlar ainda mais, consulte como criar [funções personalizadas no Azure RBAC](role-based-access-control-custom-roles.md).

## <a name="resource-hierarchy-and-access-inheritance"></a>Herança de hierarquia e acesso de recursos
- Cada **assinatura** no Azure pertence a apenas um diretório.
- Cada **grupo de recursos** pertence a apenas uma assinatura.
- Cada **recurso** pertence a apenas um grupo de recursos.

Acesso concedido em escopos pai é herdado em escopos filho. Por exemplo:

- Você atribuir a função de leitor a um grupo do Azure AD no escopo assinatura. Os membros do grupo podem exibir cada grupo de recursos e recursos na assinatura.
- Você pode atribuir a função Colaborador a um aplicativo no escopo do grupo de recursos. Ele pode gerenciar recursos de todos os tipos desse grupo de recursos, mas não outros grupos de recursos na assinatura.

## <a name="azure-rbac-vs-classic-subscription-administrators"></a>Azure RBAC versus administradores de assinatura clássico
Administradores de assinatura clássico e co-administradores têm acesso total à assinatura Azure. Eles podem gerenciar recursos usando o [portal do Azure](https://portal.azure.com) com APIs do Gerenciador de recursos do Azure ou o modelo de implantação clássico [portal clássico Azure](https://manage.windowsazure.com) e o Azure. No modelo de RBAC, administradores clássicos são atribuídos a função de proprietário no escopo assinatura.

Somente o portal do Azure e APIs do Gerenciador de recursos novos do Azure suportam RBAC do Azure. Usuários e aplicativos que estejam atribuídos funções RBAC não podem usar o portal de gerenciamento clássico e o modelo de implantação clássico Azure.

## <a name="authorization-for-management-vs-data-operations"></a>Autorização para gerenciamento versus operações de dados
Azure RBAC só oferece suporte a operações de gerenciamento dos recursos do Azure no portal do Azure e APIs do Gerenciador de recursos do Azure. Ele não pode autorizar todas as operações de nível de dados para recursos Azure. Por exemplo, você pode autorizar alguém para gerenciar contas de armazenamento, mas não para a blobs ou tabelas dentro de uma conta de armazenamento não. Da mesma forma, um banco de dados do SQL pode ser gerenciado, mas não as tabelas dentro dela.

## <a name="next-steps"></a>Próximas etapas
- Introdução ao [controle de acesso baseado em função no portal do Azure](role-based-access-control-configure.md).
- Ver as [funções internas RBAC](role-based-access-built-in-roles.md)
- Definir suas próprias [funções personalizadas no Azure RBAC](role-based-access-control-custom-roles.md)
