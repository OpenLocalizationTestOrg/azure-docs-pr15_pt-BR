<properties
   pageTitle="Definir e gerenciar estado | Microsoft Azure"
   description="Como definir e gerenciar o estado de serviço em estrutura de serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="aprameyr"/>

# <a name="service-state"></a>Estado do serviço
**Estado do serviço** refere-se aos dados que o serviço requer para funcionar. Ele inclui as estruturas de dados e variáveis que o serviço lê e grava para realizar o trabalho.

Considere um serviço de calculadora simples, por exemplo. Este serviço leva dois números e retorna a soma. Este é um serviço puramente sem estado que não contenha dados associados a ele.

Agora, considere a mesma Calculadora, mas além de computação soma, ele também tem um método para retornar a soma de última que ele tenha computado. Esse serviço é agora com informações de estado – ele contém algum estado que ele grava (quando ele calcula uma soma nova) e lê a partir (quando ela retorna a soma de computado última).

Em estrutura de serviço do Azure, o primeiro serviço é chamado de serviço sem estado. O segundo serviço é chamado de serviço com informações de estado.

## <a name="storing-service-state"></a>Armazenando o estado de serviço
Estado pode ser exteriorizado ou localizado com o código que está manipulando o estado. Externalization de estado geralmente é feito usando um banco de dados externo ou store. Em nosso exemplo de Calculadora, isso pode ser um banco de dados do SQL no qual o resultado atual é armazenado em uma tabela. Cada solicitação para calcular a soma executa uma atualização nesta linha.

Estado também pode ser localizado com o código que manipula este código. Serviços de estado no serviço tecidos são criados usando este modelo. Serviço tecidos fornece a infraestrutura para assegurar que esse estado é altamente disponível e tolerância em caso de uma falha.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os conceitos de estrutura de serviço, consulte o seguinte:

- [Disponibilidade de serviços de estrutura de serviço](service-fabric-availability-services.md)

- [Escalabilidade dos serviços de estrutura de serviço](service-fabric-concepts-scalability.md)

- [Serviços de serviço tecidos partição](service-fabric-concepts-partitioning.md)
