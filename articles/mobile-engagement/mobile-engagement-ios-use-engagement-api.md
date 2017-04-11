<properties
    pageTitle="Como usar a API de contrato no iOS"
    description="IOS mais recente SDK - como usar a API de contrato no iOS"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />


#<a name="how-to-use-the-engagement-api-on-ios"></a>Como usar a API de contrato no iOS

Este documento é um complemento para o documento como integrar contrato IOS: fornece nos detalhes de profundidade sobre como usar a API de contrato para relatar as estatísticas de aplicativo.

Lembre-se de que se você só quiser contrato para relatar seu aplicativo sessões, atividades, falhas e informações técnicas, em seguida, a maneira mais simples é criar todos os seu personalizado `UIViewController` objetos herdam correspondente `EngagementViewController` classe.

Se você quiser fazer mais, por exemplo, se for necessário relatório eventos específicos do aplicativo, erros e trabalhos, ou se você tiver que relatar atividades do seu aplicativo de maneira diferente do que aquele implementada a `EngagementViewController` classes, você precisará usar a API do contrato.

A API do contrato é fornecida pela `EngagementAgent` classe. Uma instância desta classe pode ser recuperada chamando a `[EngagementAgent shared]` método estático (Observe que o `EngagementAgent` objeto retornado é um único).

Antes de quaisquer chamadas de API, o `EngagementAgent` objeto deve ser inicializado chamando o método`[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`

##<a name="engagement-concepts"></a>Conceitos de contrato

As seguintes partes refinar os [Mobile contrato conceitos](mobile-engagement-concepts.md) comuns para a plataforma do iOS.

### <a name="session-and-activity"></a>`Session`e`Activity`

Uma *atividade* geralmente está associado com uma tela do aplicativo, que é dizer que a *atividade* inicia quando a tela é exibida e para quando a tela estiver fechada: esse é o caso quando o SDK do contrato é integrado usando o `EngagementViewController` classes.

Mas *atividades* também pode ser controlado manualmente usando a API do contrato. Isso permite dividir uma determinada tela em várias partes de sub para obter mais detalhes sobre o uso dessa tela (por exemplo, para conhecidos frequência e por quanto tempo as caixas de diálogo são usadas dentro esta tela).

##<a name="reporting-activities"></a>Atividades de relatórios

### <a name="user-starts-a-new-activity"></a>Usuário inicia uma nova atividade

            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

Você precisa chamar `startActivity()` sempre que as alterações de atividade do usuário. A primeira chamada para essa função inicia uma nova sessão do usuário.

### <a name="user-ends-his-current-activity"></a>Usuário encerra sua atividade atual

            [[EngagementAgent shared] endActivity];

> [AZURE.WARNING] Você **nunca** deve ligar para esta função por si mesmo, exceto se você deseja dividir um uso do seu aplicativo em várias sessões: uma chamada para essa função seria encerrar a sessão atual imediatamente, portanto, uma chamada subsequente para `startActivity()` seria iniciar uma nova sessão. Esta função é chamada automaticamente pelo SDK quando seu aplicativo estiver fechado.

##<a name="reporting-events"></a>Relatar eventos

### <a name="session-events"></a>Eventos de sessão

Eventos de sessão geralmente são usados para relatar as ações executadas por um usuário durante a sessão.

**Exemplo sem dados extras:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**Exemplo com dados extras:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>Eventos autônomo

Ao contrário dos eventos de sessão, eventos autônomo podem ser usados fora do contexto de uma sessão.

**Exemplo:**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

##<a name="reporting-errors"></a>Relatório de erros

### <a name="session-errors"></a>Erros de sessão

Erros de sessão geralmente são usados para relatar os erros afetar o usuário durante a sessão.

**Exemplo:**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>Erros de autônomo

Ao contrário dos erros de sessão, erros de autônomo podem ser usados fora do contexto de uma sessão.

**Exemplo:**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

##<a name="reporting-jobs"></a>Trabalhos de relatório

**Exemplo:**

Suponha que você deseja relatar a duração do processo de logon:

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>Relatório de erros durante um trabalho

Erros podem estar relacionados a um trabalho em execução em vez de serem relacionadas a sessão de usuário atual.

**Exemplo:**

Suponha que você deseja relatar um erro durante o processo de logon:

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>Eventos durante um trabalho

Eventos podem estar relacionados a um trabalho em execução em vez de serem relacionadas a sessão de usuário atual.

**Exemplo:**

Suponha que temos uma rede social e usamos um trabalho de relatório o tempo total durante o qual o usuário está conectado ao servidor. O usuário pode receber mensagens de seus amigos, esse é um evento de trabalho.

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

##<a name="extra-parameters"></a>Parâmetros extras

Dados aleatório podem ser anexados eventos, erros, atividades e trabalhos.

Esses dados podem ser estruturados, ele usa a classe de NSDictionary do iOS.

Observe que extras podem conter `arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` ou outros `NSDictionary` instâncias.

> [AZURE.NOTE] O parâmetro extra é serializado em JSON. Se você quiser passar diferentes objetos que foram descritos acima, você deve implementar o seguinte método na sua classe:
>
             -(NSString*)JSONRepresentation;
>
> O método deve retornar uma representação JSON de seu objeto.

### <a name="example"></a>Exemplo

    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>Limites

#### <a name="keys"></a>Chaves

Cada chave a `NSDictionary` deve corresponder a seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Isso significa que teclas devem começar com pelo menos uma letra, seguida por letras, dígitos ou sublinhados (\_).

#### <a name="size"></a>Tamanho

Extras limitam-se aos caracteres de **1024** por chamada (uma vez codificado em JSON pelo agente do contrato).

No exemplo anterior, o JSON enviado para o servidor é 58 caracteres:

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##<a name="reporting-application-information"></a>Relatórios de informações do aplicativo

Você pode relatar manualmente acompanhamento informações (ou qualquer outra informação específica de aplicativo) usando o `sendAppInfo:` função.

Observe que essas informações podem ser enviadas incremental: somente o valor mais recente para uma determinada chave será mantido para um determinado dispositivo.

Extras do evento, como o `NSDictionary` classe é usada para abstratos informações do aplicativo, observe que matrizes ou dicionários sub serão tratados como cadeias de caracteres simples (usando serialização JSON).

**Exemplo:**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>Limites

#### <a name="keys"></a>Chaves

Cada chave a `NSDictionary` deve corresponder a seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Isso significa que teclas devem começar com pelo menos uma letra, seguida por letras, dígitos ou sublinhados (\_).

#### <a name="size"></a>Tamanho

Informações de aplicativo limitam-se aos caracteres de **1024** por chamada (uma vez codificado em JSON pelo agente do contrato).

No exemplo anterior, o JSON enviado para o servidor é 44 caracteres:

    {"birthdate":"1983-12-07","gender":"female"}
