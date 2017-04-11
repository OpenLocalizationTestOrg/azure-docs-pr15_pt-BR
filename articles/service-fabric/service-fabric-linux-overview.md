<properties
   pageTitle="Serviço Azure tecidos no Linux | Microsoft Azure"
   description="Serviço tecidos clusters oferecem suporte a Linux e Java, que significa que você poderá implantar e aplicativos de serviço tecidos host escritos em Java e c# no Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="SubramaR"/>

# <a name="service-fabric-on-linux"></a>Estrutura de serviço no Linux

A visualização da estrutura de serviço no Linux permite construir, implantar e gerenciar aplicativos altamente disponíveis e altamente escaláveis no Linux como faria no Windows. As estruturas de serviço tecidos (serviços confiáveis e atores confiável) estão disponíveis em Java no Linux além c# (.NET núcleo).  Você também pode criar [Serviços executável convidado](service-fabric-deploy-existing-app.md) com qualquer linguagem ou estrutura. Além disso, a visualização também suporta orquestração contêineres de Docker. Contêineres de docker podem executar executáveis convidado ou nativos serviços de serviço tecidos, que usam as estruturas de serviço tecidos.

Estrutura de serviço no Linux equivale a estrutura de serviço no Windows (exceto para especificações do sistema operacional e suporte de linguagem de programação). Portanto, na maioria das nossa [documentação existente](http://aka.ms/servicefabricdocs) se aplica ajudá-lo a se familiarizar com a tecnologia.

> [AZURE.VIDEO service-fabric-linux-preview]

## <a name="supported-operating-systems-and-programming-languages"></a>Suporte para sistemas operacionais e linguagens de programação

A visualização limitada suporta a criação de clusters de desenvolvimento de uma caixa além de máquina vários clusters no Azure executando Ubuntu servidor 16.04. A visualização suporta os atores confiável e as estruturas de serviços de estado confiáveis em Java e c# além dos executáveis de convidado e organizar contêineres de Docker.  

>[AZURE.NOTE] Conjuntos de confiáveis não são suportados no Linux ainda. Clusters sozinhos fiquem não são suportados-apenas uma caixa e clusters múltipla máquina Azure Linux são suportados na visualização.

## <a name="supported-tooling"></a>Suporte para ferramentas

A visualização oferece suporte a interação com cluster por meio de CLI do Azure. Para desenvolvedores de Java, integração com o Eclipse e Yeoman é fornecida com o Eclipse suportado no Linux e em OSX. A integração de OSX usa uma VM Linux nos bastidores via vagrant. Para desenvolvedores do c#, integração com Yeoman é fornecida para gerar modelos de aplicativo.

## <a name="next-steps"></a>Próximas etapas


1. Familiarize-se com as estruturas de programação [Atores confiável](service-fabric-reliable-actors-introduction.md) e [Serviços confiáveis](service-fabric-reliable-services-introduction.md) .

2. [Preparar o ambiente de desenvolvimento no Linux](service-fabric-get-started-linux.md)

3. [Preparar o ambiente de desenvolvimento em OSX](service-fabric-get-started-mac.md)

4. [Criar seu primeiro aplicativo de serviço tecidos Java no Linux](service-fabric-create-your-first-linux-application-with-java.md)
