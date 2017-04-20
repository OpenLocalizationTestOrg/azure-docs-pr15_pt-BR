## <a name="view-device-telemetry-in-the-dashboard"></a>Telemetria do dispositivo de modo de exibição no painel de controle

O painel na solução de monitoramento remoto permite que você exiba de telemetria que seus dispositivos de enviar para IoT Hub.

1. No seu navegador, voltar para o painel de solução de monitoramento remoto, clique em **dispositivos** no painel esquerdo para navegar até a **lista de dispositivos**.

2. Na **lista de dispositivos**, você deverá ver o status do seu dispositivo agora está **em execução**.

    ![][18]

3. Clique em **Painel de controle** , para retornar ao painel, selecione seu dispositivo no **dispositivo para o modo de exibição** lista suspensa para exibir seu telemetria. A telemetria do aplicativo de exemplo é 50 unidades para temperatura interna, 55 unidades para temperatura externa e 50 unidades para umidade. Observe que, por padrão, o painel de controle exibe somente valores de temperatura e umidade.

    ![][img-telemetry]

## <a name="send-a-command-to-your-device"></a>Enviar um comando ao seu dispositivo

O painel na solução de monitoramento remoto permite enviar comandos para os dispositivos por meio do IoT Hub. Por exemplo, na solução de monitoramento remota, você pode enviar um comando para definir a temperatura interna de um dispositivo.

1. Em que o painel de solução monitoramento remoto, clique em **dispositivos** no painel esquerdo para navegar até a **lista de dispositivos**.

2. Clique em **ID do dispositivo** para seu dispositivo na **lista de dispositivos**.

3. No painel de **detalhes do dispositivo** , clique em **comandos**.

    ![][13]

4. No menu suspenso **comando** , selecione **SetTemperature (DefinirTemperatura)**e insira um novo valor de temperatura de **temperatura** . Clique em **Enviar comando** para enviar o comando para o dispositivo.

    ![][14]

    > [AZURE.NOTE] Inicialmente, o histórico de comando mostra o status de comando como **pendentes**. Quando o dispositivo reconhece o comando, o status muda para o **sucesso**.

5. No painel, verifique se que o dispositivo agora está enviando 75 como o novo valor de temperatura.

## <a name="next-steps"></a>Próximas etapas

O artigo [Personalizando preconfigurada soluções] [ lnk-customize] descreve algumas maneiras que você pode estender este exemplo. Extensões possíveis incluem usando sensores reais e implementar comandos adicionais.

Saiba mais sobre as [permissões no site de azureiotsuite.com][lnk-permissions].

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-permissions.md
