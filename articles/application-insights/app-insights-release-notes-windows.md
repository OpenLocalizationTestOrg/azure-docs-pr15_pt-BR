<properties 
    pageTitle="Notas de versão do aplicativo ideias para Windows" 
    description="As atualizações mais recentes para o SDK do Windows Store." 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/12/2016" 
    ms.author="joshweb"/>
 
# <a name="release-notes-for-application-insights-sdk-for-windows-phone-and-store"></a>Notas de versão do aplicativo ideias SDK para Windows Phone e armazenar

O SDK do aplicativo ideias envia telemetria sobre seu aplicativo ao vivo para [Obtenção de informações do aplicativo](https://azure.microsoft.com/services/application-insights/), onde você pode analisar seu uso e o desempenho.


## <a name="version-111"></a>Versão 1.1.1

### <a name="windows-sdk"></a>Windows SDK

- Corrigi um jeito durante falha ao usar Silverlight SDK do Windows Phone. Após essa alteração, qualquer falha que acontece posteriores ao ~ 2 segundos após a chamada para WindowsAppInitialier.InitializeAsync(...) serão persistentes para disco e será enviado na próxima vez que o aplicativo for iniciado. Se ocorrer uma falha antes ~ 2 segundos após a chamada, ele será ignorado.  
- Defina dependências do NuGet para uma versão específica de núcleo e Microsoft.ApplicationInsights.PersistenceChannel (v1.2.3).   

### <a name="core-sdk"></a>Core SDK

- Núcleo é gerenciado no github. Notas de versão futura do SDK Core podem ser encontradas [na github](http://github.com/Microsoft/ApplicationInsights-dotnet/releases)

## <a name="version-11"></a>Versão 1.1

### <a name="core-sdk"></a>Core SDK

- SDK agora apresenta o novo tipo de telemetria ```DependencyTelemetry``` que contém informações sobre chamada de dependência de aplicativo
- Novo método ```TelemetryClient.TrackDependency``` permite enviar informações sobre chamadas de dependência de aplicativo

## <a name="version-100"></a>Versão 1.0.0

### <a name="windows-app-sdk"></a>SDK de aplicativo do Windows

- Inicialização de novo para os aplicativos do Windows. Novo `WindowsAppInitializer` classe com `InitializeAsync()` método permite inicialização inicialização da coleção de SDK. Essa alteração permitem um controle mais preciso e melhorias de desempenho de inicialização do aplicativo significativa sobre técnica ApplicationInsights.config anterior.
- DeveloperMode não está mais automaticamente está definido. Para alterar o comportamento de DeveloperMode que você deverá especificar em código.
- Pacote NuGet já não insere ApplicationInsights.config. Recomendamos utilizar o novo WindowsAppInitializer ao adicionar manualmente o pacote do NuGet.
- ApplicationInsights.config lê somente `<InstrumentationKey>`, todas as outras configurações são ignoradas em preferência para configurações de WindowsAppInitializer.
- Mercado de armazenamento será automaticamente coletada pelo SDK.
- Muitas correções de bugs, melhorias de estabilidade e aperfeiçoamentos de desempenho.

### <a name="core-sdk"></a>Core SDK

- Arquivo de ApplicationInsights.config não está mais requiered. E não adicionado pelo pacote NuGet. Configuração pode ser totalmente especificada no código.
- Pacote NuGet já não irá adicionar um arquivo de destinos à sua solução. Isso remove a configuração automática de DeveloperMode durante uma compilação de depuração. DeveloperMode deve ser definida manualmente no código.

## <a name="version-017"></a>Versão 0.17

### <a name="windows-app-sdk"></a>SDK de aplicativo do Windows

- SDK do aplicativo Windows agora dá suporte a Universal aplicativos do Windows criados em relação a visualização técnica do Windows 10 e com RC de 2015 VS.

### <a name="core-sdk"></a>Core SDK

- Padrões de TelemetryClient ao inicializar com o InMemoryChannel.
- Nova API adicionado, `TelemetryClient.Flush()`. Este método de liberação acionará um carregamento de bloqueio imediato de telemetria todos conectado ao cliente. Isso permite disparo manual de carregamento antes do desligamento de processo.
- Pacote do NuGet adicionado um destino de .net 4,5. Este destino não tem nenhuma dependência externa (removidos BCL e EventSource dependências).

## <a name="version-016"></a>Versão 0,16 

visualização de 2015-04-28

- SDK agora dá suporte a plataforma de destino DNX para habilitar o monitoramento de aplicativos de [núcleo do .NET framework](http://www.dotnetfoundation.org/NETCore5) .
- Instâncias de ```TelemetryClient``` não chave de cache instrumentação mais. Agora, se a chave de instrumentação não foi definida ```TelemetryClient``` explicitamente ```InstrumentationKey``` retornará null. Ela corrige um problema ao definir ```TelemetryConfiguration.Active.InstrumentationKey``` após alguns telemetria já foi coletada, módulos de telemetria como coletores de dependência, web solicitações coleta e desempenho contadores Coletores de dados usarão a nova chave de instrumentação.

## <a name="version-015"></a>Versão 0,15

- Nova propriedade ```Operation.SyntheticSource``` agora disponível em ```TelemetryContext```. Agora você pode marcar os itens de telemetria como "não o tráfego de um usuário real" e especificar como esse tráfego foi gerado. Como exemplo definindo essa propriedade, você pode distinguir tráfego de sua automação de teste de tráfego de teste de carga.
- Lógica de canal foi movida para o NuGet separado chamado Microsoft.ApplicationInsights.PersistenceChannel. Canal padrão agora é chamado de InMemoryChannel
- Novo método ```TelemetryClient.Flush``` permite liberar itens de telemetria do buffer sincronia

## <a name="version-013"></a>Versão 0.13

Não há notas de versão para versões mais antigas disponíveis. 
