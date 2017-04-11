<properties 
    pageTitle="Windows Phone SDK do Silverlight procedimentos de atualização" 
    description="Windows Phone SDK do Silverlight procedimentos de atualização para Azure contrato móvel"                  
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede"
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-sdk-upgrade-procedures"></a>Windows Phone SDK do Silverlight procedimentos de atualização

Se você já tiver integrado uma versão mais antiga do nosso SDK em seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Você pode precisar acompanhar vários procedimentos se você perdeu várias versões do SDK. Por exemplo, se você migrar do 0.10.1 para 0.11.0, que você precisa primeiro, siga o procedimento "de 0.9.0 para 0.10.1", em seguida, o procedimento "de 0.10.1 para 0.11.0".

##<a name="from-200-to-330"></a>De 2.0.0 para 3.3.0

### <a name="test-logs"></a>Logs de teste

Logs de console produzidos pelo SDK agora podem ser habilitado/desabilitado/filtrada. Para personalizar isso, atualizar a propriedade `EngagementAgent.Instance.TestLogEnabled` para uma do valor disponível a `EngagementTestLogLevel` enumeração, por exemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

##<a name="from-111-to-200"></a>De 1.1.1 para 2.0.0

A seguir descreve como migrar uma integração SDK do serviço Capptain oferecido pelo Capptain SAS para um aplicativo do Azure Mobile contrato da plataforma. 

> [Azure.IMPORTANT] Capptain e Mobile contrato não são os mesmos serviços e o procedimento abaixo realça apenas como migrar o aplicativo cliente. Migrar o SDK no aplicativo será não migrar seus dados de servidores Capptain aos servidores do contrato de celular

Se você estiver migrando de uma versão anterior, consulte o site do Capptain para migrar para o 1.1.1 primeiro e em seguida, aplique o procedimento a seguir

### <a name="nuget-package"></a>Pacote do NuGet

Substitua **Capptain.WindowsPhone** por **MicrosoftAzure.MobileEngagement** Nuget pacote.

### <a name="applying-mobile-engagement"></a>Aplicando contrato móvel

O SDK usa o termo `Engagement`. Você precisa atualizar seu projeto para corresponder a essa alteração.

Você precisa desinstalar o pacote de nuget Capptain atual. Considere a possibilidade de que todas as suas alterações na pasta de recursos de Capptain serão removidas. Se você quiser manter esses arquivos, em seguida, faça uma cópia delas.

Depois disso, instale o novo pacote do Microsoft Azure Engagement nuget em seu projeto. Você pode encontrá-lo diretamente no [Nuget](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement). Esta ação substitui todos os arquivos de recursos usados pelo contrato e adiciona a nova DLL de contrato para suas referências do projeto.

Você precisa limpar suas referências de projeto excluindo Capptain DLL referências. Se você não fizer isso, a versão do Capptain entrarão em conflito e erros acontecerá.

Se você personalizou Capptain recursos, copie o conteúdo de arquivos antigo e colá-las nos novos arquivos de contrato. Observe que os arquivos xaml e cs devem ser atualizados.

Ao concluir essas etapas, você só precisa substituir as referências de Capptain antigas pelas novas referências de contrato.

1. Todos os namespaces Capptain devem ser atualizados.

    Antes da migração:
    
        using Capptain.Agent;
        using Capptain.Reach;
    
    Após a migração:
    
        using Microsoft.Azure.Engagement;

2. Todas as classes Capptain que contêm "Capptain" devem conter "Contrato".

    Antes da migração:
    
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
    
    Após a migração:
    
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }

3. Para arquivos xaml atributos e Capptain namespace também alterar.

    Antes da migração:
    
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
    
    Após a migração:
    
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>

4. Para os outros recursos como Capptain imagens, observe que eles também foram renomeados para usar "Contrato".

### <a name="application-id--sdk-key"></a>ID do aplicativo / chave SDK

Compromisso utiliza uma cadeia de conexão. Você não precisa especificar uma ID de aplicativo e uma tecla de SDK com contrato de celular, você só precisa especificar uma cadeia de conexão. Você pode configurá-lo no seu arquivo de EngagementConfiguration.

A configuração de contrato pode ser definida no seu `Resources\EngagementConfiguration.xml` arquivo do seu projeto.

Edite esse arquivo para especificar:

-   A cadeia de caracteres de conexão de aplicativo entre marcas `<connectionString>` e `<\connectionString>`.

Se você quiser especificá-lo em tempo de execução, você pode chamar o seguinte método antes da inicialização do agente de contrato:

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        
        /* Initialize Engagement angent with above configuration. */
        EngagementAgent.Instance.Init(engagementConfiguration);

A cadeia de conexão para o seu aplicativo é exibida no Portal de clássico do Azure.

### <a name="items-name-change"></a>Alteração de nome de itens

Todos os itens nomeados *capptain* tem sido chamados *contrato*. Da mesma forma para *Capptain* *contratos*.

Exemplos de itens de Capptain comumente usados:

-   CapptainConfiguration agora denominado EngagementConfiguration
-   CapptainAgent agora denominado EngagementAgent
-   CapptainReach agora denominado EngagementReach
-   CapptainHttpConfig agora denominado EngagementHttpConfig
-   GetCapptainPageName agora denominado GetEngagementPageName

Observe que renomear também afeta métodos substituídos.



 
