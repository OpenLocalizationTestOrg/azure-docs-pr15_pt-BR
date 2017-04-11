<properties
   pageTitle="Aplicativos de lógica exemplos e cenários | Microsoft Azure"
   description="Consulte exemplos de aplicativos comuns lógica e saiba como implementar cenários comuns"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-examples-and-common-scenarios"></a>Exemplos de aplicativos de lógica e cenários comuns

Este cenários comuns de detalhes do documento e exemplos para ajudá-lo a compreender algumas das maneiras que você podem usar aplicativos de lógica para automatizar processos de negócios. 

## <a name="custom-triggers-and-actions"></a>Ações e disparadores personalizados

Há várias maneiras que você pode acionar um aplicativo de lógica de outro aplicativo. Veja aqui alguns exemplos comuns:

- [Criando um disparador personalizado ou uma ação](app-service-logic-create-api-app.md)
- [Ações de execução longa](app-service-logic-create-api-app.md)
- [Gatilho de solicitação HTTP (POST)](app-service-logic-http-endpoint.md)
- [Ações e disparadores Webhook](app-service-logic-create-api-app.md)
- [Gatilhos de sondagem](app-service-logic-create-api-app.md)

### <a name="scenarios"></a>Cenários

- [Síncrona de solicitação de resposta](app-service-logic-http-endpoint.md)
- [Solicitação de resposta com o SMS](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="error-handling-and-logging"></a>Registro em log e tratamento de erros

- [Exceção e manipulação de erro](app-service-logic-exception-handling.md)
- [Configurar alertas do Azure e diagnóstico](app-service-logic-monitor-your-logic-apps.md)

### <a name="scenarios"></a>Cenários

- [Caso de uso: Erro e manipulação de exceção](app-service-logic-scenario-error-and-exception-handling.md)

## <a name="deploying-and-managing"></a>Implantação e gerenciamento

- [Criar uma implantação automatizada](app-service-logic-create-deploy-template.md)
- [Criar e implantar aplicativos de lógica do Visual Studio](app-service-logic-deploy-from-vs.md)
- [Monitorar aplicativos de lógica](app-service-logic-monitor-your-logic-apps.md)

## <a name="content-types-conversions-and-transformations"></a>Transformações, conversões e tipos de conteúdo

Os aplicativos de lógica [linguagem de definição de fluxo de trabalho](http://aka.ms/logicappsdocs) contém muitas funções para permitir que você converter e trabalhar com diferentes tipos de conteúdo.  Além do mecanismo fará todos que pode para preservar os tipos de conteúdo como fluxos de dados no fluxo de trabalho.

- [Manipulação de tipos de conteúdo](app-service-logic-content-type.md) como application/json, o aplicativo/xml e texto/sem formatação
- [Definições de fluxo de trabalho de criação](app-service-logic-author-definitions.md)
- [Referência de linguagem de definição de fluxo de trabalho](http://aka.ms/logicappsdocs)

## <a name="batches-and-looping"></a>Lotes e loop

- [SplitOn](app-service-logic-loops-and-scopes.md)
- [ForEach](app-service-logic-loops-and-scopes.md)
- [Até](app-service-logic-loops-and-scopes.md)

## <a name="integrating-with-azure-functions"></a>Integração com o Azure funções

- [Integração de funções Azure](app-service-logic-azure-functions.md)

### <a name="scenarios"></a>Cenários

- [Função Azure como um disparador barramento de serviço](app-service-logic-scenario-function-sb-trigger.md)

## <a name="http-rest-and-soap"></a>HTTP, REST e SOAP

 - [Chamar SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)


Nós será manter adicionando exemplos e cenários para este documento. Use a seção de comentários abaixo para nos informar quais exemplos ou cenários que você gostaria de ver aqui.