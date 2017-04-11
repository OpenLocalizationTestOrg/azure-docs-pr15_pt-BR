<properties 
   pageTitle="Interfaces de hardware para StorSimple 10 GbE | Microsoft Azure"
   description="Descreve o compatíveis transceptores small form-factor conectáveis (SFP), cabos e opções para as interfaces de rede 10 GbE em seu dispositivo de StorSimple."
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Com o suporte de hardware para as interfaces de rede GbE 10 em seu dispositivo de StorSimple

## <a name="overview"></a>Visão geral

Este artigo fornece informações sobre o hardware complementares que funciona com o dispositivo StorSimple do Microsoft Azure.

## <a name="list-of-devices-tested-by-microsoft"></a>Lista de dispositivos testado pela Microsoft

A Microsoft testou as seguintes fator de forma pequeno conectáveis transceptores SFP (), cabos e alterna para garantir que funcionem de maneira ideal com dispositivos. (As tabelas a seguir serão atualizadas como novo hardware testado.)

### <a name="sfp-transceivers"></a>Transceptores SFP +

|Fazer|Modelo|
|---|---|
|Cisco|SFP-10G-SR|

### <a name="cables"></a>Cabos

|S. Não. |Fazer|Modelo|
|---|---|---|
| 1.|Cisco|SFP-H10GB-CU1M|
| 2.|Cisco|SFP-H10GB-CU2M|
| 3.|Cisco|SFP-H10GB-CU3M|
| 4.|Tripp Lite|N820 - 05M (OM3)|

### <a name="switches"></a>Opções

|S. Não.|Fazer|Modelo|
|---|---|---|
| 1. |Cisco|N3K-C3172PQ-10GE|
| 2. |Cisco|N3K-C3048-ZM-F|
| 3. |Cisco|N5K-C5596UP-FA|

## <a name="list-of-devices-tested-in-the-field"></a>Lista de dispositivos testado no campo

Esta seção contém a lista de dispositivos que tiver sido implantada com êxito no campo por StorSimple clientes. Estes não tiveram sido testados pela Microsoft, mas provavelmente vão funcionar com o dispositivo StorSimple.
 
| Parâmetro                         | Valor                                    |
|-----------------------------------|------------------------------------------|
| Fazer de mudança                     | Juniper                                  |
| Modelo de mudança                    | ex4550-32F                               |
| Versão do sistema operacional de mudança | JunOS 12.3R9.4                           |
| Modelo lâminas                     | Portas integrado PIC (0)                    |
| Tornar transceptor                  | Juniper                                  |
| Modelo de transceptor               | Número de peça 021308 740 <br></br> Número de peça 030658 740                   |
| Versão do firmware transceptor    | Acelerar 01 versão 0,0 (relatado)            |
| Modelo de cabo                     | Jumper duplex LC/LC 50/125µ, OM3, LSZH |
| Modelo de StorSimple                | 8600                                     |
| Versão do software de StorSimple     | 6.3.9600.17491                           |


## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Lista de dispositivos testado pelo provedor de OEM (Mellanox)  

Mellanox testou as seguintes fator de forma pequeno conectáveis transceptores SFP (), cabos e alterna para garantir que funcionem de maneira ideal com interfaces de rede de Mellanox como interfaces de rede 10 GbE em seu dispositivo de StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Cabos e módulos suportados pelo Mellanox 

A tabela a seguir lista os cabos e módulos suportados pelo Mellanox. Estes não tiveram sido testados pela Microsoft, mas provavelmente vão funcionar com o dispositivo StorSimple.

| S. Não. | Velocidade | Modelo                 | Descrição                                            | Fazer                |
|--------|-------|-----------------------|--------------------------------------------------------|-----------------------|
| 1.     | 10 GbE| CAB-SFP-SFP - 1M        | cabo de cobre passivo SFP + 10 Gb/s 1M                   | Arista                |
| 2.     | 10 GbE| CAB-SFP-SFP - 2M        | cabo de cobre passivo SFP + 10 Gb/s 2m                   | Arista                |
| 3.     | 10 GbE| CAB-SFP-SFP - 3M        | cabo de cobre passivo SFP + 10 Gb/s 3M                   | Arista                |
| 4.     | 10 GbE| CAB-SFP-SFP - 5M        | cabo de cobre passivo SFP + 10 Gb/s 5m                   | Arista                |
| 5.     | 10 GbE| Cisco SFP-H10GBCU1M   | Cabo SFP + Cisco                                       | Cisco                 |
| 6.     | 10 GbE| Cisco SFP-H10GBCU3M   | Cabo SFP + Cisco                                       | Cisco                 |
| 7.     |10 GbE | Cisco SFP-H10GBCU5M   | Cabo SFP + Cisco                                       | Cisco                 |
| 8.     | 10 GbE| HP J9281B X242 10 G    | SFP + SFP + 1M conexão direta cabo de cobre             | HP                    |
| 9.     | 10 GbE| 455883-B21 HP BLc     | 10Gb SR SFP + Opt                                       | HP                    |
| 10.    | 10 GbE| 455886-B21 HP BLc     | 10Gb LR SFP + Opt                                       | HP                    |
| 11.    |10 GbE | 487649-B21 HP BLc     | Cabo de cobre SFP + 0,5 m 10GbE                           | HP                    |
| 12.    |10 GbE | 487652-B21 HP BLc     | Cabo de cobre SFP + 1M 10GbE                             | HP                    |
| 13.    |10 GbE | 487655-B21 HP BLc     | Cabo de cobre SFP + 3m 10GbE                             | HP                    |
| 14.    |10 GbE | 487658-B21 HP BLc     | Cabo de cobre SFP + 7m 10GbE                             | HP                    |
| 15.    |10 GbE | 537963-B21 HP BLc     | Cabo de cobre SFP + 5m 10GbE                             | HP                    |
| 16.    |10 GbE | HP AP784A             | 3M série C passivo cobre SFP + cabo                  | HP                    |
| 17.    |10 GbE | HP AP785A             | Cabo série C passivo cobre SFP + 5m                  | HP                    |
| 18.    |10 GbE | HP AP818A             | 1M série B Active cobre SFP + cabo                   | HP                    |
| 19.    |10 GbE | HP AP819A             | 3M série B Active cobre SFP + cabo                   | HP                    |
| 20.    |10 GbE | HP J9150A             | X132 10 G SFP + LC SR transceptor                        | HP                    |
| 21.    |10 GbE | HP J9151A             | X132 10 G SFP + LC LR transceptor                        | HP                    |
| 22.    |10 GbE | HP J9283B             | X242 10 G SFP + SFP + 3 m DAC cabo                        | HP                    |
| 23.    |10 GbE | HP J9285B             | X242 10 G SFP + SFP + 7 m DAC cabo                        | HP                    |
| 24.    | 10 GbE| HP JD095B             | X240 10 G SFP + SFP + m 0,65 DAC cabo                     | HP                    |
| 25.    | 10 GbE| HP JD096B             | X240 10 G SFP + SFP + m 1.2 DAC cabo                      | HP                    |
| 26.    | 10 GbE| HP JD097B             | X240 10 G SFP + SFP + 3 m PAPAI cabo                        | HP                    |
| 27.    | 10 GbE| Mellanox MAM1Q00A-QSA | QSFP ao SFP + adaptador                                   | Tecnologias de Mellanox |
| 28.    | 10 GbE| MC2309124-006 Mt      | Cabo de cobre passivo 1 x SFP+ para QSFP 24awg de 10 Gb/s m de 7   | Tecnologias de Mellanox |
| 29.    | 10 GbE| MC2309124-007 Mt      | Cabo de cobre passivo 1 x SFP+ para QSFP 24awg de 10 Gb/s m de 7   | Tecnologias de Mellanox |
| 30.    | 10 GbE| MC2309130-003 Mt      | Cabo de cobre passivo 1 x SFP+ para QSFP 30awg de 10 Gb/s 3M   | Tecnologias de Mellanox |
| 31.    | 10 GbE| MC2309130-00A Mt      | Cabo de cobre passivo 1 x SFP+ para QSFP 30awg de 10 Gb/s 0,5 m | Tecnologias de Mellanox |
| 32.    | 10 GbE| MC3309124-005 Mt      | Cobre passivo cabo 1 24awg de 10 Gb/s x SFP+ 5 m           | Tecnologias de Mellanox |
| 33.    | 10 GbE| MC3309124-007 Mt      | Cobre passivo cabo 1 24awg de 10 Gb/s x SFP+ 7 m           | Tecnologias de Mellanox |
| 34.    | 10 GbE| MC3309130-003 Mt      | Cobre passivo cabo 1 30awg de 10 Gb/s x SFP+ 3M           | Tecnologias de Mellanox |
| 35.    | 10 GbE| MC3309130-00A Mt      | Cobre passivo cabo 1 30awg de 10 Gb/s x SFP+ 0,5 m         | Tecnologias de Mellanox |

### <a name="switches-supported-by-mellanox"></a>Opções suportadas pelo Mellanox 

A tabela a seguir lista as opções suportadas pelo Mellanox. Estes não tiveram sido testados pela Microsoft, mas provavelmente vão funcionar com o dispositivo StorSimple.

| S. Não. | Velocidade | Modelo | Descrição                                                         | Fazer              |
|--------|-------|-------------|---------------------------------------------------------------------|-------------|
| 1.     | 10GbE | 516733-B21  | HP 6120XG ProCurve da 10GbE Ethernet Blade Switch                      | HP    |
| 2.     | 10GbE | 538113-B21  | Módulo de passagem HP 10GbE (PTM)                                  | HP    |
| 3.     | 10GbE | EN4093      | Módulo de Switch Scalable 10Gigabit do EN4093 de estrutura de sistema IBM PureFlex | IBM   |
| 4.     | 1GbE  | 3020        | Blade de switch Cisco Catalyst 3020 1GbE                               | Cisco |
| 5.     | 1GbE  | 3020 X       | Cisco Catalyst 3020 X 1GbE switch blade                              | Cisco |
| 6.     | 1GbE  | 438030-B21  | Módulo de switch 1GbE HP - GbE2c camada 2/3 Ethernet Blade Switch       | HP    |
| 7.     | 1GbE  | 6120G       | Blade de mudança de 1GbE HP ProCurve da 6120G/XG                              | HP    |

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre os componentes de hardware StorSimple e status](storsimple-monitor-hardware-status.md).
