<properties 
    pageTitle="StorSimple monitoramento indicadores | Microsoft Azure" 
    description="Descreve os sinais (LED) e alarmes sonoros usados para monitorar o status do dispositivo StorSimple."
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
    ms.date="08/18/2016"
    ms.author="alkohli" />

# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Usar StorSimple monitoramento indicadores para gerenciar seu dispositivo   

## <a name="overview"></a>Visão geral

Seu dispositivo StorSimple inclui sinais (LED) e alarmes que você pode usar para monitorar a módulos e o status geral do dispositivo StorSimple. Os indicadores de monitoramento podem ser encontrados nos componentes de hardware do compartimento primária do dispositivo e o compartimento EBOD. Os indicadores de monitoramento podem ser LED ou alarmes sonoros.

Há três estados de LED usados para indicar o status de um módulo: verde, verde a vermelho-âmbar ou vermelho-âmbar piscante.  

- LED verde representam um eficaz, status operacional.  
- Piscando em verde para representar de LED âmbar de vermelho a presença de condições não críticos que pode exigir intervenção do usuário.  
- LED âmbar vermelho indica que não há uma falha crítica presente dentro do módulo.  

O restante deste artigo descreve o diversos LED indicador monitoramento, seus locais no dispositivo StorSimple, o status do dispositivo com base nos Estados de LED e qualquer alarmes sonoros associados.

## <a name="front-panel-indicator-leds"></a>Indicador de painel frontal LED

O painel frontal, também conhecido como o *Painel de operações* ou no *Painel de operações*, exibe o status de agregação de todos os módulos do sistema. Painel frontal equivale a StorSimple principal e o compartimento EBOD e está ilustrado abaixo.  

   ![Painel frontal do dispositivo][1]
 
Painel frontal contém os indicadores a seguir:  

1. Botão mudo
2. Indicador de energia LED (vermelho/verde-âmbar)
3. Indicador de falha de módulo INSTRUTOR (em vermelho-âmbar/OFF)
4. Indicador de falhas lógicas INSTRUTOR (em vermelho-âmbar/desativado
5. Exibição de ID de unidade  

A principal diferença entre o painel frontal LED do dispositivo e os estilos para o compartimento EBOD é o **Número de identificação de unidade do sistema** mostrado no monitor LED. A ID da unidade padrão exibida no dispositivo é **00**, enquanto a ID da unidade padrão exibida em compartimento EBOD é **01**. Isso permite que você rapidamente diferenciar o dispositivo e o compartimento EBOD quando o dispositivo está ativado. Se seu dispositivo estiver desativado, use as informações fornecidas no [Ativar um novo dispositivo](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) para diferenciar o dispositivo do compartimento EBOD.  

## <a name="front-panel-led-status"></a>Status do LED do painel frontal  

Use a tabela a seguir para identificar o status indicado pelo LED no painel frontal do dispositivo ou compartimento EBOD.  

|Alimentação do sistema | Falha do módulo | Falhas lógicas | Alarme | Status|
|-------------|---------------|-----------------|-------|-------|
|Vermelho-âmbar | DESATIVAR     | DESATIVAR | N/D | Alternada perdido, operando em power backup ou alternada em e o controlador módulos foram removidos.|
|Verde | ATIVADO | ATIVADO | N/D | Estado de teste de alimentação do painel de operações em (5s)|
|Verde | DESATIVAR | DESATIVAR | N/D | Ligue, todas as funções de boas|
|Verde | ATIVADO |N/D | Falhas PCM LED, falha de ventilador LED | Qualquer falha PCM, falha de ventilador, acima ou abaixo temperatura|
| Verde | ATIVADO | N/D | Módulo de i/o LED  | Qualquer falha de módulo do controlador|
| Verde | ATIVADO | N/D | N/D | Falhas de lógica de compartimento|
| Verde | Flash | N/D | Módulo LED de status no módulo do controlador. Falhas PCM LED, falha de ventilador LED | Tipo de módulo do controlador desconhecido instalado, falha de barramento I2C, o erro de configuração do controlador módulo produto vital dados (VPD) |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Indicador de módulo (PCM) refrigeração LED de energia   

Indicador de módulo (PCM) refrigeração potência LED pode ser encontrada na parte posterior do compartimento principal ou compartimento EBOD em cada módulo PCM. Este tópico aborda como usar o LED a seguir para monitorar o status do seu dispositivo de StorSimple.  

- LED de PCM para o compartimento principal
- LED de PCM para o compartimento EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>LED de PCM para o compartimento principal  

O dispositivo StorSimple tem um módulo PCM 764W com uma bateria adicional. A ilustração a seguir mostra o painel de LED do dispositivo.  

   ![LED de PCM no gabinete principal][2]

Legenda de LED:

1. Falha da alimentação AC
2. Falha do ventilador
3. Falha de bateria
4. PCM OKEY
5. Falha de DC
6. Bateria boa  

O status do PCM é indicado no painel de LED. O painel de PCM LED do dispositivo tem seis LED. Quatro estas LED exibem o status da fonte de alimentação e o ventilador. O LED de duas restante indica o status do módulo bateria de backup no PCM. Você pode usar as tabelas a seguir para determinar o status do PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>Indicador PCM LED de alimentação e ventilador
| Status | PCM Okey (verde) | Falhas de CA (âmbar) | Falhas de ventilador (âmbar) | Falhas de DC (âmbar) |
|--------|----------------|-----------------------|------------------|----------------------|
| Sem alimentação de CA (para compartimento) | DESATIVAR | DESATIVAR | DESATIVAR | DESATIVAR|
| Nenhum alternada (somente este PCM) | DESATIVAR | ATIVADO | DESATIVAR | ATIVADO |
| AC apresentar PCM ON - Okey     | ATIVADO | DESATIVAR | DESATIVAR | DESATIVAR |
| Falhas PCM (ventilador falhar) | DESATIVAR | DESATIVAR | ATIVADO | N/D |
| Falhas PCM (sobre amp, sobre voltagem, sobre atual) | DESATIVAR | ATIVADO | ATIVADO | ATIVADO |
| PCM (ventilador fora tolerância) | ATIVADO | DESATIVAR | DESATIVAR | ATIVADO |
| Modo de espera | Piscando | DESATIVAR | DESATIVAR | DESATIVAR |
| Download do firmware PCM | DESATIVAR | Piscando | Piscando | Piscando |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>Indicador PCM LED da bateria de backup  

| Status | Bateria boa (verde) | Falha de bateria (âmbar) |
|--------|----------------------|-----------------------|
| Bateria não presente | DESATIVAR | DESATIVAR |
| Bateria presente e cobrada | ATIVADO | DESATIVAR |
| Bateria descarregar charging ou manutenção | Piscando | DESATIVAR |
| Falha de "software" de bateria (recuperável) | DESATIVAR | Piscando |
| Falhas de "disco rígida" de bateria (não recuperável) | DESATIVAR | ATIVADO |
| Disarmed de bateria | Piscando | DESATIVAR |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>LED de PCM para o compartimento EBOD  

Compartimento EBOD tem um PCM 580W e nenhuma bateria adicional. O painel de PCM para compartimento EBOD tem indicador LED somente para as fontes de alimentação e o ventilador. A ilustração a seguir mostra essas LED.

   ![LED de PCM no gabinete EBOD][3] 
 
Você pode usar a tabela a seguir para determinar o status do PCM.  

| Status | PCM Okey (verde) | Falhas de CA (âmbar) | Falhas de ventilador (âmbar) | Falhas de DC (âmbar) |
|--------|---------------|------------------------|------------------|----------------------|
| Sem alimentação de CA (para compartimento) | DESATIVAR | DESATIVAR | DESATIVAR | DESATIVAR |
| Nenhum alternada (somente este PCM) | DESATIVAR | ATIVADO | DESATIVAR | ATIVADO |
| AC apresentar PCM Diante – Okey | ATIVADO | DESATIVAR | DESATIVAR | DESATIVAR |
| Falhas PCM (ventilador falhar) | DESATIVAR | DESATIVAR | ATIVADO | X |
| Falhas PCM (sobre amp, sobre voltagem, sobre atual | DESATIVAR | ATIVADO | ATIVADO | ATIVADO |
| PCM (ventilador fora tolerância) | ATIVADO | DESATIVAR | DESATIVAR | ATIVADO |
| Modelo de espera | Piscando | DESATIVAR | DESATIVAR | DESATIVAR |
| Download do firmware PCM | DESATIVAR | Piscando | Piscando | Piscando |

## <a name="controller-module-indicator-leds"></a>Indicador de módulo do controlador LED  

O dispositivo StorSimple contém LED para o controlador primário e os módulos do controlador EBOD.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Monitoramento LED o controlador primário
A ilustração a seguir ajuda você a identificar o LED no controlador primário. (Todos os componentes são listados para auxiliar na orientação.)  

   ![Monitoramento LED - controlador primário][4]
 
Use a tabela a seguir para determinar se o módulo do controlador está funcionando corretamente.  

### <a name="controller-indicator-leds"></a>Indicador de controlador LED  

| LED | Descrição                                                                            
|---- | ----------- |
| ID do LED (azul) | Indica que o módulo está sendo identificado. Se o LED azul estiver piscando em um controlador em execução, o controlador é o ativo e o outro é o controlador reserva. Para obter mais informações, consulte [identificar o controlador ativo em seu dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Falhas LED (âmbar) | Indica uma falha no controlador.        
| LED Okey (verde) | Verde estável indica que o controlador é Okey. Verde piscante indica um erro de configuração de VPD controlador. |
| Atividade SAS LED (verde) | Verde estável indica uma conexão sem atividade atual. Verde piscante indica que a conexão tem atividade contínua. |
| Status de Ethernet LED | Do lado direito indica atividade de link/rede: (verde estável) link ativo, (piscando em verde) atividade de rede. Esquerda indica a velocidade de rede: (amarelo) 1000 Mb/s, 100 Mb/s (verde) e (OFF) 10 Mb/s. Dependendo do modelo de componente, esta luz pode piscar mesmo se a interface de rede não está habilitada. |
| LED de POSTAGEM | Indica o andamento de inicialização quando o controlador está ativado. Se o dispositivo de StorSimple falhar ao inicializar, este LED ajudará a Microsoft Support identificar o ponto no processo de inicialização em que ocorreu a falha. |

>[AZURE.IMPORTANT] 
Se o LED de falha é acende, há um problema com o módulo do controlador que pode ser resolvido reiniciando o controlador. Se o controlador de reinicialização não resolver esse problema, contate Microsoft Support.  


### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Monitoramento LED para o EBOD (compartimento EBOD)  

Cada um dos controladores Gb/s SAS EBOD 6 tem LED que indicam seu status, conforme mostrado na ilustração a seguir.  

  ![Monitoramento LED - circunscrição EBOD][5]

Use a tabela a seguir para determinar se o módulo do controlador EBOD está operando normalmente.  

### <a name="ebod-controller-module-indicator-leds"></a>Indicador de módulo de controlador EBOD LED  

|Status | Módulo de i / Okey (verde) | Falha de módulo de i/o (âmbar) | Atividade de porta de host (verde) |
|-------|----------------------|-------------------------------|----------------------------|
| Módulo do controlador de Okey | ATIVADO | DESATIVAR | - |
| Falha do módulo de controlador | DESATIVAR | ATIVADO | - |
| Nenhuma conexão de porta de host externo | - | - | DESATIVAR |
| Conexão de porta de host externo – nenhuma atividade | - | - | ATIVADO |
| Conexão de porta de host externo - atividade | - | - | Piscando |
| Erro de metadados do módulo de controlador | Piscando | - | - |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Indicador de unidade de disco LED para o gabinete principal e um gabinete EBOD

O dispositivo StorSimple tem unidades de disco localizadas no compartimento principal e o compartimento EBOD. Cada unidade de disco contém monitoramento LED do indicador, conforme descrito nesta seção. 

Para as unidades de disco, o status da unidade é indicado por um verde LED e um LED vermelho-âmbar montado na frente de cada módulo de carrier de unidade. A ilustração a seguir mostra essas LED.

  ![LED de unidade de disco][6]
 
Use a tabela a seguir para determinar o estado de cada unidade de disco, que por sua vez afeta o status do LED do painel geral frontal.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Indicador de unidade de disco LED para o compartimento EBOD  

| Status | LED de Okey de atividade (verde) | Falhas LED (vermelho-âmbar) | Associado LED do painel de opções |
|-------|--------------------------|----------------------|-------------------------|
| Nenhuma unidade instalada | DESATIVAR | DESATIVAR | Nenhum |
| Unidade instalada e operacional | Piscando/desativar com atividade | X | Nenhum |
| Conjunto de identidade de dispositivo SCSI circunscrição serviços SES) | ATIVADO | Piscando 1 segundo em/1 segundo desativado | Nenhum |
| Conjunto de bits de falha de dispositivo de SES | ATIVADO | ATIVADO | Falhas lógicas (vermelho) |
| Falha de circuito de controle de energia | DESATIVAR | ATIVADO | Falha de módulo (vermelho) |

## <a name="audible-alarms"></a>Alarmes sonoros  

Um dispositivo StorSimple contém alarmes sonoros associados ao compartimento principal e o compartimento EBOD. Um alarme sonoro está localizado no painel frontal (também conhecido como no painel de operações) de ambos os compartimentos. O alarme sonoro indica quando uma condição de falha está presente. As seguintes condições ativará o alarme:  

- Falha de ventilador ou falha
- Voltagem fora do intervalo
- Sobre ou sob condição de temperatura
- Saturação térmica
- Falhas do sistema
- Falhas lógicas
- Falha da fonte de energia
- Remoção de uma potência refrigeração módulo (PCM)  

A tabela a seguir descreve os diversos estados de alarme.  

### <a name="alarm-states"></a>Estados de alarme  

| Estado de alarme | Ação | Ação com botão Mudo pressionado |
|------------|---------|---------------------------------|
| S0 | Modo normal: silencioso | AlarmeSonoro duas vezes |
| S1 | Modo de falha: 1 segundo em/1 segundo desativado | Transição para S2 ou S3 (consulte Observações) |
| S2 | Lembrar modo: bip intermitente | Nenhum |
| S3 | Modo mudo: silencioso | Nenhum |
| S4 | Modo de falhas críticas: alarme contínuo | Não disponível: sem áudio não está ativo |

> [AZURE.NOTE] 

>  - Em estado de alarme S1, se você não pressionar mudo em 2 minutos, o estado muda automaticamente para S2 ou S3.  
>  - Alarme estados S1-S4 retornam a S0 após a condição de falha está desmarcada.  
>  - Estado de falhas críticas S4 pode ser inserido de qualquer outro estado.  

Você pode ativar mudo do alarme sonoro pressionando o botão Mudo no painel de operações. Silenciar automática ocorrerá após dois minutos se a opção mudo não é operada manualmente. Quando o alarme está mudo, ele continuará som com curtos sinais sonoros intermitentes para indicar que ainda existe um problema. O alarme será silencioso quando todos os problemas estão desmarcados.  

A tabela a seguir descreve as várias condições de alarme.  

### <a name="alarm-conditions"></a>Condições de alarme  

| Status | Gravidade | Alarme | Operações LED do painel |
|--------|---------|--------|----------------|
| Alerta PCM – perda de alimentação de CC de uma única PCM | Falhas – sem perda de redundância | S1 | Falha do módulo|
|Alerta PCM – perda de alimentação de CC de uma única PCM | Falhas – perda de redundância | S1 | Falha do módulo |
| Falhas de ventilador PCM | Falhas – perda de redundância | S1 | Falha do módulo |
| Módulo SBB detectado falhas PCM | Falhas | S1 | Falha do módulo |
| PCM removido | Erro de configuração | Nenhum | Falha do módulo |
| Erro de configuração de compartimento | Falhas – crítica | S1 | Falha do módulo |
| Alerta de temperatura de aviso baixo | Aviso | S1 | Falha do módulo |
| Alerta de temperatura de aviso alto | Aviso | S1 | Falha do módulo |
| Sobre alarme de temperatura | Falhas – crítica | S1 | Falha do módulo |
| Falha de barramento I2C | Falhas – perda de redundância | S1 | Falha do módulo |
| Erro de comunicação (I2C) do painel de opções | Falhas – crítica     | S1 | Falha do módulo |
| Erro do controlador | Falhas – crítica | S1 | Falha do módulo |
| Falha do módulo de interface SBB | Falhas – crítica | S1 | Falha do módulo |
| Falha do módulo de interface SBB – sem módulos funcionando restante | Falhas – crítica | S4 | Falha do módulo |
| Módulo de interface SBB removido | Aviso | Nenhum | Falha do módulo |
| Falha de controle de alimentação de unidade | Aviso – sem perda de energia de unidade | S1 | Falha do módulo |
| Falha de controle de alimentação de unidade | Falhas – críticas; perda de energia de unidade | S1 | Falha do módulo |
| Unidade removida | Aviso | Nenhum | Falha do módulo |
| Energia insuficiente disponível | Aviso | Nenhum | Falha do módulo |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [status e componentes de hardware de StorSimple](storsimple-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png

 
