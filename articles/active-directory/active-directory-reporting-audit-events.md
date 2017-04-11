<properties
   pageTitle="Eventos de relatório de auditoria do Azure Active Directory | Microsoft Azure"
   description="Eventos de auditoria que estão disponíveis para exibição e baixando a partir de seu Active Directory do Azure"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/19/2016"
   ms.author="dhanyahk"/>

# <a name="azure-active-directory-audit-report-events"></a>Eventos de relatório de auditoria do Azure Active Directory

*Esta documentação é parte do [Azure Active Directory Reporting guia] (active-directory-reporting-guide.md).*

O relatório de auditoria do Azure Active Directory ajuda os clientes a identificar ações privilegiadas que ocorreram em seus Azure Active Directory. Ações privilegiadas incluem alterações de elevação (por exemplo, criação de função ou redefinições de senha), alterar configurações de política (por exemplo diretivas de senha) ou alterações na configuração de diretório (por exemplo, alterações às configurações de federação de domínio). Os relatórios fornecem o registro de auditoria para o nome do evento, o ator que executou a ação, o recurso de destino afetado por alterar e a data e hora (no UTC). Os clientes são capazes de recuperar a lista de eventos de auditoria para seus Azure Active Directory através de do [Portal do Azure](https://portal.azure.com/), conforme descrito no [modo de exibição os Logs de auditoria](active-directory-reporting-azure-portal.md).


## <a name="list-of-audit-report-events"></a>Lista de eventos de relatório de auditoria
<!--- audit event descriptions should be in the past tense --->

Eventos                               | Descrição do evento
------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**Eventos de usuário**                      |
Adicionar usuário                             | Adicionado a um usuário ao diretório.
Excluir usuário                          | Excluir um usuário do diretório.
Definir propriedades de licença               | Defina as propriedades de licença para um usuário no diretório.
Redefinir a senha do usuário                  | Redefina a senha de um usuário no diretório.
Alterar senha do usuário                 | Alterado a senha de um usuário no diretório.
Licença de usuário de alteração                  | Alterado a licença atribuída a um usuário no diretório. Para ver quais licenças foram atualizadas, examine as propriedades do [usuário de atualização](#update-user-attributes) abaixo
Usuário de atualização                          | Atualizar um usuário no diretório. [Veja abaixo](#update-user-attributes) para atributos que podem ser atualizados.
Definir forçar alterar senha do usuário       | Defina a propriedade que força um usuário altere sua senha no login.
Atualizar credenciais de usuário        | Usuário alterado a senha  
**Eventos de grupo**                     |
Adicionar grupo                            | Criar um grupo no diretório.
Grupo de atualização                         | Atualizar um grupo no diretório. Para ver quais propriedades de grupo foram atualizadas, consulte [Auditada de propriedades do grupo](#update-group-attributes) na seção abaixo
Excluir grupo                         | Excluir um grupo do diretório.
Adicionar membro ao grupo            | Adicionado um membro a um grupo no diretório.
Remover membro do grupo         | Remover um membro de um grupo no diretório.
CreateGroupSettings              | Configurações de grupo criado
UpdateGroupSettings          | Configurações de grupo atualizados. Para ver quais configurações de grupo foram atualizadas, consulte [Auditada de propriedades do grupo](#update-group-attributes) na seção abaixo
DeleteGroupSettings            | Configurações de grupo excluído
SetGroupLicense                  | Definir a licença do grupo.
SetGroupManagedBy              | Definir o grupo a ser gerenciado pelo usuário
AddGroupMember               | Membro adicionado ao grupo
RemoveGroupMember            | Remover membro do grupo
AddGroupOwner                | Proprietário adicionado ao grupo
RemoveGroupOwner                 | Removido proprietário do grupo
**Eventos de aplicativo**               |
Adicionar principal do serviço                | Adicionado um principal de serviço para o diretório.
Remover principal de serviço             | Removido uma entidade de segurança do serviço de diretório.
Adicionar credenciais principal do serviço    | Credenciais adicionadas a um principal de serviço.
Remover credenciais principal do serviço | Credenciais removidas de um serviço principal.
Adicionar entrada de delegação                 | Criado um [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) no diretório.
Entrada de delegação de conjunto                 | Atualizado um [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) no diretório.
Remover entrada de delegação              | Excluir um [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) no diretório.
**Eventos de função**                      |
Adicionar membro de função à função              | Adicionado a um usuário a uma função de diretório.
Remover membro da função de função         | Removido um usuário de uma função de diretório.
Definir informações de contato da empresa      | Definir preferências de contato de nível de empresa. Isso inclui endereços de email para notificações de marketing, bem como técnicas sobre o Microsoft Online Services.
Adicionar entrada de delegação                 | Criado um [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) no diretório.
Entrada de delegação de conjunto                 | Atualizado um [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) no diretório.
Remover entrada de delegação              | Excluir um [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) no diretório.
AddSevicePrincipalOwner          | Proprietário adicionado ao serviço principal.
RemoveSevicePrincipalOwner   | Removido o proprietário do serviço principal.
AddApplication  | Adicione o aplicativo.
UpdateApplication | Atualize o aplicativo. Para ver quais configurações de aplicativo foram atualizadas, consulte [Auditada de propriedades do aplicativo](#update-application-attributes) na seção abaixo
DeleteApplication | Exclua aplicativo.
RestoreApplication|Restaure o aplicativo.
AddApplicationOwner   |     Adicione proprietário ao aplicativo.
RemoveApplicationOwner    |     Remova o proprietário do aplicativo.
**Eventos de função**                         |
Adicionar membro de função à função                 | Adicionado a um usuário a uma função de diretório.
Remover membro da função de função            | Removido um usuário de uma função de diretório.
AddRoleDefinition               | Definição de função adicionada.
UpdateRoleDefinition                | Definição de função atualizado. Para ver quais configurações da função foram atualizadas, consulte a [Função definição propriedades auditados](#update-role-definition-attributes) na seção abaixo
DeleteRoleDefinition                | Definição de função excluída.
AddRoleAssignmentToRoleDefinition | Atribuição de função adicionado a definição de função.
RemoveRoleAssignmentFromRoleDefinition | Atribuição de função removidos da definição de função.
AddRoleFromTemplate     | Função adicionada do modelo.
UpdateRole  | Função atualizada.
AddRoleScopeMemberToRole    | Membro supervisionado adicionado à função.
RemoveRoleScopedMemberFromRole  | Removido membro de escopo de função.
**Eventos de dispositivo (todos os novos eventos)**                    |
Em AddDevice               | Dispositivo adicionado.
UpdateDevice            | Dispositivo atualizado. Para ver quais propriedades de dispositivo foram atualizadas, consulte [Propriedades de dispositivo auditadas](#update-device-attributes) na seção abaixo
DeleteDevice            | Dispositivo excluído.
AddDeviceConfiguration      | Configuração de dispositivo adicionado.
UpdateDeviceConfiguration   | Configuração de dispositivo atualizados. Para ver quais propriedades de configuração de dispositivo foram atualizadas, consulte [Propriedades de configuração de dispositivo auditadas](#update-device-configuration-attributes) na seção abaixo
DeleteDeviceConfiguration   | Configuração de dispositivo excluído.
AddRegisteredOwner     | Proprietário registrado adicionado ao dispositivo.
AddRegisteredUsers     | Usuários registrados adicionados ao dispositivo.
RemoveRegisteredOwner    | Remova proprietário registrado do dispositivo.
RemoveRegisteredUsers    | Remova usuários registrados do dispositivo.
RemoveDeviceCredentials  | Remova credenciais do dispositivo.
**Eventos de B2B**                       |
Convites de lote carregados.              | Um administrador tem carregados um arquivo que contém os convites sejam enviadas aos usuários de parceiro.
Convites de lote processadas.             | Um arquivo contendo convites para usuários do parceiro foi processado.
Convide usuários externos.                | Um usuário externo foi convidado ao diretório.
Resgate convidar usuários externos.         | Um usuário externo tem trocados seu convite para o diretório.
Adicione usuário externo ao grupo.          | Um usuário externo foi atribuído associação a um grupo no diretório.
Atribua usuários externos ao aplicativo. | Um usuário externo recebeu acesso direto para um aplicativo.
Criação de locatário vírus.               | Um novo locatário foi criado no Azure AD por resgate o convite.
Criação de usuário vírus.                 | Um usuário tiver sido criado em um locatário existente no Azure AD por resgate o convite.
**Unidades administrativas (todos os novos eventos)**             |
AddAdministrativeUnit   |   Adicione unidade administrativa.
UpdateAdministrativeUnit|   Atualize unidade administrativa. Para ver quais propriedades de unidade administrativa foram atualizadas, consulte [Propriedades de unidade administrativas auditados](#update-administrative-unit-attributes) na seção abaixo
DeleteAdministrativeUnit|   Exclua unidade administrativa.
AddMemberToAdministrativeUnit|  Adicione membro a unidade administrativa.
RemoveMemberFromAdministrativeUnit|     Remova membro de unidade administrativa.
**Eventos de diretório**                 |
Adicionar parceiro a empresa               | Adicionar um parceiro para o diretório.
Remover o parceiro da empresa          | Removido um parceiro do diretório.
DemotePartner   |   Rebaixe o parceiro.
Adicionar domínio para a empresa                | Adicionou um domínio ao diretório.
Remover o domínio da empresa           | Um domínio foi removido do diretório.
Domínio de atualização                        | Atualizado um domínio no diretório. Para ver quais propriedades de domínio foram atualizadas, consulte [Domain Properties auditados](#update-domain-attributes) na seção abaixo
Configurar autenticação de domínio            | Alterar a configuração de domínio padrão para a empresa.
Definir informações de contato da empresa      | Definir preferências de contato de nível de empresa. Isso inclui endereços de email para notificações de marketing, bem como técnicas sobre o Microsoft Online Services.
Definir configurações de Federação em domínio    | Atualizadas as configurações de federação para um domínio.
Verificar o domínio                        | Verificar um domínio no diretório.
Verificar o domínio verificado do email         | Verificar um domínio no diretório usando a verificação de email.
Definir o sinalizador de DirSyncEnabled na empresa   | Defina a propriedade que permite que uma pasta de sincronização do Azure AD.
Definir a política de senha                  | Definir restrições de comprimento e caractere para senhas de usuário.
Informações da empresa do conjunto              | Atualizou as informações de nível de empresa. Consulte o cmdlet [Get-MsolCompanyInformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) do PowerShell para obter mais detalhes.
SetCompanyAllowedDataLocation  |    Definir empresa permitida local de dados.
SetCompanyDirSyncEnabled    |   Defina sinalizador de DirSyncEnabled.
SetCompanyDirSyncFeature |  Defina o recurso de DirSync.
SetCompanyInformation |   Informações da empresa do conjunto.
SetCompanyMultiNationalEnabled    |     Definir empresa recurso multinacionais habilitado.
SetDirectoryFeatureOnTenant   |     Definir o recurso de diretório principal.
SetTenantLicenseProperties  |   Definir propriedades de licença do locatário.
CreateCompanySettings   |   Criar configurações de empresa
UpdateCompanySettings   |   Atualize configurações de empresa. Para ver quais propriedades de empresa foram atualizadas, consulte [Propriedades de empresa auditados](#update-company-attributes) na seção abaixo
DeleteCompanySettings   |   Excluir configurações da empresa
SetAccidentalDeletionThreshold   |  Defina o limite de exclusões acidentais.
SetRightsManagementProperties   |   Definir propriedades de gerenciamento de direitos.
PurgeRightsManagementProperties     |   Limpe propriedades de gerenciamento de direitos.
UpdateExternalSecrets   |   Atualizar segredos externos
**Eventos de política (todos os novos eventos)**           |
AddPolicy   |   Adicione política.
UpdatePolicy    |   Política de atualização.
DeletePolicy    |   Exclua política.
AddDefaultPolicyApplication     |   Adicione política ao aplicativo.
AddDefaultPolicyServicePrincipal    |   Adicione política ao serviço principal.
RemoveDefaultPolicyApplication  |   Remova política de aplicativo.
RemoveDefaultPolicyServicePrincipal     |   Remova política de capital de serviço.
RemovePolicyCredentials     |   Remova credenciais de política.

## <a name="audit-report-retention"></a>Retenção de relatório de auditoria
Eventos no relatório de auditoria do Azure AD são mantidos por 180 dias. Para obter mais informações sobre a retenção em relatórios, consulte [Políticas de retenção de relatório do Active Directory do Azure](active-directory-reporting-retention.md).

Para clientes interessados em armazenar seus eventos de auditoria por longos períodos de retenção, a API de relatório pode ser usada para regularmente retirar eventos de auditoria em um armazenamento de dados separado. Consulte o [Guia de Introdução com a API de relatórios](active-directory-reporting-api-getting-started.md) para obter detalhes.

## <a name="properties-included-with-each-audit-event"></a>Propriedades incluídas com cada evento de auditoria

Propriedade      | Descrição
------------- | --------------------------------------------------------------
Data e hora | A data e a hora em que ocorreu o evento de auditoria
Ator         | O usuário ou entidade de serviço que executou a ação
Ação        | A ação que foi executada
Destino        | O usuário ou entidade de serviço que a ação foi executada em


## <a name="update-user-attributes"></a>Atributos "Atualizar usuário"
O evento de auditoria "Usuário de atualização" inclui informações adicionais sobre quais atributos de usuário foram atualizados. Para cada atributo, o valor anterior e o novo valor está incluído.

Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled                      | O usuário pode entrar.
AssignedLicense                     | Todas as licenças que foram atribuídas ao usuário.
AssignedPlan                        | Planos de serviço resultante das licenças atribuídas ao usuário.
LicenseAssignmentDetail             | Detalhes sobre licenças atribuídas ao usuário. Por exemplo, se o licenciamento baseado em grupo foi envolvido, isso inclui o grupo que a concessão de licença.
Celular                              | Celular do usuário.
OtherMail                           | Endereço de email alternativo do usuário.
OtherMobile                         | Alternativo celular do usuário.
StrongAuthenticationMethod          | Uma lista de métodos de verificação definidas pelo usuário para a autenticação multifator, como o código de chamada de voz, SMS ou verificação de um aplicativo móvel.
StrongAuthenticationRequirement     | Se for imposta a autenticação multifator, habilitado ou desabilitado para esse usuário.
StrongAuthenticationUserDetails     | Número de telefone do usuário, alternativo de telefone e endereço de email usado para verificação de redefinição de autenticação multifator e senha.
StrongAuthenticationPhoneAppDetail  | Aplicativos do telefone de forof detalhes registrados para executar 2FA login
TelephoneNumber                     | Número de telefone do usuário.
AlternativeSecurityId               | Uma identificação de segurança alternativo para o objeto.
CreationType                        |Método de criação do usuário (ou por convite ou vírus).
InviteTicket                        |Lista de tíquetes de convite para o usuário.
InviteReplyUrl                      |Lista de urls para responder mediante a aceitação do convite.
InviteResources                     |Lista de recursos aos quais o usuário foi convidado.
LastDirSyncTime                     |Última vez em que o objeto foi atualizado devido a sincronização do autoritativas diretório (cliente, no local).
MSExchRemoteRecipientType           |Mapas para tipos de destinatário MSO. Consulte https://msdn.microsoft.com/library/microsoft.office.interop.outlook.recipient.type.aspx [tipos de destinatário MSO] para os tipos de destinatários
PreferredDataLocation               |O local preferido do usuário, do grupo, do contato, pasta pública ou dados do dispositivo.
ProxyAddresses                      |O endereço pelo qual um objeto de destinatário do Exchange Server é reconhecido em um sistema de email externos.
StsRefreshTokensValidFrom           |Realiza atualizar informações de revogação token - qualquer tokens de atualização de STS emitidos antes desta vez são considerados expirou.
UserPrincipalName                   |O UPN que é um nome de logon de estilo de Internet para um usuário.
UserState                           |Status do usuário PendingApproval/PendingAcceptance/aceitas/PendingVerification.
UserStateChangedOn                  |Carimbo de hora da última alteração UserState. Usado para disparar fluxos de trabalho do ciclo de vida.
UserType                            |Tipo de usuário. Membro (0), convidado (1), Viral(2).

## <a name="update-group-attributes"></a>Atributos de "Grupo de atualizar"
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Classificação            | A classificação de um grupo de Unificação (AIN, IMN etc.).
Descrição               | Legível frases descritivas sobre o objeto.  
DisplayName               |O nome de exibição para um objeto
DirSyncEnabled            |Indica se a sincronização ocorre de um autoritativas diretório (cliente, no local).
GroupLicenseAssignment    |Atribuição de licença de um grupo
GroupType                 |Tipo de grupo, unificado (0)
IsMembershipRuleLocked    |Indica que a propriedade MembershipRule é definida pelo serviço de gerenciamento de grupo de autoatendimento e não pode ser alterada pelos usuários. Aplicável somente aos grupos onde GroupType inclui GroupType.DynamicMembership
IsPublic                  |Sinalizador para indicar se o grupo for público/particular.
LastDirSyncTime           |Última vez em que o objeto foi atualizado como resultado de sincronização do autoritativas diretório (cliente, local).
Mail                      |Endereço de email principal
MailEnabled               |Indica se este grupo tem capacidade de email.
MailNickname              |Identificador de origem de um objeto de catálogo de endereços, normalmente a parte de seu nome de email anterior a "@" símbolo.   
MembershipRule            |Uma cadeia de caracteres expressar os critérios usados pelo serviço de gerenciamento de grupo de autoatendimento para determinar quais membros devem pertencer a este grupo. Consulte também IsMembershipRuleLocked. Se aplica somente aos grupos onde GroupType inclui GroupType.DynamicMembership.
MembershipRuleProcessingState| Um valor de enumeração definido pelo serviço de gerenciamento de grupo de autoatendimento definindo o status da associação de processamento para esse grupo. Se aplica somente aos grupos onde GroupType inclui GroupType.DynamicMembership.
ProxyAddresses            |O endereço pelo qual um objeto de destinatário do Exchange Server é reconhecido em um sistema de email externos.
RenewedDateTime           |Registro de carimbo de hora de quando um grupo mais recentemente foi renovado.   
SecurityEnabled           |Indica se o membro do grupo pode influenciar decisões de autorização.
WellKnownObject           |Etiquetas de um objeto de diretório, designando-o como um de um conjunto predefinido.

## <a name="update-device-attributes"></a>Atributos "Atualizar dispositivo"
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled | Indica se uma entidade de segurança pode autenticar.
CloudAccountEnabled | Indica se uma entidade de segurança pode autenticar. Escrito por InTune quando o dispositivo está mastered local.
CloudDeviceOSType | Tipo de dispositivo com base no sistema operacional por exemplo, Windows RT, iOS. Quando definido por um serviço de nuvem (como o Intune), esse atributo se torna autoritativo no diretório para DeviceOSType.
CloudDeviceOSVersion | Versão do sistema operacional. Quando definido por um serviço de nuvem (como o Intune), esse atributo se torna autoritativo no diretório para DeviceOSVersion.
CloudDisplayName | Valor do atributo displayName LDAP. Quando definido por um serviço de nuvem (como o Intune), esse atributo se torna autoritativo no diretório para displayName.
CloudCreated |Indica se o objeto foi criado pelos serviços de nuvem.
CompliantUntil | Até que horário dispositivo é considerado compatível.
DeviceMetadata |Metadados personalizados para o dispositivo
DeviceObjectVersion | Esse atributo é usado para identificar a versão de esquema do dispositivo.
DeviceOSType |Tipo de dispositivo com base no sistema operacional por exemplo, Windows RT, iOS. Gravados pelo serviço do registro e se destina a ser atualizados pelo MDM STS ou serviço de gerenciamento claro serviço de gerenciamento.
DeviceOSVersion |Versão do sistema operacional. Gravados pelo serviço do registro e se destina a ser atualizados pelo MDM STS ou serviço de gerenciamento claro serviço de gerenciamento.
DevicePhysicalIds |Atributo de valores múltiplos destinado para armazenar identificadores do dispositivo físico. Isso pode incluir BIOS IDs, impressões digitais TPM, hardware IDs específicas, etc.
DirSyncEnabled | Indica se a sincronização ocorre de um autoritativo (cliente, local) diretório.
DisplayName |O nome de exibição para um objeto
IsCompliant | Esse atributo é usado para gerenciar o status de gerenciamento de dispositivo móvel do dispositivo.
IsManaged |Esse atributo é usado para indicar que o dispositivo é gerenciado por uma nuvem MDM.
LastDirSyncTime |Última vez em que o objeto foi atualizado devido a sincronização do autoritativas diretório (cliente, local).

## <a name="update-device-configuration-attributes"></a>"Atualizar a configuração de dispositivo" atributos
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
MaximumRegistrationInactivityPeriod |O número máximo de dias que um dispositivo pode ficar inativo antes que ele é considerado para remoção.
RegistrationQuota   |Política usada para limitar o número de registros de dispositivo permitidos para um único usuário.

## <a name="update-service-principal-configuration-attributes"></a>"Atualizar a configuração principal do serviço" atributos
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled |Indica se uma entidade de segurança pode autenticar.
AppPrincipalId | Identidade externa, definidos pelo aplicativo para uma entidade de segurança.
DisplayName |O nome de exibição para um objeto
ServicePrincipalName    | Um nome principal de serviço, que contêm "nome/autoridade" onde nome Especifica um valor de classe do aplicativo e autoridade contém pelo menos hostname [: porta] ou "nome" que especifica um identificador para a entidade de serviço.

## <a name="update-app-attributes"></a>Atributos "Atualizar aplicativo"
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |O conjunto de endereços (redirecionar URLs) que estejam atribuídos a uma entidade de segurança do serviço.
AppId                               |ID de aplicativo do aplicativo   
AppIdentifierUri | URI aplicativo, que identifica o aplicativo.  Geralmente é a URL de acesso do aplicativo.
AppLogoUrl |A url da imagem de logotipo do aplicativo armazenados em uma CDN.
AvailableToOtherTenants | True o aplicativo for vários locatários (ou seja, pode ser usado por outros locatários).
DisplayName | O nome de exibição para um nome de aplicativo
Qualificação |Lista de direitos de aplicativo.
ExternalUserAccountDelegationsAllowed |Sinalizador que indica se o aplicativo de recurso é um confiável e pode criar entradas de delegação para contas de usuário externo.
GroupMembershipClaims |A política de declarações de associação de grupo.
PublicClient | True se o cliente não é possível manter secreto (ou seja, não confidenciais client no OAuth2.0)
RecordConsentConditions | Tipos de condições de consentimento, conforme definido pelo termos do contrato: nenhum (0), SilentConsentForPartnerManagedApp(1). Esse valor será exposto no esquema de API do gráfico e só pode ser definidas/alteradas por administradores de locatários.
RequiredResourceAccess |Conteúdo XML de um valor da propriedade RequiredResourceAccess.
Web App |Se for verdadeiro, indica que esse aplicativo é um aplicativo web.
WwwHomepage |Página da Web primária.

## <a name="update-role-attributes"></a>"Atualizar a função" atributos
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |O conjunto de endereços (redirecionar URLs) que estejam atribuídos a uma entidade de segurança do serviço.
BelongsToFirstLoginObjectSet |Se for verdadeiro, indica que esse objeto pertence ao conjunto de objetos necessários para habilitar o logon do administrador do primeiro de um novo locatário.
Interno |Indica se a vida de um objeto pertence pelo sistema.
Descrição | Legível frases descritivas sobre o objeto.  
DisplayName |O nome de exibição para um objeto
MailNickname | Identificador de origem de um objeto de catálogo de endereços, normalmente a parte de seu nome de email anterior a "@" símbolo.
RoleDisabled |Indica se a função deve ser ignorada para fins de verificações de acesso.
RoleTemplateId | Identidade do modelo de função.
ServiceInfo |Específico do serviço informações de provisionamento que podem ser consumidas por MOAC e/ou outras instâncias de serviço (dos tipos de serviço iguais ou diferentes).
TaskSetScopeReference |Identifica um TaskSet e um conjunto de escopos associados a uma função ou RoleTemplate.
ValidationError |Informações publicadas por um serviço federado descrevendo um erro não transitório, específico do serviço sobre as propriedades ou o link de uma ação de administrador do objeto para resolver.
WellKnownObject |Etiquetas de um objeto de diretório, designando-o como um de um conjunto predefinido.

## <a name="update-role-definition-attributes"></a>"Atualizar a definição de função" atributos
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AssignableScopes    |Coleção de escopos de autorização que podem ser referenciados ao atribuir esta RoleDefinition a uma entidade de segurança.
DisplayName     |O nome de exibição para um objeto
GrantedPermissions  |Permissões concedidas por esta RoleDefinition.


## <a name="update-administrative-unit-attributes"></a>"Atualizar a unidade administrativa" atributos
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Descrição |   Essa propriedade é atualizada quando você alterar a descrição de uma unidade administrativa.
DisplayName |   Esta propriedade é atualizada quando você alterar o nome de uma unidade administrativa.

## <a name="update-company-attributes"></a>Atributos de "Atualização da empresa"
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AllowedDataLocation     | Um local no qual os usuários da empresa poderão ser provisionado.
AuthorizedServiceInstance   | Nomes de instâncias do serviço para o qual um plano pode ser implantado.
DirSyncEnabled |Indica se a sincronização ocorre de um autoritativo (cliente, local) diretório.
DirSyncStatus |Indica se a sincronização de objetos de catálogo de endereços neste contexto de locatário ocorre de um autoritativo (cliente, local) diretório; uma expansão da propriedade DirSyncEnabled em objetos de empresa.
DirSyncFeatures |Sinalizador de bit para controlar conjunto de recursos de dirsync habilitado e desabilitado para o site principal.
DirectoryFeatures |Recursos de diretório ativado/desativado.
DirSyncConfiguration |Contém todas as configurações de DirSync específicos para o locatário atual.
DisplayName |O nome de exibição para um objeto
IsMnc |Um sinalizador booliano definido como "true" iff que a empresa está habilitada para o recurso de empresa multinacionais.
ObjectSettings | Uma coleção de configurações aplicáveis para o escopo do objeto.
PartnerCommerceUrl |URL para o site de comércio do parceiro.
PartnerHelpUrl |URL para o site de Ajuda do parceiro.
PartnerSupportEmail | URL ao email de suporte do parceiro.
PartnerSupportTelephone |URL para o telefone de suporte do parceiro.
PartnerSupportUrl |URL do site de suporte do parceiro.
StrongAuthenticationDetails |Detalhes relacionados a StrongAuthentication.
StrongAuthenticationPolicy |Política de autenticação sólida para a empresa.
TechnicalNotificationMail |Endereço de email para notificar técnicos questões relacionadas a uma empresa.
TelephoneNumber |Números de telefone que cumprir a E.123 de recomendação ITU.
TenantType |O tipo de um locatário. Se esse valor não for especificado, o locatário é uma empresa. Caso contrário, os valores possíveis são: MicrosoftSupport (0), SyndicatePartner (1), BreadthPartner (2) BreadthPartnerDelegatedAdmin (3) ResellerPartnerDelegatedAdmin (4) ValueAddedResellerPartnerDelegatedAdmin (5).
VerifiedDomain |Um conjunto de nomes de domínio DNS associado a uma empresa.

## <a name="update-domain-attributes"></a>Atributos "Atualizar domínio"
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Recursos    |Bit sinalizadores que descreve os recursos do domínio, se houver.
Padrão |Indica se o domínio é o valor padrão; Por exemplo, o UserPrincipalName sufixo padrão quando um administrador cria um novo usuário em MOAC.
Inicial |Indica se o domínio é o domínio inicial para a empresa, como alocado pelo OCP. O domínio inicial é um subdomínio exclusivo de um domínio do Microsoft Online; e.g.contoso3.microsoftonline.com.
LiveType    |Tipo do Windows Live namespace correspondente, se houver.
Nome    | Identificador para o ponto de extremidade.
PasswordNotificationWindowDays  |O número de dias antes que uma senha expire o usuário será notificado.
PasswordValidityPeriodDays  | O número de dias que uma senha é bom para antes que ele deve ser alterado.

Registros de auditoria são um controle necessário para muitos regulamentos. Para clientes usando o Azure Active Directory relatório de auditoria para atender às suas regulamentos, é recomendável que o cliente enviar uma cópia deste tópico de ajuda com a cópia do relatório de auditoria exportado do cliente para ajudar a explicar os detalhes do relatório. Se o auditor gostaria de entender os regulamentos Azure atualmente atende, encaminhe o auditor para a [página de conformidade](https://azure.microsoft.com/support/trust-center/compliance/) do Microsoft Azure Trust Center.
