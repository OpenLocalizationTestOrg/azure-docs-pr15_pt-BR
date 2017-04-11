<properties 
   pageTitle="Substitua a bateria em um dispositivo de StorSimple | Microsoft Azure"
   description="Descreve como remover, substituir e manter o módulo de bateria de backup no seu dispositivo de StorSimple."
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

# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Substitua o módulo de bateria de backup no seu dispositivo de StorSimple

## <a name="overview"></a>Visão geral

A principal circunscrição Power e módulo refrigeração (PCM) em seu dispositivo Microsoft Azure StorSimple tem um pacote de bateria adicionais. Este pacote fornece energia para que o dispositivo StorSimple salvem dados se houver perda de energia CA para o compartimento principal. Este pacote de bateria é conhecido como o *módulo de bateria de backup*. O módulo de bateria de reserva existe somente para o compartimento primário em seu dispositivo de StorSimple (compartimento EBOD não contém um módulo de bateria reserva). 

Este tutorial explica como:

- Remova o módulo de bateria de backup 
- Instalar um novo módulo de bateria de backup
- Manter o módulo de bateria de backup

>[AZURE.IMPORTANT] Antes de remover e substituir um módulo de bateria de backup, examine as informações de segurança na [Introdução a substituição de componentes de hardware StorSimple](storsimple-hardware-component-replacement.md).

## <a name="remove-the-backup-battery-module"></a>Remova o módulo de bateria de backup

O módulo de bateria de reserva para o seu dispositivo StorSimple é uma unidade de substituição em campo. Antes que ele está instalado no PCM, o módulo de bateria deve ser armazenado em sua embalagem original. Execute as seguintes etapas para remover a bateria de backup.

#### <a name="to-remove-the-backup-battery-module"></a>Para remover o módulo de bateria de backup

1. No portal do Azure clássico, vá para **dispositivos** > **manutenção** > **Status de Hardware**. Em **Componentes compartilhados**, examine o status da bateria.

2. Identifique o PCM no qual a bateria falhou. Figura 1 mostra a traseira do dispositivo StorSimple.

    ![Backplane de dispositivo primário circunscrição módulos](./media/storsimple-battery-replacement/IC740994.png)

    **Figura 1** Volta do dispositivo primário mostrando módulos PCM e controlador

  	|Rótulo|Descrição|
  	|:----|:----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|Controlador de 0|
  	|4|Controlador de 1|

    Como mostrado pelo número 3 na Figura 2, o indicador de monitoramento LED no PCM 0 que corresponde à **Falha de bateria** deve ser acende.

    ![Backplane de LED do dispositivo PCM monitoramento indicador](./media/storsimple-battery-replacement/IC740992.png)

    **Figura 2** Verso PCM mostrando o indicador de monitoramento LED

  	|Rótulo|Descrição|
  	|:---|:-----------|
  	|1|Falha da alimentação AC|
  	|2|Falha do ventilador|
  	|3|Falha de bateria|
  	|4|PCM OKEY|
  	|5|Falha de energia DC|
  	|6|Bateria íntegra|

3. Para remover o PCM com uma bateria falhou, siga as etapas em [Remover um PCM](storsimple-power-cooling-module-replacement.md#remove-a-pcm).

4. Com o PCM removido, tire e gire a alça de módulo de bateria para cima, conforme indicado na figura a seguir e puxe-o para cima até remover a bateria.

    ![Removendo bateria de PCM](./media/storsimple-battery-replacement/IC741019.png)

    **Figura 3** Remover a bateria do PCM

5. Coloque o módulo na unidade de substituição em campo embalagem.

6. Retorne a unidade com defeito à Microsoft para manutenção e manipulação adequada.

## <a name="install-a-new-backup-battery-module"></a>Instalar um novo módulo de bateria de backup

Execute as seguintes etapas para instalar o módulo de bateria de substituição no PCM no compartimento primário do seu dispositivo de StorSimple.

#### <a name="to-install-the-battery-module"></a>Para instalar o módulo de bateria

1. Coloque o módulo de bateria de reserva na posição correta no PCM.

2. Pressione a alça do módulo de bateria totalmente para encaixar o conector.

3. Substitua o PCM no compartimento primário seguindo as diretrizes em [Substituir uma energia e refrigeração módulo em seu dispositivo de StorSimple](storsimple-power-cooling-module-replacement.md).

4. Após concluir a substituição, vá para **dispositivos** > **manutenção** > **Status de Hardware** no portal de clássico do Azure. Verifique o status da bateria para certificar-se de que a instalação foi bem-sucedida. Um status verde indica que a bateria está íntegra.

## <a name="maintain-the-backup-battery-module"></a>Manter o módulo de bateria de backup

No seu dispositivo de StorSimple, o módulo de bateria de backup fornece energia ao controlador durante um evento de perda de energia. Ele permite que o dispositivo StorSimple salvar dados críticos antes de desligar de uma maneira controlada. Com duas baterias totalmente carregadas nas PCMs, o sistema pode lidar com dois eventos de perda consecutivas.

No portal de clássico do Azure, o **Status de Hardware** na página **manutenção** indica se a bateria está com defeito ou o fim de vida está se aproximando. O status da bateria é indicado por **bateria em PCM 0** ou **bateria em PCM 1** em **Componentes compartilhados**. Esta página mostrará um estado **DEGRADADO** para se aproximando de fim de vida e **Falha** para o fim da vida atingido. 

>[AZURE.NOTE] A bateria pode relatar **Falha** quando ele simplesmente precisa ser carregada.
 
Se o estado de **DEGRADAÇÃO** aparecer, recomendamos o seguinte curso de ação:

- O sistema pode tiveram uma perda de energia recentes ou as pilhas podem ser em manutenção periódica. Observe o sistema de 12 horas antes de continuar.

    - Se o estado ainda **DEGRADADO** após 12 horas de conexão contínua para alimentação de CA com os controladores e PCMs em execução, a bateria precisa ser substituído. Faça a [contatar o suporte da Microsoft](storsimple-contact-microsoft-support.md) para um módulo de bateria reserva de substituição.

    - Se o estado se torna Okey depois de 12 horas, a bateria está funcionando e somente é necessário um encargo de manutenção.

- Se não houve uma perda de associados de alimentação e o PCM está ligado e conectado à energia CA, a bateria precisa ser substituído. [Contate o suporte do Microsoft](storsimple-contact-microsoft-support.md) para solicitar um módulo de bateria reserva de substituição.

>[AZURE.IMPORTANT] Descarte da bateria falha acordo com regulamentos nacionais e regionais. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [substituição de componentes de hardware StorSimple](storsimple-hardware-component-replacement.md).
