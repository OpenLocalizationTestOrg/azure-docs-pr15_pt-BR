<properties
   pageTitle="Azure AD Connect: Recursos na visualização | Microsoft Azure"
   description="Este tópico descreve em mais recursos de detalhes que estão na visualização Azure AD Connect."
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
   ms.date="06/27/2016"
   ms.author="billmath"/>

# <a name="more-details-about-features-in-preview"></a>Mais detalhes sobre os recursos no modo de visualização
Este tópico descreve como usar recursos atualmente na visualização.

## <a name="group-writeback"></a>Write-back de grupo
A opção para write-back do grupo de recursos opcionais permitirá que você write-back **Grupos do Office 365** para uma floresta com Exchange instalado. Este é um grupo que é sempre mastered na nuvem. Se você tiver o Exchange local e em seguida, você pode gravar novamente esses grupos para o local portanto usuários com uma caixa de correio do Exchange local podem enviar e receber emails desses grupos.

Mais informações sobre grupos do Office 365 e como usá-las podem ser encontradas [aqui](http://aka.ms/O365g).

Este grupo será representado como um grupo de distribuição no local AD DS. Seu servidor do Exchange local deve estar na atualização cumulativa do Exchange 2013 8 (lançada em março de 2015) ou 2016 do Exchange para reconhecer esse novo tipo de grupo.

**Anotações durante a visualização**

- Atualmente, o atributo de catálogo de endereços não é preenchido na visualização. Sem esse atributo, o grupo não ficará visível na GAL. A maneira mais fácil para preencher esse atributo é usar o cmdlet do PowerShell do Exchange `update-recipient`.
- Somente florestas com o esquema do Exchange são válidos destinos para grupos. Se nenhum Exchange foi detectado, grupo write-back não será possível habilitar.
- Somente floresta organização implementações são aceitos atualmente. Se você tiver mais de um Exchange organização local, você precisará de uma solução local de GALSync para esses grupos apareça em seu outras florestas.
- O recurso de write-back do grupo não atualmente lidar com grupos de segurança ou grupos de distribuição.

>[AZURE.NOTE] Uma assinatura do Azure AD Premium é exigida para write-back do grupo.

## <a name="user-writeback"></a>Write-back do usuário
> [AZURE.IMPORTANT] O recurso de visualização de write-back de usuário foi removido na atualização agosto de 2015 Azure AD Connect. Se você habilitou-lo, você deve desativar esse recurso.

## <a name="next-steps"></a>Próximas etapas
Continue com a [instalação personalizada do Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md).

Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
