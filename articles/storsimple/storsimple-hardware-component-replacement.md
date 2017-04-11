<properties 
   pageTitle="Substituição de componentes de hardware StorSimple | Microsoft Azure"
   description="Descreve como substituir com segurança a PCMs, bateria, módulos do controlador, EBOD controladores, unidades de disco e gabinete de um dispositivo de StorSimple."
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
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="storsimple-hardware-component-replacement"></a>Substituição de componentes de hardware StorSimple

## <a name="overview"></a>Visão geral

Os tutoriais de substituição do componente de hardware descrevem os componentes de hardware do seu dispositivo de série do Microsoft Azure StorSimple 8000 e as etapas necessárias para remover e substituí-las. Este artigo descreve os ícones de segurança, fornece ponteiros para os tutoriais detalhados e lista os componentes que são substituição.

>[AZURE.IMPORTANT] Antes de tentar remover ou substituir qualquer componente StorSimple, certifique-se de que você examine as [convenções de ícone de segurança](#safety-icon-conventions) e outras [precauções de segurança](storsimple-safety.md).
 
### <a name="safety-icon-conventions"></a>Convenções de ícone de segurança

A tabela a seguir descreve os ícones de segurança usados nestes tutoriais. Fique atento para esses ícones de segurança conforme você percorrer as etapas para remover e substituir componentes do dispositivo.

| Ícone | Texto | Informações adicionais |
|:---- |:---- |:-----------|
|![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png)| **PERIGO!** | Indica uma situação perigosa que, se não evitar, resultarão em morte ou danos graves. Esta palavra sinal está limitada às situações mais extremas.|
|![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png)| **AVISO!** | Indica uma situação perigosa que, se não evitar, pode resultar em morte ou danos graves.|
|![Ícone de cuidado](./media/storsimple-hardware-component-replacement/Caution.png)| **CUIDADO!** |Indica uma situação perigosa que, se não evitar, pode resultar em danos secundário ou moderado.|
|![Ícone de aviso](./media/storsimple-hardware-component-replacement/NoticeIcon.png)| **AVISO:** | Indica informações consideradas importantes, mas não relacionados de risco.|
|![Ícone de choque elétrico](./media/storsimple-hardware-component-replacement/Electric.png) | **Risco de choque elétrico** | Indica alta tensão.|
|![Ícone de peso pesado](./media/storsimple-hardware-component-replacement/Weight.png)| **Peso pesado**| |
|![Nenhum ícone de partes pelo usuário](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png)| **Não há partes de facilidade de manutenção de usuário** | Não acesse a menos que corretamente.|
|![Ícone de instruções de leitura](./media/storsimple-hardware-component-replacement/ReadInstructions.png)|**Ler todas as instruções primeiro**| |
|![Ícone de risco de dica](./media/storsimple-hardware-component-replacement/TipHazard.png)|**Dica de risco**| |

### <a name="before-you-begin"></a>Antes de começar

Familiarize-se com as informações de segurança sobre os ícones de dispositivo e segurança usados neste tutorial. Vá para [instalar com segurança e operar seu dispositivo StorSimple](storsimple-safety.md) para obter informações completas. Certifique-se de examinar as [precauções de segurança](storsimple-safety.md#handling-precautions) antes de você lidar com seu dispositivo StorSimple. 

Antes de tentar substituir um componente, considere as seguintes informações.

![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png) ![choque elétrico ícone](./media/storsimple-hardware-component-replacement/Electric.png) **Aviso!** 

- Proteja-se corretamente usando um descarga eletrostática ou tapete antiestática ao manipular módulos e componentes do seu dispositivo de StorSimple.

- Não toque qualquer circuitos. Use as alças de fornecido e guias ao manipular componentes que podem ter exposto circuitos.

![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png) ![Observe ícone](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **Aviso:**

Quando você substitui um módulo, **nunca deixe um compartimento vazio na parte traseira do compartimento**. Obter um módulo em branco ou substituição antes de remover a parte do problema.

## <a name="hardware-component-replacement-procedures"></a>Procedimentos de substituição do componente de hardware

Seu dispositivo de série 8000 StorSimple consiste em vários módulos plug-in no principal e/ou compartimentos EBOD. O 8100 tem um único gabinete principal, enquanto o 8600 é um dispositivo de compartimento duplo com um gabinete principal e uma circunscrição EBOD.

Os componentes de hardware principal no seu dispositivo são resumidos nas tabelas a seguir. Clique no link na coluna **procedimento de substituição** para ir para o tutorial associado.

|Componentes|# Apresentar-se|Módulo de plug-in?|Procedimento de substituição
|:---------|:--------|:--------------|:---------------------|
| Gabinete|1|Não|[Substituir o gabinete em seu dispositivo de StorSimple](storsimple-chassis-replacement.md) |
|Controladores principais|2|Sim| [Substituir um módulo de controlador em seu dispositivo de StorSimple](storsimple-controller-replacement.md) |
|Energia de 764W e refrigeração módulos (PCMs)|2|Sim| [Substituir uma energia e refrigeração módulo em seu dispositivo de StorSimple](storsimple-power-cooling-module-replacement.md) |
|Bateria de backup|2|Sim| [Substitua o módulo de bateria de backup no seu dispositivo de StorSimple](storsimple-battery-replacement.md) |
|Unidades de disco|12|Sim| [Substituir uma unidade de disco em seu dispositivo de StorSimple](storsimple-disk-drive-replacement.md) |

**Tabela 1** Componentes de hardware no compartimento principal

Compartimento do principal e o compartimento EBOD diferem em seus módulos e/s. Além disso, os PCMs tem potência diferentes. Os PCMs no compartimento primário são 764 W, enquanto aqueles no compartimento EBOD são 580 W. Os PCMs no compartimento primário também contêm um módulo de bateria de backup.

|Componentes|# Apresentar-se|Módulo de plug-in?| Procedimento de substituição
|:---------|:--------|:--------------|:---------------------|
|Gabinete|1|Não| [Substituir o gabinete em seu dispositivo de StorSimple](storsimple-chassis-replacement.md) |
|Controladores EBOD|2|Sim| [Substituir um controlador EBOD em seu dispositivo de StorSimple](storsimple-ebod-controller-replacement.md) |
|Energia de 580W e refrigeração módulos (PCMs)|2|Sim| [Substituir uma energia e refrigeração módulo em seu dispositivo de StorSimple](storsimple-power-cooling-module-replacement.md) |
|Unidades de disco|12|Sim| [Substituir uma unidade de disco em seu dispositivo de StorSimple](storsimple-disk-drive-replacement.md) |

**Tabela 2** Componentes de hardware no compartimento EBOD

Os módulos plug-in no dispositivo são realçados na seguinte frontal e traseiros diagramas. Você pode usar esses diagramas para determinar a localização dos vários módulos plug-in se substituto é necessário. O diagrama frontal mostra as unidades de disco e os diagramas traseiros do compartimento do EBOD e a apresentação principal circunscrição os módulos plug-in.

![Frontplane do dispositivo com unidades de disco](./media/storsimple-hardware-component-replacement/IC741028.png)

**Figura 1** Frente do dispositivo

|Rótulo|Descrição|
|:----|:----------|
|0 - 11|Unidades de disco (total de 12)|

Compartimento principal e o compartimento EBOD tem módulos de carrier de unidade. Gabinete hospeda doze 3,5" unidades de disco organizadas em um formato de 3 por 4.

![Backplane de módulos de circunscrição principal de dispositivo](./media/storsimple-hardware-component-replacement/IC740994.png)

**Figura 2** Verso do compartimento principal

|Rótulo|Descrição|
|:----|:----------|
|1|PCM 0|
|2|PCM 1|
|3|Controlador de 0|
|4|Controlador de 1|

![Backplane de módulos plug-in do dispositivo EBOD compartimento](./media/storsimple-hardware-component-replacement/IC769599.png)

**Figura 3** Verso do compartimento EBOD

|Rótulo|Descrição|
|:----|:----------|
|1|PCM 0|
|2|PCM 1|
|3|Controlador EBOD 0|
|4|Controlador EBOD 1|

## <a name="field-replaceable-units"></a>Unidades de substituição de campo

As unidades de substituição de campo (FRUs) seguintes estão disponíveis para seu dispositivo de StorSimple:

- Gabinete (incluindo o painel de operações integrada)

- 764 PCM DE AC W

- 580 PCM DE AC W

- Unidade de disco rígido com módulo de carrier de unidade

- Módulo do controlador

- Módulo do controlador EBOD

- Módulo de bateria de backup

- Rack rail kit de montagem

Faça [entre em contato com o suporte da Microsoft](storsimple-contact-microsoft-support.md) para solicitar qualquer uma dessas unidades de substituição.

## <a name="next-steps"></a>Próximas etapas

Revise todas [as informações de segurança](storsimple-safety.md) antes de tentar substituir um componente de hardware StorSimple.
