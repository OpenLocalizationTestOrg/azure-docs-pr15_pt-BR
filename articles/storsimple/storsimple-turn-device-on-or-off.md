<properties 
   pageTitle="Ativar ou desativar o seu dispositivo de StorSimple | Microsoft Azure"
   description="Explica como ativar um novo dispositivo StorSimple, ativar um dispositivo que foi encerrado ou perdido energia e desativar um dispositivo em execução."
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
   ms.date="08/23/2016"
   ms.author="alkohli" />

# <a name="turn-your-storsimple-device-on-or-off"></a>Ativar ou desativar o seu dispositivo de StorSimple 

## <a name="overview"></a>Visão geral

Desligar um dispositivo Microsoft Azure StorSimple não é necessário como parte da operação normal do sistema. No entanto, você talvez precise ativar um novo dispositivo ou um dispositivo que tinham que ser desligado. Geralmente, um desligamento é necessário em casos em que você deve substituir hardware com falha, física mover uma unidade ou colocar um dispositivo sair do serviço. Este tutorial descreve o procedimento necessário para ligar e desligar o seu dispositivo StorSimple em cenários diferentes.

A tabela a seguir lista vários cenários para ligar e desligar o seu dispositivo StorSimple e fornece links para os procedimentos apropriados.

|Cenário|Tópicos de referência|
|:-------|:---------------|
|Ativar um novo dispositivo|[Ativar um novo dispositivo](#turn-on-a-new-device)<ul><li>[Novo dispositivo com apenas gabinete principal](#new-device-with-primary-enclosure-only)</li><li>[Novo dispositivo com gabinete EBOD](#new-device-with-ebod-enclosure)</li></ul>|
|Ativar um dispositivo após desligamento|[Ativar um dispositivo após desligamento](#turn-on-a-device-after-shutdown)<ul><li>[Dispositivo com apenas circunscrição principal](#device-with-primary-enclosure-only)</li><li>[Dispositivo com compartimento EBOD](#device-with-ebod-enclosure)</li></ul>|
|Ativar um dispositivo após uma queda de energia|[Ativar um dispositivo após uma queda de energia](#turn-on-a-device-after-a-power-loss)<ul><li>[Dispositivo com apenas circunscrição principal](#8100)</li><li>[Dispositivo com compartimento EBOD](#8600)</li></ul>|
|Ativar um dispositivo após o compartimento primário e conexão EBOD será perdida|[Ativar um dispositivo após o principal e conexão de circunscrição EBOD será perdida](#turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost)|
|Desligar um dispositivo em execução|[Desativar um dispositivo em execução](#turn-off-a-running-device)<ul><li>[Dispositivo com apenas circunscrição principal](#8100a)</li><li>[Dispositivo com compartimento EBOD](#8600a)</li></ul>|

## <a name="turn-on-a-new-device"></a>Ativar um novo dispositivo

As etapas para ativar um dispositivo StorSimple pela primeira vez diferem dependendo se o dispositivo é um 8100 ou um modelo de 8600. O 8100 tem um único gabinete principal, enquanto o 8600 é um dispositivo de compartimento duplo com um gabinete principal e uma circunscrição EBOD. As etapas detalhadas para ambos os modelos são descritas nas seções a seguir.

- [Novo dispositivo com apenas gabinete principal](#new-device-with-primary-enclosure-only)

- [Novo dispositivo com gabinete EBOD](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Novo dispositivo com apenas gabinete principal

O modelo de StorSimple 8100 é um dispositivo de compartimento único. Seu dispositivo inclui redundantes Power e módulos refrigeração (PCMs). Ambos PCMs devem ser instalados e conectadas a fontes de energia diferente para garantir alta disponibilidade.

Execute as seguintes etapas para cabo seu dispositivo de alimentação.

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

>[AZURE.NOTE]Para configuração do dispositivo concluída e obter instruções de cabeamento, vá para [instalar o dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md). Certifique-se de que você siga as instruções exatamente.

### <a name="new-device-with-ebod-enclosure"></a>Novo dispositivo com gabinete EBOD

O modelo de StorSimple 8600 tem um compartimento principal e uma circunscrição EBOD. Isso requer que as unidades a ser conectados juntos para alimentação e conectividade SCSI Serial anexado (SAS).

Ao configurar esse dispositivo pela primeira vez, execute as etapas para SAS cabeamento primeiro e execute as etapas para cabeamento de alimentação.

[AZURE.INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

>[AZURE.NOTE]Para configuração do dispositivo concluída e obter instruções de cabeamento, vá para [instalar o dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md). Certifique-se de que você siga as instruções exatamente.

## <a name="turn-on-a-device-after-shutdown"></a>Ativar um dispositivo após desligamento

As etapas para ativar um dispositivo StorSimple depois que ela foi desligada são diferentes dependendo se o dispositivo é um 8100 ou um modelo de 8600. O 8100 tem um único gabinete principal, enquanto o 8600 é um dispositivo de compartimento duplo com um gabinete principal e uma circunscrição EBOD.

- [Dispositivo com apenas circunscrição principal](#device-with-primary-enclosure-only)

- [Dispositivo com compartimento EBOD](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Dispositivo com apenas circunscrição principal

Depois de um desligamento, use o procedimento a seguir para ativar um dispositivo de StorSimple com um compartimento primário e nenhum compartimento EBOD.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Para ativar um dispositivo com apenas um compartimento principal

1. Certifique-se de que a potência alterna em ambos os Power e módulos refrigeração (PCMs) estão na posição desativado. Se as opções não estiverem na posição OFF, invertê-las para a posição desativado e aguarde as luzes desativado.

2. Ative o dispositivo invertendo as opções de energia em ambos os PCMs para a posição ON. O dispositivo deve ativar.

3. Verifique o seguinte para verificar se o dispositivo está totalmente na:

    1. Okey nos dois módulos PCM estiverem verde.

    2. O status LED nos dois controladores são verde.

    3. O LED azul em um dos controladores está piscando, que indica que o controlador está ativo.

    Se qualquer uma das seguintes condições não forem atendidas, o dispositivo não está íntegro. Entre [em contato em suporte da Microsoft](storsimple-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Dispositivo com compartimento EBOD

Depois de um desligamento, use o procedimento a seguir para ativar um dispositivo de StorSimple com um gabinete principal e uma circunscrição EBOD. Execute cada etapa na sequência exatamente conforme descrito. Falha ao fazer isso pode resultar em perda de dados.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Para ativar um dispositivo com um principal e um gabinete EBOD

1. Certifique-se de que o compartimento EBOD está conectado ao compartimento principal. Para obter mais informações, consulte [instalar o dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

2. Certifique-se de que o Power e módulos refrigeração (PCMs) no EBOD e compartimentos primários estão na posição desativado. Se as opções não estiverem na posição OFF, invertê-las para a posição desativado e aguarde as luzes desativado.

3. Ative o compartimento EBOD primeiro invertendo as opções de energia em ambos os PCMs para a posição ON. O LED PCM devem ser verde. Um controlador EBOD verde LED nesta unidade indica que o compartimento EBOD está no.

4. Ative o compartimento primário invertendo as opções de energia em ambos os PCMs para a posição ON. Todo o sistema agora deve estar em.

5. Verifique se o LED SAS verde, que garante que a conexão entre o compartimento EBOD e o compartimento primário é bom.

## <a name="turn-on-a-device-after-a-power-loss"></a>Ativar um dispositivo após uma queda de energia

Uma queda de energia ou interrupção pode desligar um dispositivo StorSimple. Falta de energia pode acontecer em uma das fontes de alimentação ou ambas as fontes de alimentação. As etapas de recuperação são diferentes dependendo se o dispositivo é um modelo 8100 ou um 8600. O 8100 tem um único gabinete principal, enquanto o 8600 é um dispositivo de compartimento duplo com um gabinete principal e uma circunscrição EBOD. Esta seção descreve o procedimento de recuperação para cada cenário.

- [Dispositivo com apenas circunscrição principal](#8100)

- [Dispositivo com compartimento EBOD](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Dispositivo com apenas circunscrição principal<a name="8100">

O sistema pode continuar sua operação normal, se houver perda de energia para uma das suas fontes de alimentação. No entanto, para garantir alta disponibilidade do dispositivo, restaure power de alimentação assim que possível.

Se houver uma queda de energia ou queda de energia em ambas as fontes de alimentação, o sistema será desligado de forma ordenada e controlada. Quando a alimentação é restaurada, o sistema automaticamente ativará.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Dispositivo com compartimento EBOD<a name="8600">

#### <a name="power-loss-on-one-power-supply"></a>Perda de energia em um power fornecer

O sistema pode continuar sua operação normal, se houver perda de energia para uma das suas fontes de alimentação no compartimento principal ou compartimento EBOD. No entanto, para garantir alta disponibilidade do dispositivo, restaure power à fonte de alimentação assim que possível.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Perda de energia em ambas as fontes de alimentação no primário e compartimentos EBOD

Se houver uma queda de energia ou queda de energia em ambas as fontes de alimentação, o compartimento EBOD será desligado imediatamente e o compartimento primário será encerrado de maneira organizada e controlada. Quando a alimentação é restaurada, o dispositivo será iniciado automaticamente.

Se a potência for desligada manualmente, faça as seguintes etapas para restaurar a energia para o sistema.

1. Ative o compartimento EBOD.

2. Após o compartimento EBOD na, ative o compartimento principal.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Perda de energia em ambas as fontes de alimentação no compartimento EBOD

Quando você configura os cabos, você deve garantir que o EBOD nunca é sozinho conectada a uma PDU separada. Se a EBOD e compartimento primário falharem ao mesmo tempo, o sistema irá recuperar.

Se apenas o compartimento EBOD falha em ambas as fontes de alimentação, o sistema não irá recuperar automaticamente. Siga estas etapas para ativar o sistema e restaurá-lo para um estado de integridade:

1. Se o compartimento principal estiver ativado, desative Power e módulos refrigeração (PCMs).

2. Aguarde alguns minutos desligar o sistema.

3. Ative o compartimento EBOD.

4. Após o compartimento EBOD na, ative o compartimento principal.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Ativar um dispositivo após o principal e conexão de circunscrição EBOD será perdida

Se a conexão for perdida entre o controlador reserva e o controlador EBOD correspondente, o dispositivo continuará a funcionar. Se a conexão entre o controlador de ativos do sistema e o controlador EBOD correspondente for perdida, falha deverá ocorrer e o dispositivo deve continuar a funcionar normalmente.

Quando os cabos SCSI Serial anexado (SAS) são removidos ou a conexão entre o compartimento EBOD e o compartimento primário é desligada, o dispositivo deixarão de funcionar. Neste ponto, execute as seguintes etapas.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Para ativar o dispositivo após a conexão é perdida

1. Acesso a traseira do dispositivo.

2. Se a conexão de cabo SAS entre o compartimento EBOD e o compartimento primário for quebrado, lane todas as SAS LED no gabinete EBOD será desativado.

3. Desligue Power e módulos refrigeração (PCMs) no compartimento do EBOD e o compartimento principal.

4. Aguarde até que todas as luzes no verso do ambos os compartimentos desativar.

5. Reinsira os cabos SAS e certifique-se de que não há uma boa conexão entre o compartimento EBOD e o compartimento principal.

6. Ative o compartimento EBOD primeiro invertendo-se ambas as opções PCM para a posição ON.

7. Verifique se o compartimento EBOD é em verificando se o LED verde está ativado.

8. Ative o compartimento principal.

9. Verifique se o compartimento primário é em verificando o LED do controlador verde é ativada.

10. Verifique se a conexão de circunscrição EBOD com o compartimento primário é bom marcando que a lane SAS LED (quatro por controlador EBOD) são Diante todos os.

>[AZURE.IMPORTANT] Se os cabos SAS estão com defeito ou a conexão entre o compartimento EBOD e o compartimento primário for não é bom, quando você ativa o sistema, ela entrará no modo de recuperação. Entre [em contato em suporte da Microsoft](storsimple-contact-microsoft-support.md) se isso acontecer.

## <a name="turn-off-a-running-device"></a>Desativar um dispositivo em execução

Um dispositivo StorSimple em execução talvez precise ser desligado se ele está sendo movido, conduzido sair do serviço, ou tem um componente com defeito que precisa ser substituído. As etapas são diferentes dependendo se o dispositivo de StorSimple é um 8100 ou um modelo de 8600. O 8100 tem um único gabinete principal, enquanto o 8600 é um dispositivo de compartimento duplo com um gabinete principal e uma circunscrição EBOD. Esta seção detalha as etapas para desligar um dispositivo em execução.

- [Dispositivo com compartimento principal](#8100a)

- [Dispositivo com compartimento EBOD](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Dispositivo com compartimento principal<a name="8100a"> 

Para desligar o dispositivo de maneira organizada e controlada, você poderá fazer isso por meio do portal de clássico Azure ou por meio do Windows PowerShell para StorSimple. 

>[AZURE.IMPORTANT] Não desligar um dispositivo em execução usando o botão de energia no verso do dispositivo.
>
>Antes de desligar o dispositivo, certifique-se de que todos os componentes de dispositivo são íntegros. No portal do Azure clássico, navegue até **dispositivos** > **manutenção** > **Status de Hardware**e verifique se o status de todos os componentes é verde. Isso é verdadeiro somente para um sistema de saúde. Se o sistema está sendo desligado para substituir um componente não está funcionando corretamente, você verá um falha (vermelho) ou degradados status (amarelo) para o componente respectivo no **Status de Hardware**.

Depois de acessar o Windows PowerShell para StorSimple ou portal clássico do Azure, siga as etapas em [Desligar um dispositivo StorSimple](storsimple-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Dispositivo com compartimento EBOD<a name="8600a">

>[AZURE.IMPORTANT] Antes de desligar o compartimento principal e o compartimento EBOD, certifique-se de que todos os componentes de dispositivo são íntegros. No portal do Azure clássico, navegue até **dispositivos** > **manutenção** > **Status de Hardware**e verifique se todos os componentes são íntegros.

#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Para desligar um dispositivo em execução com compartimento EBOD

1. Siga todas as etapas listadas em [Desligar um dispositivo de StorSimple](storsimple-manage-device-controller.md#shut-down-a-storsimple-device) para o compartimento principal.

2. Após o compartimento primário está desligado, desliga o EBOD invertendo-se desativar as opções de energia e refrigeração módulo (PCM).

3. Para verificar a EBOD foi desligado, verifique se todas as luzes no verso do compartimento EBOD estão desativado.

>[AZURE.NOTE] Os cabos SAS que são usados para conectar o compartimento EBOD ao compartimento primário não devem ser removidos até depois que o sistema for desligado.

## <a name="next-steps"></a>Próximas etapas

[Contate o suporte do Microsoft](storsimple-contact-microsoft-support.md) se você encontrar problemas quando ligar ou desligar um dispositivo StorSimple.

