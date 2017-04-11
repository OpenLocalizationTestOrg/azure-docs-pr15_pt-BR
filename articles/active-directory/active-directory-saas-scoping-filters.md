<properties
    pageTitle="Aplicativo baseado no atributo provisionamento com escopo filtros | Microsoft Azure"
    description="Saiba como usar filtros de escopo para impedir que objetos nos aplicativos que oferecem suporte ao usuário automatizado de provisionamento de realmente sendo provisionado se um objeto não atender às suas necessidades de negócios."
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


# <a name="attribute-based-app-provisioning-with-scoping-filters"></a>Aplicativo baseado no atributo provisionamento com filtros de escopo

O objetivo desta seção é explicar como usar filtros de escopo para definir regras baseadas em atributo que determinam quais usuários serão configurados para o aplicativo.





## <a name="clauses-and-scope-groups"></a>Cláusulas e grupos de escopo


![Filtro de escopo][1] 




Filtros de escopo são definidos por um ou mais **grupos de escopo**, cada um deles mantenha uma ou mais **cláusulas**. Para ver as cláusulas para um grupo de escopo específico, expandi-la clicando na seta à esquerda do nome do grupo.

Uma **cláusula** determina quais usuários têm permissão para passar pelo filtro de escopo avaliando os atributos de cada usuário. Por exemplo, você pode ter uma cláusula que requer que o atributo de 'estado' de um usuário seja igual a Nova York, o que significa que somente os usuários de Nova York serão configurados para o aplicativo.

![Nome do grupo de escopo][2] 



Cada **grupo escopo** começa com obrigatório uma **cláusula**, conforme mostrado na captura de tela acima. Esta cláusula simplesmente informa que o usuário primeiro deve ser atribuído ao aplicativo antes que ela seja avaliada por seus filtros de escopo. Esta cláusula não pode ser excluída ou modificada.

Você pode adicionar novas cláusulas ou novos grupos de escopo pressionando o botão apropriado. Você pode dar um nome de cada grupo de escopo editando sua propriedade **Nome do grupo de escopo** .





## <a name="how-scoping-filters-are-evaluated"></a>Como os filtros de escopo são avaliados

Durante a configuração, testamos cada usuário atribuído em relação a seus filtros escopo para determinar se esse usuário merece acesso ao aplicativo. Você pode considerar cada cláusula como sendo um teste que deve ser passado na ordem para o usuário evitar obtendo filtrados. 

Se você tiver vários grupos de escopo definidos, cada usuário deve passar pelo menos um para acessar o aplicativo. Dentro de cada grupo de escopo, no entanto, o usuário deve passar cada cláusula única para passar esse grupo escopo específico. 

Em outras palavras, você pode pensar grupos de escopo como sendo ou agrupados e você pode considerar das cláusulas neles como sendo e agrupados. Por exemplo, considere o filtro de escopo abaixo:


![Nome do grupo de escopo][2]  


De acordo com a esse filtro de escopo, os usuários devem atender aos seguintes critérios, para ser provisionado:

1. Elas devem ser atribuídas ao aplicativo.

2. Eles devem trabalhar no departamento de engenharia

3. Eles devem ser trabalho de são Francisco ou Canadá.


##<a name="related-articles"></a>Artigos relacionados

- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
- [Automatizar usuário provisionamento e desprovisionamento para aplicativos SaaS](active-directory-saas-app-provisioning.md)
- [Personalizando os mapeamentos de atributo de provisionamento do usuário](active-directory-saas-customizing-attribute-mappings.md)
- [Escrevendo expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Notificações de provisionamento de conta](active-directory-saas-account-provisioning-notifications.md)
- [Usando SCIM para habilitar o fornecimento automático de usuários e grupos do Active Directory do Azure para aplicativos](active-directory-scim-provisioning.md)
- [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-scoping-filters/ic782811.png
[2]: ./media/active-directory-saas-scoping-filters/ic782812.png
[3]: ./active-directory-saas-scoping-filters/ic782813.png
