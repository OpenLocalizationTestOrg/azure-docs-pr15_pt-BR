<properties
    pageTitle="Sincronização do Azure AD Connect: práticas recomendadas para alterar a configuração padrão | Microsoft Azure"
    description="Fornece as práticas recomendadas para alterar a configuração padrão de sincronização do Azure AD Connect."
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
    ms.date="08/22/2016"
    ms.author="markvi;andkjell"/>


# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Sincronização do Azure AD Connect: práticas recomendadas para alterar a configuração padrão
O objetivo deste tópico é descrever as alterações e não suportadas para sincronização do Azure AD Connect.

A configuração criada por Azure AD Connect funciona "assim mesmo" para a maioria dos ambientes que sincronizar do Active Directory local com o Azure AD. No entanto, em alguns casos, é necessário aplicar algumas alterações a uma configuração para atender a uma determinada necessidade ou requisito.

## <a name="changes-to-the-service-account"></a>Alterações à conta de serviço
Sincronização do Azure AD Connect está sendo executado em uma conta de serviço criada pelo Assistente de instalação. Essa conta de serviço mantém as chaves de criptografia para o banco de dados usado pela sincronização. Ele é criado com uma senha longa 127 caracteres e a senha está definida para não expirar.

- Ele é **sem suporte** para alterar ou redefinir a senha da conta de serviço. Isso destrói as chaves de criptografia e o serviço não é possível acessar o banco de dados e não é possível iniciar.

## <a name="changes-to-the-scheduler"></a>Alterações para o Agendador
Começando com as versões de compilação 1.1 (fevereiro de 2016) você pode configurar o [Agendador](active-directory-aadconnectsync-feature-scheduler.md) ter um ciclo de sincronização diferente do padrão de 30 minutos.

## <a name="changes-to-synchronization-rules"></a>Alterações para regras de sincronização
O Assistente de instalação fornece uma configuração que deveria funcionam para os cenários mais comuns. Caso você precise fazer alterações na configuração, você deve seguir estas regras para ainda tem uma configuração suportada.

- Você pode [alterar fluxos de atributo](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) se os fluxos de atributo direto padrão não são adequados para sua organização.
- Se você deseja [não fluxo um atributo](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) e remove quaisquer valores de atributo existentes no Azure AD, você precisa criar uma regra para esse cenário.
- [Desabilitar uma regra de sincronização indesejadas](#disable-an-unwanted-sync-rule) em vez de excluí-lo. Uma regra excluída será recriada durante uma atualização.
- Para [alterar uma regra de prontos](#change-an-out-of-box-rule), você deve fazer uma cópia da regra original e desabilitar a regra de prontos. O Editor de regra de sincronização solicita e ajuda.
- Exporte regras personalizado de sincronização usando o Editor de regras de sincronização. O editor oferece um script do PowerShell que você pode usar para recriá-los facilmente em um cenário de recuperação de desastres.

>[AZURE.WARNING] As regras de sincronização de prontos têm uma impressão digital. Se você fizer uma alteração nessas regras, já não é compatível com a impressão digital. Você pode ter problemas no futuro quando você tenta aplicar uma nova versão do Azure AD Connect. Somente faça alterações a maneira como ele é descrito neste artigo.

### <a name="disable-an-unwanted-sync-rule"></a>Desabilitar uma regra de sincronização indesejadas
Não exclua uma regra prontos de sincronização. Ele é recriado durante a próxima atualização.

Em alguns casos, o Assistente de instalação produziu uma configuração que não está funcionando para a sua topologia. Por exemplo, se você tiver uma topologia de floresta conta-recurso, mas você tenha estendido o esquema da floresta de conta com o esquema do Exchange, regras para o Exchange são criadas para a floresta de conta e floresta do recurso. Nesse caso, você precisa desativar a regra de sincronização para o Exchange.

![Regra de sincronização desabilitado](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Na imagem acima, o Assistente para instalação encontrou um esquema antigo do Exchange 2003 na floresta conta. Essa extensão de esquema foi adicionada antes de floresta do recurso foi introduzida no ambiente da Fabrikam. Para garantir que nenhuma atributos da implementação do Exchange antigo são sincronizados, a regra de sincronização deve ser desabilitada conforme mostrado.

### <a name="change-an-out-of-box-rule"></a>Alterar uma regra de prontos
Se você precisar fazer alterações em uma regra de prontos, você deve fazer uma cópia da regra de prontos e desabilitar a regra original. Faça as alterações à regra clonada. O Editor de regra de sincronização é ajudá-lo com essas etapas. Quando você abre uma regra de prontos, são apresentadas com esta caixa de diálogo:  
![Aviso fora regra de caixa](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Selecione **Sim** para criar uma cópia da regra. A regra clonada é aberta.  
![Regra clonada](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

Nessa regra clonada, faça as alterações necessárias escopo, ingressar e transformações.

## <a name="next-steps"></a>Próximas etapas

**Tópicos de visão geral**

- [Sincronização do Azure AD Connect: entender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
- [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
