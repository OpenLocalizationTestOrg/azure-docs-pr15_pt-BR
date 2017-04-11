<properties
   pageTitle="Visão geral de funções do Azure | Microsoft Azure"
   description="Compreenda como usar funções do Azure para otimizar cargas de trabalho assíncronas em minutos."
   services="functions"
   documentationCenter="na"
   authors="mattchenderson"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure funções, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/29/2016"
   ms.author="cfowler;mahender;glenga"/>
   
   
# <a name="azure-functions-overview"></a>Visão geral de funções Azure

Funções Azure é uma solução para executar facilmente pequenas partes de código ou "funções," na nuvem. Você pode gravar apenas o código que você precisa para o problema à mão, sem se preocupar em todo o aplicativo ou a infraestrutura para executá-la. Isso pode tornar o desenvolvimento ainda mais produtivo e você pode usar o idioma de desenvolvimento de escolha, como c#, F #, Node, Python ou PHP. Paga somente para o tempo de que execução do seu código e confiança Azure dimensionar conforme necessário.

Este tópico fornece uma visão geral das funções do Azure. Se você quiser ir direto e começar a usar funções do Azure, comece com [criar sua primeira função do Azure](functions-create-first-azure-function.md). Se você estiver procurando mais informações técnicas sobre funções, consulte a [referência do desenvolvedor](functions-reference.md).

## <a name="features"></a>Recursos

Aqui estão alguns dos principais recursos das funções do Azure:
    
* **Escolha de idioma** - funções de gravação usando c#, F #, Node, Python, PHP, lote, bash, Java ou qualquer executável.
* **Modelo de preços de pagamento por uso** - pagamento somente para o tempo gasto executando seu código. Consulte a opção de plano de serviço de aplicativo dinâmico nos [preços seção](#pricing) abaixo.  
* **Trazer seus próprio dependências** - funções suporta NuGet e NPM, para que você possa usar suas bibliotecas favoritas.  
* **Segurança integrada** - disparadas HTTP proteger funções com provedores de OAuth como o Active Directory do Azure, Facebook, Twitter, Google e Account da Microsoft.  
* **Integração de simplificado** - aproveite facilmente os serviços do Azure e ofertas de software-como um serviço (SaaS). Consulte a [seção de integrações](#integrations) abaixo para obter alguns exemplos.  
* **Desenvolvimento flexível** - seu direito de funções no portal de código ou configurar integração contínua e implante seu código por meio de GitHub, serviços de equipe do Visual Studio e outras [Ferramentas de desenvolvimento de suporte](../app-service-web/web-sites-deploy.md#deploy-using-an-ide).  
* **Código-fonte aberto** - tempo de execução de funções a é aberto e está [disponível no GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>O que posso fazer com funções?

Funções Azure é uma ótima solução para processamento de dados, integração de sistemas, trabalhando com a internet-de-coisas (IoT) e a criação de APIs simples e microservices. Considere a possibilidade de funções para tarefas como processamento de imagem ou ordem, manutenção de arquivo, tarefas de longa execução que você deseja executar em um thread de plano de fundo ou para as tarefas que você deseja executar em um cronograma. 

Funções fornece modelos para iniciá-lo com cenários-chave, incluindo o seguinte:

* **BlobTrigger** - blobs de armazenamento do Azure processo quando eles são adicionados ao contêineres. Isso pode ser usado para o redimensionamento de imagens.
* **EventHubTrigger** - responder a eventos entregue a um Hub de evento do Azure. Especialmente útil em situações de Internet das coisas (IoT), processamento de fluxo de trabalho ou de experiência do usuário e instrumentação do aplicativo.
* **Genérico webhook** - webhook de processo HTTP solicitações de qualquer serviço que ofereça suporte a webhooks.
* **GitHub webhook** - responder a eventos que ocorrem em seus repositórios GitHub. Por exemplo, consulte [criar um webhook ou função API](functions-create-a-web-hook-or-api-function.md).
* **HTTPTrigger** - disparadores a execução do seu código usando uma solicitação HTTP.
* **QueueTrigger** - responder a mensagens quando chegarem em uma fila do armazenamento do Azure. Por exemplo, consulte [criar uma função do Azure que vincula a um serviço Azure](functions-create-an-azure-connected-function.md).
* **ServiceBusQueueTrigger** - conecte seu código para outros serviços do Azure ou local serviços ao ouvir a filas de mensagens. 
* **ServiceBusTopicTrigger** - conecte seu código para outros serviços do Azure ou local serviços por assinatura para tópicos. 
* **TimerTrigger** - Execute a limpeza ou outras tarefas de lote em um cronograma predefinido. Por exemplo, consulte [criar um evento de função de processamento](functions-create-an-event-processing-function.md).

Funções Azure suporta *disparadores*, que são maneiras de começar a execução do seu código e *ligações*, que são maneiras de simplificar a codificação para dados de entrada e saídos. Para obter uma descrição detalhada dos disparadores e ligações que fornece funções do Azure, consulte [gatilhos de funções do Azure e referência do desenvolvedor de ligações](functions-triggers-bindings.md).


## <a name="integrations"></a>Integrações

Funções Azure integra-se uma variedade de Azure e serviços de terceiros 3º. Você pode usar esses disparar sua função e comece a execução ou para servir como entrada e saída para seu código. As seguintes integrações de serviço são suportadas pelo funções do Azure. 

* DocumentDB Azure
* Hubs de evento Azure 
* Aplicativos do Azure Mobile (tabelas)
* Hubs de notificação do Azure
* Azure barramento de serviço (filas e tópicos)
* Armazenamento do Azure (blob, filas e tabelas) 
* GitHub (webhooks)
* Local (usando o barramento de serviço)

## <a name="pricing"></a>Quanto o funções custo?

Funções Azure tem dois tipos de planos de preço, escolha a opção que melhor se adapta às suas necessidades: 

* **Plano de hospedagem dinâmico** - durante a execução de sua função, Azure fornece todos os recursos de computação necessários. Você não precisa se preocupar em gerenciamento de recursos e você paga apenas para o tempo que o código é executado. Detalhes completos de preços estão disponíveis na [página de preços de funções](/pricing/details/functions). 

* **Plano de serviço de aplicativo** - executar suas funções como sua web, celular e aplicativos de API. Quando você já estiver usando o serviço de aplicativo para os outros aplicativos, você pode executar as funções no mesmo plano sem nenhum custo adicional. Detalhes completos podem ser encontrados na [página de preços de serviços de aplicativo](/pricing/details/app-service/).

Para obter mais informações sobre as funções de dimensionamento, veja [como dimensionar funções do Azure](functions-scale.md).

##<a name="next-steps"></a>Próximas etapas

+ [Crie sua primeira função do Azure](functions-create-first-azure-function.md)  
Ir direto e crie sua primeira função usando o início rápido do Azure funções. 
+ [Referência do desenvolvedor funções Azure](functions-reference.md)  
Fornece mais informações técnicas sobre o tempo de execução de funções do Azure e uma referência para funções de codificação e definir disparadores e ligações.
+ [Funções Azure de teste](functions-test-a-function.md)  
Descreve várias ferramentas e técnicas para testar suas funções.
+ [Como escalar funções Azure](functions-scale.md)  
Discute os planos de serviços disponíveis com as funções do Azure, incluindo o plano de serviço dinâmico e como escolher o plano certo. 
+ [Saiba mais sobre o serviço de aplicativo do Azure](../app-service/app-service-value-prop-what-is.md)  
Funções Azure utiliza a plataforma de serviço de aplicativo do Azure funcionalidade principais como implantações, variáveis de ambiente e o diagnóstico. 