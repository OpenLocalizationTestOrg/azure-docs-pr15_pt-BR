<properties
    pageTitle="Como usar o API do contrato no Android"
    description="SDK mais recente do Android - como usar o contrato API no Android"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="piyushjo;ricksal" />

#<a name="how-to-use-the-engagement-api-on-android"></a>Como usar o API do contrato no Android

Este documento é um complemento para o documento [Opções avançadas de relatório para Android Mobile contrato SDK](mobile-engagement-android-advanced-reporting.md). Ele fornece nos detalhes de profundidade sobre como usar a API de contrato para relatar as estatísticas de aplicativo.

Lembre-se de que se você só quiser contrato para relatar seu aplicativo sessões, atividades, falhas e informações técnicas, em seguida, a maneira mais simples é fazer todas as suas `Activity` sub classes herdam correspondente `EngagementActivity` classe.

Se você quiser fazer mais, por exemplo, se for necessário relatório eventos específicos do aplicativo, erros e trabalhos, ou se você tiver que relatar atividades do seu aplicativo de maneira diferente do que aquele implementada a `EngagementActivity` classes, você precisará usar a API do contrato.

A API do contrato é fornecida pela `EngagementAgent` classe. Uma instância desta classe pode ser recuperada chamando a `EngagementAgent.getInstance(Context)` método estático (Observe que o `EngagementAgent` objeto retornado é um único).

##<a name="engagement-concepts"></a>Conceitos de contrato

As seguintes partes refinar os comuns [Mobile contrato conceitos](mobile-engagement-concepts.md), para a plataforma Android.

### <a name="session-and-activity"></a>`Session`e`Activity`

Se o usuário permanecer ociosa entre duas *atividades*de mais de alguns segundos, em seguida, sua sequência de *atividades* é dividida em duas distintas *sessões*. Estas alguns segundos são chamados o "limite de sessão".

Uma *atividade* geralmente está associado com uma tela do aplicativo, que é dizer que a *atividade* inicia quando a tela é exibida e para quando a tela estiver fechada: esse é o caso quando o SDK do contrato é integrado usando o `EngagementActivity` classes.

Mas *atividades* também pode ser controlado manualmente usando a API do contrato. Isso permite dividir uma determinada tela em várias partes de sub para obter mais detalhes sobre o uso dessa tela (por exemplo, para conhecidos frequência e por quanto tempo as caixas de diálogo são usadas dentro esta tela).

##<a name="reporting-activities"></a>Atividades de relatórios

> [AZURE.IMPORTANT] Não é necessário relatório atividades, como descrito nesta seção se você estiver usando o `EngagementActivity` classe e suas variantes, como explicado em como integrar o contrato no documento Android.

### <a name="user-starts-a-new-activity"></a>Usuário inicia uma nova atividade

            EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
            // Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

Você precisa chamar `startActivity()` sempre que as alterações de atividade do usuário. A primeira chamada para essa função inicia uma nova sessão do usuário.

O melhor lugar para chamar esta função é em cada atividade `onResume` retorno de chamada.

### <a name="user-ends-his-current-activity"></a>Usuário encerra sua atividade atual

            EngagementAgent.getInstance(this).endActivity();

Você precisa chamar `endActivity()` pelo menos uma vez quando o usuário termina sua última atividade. Isso informa o SDK do contrato que o usuário está ocioso no momento e que a sessão do usuário precisam ser fechado uma vez o tempo limite da sessão expirará (se você chamar `startActivity()` antes que expire o tempo limite de sessão, a sessão é retomada simplesmente).

O melhor lugar para chamar esta função é em cada atividade `onPause` retorno de chamada.

##<a name="reporting-events"></a>Relatar eventos

### <a name="session-events"></a>Eventos de sessão

Eventos de sessão geralmente são usados para relatar as ações executadas por um usuário durante a sessão.

**Exemplo sem dados extras:**

            public MyActivity extends EngagementActivity {
               [...]
               @Override
               public boolean onPrepareOptionsMenu(Menu menu) {
                  getEngagementAgent().sendSessionEvent("menu_shown", null);
               }
               [...]
            }

**Exemplo com dados extras:**

            public MyActivity extends EngagementActivity {
              [...]
              @Override
              public boolean onMenuItemSelected(int featureId, MenuItem item) {
                Bundle extras = new Bundle();
                extras.putInt("id", item.getItemId());
                getEngagementAgent().sendSessionEvent("menu_selected", extras);
              }
              [...]
            }

### <a name="standalone-events"></a>Eventos autônomo

Ao contrário dos eventos de sessão, eventos de autônomo podem ocorrer fora do contexto de uma sessão.

**Exemplo:**

Suponha que você deseja relatar eventos que ocorrem quando um receptor de difusão é acionado:

            /** Triggered by Intent.ACTION_BATTERY_LOW */
            public BatteryLowReceiver extends BroadcastReceiver {
              [...]
              @Override
              public void onReceive(Context context, Intent intent) {
                EngagementAgent.getInstance(context).sendEvent("battery_low", null);
              }
              [...]
            }

##<a name="reporting-errors"></a>Relatório de erros

### <a name="session-errors"></a>Erros de sessão

Erros de sessão geralmente são usados para relatar os erros afetar o usuário durante a sessão.

**Exemplo:**

            /** The user has entered invalid data in a form */
            public MyActivity extends EngagementActivity {
              [...]
              public void onMyFormSubmitted(MyForm form) {
                [...]
                /* The user has entered an invalid email address */
                getEngagementAgent().sendSessionError("sign_up_email", null);
                [...]
              }
              [...]
            }

### <a name="standalone-errors"></a>Erros de autônomo

Ao contrário dos erros de sessão, podem ocorrer erros de autônomo fora do contexto de uma sessão.

**Exemplo:**

O exemplo a seguir mostra como informar um erro sempre que a memória se torna baixa no telefone enquanto o processo de aplicativo está em execução.

            public MyApplication extends EngagementApplication {

              @Override
              protected void onApplicationProcessLowMemory() {
                EngagementAgent.getInstance(this).sendError("low_memory", null);
              }
            }

##<a name="reporting-jobs"></a>Trabalhos de relatório

### <a name="example"></a>Exemplo

Suponha que você deseja relatar a duração do processo de logon:

            [...]
            public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has started */
              engagementAgent.startJob("sign_in", null);

              [... sign in ...]

              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="report-errors-during-a-job"></a>Relatório de erros durante um trabalho

Erros podem estar relacionados a um trabalho em execução em vez de serem relacionadas a sessão de usuário atual.

**Exemplo:**

Suponha que você deseja relatar um erro durante você login processo:

[...] Entrar anular pública (contexto contexto,...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has been started */
              engagementAgent.startJob("sign_in", null);

              /* Try to sign in */
              while(true)
                try {
                  trySignin();
                  break;
                }
                catch(Exception e) {
                  /* Report the error to Engagement */
                  engagementAgent.sendJobError("sign_in_error", "sign_in", null);

                  /* Retry after a moment */
                  sleep(2000);
                }
              [...]
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="reporting-events-during-a-job"></a>Relatar eventos durante um trabalho

Eventos podem estar relacionados a um trabalho em execução em vez de serem relacionadas a sessão de usuário atual.

**Exemplo:**

Suponha que temos uma rede social e usamos um trabalho de relatório o tempo total durante o qual o usuário está conectado ao servidor. O usuário pode permanecer conectado no plano de fundo par, quando ele está usando outro aplicativo ou quando o telefone está em suspensão, então não há nenhuma sessão.

O usuário pode receber mensagens de seus amigos, esse é um evento de trabalho.

            [...]
            public void signin(Context context, ...) {
              [...Sign in code...]
              EngagementAgent.getInstance(context).startJob("connection", null);
            }
            [...]
            public void signout(Context context) {
              [...Sign out code...]
              EngagementAgent.getInstance(context).endJob("connection");
            }
            [...]
            public void onMessageReceived(Context context) {
              [...Notify in status bar...]
              EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
            }
            [...]

##<a name="extra-parameters"></a>Parâmetros extras

Dados aleatório podem ser anexados eventos, erros, atividades e trabalhos.

Esses dados podem ser estruturados, ele usa a classe de pacote do Android (na verdade, ele funciona como parâmetros extras no Android propósitos). Observe que um pacote pode conter matrizes ou outro instâncias de pacote.

> [AZURE.IMPORTANT] Se você colocou no parcelable ou serializáveis parâmetros, verifique se suas `toString()` método é implementado para retornar uma cadeia de caracteres legível. Serializáveis classes que contêm os campos não temporárias que não são serializáveis fará Android falha quando você chamará`bundle.putSerializable("key",value);`

> [AZURE.WARNING] Não há suporte para matrizes esparsos nos parâmetros adicionais, ou seja, ele não ser serializado como uma matriz. Você deve convertê-los em matrizes padrão antes de usá-lo em parâmetros extras.

### <a name="example"></a>Exemplo

            Bundle extras = new Bundle();
            extras.putString("video_id", 123);
            extras.putString("ref_click", "http://foobar.com/blog");
            EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### <a name="limits"></a>Limites

#### <a name="keys"></a>Chaves

Cada chave a `Bundle` deve corresponder a seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Isso significa que teclas devem começar com pelo menos uma letra, seguida por letras, dígitos ou sublinhados (\_).

#### <a name="size"></a>Tamanho

Extras limitam-se aos caracteres de **1024** por chamada (uma vez codificado em JSON pelo serviço contrato).

No exemplo anterior, o JSON enviado para o servidor é 58 caracteres:

            {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##<a name="reporting-application-information"></a>Relatórios de informações do aplicativo

Você pode relatar manualmente acompanhamento informações (ou qualquer outra informação específica de aplicativo) usando o `sendAppInfo()` função.

Observe que essas informações podem ser enviadas incremental: somente o valor mais recente para uma determinada chave será mantido para um determinado dispositivo.

Como o evento extras, a classe de pacote é usada para abstratos informações do aplicativo, observe que matrizes ou pacotes sub serão tratados como cadeias de caracteres simples (usando serialização JSON).

### <a name="example"></a>Exemplo

Aqui está um exemplo de código para enviar DataDeNascimento e gênero de usuário:

            Bundle appInfo = new Bundle();
            appInfo.putString("status", "premium");
            appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
            EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### <a name="limits"></a>Limites

#### <a name="keys"></a>Chaves

Cada chave a `Bundle` deve corresponder a seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Isso significa que teclas devem começar com pelo menos uma letra, seguida por letras, dígitos ou sublinhados (\_).

#### <a name="size"></a>Tamanho

Informações de aplicativo limitam-se aos caracteres de **1024** por chamada (uma vez codificado em JSON pelo serviço contrato).

No exemplo anterior, o JSON enviado para o servidor é 44 caracteres:

            {"expiration":"2016-12-07","status":"premium"}
