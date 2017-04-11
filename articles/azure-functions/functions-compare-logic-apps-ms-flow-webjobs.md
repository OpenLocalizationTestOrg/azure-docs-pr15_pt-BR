<properties
    pageTitle="Escolha entre fluxo, aplicativos de lógica, funções e WebJobs | Microsoft Azure"
    description="Compare e contraste a nuvem integração de serviços da Microsoft e decidir quais serviços você deve usar."
    services="functions,app-service\logic"
    documentationCenter="na"
    authors="cephalin"
    manager="wpickett"
    tags=""
    keywords="Microsoft fluxo, fluxo, aplicativos de lógica, funções azure, funções, azure webjobs, webjobs, evento arquitetura de processamento, computação dinâmica, sem servidor"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="09/08/2016"
    ms.author="chrande; glenga"/>

# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Escolha entre fluxo, aplicativos de lógica, funções e WebJobs

Este artigo compara e contrasta os seguintes serviços de nuvem da Microsoft, que pode todos solucionar problemas de integração e automação de processos de negócios:

- [Fluxo da Microsoft](https://flow.microsoft.com/)
- [Aplicativos do Azure lógica](https://azure.microsoft.com/services/logic-apps/)
- [Funções do Azure](https://azure.microsoft.com/services/functions/)
- [Serviço de aplicativo Azure WebJobs](../app-service-web/web-sites-create-web-jobs.md)

Todos esses serviços são úteis quando "colando" juntos sistemas distintos. Elas todos definem entrada, ações, condições e saída. Você pode executar cada um em um cronograma ou disparador. No entanto, cada serviço adiciona um conjunto exclusivo de valor e compará-los não é uma questão de "qual serviço é o melhor?" mas uma das "qual serviço é o mais adequado para essa situação?" Geralmente, uma combinação desses serviços é a melhor maneira de criar rapidamente uma solução completa, scalable integração em destaque.

<a name="flow"></a>
## <a name="flow-vs-logic-apps"></a>Aplicativos de fluxo versus lógica

Podemos discutir Microsoft Flow e aplicativos do Azure lógica juntos porque eles são ambos os serviços de integração de *configuração primeiro* , que torna mais fácil criar processos e fluxos de trabalho e integração com vários aplicativos SaaS e enterprise. 

- Fluxo baseia-se na parte superior de lógica de aplicativos
- Eles têm o mesmo designer de fluxo de trabalho
- [Conectores](../connectors/apis-list.md) que trabalha em uma também pode trabalhar no outro

Fluxos de habilita qualquer trabalhador do office para realizar integrações simples (por exemplo, obter SMS emails importantes) sem percorrer os desenvolvedores ou IT. Por outro lado, aplicativos de lógica pode habilitar avançadas ou essenciais integrações (por exemplo, processos B2B) onde práticas de DevOps e segurança de nível empresarial são necessárias. É comum para um fluxo de trabalho de negócios crescer em horas extras complexidade. Portanto, você pode começar com um fluxo a primeira, e convertê-lo em um aplicativo de lógica, conforme necessário.

A tabela a seguir o ajudará a determinar se o fluxo ou aplicativos de lógica é melhores para uma determinada integração.

|               | Fluxo                                                                             | Aplicativos de lógica                                                                                          |
|---------------|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| Público-alvo      | trabalhadores do Office, os usuários de negócios                                                   | Profissionais de TI, desenvolvedores                                                                                 |
| Cenários     | Autoatendimento                                                                     | Essenciais                                                                                    |
| Ferramenta de design   | Interface do usuário somente no navegador                                                              | No navegador e o [Visual Studio](../app-service/logic/app-service-logic-deploy-from-vs.md), o [modo de exibição de código](../app-service-logic/app-service-logic-author-definitions.md) disponíveis |
| DevOps        | Ad-hoc, desenvolver em produção                                                    | controle de origem, teste, suporte e automação e gerenciamento no [Gerenciamento de recursos do Azure](../app-service-logic/app-service-logic-arm-provision.md)|
| Experiência de administração| [https://Flow.microsoft.com](https://flow.microsoft.com)                       | [https://portal.Azure.com](https://portal.azure.com)                                                |
| Segurança      | Práticas padrão: [Soberania de dados](https://wikipedia.org/wiki/Technological_Sovereignty), [criptografia inativos](https://wikipedia.org/wiki/Data_at_rest#Encryption) para dados confidenciais, etc. | Garantia de segurança do Azure: [Segurança do Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [O Centro de segurança](https://azure.microsoft.com/services/security-center/), [logs de auditoria](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/)e muito mais. |

<a name="function"></a>
## <a name="functions-vs-webjobs"></a>Funções versus WebJobs

Estamos pode discutir funções do Azure e WebJobs de serviço de aplicativo do Azure juntos porque eles são ambos os serviços de integração de *código primeiro* e projetado para desenvolvedores. Eles permitem que você executar um script ou um pedaço de código em resposta a vários eventos, como [Novo armazenamento Blobs](functions-bindings-storage.md) ou [uma solicitação de WebHook](functions-bindings-http-webhook.md). Aqui estão as semelhanças: 

- Ambos são criados no [Serviço de aplicativo do Azure](../app-service/app-service-value-prop-what-is.md) e aproveitam recursos como [controle de origem](../app-service-web/app-service-continuous-deployment.md), [autenticação](../app-service/app-service-authentication-overview.md)e [monitoramento](../app-service-web/web-sites-monitor.md).
- Ambos são serviços voltada para desenvolvedores.
- O suporte de script padrão e linguagens de programação.
- Ambos têm NuGet e NPM oferece suporte.

Funções é a evolução natural de WebJobs em que ela leva melhores coisas WebJobs e melhora sobre eles. Os aperfeiçoamentos incluem: 

- Desenvolvimento simplificado, teste e execução de código, diretamente no navegador.
- Integração interna com serviços mais Azure e serviços de terceiros 3º como [GitHub WebHooks](https://developer.github.com/webhooks/creating/).
- Pagamento por uso, sem a necessidade de pagamento para um [Plano de serviço de aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
- Automático, [Dimensionamento dinâmico](functions-scale.md).
- Para clientes existentes do serviço de aplicativo, em execução no plano de serviço de aplicativo ainda possível (para tirar proveito dos recursos sob utilizados).
- Integração com aplicativos de lógica.

A tabela a seguir resume as diferenças entre funções e WebJobs:

|                        | Funções                                                                                                                                                                | WebJobs                            |
|------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------|
| Dimensionamento                | Dimensionamento configurationless                                                                                                                                                | Dimensionar com plano de serviço de aplicativo        |
| Preços                | Pagamento por uso ou parte do plano de serviço de aplicativo                                                                                                                                  | Parte do plano de serviço de aplicativo           |
| Tipo de execução               | disparada, agendados (por disparador de timer)                                                                                                                                  | disparadas, contínua, agendada   |
| Eventos de disparadores         | [timer](functions-bindings-timer.md), [DocumentDB do Azure](functions-bindings-documentdb.md), [Hubs de evento do Azure](functions-bindings-event-hubs), [HTTP/WebHook (GitHub, margem de atraso)](functions-bindings-http-webhook.md), [Aplicativos do Azure aplicativo serviço Mobile](functions-bindings-mobile-apps.md), [Hubs de notificação do Azure](functions-bindings-notification-hubs.md), [Barramento de serviço do Azure](functions-bindings-service-bus.md), [O armazenamento do Azure](articles/functions-bindings-storage.md) | [Armazenamento do azure](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), [barramento de serviço Azure](websites-dotnet-webjobs-sdk-service-bus.md)         |
| Desenvolvimento do navegador | x                                                                                                                                                                        |                                    |
| Janela script       | experimental                                                                                                                                                             | x                                  |
| PowerShell             | experimental                                                                                                                                                             | x                                  |
| C#                     | x                                                                                                                                                                        | x                                  |
| F#                     | x                                                                                                                                                                        |                                    |
| Bash                   | experimental                                                                                                                                                             | x                                  |
| PHP                    | experimental                                                                                                                                                             | x                                  |
| Python                 | experimental                                                                                                                                                             | x                                  |
| JavaScript             | x                                                                                                                                                                        | x                                  |
| Java                   | experimental                                                                                                                                                             | x                                  |

Usar funções ou WebJobs depende que você já estiver fazendo com o serviço de aplicativo. Se você tiver um aplicativo de serviço de aplicativo para o qual você deseja executar trechos de código, e você quiser gerenciá-los juntos no mesmo ambiente DevOps, você deve usar WebJobs. Se você quiser executar trechos de código para outros serviços Azure ou até mesmo 3º terceiros aplicativos, ou se você quiser gerenciar seus trechos de código de integração separadamente dos seus aplicativos de serviço de aplicativo, ou se você quiser ligar trechos de código de um aplicativo de lógica, você deve tirar proveito de todos os aperfeiçoamentos em funções.  

<a name="together"></a>
## <a name="flow-logic-apps-and-functions-together"></a>Fluxo, aplicativos de lógica e funções juntos

Conforme mencionado anteriormente, qual serviço é mais adequado para você depende de sua situação. 

- Para otimização de negócios simples, em seguida, use fluxo.
- Se seu cenário de integração é muito avançado para fluxo, ou você precisa de recursos de DevOps e conformidades de segurança, use aplicativos de lógica.
- Se uma etapa no seu cenário de integração requer transformação altamente personalizada ou código especializado, então escrever um aplicativo de função e, em seguida, disparar uma função como uma ação em seu aplicativo de lógica.

Você pode chamar um aplicativo de lógica em um fluxo. Você também pode chamar uma função em um aplicativo de lógica e um aplicativo de lógica em uma função. A integração entre o fluxo, aplicativos de lógica e funções continuar a aprimorar horas extras. Você pode criar algo em um serviço e usá-lo em outros serviços. Portanto, qualquer investimento feitas nestas três tecnologias é interessante.

## <a name="next-steps"></a>Próximas etapas

Começar a usar cada um dos serviços ao criar seu primeiro fluxo, lógica aplicativo, função aplicativo ou WebJob. Clique em qualquer um dos links a seguir:

- [Introdução ao Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
- [Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [Crie sua primeira função do Azure](../azure-functions/functions-create-first-azure-function.md)
- [Implantar WebJobs usando o Visual Studio](../app-service-web/websites-dotnet-deploy-webjobs.md)

Ou, obtenha mais informações sobre esses serviços de integração com os links a seguir:

- [Aproveitando funções do Azure e serviço de aplicativo do Azure para cenários de integração por Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
- [Integrações feitas simples por Carlos Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
- [Webcast ao vivo de aplicativos de lógica](http://aka.ms/logicappslive)
- [Fluxo de Microsoft frequentemente perguntas frequentes](https://flow.microsoft.com/documentation/frequently-asked-questions/)
- [Recursos de documentação do Azure WebJobs](../app-service-web/websites-webjobs-resources.md)
