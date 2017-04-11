<properties 
   pageTitle="Exibir e gerenciar alertas de Array Virtual StorSimple | Microsoft Azure"
   description="Descreve como usar o serviço do Gerenciador de StorSimple para gerenciar alertas e gravidade e condições de alerta de Array Virtual StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-alerts-for-the-storsimple-virtual-array"></a>Usar o serviço Gerenciador de StorSimple para exibir e gerenciar alertas para a matriz Virtual StorSimple

## <a name="overview"></a>Visão geral

Na guia **alertas** no serviço Gerenciador de StorSimple fornece uma maneira de rever e limpar alertas relacionados ao Array Virtual StorSimple em tempo real. Nesta guia, você pode monitorar centralmente os problemas de integridade de seu StorSimple Virtual matrizes (também conhecido como StorSimple local dispositivos virtuais) e a solução Microsoft Azure StorSimple geral.

Este tutorial descreve como configurar as notificações de alerta, condições comuns de alerta, níveis de gravidade de alerta e como exibir e controlar alertas. Além disso, ele inclui tabelas de alerta de referência rápida, que permitem localizar um alerta específico e responder adequadamente com rapidez.

![Página alertas](./media/storsimple-ova-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Configurar definições de alerta

Você pode escolher se deseja ser notificado por email de condições de alerta para cada um dos seus dispositivos de virtual StorSimple. Além disso, você pode identificar outros destinatários de notificação de alerta por meio de seus endereços de email na caixa **outros destinatários de email** , separada por ponto e vírgula.

>[AZURE.NOTE] Você pode inserir um máximo de 20 endereços de email por dispositivo virtual.

Depois de habilitar notificação por email para um dispositivo virtual, os membros da lista de notificação receberá uma mensagem de email cada vez que ocorre um alerta crítico. As mensagens serão enviadas por *storsimple-alerts-noreply@mail.windowsazure.com* e descreverá da condição de alerta. Destinatários podem clicar em **Cancelar assinatura** para remover da lista de notificação de email.

#### <a name="to-enable-email-notification-of-alerts-for-a-virtual-device"></a>Para ativar a notificação de email de alertas para um dispositivo virtual

1. Vá para **dispositivos** > **configuração** para o dispositivo virtual. Vá para a seção **configurações de alerta** .

    ![configurações de alerta](./media/storsimple-ova-manage-alerts/alerts2.png)

2. Em **configurações de alerta**, defina o seguinte:

    1. No campo **Enviar notificação por email** , selecione **Sim**.

    2. No campo **administradores de serviços de Email** , selecione **Sim** se desejar que o administrador do serviço e todos os administradores co recebem as notificações de alerta.

    3. No campo **outros destinatários de email** , insira os endereços de email de todos os outros destinatários que devem receber as notificações de alerta. Insira os nomes no formato *someone@somewhere.com*. Use ponto e vírgula para separar os endereços de email. Você pode configurar um máximo de 20 endereços de email por dispositivo virtual. 

        ![configuração de notificação de alertas](./media/storsimple-ova-manage-alerts/alerts3.png)

3. Na parte inferior da página, clique em **Salvar** para salvar sua configuração.

4. Para enviar uma notificação de email de teste, clique no ícone de seta ao lado de **Enviar email de teste**. O serviço do Gerenciador de StorSimple exibirá mensagens de status como ele encaminha a notificação de teste. 

5. Quando a seguinte mensagem for exibida, clique em **Okey**. 

    ![Teste de alertas de e-mail de notificação enviado](./media/storsimple-ova-manage-alerts/alerts4.png)

    >[AZURE.NOTE] Se a mensagem de notificação de teste não puder ser enviada, o serviço do Gerenciador de StorSimple exibirá uma mensagem apropriada. Clique **Okey**, aguarde alguns minutos e tente enviar sua mensagem de notificação de teste novamente. 

    A mensagem de notificação de teste será semelhante à seguinte.

    ![Exemplo de email de teste de alertas](./media/storsimple-ova-manage-alerts/alerts5.png)

## <a name="common-alert-conditions"></a>Condições de alerta comuns

Sua matriz Virtual StorSimple gera alertas em resposta a uma variedade de condições. A seguir está os tipos mais comuns de condições de alerta:

- **Problemas de conectividade** – esses alertas ocorrem quando há dificuldade em transferência de dados. Problemas de comunicação podem ocorrer durante a transferência de dados e da conta de armazenamento do Azure ou devido a falta de conectividade entre os dispositivos virtuais e o serviço do Gerenciador de StorSimple. Problemas de comunicação são alguns dos mais difíceis de corrigir porque há muitos pontos de falha. Você deve sempre verificar primeiro que a conectividade de rede e acesso à Internet estão disponíveis antes de continuar a solução de problemas mais avançados. Para obter informações sobre as configurações de firewall e portas, vá para [requisitos de sistema do Array Virtual StorSimple](storsimple-ova-system-requirements.md). Para obter ajuda com a solução de problemas, vá para [Solucionar problemas com o cmdlet de Conexão de teste](storsimple-troubleshoot-deployment.md).

- **Problemas de desempenho** – esses alertas são causados quando o sistema não estiver executando ideal, como quando ela está sob uma carga pesada.

Além disso, você talvez veja alertas relacionadas à segurança, atualizações ou falhas de trabalho.

## <a name="alert-severity-levels"></a>Níveis de gravidade de alerta

Alertas têm diferentes níveis de gravidade, dependendo do impacto que terá a situação alerta e a necessidade de uma resposta para o alerta. Os níveis de gravidade são:

- **Crítica** – este alerta é em resposta a uma condição que está afetando o desempenho bem-sucedida do seu sistema. Ação é necessária para garantir que o StorSimple serviço não é interrompido.

- **Aviso** – esta condição pode tornar-se crítica se não resolvido. Você deve investigar a situação e realizar qualquer ação necessária para limpar o problema.

- **Informações** – esse alerta contém informações que podem ser úteis para controlar e gerenciar seu sistema.

## <a name="view-and-track-alerts"></a>Exibir e controlar alertas

O painel de serviço do Gerenciador de StorSimple oferece uma olhada rápida no número de alertas em seus dispositivos virtuais, organizados por nível de gravidade.

![Painel de alertas](./media/storsimple-ova-manage-alerts/alerts6.png)

Clicar em nível de gravidade abre na guia **alertas** . Os resultados incluem somente os alertas que correspondem a esse nível de gravidade.

![Relatório de alertas com escopo de tipo de alerta](./media/storsimple-ova-manage-alerts/alerts7.png)

Clicando em um alerta na lista fornece detalhes adicionais para o alerta, incluindo a última vez em que o alerta foi relatado, o número de ocorrências do alerta sobre o dispositivo e a ação recomendada para resolver o alerta.

Você pode copiar os detalhes do alerta para um arquivo de texto se você precisar enviar as informações ao Microsoft Support. Depois de ter seguido a recomendação e resolvido o condição de alerta local, você deve desmarcar o alerta selecionando o alerta na guia **alertas** e clicando em **Limpar**. Para limpar vários alertas, selecione cada alerta, clique em qualquer coluna exceto a coluna de **alerta** e clique em **Limpar** depois que você tiver selecionado todos os alertas para ser limpos. Observe que alguns alertas automaticamente são removidos quando o problema for resolvido ou quando o sistema atualiza o alerta com novas informações.

Quando você clica em **Limpar**, você terá a oportunidade de fornecer comentários sobre o alerta e as etapas que você tirou para resolver o problema. 

![comentários de alerta](./media/storsimple-ova-manage-alerts/clear-alert.png)

Clique no ícone de seleção ![ícone de verificação](./media/storsimple-ova-manage-alerts/check-icon.png) Para salvar seus comentários.

Alguns eventos serão limpo pelo sistema se outro evento for disparado com novas informações. Nesse caso, você verá a seguinte mensagem.

![Mensagem de alerta limpar](./media/storsimple-ova-manage-alerts/alerts8.png)

## <a name="sort-and-review-alerts"></a>Alertas de classificação e revisão

Na guia **alertas** pode exibir até 250 alertas. Se você tiver excedido o número de alertas, nem todos os alertas serão exibidos no modo de exibição padrão. Você pode combinar os seguintes campos para personalizar quais alertas são exibidas:

- **Status** – você pode exibir os alertas de **ativo** ou **desmarcada** . Alertas ativos ainda estão sendo disparadas em seu sistema, enquanto alertas desmarcadas tenham sido limpas manualmente por um administrador ou limpas programaticamente porque o sistema atualizado da condição de alerta com novas informações.

- **Gravidade** – você pode exibir os alertas de todos os níveis de gravidade (críticas, avisos, informações) ou apenas uma determinada gravidade, como alertas somente críticos.

- **Origem** – você pode exibir alertas de todas as fontes ou limitar os alertas para aqueles que vêm de um ou todos os dispositivos virtuais ou o serviço.

- **Intervalo de tempo** – especificando as datas **de** e **para** e carimbos de hora, você pode examinar alertas durante o período de tempo que você está interessado.

## <a name="alerts-quick-reference"></a>Referência rápida de alertas

As tabelas a seguir listam alguns dos alertas Microsoft Azure StorSimple que podem ocorrer, bem como recomendações e informações adicionais onde estiver disponível. Alertas de dispositivo virtual StorSimple se encaixam em uma das categorias a seguir:

- [Alertas de conectividade de nuvem](#cloud-connectivity-alerts)

- [Alertas de configuração](#configuration-alerts)

- [Alertas de falha de trabalho](#job-failure-alerts)

- [Alertas de desempenho](#performance-alerts)

- [Alertas de segurança](#security-alerts)

- [Alertas de atualização](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividade de nuvem

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Dispositivo *<device name>* não está conectado na nuvem.|O dispositivo nomeado não consegue se conectar na nuvem. |Não foi possível conectar-se para a nuvem. Isso pode ser devido a um destes procedimentos:<ul><li>Pode haver um problema com as configurações de rede em seu dispositivo.</li><li>Pode haver um problema com as credenciais de conta de armazenamento.</li></ul>Para obter mais informações sobre solução de problemas de conectividade, vá para o [local da web a interface do usuário](storsimple-ova-web-ui-admin.md) do dispositivo.|


### <a name="configuration-alerts"></a>Alertas de configuração

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Configuração de dispositivo virtual local sem suporte.|Desempenho lento.|A configuração atual pode resultar em degradação de desempenho. Certifique-se de que seu servidor atende aos requisitos mínimos de configuração. Para obter mais informações, vá para [StorSimple Virtual requisitos de matriz](storsimple-ova-system-requirements.md). 
|Você está ficando sem espaço de disco configurado no <*nome do dispositivo*>.|Aviso de espaço de disco.|Há pouco espaço em disco provisionado. Para liberar espaço, considere movendo cargas de trabalho para outro volume ou compartilhamento ou a exclusão de dados.

### <a name="job-failure-alerts"></a>Alertas de falha de trabalho

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Fazer backup de <*nome do dispositivo*> não pôde ser concluída.|Falha de trabalho de backup.|Não foi possível criar um backup. Considere o seguinte:<ul><li>Problemas de conectividade podem estar impedindo a operação de backup seja concluída com êxito. Certifique-se de que não há nenhum problemas de conectividade. Para obter mais informações sobre solução de problemas de conectividade, vá para o [local da web a interface do usuário](storsimple-ova-web-ui-admin.md) para o seu dispositivo virtual.</li><li>Você atingiu o limite de armazenamento disponível. Para liberar espaço, considere a possibilidade de excluir qualquer backups que não são mais necessárias.</li></ul> Resolver os problemas, desmarque o alerta e repita a operação.|
|Restaurar do <*nome do dispositivo*> não pôde ser concluída.|Restaure falha de trabalho.|Não foi possível restaurar de backup. Considere o seguinte:<ul><li>Sua lista de backup pode não ser válida. Atualize a lista para verificar se que ele ainda estiver válido.</li><li>Problemas de conectividade podem estar impedindo a operação de restauração seja concluída com êxito. Certifique-se de que não há nenhum problemas de conectividade.</li><li>Você atingiu o limite de armazenamento disponível. Para liberar espaço, considere a possibilidade de excluir qualquer backups que não são mais necessárias.</li></ul>Resolver os problemas, desmarque o alerta e repita a operação de restauração.|
|Clonar do <*nome do dispositivo*> não pôde ser concluída.|Clone falha de trabalho.|Não foi possível criar um clonar. Considere o seguinte:<ul><li>Sua lista de backup pode não ser válida. Atualize a lista para verificar se que ele ainda estiver válido.</li><li>Problemas de conectividade podem estar impedindo a operação de clonagem seja concluída com êxito. Certifique-se de que não há nenhum problemas de conectividade.</li><li>Você atingiu o limite de armazenamento disponível. Para liberar espaço, considere a possibilidade de excluir qualquer backups que não são mais necessárias.</li></ul>Resolver os problemas, desmarque o alerta e repita a operação.|

### <a name="performance-alerts"></a>Alertas de desempenho

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Você está enfrentando atrasos inesperados na transferência de dados.|Transferência de dados lentos.|Limitação erros ocorrem quando você exceder os destinos de escalabilidade de um serviço de armazenamento. O serviço de armazenamento faz isso para garantir que nenhum cliente único ou inquilino pode usar o serviço ao custo de outras pessoas. Para obter mais informações sobre sua conta de armazenamento do Azure de solução de problemas, vá para o [Monitor, diagnosticar e solucionar problemas de armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).
|Você está executando baixa local reserva espaço em disco na <*nome do dispositivo*>.|Tempo de resposta lenta.|10% do total tamanho provisionado para <*nome do dispositivo*> é reservado no dispositivo local e você está executando baixa no espaço reservado. A carga de trabalho no <*nome do dispositivo*> está gerando uma taxa maior de rotatividade ou talvez tenha migrado recentemente uma grande quantidade de dados. Isso pode resultar em desempenho reduzido. Considere uma das seguintes ações para resolver esse problema:<ul><li>Aumente a largura de banda de nuvem para este dispositivo.</li><li>Reduzir ou mova cargas de trabalho para outro volume ou compartilhamento.</li></ul>

### <a name="security-alerts"></a>Alertas de segurança

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Senha para <*nome do dispositivo*> expirará em <*número*> dias.|Aviso de senha.| Sua senha expirará em < número < dias. Considere a possibilidade de alterar sua senha. Para obter mais informações, vá para [alterar a senha de administrador do dispositivo StorSimple Virtual matriz](storsimple-ova-change-device-admin-password.md).

### <a name="update-alerts"></a>Alertas de atualização

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Novas atualizações estão disponíveis para seu dispositivo.|Atualizações à matriz Virtual StorSimple estão disponíveis.|Você pode instalar novas atualizações na página de **manutenção** .|
|Não pôde examinar há novas atualizações no <*nome do dispositivo*>.|Falha de atualização. |Ocorreu um erro ao instalar novas atualizações. Você pode instalar manualmente as atualizações. Se o problema persistir, contate o [Suporte da Microsoft](storsimple-contact-microsoft-support.md).|


## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre a matriz Virtual StorSimple](storsimple-ova-overview.md).


