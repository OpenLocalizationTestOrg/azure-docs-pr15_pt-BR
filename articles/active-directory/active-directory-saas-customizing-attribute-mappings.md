<properties
    pageTitle="Personalizando os mapeamentos de atributo | Microsoft Azure"
    description="Saiba quais mapeamentos de atributo para aplicativos SaaS no Active Directory do Azure como você pode modificá-los para atender às suas necessidades de negócios."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="customizing-attribute-mappings"></a>Personalizando os mapeamentos de atributo


Microsoft Azure AD oferece suporte para provisionamento do usuário para aplicativos SaaS de terceiros como Salesforce, Google Apps e outras pessoas. Se você tiver provisionamento do usuário para um terceiro aplicativo SaaS ativado, o Portal de gerenciamento do Azure controla seus valores de atributo no formulário de uma configuração chamado de "mapeamento de atributo".

Há um conjunto predefinido de mapeamentos de atributo entre objetos de usuário do Azure AD e objetos de usuário do cada aplicativo SaaS. Alguns aplicativos gerenciar outros tipos de objetos, como grupos ou contatos. <br> 
Você pode personalizar os mapeamentos de atributo padrão acordo com suas necessidades de negócios. Isso significa, você pode alterar ou excluir os mapeamentos de atributo existentes ou criar novos mapeamentos de atributo.

No portal do Azure AD, você pode acessar esse recurso clicando em atributos na barra de ferramentas de um aplicativo de SaaS.

> [AZURE.NOTE] O link de **atributos** só estará disponível se você tiver habilitado para um aplicativo de SaaS de provisionamento do usuário. 


![SalesForce][1] 


Quando você clica em atributos na barra de ferramentas, a lista de mapeamentos atuais que estão configurados para um aplicativo de SaaS.

A captura de tela a seguir mostra um exemplo para isso:



![SalesForce][2]  


No exemplo acima, você pode ver que o atributo de **nome** de um objeto gerenciado no Salesforce é preenchido com o valor de **givenName** de vinculado objeto do Azure AD.

Se você deseja personalizar os mapeamentos de atributo ou se você quiser reverter personalizadas configurações de volta para a configuração padrão, você pode fazer isso clicando no botão relacionado na barra de ferramentas na parte inferior de um aplicativo.


![SalesForce][3]  


Há mapeamentos de atributo que são exigidos por um aplicativo de SaaS funcione corretamente. Na tabela atributos, os mapeamentos de atributo relacionado tem **Sim** como valor do atributo **obrigatório** . Se um mapeamento de atributo for necessário, você não pode excluí-la. Nesse caso, **Excluir** recurso não está disponível.

Para modificar um mapeamento de atributo existente, selecione apenas o mapeamento e clique em **Editar**. Isto traz uma página de diálogo que permite que você modifique o mapeamento de atributo selecionado.


![Editar mapeamento de atributo][4]  



## <a name="understanding-attribute-mapping-types"></a>Noções básicas sobre tipos de mapeamento de atributos


Com os mapeamentos de atributo, que você controle como os atributos são preenchidos em um terceiro SaaS aplicativo de terceiros. Há quatro diferentes tipos de mapeamento suportados:

- **Direct** – o atributo de destino é preenchida com o valor de um atributo do objeto vinculado no Azure AD.


- **Constante** – o atributo de destino é preenchida com uma cadeia de caracteres específica que você especificou.


- **Expressão** - o atributo de destino é preenchida com base no resultado de uma expressão de script semelhante. Para obter mais detalhes, consulte [Expressões de escrita para mapeamentos de atributo no Active Directory do Azure](active-directory-saas-writing-expressions-for-attribute-mappings.md).


- **Nenhum** - o atributo de destino é esquerda inalterada. No entanto, se o atributo de destino estiver nunca vazio, ele será preenchido com o valor padrão que você especificar.



Além desses quatro tipos de mapeamento de atributo básica, mapeamentos de atributo personalizado suportam o conceito de uma atribuição de valor **padrão** . A atribuição de valor padrão garante que um atributo de destino é preenchido com um valor se não houver nenhum valor no Azure AD nem no objeto de destino.

Microsoft Azure AD fornece uma implementação muito eficiente de um processo de sincronização. Em um ambiente inicializado, somente os objetos que exigem atualizações são processados durante um ciclo de sincronização. Atualizar mapeamentos de atributo tem um impacto sobre o desempenho de um ciclo de sincronização. Isso ocorre porque uma atualização para a configuração de mapeamento de atributo requer todos os objetos gerenciados ser reavaliadas. Por isso, é uma prática recomendada para manter o número de alterações consecutivas para os mapeamentos de atributo no mínimo.


##<a name="related-articles"></a>Artigos relacionados

- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
- [Automatizar usuário provisionamento/desprovisionamento aos aplicativos SaaS](active-directory-saas-app-provisioning.md)
- [Escrevendo expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Escopo filtros para provisionamento do usuário](active-directory-saas-scoping-filters.md)
- [Usando SCIM para habilitar o fornecimento automático de usuários e grupos do Active Directory do Azure para aplicativos](active-directory-scim-provisioning.md)
- [Notificações de provisionamento de conta](active-directory-saas-account-provisioning-notifications.md)
- [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)


<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png
