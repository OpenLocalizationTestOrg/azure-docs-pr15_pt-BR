<properties
   pageTitle="Instalar o dispositivo StorSimple 8100 | Microsoft Azure"
   description="Descreve como descompactar, montagem em rack e cabo seu dispositivo StorSimple 8100 antes de implantar e configurar o software."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Descompactar, para rack e o cabo seu dispositivo StorSimple 8100

## <a name="overview"></a>Visão geral

Seu 8100 do Microsoft Azure StorSimple é um único gabinete montado em rack dispositivo. Este tutorial explica como descompactar, para rack e cabo a 8100 StorSimple dispositivo de hardware antes de configurar e implantar o dispositivo StorSimple.

## <a name="unpack-your-storsimple-8100-device"></a>Descompactar seu dispositivo StorSimple 8100

As etapas a seguir fornecem instruções claras e detalhadas sobre como descompactar seu dispositivo de armazenamento de StorSimple 8100. Este dispositivo é fornecido em uma única caixa.

### <a name="prepare-to-unpack-your-device"></a>Preparar para descompactar seu dispositivo

Antes de você descompactar seu dispositivo, examine as informações a seguir.

![Ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone de peso pesado](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **Aviso!**

1. Certifique-se de que você tenha duas pessoas disponíveis para gerenciar a espessura do compartimento, se você estiver tratando-lo manualmente. Um gabinete completamente configurado pode ponderar até 32 kg (libras 70.).
1. Posicione a caixa em uma superfície plana nível.

Em seguida, conclua as etapas a seguir para descompactar seu dispositivo.

#### <a name="to-unpack-your-device"></a>Descompactar seu dispositivo

1. Inspecione a caixa e espuma embalagem para crushes, cortes, danos de água ou quaisquer outros danos óbvios. Se a caixa ou embalagem estiver seriamente danificada, não abra a caixa. Faça a [contatar o suporte da Microsoft](storsimple-contact-microsoft-support.md) para ajudá-lo a avaliar se o dispositivo está em bom estado de funcionamento.

2. Descompacte a caixa. A imagem a seguir mostra o modo de exibição descompactado do seu dispositivo de StorSimple.

     ![Descompactar seu dispositivo de armazenamento](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)

    **Modo de exibição descompactado de seu dispositivo de armazenamento**

     Rótulo | Descrição
     ----- | -------------
     1     | Caixa de remessa
     2     | Espuma inferior
     3     | Dispositivo
     4     | Espuma superior
     5     | Caixa de acessório


3. Após descompactar a caixa, certifique-se de que você tenha:

   - 1 dispositivo de compartimento único
   - 2 cabos de alimentação
   - 1 cruzado cabo Ethernet
   - 2 cabos de console serial
   - 1 conversor de série-USB para acesso serial
   - 1 à prova T10 chave de fenda
   - 4 QSFP-para-SFP + adaptadores para uso com 10 interfaces de rede GbE
   - 1 para rack kit (2 trilhos lado com montagem)
   - Obtendo a documentação de Introdução

    Se você não recebeu qualquer um dos itens listados acima, [contate o suporte da Microsoft](storsimple-contact-microsoft-support.md).

A próxima etapa é seu dispositivo para rack.

## <a name="rack-mount-your-storsimple-8100-device"></a>Seu dispositivo StorSimple 8100 para rack

Siga as próximas etapas para instalar o seu dispositivo de armazenamento de StorSimple 8100 em um rack padrão de 19 polegadas com frontal e traseiras postagens. O dispositivo StorSimple 8100 tem um único gabinete principal.

A instalação consiste em várias etapas, cada uma das quais é discutida nos procedimentos a seguir.

> [AZURE.IMPORTANT]
Dispositivos de StorSimple devem ser montado em rack para a operação correta.

### <a name="prepare-the-site"></a>Preparar o site

O dispositivo deve ser instalado em um rack de 19 polegadas padrão que tem frontal e traseiras postagens. Use o procedimento a seguir para se preparar para a instalação do rack.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Preparar o site para instalação de rack

1. Certifique-se de que o dispositivo dependem com segurança um trabalho plano, estável e nível superfície (ou semelhante).

2. Verifique se o site onde você pretende configurar possui alternada padrão de uma fonte independente ou uma unidade de distribuição de energia (PDU) rack com no-break (UPS).

3. Certifique-se de que um slot 2 unidades está disponível no rack na qual você pretende montar o dispositivo.

![Ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone de peso pesado](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **Aviso!**

Certifique-se de que você tenha duas pessoas disponíveis para gerenciar o peso se você estiver tratando a instalação do dispositivo manualmente. Um gabinete completamente configurado pode ponderar até 32 kg (libras 70.).

### <a name="rack-prerequisites"></a>Pré-requisitos de rack

Compartimento 8100 destina-se a instalação em um rack padrão de 19 polegadas gabinete com:

- Profundidade mínima de 27.84 polegadas de rack postagem a postagem.
- Peso máximo de 32 kg para o dispositivo
- Pressão traseira máxima de 5 Pascal (medidor de água 0,5 mm).

### <a name="rack-mounting-rail-kit"></a>Kit de rail montagem de rack

Um conjunto de trilhos de montagem é fornecido para uso com o gabinete de rack de 19 polegadas. Trilhos foram testados para lidar com a espessura de circunscrição máximo. Estas rails também permitirá que a instalação de vários compartimentos sem qualquer perda de espaço no rack.


#### <a name="to-install-the-device-on-the-rails"></a>Para instalar o dispositivo nos trilhos

2. Execute esta etapa somente se rails internas não estão instalados no seu dispositivo. Normalmente, os trilhos internos são instalados na fábrica. Se rails não estiverem instalados, em seguida, instale os slides rail esquerda e direita para os lados do gabinete circunscrição. Eles anexar usando seis parafusos métricos em cada lado. Para ajudar com orientação, os slides rail são marcados **LH – frente** e **RH – frente**e final que é fixado em direção à parte traseira do compartimento tem um end cônico.<br/>

    ![Anexar slides rail gabinete de circunscrição](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
    **Anexar slides de rail interna para os lados do compartimento**

       Rótulo | Descrição
       ----- | -----------
       1     | M 3 x4 parafusos de cabeça botão
       2     | Slides do gabinete

3. Anexe a rail esquerda externa e montagens de à direita externa para os membros vertical gabinete de rack. Os colchetes são marcados **LH**, **RH**e **Este lado para cima** para orientá-lo a orientação correta.

4. Localize os pinos rail na parte frontal e traseira do assembly rail. Estenda o rail para ajustá-lo entre as postagens de rack e insira os pinos as frontal e traseira rack postagem membro vertical furos. Certifique-se de que o conjunto de rail é nível.

5. Use dois dos parafusos métricos fornecidos para proteger o conjunto rail o rack membros verticais. Use um parafusos na frente e outra na parte traseira.

6. Repita essas etapas para o outro assembly rail.<br/>

     ![Anexar slides rail gabinete de rack](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Anexar conjuntos rail externa no rack**

     Rótulo | Descrição
     ----- | -----------
     1     | Clamping parafusos
     2     | Parafusos de postagem de rack frontal furos quadrado
     3     | Esquerda rail local frontal pinos
     4     | Clamping parafusos
     5     | Esquerda rail local traseira pinos


### <a name="mounting-the-device-in-the-rack"></a>Montar o dispositivo no rack

Usando os trilhos de rack que acabou de ser instalar, execute as seguintes etapas para montar o dispositivo no rack.

#### <a name="to-mount-the-device"></a>Para montar o dispositivo

1. Com um assistente, tire o compartimento e alinhá-lo com os trilhos de rack.

2. Insira cuidadosamente o dispositivo em trilhos e, em seguida, envio por push do dispositivo completamente no rack gabinete.<br/>

    ![Inserção de dispositivo no rack](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Montar o dispositivo no rack**


3. Remova os caps de borda esquerda e direita frontal, puxe os caps gratuitos. Simplesmente ajustam os caps de borda para as bordas.

5. Proteger o compartimento no rack instalando um parafuso Phillips fornecido por meio de cada borda, esquerda e direita.

4. Instale os caps de borda pressionando-os para a posição e encaixe-las no lugar.<br/>

     ![Instalando caps de borda](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)

    **Instalar os caps de borda**

     Rótulo | Descrição
     ----- | -----------
     1     | Parafusos de fixação do compartimento

A próxima etapa é cabo seu dispositivo para power, rede e acesso serial.

## <a name="cable-your-storsimple-8100-device"></a>Conecte seu dispositivo StorSimple 8100

Os procedimentos a seguir explicam como cabo seu dispositivo StorSimple 8100 para power, rede e conexões seriais.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar o cabeamento do seu dispositivo, você precisará:

- Seu dispositivo de armazenamento, completamente descompactados e rack montado.

- 2 cabos de alimentação que acompanham seu dispositivo

- Acesso a 2 unidades de distribuição de energia (recomendado).

- Cabos de rede

- Fornecido cabos serial

- Conversor USB serial com o driver apropriado instalado em seu computador (se necessário)

- Fornecido QSFP 4-para-SFP + adaptadores para uso com 10 interfaces de rede GbE

- [Com o suporte de hardware para as interfaces de rede GbE 10 em seu dispositivo de StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)


### <a name="power-cabling"></a>Cabeamento de energia

Seu dispositivo inclui redundantes Power e módulos refrigeração (PCMs). Ambos PCMs devem ser instalados e conectadas a fontes de energia diferente para garantir alta disponibilidade.

Execute as seguintes etapas para cabo seu dispositivo de alimentação.

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Cabeamento de rede

Seu dispositivo é uma configuração de espera ativo: a qualquer momento, um módulo de controlador está ativo e todas as operações de disco e de rede durante o outro módulo de controlador de processamento está em espera. Se um controlador falhar, o controlador reserva será ativado imediatamente e continua todas as operações de rede e disco.

Para oferecer suporte a esse failover controlador redundantes, você precisa cabo sua rede do dispositivo, conforme descrito nas etapas a seguir.

#### <a name="to-cable-for-network-connection"></a>Cabo para conexão de rede

1. Seu dispositivo tem seis interfaces de rede em cada controlador: quatro 1 Gbps, e duas 10 Gbps Ethernet portas. Identifique as várias portas de dados no backplane do seu dispositivo.

    ![Backplane do dispositivo 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Volta do dispositivo mostrando portas de dados**

     Rótulo   | Descrição
     ------- | -----------
     0,1,4,5 |  Interfaces de rede GbE 1
     2,3     | Interfaces de rede 10 GbE
     6       | Portas seriais

2. Consulte o diagrama a seguir para cabeamento de rede. (A configuração de rede mínima é mostrada por linhas azuis sólidas. Configuração adicional necessária para o desempenho e alta disponibilidade é mostrada por linhas pontilhadas.)


    ![Cabo seu dispositivo de 2 unidades de rede](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Cabeamento para seu dispositivo de rede**


  	|Rótulo | Descrição |
  	|----- | ----------- |
  	| R    | LAN com acesso à Internet |
  	| B    | Controlador de 0 |
  	| C    | PCM 0 |
  	| D    | Controlador de 1 |
  	| E    | PCM 1 |
  	| F, G | Hosts |
  	| 0-5  | Interfaces de rede |



Quando o dispositivo de cabeamento, requer configuração mínima:


- Pelo menos duas interfaces de rede conectadas em cada controlador com um para acesso à nuvem e outro para iSCSI. Os dados 0 porta automaticamente está habilitada e configurada por meio do console serial do dispositivo. Além de dados 0, outra porta de dados também precisa ser configurada por meio do portal de clássico Azure. Nesse caso, conectar dados 0 porta à LAN primária (rede com acesso à Internet). As outras portas de dados podem ser conectadas ao segmento de SAN/iSCSI VLAN (LAN) da rede, dependendo da função pretendida.

- Interfaces idênticas em cada controlador conectado à mesma rede para assegurar a disponibilidade se houver uma falha de controlador. Por exemplo, se você optar por conectar dados 0 e 3 de dados para um dos controladores, você precisa se conectar os dados correspondentes 0 e dados 3 no outro controlador.

Lembre-se de alta disponibilidade e desempenho:


- Quando possível, configure um par de interface de rede para acesso à nuvem (1 GbE) e outro par para iSCSI (10 GbE recomendado) em cada controlador.

- Quando possível, conecte interfaces de rede de cada controlador a duas opções diferentes para assegurar a disponibilidade contra uma falha de alternar. A figura ilustra as duas 10 GbE interfaces de rede, dados 2 e 3 de dados, de cada controlador conectado a duas opções diferentes.

Para obter mais informações, consulte as **interfaces de rede** sob os [requisitos de alta disponibilidade para o seu dispositivo StorSimple](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

>[AZURE.NOTE] Se você estiver usando transceptores SFP + com suas interfaces de rede 10 GbE, use o QSFP fornecido-SFP + adaptadores. Para obter mais informações, vá para [hardware com suporte as 10 GbE para interfaces de rede em seu dispositivo de StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).



### <a name="serial-port-cabling"></a>Cabeamento de porta serial

Execute as seguintes etapas para cabo porta serial.

#### <a name="to-cable-for-serial-connection"></a>Cabo para conexão serial

1. Seu dispositivo tem uma porta serial em cada controlador que é identificado por um ícone de chave inglesa. Consulte a ilustração na seção [cabeamento de rede](#network-cabling) para localizar as portas seriais no backplane do seu dispositivo.

2. Identifique o controlador ativo no backplane seu dispositivo. Um LED azul piscante indica que o controlador está ativo.

3. Use os cabos seriais fornecidos (se necessário, o conversor de USB serial para seu laptop) e conectar seu console ou computador (com emulação de terminal no dispositivo) à porta serial do controlador de ativos.

4. Instale os drivers de USB de série (fornecidos com o dispositivo) em seu computador.

5. Configurar a conexão serial da seguinte maneira: transmissão 115.200, 8 bits de dados, 1 bit de parada, sem paridade e controle de fluxo definido como nenhum.

6. Verifique se a conexão está funcionando pressionando Enter no console. Um menu de console serial deverá aparecer.

>[AZURE.NOTE] **Gerenciamento noturno**: quando o dispositivo está instalado em um data center remoto ou em uma sala de computador com acesso limitado, certifique-se de que as conexões seriais com dois controladores sempre estão conectadas a um comutador de console serial ou equipamento semelhante. Isso permite o controle remoto de fora de banda e operações de suporte se houver interrupções de rede ou falhas inesperadas.

Seu dispositivo agora está conectado para power, acesso de rede e conectividade serial. A próxima etapa é configurar o software e implantar o seu dispositivo.

## <a name="next-steps"></a>Próximas etapas

Saiba como [implantar e configurar seu dispositivo de StorSimple local](storsimple-deployment-walkthrough.md).
