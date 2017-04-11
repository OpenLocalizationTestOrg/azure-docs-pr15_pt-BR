<properties
   pageTitle="Explicação passo a passo detalhada de como usar a visualização de colaboração do Azure Active Directory B2B | Microsoft Azure"
   description="Colaboração do Azure Active Directory B2B oferece suporte a suas relações entre empresas habilitando parceiros de negócios seletivamente acessar seus aplicativos corporativos"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-detailed-walkthrough"></a>Visualização de colaboração do Azure AD B2B: passo a passo detalhada

Este passo a passo descreve como usar o Azure AD B2B colaboração. Como o administrador de TI da Contoso, queremos compartilhar aplicativos com funcionários de três empresas de parceiro. Nenhuma das empresas parceiro precisa ter Azure AD.

- Alice de organização de parceiro simples
- Paulo, da organização do parceiro de média, precisa de acesso a um conjunto de aplicativos
- Clara, do organograma de parceiro complexas, precisa de acesso a um conjunto de aplicativos e participação em grupos da Contoso

Depois de convites são enviadas aos usuários de parceiro, podemos pode configurá-los no Azure AD para conceder acesso aos aplicativos e associação a grupos por meio do portal Azure. Vamos começar adicionando Alice.

## <a name="adding-alice-to-the-contoso-directory"></a>A adição de Alice ao diretório Contoso
1. Crie um arquivo. csv com os cabeçalhos, conforme mostrado, preenchendo somente Alice **emails**, **DisplayName**e **InviteContactUsUrl**. **DisplayName** é o nome que aparece no convite e também o nome que aparece no diretório Contoso Azure AD. **InviteContactUsUrl** é uma maneira de Alice contatar Contoso. No exemplo a seguir, InviteContactUsUrl Especifica o perfil de LinkedIn da Contoso. É importante representar os rótulos na primeira linha do arquivo. csv exatamente como especificado na [referência de formato de arquivo CSV](active-directory-b2b-references-csv-file-format.md).  
![Arquivo CSV de exemplo para Alice](./media/active-directory-b2b-detailed-walkthrough/AliceCSV.png)

2. No portal do Azure, adicionar um usuário no diretório Contoso (Active Directory > Contoso > usuários > Adicionar usuário). Em ""tipo de usuário suspensa, selecione "Usuários em empresas de parceiro". Carregue o arquivo. csv. Certifique-se de que o arquivo. csv é fechado antes de carregar.  
![Carregamento de arquivo CSV para Alice](./media/active-directory-b2b-detailed-walkthrough/AliceUpload.png)

3. Alice agora é representada por um usuário externo no diretório Contoso Azure AD.  
![Alice está listada no Azure AD](./media/active-directory-b2b-detailed-walkthrough/AliceInAD.png)

4. Alice recebe o email a seguir.  
![Email de convite para Alice](./media/active-directory-b2b-detailed-walkthrough/AliceEmail.png)

5. Alice clica no link, e ela é solicitada para aceitar o convite e entrar usando suas credenciais de trabalho. Se Alice não estiver no diretório Azure AD, Alice é solicitada a inscrever-se.  
![Inscrever-se depois de convite para Alice](./media/active-directory-b2b-detailed-walkthrough/AliceSignUp.png)

6. Alice será redirecionada para o painel de acesso do aplicativo, vazia até que ela é concedida acesso aos aplicativos.  
![Painel de acesso para Alice](./media/active-directory-b2b-detailed-walkthrough/AliceAccessPanel.png)

Esse procedimento permite a forma mais simples de colaboração B2B. Como um usuário no diretório Contoso Azure AD, Alice pode ser concedida acesso aos grupos por meio do portal do Azure e aplicativos. Agora vamos adicionar Paulo, quem precisa de acesso para os aplicativos Moodle e Salesforce.

## <a name="adding-bob-to-the-contoso-directory-and-granting-access-to-apps"></a>A adição de Paulo ao diretório Contoso e conceder acesso aos aplicativos
1. Usar o Windows PowerShell com o módulo Microsoft Azure AD instalado para encontrar o aplicativo IDs de Moodle e Salesforce. As IDs podem ser recuperadas usando o cmdlet: `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` isto traz uma lista de todos os aplicativos disponíveis na Contoso e seus AppPrincialIds.  
![Recuperar identificações para Paulo](./media/active-directory-b2b-detailed-walkthrough/BobPowerShell.png)

2. Crie um arquivo. csv que contém o Email de Paulo e DisplayName, **InviteAppID**, **InviteAppResources**e InviteContactUsUrl. Preencha **InviteAppResources** com o AppPrincipalIds da Moodle e Salesforce encontrados do PowerShell, separado por um espaço. Preencha **InviteAppId** com a AppPrincipalId de Moodle mesmo para o email de marca e entre páginas com um logotipo de Moodle.  
![Arquivo CSV de exemplo para Paulo](./media/active-directory-b2b-detailed-walkthrough/BobCSV.png)

3. Carregue o arquivo. csv por meio do Portal do Azure exatamente como ela foi feita para Alice. Paulo agora é um usuário externo no diretório Contoso Azure AD.

4. Paulo recebe o email a seguir.  
![Email de convite para Paulo](./media/active-directory-b2b-detailed-walkthrough/BobEmail.png)

5. Paulo clica no link e é solicitado a aceitar o convite. Depois que ele estiver conectado, ele será direcionado para o painel de acesso e já pode usar Moodle e Salesforce.  
![Painel de acesso para Paulo](./media/active-directory-b2b-detailed-walkthrough/BobAccessPanel.png)

Adicionaremos Marina a seguir, quem precisa de acesso aos aplicativos, bem como a associação a grupos no diretório de Contoso.

## <a name="adding-carol-to-the-contoso-directory-granting-access-to-apps-and-giving-group-membership"></a>Adicionando clara para o diretório de Contoso, conceder acesso aos aplicativos e fazendo associação um grupo

1. Usar o Windows PowerShell com o módulo Microsoft Azure AD instalado para localizar as identificações de aplicativo e grupo dentro da Contoso.
 - Recuperar AppPrincipalId usando o cmdlet `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`, mesmo que Paulo
 - Recuperar ID do objeto para grupos usando o cmdlet `Get-MsolGroup | fl DisplayName, ObjectId`. Isto traz uma lista de todos os grupos de Contoso e suas identificações de objeto. IDs de grupo também podem ser recuperadas como a ID de objeto na guia Propriedades do grupo no portal do Azure.  
![Recuperar IDs e grupos para Marina](./media/active-directory-b2b-detailed-walkthrough/CarolPowerShell.png)

2. Crie o arquivo. csv, preenchendo o Email de clara, DisplayName, InviteAppID, InviteAppResources, **InviteGroupResources**e InviteContactUsUrl. **InviteGroupResources** é preenchido pelas identificações de objeto dos grupos MyGroup1 e itens externos, separados por um espaço.  
![Arquivo CSV de exemplo para Marina](./media/active-directory-b2b-detailed-walkthrough/CarolCSV.png)

3. Carregue o arquivo. csv por meio do portal Azure.

4. Clara é um usuário no diretório Contoso e também é um membro dos grupos MyGroup1 e itens externos, como visto no portal do Azure.  
![Clara está listada em um grupo no Azure AD](./media/active-directory-b2b-detailed-walkthrough/CarolGroup.png)

5. Marina recebe um email contendo um link para aceitar o convite. Depois que ela se inscreve, ela será redirecionada para o painel de acesso do aplicativo tenha acesso a Moodle e Salesforce.  

Isso é tudo há para adicionar usuários de empresas de parceiro em colaboração do Azure AD B2B. Este passo a passo mostrava como adicionar usuários Alice, Paulo e clara para o diretório de Contoso usando três arquivos. csv separados. Esse processo pode ser mais fácil por condensação os arquivos. csv separados em um único arquivo.  
![Arquivo CSV de exemplo para Alice, Paulo e clara](./media/active-directory-b2b-detailed-walkthrough/CombinedCSV.png)

## <a name="related-articles"></a>Artigos relacionados
Procure nossos outros artigos sobre colaboração do Azure AD B2B:

- [O que é Azure AD B2B colaboração?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Como ele funciona](active-directory-b2b-how-it-works.md)
- [Referência de formato de arquivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato do token de usuário externo](active-directory-b2b-references-external-user-token-format.md)
- [Alterações de atributo de objeto do usuário externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitações de visualização atual](active-directory-b2b-current-preview-limitations.md)
- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
