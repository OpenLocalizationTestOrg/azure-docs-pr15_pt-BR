<properties 
   pageTitle="Especificações técnicas StorSimple | Microsoft Azure"
   description="Descreve as especificações técnicas e informações de conformidade regulamentar padrões para os componentes de hardware StorSimple."
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

# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Especificações técnicas e conformidade para o dispositivo de StorSimple

## <a name="overview"></a>Visão geral

Os componentes de hardware do seu dispositivo Microsoft Azure StorSimple de acordo com as especificações técnicas e padrões regulatórios descritos neste artigo. As especificações técnicas descrevem Power e módulos refrigeração (PCMs), unidades de disco, capacidade de armazenamento e compartimentos. As informações de conformidade abordam coisas como padrões internacionais, segurança e emissão e cabeamento.


## <a name="power-and-cooling-module-specifications"></a>Especificações de energia e refrigeração módulo  

O dispositivo StorSimple tem dois 100-240V duplas de ventilador, compatível com SBB Power módulos refrigeração (PCMs). Isso fornece uma configuração de alimentação redundante. Se um PCM falhar, o dispositivo continua operando normalmente sobre o outro PCM até que o módulo com defeito seja substituído.  

Compartimento EBOD usa um 580 PCM W e compartimento primário usa um 764 PCM W. As tabelas a seguir listam as especificações técnicas associadas as PCMs.

| Especificação           | 580 PCM W (EBOD)                                    | 764 PCM W (primária)                                |
|------------------------ | --------------------------------------------------- | -------------------------------------------------- |
| Potência máxima de saída    | 580 W                                               | 764                                                |
| Frequência               | 50/60 Hz                                            | 50/60 Hz                                           |
| Seleção de intervalo de voltagem | Automático que varia: 90-264 V AC, 47/63 Hz               | Automático que varia: 90-264 V AC, 47/63 Hz               |
| Máximo corrente  | 20 A                                                | 20 A                                               |
| Correção de fator de energia | > voltagem nominal de entrada 95%                          | > voltagem nominal de entrada 95%                         |
| Harmônicas               | Atende EN61000-3-2                                   | Atende EN61000-3-2                                  |
| Saída                  | Voltagem de espera de 5 v @ 2.0 r                          | Voltagem de espera de 5 v @ 2.7 r                         |
|                         | + 5 v @ 42 R                                          | + 5 v @ 40 R                                         |
|                         | + 12 v @ 38 R                                         | + 12 v @ 38 R                                        |
| Em funcionamento           |  Sim                                                | Sim                                                |
| Opções e LED       | AC desliga e indicador de status quatro LED     | AC desliga e indicador de status de seis LED     |
| Compartimento refrigeração       | Axial ventiladores com variável de controle de velocidade  | Axial ventiladores com variável de controle de velocidade |

 
## <a name="power-consumption-statistics"></a>Estatísticas de consumo de energia  

A tabela a seguir lista os dados de consumo de energia típico (valores reais podem variar do publicados) para os diversos modelos de dispositivo de StorSimple. 
 
 Condições | 240 V AC | 240 V AC | 240 V AC | 110 V CA | 110 V CA | 110 V CA 
 ---------- | -------- | -------- | -------- | -------- | -------- | -------- 
 Unidades de lentas, de ventiladores ociosas | 1,45 r  |0.31 kW | 1057.76 BTU/h | 3.19 A | 0.34 kW | 1160.13 BTU/h 
 Unidades de lentas, ventiladores acessando | 1,54 r | 0,33 kW | 1126.01 BTU/h | 3.27 r | 0,36 kW | 1228.37 BTU/h 
 Ventiladores, unidades rápidas ociosas, dois PSUs da plataforma | 2.14 r | 0.49 kW  | 1671.95 BTU/h | 4,99 A | 0.54 kW | 1842.56 BTU/h 
 Ventiladores rápidas, agrega ocioso, uma fonte de alimentação da plataforma um ocioso | 2.05 r | 0.48 kW | 1637.83 BTU/h | 4.58 A | 0,50 kW | 1706.07 BTU/h 
 Unidades de rápidas, ventiladores acessando, dois PSUs da plataforma | 2,26 r | 0.51 kW | 1740.19 BTU/h | 4.95 A | 0.54 kW | 1842.56 BTU/h 
 Ventiladores rápidas, unidades acessando, uma fonte de alimentação da plataforma um ocioso | 2.14 r |0.49 kW | 1671.95 BTU/h | 4.81 A  | 0.53 kW | 1808.44 BTU/h 

## <a name="disk-drive-specifications"></a>Especificações de unidade de disco  

Seu dispositivo StorSimple suporta até 12 unidades de disco do formulário de 3,5 fator SCSI Serial anexado (SAS). As unidades reais podem ser uma mistura de unidades de estado sólidas (SSDs) ou unidades de disco rígido (HDDs), dependendo da configuração do produto. Os 12 slots de unidade de disco estão localizados em uma configuração de 3 por 4 na frente do compartimento. Compartimento EBOD permite de armazenamento adicional para outro 12 unidades de disco. Estas são sempre unidades de disco rígido.  

## <a name="storage-specifications"></a>Especificações de armazenamento
Os dispositivos StorSimple tem uma mistura de unidades de disco rígido e unidades de estado sólidas para o 8100 e 8600. A capacidade útil total para o 8100 e 8600 são aproximadamente 15 TB e 38 TB respectivamente. A tabela a seguir documentos os detalhes de SSD, unidade de disco rígido e a capacidade de nuvem no contexto da capacidade da solução StorSimple.

| Modelo do dispositivo / capacidade                         | 8100                                                    | 8600                                                    |
|------------------------------------------------|---------------------------------------------------------|---------------------------------------------------------|
| Número de unidades de disco rígido (unidades de disco rígido)              |   8                                                     |  19                                                     |
| Número de unidades de estado sólidos (SSDs)            |   4                                                     |  5                                                      |
| Capacidade de disco rígido única                            |   4 TB                                                  |  4 TB                                                   |
| Capacidade SSD única                            |   400 GB                                                |  800 GB                                                 |
| Capacidade extra                                 |   4 TB                                                  | 4 TB                                                    |
| Capacidade de disco rígido                            | 14 TB                                                   | 36 TB                                                   |
| Capacidade SSD                            | 800 GB                                                  | 2 TB                                                    |
| Total utilizáveis capacidade *                         | ~ 15 TB                                                 | ~ 38 TB                                                 |
| Capacidade de solução máximo (incluindo a nuvem)    | 200 TB                                                  | 500 TB                                                  |


<sup> * </sup> -  *a capacidade útil total inclui a capacidade disponível para dados, metadados e buffers.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Especificações de peso e dimensões de compartimento  

As tabelas a seguir listam as especificações circunscrição várias dimensões e peso.  

### <a name="enclosure-dimensions"></a>Dimensões de compartimento
A tabela a seguir lista as dimensões do compartimento em milímetros e polegadas.

|Compartimento |Milímetros |Polegadas |
|----------|------------|-------| 
| Altura |87,9 | 3,46 |
| Largura entre a borda de montagem | 483 | 19.02 |
| Largura em corpo do compartimento | 443 | 17.44 |
| Intensidade da borda de montagem frontal a extremity do corpo de compartimento | 577 | 22.72 |
| Profundidade do painel de operações para extremity mais distante do compartimento | 630.5 | 24.82 |
| Intensidade da borda de montagem a extremity mais distante do compartimento |   603 | 23.74 |

### <a name="enclosure-weight"></a>Peso de compartimento  

Dependendo da configuração, um compartimento primário totalmente carregado pode ponderar de 21 para 33 kg e requer duas pessoas lidar com ele. 
 
| Compartimento | Peso |
|-----------|--------| 
| Peso máximo (depende da configuração) |30 kg – 33 kg |
| Vazio (sem unidades ajustados) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Especificações do ambiente de compartimento  

Esta seção mostra as especificações relacionadas para o ambiente de compartimento. A temperatura, umidade, altitude, choque, vibração, orientação, segurança e compatibilidade eletromagnética (EMC) são incluídos nesta categoria.  

### <a name="temperature-and-humidity"></a>Temperatura e umidade

| Compartimento        | Faixa de temperatura ambiente  | Ambiente Umidade relativa | Lâmpada úmida máxima   |
|------------------|----------------------------|---------------------------|--------------------|
| Operacional      | 5° C - 35° C (41° F - 95° F)    | 20-80% não-condensação- | 28° C (82° F)        |
| Não operacional  | -40° C - 70° C (40° F - 158° F) | 5-100% sem condensação  | 29° C (84° F)        |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Fluxo de ar, altitude, choque, vibração, orientação, segurança e EMC
 
| Compartimento          | Especificações operacionais                                                |
|--------------------|---------------------------------------------------------------------------| 
| Fluxo de ar            | Fluxo de ar do sistema é frontal para a posterior. Sistema deve ser operado com uma instalação low-pressure, saída traseira. Voltar pressão criada por portas de rack e obstáculos não deve exceder 5 pascals (medidor de água 0,5 mm). |
| Altitude operacional  | -30 metros 3045 metros (-100 pés a 10.000 pés) com eliminação classificados por 5 ° C acima pés 7000 temperatura máxima. |
| Altitude não operacional  | metros-305 12.192 metros (-1,000 pés para 40.000 pés) |
| Shock, operacional  | 5g 10 ms ½ seno |
| Shock, não-operacional  | 30g 10 ms ½ seno |
| Vibração, operacional  | g 0.21 RMS 5-500Hz aleatório |
| Vibração, não-operacional  | g 1.04 RMS 2-200 Hz aleatório |
| Vibração, realocação  | seno de 2-200 Hz 3G |
| Orientação e montagem  | montagem de rack 19"(2 unidades EIA) |
| Trilhos de rack  | Para ajustar a intensidade de mínimo 700 mm (31.50 polegadas) racks compatível com IEC 297 |
| Segurança e aprovações  |   CE e UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| EMC  | EN55022 (CISPR - R), FCC R |

## <a name="international-standards-compliance"></a>Conformidade com padrões internacionais
Seu dispositivo do Microsoft Azure StorSimple esteja em conformidade com os padrões internacionais a seguir:  

- CE - EN 60950-1  
- Relatório CB para IEC 60950-1  
- UL e cUL para UL 60950-1  

## <a name="safety-compliance"></a>Conformidade com segurança  

Seu dispositivo do Microsoft Azure StorSimple atende as classificações de segurança a seguir:  

- Aprovação de tipo de produto do sistema: UL, cUL, CE  
- Conformidade com segurança: UL 60950, IEC 60950, EN 60950  

## <a name="emc-compliance"></a>Conformidade EMC 

Seu dispositivo do Microsoft Azure StorSimple atende aos seguintes classificações de EMC.  

### <a name="emissions"></a>Emissão

O dispositivo é compatível com EMC para níveis de emissão realizadas e irradiada.  

- Emissão realizada limitar níveis: CFR 47 Parte 15B classe A EN55022 Classe A CISPR classe A  
- Emissão irradiada limitar níveis: CFR 47 Parte 15B classe A EN55022 Classe A CISPR classe A   

### <a name="harmonics-and-flicker"></a>Harmônicas e flicker  

O dispositivo seja compatível com EN61000-3-2/3.  

### <a name="immunity-limit-levels"></a>Níveis de limite de imunidade  
O dispositivo seja compatível com EN55024.  

## <a name="ac-power-cord-compliance"></a>Conformidade de cabo de alimentação de CA
  
Plug e o conjunto completo de energia do cabo devem atender os padrões apropriados para o país em que o dispositivo está sendo usado, e eles devem ter aprovações de segurança que são aceitáveis nesse país. As tabelas a seguir listam padrões para os EUA e a Europa.  

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Cabos de alimentação de CA - EUA (deve ser NRTL listado)

| Componente       | Especificação                                                     |
| --------------- | ----------------------------------------------------------------- | 
| Tipo de cabo       | VA ou SVT, 18 AWG (mínimo), 3 condutores, comprimento máximo de medidores 2.0 |
| Plug            | Plug de anexo de tipo de base do NEMA 5-15P classificados 120 V, 10 A; ou IEC 320 C14, 250 V, 10 A |
| Soquete          | IEC 320 C-13, 250 V, 10 R                                         |

### <a name="ac-power-cords---europe"></a>Cabos de alimentação de CA - Europa

| Componente       | Especificação                                                     |
| --------------- | ----------------------------------------------------------------- | 
| Tipo de cabo       | Padronizadas, H05-VVF-3G1.0                                         |
| Soquete          | IEC 320 C-13, 250 V, 10 R                                         |

## <a name="supported-network-cables"></a>Cabos de rede com suporte  

Para 10 GbE interfaces de rede, dados 2 e 3 de dados, consulte a [lista de cabos de rede com suporte e módulos](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Próximas etapas

Agora você está pronto para implantar um dispositivo StorSimple no seu data center. Para obter mais informações, consulte [Implantando seu dispositivo local](storsimple-deployment-walkthrough-u2.md).  
