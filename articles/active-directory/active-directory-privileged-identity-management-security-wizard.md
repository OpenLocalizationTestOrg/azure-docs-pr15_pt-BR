<properties
   pageTitle="O Assistente de segurança do gerenciamento de identidades do Azure AD privilegiado"
   description="Na primeira vez que você usa a extensão do Azure Active Directory privilegiados gerenciamento de identidades, você receberá um Assistente de segurança. Este artigo descreve as etapas para usar o assistente."
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
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# <a name="the-azure-ad-privileged-identity-management-security-wizard"></a>O Assistente de segurança do gerenciamento de identidades do Azure AD privilegiado

Se você for a primeira pessoa para executar o gerenciamento de identidade privilegiados Azure (PIM) para sua organização, você receberá um assistente. O assistente o ajudará a entender os riscos de segurança de identidades privilegiadas e como usar o Gerenciador de reduzir esses riscos. Você não precisa fazer qualquer alteração às atribuições de função existentes no assistente, se você preferir fazê-lo mais tarde.

## <a name="what-to-expect"></a>O que esperar

Antes de sua organização for iniciado usando o Gerenciador, todas as atribuições de função são permanentes: os usuários são sempre essas funções mesmo se eles não precisam atualmente seus privilégios.  A primeira etapa do assistente mostra uma lista das funções de altamente privilegiado e quantos usuários estão sendo essas funções. É possível fazer drill uma função particular para saber mais sobre os usuários se uma ou mais deles não estiverem familiarizados.

A segunda etapa do assistente oferece a oportunidade de alterar atribuições de função do administrador.  

> [AZURE.WARNING]É importante que você tenha pelo menos um administrador global e mais de um administrador de função privilegiado com uma conta organizacional (não uma conta da Microsoft). Se houver somente um administrador de função privilegiados, a organização não poderão gerenciar Gerenciador se essa conta for excluída.
> Além disso, manter atribuições de função permanente se um usuário tiver uma conta da Microsoft (uma conta que eles usar para entrar nos serviços da Microsoft como o Skype e Outlook.com). Se você planeja exigir MFA para ativação para essa função, esse usuário será bloqueado.


Depois de fazer alterações, o assistente não aparecerá. Na próxima vez que você ou outra função privilegiados administrador usar Gerenciador, você verá o painel Gerenciador.  

- Se você gostaria de adicionar ou remover usuários de funções ou alterar atribuições de permanentes para qualificada, leia mais em [como adicionar ou remover uma função de usuário](active-directory-privileged-identity-management-how-to-add-role-to-user.md).
- Se você gostaria de conceder acesso para gerenciar o Gerenciador de usuários mais, leia mais em [como conceder acesso para gerenciar no Gerenciador](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).



## <a name="next-steps"></a>Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
