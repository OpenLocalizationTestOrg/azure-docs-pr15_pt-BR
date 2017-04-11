<properties
    pageTitle="Conceitos de contrato Mobile | Microsoft Azure"
    description="Azure conceitos de contrato de celular"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement-concepts"></a>Azure conceitos de contrato de celular

Contrato de celular define alguns conceitos comuns a todas as plataformas com suporte. Este artigo descreve brevemente esses conceitos.

Este artigo é um bom começo, se você estiver começando a usar o contrato de celular. Verifique também se ler a documentação específica para a plataforma que você está usando, como ele será refinar os conceitos descritos neste artigo com mais detalhes e exemplos, bem como possíveis limitações.

## <a name="devices-and-users"></a>Dispositivos e usuários
Contrato de celular identifica os usuários gerando um identificador exclusivo para cada dispositivo. Esse identificador é chamado o identificador de dispositivo (ou `deviceid`). Ele é gerado de forma que todos os execução de aplicativos do mesmo dispositivo compartilham o mesmo identificador de dispositivo.

Implicitamente, significa que Mobile contrato considera um dispositivo pertencem a exatamente um usuário e, portanto, usuários e dispositivos são conceitos equivalentes.

## <a name="sessions-and-activities"></a>Sessões e atividades
Uma sessão é um uso do aplicativo executado por um usuário, do horário o usuário começar a usá-lo, até que as paradas de usuário.

Uma atividade é um uso de uma determinada parte sub do aplicativo realizado por um usuário (é geralmente uma tela, mas ele pode ser qualquer adequado para o aplicativo).

Um usuário só pode realizar uma atividade por vez.

Uma atividade é identificada por um nome (limitado a 64 caracteres) e, opcionalmente, pode inserir alguns dados extras (no limite de 1024 bytes).

Sessões são calculadas automaticamente da sequência de atividades realizadas pelos usuários. Uma sessão começa quando o usuário iniciar sua primeira atividade e parar quando ele termina sua última atividade. Isso significa que uma sessão não precisa ser explicitamente iniciado ou interrompido. Em vez disso, atividades são explicitamente iniciadas ou interrompidas. Se nenhuma atividade for relatada, nenhuma sessão é informado.

## <a name="events"></a>Eventos
Eventos são usados para relatar instantâneas ações (como botão pressionado ou artigos lidos por usuários).

Um evento pode estar relacionado à sessão atual, para um trabalho em execução, ou pode ser um evento autônomo.

Um evento é identificado por um nome (limitado a 64 caracteres) e, opcionalmente, pode inserir alguns dados extras (no limite de 1024 bytes).

## <a name="error"></a>Erro
Erros são usados para relatar problemas detectados corretamente pelo aplicativo (como ações do usuário incorretos ou falhas de chamada de API).

Um erro pode estar relacionado à sessão atual, para um trabalho em execução, ou pode ser um erro de autônomo.

Um erro é identificado por um nome (limitado a 64 caracteres) e, opcionalmente, pode inserir alguns dados extras (no limite de 1024 bytes).

## <a name="job"></a>Trabalho
Trabalhos são usados para relatar ações tendo uma duração (como duração das chamadas de API, exibir hora de anúncios, a duração das tarefas de plano de fundo ou a duração de ações do usuário).

Um trabalho não está relacionado a uma sessão, porque uma tarefa pode ser executada em segundo plano, sem qualquer interação do usuário.

Um trabalho é identificado por um nome (limitado a 64 caracteres) e, opcionalmente, pode inserir alguns dados extras (no limite de 1024 bytes).

## <a name="crash"></a>Falhar
Travar é emitidos automaticamente pelo SDK do contrato Mobile para relatar falhas de aplicativo onde problemas não detectados pelo aplicativo torná-la falha.

## <a name="application-information"></a>Informações do aplicativo
Informações de aplicativo (ou informações de aplicativo) é usada para marcar usuários, ou seja, associar alguns dados para os usuários de um aplicativo (Isso é semelhante a cookies da web, exceto que as informações de aplicativo está armazenada no lado do servidor na plataforma Azure Mobile contrato).

Informações de aplicativo podem ser registrada usando a API de SDK do contrato de celular ou usando a plataforma Mobile contrato API do dispositivo.

Informações de aplicativo são um par de chave/valor associado a um dispositivo. A chave é o nome das informações de aplicativo (limitado a 64 ASCII letras [a-zA-Z], números [0-9] e sublinhados [_]). O valor (limitado a 1024 caracteres) pode ser qualquer cadeia de caracteres, o número inteiro, a data (AAAA-MM-dd) ou o booliano (verdadeiro ou falso).

Qualquer número de informações de aplicativo pode ser associado a um dispositivo, dentro dos limites definidos pelos termos de preços do contrato de celular. Para uma determinada chave, Mobile contrato apenas controla os o último conjunto de valor (sem histórico). Configurando ou alterando o valor de uma informação de aplicativo força compromisso de celular para reavalie audiência conjunto de critérios nessas informações de aplicativo (se houver) significando que informações de aplicativo pode ser usado para disparar coloca em tempo real.

## <a name="extra-data"></a>Dados extras
Dados extras (ou extras) são alguns dados aleatório que podem ser anexados eventos, erros, atividades e trabalhos.

Extras são estruturadas de maneira semelhante aos objetos JSON: elas são feitas de uma árvore de pares chave/valor. Teclas são limitadas a 64 ASCII letras [a-zA-Z], [0-9] números e sublinhados [_]) e o tamanho total de extras está limitado a 1024 caracteres (uma vez codificados em JSON pelo SDK do contrato Mobile).

Toda a árvore de pares chave/valor é armazenada como um objeto JSON. No entanto, somente o primeiro nível de chaves/valores é decomposto estejam diretamente acessíveis para algumas funções avançadas como segmentos (por exemplo, você pode facilmente definir um segmento chamado "SciFi ventiladores" que é feito de todos os usuários que enviou pelo menos 10 vezes o evento chamado "content_viewed" com a chave "content_type" definido para o valor "scifi" no último mês). Ele, portanto, é altamente recomendável para enviar apenas extras feitas de listas simples de pares de chave/valor usando valores escalares (por exemplo, cadeias de caracteres, datas, números inteiros ou booliano).

## <a name="next-steps"></a>Próximas etapas

- [Visão geral do Windows SDK Universal para contrato de celular do Azure](mobile-engagement-windows-store-sdk-overview.md)
- [Visão geral do Windows Phone Silverlight SDK para contrato de celular do Azure](mobile-engagement-windows-phone-sdk-overview.md)
- [iOS SDK para contrato de celular do Azure](mobile-engagement-ios-sdk-overview.md)
- [Android SDK do Azure contrato móvel](mobile-engagement-android-sdk-overview.md)
