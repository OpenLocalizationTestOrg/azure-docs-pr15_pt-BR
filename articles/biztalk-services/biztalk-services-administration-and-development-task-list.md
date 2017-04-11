<properties
    pageTitle="Lista nos serviços do BizTalk de tarefas de administração e desenvolvimento | Microsoft Azure"
    description="Planejamento e trabalho auxiliam para implantar os serviços do Azure BizTalk."
    services="biztalk-services"
    documentationCenter=""
    authors="msftman"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="deonhe"/>

# <a name="administration-and-development-task-list-in-biztalk-services"></a>Lista de tarefas de desenvolvimento nos serviços do BizTalk e administração  

## <a name="getting-started"></a>Guia de Introdução
Ao trabalhar com os serviços do Microsoft Azure BizTalk, há vários locais e na nuvem componentes a considerar. Para começar, considere o fluxo de processo a seguir:  

|Etapa|Quem é responsável|Tarefa|Links relacionados|
|----|----|----|----|
|1.|Administrador|Criar a assinatura do Microsoft Azure usando uma conta da Microsoft ou uma conta organizacional|[Azure portal clássico](http://go.microsoft.com/fwlink/p/?LinkID=213885)|
|2.|Administrador|Criar ou provisionar um BizTalk Service.|[Criar um BizTalk Service usando Azure portal clássico](http://go.microsoft.com/fwlink/p/?LinkID=302280)|
|3.|Administrador|Registre-se você ou implantação de Serviços BizTalk da sua empresa|[Registrando e atualizando uma implantação de serviço BizTalk no Portal de serviços do BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx)|
|4.|Administrador|Se aplica se o aplicativo usa o serviço de adaptador BizTalk para conectar a um sistema de linha de negócios (LOB) local ou usa uma fila ou um destino de tópico.  Crie o serviço Azure barramento Namespace. Dê este namespace, nome de emissor do barramento de serviço e valores de chave de emissor do barramento de serviço para o desenvolvedor.|[Como: criar ou modificar um Namespace de serviço do serviço barramento](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) e [obter o nome do emissor e valores de chave emissor](biztalk-issuer-name-issuer-key.md)|
|5.|Desenvolvedor|Instale o SDK e crie o projeto BizTalk Service no Visual Studio.|[Instalar o SDK dos serviços do BizTalk Azure](https://msdn.microsoft.com/library/azure/hh689760.aspx) e [criar sofisticados pontos de extremidade de mensagens no Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx)|
|6.|Desenvolvedor|Implante seu BizTalk Service projeto para seu BizTalk Service hospedado no Azure.|[Implantando e atualizando o projeto de Serviços BizTalk](https://msdn.microsoft.com/library/azure/hh689881.aspx)|
|7.|Administrador|Se aplica se você estiver usando o EDI.  Você pode adicionar parceiros e criar contratos no Portal de serviços do Microsoft Azure BizTalk. Quando você cria um contrato, você pode adicionar a ponte e/ou transformações criadas pelo desenvolvedor para as configurações de contrato.|[Configurando EDI, AS2 e EDIFACT no Portal de serviços do BizTalk](https://msdn.microsoft.com/library/azure/hh689853.aspx)|
|8.|Administrador|Usando o Portal de clássico Azure, monitore a integridade do seu BizTalk Service, incluindo métricas de desempenho.|[Serviços BizTalk: Guias de painel, o Monitor e a escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)|
|9.|Administrador|Usando o Portal de serviços do Microsoft Azure BizTalk, gerencie os artefatos usados por serviços BizTalk e controlar mensagens conforme elas são processadas pelos arquivos de ponte.|[Usando o Portal de serviços do BizTalk](https://msdn.microsoft.com/library/azure/dn874043.aspx)|
|10.|Administrador|Crie um plano de backup para fazer backup do BizTalk Service.|[Continuidade de negócios e recuperação de dados nos serviços do BizTalk](https://msdn.microsoft.com/library/azure/dn509557.aspx) |  
## <a name="next-steps"></a>Próximas etapas
[Amostras e tutoriais](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Crie o projeto no Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Instalar o SDK dos serviços do BizTalk Azure](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>Conceitos
[Crie o projeto no Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[EDI, AS2 e EDIFACT mensagens (Business-to-Business)](https://msdn.microsoft.com/library/azure/hh689898.aspx)  
## <a name="other-resources"></a>Outros recursos  
[Adicionar origem, destino e ponte de pontos de extremidade de mensagens](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[Saiba e criar mapas de mensagem e transformações](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[Usando o serviço de adaptador BizTalk (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Serviços BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)
