<properties
   pageTitle="Como exigir autenticação multifator | Microsoft Azure"
   description="Saiba como exigir autenticação multifator (MFA) para identidades privilegiadas com a extensão do Azure Active Directory privilegiados gerenciamento de identidades."
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

# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Como exigir MFA no gerenciamento de identidades do Azure AD privilegiado

Recomendamos que você exija a autenticação multifator (MFA) para todos os administradores. Isso reduz o risco de um ataque devido a uma senha comprometida.

Você pode exigir que os usuários concluirem um desafio MFA quando eles entrar. A postagem no blog [MFA para Office 365 e MFA para Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) compara que está incluído no Office e Azure assinaturas, com os recursos contidos na oferta de autenticação do Microsoft Azure multifator.

Você também pode exigir que os usuários concluirem um desafio MFA quando eles ativa uma função no Gerenciador do Azure AD. Dessa forma, se o usuário não concluir um desafio MFA quando eles entrou, ele será solicitado a fazer isso pelo Gerenciador.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Exigir MFA no gerenciamento de identidades do Azure AD privilegiado

Quando você gerencia identidades no Gerenciador como um administrador com privilégios de função, você poderá ver alertas que recomendam MFA para contas com privilégios. Clique no alerta de segurança no painel Gerenciador e um novo blade será aberto com uma lista das contas de administrador que deve exigir MFA.  Você pode exigir MFA selecionando várias funções e, em seguida, clicando no botão **corrigir** , ou clique nas reticências ao lado de funções individuais e, em seguida, clique no botão **corrigir** .

> [AZURE.IMPORTANT] Direita agora, Azure MFA só funciona com o trabalho ou escola contas, não contas da Microsoft (geralmente uma conta pessoal que é usado para entrar nos serviços da Microsoft como o Skype, Xbox, Outlook.com, etc.). Por isso, alguém usando uma conta da Microsoft não pode ser um administrador qualificado porque eles não podem usar MFA para ativar suas funções. Se esses usuários necessário continuar gerenciando cargas de trabalho usando uma conta da Microsoft, elevá-las aos administradores permanentes por agora.

Além disso, você pode alterar o requisito de MFA para uma função específica, clicando na seção funções do painel Gerenciador. Em seguida, clique em **configurações** na lâmina de função e selecionando **Habilitar** em autenticação multifator.

## <a name="how-azure-ad-pim-validates-mfa"></a>Como o Azure AD Gerenciador valida MFA

Há duas opções para validar MFA quando um usuário ativa uma função.

A opção mais simples é dependem do Azure MFA para os usuários que estão Ativando uma função privilegiada. Para fazer isso, primeiro verifique se esses usuários licenciados, se necessário e se inscreveram para MFA do Azure. Obter mais informações sobre como fazer isso estão em [Introdução ao Azure multi-Factor Authentication na nuvem](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md). Ele é recomendado, mas não obrigatório, que você configure Azure AD para impor MFA para esses usuários quando eles entrar. Isso ocorre porque as verificações MFA serão feitas por Azure AD Gerenciador em si.

Como alternativa, se os usuários autenticados no local que você pode ter seu provedor de identidade responsável por MFA. Por exemplo, se você tiver configurado AD serviços de federação para exigir autenticação baseada em cartão inteligente antes de acessar Azure AD, [recursos de nuvem protegendo com autenticação multifator do Azure e AD FS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md) inclui instruções para configurar o AD FS para enviar declarações ao Azure AD. Quando um usuário tenta ativar uma função, o Azure AD Gerenciador aceitará que MFA já foi validada para o usuário quando ele recebe as declarações apropriadas.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
