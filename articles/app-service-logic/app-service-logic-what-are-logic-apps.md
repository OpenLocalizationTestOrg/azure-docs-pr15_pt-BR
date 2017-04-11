<properties 
    pageTitle="Quais são os aplicativos de lógica?" 
    description="Saiba mais sobre os aplicativos de lógica de serviço de aplicativo" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article" 
    ms.date="10/12/2016"
    ms.author="klam"/>

# <a name="what-are-logic-apps"></a>Quais são os aplicativos de lógica?

Aplicativos de lógica oferecem uma maneira de simplificar e implementar integrações scalable e fluxos de trabalho na nuvem. Ele fornece um designer visual para modelar e automatizar o processo de uma série de etapas conhecido como um fluxo de trabalho.  Existem [Muitos conectores](../connectors/apis-list.md) na nuvem e local para integrar rapidamente através de serviços e protocolos.  Um aplicativo de lógica começa com um disparador (como 'quando uma conta for adicionada ao Dynamics CRM') e depois acionamento pode começar a várias ações de combinações, conversões e lógica da condição.

As vantagens de usar aplicativos de lógica incluem o seguinte:  

- Economizar tempo criando processos complexos usando fácil compreender as ferramentas de design
- Implementação de padrões e fluxos de trabalho diretamente, que seria difícil implementar no código
- Guia de Introdução rapidamente de modelos
- Personalizando seu aplicativo de lógica com suas próprias APIs, código e ações personalizadas
- Conectar-se e sincronizar sistemas distintos em locais e na nuvem
- Desenvolva BizTalk server, gerenciamento de API, funções do Azure e barramento de serviço do Azure com suporte à integração de primeira classe

Lógica de aplicativos é uma iPaaS totalmente gerenciado (integração plataforma como um serviço) permitindo que os desenvolvedores não precisa se preocupar sobre a criação de hospedagem, escalabilidade, disponibilidade e gerenciamento.  Lógica de aplicativos serão redimensionados automaticamente para atender a demanda.

![Designer de aplicativo de fluxo](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Conforme mencionado, com aplicativos de lógica, você pode automatizar processos de negócios. Aqui estão alguns exemplos:  
 
* Mover os arquivos carregados em um servidor FTP no armazenamento do Azure
* Processar e rotear pedidos em locais e na nuvem sistemas
* Monitorar tweets tudo sobre um determinado tópico, analisar o sentimento e crie alertas e tarefas para itens que precisam de acompanhamento.

Cenários como esses podem ser configurados tudo de visual designer e sem escrever uma única linha de código. Obter iniciada a [criação de seu aplicativo de lógica agora][create].  Depois escreveu - um aplicativo de lógica pode ser [implantado e reconfigurar rapidamente](app-service-logic-create-deploy-template.md) em diferentes ambientes e regiões.

## <a name="why-logic-apps"></a>Por que aplicativos de lógica?

Lógica de aplicativos traz a velocidade e escalabilidade no espaço de integração do enterprise.  Facilidade de uso do designer, variedade de disparadores disponíveis e ações e ferramentas de gerenciamento eficientes Verifique centralizar seu APIs ainda mais simples.  Como empresas mover na direção digitalization, lógica de aplicativos permite que você conectar sistemas de ponta e legados juntos.

Além disso, com nossa [Conta de integração do Enterprise] [ biztalk] você pode dimensionar para desenvolver cenários de integração com potência de um [sistema de mensagens de XML][xml], [negociação gerenciamento de parceiro][tpm]e muito mais.

- **Ferramentas de design fácil de usar** - aplicativos de lógica pode ser projetada ponta a ponta no navegador ou com o Visual Studio tools. Iniciar com um disparador - de um cronograma simple para quando um problema de GitHub for criado. Em seguida, organize qualquer número de ações usando a Galeria de rich dos conectores.

- **APIs conectar facilmente** -tarefas composição mesmo que são fáceis descrever são difíceis de implementar no código. Lógica de aplicativos torna mais fácil para se conectar sistemas diferentes. Deseja se conectar a sua solução para seu local de marketing de nuvem sistema de cobrança? Deseja centralizar através de APIs e sistemas com um barramento de serviço corporativo de mensagens? Aplicativos de lógica são a maneira mais rápida e mais confiável para oferecer soluções para esses problemas.

- **Começar rapidamente a partir de modelos** - para ajudá-lo a começar a fornecemos uma [Galeria de modelos de] [ templates] que permitem que você criar rapidamente algumas soluções comuns. De soluções avançadas de B2B à conectividade SaaS simple e até mesmo alguns que são apenas 'por diversão' - a Galeria é a maneira mais rápida para começar a usar o poder dos aplicativos de lógica.

- **Extensibilidade baked-em** - não vir o conector que você precisa? Lógica de aplicativos foi projetada para funcionar com suas próprias APIs e o código; Você pode facilmente criar seu próprio aplicativo de API para usar como um conector personalizado, ou ligar para uma [Função do Azure](https://functions.azure.com) para executar trechos de código sob demanda. 

- **Potência de integração real** - iniciar fácil e crescer conforme necessário. Lógica de aplicativos pode facilmente proveito das BizTalk, solução líder setor integração da Microsoft habilitar profissionais de integração compilar as soluções de que precisam. Saiba mais sobre o [Pacote de integração de empresa](./app-service-logic-enterprise-integration-overview.md).

## <a name="logic-app-concepts"></a>Conceitos de aplicativo de lógica

A seguir estão algumas das principais partes que compõem a experiência de aplicativos de lógica. 

- **Fluxo de trabalho** - lógica aplicativos fornece uma maneira gráfica de seus processos de negócios de modelo como uma série de etapas ou um fluxo de trabalho.
- **Conectores gerenciados** - seus aplicativos de lógica precisam de acesso a dados e serviços. Conectores gerenciados são criados especificamente para ajudá-lo quando você estiver se conectando ao e trabalhar com seus dados. Consulte a lista dos conectores disponíveis no [gerenciado conectores][managedapis].
- **Disparadores** - alguns conectores gerenciado também pode atuar como um disparador. Um disparador inicia uma nova instância de um fluxo de trabalho com base em um evento específico, como a chegada de um email ou uma alteração em sua conta de armazenamento do Azure.
-  **Ações** - cada etapa depois que o disparador de fluxo de trabalho é chamado uma ação. Cada ação geralmente mapeia para uma operação em seu conector gerenciado ou aplicativos personalizados de API.
- **Pacote de integração de Enterprise** - cenários de integração mais avançados, aplicativos de lógica inclui recursos do BizTalk. BizTalk é plataforma de integração de líderes do setor da Microsoft. Os conectores de pacote de integração de Enterprise permitem que você inclua facilmente validação, transformação e muito mais para seus fluxos de trabalho do aplicativo de lógica.

## <a name="getting-started"></a>Guia de Introdução  

- Para começar com aplicativos de lógica, siga a [criar um aplicativo de lógica] [ create] tutorial.  
- [Exibir exemplos e cenários comuns](app-service-logic-examples-and-scenarios.md)
- [Você pode automatizar processos de negócios com aplicativos de lógica](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Aprenda a integrar seus sistemas com aplicativos de lógica](http://channel9.msdn.com/Events/Build/2016/P462)

[biztalk]: app-service-logic-enterprise-integration-accounts.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-enterprise-integration-accounts.md
[xml]: app-service-logic-enterprise-integration-b2b.md
[templates]: app-service-logic-use-logic-app-templates.md
