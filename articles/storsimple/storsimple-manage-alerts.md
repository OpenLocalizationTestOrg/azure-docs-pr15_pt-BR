<properties 
   pageTitle="Exibir e gerenciar alertas de StorSimple | Microsoft Azure"
   description="Descreve as condições de alerta StorSimple e gravidade, como configurar notificações de alerta e como usar o serviço do Gerenciador de StorSimple para gerenciar alertas."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/18/2016"
   ms.author="anbacker" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-alerts"></a>Usar o serviço do Gerenciador de StorSimple para exibir e gerenciar alertas de StorSimple

## <a name="overview"></a>Visão geral

Na guia **alertas** no serviço Gerenciador de StorSimple fornece uma maneira de rever e limpar StorSimple relacionados ao dispositivo alertas em tempo real. Nesta guia, você pode monitorar centralmente os problemas de integridade de seus dispositivos de StorSimple e a solução Microsoft Azure StorSimple geral.

Este tutorial descreve condições comuns de alerta, níveis de gravidade de alerta e como configurar as notificações de alerta. Além disso, ele inclui tabelas de alerta de referência rápida, que permitem localizar um alerta específico e responder adequadamente com rapidez.

![Página alertas](./media/storsimple-manage-alerts/HCS_AlertsPage.png)

## <a name="common-alert-conditions"></a>Condições de alerta comuns

Seu dispositivo StorSimple gera alertas em resposta a uma variedade de condições. A seguir está os tipos mais comuns de condições de alerta:

- **Problemas de hardware** – estes alertas informam sobre a integridade de seu hardware. Eles avise se firmware forem necessárias atualizações, se uma interface de rede tiver problemas ou se há um problema com uma de suas unidades de dados.

- **Problemas de conectividade** – esses alertas ocorrem quando há dificuldade em transferência de dados. Problemas de comunicação podem ocorrer durante a transferência de dados e da conta de armazenamento do Azure ou devido a falta de conectividade entre os dispositivos e o serviço do Gerenciador de StorSimple. Problemas de comunicação são alguns dos mais difíceis de corrigir porque há muitos pontos de falha. Você deve sempre verificar primeiro que a conectividade de rede e acesso à Internet estão disponíveis antes de continuar a solução de problemas mais avançados. Para obter ajuda com a solução de problemas, vá para [Solucionar problemas com o cmdlet de Conexão de teste](storsimple-troubleshoot-deployment.md).

- **Problemas de desempenho** – esses alertas são causados quando o sistema não estiver executando ideal, como quando ela está sob uma carga pesada.

Além disso, você talvez veja alertas relacionadas à segurança, atualizações ou falhas de trabalho.

## <a name="alert-severity-levels"></a>Níveis de gravidade de alerta

Alertas têm diferentes níveis de gravidade, dependendo do impacto que terá a situação alerta e a necessidade de uma resposta para o alerta. Os níveis de gravidade são:

- **Crítica** – este alerta ocorre em resposta a uma condição que está afetando o desempenho bem-sucedida do seu sistema. Ação é necessária para garantir que o StorSimple serviço não é interrompido.

- **Aviso** – esta condição pode tornar-se crítica se não resolvido. Você deve investigar a situação e realizar qualquer ação necessária para limpar o problema.

- **Informações** – esse alerta contém informações que podem ser úteis para controlar e gerenciar seu sistema.

## <a name="configure-alert-settings"></a>Configurar definições de alerta

Você pode escolher se deseja ser notificado por email de condições de alerta para cada um dos seus dispositivos de StorSimple. Além disso, você pode identificar outros destinatários de notificação de alerta por meio de seus endereços de email na caixa **outros destinatários de email** , separada por ponto e vírgula.

>[AZURE.NOTE] Você pode inserir um máximo de 20 endereços de email por dispositivo.

Depois de habilitar notificação por email para um dispositivo, os membros da lista de notificação receberá uma mensagem de email cada vez que ocorre um alerta crítico. As mensagens serão enviadas por *storsimple-alerts-noreply@mail.windowsazure.com* e descreverá da condição de alerta. Destinatários podem clicar em **Cancelar assinatura** para remover da lista de notificação de email.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Para ativar a notificação de email de alertas para um dispositivo

1. Vá para **dispositivos** > **Configurar** para o dispositivo.

2. Em **Configurações de alerta**, defina o seguinte:

    1. No campo **Enviar notificação por email** , selecione **Sim**.

    2. No campo **administradores de serviços de Email** , selecione **Sim** se desejar que o administrador do serviço e todos os administradores co recebem as notificações de alerta.

    3. No campo **outros destinatários de email** , insira os endereços de email de todos os outros destinatários que devem receber as notificações de alerta. Insira os nomes no formato *someone@somewhere.com*. Use ponto e vírgula para separar os endereços de email. Você pode configurar um máximo de 20 endereços de email por dispositivo. 

        ![Configuração de notificação de alertas](./media/storsimple-manage-alerts/AlertNotify.png)

3. Para enviar uma notificação de email de teste, clique no ícone de seta ao lado de **Enviar email de teste**. O serviço do Gerenciador de StorSimple exibirá mensagens de status como ele encaminha a notificação de teste. 

4. Quando a seguinte mensagem for exibida, clique em **Okey**. 

    ![Teste de alertas de e-mail de notificação enviado](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig3.png)

    >[AZURE.NOTE] Se a mensagem de notificação de teste não puder ser enviada, o serviço do Gerenciador de StorSimple exibirá uma mensagem apropriada. Clique **Okey**, aguarde alguns minutos e tente enviar sua mensagem de notificação de teste novamente. 

## <a name="view-and-track-alerts"></a>Exibir e controlar alertas

O painel de serviço do Gerenciador de StorSimple oferece uma olhada rápida no número de alertas em seus dispositivos, organizados por nível de gravidade.

![Painel de alertas](./media/storsimple-manage-alerts/admin_alerts_dashboard.png)

Clicar em nível de gravidade abre na guia **alertas** . Os resultados incluem somente os alertas que correspondem a esse nível de gravidade.

![Relatório de alertas com escopo de tipo de alerta](./media/storsimple-manage-alerts/admin_alerts_scoped.png)

Clicando em um alerta na lista fornece detalhes adicionais para o alerta, incluindo a última vez em que o alerta foi relatado, o número de ocorrências do alerta sobre o dispositivo e a ação recomendada para resolver o alerta. Se for um alerta de hardware, ele também será identificar o componente de hardware.

![Exemplo de alerta de hardware](./media/storsimple-manage-alerts/admin_alerts_hardware.png)

Você pode copiar os detalhes do alerta para um arquivo de texto se você precisar enviar as informações ao Microsoft Support. Depois de ter seguido a recomendação e resolvido o condição de alerta local, você deve desmarcar o alerta do dispositivo selecionando o alerta na guia **alertas** e clicando em **Limpar**. Para limpar vários alertas, selecione cada alerta, clique em qualquer coluna exceto a coluna de **alerta** e clique em **Limpar** depois que você tiver selecionado todos os alertas para ser limpos. Observe que alguns alertas automaticamente são removidos quando o problema for resolvido ou quando o sistema atualiza o alerta com novas informações.

Quando você clica em **Limpar**, você terá a oportunidade de fornecer comentários sobre o alerta e as etapas que você tirou para resolver o problema. Alguns eventos serão limpo pelo sistema se outro evento for disparado com novas informações. Nesse caso, você verá a seguinte mensagem.

![Mensagem de alerta limpar](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Alertas de classificação e revisão

Você pode encontrar mais eficiente executar relatórios sobre alertas para que você possa revisar e apagá-los em grupos. Além disso, na guia **alertas** pode exibir até 250 alertas. Se você tiver excedido o número de alertas, nem todos os alertas serão exibidos no modo de exibição padrão. Você pode combinar os seguintes campos para personalizar quais alertas são exibidas:

- **Status** – você pode exibir os alertas de **ativo** ou **desmarcada** . Alertas ativos ainda estão sendo disparadas em seu sistema, enquanto alertas desmarcadas tenham sido limpas manualmente por um administrador ou limpas programaticamente porque o sistema atualizado da condição de alerta com novas informações.

- **Gravidade** – você pode exibir os alertas de todos os níveis de gravidade (críticas, avisos, informações) ou apenas uma determinada gravidade, como alertas somente críticos.

- **Origem** – você pode exibir alertas de todas as fontes ou limitar os alertas para aqueles que vêm de serviço ou um ou todos os dispositivos.

- **Intervalo de tempo** – especificando as datas **de** e **para** e carimbos de hora, você pode examinar alertas durante o período de tempo que você está interessado.

## <a name="alerts-quick-reference"></a>Referência rápida de alertas

As tabelas a seguir listam alguns dos alertas Microsoft Azure StorSimple que podem ocorrer, bem como recomendações e informações adicionais onde estiver disponível. Alertas de dispositivo StorSimple se encaixam em uma das categorias a seguir:

- [Alertas de conectividade de nuvem](#cloud-connectivity-alerts)

- [Alertas de cluster](#cluster-alerts)

- [Alertas de recuperação de desastres](#disaster-recovery-alerts)

- [Alertas de hardware](#hardware-alerts)

- [Alertas de falha de trabalho](#job-failure-alerts)

- [Alertas de volume localmente fixado](#locally-pinned-volume-alerts)

- [Alertas de rede](#networking-alerts)

- [Alertas de desempenho](#performance-alerts)

- [Alertas de segurança](#security-alerts)

- [Suporte a pacote alertas](#support-package-alerts)

- [Alertas de atualização](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividade de nuvem

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Conectividade com o <*nome de credencial de nuvem*> não pode ser estabelecida.|Não é possível se conectar à conta de armazenamento.|Parece que pode haver um problema de conectividade com o seu dispositivo. Execute o `Test-HcsmConnection` cmdlet da Interface do Windows PowerShell para StorSimple em seu dispositivo para identificar e corrigir o problema. Se as configurações estão corretas, o problema pode ser com as credenciais da conta de armazenamento para o qual o alerta foi gerado. Nesse caso, use o `Test-HcsStorageAccountCredential` cmdlet para determinar se há problemas que você pode resolver.<ul><li>Verifique suas configurações de rede.</li><li>Verificar suas credenciais de conta de armazenamento.</li></ul>|
|Não recebemos uma pulsação do seu dispositivo para os últimos <*número*> minutos.|Não é possível conectar ao dispositivo.|Parece que há um problema de conectividade com o seu dispositivo. Use o `Test-HcsmConnection` cmdlet da Interface do Windows PowerShell para StorSimple em seu dispositivo para identificar e corrigir o problema ou contate o administrador de rede.|

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>Comportamento de StorSimple quando falha de conectividade de nuvem

O que acontece se falha de conectividade de nuvem para o meu dispositivo de StorSimple em execução em produção?

Se conectividade de nuvem falhar em seu dispositivo de produção StorSimple, em seguida, dependendo do estado do seu dispositivo, o seguinte pode ocorrer: 

- **Para os dados locais no seu dispositivo**: por algum tempo, haverá sem interrupção e leituras continuará a ser atendidas. No entanto, como o número do IOs pendentes aumenta e excede um limite, as leituras podem começar a falhar. 

    Dependendo da quantidade de dados em seu dispositivo, as gravações também continuará ocorra para as primeiras horas após a interrupção em conectividade a nuvem. As gravações serão reduzir a velocidade e eventualmente começar a falhar se a conectividade de nuvem é interrompida por várias horas. (Há armazenamento temporário no dispositivo para dados que devem ser enviadas para a nuvem. Esta área é limpos quando os dados são enviados. Se a conectividade falhar, dados nesta área de armazenamento não serão enviados para a nuvem e IO falhará.)   

 
- **Para os dados na nuvem**: a maioria dos erros de conectividade de nuvem, será retornado um erro. Depois que a conectividade for restaurada, o IOs são retomado sem que o usuário que está tendo que colocar o volume online. Em casos raros, intervenção do usuário pode ser necessária para trazer de volta o volume online a partir do portal clássico Azure. 
 
- **Para instantâneos de nuvem em andamento**: A operação será repetida algumas vezes dentro de 4 e 5 horas e se a conectividade não é restaurada, os instantâneos de nuvem falhará.


### <a name="cluster-alerts"></a>Alertas de cluster

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Dispositivo falhou para <*nome do dispositivo*>.|Dispositivo está no modo de manutenção.|Dispositivo falhou sobre devido a inserindo ou sair do modo de manutenção. Isso é normal e nenhuma ação é necessária. Depois que você tiver confirmado este alerta, desmarque-a na página alertas.|
|Dispositivo falhou para <*nome do dispositivo*>.|Firmware de dispositivo ou software apenas foi atualizado.|Houve um failover de cluster devido a uma atualização. Isso é normal e nenhuma ação é necessária. Depois que você tiver confirmado este alerta, desmarque-a na página alertas.|
|Dispositivo falhou para <*nome do dispositivo*>.|Controlador foi encerrado ou reiniciado.|Falha do dispositivo sobre porque o controlador de ativo foi encerrado ou reiniciado por um administrador. Nenhuma ação é necessária. Depois que você tiver confirmado este alerta, desmarque-a na página alertas.|
|Dispositivo falhou para <*nome do dispositivo*>.|Failover planejado.|Verificar se isso era um failover planejado. Após você ter conduzido ação apropriada, desmarque esse alerta na página alertas.|
|Dispositivo falhou para <*nome do dispositivo*>.|Failover não planejado.|StorSimple baseia-se para recuperar automaticamente failovers planejado. Se você vir um grande número desses alertas, contate o Microsoft Support.|
|Dispositivo falhou para <*nome do dispositivo*>.|Causa outra/desconhecida.|Se você vir um grande número desses alertas, contate o Microsoft Support. Depois que o problema for resolvido, desmarque esse alerta na página alertas.|
|Um serviço de dispositivo críticos relatórios de status como falha.|Falha de DataPath de serviço. |Contate o Microsoft Support para obter assistência.|
|Endereço IP virtual para a interface de rede <*dados #*> relatórios de status como falha. |Causa outra/desconhecida. |Condições temporárias, às vezes, podem causar esses alertas. Se esse for o caso, em seguida, esse alerta será automaticamente limpa após alguns minutos. Se o problema persistir, contate o Microsoft Support.|
|Endereço IP virtual para a interface de rede <*dados #*> relatórios de status como falha.|Nome da interface: <*dados #*> endereço IP <IP address> não pode ser colocado online porque um endereço IP duplicado foi detectado na rede. |Certifique-se de que o endereço IP duplicado será removido da rede ou reconfigure a interface com um endereço IP diferente.|


### <a name="disaster-recovery-alerts"></a>Alertas de recuperação de desastres

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Operações de recuperação não podem restaurar todas as configurações para esse serviço. Dados de configuração de dispositivo estão em um estado inconsistente para alguns dispositivos.|Inconsistência de dados detectada após a recuperação de dados.|Dados criptografados no serviço não estão sincronizados com isso no dispositivo. Autorize o dispositivo <*nome do dispositivo*> em Gerenciador de StorSimple para iniciar o processo de sincronização. Usar a Interface do Windows PowerShell para StorSimple para executar o `Restore-HcsmEncryptedServiceData` no dispositivo <*nome do dispositivo*> cmdlet, fornecendo a senha antiga como entrada para esse cmdlet para restaurar o perfil de segurança. Execute o `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet para atualizar a chave de criptografia de dados de serviço. Após você ter conduzido ação apropriada, desmarque esse alerta na página alertas.|


### <a name="hardware-alerts"></a>Alertas de hardware

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Componente de hardware <*ID do componente*> relatórios status como <*status*>.||Condições temporárias, às vezes, podem causar esses alertas. Em caso afirmativo, este alerta será limpo automaticamente após alguns minutos. Se o problema persistir, contate o Microsoft Support.|
|Controlador de passivo funcionando corretamente.|O controlador (secundário) passivo não está funcionando.|Seu dispositivo está funcionando, mas um dos seus controladores está funcionando corretamente. Tente reiniciar esse controlador. Se o problema não for resolvido, contate o Microsoft Support.|

### <a name="job-failure-alerts"></a>Alertas de falha de trabalho

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Falha no backup <*ID do grupo de volume de origem*> de.|Falha no trabalho de backup.|Problemas de conectividade podem estar impedindo a operação de backup seja concluída com êxito. Se não houver nenhum problemas de conectividade, talvez você tenha atingido o número máximo de backups. Exclua todos os backups que não são mais necessários e repita a operação. Após você ter conduzido ação apropriada, desmarque esse alerta na página alertas.|
|Clonar de <*elemento de fonte de backup IDs*> <*números de série de volume de destino*> falha.|Falha ao clonar trabalho.|Atualize a lista de backup para verificar se o backup ainda é válido. Se o backup for válido, é possível que problemas de conectividade de nuvem estejam impedindo a operação de clonagem seja concluída com êxito. Se não houver nenhum problemas de conectividade, você pode atingiu o limite de armazenamento. Exclua todos os backups que não são mais necessários e repita a operação. Após você ter conduzido ação apropriada para resolver o problema, desmarque esse alerta na página alertas.|
|Falha na restauração de <*elemento de fonte de backup IDs*>.|Restaure falha no trabalho.|Atualize a lista de backup para verificar se o backup ainda é válido. Se o backup for válido, é possível que problemas de conectividade de nuvem estejam impedindo a operação de restauração seja concluída com êxito. Se não houver nenhum problemas de conectividade, você pode atingiu o limite de armazenamento. Exclua todos os backups que não são mais necessários e repita a operação. Após você ter conduzido ação apropriada para resolver o problema, desmarque esse alerta na página alertas.|

### <a name="locally-pinned-volume-alerts"></a>Alertas de volume localmente fixado

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Falha na criação de volume local <*nome do volume*>.| O trabalho de criação de volume falhou. <*Mensagem de erro correspondente para o código de erro Falha*>.|Problemas de conectividade podem estar impedindo a operação de criação de espaço de conclusão com êxito. Volumes localmente fixados thickly são provisionados e o processo de criação de espaço envolve derramada volumes hierárquicos para a nuvem. Se não houver nenhum problemas de conectividade, você pode ter esgotado o espaço local no dispositivo. Determine se o espaço existe no dispositivo antes de tentar novamente a operação.|
|Expansão de volume local <*nome do volume*> falha.|O trabalho de modificação de volume falhou devido a <*mensagem de erro correspondente para o código de erro Falha*>.|Problemas de conectividade podem estar impedindo a operação de expansão de volume do concluído com êxito. Volumes localmente fixados thickly são provisionados e o processo de estender o espaço existente envolve derramada volumes hierárquicos para a nuvem. Se não houver nenhum problemas de conectividade, você pode ter esgotado o espaço local no dispositivo. Determine se o espaço existe no dispositivo antes de tentar novamente a operação.|
|Falha de conversão de <*nome do volume*> volume.|O trabalho de conversão de volume para converter o tipo de volume de localmente fixada hierárquico falha.|Conversão do volume de tipo localmente fixado hierárquico não pôde ser concluída. Certifique-se de que não há nenhum problemas de conectividade impedindo que a operação concluída com êxito. Para solucionar problemas de conectividade vá para [Solucionar problemas com o cmdlet Test-HcsmConnection](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>O volume localmente fixado original agora foi marcado como um volume hierárquico desde que alguns dos dados do volume localmente fixado tem derramado na nuvem durante a conversão. O volume de hierárquico resultante ainda ocupa espaço local no dispositivo que não pode ser recuperado para futuros volumes locais.<br>Resolver problemas de conectividade, desmarque o alerta e converta este volume volta para o tipo de volume localmente fixada original para garantir que todos os dados são disponibilizados localmente novamente.|
|Falha de conversão de <*nome do volume*> volume.|O trabalho de conversão de volume para converter o tipo de volume do hierárquico para localmente fixos falha.|Conversão do volume de tipo hierárquico para localmente fixados não podem ser concluído. Certifique-se de que não há nenhum problemas de conectividade impedindo que a operação concluída com êxito. Para solucionar problemas de conectividade vá para [Solucionar problemas com o cmdlet Test-HcsmConnection](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>O volume hierárquico original agora marcado como um volume localmente fixado como parte do processo de conversão continua a ter dados residente na nuvem, enquanto o espaço thickly provisionado no dispositivo para este volume já não pode recuperado para futuros volumes locais.<br>Resolver problemas de conectividade, desmarque o alerta e converta este volume volta para o tipo de volume hierárquico original para garantir espaço local thickly provisionado no dispositivo pode ser recuperado.|
|Se aproximando consumo de espaço local para locais instantâneos de <*nome do grupo de volume*>|Instantâneos locais para a política de backup assim podem ficar sem espaço e ser invalidados para evitar falhas de gravação do host.|Frequentes instantâneos locais junto com uma rotatividade alta dados nos volumes associadas a esse grupo de política de backup estão causando espaço local no dispositivo para ser consumida rapidamente. Exclua qualquer instantâneos locais que não são mais necessárias. Além disso, atualize suas agendas instantâneo local para esta política de backup instantâneos menos frequentes local e garantir que os instantâneos de nuvem são criados regularmente. Se essas ações não são executadas, assim pode ser esgotado local espaço para esses instantâneos e o sistema automaticamente excluirá-los para garantir que as gravações de host continuem sendo processadas com êxito.|
|Instantâneos locais para <*nome do grupo de volume*> tiveram sido invalidados.|Os instantâneos locais para <*nome do grupo de volume*> foram invalidados e depois excluídos porque eles foram exceder o espaço local no dispositivo.|Para garantir que isso não se repita no futuro, examine os agendamentos de instantâneo local para esta política de backup e exclua qualquer instantâneos locais que não são mais necessárias. Frequentes instantâneos locais junto com uma rotatividade alta dados nos volumes associadas a esse grupo de política de backup podem causar espaço local no dispositivo para ser consumida rapidamente.|
|Falha na restauração de <*elemento de fonte de backup IDs*>.|O trabalho de restauração falhou.|Se você tiver fixos localmente ou uma mistura de volumes localmente fixados e hierárquicos nesta política de backup, atualize a lista de backup para verificar se o backup ainda é válido. Se o backup for válido, é possível que problemas de conectividade de nuvem estejam impedindo a operação de restauração seja concluída com êxito. Os volumes localmente fixados que estavam sendo restaurados como parte desse grupo de instantâneo não tem todos os seus dados baixados para o dispositivo e, se você tiver uma mistura de volumes hierárquicos e localmente fixados este grupo instantâneo, elas não serão sincronizadas com os outros. Para concluir a operação de restauração, faça os volumes neste grupo offline no host e repita a operação de restauração. Observe que todas as modificações aos dados volume que foram executadas durante o processo de restauração serão perdidas.|

### <a name="networking-alerts"></a>Alertas de rede

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Serviços de StorSimple não pôde ser iniciado.|Erro de DataPath |Se o problema persistir, contate o Microsoft Support.|
|Endereço IP duplicado detectado para 'Data0'.| |O sistema detectou um conflito de endereço IP '10.0.0.1'. O recurso de rede 'Data0' no dispositivo *<device1>* está offline. Certifique-se de que este endereço IP não é usado por qualquer outra entidade nesta rede. Para solucionar problemas de rede, vá para [Solucionar problemas com o cmdlet Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Contate o administrador de rede para ajudar a resolver esse problema. Se o problema persistir, contate o Microsoft Support. |
|Endereços IPv4 (ou IPv6) para 'Data0' está offline.| |O recurso de rede 'Data0' com endereço IP '10.0.0.1'. e comprimento '22' no dispositivo de prefixo *<device1>* está offline. Certifique-se de que as portas do switch à qual essa interface está conectada estão funcionando. Para solucionar problemas de rede, vá para [Solucionar problemas com o cmdlet Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
 

### <a name="performance-alerts"></a>Alertas de desempenho

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|A carga de dispositivo excedeu <*limite*>.|Mais lento que o esperado tempos de resposta.|Utilização sob uma carga de entrada/saída pesada de relatórios do seu dispositivo. Isso pode causar o dispositivo não funcione bem como deveria. Examine as cargas de trabalho que você tiver conectado ao dispositivo e determine se houver algum que poderia ser movido para outro dispositivo ou que não são mais necessárias.<br>Para compreender o status atual, vá para [usar o serviço de Gerenciador de StorSimple para monitorar seu dispositivo](storsimple-monitor-device.md)|

### <a name="security-alerts"></a>Alertas de segurança

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Sessão Microsoft Support começou.|Terceiro acessadas sessão de suporte.|Confirme que esse acesso está autorizado. Após você ter conduzido ação apropriada, desmarque esse alerta na página alertas.|
|Senha de <*elemento*> expirará em <*período de tempo*>.|Expiração de senha está se aproximando.|Altere sua senha antes que ela expire.|
|Informações de configuração de segurança ausente para <*elemento ID*>.||Os volumes associados a este contêiner de volume não podem ser usados para duplicar sua configuração de StorSimple. Para garantir que seus dados são armazenados com segurança, recomendamos que você exclua o contêiner de volume e quaisquer volumes associados com o contêiner de volume. Após você ter conduzido ação apropriada, desmarque esse alerta na página alertas.|
|<*número*> falharam de tentativas de logon para <*elemento ID*>.|Tentativas de várias tentativas de logon.|O dispositivo pode estar sob ataque ou um usuário autorizado está tentando se conectar com uma senha incorreta.<ul><li>Contate seus usuários autorizados e verificar se essas tentativas foram uma origem legítima. Se você continuar a ver grande número de tentativas de login falhas, considere desabilitando o gerenciamento remoto e entrar em contato com seu administrador de rede. Após você ter conduzido ação apropriada, desmarque esse alerta na página alertas.</li><li>Verifique se suas instâncias do Gerenciador de instantâneo estão configuradas com a senha correta. Após você ter conduzido ação apropriada, desmarque esse alerta na página alertas.</li></ul>Para obter mais informações, vá para [alterar uma senha de dispositivo expirada](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password).|
|Falhas de um ou mais ocorreram ao alterar a chave de criptografia de dados de serviço.||Ocorreram erros encontrados ao alterar a chave de criptografia de dados de serviço. Depois de você resolveu as condições de erro, execute o `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet da Interface do Windows PowerShell para StorSimple no seu dispositivo para atualizar o serviço. Se esse problema persistir, contate o suporte da Microsoft. Depois de resolver o problema, desmarque esse alerta na página alertas.|

### <a name="support-package-alerts"></a>Suporte a pacote alertas

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Falha na criação do pacote de suporte.|StorSimple não pôde gerar o pacote.|Repita a operação. Se o problema persistir, contate o Microsoft Support. Depois que você tiver resolvido o problema, desmarque esse alerta na página alertas.|

### <a name="update-alerts"></a>Alertas de atualização

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Hotfix instalado.|Atualização de software/firmware foi concluída.|O hotfix foi instalado com êxito em seu dispositivo.|
|Atualizações manuais disponíveis.|Notificação de atualizações disponíveis.|Use a Interface do Windows PowerShell para StorSimple em seu dispositivo para instalá-las. <br>Para obter mais informações, vá para [atualizar seu dispositivo de série de 8000 StorSimple](storsimple-update-device.md).|
|Novas atualizações disponíveis.|Notificação de atualizações disponíveis.|Você pode instalar estas atualizações na página de **manutenção** ou usando a Interface do Windows PowerShell para StorSimple em seu dispositivo. <br>Para obter mais informações, vá para [atualizar seu dispositivo de série de 8000 StorSimple](storsimple-update-device.md).|
|Falha ao instalar atualizações.|Atualizações não foram instaladas com êxito.|Seu sistema não conseguiu instalar as atualizações. Você pode instalar estas atualizações na página de **manutenção** ou usando a Interface do Windows PowerShell para StorSimple em seu dispositivo. Se o problema persistir, contate o Microsoft Support. <br>Para obter mais informações, vá para [atualizar seu dispositivo de série de 8000 StorSimple](storsimple-update-device.md).|
|Não é possível verificar automaticamente se há novas atualizações.|Falha de verificação automática.|Você pode verificar manualmente novas atualizações na página de **manutenção** .|
|Novo agente WUA disponível.|Notificação de atualização disponível.|Baixar o Windows Update Agent mais recente e instale-o da interface do Windows PowerShell.|
|Versão do componente de firmware <*ID do componente*> não coincide com hardware.|Atualizações de firmware não foram instaladas com êxito.|Contate o suporte da Microsoft.|

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [erros de StorSimple e um dispositivo operacional de solução de problemas](storsimple-troubleshoot-operational-device.md).

