<properties
    pageTitle="Azure domínio serviços do Active Directory: Sincronização em domínios gerenciados | Microsoft Azure"
    description="Entender a sincronização em um domínio gerenciado do Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Sincronização em um domínio gerenciado de serviços de domínio do Azure AD
O diagrama a seguir ilustra como funciona a sincronização nos serviços de domínio do Azure AD domínios gerenciados.

![Topologia de sincronização nos serviços de domínio do Azure AD](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Sincronização de diretório local para seu locatário do Azure AD
Sincronização do Azure AD Connect é usada para sincronizar contas de usuário, associações de grupo e credencial aplica hash seu locatário do Azure AD. Atributos de usuário contas como o nome UPN e local SID (identificador de segurança) são sincronizados. Se você usar os serviços de domínio do Azure AD, hash de credencial herdados necessários para autenticação NTLM e Kerberos também é sincronizadas em seu locatário do Azure AD.

Se você configurar regravação, alterações ocorrendo no diretório Azure AD são sincronizadas volta para seu local Active Directory. Por exemplo, se você alterar sua senha usando recursos de alteração de senha de autoatendimento do Azure AD, a senha alterada é atualizada no seu local domínio do AD.

> [AZURE.NOTE] Sempre use a versão mais recente do Azure AD Connect para garantir que você tenha correções para todos os erros conhecidos.


## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Sincronização de seu locatário do Azure AD para seu domínio gerenciado
Contas de usuário, membros do grupo e hash de credencial é sincronizados do seu locatário do Azure AD para seu domínio de serviços de domínio do Azure AD gerenciado. Esse processo de sincronização é automático. Você não precisa configurar, monitorar ou gerenciar este processo de sincronização. O processo de sincronização também é uma-way/unidirecional natureza. Seu domínio gerenciado é amplamente somente leitura, exceto todas organizacionais personalizados que você criar. Portanto, você não pode fazer alterações de atributos de usuário, senhas de usuário ou associações de grupo dentro do domínio gerenciado. Como resultado, há uma sincronização reversa das alterações de seu domínio gerenciado volta para seu locatário do Azure AD.


## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Sincronização de um ambiente de floresta vários locais
Muitas organizações têm uma infraestrutura de identidade local relativamente complexo consiste em várias florestas de conta. Azure AD Connect dá suporte à sincronização de usuários, grupos e hash de credencial florestas vários ambientes de seu locatário do Azure AD.

Em contraste, seu locatário do Azure AD é um namespace muito mais simples e simples. Para permitir que usuários acessem confiavelmente aplicativos protegidos por AD Azure, resolva conflitos de nome UPN entre contas de usuário em diferentes florestas. Seu ursos de domínio gerenciado de serviços de domínio do Azure AD fechar semelhança seu locatário do Azure AD. Portanto, você vê uma estrutura de unidade Organizacional simples no seu domínio gerenciado. Todos os usuários e grupos são armazenados dentro do contêiner de 'AADDC os usuários', independentemente da floresta ou o domínio local do qual eles foram sincronizados em. Talvez você tenha configurado uma unidade Organizacional hierárquica estruturar local. No entanto, seu domínio gerenciado ainda tem uma estrutura simples de OU simples.


## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Exclusões - o que não está sincronizado com seu domínio gerenciado
Os seguintes atributos ou objetos não são sincronizados em seu locatário do Azure AD ou ao seu domínio gerenciado:

- **Excluídas atributos:** Você pode optar por excluir determinados atributos dos sincronizando seu locatário do Azure AD do seu domínio local usando o Azure AD Connect. Esses atributos excluídos não estão disponíveis em seu domínio gerenciado.

- **Políticas de grupo:** Políticas de grupo configuradas em seu domínio local não são sincronizadas para seu domínio gerenciado.

- **Compartilhamento SYSVOL:** Da mesma forma, o conteúdo do compartilhamento SYSVOL em seu domínio local não é sincronizado para seu domínio gerenciado.

- **Objetos de computador:** Objetos de computador para computadores que ingressaram em seu domínio local não são sincronizados para seu domínio gerenciado. Esses computadores não têm uma relação de confiança com seu domínio gerenciado e pertencer apenas seu domínio local. No seu domínio gerenciado, você pode encontrar objetos de computador somente para computadores que você tem explicitamente domínio para o domínio gerenciado.

- **Atributos de histórico SID para usuários e grupos:** O usuário principal e o grupo primário SIDs de seu domínio local são sincronizadas em seu domínio gerenciado. No entanto, atributos de histórico SID existentes para usuários e grupos não são sincronizados do seu domínio local para seu domínio gerenciado.

- **Estruturas de unidades de organização (OU):** Unidades organizacionais definidas no seu domínio local não sincronizar seu domínio gerenciado. Há duas unidades organizacionais internos no seu domínio gerenciado. Por padrão, o seu domínio gerenciado tem uma estrutura de unidade Organizacional simples. No entanto, você pode optar por [criar uma OU personalizada em seu domínio gerenciado](./active-directory-ds-admin-guide-create-ou.md).


## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Como os atributos específicos são sincronizados com seu domínio gerenciado
A tabela a seguir lista alguns atributos comuns e descreve como eles são sincronizados com seu domínio gerenciado.

| O atributo em seu domínio gerenciado | Fonte | Anotações |
|:---|:---|:---|
|UPN|Atributo UPN do usuário em seu locatário do Azure AD|O atributo UPN do seu locatário do Azure AD está sincronizado como é seu domínio gerenciado. Portanto, a maneira mais confiável para entrar no seu domínio gerenciado está usando seu UPN.|
|SAMAccountName|MailNickname do usuário atributo em seu locatário do Azure AD ou geradas automaticamente|O atributo SAMAccountName é origem do atributo mailNickname no seu locatário do Azure AD. Se várias contas de usuário tem o mesmo atributo mailNickname, SAMAccountName é gerado automaticamente. Se mailNickname ou prefixo UPN do usuário for mais de 20 caracteres, SAMAccountName é gerado automaticamente para satisfazer o limite de 20 caracteres em atributos SAMAccountName.|
|Senhas|Senha do usuário do seu locatário do Azure AD|Hash de credencial necessários para autenticação NTLM ou Kerberos (também chamada de credenciais complementares) é sincronizados em seu locatário do Azure AD. Se seu locatário do Azure AD for um locatário sincronizado, essas credenciais são origem do seu domínio local.|
|Usuário/grupo SID primário|Gerado automaticamente|O SID primário para contas de usuário/grupo é gerado automaticamente no seu domínio gerenciado. Esse atributo não coincidir com o usuário/grupo primário SID do objeto no seu local de domínio do AD. Essa incompatibilidade é porque o domínio gerenciado tem um namespace SID diferente de seu domínio local.|
|Histórico de SID para usuários e grupos|Local principal usuário e grupo SID|O atributo de histórico SID para usuários e grupos em seu domínio gerenciado é definido para corresponder ao usuário principal correspondente ou grupo SID no seu domínio local. Esse recurso ajuda a facilitar lift shift e de aplicativos no local para o domínio gerenciado, desde que você não precisa recursos re-ACL.|

> [AZURE.NOTE] **Entrar no domínio gerenciado usando o formato UPN:** O atributo SAMAccountName pode ser gerada automaticamente para algumas contas de usuário no seu domínio gerenciado. Se vários usuários têm o mesmo atributo mailNickname ou usuários têm prefixos UPN muito longos, o SAMAccountName para esses usuários pode ser gerado automaticamente. Portanto, o formato de SAMAccountName (por exemplo, ' CONTOSO100\joeuser') nem sempre é uma maneira confiável para entrar no domínio. Geradas automaticamente SAMAccountName dos usuários pode diferir seu prefixo UPN. Usar o formato UPN (por exemplo, 'joeuser@contoso100.com') para entrar no domínio gerenciado confiável.


## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objetos que não são sincronizados em seu locatário do Azure AD do seu domínio gerenciado
Conforme descrito em uma seção anterior deste artigo, há uma sincronização de seu domínio gerenciado para seu locatário do Azure AD. Você pode optar por [criar um personalizado OU (unidade organizacional)](./active-directory-ds-admin-guide-create-ou.md) em seu domínio gerenciado. Além disso, você pode criar outras unidades organizacionais, usuários, grupos ou contas de serviço essas OUs personalizadas. Nenhum dos objetos criados em unidades organizacionais personalizados são sincronizados voltar ao seu locatário do Azure AD. Esses objetos estão disponíveis para uso somente em seu domínio gerenciado. Portanto, esses objetos não são visíveis usando cmdlets do PowerShell do Azure AD, API do Azure AD Graph ou usando o gerenciamento do Azure AD interface do usuário.


## <a name="related-content"></a>Conteúdo relacionado
- [Recursos - serviços de domínio do Azure AD](active-directory-ds-features.md)

- [Cenários de implantação - serviços de domínio do Azure AD](active-directory-ds-scenarios.md)

- [Considerações de rede para serviços de domínio do Azure AD](active-directory-ds-networking.md)

- [Começar a usar os serviços de domínio do Azure AD](active-directory-ds-getting-started.md)
