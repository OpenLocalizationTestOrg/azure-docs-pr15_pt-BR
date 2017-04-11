<properties 
   pageTitle="Gerenciar dispositivos com o Gerenciador de instantâneo StorSimple | Microsoft Azure"
   description="Descreve como usar o snap-in MMC Gerenciador de instantâneo StorSimple para se conectar e gerenciar dispositivos de StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Use o Gerenciador de instantâneo StorSimple para se conectar e gerenciar dispositivos de StorSimple

## <a name="overview"></a>Visão geral

Você pode usar nós no painel Gerenciador de instantâneo StorSimple **escopo** para verificar os dados importados de dispositivo de StorSimple e a atualização de dispositivos de armazenamento conectados. Além disso, quando você clica o nó **dispositivos** , você pode ver uma lista de dispositivos conectados e informações de status correspondente no painel de **resultados** .

![Dispositivos conectados](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Figura 1: Dispositivo conectado do Gerenciador de StorSimple instantâneo** 

Dependendo das suas seleções de **modo de exibição** , o painel de **resultados** mostra as seguintes informações sobre cada dispositivo. (Para obter mais informações sobre como configurar um modo de exibição, vá para o [menu Exibir](storsimple-use-snapshot-manager.md#view-menu).


| Coluna de resultados  |Descrição          |
|:----------------|:--------------------| 
| Nome            | O nome do dispositivo, conforme configurado no portal de clássico do Azure|
| Modelo           | O número do modelo do dispositivo|
| Versão         | A versão do software instalado no dispositivo |
| Status          | Se o dispositivo está disponível |
| Sincronizado pela última vez     | Data e hora quando o dispositivo foi sincronizada pela última vez |
| Não serial.      | O número de série para o dispositivo |
 
Se você o nó de **dispositivos** no painel de **escopo** de atalho, você pode selecionar as seguintes ações:

- Adicionar ou substituir um dispositivo 
- Conecte um dispositivo e verificar importações 
- Atualizar os dispositivos conectados 

Se você clicar o nó **dispositivos** e clique em um nome de dispositivo no painel de **resultados** , você pode selecionar as seguintes ações:

- Autenticar um dispositivo 
- Exibir detalhes do dispositivo 
- Atualizar um dispositivo 
- Excluir uma configuração de dispositivo 
- Alterar uma senha de dispositivo

>[AZURE.NOTE] Todas essas ações também estão disponíveis no painel de **ações** .
 
Este tutorial explica como usar o Gerenciador de instantâneo StorSimple para se conectar e gerenciar dispositivos e executar as seguintes tarefas:

- Adicionar ou substituir um dispositivo 
- Conecte um dispositivo e verificar importações 
- Atualizar os dispositivos conectados 
- Autenticar um dispositivo 
- Exibir detalhes do dispositivo 
- Atualizar um dispositivo individual 
- Excluir uma configuração de dispositivo 
- Alterar uma senha de dispositivo expirada
- Substituir um dispositivo com falha

>[AZURE.NOTE] Para obter informações gerais sobre como usar a interface do Gerenciador de instantâneo StorSimple, vá para a [interface de usuário do Gerenciador de instantâneo StorSimple](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Adicionar ou substituir um dispositivo

Use o procedimento a seguir para adicionar ou substituir um dispositivo StorSimple.

#### <a name="to-add-or-replace-a-device"></a>Para adicionar ou substituir um dispositivo

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , o nó de **dispositivos** de atalho e, em seguida, clique em **Configurar um dispositivo**. Caixa de diálogo **Configurar um dispositivo** é exibida.

    ![Configurar um dispositivo de StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 

3. Na caixa suspensa **dispositivo** , selecione o endereço IP do dispositivo ou do dispositivo virtual. 

4. Na caixa de texto **senha** , digite a senha do Gerenciador de instantâneo StorSimple que você criou para o dispositivo no portal de clássico do Azure. Clique em **Okey**. Gerenciador de instantâneo StorSimple procura o dispositivo que você identificou. 

    - Se o dispositivo estiver disponível, o Gerenciador de instantâneo StorSimple adiciona uma conexão. 

    - Se o dispositivo não estiver disponível por algum motivo, o Gerenciador de instantâneo StorSimple retornará uma mensagem de erro. Clique **Okey** para fechar a mensagem de erro e clique em **Cancelar** para fechar a caixa de diálogo **Configurar um dispositivo** .

## <a name="connect-a-device-and-verify-imports"></a>Conecte um dispositivo e verificar importações

Use o procedimento a seguir para conectar um dispositivo de StorSimple e confirmar que os grupos de volume existentes que associaram backups serão importados.

#### <a name="to-connect-a-device-and-verify-imports"></a>Conecte um dispositivo e verificar importa

1. Para conectar um dispositivo ao Gerenciador de instantâneo StorSimple, siga as instruções em Adicionar ou substituir um dispositivo. Quando ele se conecta a um dispositivo, Gerenciador de instantâneo StorSimple responde da seguinte maneira:

    - Se o dispositivo não estiver disponível por algum motivo, o Gerenciador de instantâneo StorSimple retornará uma mensagem de erro. 

   - Se o dispositivo estiver disponível, o Gerenciador de instantâneo StorSimple adiciona uma conexão. Quando você seleciona o dispositivo, ele aparece no painel de **resultados** e o campo status indica que o dispositivo está **disponível**. Gerenciador de instantâneo StorSimple importa quaisquer grupos de volume configurados para o dispositivo, desde que os grupos de volume associou backups. Políticas de backup não serão importadas. Grupos de volume que não têm backups associados não serão importados.

2. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

3. Clique com botão direito no nó superior no painel de **escopo** e clique em **Exibição de importações de alternância**.

    ![Selecione Alternar importações exibição](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 

4. A caixa de diálogo de **Alternância importações exibição** é exibida, mostrando o status dos grupos de volume importado e backups. Clique em **Okey**. 

Depois que os grupos de volume e backups estiverem importados com êxito, você pode usar o Gerenciador de instantâneo StorSimple para gerenciá-los, exatamente como você faria gerenciar grupos de volume e backups que você criou e configurou com o Gerenciador de instantâneo StorSimple. 

## <a name="refresh-connected-devices"></a>Atualizar os dispositivos conectados

Use o procedimento a seguir para sincronizar os dispositivos conectados de StorSimple com o Gerenciador de instantâneo de StorSimple.

####<a name="to-refresh-connected-devices"></a>Atualizar os dispositivos conectados

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , **dispositivos**de atalho e, em seguida, clique em **Atualizar dispositivos**. Sincroniza os dispositivos conectados com o Gerenciador de instantâneo StorSimple para que você possa exibir os grupos de volume e backups, incluindo quaisquer adições recentes. 

    ![Atualizar os dispositivos StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)
 
A ação de **Atualização de dispositivos** recupera qualquer novos grupos de volume e quaisquer backups associados de dispositivos conectados. Diferentemente da ação **Examinar volumes** disponível para o nó de **Volumes** , **Atualizar dispositivos** não restaurar o backup do registro.

## <a name="authenticate-a-device"></a>Autenticar um dispositivo

Use o procedimento a seguir para autenticar um dispositivo StorSimple com o Gerenciador de instantâneo StorSimple.

#### <a name="to-authenticate-a-device"></a>Para autenticar um dispositivo

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , clique em **dispositivos**.

3. No painel de **resultados** , clique com botão direito no nome do dispositivo e clique em **autenticar**.

4. A caixa de diálogo **autenticar** é exibida. Digite a senha do dispositivo e clique em **Okey**.

    ![Caixa de diálogo de autenticar](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 
 
## <a name="view-device-details"></a>Exibir detalhes do dispositivo

Use o procedimento a seguir para exibir os detalhes de um dispositivo de StorSimple e, se necessário, sincronize novamente o dispositivo com o Gerenciador de instantâneo StorSimple.

#### <a name="to-view-and-resynchronize-device-details"></a>Para exibir e sincronizar os detalhes do dispositivo

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , clique em **dispositivos**.

3. No painel de **resultados** , clique com botão direito no nome do dispositivo e clique em **detalhes**. 

4. a caixa de diálogo **Detalhes de dispositivo** é exibida. Esta caixa mostra o nome, modelo, versão, número de série, status, destino iSCSI nome qualificado (IQN) e a última data de sincronização e hora. 

   - Clique em **sincronizar novamente** para sincronizar o dispositivo.

   - Clique em **Okey** ou em **Cancelar** para fechar a caixa de diálogo.

    ![Detalhes do dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 
 
## <a name="refresh-an-individual-device"></a>Atualizar um dispositivo individual

Use o procedimento a seguir para sincronizar um dispositivo de StorSimple com o Gerenciador de instantâneo StorSimple individual.

#### <a name="to-refresh-a-device"></a>Para atualizar um dispositivo

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple. 

2. No painel de **escopo** , clique em **dispositivos**. 

3. No painel de **resultados** , clique com botão direito no nome do dispositivo e, em seguida, clique em **Atualizar o dispositivo**. Sincroniza o dispositivo com o Gerenciador de instantâneo StorSimple. 

## <a name="delete-a-device-configuration"></a>Excluir uma configuração de dispositivo

Use o procedimento a seguir para excluir uma configuração de dispositivo StorSimple individual em Gerenciador de instantâneo StorSimple.

#### <a name="to-delete-a-device-configuration"></a>Para excluir uma configuração de dispositivo

1. Clique no ícone da área de trabalho para iniciar o Gerenciador de instantâneo StorSimple. 

2. No painel de **escopo** , clique em **dispositivos**. 

3. No painel de **resultados** , clique com botão direito no nome do dispositivo e clique em **Excluir**. 

4. A seguinte mensagem será exibida. Clique em **Sim** para excluir a configuração ou clique em **não** para cancelar a exclusão.

    ![Excluir configuração de dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Alterar uma senha de dispositivo expirada

Você deve digitar uma senha para autenticar um dispositivo StorSimple com o Gerenciador de instantâneo StorSimple. Você configura essa senha quando você usa a interface do Windows PowerShell para configurar o dispositivo. No entanto, a senha pode expirar. Se isso acontecer, você pode usar o portal de clássico Azure para alterar a senha. Em seguida, porque o dispositivo foi configurado no Gerenciador de instantâneo StorSimple antes da senha expirado, você deve autenticar o dispositivo no Gerenciador de instantâneo StorSimple novamente. 

#### <a name="to-change-the-expired-password"></a>Para alterar a senha expirada

1. No portal do Azure clássico, inicie o serviço do Gerenciador de StorSimple.

2. Clique em **dispositivos** > **Configurar** para o dispositivo.

3. Role até a seção Gerenciador de instantâneo StorSimple. Digite uma senha 14-15 caracteres. Certifique-se de que a senha contém uma mistura de caracteres de maiusculas, minúsculas, numérico e especial.

4. Digite novamente a senha para confirmá-la.

5. Clique em **Salvar** na parte inferior da página.

#### <a name="to-re-authenticate-the-device"></a>Autenticar novamente o dispositivo

1. Inicie o Gerenciador de instantâneo StorSimple.

2. No painel de **escopo** , clique em **dispositivos**. Uma lista dos dispositivos configurados aparece no painel de **resultados** . 

3. Selecione o dispositivo, com o botão direito e clique em **autenticar**.

4. Na janela **autenticar** , insira a nova senha. 

5. Selecione o dispositivo, com o botão direito e selecione **o dispositivo de atualização**. Sincroniza o dispositivo com o Gerenciador de instantâneo StorSimple. 

## <a name="replace-a-failed-device"></a>Substituir um dispositivo com falha

Se um dispositivo de StorSimple falhar e é substituído por um dispositivo de espera (failover), use as etapas a seguir para conectar-se para o novo dispositivo e exibir os backups associados.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Para se conectar a um novo dispositivo após failover

1. Reconfigure a conexão iSCSI para o novo dispositivo. Para obter instruções, vá para "etapa 7: montagem, inicializar e formatar um volume" em [implantar seu dispositivo de StorSimple local](storsimple-deployment-walkthrough-u2.md). 

>[AZURE.NOTE] Se o novo dispositivo StorSimple tiver o mesmo endereço IP antigo, você pode ser capaz de se conectar a configuração antiga. 

2. Pare o serviço de gerenciamento de StorSimple da Microsoft:

    1. Inicie o Gerenciador de servidor.

    2. No painel Gerenciador do servidor, no menu **Ferramentas** , selecione **Serviços**. 

    3. Na janela **Serviços** , selecione o **Serviço de gerenciamento do Microsoft StorSimple**. 

    4. No painel direito, em **Serviço de gerenciamento do Microsoft StorSimple**, clique em **Parar o serviço**. 

3. Remova as informações de configuração relacionadas ao dispositivo antigo: 

    1. No Explorador de arquivos, navegue até C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    2. Exclua os arquivos na pasta BACatalog. 

4. Reinicie o serviço de gerenciamento de StorSimple da Microsoft: 

    1. No painel Gerenciador do servidor, no menu **Ferramentas** , selecione **Serviços**. 

    2. Na janela **Serviços** , selecione o **Serviço de gerenciamento do Microsoft StorSimple**. 

    3. No painel direito, em **Serviço de gerenciamento do Microsoft StorSimple**, clique em **reiniciar o serviço**. 

5. Inicie o Gerenciador de instantâneo StorSimple. 

6. Para configurar o novo dispositivo StorSimple, conclua as etapas na etapa 2: conectar um dispositivo StorSimple no [Gerenciador de instantâneo StorSimple implantar](storsimple-snapshot-manager-deployment.md). 

7. Clique com botão direito o nó de nível superior no painel de **escopo** (StorSimple Gerenciador de instantâneo no exemplo) e clique em **Exibição de importações de alternância**. 

8. Uma mensagem será exibida quando os grupos de volume importado e backups estão visíveis no Gerenciador de instantâneo StorSimple. Clique em **Okey**. 

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Gerenciador de instantâneo StorSimple administrar sua solução de StorSimple](storsimple-snapshot-manager-admin.md).
- Saiba como [usar o Gerenciador de instantâneo StorSimple para exibir e gerenciar volumes](storsimple-snapshot-manager-manage-volumes.md).

