> [AZURE.SELECTOR]
- [C no Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
- [C no Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
- [C de mbed](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
- [Node. js](../articles/iot-suite/iot-suite-connecting-devices-node.md)

## <a name="scenario-overview"></a>Visão geral do cenário

Neste cenário, você cria um dispositivo que envia telemetria a seguir para o computador remoto [pré-configurado de solução]de monitoramento[lnk-what-are-preconfig-solutions]:

- Temperatura externa
- Temperatura interna
- Umidade

Para manter a simplicidade, o código no dispositivo gera os valores de amostra, mas recomendamos que você estenda a amostra conectando sensores reais para seu dispositivo e enviando telemetria real.

Para concluir este tutorial, você precisa de uma conta do Windows Azure ativa. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Windows Azure][lnk-free-trial].

## <a name="before-you-start"></a>Antes de começar

Antes de escrever qualquer código para seu dispositivo, você deve provisionar a sua solução de pré-configurado monitoramento remota e, em seguida, provisionar um novo dispositivo personalizado nessa solução.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Provisione sua solução de pré-configurado monitoramento remota

O dispositivo que você cria neste tutorial envia dados para uma instância do [monitoramento remoto] [ lnk-remote-monitoring] preconfigurada da solução. Se você já ainda não provisionado o monitoramento remoto preconfigurada solução na sua conta do Azure, siga as etapas abaixo:

1. Na página <https://www.azureiotsuite.com/> , clique em **+** para criar uma nova solução.

2. No painel de **monitoramento remoto** para criar a nova solução, clique em **Selecionar** .

3. Na página **criar remoto de solução de monitoramento** , insira um **nome da solução** de sua escolha, selecione a **região** em que você deseja implantar para e selecione a assinatura do Windows Azure para ser usado. Em seguida, clique em **Create solution**.

4. Aguarde até que o processo de provisionamento for concluído.

> [AZURE.WARNING] As soluções pré-configurado usam serviços do Azure faturáveis. Certifique-se de remover a solução pré-configurada da sua assinatura quando terminar com ele para evitar quaisquer encargos desnecessários. Completamente, você pode remover uma solução pré-configurada de sua assinatura do visitando a página <https://www.azureiotsuite.com/> .

Quando o processo de provisionamento para a solução de monitoramento remoto estiver concluída, clique em **Iniciar** para abrir o painel de solução no seu navegador.

![][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Provisionar o seu dispositivo na solução de monitoramento remoto

> [AZURE.NOTE] Se você já tiver provisionado um dispositivo em sua solução, você poderá ignorar esta etapa. Você precisará saber as credenciais de dispositivo, quando você cria o aplicativo cliente.

Para um dispositivo conectar-se à solução pré-configurado, ela deve se identificar para o IoT Hub usando credenciais válidas. É possível recuperar as credenciais do dispositivo do painel solução. Você pode incluir as credenciais de dispositivo em seu aplicativo de cliente posteriormente neste tutorial. 

Para adicionar um novo dispositivo à sua solução de monitoramento remota, conclua as etapas a seguir no painel solução:

1.  No canto inferior esquerdo do painel de controle, clique em **Adicionar um dispositivo**.

    ![][1]

2.  No painel do **Dispositivo personalizado** , clique em **Adicionar novo**.

    ![][2]

3.  Escolha **Deixe-me definir meu próprio ID do dispositivo**, insira uma identificação do dispositivo como **mydevice**, clique em **Verificar ID** para verificar que esse nome não estiver em uso e, em seguida, clique em **criar** para provisionar o dispositivo.

    ![][3]

5. Anote as credenciais do dispositivo (ID do dispositivo, o nome de host do IoT Hub e chave de dispositivo), seu aplicativo cliente precisa que eles se conectem à solução de monitoração remota. Clique em **concluído**.

    ![][4]

6. Verifique se o que seu dispositivo exibe na seção dispositivos. O status do dispositivo está **pendente** até que o dispositivo estabelece uma conexão à solução de monitoração remota.

    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/