<properties
   pageTitle="Alterações de atributo de objeto de usuário externo para visualização de colaboração do Azure Active Directory B2B | Microsoft Azure"
   description="Azure B2B diretório ativo oferece suporte a suas relações entre empresas habilitando parceiros de negócios seletivamente acessar seus aplicativos corporativos"
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
   ms.workload="na"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-external-user-object-attribute-changes"></a>Visualização de colaboração do Azure AD B2B: alterações de atributo de objeto do usuário externo

Cada usuário em um diretório do Azure AD é representado por um objeto de usuário. Objeto do usuário no Azure AD passa por alterações de atributo em vários estágios de colaboração B2B convidar-resgatar fluxo. O objeto de usuário que representa o usuário do parceiro no diretório tem atributos que alterar a resgatar tempo, quando o usuário de parceiro clica no link no email de convite. Especificamente:

- Os atributos **SignInName** e **AltSecId** são preenchidos
- O atributo **DisplayName** altera do nome do usuário Principal (user_fabrikam.com#EXT#@contoso.com) o nome de entrada(user@fabrikam.com)

Controle esses atributos no Azure AD pode ajudá-lo a solucionar estando ou não um usuário de parceiro tem trocados seu convite de colaboração B2B.

## <a name="related-articles"></a>Artigos relacionados
Procure nossos outros artigos sobre colaboração do Azure AD B2B:

- [O que é Azure AD B2B colaboração?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Como ele funciona](active-directory-b2b-how-it-works.md)
- [Explicação passo a passo detalhada](active-directory-b2b-detailed-walkthrough.md)
- [Referência de formato de arquivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato do token de usuário externo](active-directory-b2b-references-external-user-token-format.md)
- [Limitações de visualização atual](active-directory-b2b-current-preview-limitations.md)
- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
