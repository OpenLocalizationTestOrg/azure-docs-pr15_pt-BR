<properties
   pageTitle="Colaboração do Azure Active Directory B2B | Microsoft Azure"
   description="Azure Active Directory B2B colaboração permite que parceiros de negócios acessar seus aplicativos corporativos, com cada um dos seus usuários são representados por um único Azure AD conta"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="azure-active-directory-b2b-collaboration"></a>Colaboração do Azure Active Directory B2B

Azure Active Directory (AD Azure) B2B colaboração permite que você habilitar o acesso aos seus aplicativos corporativos de identidades gerenciados pelo parceiro. Você pode criar relações entre empresas por convidando e autorização de usuários de empresas de parceiro para acessar seus recursos. Complexidade é reduzida porque cada empresa agrupa uma vez com o Active Directory do Azure e cada usuário é representado por uma única conta Azure AD. Segurança é aumentada se seus parceiros de negócios gerenciar suas contas no Azure AD porque o acesso foi revogado quando os usuários do parceiro são encerrados de suas organizações e acesso não intencionais por meio de associação em diretórios internos é impedido. Para parceiros de negócios que ainda não tem o Azure AD, colaboração B2B tem uma experiência simplificada de inscrição para fornecer contas do Azure AD para seus parceiros de negócios.

-   Seus parceiros de negócios usam suas próprias credenciais de entrada, que libera você do gerenciamento de um diretório de parceiro externo e da necessidade de remover acesso quando os usuários saem da organização do parceiro.

-   Gerenciar o acesso aos seus aplicativos independentemente dos ciclo de vida de conta do seu parceiro de negócios. Por exemplo, isso significa que você pode revogar o acesso sem precisar pergunte o departamento de TI do seu parceiro de negócios fazer nada.

## <a name="capabilities"></a>Recursos

Colaboração de B2B simplifica o gerenciamento e melhora a segurança de acesso do parceiro aos recursos corporativos, incluindo SaaS aplicativos como o Office 365, Salesforce, serviços do Azure e cada mobile, nuvem e aplicativo de reconhecimento de local. B2B colaboração permite aos parceiros gerenciar suas próprias contas e empresas podem aplicar políticas de segurança para o acesso do parceiro.

Azure B2B de diretório ativo colaboração é fácil de configurar com simplificado inscrição para parceiros de todos os portes, mesmo que ela não tenha suas próprias Azure Active Directory por meio de um processo verificado por email. Também é fácil de manter com nenhum diretórios externos ou por configurações de Federação do parceiro.

## <a name="b2b-collaboration-process"></a>Processo de colaboração de B2B

1. Azure AD B2B colaboração permite que um administrador da empresa convidar e autorizar um conjunto de usuários externos ao carregar um arquivo de valores separados por vírgula (CSV) de linhas não mais do que 2000 para o portal de colaboração B2B.

  ![Caixa de diálogo de carregamento de arquivo CSV](./media/active-directory-b2b-collaboration-overview/upload-csv.png)

2. O portal enviará convites por email para esses usuários externos.

3. Usuário convidado irá entrar uma conta existente do trabalho com a Microsoft (gerenciado no Azure AD), ou obter uma nova conta de trabalho no Azure AD.

4. Uma vez conectado, o usuário será redirecionado para o aplicativo que foi compartilhado com eles.

Convites para endereços de email do consumidor (por exemplo, Gmail ou [*comcast.net*](http://comcast.net/)) não são suportados no momento.

Para obter mais informações sobre como funciona o B2B colaboração, assista [Este vídeo](http://aka.ms/aadshowb2b).

## <a name="next-steps"></a>Próximas etapas
Navegue nossos outros artigos sobre colaboração do Azure AD B2B.

- [O que é Azure AD B2B colaboração?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Como ele funciona](active-directory-b2b-how-it-works.md)
- [Explicação passo a passo detalhada](active-directory-b2b-detailed-walkthrough.md)
- [Referência de formato de arquivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato do token de usuário externo](active-directory-b2b-references-external-user-token-format.md)
- [Alterações de atributo de objeto do usuário externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitações de visualização atual](active-directory-b2b-current-preview-limitations.md)
- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
