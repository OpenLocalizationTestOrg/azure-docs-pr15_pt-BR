<properties
   pageTitle="Azure Active Directory relatório - visualização | Microsoft Azure"
   description="Lista os vários relatórios disponíveis para visualização do Active Directory do Azure"
   services="active-directory"
   documentationCenter=""
   authors="markusvi"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/30/2016"
   ms.author="markvi"/>

# <a name="azure-active-directory-reporting---preview"></a>Azure Active Directory relatório - visualização

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-reporting-azure-portal.md)
- [Azure portal clássico](active-directory-reporting-guide.md)

*Esta documentação é parte do [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).*

Com relatórios na visualização do Active Directory do Azure, você obtém todas as informações que necessárias para determinar como seu ambiente está fazendo. [O que é no preview?](active-directory-preview-explainer.md)

Há duas áreas principais de relatórios:

- **Atividades de entrada** : informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário

- **Logs de auditoria** - informações de atividade do sistema sobre usuários e gerenciamento de grupo, seus aplicativos gerenciados e atividades de diretório

Dependendo do escopo dos dados que você está procurando, você pode acessá esses relatórios clicando em **usuários e grupos** ou **aplicativos corporativos** na lista de serviços no [portal do Azure](https://portal.azure.com).

## <a name="sign-in-activities"></a>Atividades de entrada

### <a name="user-sign-in-activities"></a>Atividades de entrada do usuário

Com as informações fornecidas pelo relatório de entrada de usuário, Encontre respostas para perguntas como:

- O que é o padrão de entrada de um usuário?
- Quantos usuários têm usuários conectados mais de uma semana?
- O que é o status desses suplementos de entrada?

Seu ponto de entrada a esses dados é o usuário entrar graph na seção **Visão geral** em **usuários e grupos**.

 ![Relatório] (./media/active-directory-reporting-azure-portal/05.png "Relatório")

Gráfico de entrada do usuário mostra agregações semanais de entrada ins para todos os usuários em um determinado período de tempo. O padrão para o período de tempo é 30 dias.

![Relatório] (./media/active-directory-reporting-azure-portal/02.png "Relatório")

Quando você clica em um dia no gráfico entrar, você pode obter uma lista detalhada das atividades entrar.

![Relatório] (./media/active-directory-reporting-azure-portal/03.png "Relatório")

Cada linha na lista atividades entrar fornece as informações detalhadas sobre a entrar selecionado como:

- Quem tem conectado?

- Qual foi o UPN relacionado?

- Qual aplicativo destinou a entrar?

- O que é o endereço IP do sinal do?

- Qual foi o status de entrada do?

### <a name="usage-of-managed-applications"></a>Uso de aplicativos gerenciados

Com uma exibição centrados em aplicativos dos seus dados de entrada, você pode responder perguntas como:

- Quem está usando Meus aplicativos?

- Quais são os principais 3 aplicativos em sua organização?

- Posso ter distribuiu recentemente um aplicativo. Como ele está fazendo?


Seu ponto de entrada a esses dados é superiores 3 aplicativos em sua organização dentro do último relatório de 30 dias na seção **Visão geral** em **aplicativos corporativos**.

 ![Relatório] (./media/active-directory-reporting-azure-portal/06.png "Relatório")


As agregações de semanal de gráfico de uso de aplicativo de entrar ins para seus aplicativos de 3 principais um determinado período de tempo. O padrão para o período de tempo é 30 dias.

![Relatório] (./media/active-directory-reporting-azure-portal/78.png "Relatório")

Se desejar, você pode definir o foco em um aplicativo específico.

![Relatório] (./media/active-directory-reporting-azure-portal/single_spp_usage_graph.png "Relatório")


Quando você clica em um dia do gráfico de uso do aplicativo, você obter uma lista detalhada das atividades entrar.


![Relatório] (./media/active-directory-reporting-azure-portal/top_app_sign_ins.png "Relatório")



A opção de **entrada-ins** fornece uma visão geral completa de todos os eventos de entrada a seus aplicativos.

![Relatório] (./media/active-directory-reporting-azure-portal/85.png "Relatório")

Usando o seletor de coluna, você pode selecionar os campos de dados que você deseja exibir.

![Relatório] (./media/active-directory-reporting-azure-portal/column_chooser.png "Relatório")



### <a name="filtering-sign-ins"></a>Filtragem de entrada

Você pode filtrar inscrições por um intervalo de tempo para limitar a quantidade de dados exibidos.

![Relatório] (./media/active-directory-reporting-azure-portal/927.png "Relatório")


Outro método para filtrar as entradas das atividades entrar é procurar entradas específicas.
O método de pesquisa permite escopo sua entrada suplementos em torno de determinados **usuários**, **grupos** ou **aplicativos**.


![Relatório] (./media/active-directory-reporting-azure-portal/84.png "Relatório")

## <a name="audit-logs"></a>Logs de auditoria

Os logs de auditoria no Active Directory do Azure fornecem registros de atividades de sistema para fins de conformidade.

Há três categorias principais para auditoria atividades relacionadas no portal do Azure:

- Usuários e grupos   

- Aplicativos

- Diretório   


Para obter uma lista completa de atividades de relatório de auditoria, consulte a [lista de eventos de relatório de auditoria](active-directory-reporting-audit-events.md#list-of-audit-report-events).


Seu ponto de entrada a todos os dados de auditoria é **logs de auditoria** na seção **atividade** do **Azure Active Directory**.


![Auditoria] (./media/active-directory-reporting-azure-portal/61.png "Auditoria")


Um log de auditoria tem um modo de exibição de lista que mostra os atores (quem), as atividades (o que) e os destinos.


![Auditoria] (./media/active-directory-reporting-azure-portal/345.png "Auditoria")


Clicando em um item na exibição de lista, você pode obter mais detalhes sobre ele.

![Auditoria] (./media/active-directory-reporting-azure-portal/873.png "Auditoria")




### <a name="users-and-groups-audit-logs"></a>Logs de auditoria de usuários e grupos


Com o usuário e relatórios de auditoria baseado em grupo, você pode obter respostas a perguntas como:

- Quais tipos de atualizações foram aplicados os usuários?

- Quantos usuários foram alterados?

- Quantas senhas foram alteradas?

- O que um administrador tenha feito em um diretório?

- Quais são os grupos que foram adicionados?

- Existem grupos com alterações de associação?

- Os proprietários do grupo foram alterados?

- Quais licenças foram atribuídas a um grupo ou um usuário?


Se você quiser revisar dados de auditoria relacionados a usuários e grupos, você pode encontrar um modo de exibição filtrado em **logs de auditoria** na seção **atividade** de **usuários e grupos**.


![Auditoria] (./media/active-directory-reporting-azure-portal/93.png "Auditoria")


### <a name="application-audit-logs"></a>Logs de auditoria de aplicativo

Relatórios de auditoria com baseada em aplicativo, você pode obter respostas a perguntas como:

- Quais são os aplicativos que foram adicionados ou atualizados?

- Quais são os aplicativos que foram removidos?

- Um princípio de serviço para um aplicativo mudou?

- Os nomes dos aplicativos foram alterados?

- Quem deu consentimento para um aplicativo?


Se você quiser revisar dados de auditoria relacionados a aplicativos, você pode encontrar um modo de exibição filtrado em **logs de auditoria** na seção de **atividade** de **aplicativos corporativos**.


![Auditoria] (./media/active-directory-reporting-azure-portal/134.png "Auditoria")


### <a name="filtering-audit-logs"></a>Filtragem logs de auditoria

Você pode filtrar um relatório de auditoria por um intervalo de tempo para limitar a quantidade de dados exibidos.

![Auditoria] (./media/active-directory-reporting-azure-portal/324.png "Auditoria")

Outro método para filtrar as entradas de um log de auditoria é procurar entradas específicas.

![Auditoria] (./media/active-directory-reporting-azure-portal/237.png "Auditoria")

## <a name="next-steps"></a>Próximas etapas

Consulte o [Active Directory do Azure guia de relatórios](active-directory-reporting-guide.md).
