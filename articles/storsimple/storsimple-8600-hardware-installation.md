<properties
   pageTitle="Instalar o dispositivo StorSimple 8600 | Microsoft Azure"
   description="Descreve como descompactar, montagem em rack e cabo seu dispositivo StorSimple 8600 antes de implantar e configurar o software."
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
   ms.date="10/24/2016"
   ms.author="alkohli" />

# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Descompactar, para rack e o cabo seu dispositivo StorSimple 8600

## <a name="overview"></a>Visão geral
Seu 8600 do Microsoft Azure StorSimple é um dispositivo de compartimento duplo e consiste em um principal e um gabinete EBOD. Este tutorial explica como descompactar, para rack e hardware do dispositivo de cabo a 8600 StorSimple antes de configuram o software de StorSimple.

## <a name="unpack-your-storsimple-8600-device"></a>Descompactar seu dispositivo StorSimple 8600

As etapas a seguir fornecem instruções claras e detalhadas sobre como descompactar seu dispositivo de armazenamento de StorSimple 8600. Este dispositivo é fornecido em duas caixas, um para o compartimento primário e outro para o compartimento EBOD. Essas duas caixas, em seguida, são colocadas em uma única caixa.

### <a name="prepare-to-unpack-your-device"></a>Preparar para descompactar seu dispositivo

Antes de você descompactar seu dispositivo, examine as informações a seguir.


![Ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone de peso pesado](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **Aviso!**

1. Certifique-se de que você tenha duas pessoas disponíveis para gerenciar a espessura do dispositivo se você estiver tratando-lo manualmente. Um gabinete completamente configurado pode ponderar até 32 kg (libras 70.).
1. Posicione a caixa em uma superfície plana nível.

Em seguida, conclua as etapas a seguir para descompactar seu dispositivo.

#### <a name="to-unpack-your-device"></a>Descompactar seu dispositivo

1. Inspecione a caixa e espuma embalagem para crushes, cortes, danos de água ou quaisquer outros danos óbvios. Se a caixa ou embalagem estiver seriamente danificada, não abra a caixa. Faça a [contatar o suporte da Microsoft](storsimple-contact-microsoft-support.md) para ajudá-lo a avaliar se o dispositivo está em bom estado de funcionamento.

2. Abrir a caixa de dados externa e, em seguida, tire as duas caixas correspondente ao primário e compartimentos EBOD. Agora você pode descompactar primário e compartimentos EBOD. A figura a seguir mostra o modo de exibição descompactado de um dos compartimentos.

    ![Descompactar seu dispositivo de armazenamento](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)

    **Modo de exibição descompactado de seu dispositivo de armazenamento**

     Rótulo | Descrição
     ----- | -------------
     1     | Caixa de remessa
     2     | Cabos SAS (na bandeja de Acessórios e cabos)
     3     | Espuma inferior
     4     | Dispositivo
     5     | Espuma superior
     6     | Caixa de acessório

3. Após descompactar as duas caixas, certifique-se de que você tenha:

  - 1 compartimento principal (o gabinete principal e um gabinete EBOD estão em duas caixas separadas)
  - 1 compartimento EBOD
  - 4 cabos de alimentação, 2 em cada caixa
  - 2 cabos SAS (conectem compartimento primário circunscrição EBOD)
  - 1 cruzado cabo Ethernet
  - 2 cabos de console serial
  - 1 conversor de série-USB para acesso serial
  - 4 QSFP-para-SFP + adaptadores para uso com 10 interfaces de rede GbE
  - 2 rack kits de montagem (4 rails de lado com montagem, 2 para o gabinete principal e um gabinete EBOD), 1 em cada caixa
  - Obtendo a documentação de Introdução

    Se você não recebeu qualquer um dos itens listados acima, [contate o suporte da Microsoft](storsimple-contact-microsoft-support.md).  

A próxima etapa é seu dispositivo para rack.

## <a name="rack-mount-your-storsimple-8600-device"></a>Seu dispositivo StorSimple 8600 para rack

Siga as próximas etapas para instalar o seu dispositivo de armazenamento de StorSimple 8600 em um rack padrão de 19 polegadas com frontal e traseiras postagens. Este dispositivo vem com dois compartimentos: um compartimento principal e uma circunscrição EBOD. Ambos precisam ser montado em rack.

A instalação consiste em várias etapas, cada uma das quais é discutida nos procedimentos a seguir.

> [AZURE.IMPORTANT]
Dispositivos de StorSimple devem ser montado em rack para a operação correta.



### <a name="site-preparation"></a>Preparação do site

Os compartimentos devem ser instalados em um rack de 19 polegadas padrão que tem frontal e traseiras postagens. Use o procedimento a seguir para se preparar para a instalação do rack.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Preparar o site para instalação de rack

1. Certifique-se de que a principal e compartimentos EBOD estão repouso com segurança em uma superfície de trabalho planas, estável e nível (ou semelhante).

2. Verifique se o site onde você pretende configurar possui alternada padrão de uma fonte independente ou um unidade de distribuição de energia (PDU) de rack com no-break (UPS).

3. Certifique-se de que slot de (2 X 2 unidades) um 4U está disponível no rack de no qual você pretende montar os compartimentos.

![Ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone de peso pesado](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **Aviso!**

 Certifique-se de que você tenha duas pessoas disponíveis para gerenciar o peso se você estiver tratando a instalação do dispositivo manualmente. Um gabinete completamente configurado pode ponderar até 32 kg (libras 70.).

### <a name="rack-prerequisites"></a>Pré-requisitos de rack

Os compartimentos destinam-se a instalação em um rack padrão de 19 polegadas gabinete com:

- Profundidade mínima de 27.84 polegadas de rack postagem para postar
- Peso máximo de 32 kg para o dispositivo
- Pressão de volta máxima de 5 Pascal (medidor de água 0,5 mm)

### <a name="rack-mounting-rail-kit"></a>Kit de rail montagem de rack

Um conjunto de trilhos de montagem será fornecido para uso com o gabinete de rack de 19 polegadas. Trilhos foram testados para lidar com a espessura de circunscrição máximo. Estas rails também permitirá que a instalação de vários compartimentos sem perda de espaço no rack. Instale o compartimento EBOD primeiro.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Para instalar o compartimento EBOD nos trilhos

2. Execute esta etapa somente se rails internas não estão instalados no seu dispositivo. Normalmente, os trilhos internos são instalados na fábrica. Se rails não estiverem instalados, em seguida, instale os slides rail esquerda e direita para os lados do gabinete circunscrição. Eles anexar usando seis parafusos métricos em cada lado. Para ajudar com orientação, os slides rail são marcados **LH – frente** e **RH – frente**e final que é fixado em direção à parte traseira do compartimento tem um end cônico.

    ![Anexar slides rail gabinete de compartimento](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Anexar slides rail para os lados do compartimento**

    Rótulo | Descrição
    ----- | -----------
    1     | M 3 x4 parafusos de cabeça botão
    2     | Slides do gabinete

3. Anexe a rail esquerdo e montagens de à direita para os membros vertical gabinete de rack. Os colchetes são marcados **LH**, **RH**e **Este lado para cima** para orientá-lo orientação correta.

4. Localize os pinos rail na parte frontal e traseira do assembly rail. Estenda o rail para ajustá-lo entre as postagens de rack e insira os pinos em frente e furos de membro vertical de postagem de rack traseira. Certifique-se de que o conjunto de rail é nível.

5. Proteger o conjunto rail o rack membros verticais usando dois dos parafusos métricos fornecidos. Use um parafusos na frente e outra na parte traseira.

6. Repita essas etapas para o outro assembly rail.

     ![Anexar slides rail gabinete de rack](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Anexar conjuntos rail no rack**

     Rótulo | Descrição
     ----- | -----------
     1     | Clamping parafusos
     2     | Parafusos de postagem de rack frontal furos quadrado
     3     | Pins ao local rail frontal para a esquerda
     4     | Clamping parafusos
     5     | Esquerda rail traseira local pinos

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Montagem do compartimento EBOD no rack

Usando os trilhos de rack que acabou de ser instalar, execute as etapas a seguir para montar o compartimento EBOD no rack.

#### <a name="to-mount-the-ebod-enclosure"></a>Montar o compartimento EBOD

1. Com um assistente, tire o compartimento e alinhá-lo com os trilhos de rack.

2. Inserir cuidadosamente o compartimento em trilhos e distribuí-lo completamente em rack gabinete.

    ![Inserção de dispositivo no rack](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Montagem do compartimento no rack**

3. Remova os caps de borda esquerda e direita frontal, puxe os caps gratuitos. Simplesmente ajustam os caps de borda para as bordas.

4. Proteja o compartimento no rack instalando um parafuso Phillips fornecido por meio de cada borda, esquerda e direita.

4. Instale os caps de borda pressionando-os para a posição e encaixe-las no lugar.

     ![Instalando caps de borda](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)

    **Instalar os caps de borda**

     Rótulo | Descrição
     ----- | -----------
     1     | Parafusos de fixação do compartimento


### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Montagem do compartimento principal no rack

Após terminar de montagem do compartimento EBOD, você precisará montar o compartimento primário seguindo as mesmas etapas.

> [AZURE.NOTE]
>
> - É possível ter alguns slot vazio no rack entre o compartimento principal e o compartimento EBOD.
> - Use o cabo SAS fornecido 2m para conectar o compartimento primário ao compartimento EBOD.
> - Não há nenhuma restrição no posicionamento relativo da unidade de cabeça para a unidade EBOD. Portanto, o compartimento primário pode ser colocado em slot superior e o compartimento EBOD abaixo — ou vice-versa.

A próxima etapa é cabo seu dispositivo para power, rede e acesso serial.

## <a name="cable-your-storsimple-8600-device"></a>Conecte seu dispositivo StorSimple 8600

Os procedimentos a seguir explicam como cabo seu dispositivo StorSimple 8600 para power, rede e conexões seriais.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar a cabo seu dispositivo, você precisará:

- Seu compartimento principal e o compartimento EBOD, completamente descompactados
- 4 cabos de alimentação (2 cada para o principal e o compartimento EBOD) que acompanham seu dispositivo
- 2 cabos SAS fornecidos com o dispositivo para conectar o compartimento EBOD ao compartimento principal
- Acesso a 2 Power distribuição unidades (PDUs) (recomendado)
- Cabos de rede
- Fornecido cabos serial
- Conversor de série-USB com o driver apropriado instalado em seu computador (se necessário)
- Fornecido QSFP 4-para-SFP + adaptadores para uso com 10 interfaces de rede GbE
- [Com o suporte de hardware para as interfaces de rede GbE 10 em seu dispositivo de StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS e cabeamento de energia

Seu dispositivo tem uma circunscrição principal e uma circunscrição EBOD. Isso requer que as unidades a ser conectados juntos para alimentação e conectividade SCSI Serial anexado (SAS).

Ao configurar esse dispositivo pela primeira vez, execute as etapas para SAS cabeamento primeiro e execute as etapas para cabeamento de alimentação.

[AZURE.INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Cabeamento de rede

Seu dispositivo está em uma configuração de espera ativo: a qualquer momento, um módulo de controlador está ativo e todas as operações de disco e de rede durante o outro módulo de controlador de processamento está em espera. Se ocorrer uma falha de controlador, o controlador reserva imediatamente ativa e continua todas as operações de rede e disco.

Para oferecer suporte a esse failover controlador redundantes, você precisa cabo sua rede do dispositivo, conforme mostrado nas etapas a seguir.

#### <a name="to-cable-for-network-connection"></a>Cabo para conexão de rede

1. Seu dispositivo tem seis interfaces de rede em cada controlador: quatro 1 Gbps e duas 10 Gbps Ethernet portas. Consulte a ilustração a seguir para identificar as portas de dados no backplane do seu dispositivo.

     ![Backplane do dispositivo 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Voltar do seu dispositivo mostrando as portas de dados**

     Rótulo   | Descrição
     ------- | -----------
     0,1,4,5 |  Interfaces de rede GbE 1
     2,3     | Interfaces de rede 10 GbE
     6       | Portas seriais



1. Consulte o diagrama a seguir para cabeamento de rede. (A configuração de rede mínima é mostrada por linhas azuis sólidas. Para alta disponibilidade e desempenho, configuração adicional necessária é mostrada por linhas pontilhadas.)

![Cabo seu dispositivo 4U para rede](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Cabeamento para seu dispositivo de rede**

Rótulo | Descrição
----- | -----------
R    | LAN com acesso à Internet
B    | Controlador de 0
C    | PCM 0
D    | Controlador de 1
E    | PCM 1
F    | Controlador EBOD 0
G    | Controlador EBOD 1
H, EU  | Hosts (por exemplo, servidores de arquivo)
0-5  | Interfaces de rede
6    | Compartimento principal
7    | Compartimento EBOD

Quando o dispositivo de cabeamento, requer configuração mínima:


- Pelo menos duas interfaces de rede conectadas em cada controlador com um para acesso à nuvem e outro para iSCSI. Os dados 0 porta automaticamente está habilitada e configurada por meio do console serial do dispositivo. Além de dados 0, outra porta de dados também precisa ser configurada por meio do portal de clássico Azure. Nesse caso, conectar dados 0 porta à LAN primária (rede com acesso à Internet). As outras portas de dados podem ser conectadas ao segmento de SAN/iSCSI VLAN (LAN) da rede, dependendo da função pretendida.

- Interfaces idênticas em cada controlador conectado à mesma rede para assegurar a disponibilidade se houver uma falha de controlador. Por exemplo, se você optar por conectar dados 0 e 3 de dados para um dos controladores, você precisa se conectar os dados correspondentes 0 e dados 3 no outro controlador.

Lembre-se de alta disponibilidade e desempenho:


- Quando possível, configure um par de interface de rede para acesso à nuvem (1 GbE) e outro par para iSCSI (10 GbE recomendado) em cada controlador.

- Quando possível, conecte interfaces de rede de cada controlador a duas opções diferentes para assegurar a disponibilidade contra uma falha de alternar. A figura ilustra as duas 10 GbE interfaces de rede, dados 2 e 3 de dados, de cada controlador conectado a duas opções diferentes. Para obter mais informações, consulte as **interfaces de rede** sob os [requisitos de alta disponibilidade para o seu dispositivo StorSimple](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

>[AZURE.NOTE] Se usando transceptores SFP + com suas interfaces de rede 10 GbE, use o QSFP fornecido-SFP + adaptadores. Para obter mais informações, vá para [hardware com suporte as 10 GbE para interfaces de rede em seu dispositivo de StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

### <a name="serial-port-cabling"></a>Cabeamento de porta serial

Execute as seguintes etapas para cabo porta serial.

#### <a name="to-cable-for-serial-connection"></a>Cabo para conexão serial

1. Seu dispositivo tem uma porta serial em cada controlador que é identificado por um ícone de chave inglesa. Para localizar as portas seriais, consulte a ilustração que mostra os dados portas no verso do seu dispositivo.

2. Identifique o controlador ativo no backplane seu dispositivo. Um LED azul piscante indica que o controlador está ativo.

3. Use o cabo serial fornecido (se necessário, o conversor de USB serial para seu laptop) e conectar seu console ou computador (com emulação de terminal no dispositivo) à porta serial do controlador de ativos.

4. Instale os drivers de USB de série (fornecidos com o dispositivo) em seu computador.

5. Configure a conexão serial da seguinte maneira:
   - transmissão 115.200
   - 8 bits de dados
   - 1 bit de parada
   - Sem paridade
   - Controle de fluxo definido como **Nenhum**

6. Verifique se a conexão está funcionando pressionando Enter no console. Um menu de console serial deverá aparecer.

> [AZURE.NOTE] **Gerenciamento noturno:** Quando o dispositivo estiver instalado em um data center remoto ou em uma sala de computador com acesso limitado, certifique-se de que as conexões seriais com dois controladores sempre estão conectadas a um comutador de console serial ou equipamento semelhante. Isso permite controle remoto de fora de banda e operações de suporte em caso de interrupção de rede ou falhas inesperadas.

Você concluiu cabeamento seu dispositivo para power, acesso de rede e conexão serial. A próxima etapa é configurar o software em seu dispositivo.

## <a name="next-steps"></a>Próximas etapas

Agora você está pronto para [implantar e configurar seu dispositivo de StorSimple local](storsimple-deployment-walkthrough-u2.md).
