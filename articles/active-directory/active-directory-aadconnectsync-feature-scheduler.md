<properties
   pageTitle="Sincronização do Azure AD Connect: Agendador | Microsoft Azure"
   description="Este tópico descreve o recurso Agendador interno do Azure AD Connect sincronização."
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
   ms.date="08/04/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-scheduler"></a>Sincronização do Azure AD Connect: Agendador
Este tópico descreve o agendador interno na sincronização do Azure AD Connect (também conhecido como mecanismo de sincronização).

Este recurso foi introduzido com compilação 1.1.105.0 (lançada fevereiro de 2016).

## <a name="overview"></a>Visão geral
Sincronização do Azure AD Connect sincronizará acontecendo no seu diretório local usando um agendador de alterações. Há dois processos de Agendador, uma para sincronização de senha e outra para sincronização de atributo do objeto e tarefas de manutenção. Este tópico abordará o último.

Em versões anteriores do Agendador para objetos e atributos era externo para o mecanismo de sincronização e o Agendador de tarefas do Windows ou um serviço Windows separado foi usado para acionar o processo de sincronização. O Agendador é com os internos de 1.1 versões para a sincronização engine e permitir a alguns personalização. A nova frequência de sincronização padrão é 30 minutos.

O Agendador é responsável por duas tarefas:

- **Ciclo de sincronização**. O processo a importar, sincronizar e exportar alterações.
- **Tarefas de manutenção**. Renove chaves e certificados para redefinição de senha e o serviço de registro do dispositivo (DRS). Limpe antigas entradas no log de operações.

O Agendador próprio sempre está em execução, mas ele pode ser configurado para executar somente uma ou nenhuma dessas tarefas. Por exemplo se você precisa ter seu próprio processo de ciclo de sincronização, você pode desabilitar esta tarefa no Agendador mas ainda executar a tarefa de manutenção.

## <a name="scheduler-configuration"></a>Configuração do Agendador
Para ver as configurações atuais, vá ao PowerShell e executar `Get-ADSyncScheduler`. Ele mostrará algo parecido com isto:

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings.png)

Se você vir **o comando de sincronização ou o cmdlet não está disponível** quando você executa esse cmdlet, o módulo do PowerShell não é carregado. Isso pode acontecer se você executar o Azure AD Connect em um controlador de domínio ou em um servidor com níveis de restrição mais altos do PowerShell que as configurações padrão. Se você vir esse erro, execute `Import-Module ADSync` para disponibilizar o cmdlet.

- **AllowedSyncCycleInterval**. Com mais frequência Azure AD permitirá sincronizações ocorra. Você não pode sincronizar mais do que isso e ainda ser suportada.
- **CurrentlyEffectiveSyncCycleInterval**. O agendamento atualmente em vigor. Ele terá o mesmo valor que CustomizedSyncInterval (se definir) se não for mais frequente que AllowedSyncInterval. Se você alterar CustomizedSyncCycleInterval, isso entrará em vigor após o próximo ciclo de sincronização.
- **CustomizedSyncCycleInterval**. Se desejar que o Agendador para executar qualquer outra frequência do padrão 30 minutos, você configurará essa configuração. Na imagem acima o Agendador foi definida para executar a cada hora, em vez disso. Se você definir como um valor menor do que AllowedSyncInterval, o último será usado.
- **NextSyncCyclePolicyType**. Delta ou inicial. Define se próxima execução deve apenas o processo delta alterações, ou se próxima execução deve fazer uma completa importar e sincronizar, que também seria reprocessar quaisquer regras novas ou alteradas.
- **NextSyncCycleStartTimeInUTC**. Próxima vez que o Agendador iniciará o próximo ciclo de sincronização.
- **PurgeRunHistoryInterval**. O tempo de operação logs devem ser mantidos. Eles podem ser examinados no Gerenciador de serviço de sincronização. O padrão é mantê-las por 7 dias.
- **SyncCycleEnabled**. Indica se o Agendador está executando a importação, sincronizar e processos de exportação como parte de sua operação.
- **MaintenanceEnabled**. Mostra se o processo de manutenção está habilitado. Ele irá atualizar as certificados/chaves e limpar o log de operações.
- **IsStagingModeEnabled**. Mostra se o [modo de teste](active-directory-aadconnectsync-operations.md#staging-mode) está habilitado.

Você pode alterar algumas dessas configurações com `Set-ADSyncScheduler`. Os seguintes parâmetros podem ser modificados:

- CustomizedSyncCycleInterval
- NextSyncCyclePolicyType
- PurgeRunHistoryInterval
- SyncCycleEnabled
- MaintenanceEnabled

A configuração do Agendador é armazenada no Azure AD. Se você tiver um servidor de teste, qualquer alteração no servidor primário também afetarão o servidor de teste (com exceção de IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Sintaxe:`Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - dias, HH - horas, mm - minutos, ss - segundos

Exemplo:`Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Alterará o Agendador para executar cada 3 horas.

Exemplo:`Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Alterará o Agendador para executar diariamente.

## <a name="start-the-scheduler"></a>Iniciar o Agendador
Por padrão, o Agendador será executado a cada 30 minutos. Em alguns casos talvez você queira executar um ciclo de sincronização entre os ciclos de agendada ou necessário para executar um tipo diferente.

**Ciclo de sincronização delta**  
Um ciclo de sincronização delta inclui as seguintes etapas:

- Importação de delta em todos os conectores
- Sincronizar delta em todos os conectores
- Exportar todos os conectores

É possível que você tenha um urgente alterar que deve ser sincronizado imediatamente motivo pelo qual você precisa executar manualmente um ciclo. Se você precisar executar manualmente um ciclo, depois de executar o PowerShell `Start-ADSyncSyncCycle -PolicyType Delta`.

**Ciclo de sincronização completa**  
Se você tiver feito uma das seguintes alterações de configuração, você precisa executar um ciclo de sincronização completa (também conhecido como Inicial):

- Adicionou mais objetos ou atributos a ser importado de um diretório de origem
- Alterações feitas as regras de sincronização
- Alterada [filtragem](active-directory-aadconnectsync-configure-filtering.md) , portanto, um número diferente de objetos deve ser incluído

Se você tiver feito uma dessas alterações, você precisa executar um ciclo de sincronização completa assim o mecanismo de sincronização tem a oportunidade de reconsolidar os espaços de conector. Um ciclo de sincronização completa inclui as seguintes etapas:

- Importação completa de todos os conectores
- Sincronização completa de todos os conectores
- Exportar todos os conectores

Para iniciar um ciclo de sincronização completa, execute `Start-ADSyncSyncCycle -PolicyType Initial` partir de um prompt do PowerShell. Isso iniciará um ciclo de sincronização completa.

## <a name="stop-the-scheduler"></a>Interromper o Agendador
Se o Agendador está sendo executado um ciclo de sincronização, que talvez você precise interrompê-lo. Por exemplo, se você iniciar o Assistente de instalação e você receber esse erro:

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Quando um ciclo de sincronização é executado, você não pode fazer alterações de configuração. Você poderia esperar até que o Agendador terminou o processo, mas você também poderá interrompê-lo para que possa fazer as alterações imediatamente. Parar o ciclo atual não é prejudicial e quaisquer alterações ainda não processadas serão processadas com próxima execução.

1. Iniciar informando o Agendador para interromper seu ciclo atual com o cmdlet do PowerShell `Stop-ADSyncSyncCycle`.
2. Interromper o Agendador não interromperá conector atual de sua tarefa atual. Para forçar o conector para parar, faça o seguinte: ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
    - Inicie o **Serviço de sincronização** do menu Iniciar. Vá para **conectores**, realce o conector com o estado **em execução** e selecione **Parar** as ações.

O Agendador ainda está ativo e iniciará novamente na próxima oportunidade.

## <a name="custom-scheduler"></a>Agendador personalizado
Os cmdlets documentados nesta seção só estão disponíveis no compilação [1.1.130.0](active-directory-aadconnect-version-history.md#111300) e posterior.

Se o agendador interno não atender às suas necessidades, você pode programar os conectores usando o PowerShell.

### <a name="invoke-adsyncrunprofile"></a>ADSyncRunProfile invocar
Você pode iniciar um perfil para um conector dessa maneira:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Os nomes para usar para [nomes de conector](active-directory-aadconnectsync-service-manager-ui-connectors.md) e [perfil executar](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles) podem ser encontrados na [Interface de usuário do Gerenciador de serviço de sincronização](active-directory-aadconnectsync-service-manager-ui.md).

![Invocar perfil de execução](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

O `Invoke-ADSyncRunProfile` cmdlet é síncrono, ou seja, não retornará controle até que o conector concluiu a operação, com êxito ou com um erro.

Quando você agenda seus conectores, a recomendação é agendá-los na seguinte ordem:

1. (Completo/Delta) Importar de diretórios no local, como o Active Directory
2. (Completo/Delta) Importar do Azure AD
3. (Completo/Delta) Sincronização de diretórios no local, como o Active Directory
4. (Completo/Delta) Sincronização do Azure AD
5. Exportar para o Azure AD
6. Exportar para diretórios de local, como o Active Directory

Se você examinar o agendador interno, essa é a ordem em que os conectores serão executadas.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Você também pode monitorar o mecanismo de sincronização para ver se ele está ocupado ou ocioso. Esse cmdlet retornará um resultado vazio se o mecanismo de sincronização está ocioso e não está sendo executado um conector. Se estiver executando um conector, ela retornará o nome do conector.

```
Get-ADSyncConnectorRunStatus
```

![Status da execução de conector](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
Na imagem acima, a primeira linha é de um estado em que o mecanismo de sincronização está ocioso. Na segunda linha de quando o conector do Azure AD está sendo executado.

## <a name="scheduler-and-installation-wizard"></a>Assistente de instalação e Agendador
Se você iniciar o Assistente de instalação, em seguida, o Agendador será temporariamente suspenso. Isso ocorre porque pressupõe-se que você vai fazer alterações de configuração e eles não podem ser aplicados se o mecanismo de sincronização ativamente estiver em execução. Por esse motivo, não deixe o Assistente para instalação aberto desde que ele irá parar o mecanismo de sincronização de executar quaisquer ações de sincronização.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
