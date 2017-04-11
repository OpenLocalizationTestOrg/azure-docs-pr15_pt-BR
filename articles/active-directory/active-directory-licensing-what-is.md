<properties
    pageTitle="O que é o licenciamento de Microsoft Azure Active Directory? | Microsoft Azure"
    description="Descrição de licenciamento do Microsoft Azure Active Directory, como ele funciona, como começar e práticas recomendadas, incluindo o Office 365, Microsoft Intune e Azure Active Directory Premium e edições básicas"
    services="active-directory"
      keywords="Licenciamento do Azure AD"
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

# <a name="what-is-microsoft-azure-active-directory-licensing"></a>O que é o licenciamento de Microsoft Azure Active Directory?

##<a name="description"></a>Descrição
Azure Active Directory (AD Azure) é identidade da Microsoft como uma solução de serviço (IDaaS) e a plataforma. Azure AD é oferecida em um número de versões técnicas e funcionais desde Azure AD livre, que está disponível com qualquer serviço Microsoft como o Office 365, Dynamics, Microsoft Intune e Azure (Azure AD não gerar qualquer encargos de consumo nesse modo), ao Azure AD pagos versões como pacote de mobilidade da empresa (EMS), Azure AD Premium e básico, bem como autenticação multifator da Azure (MFA). Como muitos do Microsoft online services, mais Azure AD pago versões são entregues por meio de direitos por usuário, como eles estão no Office 365, Microsoft Intune e Azure AD. Nesses casos, a compra de serviço é representada com uma ou mais assinaturas e cada assinatura inclui um número de pré-lançamento de compra de licenças em seu locatário. Direitos por usuário são obtidos por meio de atribuição de licença, criar um vínculo entre o usuário e o produto, permitindo que os componentes do serviço para o usuário e consumindo uma das licenças pré-paga.

[Experimente o Azure AD premium agora.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

> [AZURE.NOTE] Portal de administração do Azure AD é uma parte do portal de clássico do Azure. Embora usando o Azure AD não exige qualquer Azure compras, acessar esse portal requer uma assinatura ativa do Azure ou uma [assinatura de avaliação do Azure](https://azure.microsoft.com/pricing/free-trial/).

Para obter uma visão geral dos recursos de serviço do Azure AD, consulte [o que é Azure AD](active-directory-whatis.md).
[Saiba mais sobre os níveis de serviço do Azure AD](https://azure.microsoft.com/support/legal/sla/)

> [AZURE.NOTE]  Assinaturas do Azure pré-pago são diferentes: enquanto também representados no seu diretório, essas assinaturas habilita a criação de recursos do Azure e mapeá-los para seu método de pagamento. Nesse caso, há nenhuma contagens de licença associadas a assinatura. Associação dos usuários com a assinatura, o acesso dos usuários para gerenciar recursos de assinatura, é alcançada concedendo eles permissões para operar em recursos Azure mapeados para a assinatura.


##<a name="how-does-azure-ad-licensing-work"></a>Como funciona o licenciamento do Azure AD?

Baseada em licença (baseado em direito) Azure AD serviços trabalho ativando uma assinatura em seu locatário do Azure AD/serviço de diretório. Assim que a assinatura estiver ativa os recursos de serviço podem ser gerenciados por administradores de serviço de diretório/e usados pelos usuários licenciados.

Quando você compra ou ativar o pacote de mobilidade do Enterprise, Azure AD Premium ou do Azure AD básica, seu diretório é atualizado com a assinatura, incluindo seu período de validade e licenças pré-paga. Suas informações de assinatura, incluindo status, próximo evento de ciclo de vida e o número de licenças atribuídas ou disponíveis está disponível por meio do portal de clássico Azure sob a guia de licenças para o diretório específico. Isso também é melhor lugar para gerenciar suas atribuições de licença.

Cada assinatura consiste em um ou mais planos de serviço, cada um mapeamento o nível funcional incluído do tipo serviço; Por exemplo, Azure AD, MFA do Azure, Microsoft Intune, Exchange Online ou do SharePoint Online. Gerenciamento de licenças do Azure AD não exige gerenciamento de nível de plano de serviço. Isso é diferente do Office 365, que depende este modo de configuração avançada para gerenciar o acesso aos serviços incluídos. Azure AD depende na configuração do serviço, para habilitar recursos e gerenciar permissões individuais.

Em geral, informações de assinatura do Azure AD são gerenciadas por meio do portal de clássico Azure, sob a guia de licenças para o diretório específico. Assinaturas de AD Azure, com exceção do Azure AD Premium, não aparecerão no portal do Office.

> [AZURE.IMPORTANT] Azure AD Premium e básico, bem como assinaturas de pacote de mobilidade do Enterprise, se restringe a seu diretório/locatário provisionado. Assinaturas não podem ser divididas entre diretórios ou usadas para dão direito usuários em outros diretórios. Mover uma assinatura entre diretórios é possível, mas exige enviando um tíquete ou cancelamento e adquirir novamente no caso de compras diretas.

> Ao comprar Azure AD ou pacote de mobilidade do Enterprise por meio do licenciamento por Volume da ativação da assinatura ocorrerá automaticamente quando o contrato inclui outros serviços Online da Microsoft, por exemplo, o Office 365.

Pago Azure recursos AD estendem a amplitude do diretório. Alguns exemplos incluem:
- Grupo atribuição baseada em aplicativos, que é ativada sob o aplicativo específico que você está gerenciando.
- Avançada e recursos de gerenciamento de grupo de autoatendimento estão disponíveis em configuração de diretório ou dentro do grupo específico.
- Relatórios de segurança Premium estão na guia relatórios
- Detecção de aplicativos de nuvem aparece no portal do Azure em identidade.

###<a name="assigning-licenses"></a>Atribuição de licenças
Enquanto obter uma assinatura é tudo o que você precisa configurar recursos pagos, usando o seu Azure AD pago recursos requer distribuir licenças para os indivíduos certos. Em geral, qualquer usuário que devem ter acesso a ou quem é gerenciada por meio de um anúncio Azure pago recurso deve ser atribuído uma licença. Uma atribuição de licença é um mapeamento entre um usuário e um serviço comprado, como Azure AD Premium, Basic ou pacote de mobilidade do Enterprise.

Gerenciar quais usuários no seu diretório devem ter uma licença é simple. Ele pode ser feito, atribuindo a um grupo para criar regras de atribuição por meio do portal de administração do Azure AD ou atribuição de licenças diretamente para os indivíduos certos por meio de um portal, PowerShell ou APIs. Ao atribuir licenças a um grupo, todos os membros do grupo receberão uma licença. Se os usuários são adicionados ou removidos do grupo que eles serão atribuídos ou removidos a licença apropriada. Atribuição de grupo pode utilizar qualquer gerenciamento de grupo disponível para você e é consistente com atribuição baseada em grupo para aplicativos. Usando essa abordagem, você pode configurar regras de forma que todos os usuários no seu diretório recebem automaticamente, certifique-se de que todos com o cargo apropriado tem uma licença ou até mesmo delegar a decisão de outros gerentes na organização.

Com a atribuição de licença baseada em grupo, qualquer usuário faltando um local de uso herdarão o local do diretório durante a atribuição. Esse local pode ser alterado pelo administrador a qualquer momento. Em casos em que a atribuição automatizada falhou devido a um erro, as informações do usuário em que tipo de licença refletirão estado.

##<a name="getting-started-with-azure-ad-licensing"></a>Guia de Introdução com o licenciamento do Azure AD

Introdução ao Azure AD é fácil; Você sempre pode criar seu diretório como parte da inscrição para uma avaliação de Azure livre. [Saiba mais sobre a inscrição como uma organização](sign-up-organization.md). O seguinte pode ajudar a garantir que seu diretório melhor está alinhado com outros serviços da Microsoft que pode ser consumindo ou estiver planejando consumir e suas metas em Obtendo o serviço.

Veja algumas das práticas recomendadas:
- Se você já estiver usando qualquer um dos serviços organizacionais da Microsoft, você já tem um diretório Azure AD. Nesse caso, você deve continuar a usar o mesmo diretório para outros serviços, para que o gerenciamento de identidades core, incluindo provisionamento e híbridos, SSO que pode ser utilizado em serviços. Os usuários terão uma experiência de logon único e irá beneficiar sofisticados recursos entre os serviços. Como resultado, se você decidir comprar um anúncio Azure pago serviço para sua força de trabalho, é recomendável que você use o mesmo diretório para fazer isso.
- Se você estiver planejando usar Azure AD para um conjunto diferente de usuários (parceiros, clientes e assim por diante) ou se você gostaria de avaliar Azure AD serviços e gostaria de fazer isso em isolamento de seu serviço de produção, ou se você está procurando para configurar um ambiente de área restrita para seus serviços, recomendamos que você primeiro criar um novo diretório através do portal de clássico do Azure Azure. [Saber mais sobre como criar um novo diretório Azure AD no portal de clássico do Azure](active-directory-licensing-directory-independence.md). A nova pasta será criada com a sua conta como um usuário externo com permissões de administrador global. Quando você entrar no portal do Azure clássico com esta conta, você poderá ver este diretório e acessar todas as tarefas de administração de diretório. Recomendamos que você crie uma conta local com privilégios adequados para gerenciar outros serviços da Microsoft (aquelas não pode ser acessado por meio do portal de clássico Azure). [Saiba mais sobre como criar contas de usuário no Azure AD](active-directory-create-users.md).

> [AZURE.NOTE] Azure AD suporta "usuários externos", que são contas de usuário em uma instância do Azure AD que foram criados usando uma conta da Microsoft (MSA) ou uma identidade do Azure AD de outro diretório. Embora não possamos ocupados estender esse recurso em todos os serviços organizacionais da Microsoft, agora essas contas não são compatíveis com algumas das experiências dos serviços; Por exemplo, o portal de administração do Office 365 não suportamos esses usuários. Como resultado, os usuários externos com contas da Microsoft não poderão acessar o portal de administração do Office 365, enquanto os usuários externos de outros diretórios Azure AD serão ignorados. Nesse caso, apenas a conta do usuário local, o Azure AD ou diretório do Office 365 onde o usuário foi originalmente criado, seria acessível através essas experiências.

Conforme indicado, o Azure AD tem diferentes versões pagas. Estas versões têm algumas diferenças mínimas na sua disponibilidade de compra:


| Produto   | EA/PROGRAMA     | Abrir  |   CSP |   Direitos de uso MPN  |   Compra direta | Versão de avaliação |
|---|---|---|---|---|---|---|
| Pacote de mobilidade do Enterprise |   X | X | X | X |  |      X |
| Azure AD Premium  | X | X | X |   | X | X |
| Azure AD Basic    | X | X | X | X |  <br /> |  <br />  |

###<a name="select-one-or-more-license-trials"></a>Selecione uma ou mais tentativas de licença
 Em todos os casos, você pode ativar uma assinatura de avaliação do Azure AD Premium ou Enterprise mobilidade Suite selecionando a versão de avaliação específico que você deseja na guia licenças no diretório. Qualquer versão de avaliação contém uma assinatura de 30 dias com 100 licenças.

![Planos de licença de avaliação do Azure Active Directory](./media/active-directory-licensing-what-is/trial_plans.png)

![Planos de licença de avaliação do pacote de mobilidade do Enterprise](./media/active-directory-licensing-what-is/EMS_trial_plan.png)

![Planos de licença de avaliação ativa](./media/active-directory-licensing-what-is/active_license_trials.png)

###<a name="assign-licenses"></a>Atribuir licenças
Depois que a assinatura estiver ativa, você deve atribuir uma licença para si mesmo e atualize o navegador para garantir que você está vendo todos os seus recursos. A próxima etapa é atribuir licenças a usuários que precisam acessar ou ser incluído no pago recursos do Azure AD. Como mencionado acima em "Atribuindo licenças", a melhor maneira de fazer isso é identificar o grupo que representa o público desejado e atribuí-la para a licença; dessa maneira, os usuários que são adicionados ou removidos do grupo ao longo do ciclo de vida serão atribuídos a ou removidos de licença.

Para atribuir uma licença a um grupo ou usuários individuais, selecione o plano de licença que você gostaria de atribuir e clique em **atribuir** na barra de comando.

![Planos de licença de avaliação ativa](./media/active-directory-licensing-what-is/assign_licenses.png)

Uma vez na caixa de diálogo atribuição para o plano de selecionada, você pode selecionar usuários e adicioná-los à coluna **atribuir** à direita. Você pode percorrer a lista de usuários ou procurar indivíduos específicos usando o procurando vidro no canto superior direito da grade de usuário. Para atribuir grupos, selecione "Grupos" no menu **Exibir** e clique no botão de seleção à direita para atualizar as atribuições que são exibidas.

![Atribuição de licenças aos grupos](./media/active-directory-licensing-what-is/assign_licenses_to_groups.png)

Agora você pode pesquisar ou navegar por grupos e adicioná-los à coluna **atribuir** da mesma maneira. Você pode usar esses para atribuir uma combinação de usuários e grupos em uma única operação. Para concluir o processo de atribuição, clique no botão de seleção no canto inferior direito da página.

![Mensagem de progresso de atribuição de licença](./media/active-directory-licensing-what-is/license_assignment_progress_message.png)

Quando um grupo é atribuído, seus membros herdam as licenças em 30 minutos, mas geralmente dentro de 1-2 minutos.

Erros de atribuição podem ocorrer durante a atribuição de licença do Azure AD, mas são relativamente raros. Possíveis erros de atribuição estão limitados a:
- Conflito de atribuição - quando um usuário já foi atribuído a uma licença que é incompatível com a licença atual. Nesse caso, atribuindo a nova licença exigirão removendo anterior.
- Licenças disponíveis excedidas - quando o número de usuários em grupos atribuídos exceder as licenças disponíveis, o status da atribuição dos usuários refletirão uma falha ao atribuir devido a falta de licenças.

###<a name="view-assigned-licenses"></a>Exibir atribuída as licenças

Uma exibição de resumo de licenças atribuídas, incluindo eventos de ciclo de vida de assinatura disponível, atribuído e próximo são exibidos na guia **licenças** .

![Exibir o número de licenças atribuídas](./media/active-directory-licensing-what-is/view_assigned_licenses.png)

Uma lista detalhada de atribuído usuários e grupos, incluindo o status da atribuição e o caminho (direto ou herdado de um ou mais grupos) está disponível quando você navegar em um plano de licença.

![Exibição de detalhes de licenças atribuídas para um plano de licença](./media/active-directory-licensing-what-is/assigned_licenses_detail.png)

Removendo licenças é tão fácil quanto atribuí-las. Se o usuário está atribuído diretamente ou para um grupo atribuído, você pode remover a licença selecionando o tipo de licença, selecionando **Remover**, adicionando o usuário ou grupo à lista de remover e confirmar a ação. Como alternativa, você pode abrir um tipo de licença, selecione o usuário ou grupo específico e toque em **Remover** na barra de comando. Para finalizar a herança de um usuário de uma licença de um grupo, basta remova o usuário do grupo.

###<a name="extending-trials"></a>Estendendo tentativas

Extensões de avaliação para clientes que estão disponíveis como autoatendimento por meio do portal do Office 365. Um administrador do cliente pode navegar para o [portal do Office](https://portal.office.com/#Billing) (acesso depende permissões para o portal do Office) e selecione sua versão de avaliação do Azure AD Premium. Clique no link **Estender avaliação** e siga as instruções. Você precisará inserir um cartão de crédito, mas ela não será cobrada.

![Estendendo uma avaliação de licença no portal do Office](./media/active-directory-licensing-what-is/extend_license_trial.png)

Os clientes também podem solicitar uma extensão de avaliação enviando uma solicitação de suporte. Um administrador do cliente pode navegar para o portal do Office 365 [página de suporte](http://aka.ms/extendAADtrial) (acesso depende permissões para a página de suporte do Office). Nesta página, selecione "Assinaturas e tentativas" em recursos e "Perguntas de avaliação" em sintoma. Por fim, insira informações sobre as circunstâncias

![Estendendo uma avaliação de licença usando uma solicitação de suporte](./media/active-directory-licensing-what-is/alternate_office_aad_trial_extension.png)

## <a name="next-steps"></a>Próximas etapas

Agora você pode ser pronto para configurar e usar alguns recursos Premium do Azure AD.

- [Redefinição de senha de autoatendimento](active-directory-manage-passwords.md)
- [Gerenciamento de grupo de autoatendimento](active-directory-accessmanagement-self-service-group-management.md)
- [Integridade do Azure AD Connect](active-directory-aadconnect-health.md)
- [Atribuição de grupo para aplicativos](active-directory-manage-groups.md)
- [Azure autenticação multifator](../multi-factor-authentication/multi-factor-authentication.md)
- [Compra direta de licenças do Azure AD Premium](http://aka.ms/buyaadp)
