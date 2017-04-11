<properties 
   pageTitle="Segurança para seu dispositivo de StorSimple | Microsoft Azure"
   description="Descreve as considerações, diretrizes e convenções de segurança e explica como instalar e operar seu dispositivo StorSimple com segurança."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="safely-install-and-operate-your-storsimple-device"></a>Instalar e operar seu dispositivo StorSimple com segurança

![Ícone de aviso](./media/storsimple-safety/IC740879.png)
![ler o ícone de aviso de segurança](./media/storsimple-safety/IC740885.png) **Ler segurança e informações de integridade**

Ler todas as informações de segurança e saúde neste artigo que se aplica ao seu dispositivo de StorSimple do Microsoft Azure. Manter todas as guias impressas fornecidas com o seu dispositivo StorSimple para referência futura. Falha ao siga instruções e corretamente configurar, usar e cuidado para este produto pode aumentar o risco de danos graves ou morte ou danos ao dispositivo ou dispositivos. Uma [versão para download deste guia](http://www.microsoft.com/download/details.aspx?id=44233) também está disponível.

## <a name="safety-icon-conventions"></a>Convenções de ícone de segurança

Aqui estão os ícones que você encontrará quando você examinar as precauções de segurança para ser observado quando Configurando e executando o seu dispositivo de StorSimple do Microsoft Azure.

| Ícone  | Descrição  |
|:------|:-------------| 
|![Ícone de perigo](./media/storsimple-safety/IC740879.png) **perigo!**|Indica uma situação perigosa que, se não evitar, resultarão em morte ou danos graves. Esta palavra sinal é estar limitado às situações mais extremas.| 
|![Ícone de aviso](./media/storsimple-safety/IC740879.png) **Aviso!**|Indica uma situação perigosa que, se não evitar, pode resultar em morte ou danos graves.|
|![Ícone de aviso](./media/storsimple-safety/IC740879.png) **cuidado!**|Indica uma situação perigosa que, se não evitar, pode resultar em danos secundário ou moderado.|
|![Observe o ícone](./media/storsimple-safety/IC740881.png) **Aviso:**|Indica informações consideradas importantes, mas não relacionados de risco.|
|![Ícone de choque elétrico](./media/storsimple-safety/IC740882.png) **o risco de choque elétrico** |Alta tensão|
|![Ícone de peso pesado](./media/storsimple-safety/IC740883.png) **peso pesado**| |
|![Nenhum usuário pelo ícone de peças](./media/storsimple-safety/IC740879.png) **Nenhum partes de facilidade de manutenção de usuário**|Não acesse a menos que corretamente.|
|![Ler segurança aviso ícone](./media/storsimple-safety/IC740885.png)**Ler todas as instruções primeiro**| |
|![Ícone de risco de dica](./media/storsimple-safety/IC740886.png) **Dica de risco**| |


## <a name="handling-precautions"></a>Manipulando precauções

![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![ícone de peso pesado](./media/storsimple-safety/IC740883.png) **Aviso!** 


Para reduzir o risco de danos:

- Um gabinete completamente configurado pode ponderar até 32 kg (70 libras); Não tente tire-lo sozinho.
- Antes de mover o compartimento, verifique sempre se duas pessoas estão disponíveis para lidar com a espessura. Lembre-se de que uma pessoa tentando tire esse peso pode sustentar ferimentos.
- Não tire o compartimento por alças a potência e módulos refrigeração (PCMs) localizadas na parte traseira da unidade. Eles não foram projetados para tirar a espessura.

## <a name="connection-precautions"></a>Precauções de Conexão

![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![choque elétrico ícone](./media/storsimple-safety/IC740882.png) **Aviso!**

Para reduzir a probabilidade de danos, choque elétrico ou morte:

- Quando da plataforma várias fontes de CA, desconecte toda a alimentação suprimentos para isolamento concluída.

- Desconecte permanentemente a unidade antes de movê-la ou se você acha que ele danificado de nenhuma forma.

- Fornece uma conexão Terra elétrica confiáveis para os fios de alimentação. Verifique se que a base do compartimento atende aos requisitos nacionais e locais antes de aplicar power.

- Certifique-se de que a conexão de energia está desconectada sempre antes da remoção de um PCM do compartimento.

- Considerando que plug sobre o cabo de fonte de alimentação é o principal desconectar o dispositivo, certifique-se de que as tomadas de soquete estão localizadas perto do equipamento e facilmente acessíveis.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![choque elétrico ícone](./media/storsimple-safety/IC740882.png) **Aviso!**

Para reduzir a probabilidade de superaquecimento ou fire das conexões elétricas:

- Fornece uma fonte de energia adequado com proteção de sobrecarga elétrica para atender a requisitos detalhada na especificação técnica.

- Não use cabos de alimentação bifurcada ("Y" clientes potenciais).

- Para atender aos requisitos térmicos, emissão e segurança aplicáveis, sem capas devem ser removidas e todos os compartimentos devem ser preenchidos com módulos plug-in ou unidade espaços em branco.

- Assegure-se de que o equipamento de maneira especificada pelo fabricante. Se este equipamento é usado em uma forma não especificada pelo fabricante, a proteção fornecida pelo equipamento pode ser deficiência visual.

![Observe o ícone](./media/storsimple-safety/IC740881.png) **Aviso:**

Para a operação adequada de seu equipamento e para evitar danos de produto:

- As portas RJ45 atrás do dispositivo são para somente uma conexão de Ethernet. Estes não devem estar conectados a uma rede de telecomunicações.

- Certifique-se de instalar o dispositivo em um rack que pode acomodar um design de refrigeração de frente para trás.

- Módulos todos os plug-in e chapas em branco são parte do compartimento do sistema. Eles devem ser removidos apenas quando uma substituição pode ser adicionada imediatamente. O sistema não deve ser executado sem todos os módulos ou espaços em branco no lugar.

## <a name="rack-system-precautions"></a>Precauções de sistema de rack

Os seguintes requisitos de segurança devem ser considerados quando você monta o dispositivo em um gabinete de rack.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![dica risco ícone](./media/storsimple-safety/IC740886.png) **Aviso!**
 
Para reduzir a probabilidade de danos de uma dica sobre:

- O design de rack deve suportar o peso total dos compartimentos instalados e deve incorporar estabilização recursos adequados para impedir que o rack de ponta ou sendo enviadas durante a instalação ou uso normal.

- Ao carregar um rack, preencher o rack de baixo para cima e esvazie de cima para baixo.

- Não deslize a mais de um compartimento fora do rack por vez para evitar o perigo do rack toppling.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![choque elétrico ícone](./media/storsimple-safety/IC740882.png) **Aviso!**

Para reduzir a probabilidade de danos, choque elétrico ou morte:

- Rack deve ter um sistema de distribuição elétrica seguros. Ele deve fornecer proteção de corrente para o compartimento e não deve ser sobrecarregado pelo número total de compartimentos instalados. A classificação de consumo de energia elétrica mostrada no letreiro deve ser observada.

- O sistema de distribuição elétrica deve fornecer uma Terra confiável para cada compartimento no rack.

- O design do sistema de distribuição elétrica deve levar em consideração a perda de terra total atual de todas as fontes de alimentação em todos os compartimentos. Observe que cada fonte de alimentação em cada gabinete tem uma corrente de perda de Terra de 1,0 máximo mA a 60 Hz, volts 264. Rack pode exigir rotular com "alta perda atual. Conexão de terra (terra) é essencial antes de se conectar a uma fonte."

- Rack, quando configurado com os compartimentos, deve atender os requisitos de segurança de UL 60950-1 e IEC 60950-1/EN 60950-1.

![Observe o ícone](./media/storsimple-safety/IC740881.png) **Aviso:**

Para o refrigeração adequada do seu sistema de rack:

- Certifique-se de que o design de rack leva em consideração o compartimento máximo temperatura ambiente de 35 graus Celsius (95 ° f).

- O sistema é operado com a instalação do low-pressure, saída traseira (back pressão criada por portas de rack e obstáculos não deve exceder 5 Pascal [medidor de água 0,5 mm]).

## <a name="power-cooling-module-pcm-precautions"></a>Precauções de módulo refrigeração (PCM) de energia

O dispositivo foi projetado para funcionar com dois PCMs. Cada as PCMs tem uma fonte de alimentação e um fanático por dois eixos. Durante uma condição crítica, o sistema permite uma falha de uma fonte de alimentação enquanto continua operações normais. Dois PCMs (e, portanto, fontes de alimentação) sempre deve ser instalado. Um único PCM não fornece alimentação redundante. Portanto, a falha de PCM mesmo uma pode resultar em tempo de inatividade ou possível perda de dados.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![choque elétrico ícone](./media/storsimple-safety/IC740882.png) **Aviso!**

Para reduzir a probabilidade de danos, choque elétrico ou morte:

- Não remova bastidores o PCM. Não há um perigo choques elétricos dentro. Para retornar o PCM e obter uma substituição, [entre em contato com o suporte da Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx).

![Observe o ícone](./media/storsimple-safety/IC740881.png) **Aviso:**

Para a operação adequada de seu equipamento e para evitar danos de produto:

- Você deve substituir o PCM falha dentro de 24 horas. Depois que um PCM é removido de substituição, a substituição deve ser concluída em 10 minutos após a remoção.

- Não remova um PCM, a menos que uma substituição pode ser instalada imediatamente. Compartimento não deve ser operado sem todos os módulos no lugar.

## <a name="electrostatic-discharge-esd-precautions"></a>Precauções descarga eletrostática (ESD)

![Observe o ícone](./media/storsimple-safety/IC740881.png) **Aviso:**

Observe as seguintes precauções ESD relacionados.

- Certifique-se de que você tenha instalado e marcada uma tira de pulso ou o tornozelo antiestática adequada.

- Observe todas as precauções ESD convencionais ao manipular componentes e módulos.

- Evite o contato com componentes de backplane e conectores de módulo.

- Danos ESD não está coberto pela garantia.

## <a name="battery-disposal-precautions"></a>Precauções de eliminação de bateria

A fonte de alimentação usa uma bateria especial para proteger o conteúdo da memória durante interrupções de energia temporária, curto prazo. A bateria está encaixada no PCM. Lembre as seguintes informações sobre a bateria.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) **Aviso!**

Para reduzir o risco de curtas, fire, explosão, danos ou morte:

- Descarte baterias usadas de acordo com normas nacional/regional.

- Não desmontar, crush, ou de calor acima de 60 graus Celsius (140 ° f) ou incinerate. Substitua a bateria PCM com apenas uma bateria fornecida. Uso de outra bateria pode apresentar um risco de fire ou explosão.

- Use proteção delimitada as pilhas se eles são removidos da fonte de alimentação.

![Observe o ícone](./media/storsimple-safety/IC740881.png) **Aviso:**

Quando remessa ou caso contrário transporte as pilhas aérea, siga o documento de orientação de bateria lítio IATA disponível em [http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Depois de revisar esses avisos de segurança, as próximas etapas são para descompactar, rack e o cabo seu dispositivo.

## <a name="next-steps"></a>Próximas etapas

- Para um dispositivo 8100, vá para [instalar o dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md).

- Para um dispositivo 8600, vá para [instalar o dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

