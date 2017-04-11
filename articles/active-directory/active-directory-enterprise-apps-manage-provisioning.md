<properties
    pageTitle="Provisionamento de gerenciamento de aplicativos empresariais na visualização do Azure Active Directory do usuário | Microsoft Azure"
    description="Saiba como gerenciar provisionamento de contas de usuário para aplicativos empresariais usando a visualização do Active Directory do Azure"
    services="active-directory"
    documentationCenter=""
    authors="asmalser"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/12/2016"
    ms.author="asmalser"/>

#<a name="preview-managing-user-account-provisioning-for-enterprise-apps-in-the-new-azure-portal"></a>Visualização: Gerenciamento de conta de usuário de provisionamento para aplicativos empresariais no novo portal do Azure

Este artigo descreve como usar o [portal do Azure](https://portal.azure.com) para gerenciar a conta de usuário automáticas fornecimento e cancelamento para aplicativos que oferecem suporte a ele, especialmente aquelas que foram adicionadas da categoria "em destaque" da [Galeria de aplicativo do Active Directory do Azure](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). Esta experiência de gerenciamento no novo portal do Azure está na visualização de pública, e este artigo descreve os novos recursos, além de algumas limitações temporárias que estão no lugar durante o período de visualização. [O que é no preview?](active-directory-preview-explainer.md)

Para saber mais sobre provisionamento de contas de usuário automático e como ele funciona, consulte [automatizar provisionamento do usuário e Deprovisioning para aplicativos SaaS com o Active Directory do Azure](active-directory-saas-app-provisioning.md).

##<a name="finding-your-apps-in-the-new-portal"></a>Localizando seus aplicativos no novo portal

A partir de setembro de 2016, todos os aplicativos que foram configurados para único logon em um diretório, por um administrador de diretório usando a [Galeria de aplicativo do Active Directory do Azure](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) dentro do [Azure portal clássico](https://manage.windowsazure.com), podem agora ser visualizados e gerenciados no novo portal do Azure.

Esses aplicativos podem ser encontrados na seção **Aplicativos empresariais** do novo portal Azure, que pode ser acessada através do menu **Mais serviços** na área de navegação à esquerda. Aplicativos empresariais são aplicativos que foram implantados e estão sendo usados por usuários de sua organização.

![Blade de aplicativos corporativos][0]

Selecionando o link de **todos os aplicativos** à esquerda mostra uma lista de todos os aplicativos que foram configurados, incluindo aplicativos que tiveram sido adicionados da Galeria. Selecionar um aplicativo carrega a lâmina de recursos para esse aplicativo, onde relatórios podem ser exibidos para esse aplicativo e uma variedade de configurações pode ser gerenciada.

Configurações de provisionamento de contas de usuário podem ser gerenciadas selecionando **provisionamento** à esquerda.

![Blade de recurso do aplicativo][1]


##<a name="provisioning-modes"></a>Modos de provisionamento

A lâmina de **provisionamento** começa com um menu **modo** , que mostra quais modos de provisionamento são suportados para um aplicativo de empresa e permite que eles sejam configurados. As opções disponíveis incluem:

* **Automático** - esta opção aparecerá se o Azure AD suporta provisionamento automático baseado em API e/ou eliminação de provisionamento de contas de usuário para este aplicativo. Selecionar esse modo exibe uma interface que orienta administradores por meio de Configurando Azure AD para se conectar à API de gerenciamento de usuário do aplicativo, Criando mapeamentos de conta e fluxos de trabalho que definem como os dados de conta de usuário devem fluir entre Azure AD e o aplicativo e gerenciar o Azure AD serviço de provisionamento.

* **Manual** - esta opção é mostrado se Azure AD não suporta automática de provisionamento de contas de usuário para este aplicativo. Esta opção significa que os registros de conta de usuário armazenados no aplicativo devem ser gerenciados usando um processo externo, com base nos recursos de gerenciamento e provisionamento de usuário fornecidos pelo aplicativo (que pode incluir SAML Just-In-Time provisionamento).


##<a name="configuring-automatic-user-account-provisioning"></a>Configurando provisionamento de contas de usuário automático

Selecionando a opção **automático** exibe uma tela que é dividida em quatro seções:

###<a name="admin-credentials"></a>Credenciais de administrador
Isso é onde as credenciais necessárias para Azure AD para se conectar à API são inseridos de gerenciamento de usuários do aplicativo. A entrada necessária varia dependendo do aplicativo. Para saber mais sobre os tipos de credencial e requisitos para aplicativos específicos, consulte o [tutorial de configuração para esse aplicativo específico](active-directory-saas-app-provisioning.md#list-of-apps-that-support-automated-user-provisioning).

Selecionando o botão **Testar Conexão** permite que você teste as credenciais tendo Azure tentativa do AD para se conectar ao aplicativo do provisionamento aplicativo usando as credenciais fornecidas.

###<a name="mappings"></a>Mapeamentos
Isso é onde os administradores podem exibir e editar o fluxo de atributos de usuário entre Azure AD e o aplicativo de destino, quando as contas de usuário são provisionadas ou atualizadas.

Há um conjunto predefinido de mapeamentos entre objetos de usuário do Azure AD e objetos de usuário do cada aplicativo SaaS. Alguns aplicativos gerenciar outros tipos de objetos, como grupos ou contatos. Selecionando um desses mapeamentos a tabela mostra o editor de mapeamento à direita, que pode ser visualizados e personalizados.

![Blade de recurso do aplicativo][2]

Personalizações com suporte durante a primeira visualização incluem:

* Ativando e desativando mapeamentos para objetos específicos, como o objeto de usuário do Azure AD ao objeto de usuário do aplicativo SaaS.

* Edição de fluam de quais atributos do objeto do usuário Azure AD para objeto de usuário do aplicativo. Para obter mais informações sobre o mapeamento de atributo, consulte [Noções básicas sobre tipos de mapeamento de atributo](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types).

* Filtre quais ações de provisionamento Azure AD deve executar no aplicativo de destino, o que é um novo recurso no portal do Azure. Em vez de ter o Azure AD-sincronizar totalmente objetos, você pode limitar as ações executadas. Por exemplo, por apenas selecionando **Update**, as atualizações somente do Azure AD contas em um aplicativo de usuário existente e não criar novos. Selecionando apenas **criar**, Azure somente cria novas contas de usuário, mas não atualiza os existentes. Esse recurso permite que administradores criar mapeamentos diferentes para a criação de conta e atualizar fluxos de trabalho. A capacidade total de criar vários mapeamentos por aplicativo está planejada para posteriormente no período de visualização.

###<a name="settings"></a>Configurações
Esta seção permite que administradores iniciar e parar o Azure AD provisionamento de serviço para o aplicativo selecionado, bem como opcionalmente limpar o cache de provisionamento e reinicie o serviço.

Se provisionamento está sendo ativado pela primeira vez para um aplicativo, habilite o serviço alterando o **Status de provisionamento** para **On**. Isso faz com que o Azure AD serviço de provisionamento realizar uma sincronização inicial, onde ele lê os usuários atribuídos na seção **usuários e grupos** , consultas o aplicativo de destino para eles e, em seguida, executa as ações de provisionamento definidas na seção **mapeamentos** de Azure AD. Durante esse processo, o serviço de provisionamento armazena dados armazenados em cache sobre quais contas de usuário que ele está gerenciando, portanto não gerenciado contas dentro os aplicativos de destino que foram nunca no escopo de atribuição não são afetadas pelas operações de provisionamento eliminação. Após a sincronização inicial, o serviço de provisionamento sincroniza automaticamente objetos de usuário e grupo em um intervalo de dez minutos.

Alterar o **Status de provisionamento** para **Desativar** simplesmente pausa o serviço de provisionamento. Nesse estado, Azure não criar, atualizar ou remover qualquer usuário ou agrupar objetos no aplicativo. Alterando o estado para ativado faz com que o serviço para saber onde parou.

Selecionando a caixa de seleção **Limpar estado atual e reinicie a sincronização** e salvando interrompe o serviço de provisionamento, despejos os dados armazenados em cache sobre quais contas Azure AD está gerenciando, reiniciar os serviços e executa a sincronização inicial novamente. Essa opção permite que administradores começar o processo de implantação de provisionamento novamente.

###<a name="synchronization-details"></a>Detalhes de sincronização
Esta seção fornece detalhes de adição sobre a operação do serviço de provisionamento, incluindo os horários primeiro e últimos que o serviço de provisionamento executou contra o aplicativo e quantos objetos de usuário e grupo estão sendo gerenciados.

Links são fornecidos para o **relatório de atividades de provisionamento**, que fornece um log de todos os usuários e grupos criado, atualizado e removido entre Azure AD e o destino do aplicativo e o **relatório de erros de provisionamento** que fornece mais detalhada mensagens de erro para objetos de usuário e grupo que falhou ao ser lido, criado, atualizado ou removido. 

[0]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-blade.PNG
[1]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.PNG
[2]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.PNG
