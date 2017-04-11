<properties
    pageTitle="Web de contrato móvel Azure SDK APIs | Microsoft Azure"
    description="As últimas atualizações e procedimentos para o SDK do Web para contrato de celular do Azure"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="piyushjo" />

# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>Usar a API de contrato do Azure Mobile em um aplicativo web

Este documento é uma adição ao documento que explica como [integrar contrato Mobile em um aplicativo web](mobile-engagement-web-integrate-engagement.md). Ele fornece detalhes detalhadas sobre como usar a API do Azure Mobile contrato para relatar as estatísticas de aplicativo.

A API de contrato Mobile é fornecida pela `engagement.agent` objeto. O padrão do Azure Mobile contrato Web SDK alias está `engagement`. Você pode redefinir esse alias da configuração SDK.

## <a name="mobile-engagement-concepts"></a>Conceitos de contrato de celular

As seguintes partes refinar comuns [conceitos de contrato Mobile](mobile-engagement-concepts.md) para a plataforma da web.

### <a name="session-and-activity"></a>`Session`e`Activity`

Se o usuário permanecer ocioso por mais de alguns segundos entre duas atividades, sequência do usuário de atividades é dividida em duas sessões distintas. Estas alguns segundos são chamados o tempo limite da sessão.

Se o seu aplicativo web não declara final de atividades do usuário por si só (chamando a `engagement.agent.endActivity` função), o servidor de contrato Mobile expira automaticamente a sessão do usuário em três minutos após a página do aplicativo estiver fechada. Isso se chama o limite de sessão de servidor.

### `Crash`

Relatórios automatizados de exceções não detectadas de JavaScript não são criados por padrão. No entanto, você poderá reportar falhas manualmente usando o `sendCrash` funcionar (consulte a seção sobre relatórios de falhas).

## <a name="reporting-activities"></a>Atividades de relatórios

Relatórios sobre atividades do usuário incluem quando um usuário inicia uma nova atividade e quando o usuário termina a atividade atual.

### <a name="user-starts-a-new-activity"></a>Usuário inicia uma nova atividade

    engagement.agent.startActivity("MyUserActivity");

Você precisa chamar `startActivity()` cada atividade de usuário de tempo é alterado. A primeira chamada para essa função inicia uma nova sessão do usuário.

### <a name="user-ends-the-current-activity"></a>Usuário encerra a atividade atual

    engagement.agent.endActivity();

Você precisa chamar `endActivity()` pelo menos uma vez quando o usuário termina sua última atividade. Isso informa o SDK do Mobile contrato da Web que o usuário está ocioso no momento, e que a sessão do usuário precisa ser fechado após o tempo limite de sessão expirar. Se você chamar `startActivity()` antes que expire o tempo limite de sessão, a sessão é retomada simplesmente.

Porque não há nenhuma chamada confiável para quando a janela de navegador é fechada, muitas vezes é difícil ou impossível para capturar final de atividades do usuário dentro de um ambiente da web. É por isso que o servidor de contrato Mobile expira automaticamente a sessão do usuário em três minutos após a página do aplicativo estiver fechada.

## <a name="reporting-events"></a>Relatar eventos

Relatar eventos aborda autônomo e eventos de sessão.

### <a name="session-events"></a>Eventos de sessão

Eventos de sessão geralmente são usados para relatar as ações executadas por um usuário durante a sessão do usuário.

**Exemplo sem dados extras:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**Exemplo com dados extras:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>Eventos autônomo

Ao contrário dos eventos de sessão, eventos de autônomo podem ocorrer fora do contexto de uma sessão.

Para isso, use ``engagement.agent.sendEvent`` em vez de ``engagement.agent.sendSessionEvent``.

## <a name="reporting-errors"></a>Relatório de erros

Relatórios sobre erros aborda erros de sessão e autônomo.

### <a name="session-errors"></a>Erros de sessão

Erros de sessão geralmente são usados para relatar os erros que têm impacto no usuário durante a sessão do usuário.

**Exemplo sem dados extras:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**Exemplo com dados extras:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>Erros de autônomo

Ao contrário dos erros de sessão, podem ocorrer erros de autônomo fora do contexto de uma sessão.

Para isso, use `engagement.agent.sendError` em vez de `engagement.agent.sendSessionError`.

## <a name="reporting-jobs"></a>Trabalhos de relatório

Relatórios sobre capas de trabalhos causa uma falha de relatório e geração de relatórios de erros e eventos que ocorrem durante um trabalho.

**Exemplo:**

Se você quiser monitorar uma solicitação AJAX, você usaria o seguinte:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>Relatório de erros durante um trabalho

Erros podem estar relacionados a um trabalho em execução, em vez de à sessão atual do usuário.

**Exemplo:**

Se você deseja relatar um erro se uma solicitação AJAX falhar:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>Relatar eventos durante um trabalho

Eventos podem estar relacionados a um trabalho em execução, em vez de para a sessão atual do usuário, graças a `engagement.agent.sendJobEvent` função.

Esta função funciona exatamente como `engagement.agent.sendJobError`.

### <a name="reporting-crashes"></a>Falhas de relatório

Use o `sendCrash` função ao relatório trava manualmente.

O `crashid` argumento é uma cadeia de caracteres que identifica o tipo de falha.
O `crash` argumento geralmente é o rastreamento de pilha da falha como uma cadeia de caracteres.

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>Parâmetros extras

Você pode anexar dados aleatório para um evento, erro, atividade ou trabalho.

Os dados podem ser qualquer objeto JSON (mas não uma matriz ou tipo primitivo).

**Exemplo:**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>Limites

Limites que se aplicam a parâmetros extras são nas áreas de expressões regulares para chaves, tipos de valor e tamanho.

#### <a name="keys"></a>Chaves

Cada chave no objeto deve corresponder a seguinte expressão regular:

    ^[a-zA-Z][a-zA-Z_0-9]*

Isso significa que teclas devem começar com pelo menos uma letra, seguido por letras, dígitos ou sublinhados (\_).

#### <a name="values"></a>Valores

Valores limitam-se a cadeia de caracteres, número e tipos de Boolianas.

#### <a name="size"></a>Tamanho

Extras são limitados ao 1.024 caracteres por chamada (após o SDK do Mobile contrato Web codifica em JSON).

## <a name="reporting-application-information"></a>Relatórios de informações do aplicativo

Você pode relatar rastreamento informações (ou outras informações específicas do aplicativo) manualmente usando o `sendAppInfo()` função.

Observe que essas informações podem ser enviadas de forma incremental. Somente o valor mais recente para uma chave específica será mantido para um dispositivo específico.

Como o evento extras, você pode usar qualquer objeto JSON para abstratos informações do aplicativo. Observe que matrizes ou objetos sub são tratados como cadeias de caracteres simples (usando serialização JSON).

**Exemplo:**

Aqui está um exemplo de código para enviar o gênero do usuário e a data de nascimento:

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>Limites

Limites que se aplicam às informações de aplicativo estão nas áreas de expressões regulares para chaves e tamanho.

#### <a name="keys"></a>Chaves

Cada chave no objeto deve corresponder a seguinte expressão regular:

    ^[a-zA-Z][a-zA-Z_0-9]*

Isso significa que teclas devem começar com pelo menos uma letra, seguido por letras, dígitos ou sublinhados (\_).

#### <a name="size"></a>Tamanho

Informações sobre o aplicativo está limitada aos 1.024 caracteres por chamada (após o SDK do Mobile contrato Web codifica em JSON).

No exemplo anterior, o JSON enviado para o servidor é 44 caracteres:

    {"birthdate":"1983-12-07","gender":"female"}
