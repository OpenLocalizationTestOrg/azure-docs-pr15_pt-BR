<properties
    pageTitle="Sincronização do Azure AD Connect: Noções básicas sobre usuários e contatos | Microsoft Azure"
    description="Explica os usuários e contatos em sincronização do Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understanding-users-and-contacts"></a>Sincronização do Azure AD Connect: Noções básicas sobre usuários e contatos

Há vários motivos diferentes por que você teria várias florestas do Active Directory e há diversas topologias de implantação diferentes. Modelos comuns incluem uma implantação de conta-recurso e GAL sync'ed florestas após uma fusão e aquisição. Mas mesmo se houver modelos puros, modelos híbridos são comuns também. A configuração padrão na sincronização do Azure AD Connect não assume qualquer modelo específico, mas dependendo de como a correspondência de usuário foi selecionado na guia de instalação, diferentes comportamentos podem ser observados.

Neste tópico, veremos como a configuração padrão se comporta em determinadas topologias. Veremos a configuração e o Editor de regras de sincronização podem ser usado para examinar a configuração.

Existem algumas regras gerais que assume a configuração:

- Independentemente de qual ordem podemos importar da origem diretórios ativa, o resultado final deve ser sempre a mesma.
- Uma conta ativa sempre será contribuir informações de entrada, incluindo **userPrincipalName** e **sourceAnchor**.
- Uma conta desabilitada será contribuir userPrincipalName e sourceAnchor, a menos que ele seja uma caixa de correio vinculada, se não houver nenhuma conta ativa a ser localizado.
- Uma conta com uma caixa de correio vinculada nunca será usada para userPrincipalName e sourceAnchor. Será considerado que uma conta ativa será encontrada mais tarde.
- Um objeto de contato pode ser provisionado ao Azure AD como um contato ou como um usuário. Você realmente não sabe até que todas as florestas do Active Directory de origem foram processadas.

## <a name="contacts"></a>Contatos

Ter contatos que representa um usuário em uma floresta diferente é comum após uma fusão e aquisição onde uma solução GALSync é ponte duas ou mais florestas do Exchange. O objeto de contato está ingressando sempre de espaço do conector para o metaverso usando o atributo de email. Se já houver um objeto de contato ou usuário com o mesmo endereço de email, os objetos são Unidos. Isso é configurado na regra **do AD – contato ingressar**. Também há uma regra chamada **do AD – contato comuns** com um fluxo de atributo para o metaverso atributo **sourceObjectType** com o constante **contato**. Essa regra tem precedência muito pouca isso se qualquer objeto do usuário está associado ao mesmo objeto metaverso, em seguida, a regra **do AD – usuário comuns** será contribuir o valor de usuário para esse atributo. Com essa regra, esse atributo terá o valor contato se nenhum usuário ingressou e o valor de usuário se pelo menos um usuário foi encontrado.

Para provisionar um objeto para Azure AD, a regra de saída **Out para AAD – contatar ingressar** criará um objeto de contato, se o atributo do metaverso **sourceObjectType** é definida como **contato**. Se esse atributo está definido para o **usuário**, em seguida, a regra **Out para AAD – usuário ingressar** criará um objeto de usuário em vez disso.
É possível que um objeto é promovido de contato ao usuário quando mais diretórios de ativos de origem são importados e sincronizados.

Por exemplo, em uma topologia de GALSync nós encontrará objetos de contato para todos na segunda floresta quando podemos importar da primeira floresta. Isto irá testar novos objetos de contato no conector AAD. Quando posteriormente importar e sincronizar da segunda floresta, podemos encontrará os usuários reais e associá-los aos objetos metaverso existentes. Abordaremos, em seguida, excluir o objeto de contato em AAD e criar um novo objeto de usuário, em vez disso.

Se você tiver uma topologia onde os usuários e representados como contatos, verifique se você selecionou para corresponder ao usuários sobre o atributo de email no guia de instalação. Se você selecionar outra opção, você terá uma configuração de dependentes ordem. Objetos de contato sempre ingressarão no atributo mail, mas objetos de usuário só ingressará no atributo mail se essa opção foi selecionada no guia de instalação. Você poderia então acabar com dois objetos diferentes no metaverso com o mesmo atributo de email se o objeto de contato foi importado antes de objeto do usuário. Durante a exportação para o Azure AD, um erro será lançado. Esse comportamento é por design e indica dados inválidos ou que a topologia não foi corretamente identificada durante a instalação.

## <a name="disabled-accounts"></a>Contas desativadas

Contas desabilitadas são sincronizadas também Azure AD. Contas desabilitadas são comuns para representar recursos no Exchange, por exemplo salas de conferência. A exceção é usuários com uma caixa de correio vinculada; conforme mencionado anteriormente, eles nunca serão provisionar uma conta ao Azure AD.

A suposição é que se encontra-se uma conta de usuário desabilitada, em seguida, nós não encontrará outra conta ativa mais tarde e o objeto está provisionado ao Azure AD com o userPrincipalName e sourceAnchor encontrado. No caso de outra conta ativa ingressarão ao mesmo objeto metaverso, então seu userPrincipalName e sourceAnchor serão usados.

## <a name="changing-sourceanchor"></a>Alterando sourceAnchor

Quando um objeto foi exportado para Azure AD e em seguida, ele não tem permissão para alterar o sourceAnchor mais. Quando o objeto foi exportado o atributo do metaverso **cloudSourceAnchor** é definida com o valor de **sourceAnchor** aceito pelo Azure AD. Se **sourceAnchor** for alterado e não correspondem a **cloudSourceAnchor**, a regra de **saída para AAD – usuário ingressar** irá acionar o erro **sourceAnchor atributo foi alterado**. Nesse caso, os dados ou configuração devem ser corrigidos para que o mesmo sourceAnchor está presente no metaverso novamente antes que o objeto pode ser sincronizado novamente.

## <a name="additional-resources"></a>Recursos adicionais

* [Azure AD conectar sincronização: Opções de personalização de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
