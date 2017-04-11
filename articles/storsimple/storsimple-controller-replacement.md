<properties 
   pageTitle="Substituir um controlador de dispositivo de StorSimple | Microsoft Azure"
   description="Explica como remover e substituir um ou ambos os módulos de controlador em seu dispositivo de StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Substituir um módulo de controlador em seu dispositivo de StorSimple

## <a name="overview"></a>Visão geral

Este tutorial explica como remover e substituir um ou ambos os módulos de controlador em um dispositivo de StorSimple. Ele também aborda a lógica subjacente para os cenários de substituição de controlador único e duplo.

>[AZURE.NOTE] Antes de executar uma substituição de controlador, recomendamos que você sempre atualizar seu firmware de controlador para a versão mais recente.
>
>Para evitar danos ao seu dispositivo StorSimple, não retirar o controlador até que o LED é exibidos como um destes procedimentos:
>
>- Todas as luzes estão desativado.
>- LED 3, ![ícone de seleção verde](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), e ![ícone de cruz vermelha](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) estão piscando, e LED 0 e 7 LED estão **ativado**.

A tabela a seguir mostra os cenários de substituição de controlador suportado.

|Maiusculas e minúsculas|Cenário de substituição|Procedimento aplicável|
|:---|:-------------------|:-------------------|
|1|Um controlador está em um estado de falha, o outro controlador está íntegra e ativa.|[Substituição de controlador único](#replace-a-single-controller), que descreve a [lógica substituto controlador único](#single-controller-replacement-logic), bem como as [etapas de substituição](#single-controller-replacement-steps).|
|2|Os dois controladores de falharam e requerem substituição. Gabinete, discos, and.disk circunscrição é íntegro.|[Substituição de controlador duplo](#replace-both-controllers), que descreve a [lógica substituto controlador duplo](#dual-controller-replacement-logic), bem como as [etapas de substituição](#dual-controller-replacement-steps). |
|3|Controladores do mesmo dispositivo ou de dispositivos diferentes são trocadas. O gabinete, discos e compartimentos de disco são íntegros.|Uma mensagem de alerta de incompatibilidade de slot aparecerão.|
|4|Um controlador está ausente e o outro controlador falha.|[Substituição de controlador duplo](#replace-both-controllers), que descreve a [lógica substituto controlador duplo](#dual-controller-replacement-logic), bem como as [etapas de substituição](#dual-controller-replacement-steps).|
|5|Um ou ambos os controladores tem falhado. Você não pode acessar o dispositivo por meio do console serial ou o Windows PowerShell remoto.|[Contate o suporte do Microsoft](storsimple-contact-microsoft-support.md) para um procedimento de substituição do controlador manual.|
|6|Controladores de tem uma versão de compilação diferente, que pode ser devido a:<ul><li>Controladores ter uma versão diferente do software.</li><li>Controladores tem uma versão de firmware diferente.</li></ul>|Se as versões de software do controlador forem diferentes, a lógica de substituição detecta que e atualiza a versão de software no controlador de substituição.<br><br>Se as versões de firmware do controlador são diferentes e a versão de firmware antiga é **não** automaticamente atualizável, uma mensagem de alerta aparecerá no portal de clássico do Azure. Você deve verificar se há atualizações e instale as atualizações de firmware.</br></br>Se as versões de firmware do controlador são diferentes e a versão antiga do firmware é atualizável automaticamente, a lógica de substituição de controlador detecta isso, e depois que o controlador for iniciado, o firmware será atualizado automaticamente.|

Você precisa remover um módulo de controlador se ele falha. Um ou ambos os módulos controlador podem falhar, que pode resultar em uma substituição de controlador único ou substituição de controlador duplo. Para obter procedimentos de substituição e a lógica, consulte o seguinte:

- [Substituir um único controlador](#replace-a-single-controller)
- [Substitua os dois controladores](#replace-both-controllers)
- [Remover um controlador](#remove-a-controller)
- [Inserir um controlador](#insert-a-controller)
- [Identifique o controlador ativo em seu dispositivo](#identify-the-active-controller-on-your-device)

>[AZURE.IMPORTANT] Antes de remover e substituir um controlador, examine as informações de segurança em [substituição de componentes de hardware StorSimple](storsimple-hardware-component-replacement.md).

## <a name="replace-a-single-controller"></a>Substituir um único controlador

Quando um dos dois controladores no dispositivo Microsoft Azure StorSimple falhou, está com defeito ou está faltando, você precisa substituir um único controlador. 

### <a name="single-controller-replacement-logic"></a>Lógica de substituição de controlador único

Em uma substituição de controlador único, primeiro você deve remover o controlador de falha. (O restante controlador no dispositivo é o active.) Quando você insere o controlador de substituição, ocorrem as seguintes ações:

1. Controlador de substituição iniciará imediatamente a comunicação com o dispositivo StorSimple.

2. Um instantâneo do disco rígido virtual (VHD) para o controlador de ativo é copiado no controlador de substituição.

3. O instantâneo é modificado para que quando o controlador de substituição inicia a partir este VHD, ele será reconhecido como um controlador de espera.

4. Quando as modificações forem concluídas, o controlador de substituição será iniciado como o controlador de espera.

5. Quando os dois controladores de estiver executando, cluster vem online.

### <a name="single-controller-replacement-steps"></a>Etapas de substituição de controlador único

Conclua as etapas a seguir se um dos controladores em seu dispositivo de Microsoft Azure StorSimple falhar. (O outro controlador deve ser ativo e em execução. Se ambos os controladores falharem ou mau funcionamento, vá para [as etapas de substituição de controlador duplo](#dual-controller-replacement-steps).)

>[AZURE.NOTE] Pode levar 30 a 45 minutos para que o controlador reiniciar e recuperar completamente o procedimento de substituição de controlador único. O tempo total para o procedimento inteiro, incluindo anexando os cabos, é de aproximadamente 2 horas.

#### <a name="to-remove-a-single-failed-controller-module"></a>Para remover um módulo de controlador de falha único

1. No portal do Azure clássico, vá para o serviço de StorSimple Manager, clique na guia **dispositivos** e clique no nome do dispositivo que você deseja monitorar.

2. Vá para **manutenção > Status de Hardware**. O status de controlador de 0 ou 1 de controlador deve ser vermelho, que indica uma falha.

    >[AZURE.NOTE] Controlador de falha no substituto controlador único sempre é um controlador de espera.

3. Use a Figura 1 e a tabela a seguir para localizar o módulo do controlador com falha.  

    ![Backplane de módulos de circunscrição principal de dispositivo](./media/storsimple-controller-replacement/IC740994.png)

    **Figura 1** Verso StorSimple dispositivo

  	|Rótulo|Descrição|
  	|:----|:----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|Controlador de 0|
  	|4|Controlador de 1|

4. No controlador de falha, remova todos os cabos de rede conectada as portas de dados. Se você estiver usando um modelo 8600, também remova os cabos SAS que conectem o controlador ao controlador de EBOD.

5. Siga as etapas em [Remover um controlador](#remove-a-controller) para remover o controlador de falha. 

6. Instale a substituição de fábrica no mesmo slot do qual o controlador de falha foi removido. Isso aciona a lógica de substituição de controlador único. Para obter mais informações, consulte [lógica de substituição de controlador único](#single-controller-replacement-logic).

7. Enquanto a lógica de substituição de controlador único progride no plano de fundo, reconecte os cabos. Tome cuidado para conectar todos os cabos exatamente da mesma maneira que eles foram conectados antes de substituição.

8. Depois de reinicia o controlador, verifique o **status do controlador** e o **status de Cluster** no portal de clássico do Azure para verificar que o controlador está de volta para um estado de integridade e está no modo de espera.

>[AZURE.NOTE] Se você estiver monitorando o dispositivo por meio do console serial, você poderá ver vários reiniciar enquanto o controlador está recuperando o procedimento de substituição. Quando o menu de console serial é apresentado, você sabe que a substituição é concluída. Caso o menu não aparecer dentro de duas horas de iniciar a substituição de controlador, entre [em contato com o suporte da Microsoft](storsimple-contact-microsoft-support.md).

## <a name="replace-both-controllers"></a>Substitua os dois controladores

Quando dois controladores no dispositivo Microsoft Azure StorSimple falharam, estão funcionando corretamente ou estão faltando, você precisa substituir dois controladores. 

### <a name="dual-controller-replacement-logic"></a>Lógica de substituição de controlador duplo

Em uma substituição de controlador duplo, você primeiro remove ambos os controladores falhas e insira substituições. Quando os controladores de dois substituição são inseridos, ocorrem as seguintes ações:

1. Controlador de substituição no slot 0 verifica o seguinte:
 
   1. Ele está usando versões atuais do firmware e do software?

   2. É uma parte do cluster?

   3. É o controlador de ponto em execução e é agrupada?
                            
    Se nenhuma dessas condições forem verdadeiras, o controlador procura o backup diário mais recente (localizado na **nonDOMstorage** na unidade S). O controlador copia o instantâneo mais recente do VHD do backup.

2. O controlador no slot 0 usa o instantâneo à imagem em si.

3. Enquanto isso, o controlador no slot 1 aguarda controlador 0 para concluir a imagem e inicie.

4. Depois que o controlador 0 é iniciado, controlador 1 detecta cluster criado por controlador de 0, que aciona a lógica de substituição de controlador único. Para obter mais informações, consulte [lógica de substituição de controlador único](#single-controller-replacement-logic).

5. Posteriormente, os dois controladores serão executado e o cluster ficará online.

>[AZURE.IMPORTANT] Após uma substituição de controlador duplo, após o dispositivo StorSimple está configurado, é essencial que você dar um manual de backup do dispositivo. Backups diários de configuração de dispositivo não são acionados até depois de 24 horas. Trabalhar com o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para fazer um manual de backup de seu dispositivo.

### <a name="dual-controller-replacement-steps"></a>Etapas de substituição de controlador duplo

Este fluxo de trabalho é necessário quando ambos os controladores de seu dispositivo de StorSimple do Microsoft Azure falha. Isso pode acontecer em um data center em que o sistema de refrigeração parou de funcionar, e como resultado, os dois controladores de falharem dentro de um curto período de tempo. Dependendo de se o dispositivo de StorSimple está ligado ou desativar e se você estiver usando um 8600 ou um modelo de 8100, um conjunto diferente de etapas é necessário.

>[AZURE.IMPORTANT] Pode levar 45 minutos para 1 hora para o controlador reiniciar e recuperar completamente um procedimento de substituição de controlador duplo. O tempo total para o procedimento inteiro, incluindo anexando os cabos, é aproximadamente 2,5 horas.

#### <a name="to-replace-both-controller-modules"></a>Substituir os dois módulos de controlador

1. Se o dispositivo está desativado, pule esta etapa e vá para a próxima etapa. Se o dispositivo está ativado, desative o dispositivo.
                                        
    1. Se você estiver usando um modelo de 8600, desativar o compartimento primário primeiro e desative o compartimento EBOD.

    2. Aguarde até que o dispositivo foi desligado completamente. Todos os LEDs na parte posterior do dispositivo será desativado.

2. Remova todos os cabos de rede que estão conectados às portas de dados. Se você estiver usando um modelo 8600, também remova os cabos SAS que se conectam o compartimento primário ao compartimento EBOD.

3. Remova ambos os controladores do dispositivo StorSimple. Para obter mais informações, consulte [Remover um controlador](#remove-a-controller).

4. Inserir a substituição de fábrica para controlador 0 primeiro e, em seguida, insira 1 de controlador. Para obter mais informações, consulte [Inserir um controlador](#insert-a-controller). Isso aciona a lógica de substituição de controlador duplo. Para obter mais informações, consulte [lógica de substituição de controlador duplo](#dual-controller-replacement-logic).

5. Enquanto a lógica de substituição de controlador progride no plano de fundo, reconecte os cabos. Tome cuidado para conectar todos os cabos exatamente da mesma maneira que eles foram conectados antes de substituição. Consulte as instruções detalhadas para seu modelo o cabo a seção de dispositivo de [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

6. Ative o dispositivo StorSimple. Se você estiver usando um modelo de 8600:

    1. Certifique-se de que o compartimento EBOD está ativado primeiro.

    2. Aguarde até que o compartimento EBOD está em execução.

    3. Ative o compartimento principal.

    4. Após o primeiro controlador de reinicia e está em um estado de integridade, o sistema serão executados.

    >[AZURE.NOTE] Se você estiver monitorando o dispositivo por meio do console serial, você poderá ver vários reiniciar enquanto o controlador está recuperando o procedimento de substituição. Quando o menu de console serial aparecer, você sabe que a substituição é concluída. Caso o menu não aparecer dentro de 2,5 horas de iniciar a substituição de controlador, entre [em contato com o suporte da Microsoft](storsimple-contact-microsoft-support.md).

## <a name="remove-a-controller"></a>Remover um controlador

Use o procedimento a seguir para remover um módulo de controlador com defeito do seu dispositivo de StorSimple.

>[AZURE.NOTE] As ilustrações a seguir são para controlador 0. Controlador de 1, esses seriam invertidas.

#### <a name="to-remove-a-controller-module"></a>Para remover um módulo de controlador

1. Segure a trava de módulo entre o polegar e o dedo indicador.

2. Aperte cuidadosamente o polegar e o dedo indicador juntos para liberar a trava de controlador.

    ![Liberar trava de controlador](./media/storsimple-controller-replacement/IC741047.png)

    **Figura 2** Liberar trava de controlador

2. Use a trava como um identificador para o controlador de fora do gabinete de slide.

    ![Controlador de deslizante sair do gabinete](./media/storsimple-controller-replacement/IC741048.png)

    **Figura 3** Deslizando o controlador fora do gabinete

## <a name="insert-a-controller"></a>Inserir um controlador

Use o procedimento a seguir para instalar um módulo de controlador fornecido de fábrica depois de remover um módulo com defeito do seu dispositivo de StorSimple.

#### <a name="to-install-a-controller-module"></a>Para instalar um módulo de controlador

1. Verificar se há danos aos conectores interface. Não instale o módulo se nenhum dos pinos do conector está danificado ou curvados.

2. Deslize o módulo do controlador no gabinete enquanto a trava seja totalmente lançada. 

    ![Deslizando o controlador no gabinete](./media/storsimple-controller-replacement/IC741053.png)

    **Figura 4** Deslizando o controlador no gabinete

3. Com o módulo do controlador inserido, comece fechando a trava enquanto continua a push o módulo do controlador no gabinete. A trava será ativado orientar o controlador no lugar.

    ![Trava do controlador de fechamento](./media/storsimple-controller-replacement/IC741054.png)

    **Figura 5** Fechando a trava de controlador

4. Terminar quando a trava se encaixa no lugar. O LED **Okey** agora deve estar em.  

    >[AZURE.NOTE] Pode levar até 5 minutos para que o controlador e o LED para ativar.

5. Para verificar que a substituição foi bem-sucedida, no portal de clássico do Azure, vá para **dispositivos** > **manutenção** > **Status de Hardware**e certifique-se de que o controlador 0 e controlador 1 são eficaz, (status é verde).

## <a name="identify-the-active-controller-on-your-device"></a>Identifique o controlador ativo em seu dispositivo

Há muitas situações, como substituição de registro ou controlador de dispositivo pela primeira vez, que exigem a localizar o controlador de ativo em um dispositivo de StorSimple. Controlador de ativos processa todas as operações de disco firmware e uma rede. Você pode usar qualquer um dos seguintes métodos para identificar o controlador de ativo:

- [Usar o portal de clássico Azure para identificar o controlador de ativo](#use-the-azure-classic-portal-to-identify-the-active-controller)

- [Usar o Windows PowerShell para StorSimple para identificar o controlador de ativo](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)

- [Verificar o dispositivo físico para identificar o controlador de ativo](#check-the-physical-device-to-identify-the-active-controller)

Cada um desses procedimentos é descrita a seguir.

### <a name="use-the-azure-classic-portal-to-identify-the-active-controller"></a>Usar o portal de clássico Azure para identificar o controlador de ativo

No portal do Azure clássico, navegue até **dispositivos** > **manutenção**e role até a seção **controladores** . Aqui você pode verificar qual controlador está ativo.

![Identificar controlador de ativo no Azure portal clássico](./media/storsimple-controller-replacement/IC752072.png)

**Figura 6** Azure portal clássico mostrando o controlador de ativo

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Usar o Windows PowerShell para StorSimple para identificar o controlador de ativo

Quando você acessa seu dispositivo por meio do console serial, uma mensagem de faixa é apresentada. A mensagem de faixa contém informações de dispositivo básicos como o modelo, nome, versão de software instalado e status do controlador que estiver acessando. A imagem a seguir mostra um exemplo de uma mensagem de faixa:

![Mensagem de faixa serial](./media/storsimple-controller-replacement/IC741098.png)

**Figura 7** Faixa controlador de mostrando de mensagem 0 como ativa

Você pode usar a mensagem de faixa para determinar se o controlador que estiver conectado à está ativo ou passivo.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Verificar o dispositivo físico para identificar o controlador de ativo

Para identificar o controlador ativo em seu dispositivo, localize o LED azul acima a porta de dados 5 no verso do compartimento principal.

Se este LED estiver piscando, o controlador está ativo e o outro controlador está no modo de espera. Use o diagrama e a tabela a seguir como um auxílio.

![Dispositivo primário circunscrição backplane com dataports](./media/storsimple-controller-replacement/IC741055.png)

**Figura 8** Verso compartimento primário com portas de dados e monitoramento LED

|Rótulo|Descrição|
|:----|:----------|
|1-6|DADOS 0 – 5 portas de rede|
|7|LED azul|


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [substituição de componentes de hardware StorSimple](storsimple-hardware-component-replacement.md).
