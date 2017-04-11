<properties 
   pageTitle="Web Array Virtual StorSimple administração de interface do usuário | Microsoft Azure"
   description="Descreve como executar tarefas de administração de dispositivo básicos através da web de Array Virtual StorSimple interface do usuário."
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
   ms.workload="TBD"
   ms.date="04/07/2016"
   ms.author="alkohli" />

# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Usar a interface do usuário da Web para administrar sua matriz Virtual StorSimple

![fluxo de processo de configuração](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Visão geral

Os tutoriais neste artigo se aplicam ao Microsoft Azure StorSimple Virtual Array (também conhecido como o StorSimple local dispositivo virtual) executando o lançamento de disponibilidade geral (GA) de março de 2016. Este artigo descreve alguns dos fluxos de trabalho complexos e tarefas de gerenciamento que podem ser realizadas no Array Virtual StorSimple. Você pode gerenciar a matriz Virtual StorSimple usando o Gerenciador de StorSimple UI (referido como o portal de interface do usuário) e da web local da interface do usuário para o dispositivo de serviço. Este artigo aborda as tarefas que você pode realizar usando o interface do usuário da web.

Este artigo inclui os seguintes tutoriais:

- Obter a chave de criptografia de dados de serviço
- Solucionar problemas de erros de configuração de interface do usuário da web
- Gerar um pacote de log
- Desligar ou reiniciar seu dispositivo

## <a name="get-the-service-data-encryption-key"></a>Obter a chave de criptografia de dados de serviço

Uma chave de criptografia de dados de serviço é gerada quando você registrar seu primeiro dispositivo com o serviço Gerenciador de StorSimple. Esta é a chave depois necessárias com a chave do registro de serviço para registrar dispositivos adicionais com o serviço Gerenciador de StorSimple.

Se você tiver tiver perdido sua chave de criptografia de dados de serviço e a necessidade de recuperá-la, faça o seguinte etapas na web local da interface do usuário do dispositivo registrados com o seu serviço.

#### <a name="to-get-the-service-data-encryption-key"></a>Para obter a chave de criptografia de dados de serviço

1. Conecte a web local da interface do usuário. Acesse a **configuração** > **configurações de nuvem**.
  

2. Na parte inferior da página, clique em **obter chave de criptografia de dados de serviço**. Uma chave será exibida. Copie e salve esta tecla.
    
    ![obter a chave de criptografia de dados do serviço 1](./media/storsimple-ova-web-ui-admin/image27.png)
   


## <a name="troubleshoot-web-ui-setup-errors"></a>Solucionar problemas de erros de configuração de interface do usuário da web

Em alguns casos quando você configura o dispositivo por meio da web local UI, você poderá se deparar com erros. Para diagnosticar e solucionar esses erros, você pode executar os testes de diagnóstico.

#### <a name="to-run-the-diagnostic-tests"></a>Execute os testes de diagnóstico

1. Na web local UI, vá para **solução de problemas** > **testes de diagnóstico**.

    ![Execute o diagnóstico 1](./media/storsimple-ova-web-ui-admin/image29.png)

2. Na parte inferior da página, clique em **Executar testes de diagnóstico**. Isso iniciará testes para diagnosticar possíveis problemas com a rede, o dispositivo, o proxy da web, hora ou configurações de nuvem. Você será notificado de que o dispositivo está executando testes.

3. Depois de concluir os testes, os resultados serão exibidos. O exemplo a seguir mostra os resultados dos testes de diagnóstico. Observe que as configurações de proxy da web não foram configuradas neste dispositivo e, portanto, o teste de proxy da web não foi executado. Todos os outros testes de configurações de rede, servidor DNS e configurações de tempo foram bem-sucedido.

    ![Execute o diagnóstico 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Gerar um pacote de log

Um pacote de log é composto de todos os logs relevantes que podem ajudar a Microsoft Support para solucionar qualquer problema de dispositivo. Nesta versão, um pacote de log pode ser gerado por meio da interface do usuário da web local.

#### <a name="to-generate-the-log-package"></a>Para gerar o pacote de log

1. Na web local UI, vá para **solução de problemas** > **logs do sistema**.

    ![Gerar o pacote de log 1](./media/storsimple-ova-web-ui-admin/image31.png)

2. Na parte inferior da página, clique em **Criar pacote de log**. Um pacote de logs do sistema será criado. Isso levará alguns minutos.

    ![Gerar o pacote de log 2](./media/storsimple-ova-web-ui-admin/image32.png)

    Você será notificado após o pacote é criado com êxito e a página será atualizada para indicar a hora e a data em que o pacote foi criado.

    ![Gerar pacote de log 3](./media/storsimple-ova-web-ui-admin/image33.png)

3. Clique em **baixar o pacote de log**. Um pacote compactado será baixado no seu sistema.

    ![Gerar pacote de log 4](./media/storsimple-ova-web-ui-admin/image34.png)

4. Você pode descompactar o pacote de log baixado e exibir os arquivos de log do sistema.

## <a name="shut-down-and-restart-your-device"></a>Desligue e reinicie seu dispositivo

Você pode desligar ou reiniciar seu dispositivo virtual usando o interface do usuário de web local. Nós recomendamos que antes de reiniciar, faça os volumes ou compartilhamentos offline no host e, em seguida, no dispositivo. Isso minimiza a possibilidade de corrupção de dados. 

#### <a name="to-shut-down-your-virtual-device"></a>Para desligar seu dispositivo virtual

1. Na web local UI, vá para **manutenção** > **configurações de energia**.

2. Na parte inferior da página, clique em **Desligar**.

    ![desligamento de dispositivo 1](./media/storsimple-ova-web-ui-admin/image36.png)

3. Aparecerá um aviso informando que um desligamento do dispositivo interromperá qualquer AE que estavam em andamento, resultando em um tempo de inatividade. Clique no ícone de seleção ![Verifique o ícone](./media/storsimple-ova-web-ui-admin/image3.png).

    ![Aviso de desligamento de dispositivo](./media/storsimple-ova-web-ui-admin/image37.png)

    Você será notificado de que o desligamento foi iniciado.

    ![desligamento de dispositivo de Introdução](./media/storsimple-ova-web-ui-admin/image38.png)

    O dispositivo será encerrado agora. Se você quiser iniciar seu dispositivo, você precisará fazer isso por meio do Gerenciador de Hyper-V.

#### <a name="to-restart-your-virtual-device"></a>Para reiniciar o dispositivo virtual

1. Na web local UI, vá para **manutenção** > **configurações de energia**.

2. Na parte inferior da página, clique em **Reiniciar**.

    ![reinicialização do dispositivo](./media/storsimple-ova-web-ui-admin/image36.png)

3. Aparecerá um aviso informando que a reinicialização do dispositivo interromperá qualquer IOs que estavam em andamento, resultando em um tempo de inatividade. Clique no ícone de seleção ![Verifique o ícone](./media/storsimple-ova-web-ui-admin/image3.png).

    ![Reinicie o aviso](./media/storsimple-ova-web-ui-admin/image37.png)

    Você será notificado de que a reinicialização foi iniciada.

    ![reiniciar iniciada](./media/storsimple-ova-web-ui-admin/image39.png)

    Enquanto a reinicialização estiver em andamento, você perderá a conexão para a interface do usuário. Você pode monitorar a reinicialização atualizando a interface do usuário periodicamente. Como alternativa, você pode monitorar o status de reinicialização do dispositivo por meio do Gerenciador de Hyper-V.

## <a name="next-steps"></a>Próximas etapas

Saiba como [usar o serviço de Gerenciador de StorSimple para gerenciar seu dispositivo](storsimple-manager-service-administration.md).
