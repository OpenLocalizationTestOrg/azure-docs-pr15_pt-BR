<properties 
    pageTitle="Criar uma API para aplicativos de lógica" 
    description="Criando uma API personalizada para usar com aplicativos de lógica" 
    authors="jeffhollan" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na" 
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>
    
# <a name="creating-a-custom-api-to-use-with-logic-apps"></a>Criando uma API personalizada para usar com aplicativos de lógica

Se você quiser estender a plataforma de aplicativos de lógica, há várias maneiras que você pode chamar APIs ou sistemas que não estão disponíveis como um dos nossos muitos conectores de fora da caixa.  Uma dessas formas para criar um API App você pode chamar de dentro de um fluxo de trabalho do aplicativo de lógica.

## <a name="helpful-tools"></a>Ferramentas úteis

APIs trabalhar melhor com aplicativos de lógica, é recomendável para gerar um documento [swagger](http://swagger.io) detalha os parâmetros e operações com suporte para sua API.  Há muitas bibliotecas (como [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)) que gerarão automaticamente o swagger para você.  Você também pode usar [TRex](https://github.com/nihaue/TRex) para ajudar a fazer anotações a swagger para funcionar bem com aplicativos de lógica (Exibir nomes, tipos de propriedade, etc.).  Para alguns exemplos de aplicativos de API foram criados para aplicativos de lógica, certifique-se de fazer check-out nosso [repositório GitHub](http://github.com/logicappsio) ou [blog](http://aka.ms/logicappsblog).

## <a name="actions"></a>Ações

A ação básica para um aplicativo de lógica é um controlador que aceitar uma solicitação de HTTP e retornar uma resposta (geralmente 200).  No entanto, há padrões diferentes, que você pode seguir para estender ações de acordo com suas necessidades.

Por padrão o mecanismo de aplicativo de lógica tempo limite expirará uma solicitação após 1 minuto.  No entanto, você pode ter seu API executar ações no que levar mais tempo e tem o mecanismo Aguardar conclusão, seguindo um assíncrono ou webhook padrão detalhada abaixo.

Para ações padrão, simplesmente escreva um método de solicitação HTTP em sua API que é exposta por meio de swagger.  Você pode ver exemplos de aplicativos de API que funcionam com aplicativos de lógica em nosso [repositório GitHub](https://github.com/logicappsio).  A seguir são formas de realizar padrões comuns com um conector personalizado.

### <a name="long-running-actions---async-pattern"></a>Ações de execução demorada - padrão assíncrono

Durante a execução de uma tarefa ou etapa longa, a primeira coisa que você precisa fazer é certificar-se de que o mecanismo sabe que você ainda não expirou. Você também precisa se comunicar com o mecanismo de como ele saberá quando tiver terminado com a tarefa e por fim, você precisa retornar dados relevantes para o mecanismo para que ele possa continuar com o fluxo de trabalho. Você pode concluir que por meio de uma API seguindo o fluxo abaixo. Estas etapas são da ponto de vista da API personalizado:

1. Quando uma solicitação é recebida, volte imediatamente uma resposta (antes de trabalho é feito). Esta resposta será um `202 ACCEPTED` resposta, informando o mecanismo obtido os dados, aceitou a carga e agora está processando. A resposta 202 deve conter os seguintes cabeçalhos: 
 * `location`cabeçalho (obrigatório): Este é um absoluto caminho para os aplicativos de lógica de URL pode usar para verificar o status do trabalho.
 * `retry-after`(opcional, será padrão como 20 para ações). Este é o número de segundos que o mecanismo deverá aguardar antes da URL de cabeçalho de localização para verificar o status de sondagem.

2. Quando um status de trabalho está marcado, execute as seguintes verificações: 
 * Se o trabalho é feito: retornar um `200 OK` resposta, com a carga de resposta.
 * Se o trabalho ainda está processando: retornar outro `202 ACCEPTED` resposta, com os mesmos cabeçalhos como a resposta inicial

Esse padrão permite executar tarefas muito longas em uma thread de sua API personalizado, mas manter uma conexão ativa ativos com o mecanismo de aplicativos de lógica para que ele não tempo limite ou continuar antes de trabalho for concluído. Ao adicionar isso em seu aplicativo de lógica, é importante observar que você não precisa fazer mais nada em sua definição para o aplicativo de lógica continuar a pesquisar e verificar o status. Assim que o mecanismo vê uma resposta ACEITOS 202 com um cabeçalho de local válido, ele aceitar padrão assíncrono e continuar pesquisar o cabeçalho de local até não-202 será retornado.

Você pode ver uma amostra desse padrão no GitHub [aqui](https://github.com/jeffhollan/LogicAppsAsyncResponseSample)

### <a name="webhook-actions"></a>Ações de Webhook

Durante o fluxo de trabalho, você pode ter o aplicativo de lógica pause e aguarde um "retorno de chamada" continuar.  Esse retorno de chamada vem na forma de uma POSTAGEM HTTP.  Para implementar esse padrão, você precisará fornecer dois pontos de extremidade em seu controlador: assinar e cancelar a assinatura.

Em 'Inscrever', o aplicativo de lógica criará e registrar uma URL de retorno de chamada que sua API pode armazenar e retorno de chamada com pronto como uma POSTAGEM HTTP.  Qualquer conteúdo/cabeçalhos serão passados para o aplicativo de lógica e podem ser usados no resto do fluxo de trabalho.  O mecanismo de aplicativo de lógica chamará o ponto de inscrever-se em execução assim que ele atinja essa etapa.

Se a execução foi cancelada, o mecanismo de aplicativo de lógica fará uma chamada para o ponto de extremidade 'Cancelar assinatura'.  Sua API pode, em seguida, cancelar o registro a URL de retorno de chamada conforme necessário.

O Designer de aplicativo lógica não suportamos descobrindo um ponto de extremidade de webhook por meio de swagger, portanto para usar esse tipo de ação que você deve adicionar a ação de "Webhook" e especifique a URL, cabeçalhos e o corpo da solicitação.  Você pode usar o `@listCallbackUrl()` função de fluxo de trabalho em qualquer um desses campos conforme necessário para passar a URL de retorno de chamada.

Você pode ver um exemplo de um padrão de webhook no GitHub [aqui](https://github.com/jeffhollan/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)

## <a name="triggers"></a>Disparadores

Além das ações, você pode ter seu act API personalizado como um disparador para um aplicativo de lógica.  Há dois padrões, que você pode seguir abaixo para acionar um aplicativo de lógica:

### <a name="polling-triggers"></a>Gatilhos de sondagem

Gatilhos de sondagem atuam muito parecido com as ações de longo assíncrono executando acima.  O mecanismo de aplicativo de lógica chamará o ponto de extremidade do disparador após um determinado período de tempo decorrido (dependente SKU, 15 segundos para Premium, 1 minuto para padrão e 1 hora gratuitos).

Se nenhum dado estiver disponível, o disparador retorna um `202 ACCEPTED` resposta, com um `location` e `retry-after` cabeçalho.  No entanto, para disparadores recomenda-se a `location` cabeçalho contém um parâmetro de consulta de `triggerState`.  Isso é alguns identificador para sua API saber quando a última vez que o aplicativo de lógica acionado.  Se houver dados disponíveis, o disparador retorna um `200 OK` resposta com a carga de conteúdo.  Isso acionará o aplicativo de lógica.

Por exemplo, se eu foi sondagem para ver se um arquivo estava disponível, você pode criar um disparador de sondagem que faria o seguinte:

* Se foi recebida uma solicitação com nenhum triggerState a API retornará uma `202 ACCEPTED` com um `location` cabeçalho que tem uma triggerState da hora atual e um `retry-after` de 15.
* Se foi recebida uma solicitação com um triggerState:
 * Verifique se todos os arquivos foram adicionados após o triggerState DateTime. 
  * Se houver 1 arquivo, retornar um `200 OK` resposta com a carga de conteúdo, incrementar o triggerState ao DateTime do arquivo retornado e definir o `retry-after` para 15.
  * Se houver vários arquivos, eu pode retornar 1 por vez com uma `200 OK`, incrementar meu triggerState no `location` cabeçalho e `retry-after` como 0.  Isso avisará o mecanismo há mais dados disponíveis e imediatamente solicitará-la a `location` cabeçalho especificado.
  * Se não houver nenhum arquivo disponível, retornar um `202 ACCEPTED` resposta e deixar o `location` triggerState o mesmo.  Definir `retry-after` 15.

Você pode ver um exemplo de um gatilho de sondagem em GitHub [aqui](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)

### <a name="webhook-triggers"></a>Gatilhos de Webhook

Webhook disparadores funcionam de maneira semelhante Webhook ações acima.  O mecanismo de aplicativo de lógica chamará o ponto de extremidade 'Inscrever' sempre que um disparador webhook é adicionado e salvo.  Sua API pode registrar a URL de webhook e chame-via HTTP POST sempre que os dados estão disponíveis.  Os cabeçalhos e a carga de conteúdo serão passados no aplicativo de lógica executar.

Se um disparador webhook nunca é excluído (aplicativo lógica inteiramente ou apenas o disparador webhook), o mecanismo será fazer uma chamada para a URL 'Cancelar assinatura' onde seu API pode remover o registro a URL de retorno de chamada e interromper quaisquer processos conforme necessário.

O Designer de aplicativo lógica não suportamos descobrindo um disparador webhook por meio de swagger, portanto para usar esse tipo de ação, você deve adicionar o disparador "Webhook" e especificar o URL, cabeçalhos e o corpo da solicitação.  Você pode usar o `@listCallbackUrl()` função de fluxo de trabalho em qualquer um desses campos conforme necessário para passar a URL de retorno de chamada.

Você pode ver um exemplo de um disparador webhook no GitHub [aqui](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)