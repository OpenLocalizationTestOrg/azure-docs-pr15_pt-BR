<properties
    pageTitle="Diretivas de senha e restrições do Active Directory do Azure | Microsoft Azure"
    description="Descreve as políticas que se aplicam a senhas no Active Directory do Azure, incluindo caracteres permitidos, comprimento e vencimento"
  services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand"/>


# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Diretivas de senha e restrições do Active Directory do Azure

Este artigo descreve os requisitos de complexidade associados armazenadas no seu diretório Azure AD de contas de usuário e políticas de senha.

> [AZURE.IMPORTANT] **Você está aqui porque você está tendo problemas para entrar?** Em caso afirmativo, [Veja como você pode alterar e redefinir sua própria senha](active-directory-passwords-update-your-own-password.md).

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Políticas de UserPrincipalName que se aplicam a todas as contas de usuário

Cada conta de usuário que precisa entrar para o sistema de autenticação do Azure AD deve ter um valor de atributo de nome principal (UPN) usuário exclusivo associado a essa conta. A seguinte tabela contornos as políticas que se aplicam a ambas as contas de usuário de origem do Active Directory no local (sincronizados na nuvem) e a contas de usuário somente na nuvem.

|   Propriedade           |     Requisitos de UserPrincipalName  |
|   ----------------------- |   ----------------------- |
|  Caracteres permitidos    |  <ul> <li>A – Z</li> <li>-z </li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
|  Caracteres não permitidos  | <ul> <li>Qualquer '@' caractere que não é separar o nome de usuário do domínio.</li> <li>Não pode conter um caractere ponto '.' imediatamente anterior a '@' símbolo</li></ul> |
| Restrições de comprimento  |       <ul> <li>Comprimento total não deve exceder 113 caracteres</li><li>64 caracteres antes do ‘@’ símbolo</li><li>48 caracteres após o ‘@’ símbolo</li></ul>

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>Políticas de senha que se aplicam apenas às contas de usuário de nuvem

A tabela a seguir descreve as configurações de política de senha disponíveis que podem ser aplicadas a contas de usuário que são criadas e gerenciadas no Azure AD.

|  Propriedade       |    Requisitos          |
|   ----------------------- |   ----------------------- |
|  Caracteres permitidos   |   <ul><li>A – Z</li><li>-z </li><li>0 – 9</li> <li>@# $ % ^ & * - _ ! + = {} [] & #124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
|  Caracteres não permitidos   |       <ul><li>Caracteres Unicode</li><li>Espaços</li><li> **Somente senhas fortes**: não pode conter um caractere de ponto '.' imediatamente anterior a '@' símbolo</li></ul> |
|   Restrições de senha | <ul><li>8 caracteres mínimos e máximo de 16 caracteres</li><li>**Somente senhas fortes**: 3 requer fora 4 dos seguintes procedimentos:<ul><li>Caracteres em minúsculas</li><li>Caracteres em maiusculas</li><li>Números (0 a 9)</li><li>Símbolos (consulte restrições de senha acima)</li></ul></li></ul> |
| Duração da expiração de senha      | <ul><li>Valor padrão: **90** dias </li><li>Valor é configurável usando o cmdlet Set-MsolPasswordPolicy do Azure Active Directory módulo para Windows PowerShell.</li></ul> |
| Notificação de expiração de senha |  <ul><li>Valor padrão: **14** dias (antes da senha expirar)</li><li>Valor é configurável usando o cmdlet Set-MsolPasswordPolicy.</li></ul> |
| Expiração de senha |  <ul><li>Valor padrão: **false** dias (indica que expiração de senha está habilitada) </li><li>Valor pode ser configurado para contas de usuário individual usando o cmdlet Set-MsolUser. </li></ul> |
|  Histórico de senha  | Última senha não pode ser usada novamente. |
|  Duração do histórico de senha | Para sempre |
|  Bloqueio de conta | Depois de 10 tentativas sem êxito entrar (senha errada), o usuário será bloqueado por um minuto. Ainda mais tentativas incorretas de entrada bloqueará o usuário para aumentar durações. |


## <a name="next-steps"></a>Próximas etapas

* **Você está aqui porque você está tendo problemas para entrar?** Em caso afirmativo, [Veja como você pode alterar e redefinir sua própria senha](active-directory-passwords-update-your-own-password.md).
* [Gerenciar suas senhas de qualquer lugar](active-directory-passwords.md)
* [Como funciona o gerenciamento de senha](active-directory-passwords-how-it-works.md)
* [Introdução ao gerenciamento de senha](active-directory-passwords-getting-started.md)
* [Personalizar o gerenciamento de senha](active-directory-passwords-customize.md)
* [Práticas recomendadas de gerenciamento de senha](active-directory-passwords-best-practices.md)
* [Como obter ideias operacionais com relatórios de gerenciamento de senha](active-directory-passwords-get-insights.md)
* [Perguntas frequentes sobre o gerenciamento de senhas](active-directory-passwords-faq.md)
* [Solucionar problemas de gerenciamento de senha](active-directory-passwords-troubleshoot.md)
* [Saiba Mais](active-directory-passwords-learn-more.md)
