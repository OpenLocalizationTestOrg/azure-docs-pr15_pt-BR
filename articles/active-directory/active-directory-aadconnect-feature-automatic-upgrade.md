<properties
   pageTitle="Azure AD Connect: Atualização automática | Microsoft Azure"
   description="Este tópico descreve o recurso de atualização automática interno na sincronização do Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/24/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: Atualização automática
Este recurso foi introduzido com compilação 1.1.105.0 (lançada fevereiro de 2016).

## <a name="overview"></a>Visão geral
Certifique-se de que sua instalação do Azure AD Connect é sempre atualizada nunca foi mais fácil com o recurso de **atualização automática** . Esse recurso é habilitado por padrão em instalações express e DirSync atualizações. Quando for lançada uma nova versão, sua instalação é atualizada automaticamente.

Atualização automática está habilitada por padrão para o seguinte:

- Expresse configurações instalação e atualizações de DirSync.
- Usando o SQL Express LocalDB, que é o que as configurações de Express sempre usam. DirSync com o SQL Express também usar LocalDB.
- O AD é a conta MSOL_ padrão criada por DirSync e configurações de Express.
- Ter menos de 100.000 objetos no metaverso.

O estado atual da atualização automática pode ser exibido com o cmdlet do PowerShell `Get-ADSyncAutoUpgrade`. Ela tem os seguintes estados:

Estado | Comentário
---- | ----
Habilitado | Atualização automática está ativada.
Suspenso | Definido pelo sistema somente. O sistema já não está qualificado para receber atualizações automáticas.
Desativado | Atualização automática está desativada.

Você pode alterar entre **habilitado** e **desabilitado** com `Set-ADSyncAutoUpgrade`. Somente o sistema deve definir o estado **suspenso**.

Atualização automática está usando o Azure AD conectar integridade para a infraestrutura de atualização. Para atualização automática trabalhar, certificar-se de que você abriu as URLs no seu servidor proxy para **Azure AD conectar integridade** conforme documentadas nas [URLs do Office 365 e intervalos de endereços IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Se estiver executando o **Gerenciador de serviços de sincronização** da interface do usuário no servidor, a atualização será suspensa até que a interface do usuário é fechada.

## <a name="troubleshooting"></a>Solução de problemas
Se sua instalação conectar não atualizar próprio conforme o esperado, em seguida, siga estas etapas para descobrir o que poderia estar errado.

Primeiro, você não deve esperar a atualização automática para ser tentado o primeiro dia que é lançada uma nova versão. Não há uma aleatoriedade intencional antes de tentar uma atualização para que não se assuste se sua instalação não é atualizada imediatamente.

Se você acha que algo não está certo, primeiro execute `Get-ADSyncAutoUpgrade` para garantir a atualização automática está habilitada.

Em seguida, verifique se que você abriu os URLs necessários em seu proxy ou firewall. Atualização automática está usando o Azure AD conectar integridade, conforme descrito na [Visão geral](#overview). Se você usar um proxy, verifique se a que integridade tiver sido configurada para usar um [servidor proxy](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Também teste a [conectividade de integridade](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) Azure AD.

Com a conectividade com o Azure AD verificada, é hora de examinar os logs de eventos. Iniciar o Visualizador de eventos e examine o log de eventos do **aplicativo** . Adicione um filtro de log de eventos para a fonte **Azure AD conectar atualizar** e o evento id intervalo **300-399**.  
![Filtro de log de eventos para atualização automática](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)  

Agora você pode ver os logs de eventos associado com o status de atualização automática.  
![Filtro de log de eventos para atualização automática](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)  

O código de resultado tem um prefixo com uma visão geral do estado.

Prefixo de código de resultado | Descrição
--- | ---
Sucesso | A instalação foi atualizada com êxito.
UpgradeAborted | Uma condição temporária interrompido a atualização. Ele será repetido novamente e as expectativas é que ele é bem sucedida mais tarde.
UpgradeNotSupported | O sistema tem uma configuração que está bloqueando o sistema de sendo atualizado automaticamente. Ele será repetido para ver se o estado está mudando, mas as expectativas é que o sistema deve ser atualizado manualmente.

Aqui está uma lista das mensagens mais comuns de que encontrar. Ela não lista todos, mas a mensagem de resultado deverá ser limpar com que o problema é.

Mensagem de resultado | Descrição
--- | ---
**UpgradeAborted** |
UpgradeAbortedCouldNotSetUpgradeMarker | Não foi possível gravar no registro.
UpgradeAbortedInsufficientDatabasePermissions | O grupo de administradores internos não tem permissões para o banco de dados. Atualize manualmente a versão mais recente do Azure AD Connect para resolver esse problema.
UpgradeAbortedInsufficientDiskSpace | Não há espaço em disco suficiente para suportar uma atualização.
UpgradeAbortedSecurityGroupsNotPresent | Não foi possível localizar e resolver todos os grupos de segurança usados pelo mecanismo de sincronização.
UpgradeAbortedServiceCanNotBeStarted | Serviço NT **Microsoft Azure AD Sync** falhou ao iniciar.
UpgradeAbortedServiceCanNotBeStopped | Serviço NT **Microsoft Azure AD Sync** falhou ao parar.
UpgradeAbortedServiceIsNotRunning | Serviço NT **Microsoft Azure AD Sync** não está sendo executado.
UpgradeAbortedSyncCycleDisabled | A opção SyncCycle no [Agendador](active-directory-aadconnectsync-feature-scheduler.md) foi desativada.
UpgradeAbortedSyncExeInUse | O [Gerenciador de serviço de sincronização UI](active-directory-aadconnectsync-service-manager-ui.md) está aberta no servidor.
UpgradeAbortedSyncOrConfigurationInProgress | O Assistente de instalação está em execução ou uma sincronização foi agendada fora o Agendador.
**UpgradeNotSupported** |
UpgradeNotSupportedCustomizedSyncRules | Você adicionou suas próprias regras personalizadas para a configuração.
UpgradeNotSupportedDeviceWritebackEnabled | Você ativou o recurso de [Write-back do dispositivo](active-directory-aadconnect-feature-device-writeback.md) .
UpgradeNotSupportedGroupWritebackEnabled | Você ativou o recurso de [Write-back do grupo](active-directory-aadconnect-feature-preview.md#group-writeback) .
UpgradeNotSupportedInvalidPersistedState | A instalação não é um configurações Express ou uma atualização de DirSync.
UpgradeNotSupportedMetaverseSizeExceeeded | Você tem mais de 100.000 objetos no metaverso.
UpgradeNotSupportedMultiForestSetup | Você está se conectando mais de uma floresta. A configuração expressa somente se conecta a uma floresta.
UpgradeNotSupportedNonLocalDbInstall | Você não estiver usando um banco de dados do SQL Server Express LocalDB.
UpgradeNotSupportedNonMsolAccount | A [conta do AD conector](active-directory-aadconnect-accounts-permissions.md#active-directory-account) não é a conta padrão do MSOL_ mais.
UpgradeNotSupportedStagingModeEnabled | O servidor está definido no [modo de teste](active-directory-aadconnectsync-operations.md#staging-mode).
UpgradeNotSupportedUserWritebackEnabled | Você ativou o recurso de [Write-back do usuário](active-directory-aadconnect-feature-preview.md#user-writeback) .

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
