<properties 
   pageTitle="Substituir um PCM em seu dispositivo de StorSimple | Microsoft Azure"
   description="Explica como remover e substituir a potência e refrigeração módulo (PCM) em seu dispositivo de StorSimple"
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
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Substituir uma energia e refrigeração módulo em seu dispositivo de StorSimple

## <a name="overview"></a>Visão geral

A potência e o módulo refrigeração (PCM) em seu dispositivo de StorSimple do Microsoft Azure consiste em uma fonte de alimentação e ventiladores controlados pelo primário e compartimentos EBOD. Há apenas um modelo de PCM certificados para cada compartimento. Compartimento primário é certificado para um 764 PCM W e compartimento EBOD é certificado para um 580 PCM W. Embora os PCMs para o compartimento principal e o compartimento EBOD forem diferentes, o procedimento de substituição é idêntico.

Este tutorial explica como:

- Remover um PCM
- Instalar uma substituição PCM

>[AZURE.IMPORTANT] Antes de remover e substituir um PCM, examine as informações de segurança em [substituição de componentes de hardware StorSimple](storsimple-hardware-component-replacement.md).

## <a name="before-you-replace-a-pcm"></a>Antes de substituir um PCM

Lembre-se dos seguintes problemas importantes antes de substituir seu PCM:

- Se a fonte de alimentação do PCM falhar, deixe o módulo com defeito instalado, mas remova o cabo de alimentação. O ventilador continuará receber alimentação do compartimento e continuar a fornecer refrigeração adequada. Se o ventilador falhar, o PCM precisa ser substituídos imediatamente.

- Antes de remover o PCM, desconecte ao PCM desativando a opção principal (onde estiver presente) ou removendo física o cabo de alimentação. Isso fornece um aviso ao seu sistema que um desligamento de energia está prestes a ser.

- Certifique-se de que as outro PCM é funcional para operação de sistema contínuo antes de substituir o PCM com defeito. Um PCM com defeito deve ser substituído por um PCM totalmente operacional assim que possível.

- Substituição de módulo PCM leva apenas alguns minutos para ser concluída, mas ele deve ser concluído em 10 minutos de remover o PCM falha para evitar superaquecimento.

- Observe que substituição 764 PCM W módulos saem da fábrica não contêm o módulo de bateria de backup. Você precisará remover a bateria do seu PCM com defeito e, em seguida, inseri-lo no módulo da substituição antes de executar a substituição. Para obter mais informações, consulte como [Remover e inserir um módulo de bateria de backup](storsimple-battery-replacement.md).


## <a name="remove-a-pcm"></a>Remover um PCM

Siga estas instruções quando você estiver pronto para remover uma de energia e refrigeração módulo (PCM) de seu dispositivo de StorSimple do Microsoft Azure.

>[AZURE.NOTE] Antes de remover seu PCM, verifique se você tem uma substituição correta (764 W para o compartimento principal) ou 580 W para o compartimento EBOD.

#### <a name="to-remove-a-pcm"></a>Para remover um PCM

1. No portal do Azure clássico, clique em **dispositivos** > **manutenção** > **Status de Hardware**. Verificar o status dos componentes PCM em **Componentes compartilhados** para identificar quais PCM falhou:

     - Se uma fonte de alimentação no PCM 0 falhou, o status da **Fonte de alimentação no PCM 0** será vermelho.

     - Se uma fonte de alimentação em 1 PCM falhou, o status da **Fonte de alimentação em PCM 1** será vermelho.

     - Se o ventilador em 1 PCM falhou, o status de **refrigeração 0 para PCM 0** ou **1 refrigeração para PCM 0** será vermelho.

2. Localize o PCM falha no verso do compartimento principal. Se você estiver executando um modelo 8600, identifique o compartimento primário examinando o número de identificação de unidade do sistema mostrado no painel frontal monitor LED. O padrão que ID da unidade exibido no gabinete principal é **00**, enquanto o padrão que ID da unidade exibida no gabinete EBOD é **01**. O diagrama e a tabela a seguir explicam o painel frontal da exibição LED.

    ![ID do sistema no painel de operações frontal](./media/storsimple-power-cooling-module-replacement/IC740991.png)

     **Figura 1** Painel frontal do dispositivo  

  	|Rótulo|Descrição|
  	|:---|:-----------|
  	|1|Botão mudo|
  	|2|Alimentação do sistema|
  	|3|Falha do módulo|
  	|4|Falhas lógicas|
  	|5|Exibição de ID de unidade|

3. O indicador de monitoramento LED na parte posterior do compartimento primário também pode ser usado para identificar o PCM com defeito. Consulte o diagrama e a tabela para entender como usar o LED para localizar o PCM com defeito a seguir. Por exemplo, se o LED correspondente a **Falha de ventilador** é acende, o ventilador falhou. Da mesma forma, se o LED correspondente ao **Falhas de CA** estiver aceso, falha de alimentação. 

    ![Backplane de indicador de monitoramento de PCM LED do dispositivo](./media/storsimple-power-cooling-module-replacement/IC740992.png)

     **Figura 2** Verso PCM com indicador LED

  	|Rótulo|Descrição|
  	|:---|:-----------|
  	|1|Falha da alimentação AC|
  	|2|Falha do ventilador|
  	|3|Falha de bateria|
  	|4|PCM OKEY|
  	|5|Falha de energia DC|
  	|6|Bateria íntegra|

4. Consulte o diagrama a seguir da parte traseira do dispositivo StorSimple para localizar o módulo PCM falha. PCM 0 é à esquerda e 1 de PCM é à direita. A tabela a seguir explica os módulos.

     ![Backplane de módulos de circunscrição principal de dispositivo](./media/storsimple-power-cooling-module-replacement/IC740994.png)

     **Figura 3** Verso dispositivo com módulos plug-in 

  	|Rótulo|Descrição|
  	|:---|:-----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|Controlador de 0|
  	|4|Controlador de 1|

5. Desative o PCM com defeito e desconectar o cabo de fonte de alimentação. Agora você pode remover o PCM.

6. Segure a trava e o lado da alça PCM entre o polegar e o dedo indicador e, em seguida, aperte-las para abrir a alça.

    ![Alça de PCM de abertura](./media/storsimple-power-cooling-module-replacement/IC740995.png)

    **Figura 4** Abrindo a alça de PCM

7. Segure a alça e remover o PCM.

    ![Removendo dispositivo PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)

    **Figura 5** Removendo o PCM

## <a name="install-a-replacement-pcm"></a>Instalar uma substituição PCM

Siga estas instruções para instalar um PCM em seu dispositivo de StorSimple. Certifique-se de que você inseriu o módulo de bateria de backup antes de instalar a substituição PCM (aplica-se ao 764 PCMs W somente). Para obter mais informações, consulte como [Remover e inserir um módulo de bateria de backup](storsimple-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Para instalar um PCM

1. Verifique se você tem a substituição correta PCM para este compartimento. Compartimento primário precisa um 764 PCM W e compartimento EBOD precisa um 580 PCM W. Você não deve tentar usar o PCM W 580 no compartimento principal ou o PCM W 764 no compartimento EBOD. A imagem a seguir mostra onde identificar essas informações no rótulo é fixada o PCM.

    ![Rótulo PCM de dispositivo](./media/storsimple-power-cooling-module-replacement/IC740973.png)

    **Figura 6** Rótulo PCM

2. Verificar danos ao compartimento, prestar atenção especial para os conectores. 
                                        
    >[AZURE.NOTE] **Não instale o módulo se houver pinos de conector tortos.**

3. Com a alça PCM na posição aberta, deslize o módulo dentro do compartimento.

    ![Instalar o dispositivo PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)

    **Figura 7** Instalando o PCM

4. Feche a alça de PCM manualmente. Conforme a trava alça é ativado, você deverá ouvir um clique. 
                                        
    >[AZURE.NOTE] Para garantir que os pinos de conector tem consultado, você pode tug cuidadosamente sobre a alça sem soltar a trava. Se o PCM desliza, isso significa que a trava foi fechada antes dos conectores envolvido.

5. Conecte os cabos de alimentação para a fonte de energia e o PCM.

6. Proteger o esforço bales relevo. 

7. Ative o PCM.

8. Verificar se a substituição foi bem-sucedida: no portal clássico Azure do seu serviço de Gerenciador de StorSimple, navegue até **dispositivos** > **manutenção** > **Status de Hardware**. Em **Componentes compartilhados**, o status do PCM deve ser verde. 
                                        
    >[AZURE.NOTE] Pode levar alguns minutos para a substituição PCM inicializar completamente.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [substituição de componentes de hardware StorSimple](storsimple-hardware-component-replacement.md).
