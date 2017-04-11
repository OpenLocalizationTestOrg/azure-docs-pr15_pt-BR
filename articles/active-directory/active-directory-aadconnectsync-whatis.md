<properties
    pageTitle="Sincronização do Azure AD Connect: entender e personalizar a sincronização | Microsoft Azure"
    description="Explica como Azure AD Connect sincronizar funciona e como personalizar."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Sincronização do Azure AD Connect: entender e personalizar a sincronização
Os serviços de sincronização a conexão do Azure Active Directory do (Azure AD Connect sync) é um componente principal do Azure AD Connect. Cuida de todas as operações que estão relacionadas para sincronizar dados de identidade entre seu ambiente local e o Azure AD. Sincronização do Azure AD Connect é sucessora DirSync, Azure AD Sync e Gerenciador de identidades Forefront com o Azure Active Directory Connector configurado.

Este tópico é a página inicial para **sincronização do Azure AD Connect** (também chamado de **mecanismo de sincronização**) e listas de links para todos os outros tópicos relacionados a ele. Para obter links para Azure AD Connect, consulte [integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).

O serviço de sincronização consiste em dois componentes, o componente de **sincronização do Azure AD Connect** locais e do lado do serviço no Azure AD chamado **serviço de sincronização do Azure AD Connect**. O serviço é comum DirSync, Azure AD Sync e Azure AD Connect.

## <a name="azure-ad-connect-sync-topics"></a>Tópicos de sincronização do Azure AD Connect

Tópico | O que ele cubra e quando a leitura
----- | -----
**Conceitos básicos de sincronização do Azure AD Connect** |
[Noções básicas sobre a arquitetura](active-directory-aadconnectsync-understanding-architecture.md) | Para aqueles que são novos para o mecanismo de sincronização e deseja saber mais sobre a arquitetura e os termos usados.
[Conceitos técnicos](active-directory-aadconnectsync-technical-concepts.md) | Uma versão abreviada do tópico arquitetura e brevemente explica os termos usados.
[Conectar topologias para Azure AD](active-directory-aadconnect-topologies.md) | Descreve os diferentes topologias e cenários que o mecanismo de sincronização oferece suporte.
**Configuração personalizada** |
[Executando o Assistente de instalação novamente](active-directory-aadconnectsync-installation-wizard.md) | Explica as opções estão disponíveis quando você executar o Assistente de instalação do Azure AD Connect novamente.
[Noções básicas sobre declarativa de provisionamento](active-directory-aadconnectsync-understanding-declarative-provisioning.md)| Descreve o modelo de configuração chamado de provisionamento declarativa.
[Noções básicas sobre expressões de provisionamento declarativas](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) | Descreve a sintaxe para a linguagem de expressão usada em provisionamento declarativa.
[Noções básicas sobre a configuração padrão](active-directory-aadconnectsync-understanding-default-configuration.md)| Descreve as regras de prontos e a configuração padrão. Também descreve como as regras funcionam juntos para os cenários de prontos trabalhar.
[Noções básicas sobre usuários e contatos](active-directory-aadconnectsync-understanding-users-and-contacts.md) | Continua no tópico anterior e descreve como a configuração para usuários e contatos funciona juntas, em particular em um ambiente de floresta vários.
[Como fazer uma alteração para a configuração padrão](active-directory-aadconnectsync-change-the-configuration.md) | O guiará pelo como tornar uma configuração comum alterar para fluxos de atributo.
[Práticas recomendadas para a alteração da configuração padrão](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) | Limitações de suporte e para fazer alterações na configuração de prontos.
[Configurar a filtragem](active-directory-aadconnectsync-configure-filtering.md) | Descreve as diferentes opções para como limitar os objetos que estão sendo sincronizadas com o Azure AD e passo a passo como configurar essas opções.
**Recursos e cenários** |
[Impedir exclusões acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) | Descreve o recurso *impedir exclusões acidentais* e como configurá-lo.
[Agendador](active-directory-aadconnectsync-feature-scheduler.md) | Descreve o agendador interno, que está importando, a sincronização e exportação de dados.
[Implementar a sincronização de senha](active-directory-aadconnectsync-implement-password-synchronization.md) | Descreve como funciona a sincronização de senha, como implementar e como operar e solucionar problemas.
[Write-back do dispositivo](active-directory-aadconnect-feature-device-writeback.md) | Descreve como funciona o dispositivo write-back no Azure AD Connect.
[Extensões de diretório](active-directory-aadconnectsync-feature-directory-extensions.md) | Descreve como estender o esquema de Azure AD com seus próprios atributos personalizados.
**Serviço de sincronização** |
[Recursos do serviço de sincronização do Azure AD Connect](active-directory-aadconnectsyncservice-features.md) | Descreve como alterar as configurações de sincronização no Azure AD e o lado do serviço de sincronização.
[Resiliência de atributo duplicado](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) | Descreve como ativar e usar resiliência de valores duplicados do atributo **userPrincipalName** e **proxyAddresses** .
**Interface de usuário e operações** |
[Gerenciador de serviços de sincronização](active-directory-aadconnectsync-service-manager-ui.md) | Descreve o UI Gerenciador de serviços de sincronização, incluindo [operações](active-directory-aadconnectsync-service-manager-ui-operations.md), [conectores](active-directory-aadconnectsync-service-manager-ui-connectors.md), [Designer de metaverso](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)e guias de [Pesquisa de metaverso](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) .
[Considerações e tarefas operacionais](active-directory-aadconnectsync-operations.md) | Descreve questões operacionais, como recuperação de dados.
**Como...** |
[Redefinir a conta do Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md) | Como redefinir as credenciais da conta de serviço usado para conectar-se de sincronização do Azure AD Connect ao Azure AD.
**Mais informações e referências** |
[Portas](active-directory-aadconnect-ports.md) | Lista as portas que você precisa abrir entre o mecanismo de sincronização e seus diretórios de locais e Azure AD.
[Atributos sincronizados com o Active Directory do Azure](active-directory-aadconnectsync-attributes-synchronized.md) | Lista todos os atributos sendo sincronizados entre locais AD e Azure AD.
[Referência de funções](active-directory-aadconnectsync-functions-reference.md) | Lista todas as funções disponíveis no provisionamento declarativa.

## <a name="additional-resources"></a>Recursos adicionais

* [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
