<properties 
   pageTitle="Substituir o gabinete em um dispositivo de StorSimple | Microsoft Azure"
   description="Descreve como remover e substituir o gabinete para sua StorSimple primário compartimento ou gabinete EBOD."
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

# <a name="replace-the-chassis-on-your-storsimple-device"></a>Substituir o gabinete em seu dispositivo de StorSimple

## <a name="overview"></a>Visão geral

Este tutorial explica como remover e substituir um gabinete em um dispositivo de série 8000 StorSimple. O modelo de StorSimple 8100 é um dispositivo de compartimento único (um gabinete), enquanto o 8600 é um dispositivo de compartimento duplo (duas gabinete). Para um modelo de 8600, há potencialmente duas gabinete que pode falhar no dispositivo: gabinete de compartimento principal ou gabinete para o compartimento EBOD.

Nos dois casos, o gabinete de substituição que é fornecida pela Microsoft está vazia. Sem energia e módulos refrigeração (PCMs), módulos do controlador, unidades de disco de estado sólido (SSDs), unidades de disco rígido (unidades de disco rígido) ou EBOD módulos serão incluídos.

>[AZURE.IMPORTANT] Antes de remover e substituir o gabinete, examine as informações de segurança em [substituição de componentes de hardware StorSimple](storsimple-hardware-component-replacement.md).

## <a name="remove-the-chassis"></a>Remover o gabinete

Execute as seguintes etapas para remover o gabinete em seu dispositivo de StorSimple.

#### <a name="to-remove-a-chassis"></a>Para remover um gabinete

1. Certifique-se de que o dispositivo StorSimple está desligado e desconectado de todas as fontes de alimentação.

2. Remova todos os rede e cabos SAS, se aplicável.

3. Remova a unidade de rack.

4. Remova cada unidade e observe os slots do qual elas são removidas. Para obter mais informações, consulte [Remover a unidade de disco](storsimple-disk-drive-replacement.md#remove-the-disk-drive).

5. No gabinete EBOD (se isso for gabinete que falhou), remova os módulos do controlador EBOD. Para obter mais informações, consulte [Remover um controlador EBOD](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller). 

    Sobre o compartimento principal (se isso for gabinete que falhou), remova os controladores e observe os slots do qual elas são removidas. Para obter mais informações, consulte [Remover um controlador](storsimple-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Instalar o gabinete

Execute as seguintes etapas para instalar o gabinete em seu dispositivo de StorSimple.

#### <a name="to-install-a-chassis"></a>Para instalar um gabinete

1. Monte gabinete no rack. Para obter mais informações, consulte [seu dispositivo StorSimple 8100 para rack](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) ou [seu dispositivo StorSimple 8600 para rack](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).

2. Depois que o gabinete estiver montado no rack, instale os módulos de controlador nas mesmas posições que foram instalados anteriormente em.

3. Instale as unidades na mesma posições e slots que eles instaladas anteriormente.

    >[AZURE.NOTE] Recomendamos que você instale o SSDs nos slots primeiro e, em seguida, instale as unidades de disco rígido.

2. Com o dispositivo montado em rack e os componentes instalados, conectar o dispositivo a fontes de alimentação apropriado e ligue o dispositivo. Para obter detalhes, consulte [cabo seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [substituição de componentes de hardware StorSimple](storsimple-hardware-component-replacement.md).

