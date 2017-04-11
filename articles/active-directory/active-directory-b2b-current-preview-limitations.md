<properties
   pageTitle="Limitações de visualização atual para colaboração do Azure Active Directory B2B | Microsoft Azure"
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
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-current-preview-limitations"></a>Visualização de colaboração do Azure AD B2B: atual visualizar limitações

- Autenticação multifator (MFA) não há suporte para usuários externos. Por exemplo, se Contoso tem MFA, mas não Org de parceiro, em seguida, parceiro Org usuários não não possível conceder MFA por meio de colaboração B2B.
- Convites são possíveis somente via CSV; acesso de API e convites individuais não são suportados.
- Somente administradores globais do Azure AD pode carregar arquivos. csv.
- Convites para endereços de email do consumidor (como hotmail.com, Gmail.com ou comcast.net) não são suportados no momento.
- Acesso de usuário externo para locais aplicativos não testados.
- Usuários externos não são automaticamente limpos quando o usuário real é excluído do seu diretório.
- Convites para listas de distribuição não são suportados.
- Máximo de 2.000 registros pode ser carregado via CSV.

## <a name="related-articles"></a>Artigos relacionados
Procure nossos outros artigos sobre colaboração do Azure AD B2B:

- [O que é Azure AD B2B colaboração?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Como ele funciona](active-directory-b2b-how-it-works.md)
- [Explicação passo a passo detalhada](active-directory-b2b-detailed-walkthrough.md)
- [Referência de formato de arquivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato do token de usuário externo](active-directory-b2b-references-external-user-token-format.md)
- [Alterações de atributo de objeto do usuário externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
