<properties 
    pageTitle="Solucionar problemas de obtenção de informações do aplicativo em um projeto de web Java" 
    description="Guia de solução de problemas: monitoramento de aplicativos Java ao vivo com a obtenção de informações do aplicativo." 
    services="application-insights" 
    documentationCenter="java"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/01/2016" 
    ms.author="awills"/>
 
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Solução de problemas e perguntas e respostas para obtenção de informações de aplicativo para Java

Perguntas ou problemas com o [Visual Studio aplicativo obtenção de informações de em Java][java]? Aqui estão algumas dicas.


## <a name="build-errors"></a>Erros de compilação

*Em Eclipse, ao adicionar o SDK de ideias de aplicativo via Maven ou Gradle, posso obter construir ou soma de verificação de erros de validação.*

* Se a dependência <version> elemento está usando um padrão com caracteres curinga (por exemplo, (Maven) `<version>[1.0,)</version>` ou (Gradle) `version:'1.0.+'`), tente especificar uma versão específica em vez disso, como o `1.0.2`. Consulte as [notas de versão](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) para a versão mais recente.

## <a name="no-data"></a>Sem dados 

*Posso adicionado ideias de aplicativo com êxito e executou meu aplicativo, mas nunca vi dados no portal.*

* Aguarde um minuto e clique em Atualizar. Os gráficos atualizar próprios periodicamente, mas você também pode atualizar manualmente. O intervalo de atualização depende o intervalo de tempo do gráfico.
* Verifique se você tem uma chave de instrumentação definida no arquivo ApplicationInsights.xml (na pasta recursos em seu projeto)
* Verifique se há nenhuma `<DisableTelemetry>true</DisableTelemetry>` nó no arquivo xml.
* Em seu firewall, talvez seja necessário abrir portas TCP 80 e 443 para o tráfego de saída para dc.services.visualstudio.com. Consulte a [lista completa de exceções do firewall](app-insights-ip-addresses.md)
* No Microsoft Azure iniciar quadro, examine o mapa de status do serviço. Se houver algumas indicações alertas, aguarde até que elas tem retornado para Okey e, em seguida, feche e abra novamente o seu blade de aplicativo de obtenção de informações do aplicativo.
* Ativar o log para a janela de console IDE, adicionando um `<SDKLogger />` elemento sob o nó raiz no arquivo ApplicationInsights.xml (na pasta recursos em seu projeto) e verificar se há entradas exibirá [Error].
* Certifique-se de que o arquivo de ApplicationInsights.xml correto foi com êxito carregado pelo SDK Java, examinando mensagens de saída do console de uma instrução "arquivo de configuração foi com êxito encontrado".
* Se não for encontrado no arquivo de configuração, marque as mensagens de saída para ver onde o arquivo de configuração está sendo procurado e certifique-se de que o ApplicationInsights.xml encontra-se em um desses locais de pesquisa. Como regra, você pode colocar o arquivo de configuração perto do aplicativo ideias SDK JARs. Por exemplo: em Tomcat, isso significa pasta WEB-INF/lib.



#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Posso usado para ver os dados, mas ele parou

* Verificar o [status blog](http://blogs.msdn.com/b/applicationinsights-status/).
* Você tiver atingido sua cota mensal de pontos de dados? Abra configurações/cota e preços para descobrir. Nesse caso, você pode atualizar seu plano ou pagar por capacidade adicional. Consulte os [preços esquema](https://azure.microsoft.com/pricing/details/application-insights/).

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Não vejo todos os dados que eu estou esperando

* Abra as cotas e preço blade e verifique se [amostragem](app-insights-sampling.md) estiver em operação. (transmissão de 100% significa que amostragem não está em operação.) O serviço de obtenção de informações de aplicativo pode ser definido para aceitar apenas uma fração da telemetria que chega de seu aplicativo. Isso ajuda você a manter dentro de sua cota mensal de telemetria. 

## <a name="no-usage-data"></a>Não há dados de uso

*Vejo dados sobre solicitações e tempos de resposta, mas não há modo de exibição de página, navegador ou dados de usuário.*

Você configurar com sucesso o seu aplicativo para enviar telemetria do servidor. Agora, a próxima etapa é configurar [suas páginas da web para enviar telemetria do navegador da web][usage].

Como alternativa, se o seu cliente é um aplicativo em um [celular ou outro dispositivo][platforms], você pode enviar telemetria de lá. 

Use a mesma chave de instrumentação para configurar seu cliente e servidor telemetria. Os dados aparecerão no mesmo recurso de obtenção de informações do aplicativo, e você poderá correlação eventos de cliente e servidor.



## <a name="disabling-telemetry"></a>Desabilitando Telemetria

*Como desabilitar o conjunto de telemetria?*

No código:

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);


**Ou** 

Atualize ApplicationInsights.xml (na pasta recursos em seu projeto). Adicione o seguinte sob o nó raiz:

    <DisableTelemetry>true</DisableTelemetry>

Usando o método XML, é necessário reiniciar o aplicativo quando você altera o valor.

## <a name="changing-the-target"></a>Alterar o destino

*Como posso alterar qual recurso Azure meu projeto envia dados?*

* [Obtenha a chave de instrumentação do novo recurso.][java]
* Se você adicionou ideias de aplicativo ao seu projeto usando o Kit de ferramentas do Azure para Eclipse, para a direita clicar seu projeto da web, selecione **Azure**, **Configurar ideias de aplicativo**e alterar a chave.
* Caso contrário, atualize a chave em ApplicationInsights.xml na pasta recursos em seu projeto.


## <a name="the-azure-start-screen"></a>Na tela inicial do Azure

*Eu estou vendo [o portal do Azure](https://portal.azure.com). O mapa Diga-me algo sobre o meu aplicativo?*

Não, ela mostra a integridade do Azure servidores em todo o mundo.

*Da placa de iniciar Azure (tela inicial), como posso encontrar dados sobre o meu aplicativo?*

Supondo que você [configurar seu aplicativo para obtenção de informações de aplicativo][java], clique em Procurar, selecione ideias de aplicativo e selecione o recurso de aplicativo que você criou para seu aplicativo. Para obter ainda mais rápido no futuro, você pode fixar seu aplicativo para o quadro de início.

## <a name="intranet-servers"></a>Servidores de intranet

*Pode monitorar um servidor na minha intranet?*

Sim, desde que o servidor pode enviar telemetria para o portal de obtenção de informações de aplicativo através da internet pública. 

Em seu firewall, talvez seja necessário abrir portas TCP 80 e 443 para tráfego de saída para dc.services.visualstudio.com e f5.services.visualstudio.com.

## <a name="data-retention"></a>Retenção de dados 

*Por quanto tempo os dados são mantidos no portal? Ele está seguro?*

Consulte [privacidade e retenção de dados][data].

## <a name="next-steps"></a>Próximas etapas

*Posso configurar aplicativo ideias para meu aplicativo de servidor Java. O que mais posso fazer?*

* [Monitorar a disponibilidade de suas páginas da web][availability]
* [Monitorar o uso da página da web][usage]
* [Controlar o uso e diagnosticar problemas em seus aplicativos de dispositivo][platforms]
* [Escrever código para controlar o uso do aplicativo][track]
* [Capturar os logs de diagnóstico][javalogs]


## <a name="get-help"></a>Obter ajuda

* [Estouro de pilha](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md

 