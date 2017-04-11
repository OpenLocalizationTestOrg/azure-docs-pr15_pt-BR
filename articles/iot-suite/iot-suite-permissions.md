<properties
  pageTitle="Pacote de IoT Azure e o Azure Active Directory | Microsoft Azure"
  description="Descreve como o Azure IoT Suite usa o Active Directory do Azure para gerenciar permissões."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="10/24/2016"
  ms.author="araguila"/>
  
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Permissões no site azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>O que acontece quando você entrar

Quando entrar pela primeira vez em [azureiotsuite.com][lnk-azureiotsuite], o site determina os níveis de permissão que você tem com base na assinatura do Azure e locatário do Azure Active Directory (AAD) atualmente selecionada.

1.  O site primeiro descobre do Azure quais locatários AAD você pertence para preencher a lista de locatários visto ao lado de seu nome de usuário conectado. No momento, o site só pode obter tokens de usuário para um locatário por vez. Como resultado, quando você alterna para um locatário diferentes usando a lista suspensa no canto superior direito, o site novamente conecta ao locatário para obter os tokens locatário.

2.  Em seguida, o site descobre do Azure quais assinaturas associado locatário selecionado. Você vê as assinaturas disponíveis quando você cria uma nova solução pré-configurado.

3.  Por fim, o site recupera todos os recursos nos grupos de recursos e assinaturas marcado como soluções pré-configurado e preenche os blocos na home page.

As seções a seguir descrevem as funções que controlam o acesso às soluções pré-configurado.

## <a name="aad-roles"></a>Funções AAD

As funções AAD controlam as soluções de provisionar pré-configurado de capacidade e gerenciar usuários em uma solução pré-configurada.

Você pode encontrar mais informações sobre funções de administrador AAD em [Atribuindo funções de administrador no Azure AD][lnk-aad-admin], mas este artigo aborda principalmente o **Administrador Global** e as funções de **Usuário/membro de domínio** como usado pelas soluções pré-configurado.

**Administrador global:** Pode haver muitos administradores globais por locação AAD. Quando você cria um locatário AAD, são por padrão o administrador global do locatário. O administrador global pode provisionar uma solução pré-configurada e é atribuído a uma função de **administrador** para o aplicativo dentro de seu locatário AAD. No entanto, se outro usuário no mesmo locatário AAD cria um aplicativo, a função padrão como o administrador global é concedido é **Somente leitura de implícito**. Os administradores globais podem atribuir funções para aplicativos usando o [portal de clássico Azure][lnk-classic-portal].

**Usuário/membro do domínio:** Pode haver muitos usuários/membros do domínio por locação AAD. Um usuário do domínio pode provisionar uma solução pré-configurada através do [azureiotsuite.com] [ lnk-azureiotsuite] site. A função padrão que eles são concedidos para o aplicativo que eles provisionar é **administrador**. Eles podem criar um aplicativo usando o script build.cmd no [azure-iot-monitoramento remoto] [ lnk-rm-github-repo] ou [azure iot-previsão manutenção] [ lnk-pm-github-repo] repositório, mas a função padrão recebem é **Somente leitura implícita**, conforme eles não tem permissão para atribuir funções. Se outro usuário no locatário AAD cria um aplicativo, eles são atribuídos a função de **Somente leitura implícito** por padrão para esse aplicativo. Eles não têm a capacidade de atribuir funções para aplicativos; Portanto, não poderá adicionar usuários ou funções para os usuários para um aplicativo mesmo se eles provisionados-lo.

**Convidado da usuário convidado:** Pode haver muitos usuários de convidado/convidados por locação AAD. Usuários convidados tem um conjunto limitado de direitos no locatário AAD. Como resultado, os usuários convidados não podem provisionar uma solução pré-configurada no locatário AAD.

Para obter mais informações, consulte os seguintes recursos:

- [Criar ou editar usuários no Azure AD][lnk-create-edit-users]
- [Atribuir funções de aplicativo no AAD][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Funções de administrador de assinatura Azure

As funções de administrador Azure controlam a capacidade de mapear uma assinatura do Azure para um locatário do AD.

Você pode saber mais sobre as funções de administrador de colegas do Azure, o administrador do serviço e o administrador de conta no artigo [como adicionar ou alterar o administrador de colegas do Azure, o administrador do serviço e o administrador de conta][lnk-admin-roles].

## <a name="application-roles"></a>Funções de aplicativo

As funções do aplicativo controlam o acesso a dispositivos em sua solução pré-configurado.

Há duas definidos e uma função implícita definidos no aplicativo que é criado quando você provisionar uma solução pré-configurada.

-   **Administrador:** Tem controle total para adicionar, gerenciar e remova dispositivos

-   **Somente leitura:** Tem a capacidade de exibir dispositivos

-   **Implícito somente leitura:** Isso é o mesmo como somente leitura, mas é concedido a todos os usuários do seu locatário AAD. Isso foi feito para conveniência durante o desenvolvimento. Você pode remover esta função modificando o [RolePermissions.cs] [ lnk-resource-cs] arquivo de origem.

### <a name="changing-application-roles-for-a-user"></a>Alterar funções de aplicativo de um usuário

Você pode usar o procedimento a seguir para fazer um usuário no Active Directory um administrador de sua solução pré-configurado.

Você deve ser um administrador global AAD alterar funções para um usuário:

1. Acesse o [portal clássico Azure][lnk-classic-portal].

2. Selecione o **Active Directory**.

3. Clique no nome do seu locatário AAD (essa é a pasta que você escolheu na azureiotsuite.com quando você provisionado sua solução).

4. Clique em **aplicativos**.

5. Clique no nome do aplicativo que corresponde ao seu nome de solução pré-configurada. Se você não vir seu aplicativo na lista, alterne o menu suspenso **Mostrar** para baixo para **minha empresa possui de aplicativos** e clique na marca de seleção.

7. Clique em **usuários**.

8. Selecione o usuário que você deseja alternar funções.

9. Clique em **atribuir** e selecione a função (como **administrador**) que você deseja atribuir ao usuário, clique na marca de seleção.

## <a name="faq"></a>Perguntas Freqüentes

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-do-this"></a>Eu sou um administrador de serviço e eu gostaria de alterar o mapeamento de diretório entre minha assinatura e um locatário AAD específico. Como fazer isso?

1. Acesse o [portal clássico Azure][lnk-classic-portal], clique em **configurações** na lista de serviços no lado esquerdo.

2. Selecione a assinatura que você gostaria de alterar o mapeamento de diretório para.

3. Clique em **Editar diretório**.

4. Selecione o **diretório** que você gostaria de usar na lista suspensa. Clique na seta Avançar.

5. Confirme o mapeamento de diretório e afetados coadministradores. Observe que, se você estiver movendo de outro diretório, todos os administradores de colegas do diretório original são removidos.

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Sou um usuário/membro do domínio no locatário AAD e criei uma solução pré-configurada. Como para obter uma função atribuída para meu aplicativo?

Pergunte a um administrador global para atribuir a você como um administrador global no locatário AAD obter permissões para atribuir funções aos usuários ou peça ao administrador global para atribuir a você uma função. Se você quiser alterar o locatário AAD sua solução pré-configurada foi implantado para, consulte a próxima pergunta.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>Como troco locatário AAD a que minha solução pré-configurado monitoramento remoto e o aplicativo estão atribuídas?

Você pode executar uma implantação de nuvem do <https://github.com/Azure/azure-iot-remote-monitoring> e reimplantar com um locatário AAD recém-criado. Como você por um administrador global padrão quando você cria um novo locatário AAD, você terá acesso a adição de usuários e atribuir funções aos usuários.

1. Criar um novo diretório AAD no [portal de gerenciamento do Azure][lnk-classic-portal].

2. Vá para <https://github.com/Azure/azure-iot-remote-monitoring>.

3. Executar `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (por exemplo, `build.cmd cloud debug myRMSolution`)

4. Quando solicitado, defina o **tenantid** seja seu locatário recém-criado em vez de seu locatário anterior.


### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Quero alterar um administrador colegas quando conectado com uma conta organizacionais ou o administrador do serviço

Consulte o artigo de suporte [alterando administrador de serviço e colegas administrador quando conectado com uma conta organizacionais][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Por que estou vendo este erro? "Sua conta não tem as permissões apropriadas para criar uma solução. Verifique com seu administrador ou tente com uma conta diferente."

Dê uma olhada no diagrama abaixo:

![][img-flowchart]

> [AZURE.NOTE] Se você continuar a ver o erro após validar você estão um administrador global do locatário AAD e um administrador de colegas com assinaturas, administrador da sua conta remover o usuário e atribua novamente as permissões necessárias nesta ordem: adicionar o usuário como um administrador global e, em seguida, adicionar o usuário como um administrador de colegas na assinatura do Azure. Se problemas persistirem, entre em contato com [Ajuda & suporte][lnk-help-support].

**Por que estou vendo este erro quando há uma assinatura do Azure?** *Uma assinatura do Azure é necessária para criar soluções pré-configurado. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos.*

Se você tiver certeza de que você tiver uma assinatura do Azure, valide o locatário mapeamento para sua assinatura e verifique o locatário correto está selecionado na lista suspensa. Se você tiver validada locatário desejado está correto, siga o diagrama acima e validar o mapeamento de sua assinatura e este locatário AAD.

## <a name="next-steps"></a>Próximas etapas

Para continuar aprendendo mais IoT Suite, consulte como você pode [Personalizar uma solução pré-configurada][lnk-customize].

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-aad-admin]: https://azure.microsoft.com/documentation/articles/active-directory-assign-admin-roles/
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-create-edit-users]: https://azure.microsoft.com/documentation/articles/active-directory-create-users/
[lnk-assign-app-roles]: https://azure.microsoft.com/documentation/articles/active-directory-application-manifest/
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
