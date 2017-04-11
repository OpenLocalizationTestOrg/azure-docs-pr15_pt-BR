<properties
   pageTitle="Visualização de colaboração do Azure AD B2B: como funciona | Microsoft Azure"
   description="Descreve como o Azure Active Directory B2B colaboração suporta os relacionamentos entre empresas habilitando parceiros de negócios seletivamente acessar seus aplicativos corporativos"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-how-it-works"></a>Visualização de colaboração do Azure AD B2B: como funciona
Colaboração do Azure AD B2B baseado em um convite e resgatar o modelo. Forneça os endereços de email dos participantes que você quer trabalhar com, juntamente com os aplicativos que você deseja que eles usar. Azure AD envia um convite de email com um link. O usuário de parceiro segue o link e é solicitado a entrar usando sua conta do Azure AD ou sinal para Azure AD uma nova conta.

1. Seu administrador convida usuários parceiros carregar [um arquivo. csv estruturada](active-directory-b2b-references-csv-file-format.md) usando o portal do Azure.
2. O portal envia convida emails a esses usuários de parceiro.
3. Os usuários de parceiro clique no link no email e serão solicitados a entrar usando suas credenciais do trabalho (se ela estiverem já no Azure AD), ou para se inscrever como um usuário de colaboração do Azure AD B2B.
4. Usuários de parceiros são redirecionados para o aplicativo que eles foram convidados, onde eles agora têm acesso.

## <a name="directory-operations"></a>Operações de diretório
Usuários de parceiros existem no seu Azure AD como usuários externos. Isso significa que o administrador pode provisionar licenças, atribuir associações de grupo e ainda mais conceder acesso aos aplicativos corporativos por meio do portal do Azure ou usando o PowerShell do Azure exatamente como para os usuários da sua empresa.

Enquanto um anúncio Azure pago assinatura (Basic ou Premium) não é necessária usar Azure AD B2B, locatários que têm uma paga assinatura do Azure AD (Basic ou Premium) receber os seguintes benefícios adicionais:

 - Administradores podem atribuir grupos aos aplicativos, fornecendo para gerenciamento mais simples de acesso de usuário convidado.
 - Administrador locatário identidade visual é usado para os emails de convite de marca e experiência de resgate, fornecendo mais contexto para convidou usuários de parceiro.

## <a name="related-articles"></a>Artigos relacionados
 Procurar nossos outros artigos sobre colaboração do Azure AD B2B

 - [O que é Azure AD B2B colaboração?](active-directory-b2b-what-is-azure-ad-b2b.md)
 - [Explicação passo a passo detalhada](active-directory-b2b-detailed-walkthrough.md)
 - [Referência de formato de arquivo CSV](active-directory-b2b-references-csv-file-format.md)
 - [Formato do token de usuário externo](active-directory-b2b-references-external-user-token-format.md)
 - [Alterações de atributo de objeto do usuário externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
 - [Limitações de visualização atual](active-directory-b2b-current-preview-limitations.md)
 - [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
