<properties 
    pageTitle="Como usar o contrato API no Windows Phone Silverlight" 
    description="Como usar o contrato API no Windows Phone Silverlight"    
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

#<a name="how-to-use-the-engagement-api-on-windows-phone-silverlight"></a>Como usar o contrato API no Windows Phone Silverlight

Este documento é um complemento para o documento [como integrar contrato Mobile em seu aplicativo do Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md). Ele fornece nos detalhes de profundidade sobre como usar a API de contrato para relatar as estatísticas de aplicativo.

Se você só quiser contrato para relatar seu aplicativo sessões, atividades, falhas e informações técnicas, a maneira mais simples é fazer todas as suas `PhoneApplicationPage` sub classes herdam a `EngagementPage` classe.

Se você quiser fazer mais, por exemplo, se for necessário relatório eventos específicos do aplicativo, erros e trabalhos, ou se você tiver que relatar atividades do seu aplicativo de maneira diferente do que aquele implementada a `EngagementPage` classes, você precisará usar a API do contrato.

A API do contrato é fornecida pela `EngagementAgent` classe. Você pode obter acesso a esses métodos por meio de `EngagementAgent.Instance`.

Mesmo se o módulo do agente não foi inicializado, cada chamada à API é adiada e será executada novamente quando o agente está disponível.

##<a name="engagement-concepts"></a>Conceitos de contrato

As seguintes partes refinar os conceitos de contrato Mobile para a plataforma do Windows Phone.

### <a name="session-and-activity"></a>`Session`e`Activity`

Uma *atividade* geralmente está associado com uma página do aplicativo, que é dizer que a *atividade* inicia quando a página é exibida e para quando a página é fechada: esse é o caso quando o SDK do contrato é integrado usando o `EngagementPage` classe.

Mas *atividades* também pode ser controlado manualmente usando a API do contrato. Isso permite dividir uma determinada página em várias partes de sub para obter mais detalhes sobre o uso desta página (por exemplo, para conhecidos frequência e por quanto tempo as caixas de diálogo são usadas dentro desta página).

##<a name="reporting-activities"></a>Atividades de relatórios

### <a name="user-starts-a-new-activity"></a>Usuário inicia uma nova atividade

#### <a name="reference"></a>Referência

            void StartActivity(string name, Dictionary<object, object> extras = null)

Você precisa chamar `StartActivity()` sempre que as alterações de atividade do usuário. A primeira chamada para essa função inicia uma nova sessão do usuário.

> [AZURE.IMPORTANT] O SDK automaticamente chamar o método de EndActivity quando o aplicativo estiver fechado. Portanto, é altamente recomendável para chamar o método de StartActivity sempre que a atividade a alteração de usuário e nunca chamar o método EndActivity, desde que chamar esse método força a sessão atual para ser concluída.

#### <a name="example"></a>Exemplo

            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>Usuário encerra sua atividade atual

#### <a name="reference"></a>Referência

            void EndActivity()

Você precisa chamar `EndActivity()` pelo menos uma vez quando o usuário termina sua última atividade. Isso informa o SDK do contrato que o usuário está ocioso no momento e que a sessão do usuário precisam ser fechado uma vez o tempo limite da sessão expirará (se você chamar `StartActivity()` antes que expire o tempo limite de sessão, a sessão é simplesmente continuação).

#### <a name="example"></a>Exemplo

            EngagementAgent.Instance.EndActivity();

##<a name="reporting-jobs"></a>Trabalhos de relatório

### <a name="start-a-job"></a>Iniciar um trabalho

#### <a name="reference"></a>Referência

            void StartJob(string name, Dictionary<object, object> extras = null)

Você pode usar o trabalho para controlar tarefas de alguns durante um período de tempo.

#### <a name="example"></a>Exemplo

            // An upload begins...
            
            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");
            
            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>Encerrar um trabalho

#### <a name="reference"></a>Referência

            void EndJob(string name)

Assim que uma tarefa controlada por um trabalho foi encerrada, você deve chamar o método EndJob para este trabalho, fornecendo o nome do trabalho.

#### <a name="example"></a>Exemplo

            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends
            
            EngagementAgent.Instance.EndJob("uploadData");

##<a name="reporting-events"></a>Relatar eventos

Há três tipos de eventos:

-   Eventos autônomo
-   Eventos de sessão
-   Eventos de trabalho

### <a name="standalone-events"></a>Eventos autônomo

#### <a name="reference"></a>Referência

            void SendEvent(string name, Dictionary<object, object> extras = null)

Eventos autônomo podem ocorrer fora do contexto de uma sessão.

#### <a name="example"></a>Exemplo

            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>Eventos de sessão

#### <a name="reference"></a>Referência

            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Eventos de sessão geralmente são usados para relatar as ações executadas por um usuário durante a sessão.

#### <a name="example"></a>Exemplo

**Sem dados:**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");
            
            // or
            
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Com dados:**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>Eventos de trabalho

#### <a name="reference"></a>Referência

            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Eventos de trabalho geralmente são usados para relatar as ações executadas por um usuário durante um trabalho.

#### <a name="example"></a>Exemplo

            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##<a name="reporting-errors"></a>Relatório de erros

Há três tipos de erros:

-   Erros de autônomo
-   Erros de sessão
-   Erros do trabalho

### <a name="standalone-errors"></a>Erros de autônomo

#### <a name="reference"></a>Referência

            void SendError(string name, Dictionary<object, object> extras = null)

Ao contrário dos erros de sessão, podem ocorrer erros de autônomo fora do contexto de uma sessão.

#### <a name="example"></a>Exemplo

            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>Erros de sessão

#### <a name="reference"></a>Referência

            void SendSessionError(string name, Dictionary<object, object> extras = null)

Erros de sessão geralmente são usados para relatar os erros afetar o usuário durante a sessão.

#### <a name="example"></a>Exemplo

            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>Erros do trabalho

#### <a name="reference"></a>Referência

            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Erros podem estar relacionados a um trabalho em execução em vez de serem relacionadas a sessão de usuário atual.

#### <a name="example"></a>Exemplo

            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##<a name="reporting-crashes"></a>Falhas de relatório

O agente fornece dois métodos para lidar com falhas.

### <a name="send-an-exception"></a>Enviar uma exceção

#### <a name="reference"></a>Referência

            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>Exemplo

Você pode enviar uma exceção a qualquer momento chamando:

            EngagementAgent.Instance.SendCrash(aCatchedException);

Você também pode usar um parâmetro opcional para finalizar a sessão de contrato ao mesmo tempo que enviar a falha. Para fazer isso, ligue para:

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Se você fizer isso, a sessão e trabalhos serão fechados logo após o envio da falha.

### <a name="send-an-unhandled-exception"></a>Enviar uma exceção não tratada

#### <a name="reference"></a>Referência

            void SendCrash(ApplicationUnhandledExceptionEventArgs e)

Contrato também fornece um método para enviar exceções não tratadas. Isso é especialmente útil quando usada dentro do manipulador de eventos UnhandledException do silverlight.

Esta será método **sempre** encerrar a sessão de contrato e trabalhos após a chamada.

#### <a name="example"></a>Exemplo

Você pode usá-lo para implementar seu próprio manipulador de UnhandledException (especialmente se você desativou a recurso de contrato de relatório automática de falhas). Por exemplo, na `Application_UnhandledException` método do `App.xaml.cs` arquivo:

            // In your App.xaml.cs file
            
            // Code to execute on Unhandled Exceptions
            private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
            {
              // your own code
            
              EngagementAgent.Instance.SendCrash(e);
            }

##<a name="onactivated"></a>OnActivated

### <a name="reference"></a>Referência

            void OnActivated(ActivatedEventArgs e)

Quando o usuário navega encaminhar, longe de um aplicativo, após o evento Deactivated é gerado, o sistema operacional tentará colocar o aplicativo em um estado inativo. Em seguida, o aplicativo é exclusão. Esse processo um aplicativo é encerrado, mas alguns dados sobre o estado do aplicativo e as páginas individuais dentro do aplicativo são preservados.

Você precisa inserir `EngagementAgent.Instance.OnActivated(e)` no `Application_Activated` método do arquivo App.xaml.cs para redefinir o agente de contrato quando o aplicativo foi inativo.

### <a name="example"></a>Exemplo

            // Inside your App.xaml.cs file
            
            // Code to execute when the application is activated (brought to foreground)
            // This code will not execute when the application is first launched
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
              EngagementAgent.Instance.OnActivated(e);
            }

##<a name="device-id"></a>Id do dispositivo

            String GetDeviceId()

Você pode obter a identificação de dispositivo de contrato chamando este método.

##<a name="extras-parameters"></a>Parâmetros de extras

Dados aleatório podem ser anexados a um evento, um erro, uma atividade ou um trabalho. Esses dados podem ser estruturados usando um dicionário. Chaves e valores podem ser de qualquer tipo.

Dados extras são serializados para que se você quiser inserir seu próprio tipo em extras você precise adicionar um contrato de dados para este tipo.

### <a name="example"></a>Exemplo

Vamos criar uma nova classe "Pessoa".

            using System.Runtime.Serialization;
            
            namespace Engagement.Agent
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }
            
                // Properties
            
                [DataMember]
                public int Age
                {
                  get;
                  set;
                }
            
                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

Em seguida, adicionaremos uma `Person` instância para um extra.

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);
            
            EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING] Se você colocar outros tipos de objetos, verifique se que seu método ToString () é implementado para retornar uma cadeia de caracteres legível humana.

### <a name="limits"></a>Limites

#### <a name="keys"></a>Chaves

Cada chave no objeto deve corresponder a seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Isso significa que teclas devem começar com pelo menos uma letra, seguida por letras, dígitos ou sublinhados (\_).

#### <a name="size"></a>Tamanho

Extras limitam-se aos caracteres de **1024** por chamada.

##<a name="reporting-application-information"></a>Relatórios de informações do aplicativo

### <a name="reference"></a>Referência

            void SendAppInfo(Dictionary<object, object> appInfos)

Você pode relatar manualmente função acompanhamento informações (ou qualquer outra informação específica de aplicativo) usando o SendAppInfo().

Observe que essas informações podem ser enviadas incremental: somente o valor mais recente para uma determinada chave será mantido para um determinado dispositivo. Como o evento extras, usar um dicionário\<objeto, objeto\> para anexar informações.

### <a name="example"></a>Exemplo

            Dictionary<object, object> appInfo = new Dictionary<object, object>()
            {
               {"subscription", "2013-12-07"},
               {"premium", "true"}
            };
            
            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Limites

#### <a name="keys"></a>Chaves

Cada chave no objeto deve corresponder a seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Isso significa que teclas devem começar com pelo menos uma letra, seguida por letras, dígitos ou sublinhados (\_).

#### <a name="size"></a>Tamanho

Informações de aplicativo limitam-se aos caracteres de **1024** por chamada.

No exemplo anterior, o JSON enviado para o servidor é 44 caracteres:

            {"subscription":"2013-12-07","premium":"true"}
 
##<a name="logging"></a>Registro em log
###<a name="enable-logging"></a>Habilitar o log

O SDK pode ser configurado para gerar logs de teste no console de IDE.
Esses registros não são ativados por padrão. Para personalizar isso, atualizar a propriedade `EngagementAgent.Instance.TestLogEnabled` para uma do valor disponível a `EngagementTestLogLevel` enumeração, por exemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();
