<properties
   pageTitle="Formato de arquivo CSV para visualização de colaboração do Azure Active Directory B2B | Microsoft Azure"
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

# <a name="azure-ad-b2b-collaboration-preview-csv-file-format"></a>Visualização de colaboração do Azure AD B2B: formato de arquivo CSV

A versão de visualização de colaboração do Azure AD B2B requer um arquivo CSV especificando informações de usuário do parceiro a ser carregado por meio do portal do Azure AD. O arquivo CSV deve conter os rótulos necessários abaixo e campos opcionais conforme necessário. Modificar o arquivo CSV de exemplo (abaixo) sem alterar a ortografia dos rótulos na primeira linha.

>[AZURE.NOTE] A primeira linha de rótulos (como Email, DisplayName e assim por diante) é necessária para o arquivo CSV ser analisada com êxito. A ortografia deve corresponder os campos especificados abaixo. Esses rótulos identificam o conteúdo sob elas. Campos opcionais que não são necessárias, seus rótulos podem ser removidos do arquivo CSV. A coluna inteira pode ser deixada vazia.

## <a name="required-fields-br"></a>Campos obrigatórios: <br/>
**Email:** Endereço de email do usuário convidado. <br/>
**DisplayName:** Nome de exibição de usuário convidado (geralmente, primeiro e último nome).<br/>


## <a name="optional-fields-br"></a>Campos opcionais: <br/>

**InvitationText:** Personalize o texto de email de convite após a marca de aplicativo e antes que o link de resgate.<br/>
**InvitedToApplications:** AppIDs aplicativos corporativos para atribuir aos usuários. AppIDs são recuperáveis no PowerShell chamando`Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/>
**InvitedToGroups:** Identificações de objeto para grupos para adicionar usuário. Identificações de objeto são recuperáveis no PowerShell chamando`Get-MsolGroup | fl DisplayName, ObjectId`<br/>
**InviteRedirectURL:** URL para direcionar um usuário convidado após a aceitação do convite. Isso deve ser uma URL específica da empresa (como [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)). Se esse campo opcional não for especificado, o usuário convidado será direcionado para o painel de acesso do aplicativo onde eles podem navegar para seus aplicativos corporativos escolhidos. A URL do painel de Access App é do formulário `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`.<br/>
**CcEmailAddress**: endereço copiar convite enviado de Email. Se o campo CcEmailAddress é usado, este convite não pode ser usado para usuário verificado por email ou criação de locatário.<br/>
**Idioma:** Idioma para as experiência de email e resgate convite, com "pt" (em inglês) como o padrão quando não especificado. Os outros 10 suportados idioma códigos são:<br/>
1. de: alemão<br/>
2. es: Espanhol<br/>
3. FR: francês<br/>
4. ele: italiano<br/>
5. ja: japonês<br/>
6. Co: coreano<br/>
7. pt-BR: português (Brasil)<br/>
8. RU: russo<br/>
9. zh-HANS: chinês simplificado<br/>
10. zh-HANT: chinês tradicional<br/>

## <a name="sample-csv-file"></a>Arquivo CSV de exemplo
Aqui está um exemplo CSV que você pode modificar.

>[AZURE.NOTE] Copiar e colar isto no bloco de notas e salve-o com uma extensão de arquivo '. csv'. Edite isso no Excel. Ele vai ser estruturado em uma tabela com rótulos na primeira linha.

> Adicione mais campos opcionais no Excel, especificando a etiqueta e preencher a coluna abaixo dele.

```
Email,DisplayName,InvitationText,InviteRedirectUrl,InvitedToApplications,InvitedToGroups,CcEmailAddress,Language
wharp@contoso.com,Walter Harp,Hi Walter! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
jsmith@contoso.com,Jeff Smith,Hi Jeff! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
bsmith@contoso.com,Ben Smith,Hi Ben! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en

```

## <a name="related-articles"></a>Artigos relacionados
Procurar nossos outros artigos sobre colaboração do Azure AD B2B

- [O que é Azure AD B2B colaboração?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Como ele funciona](active-directory-b2b-how-it-works.md)
- [Explicação passo a passo detalhada](active-directory-b2b-detailed-walkthrough.md)
- [Formato do token de usuário externo](active-directory-b2b-references-external-user-token-format.md)
- [Alterações de atributo de objeto do usuário externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitações de visualização atual](active-directory-b2b-current-preview-limitations.md)
- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
