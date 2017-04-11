<properties
   pageTitle="Visão geral de segurança de Internet das coisas | Microsoft Azure"
   description=" Azure internet dos serviços de coisas (IoT) oferecem uma ampla variedade de recursos. Este artigo ajuda você a entender como proteger suas soluções IoT no Azure. "
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="internet-of-things-security-overview"></a>Visão geral de segurança de Internet das coisas

Azure internet dos serviços de coisas (IoT) oferecem uma ampla variedade de recursos. Esses serviços de nível empresarial permitem que você:

- Coletar dados de dispositivos
- Analisar fluxos de dados em movimento
- Armazenar e grandes conjuntos de dados da consulta
- Visualizar dados históricos e em tempo real
- Integração com sistemas de back-office

Para fornecer esses recursos, os pacotes do Azure IoT Suite juntos vários serviços Azure com extensões personalizadas como soluções pré-configurado. Essas soluções pré-configuradas são implementações básicas da padrões de solução IoT comuns que ajudam a reduzir o tempo que necessário para oferecer suas soluções IoT. Usando os kits de desenvolvimento de software IoT, você pode personalizar e estender essas soluções para atender às suas próprias necessidades. Você também pode usar essas soluções como exemplos ou modelos quando você está desenvolvendo soluções de IoT novo.

O pacote do Azure IoT é uma solução poderosa para suas necessidades de IoT. No entanto, é de importância upmost que suas soluções IoT destinam-se com a segurança em mente desde o início. Devido ao grande número de dispositivos IoT, qualquer incidente de segurança pode rapidamente se tornar um evento amplo com consequências significativos.

Para ajudá-lo a compreender como proteger suas soluções IoT, temos as informações a seguir.

## <a name="security-architecture"></a>Arquitetura de segurança

Ao criar um sistema, é importante entender as possíveis ameaças para esse sistema e adicionar proteção apropriada da mesma forma, como o sistema é projetado e projetado. É particularmente importante projetar o produto desde o início com segurança em mente porque Noções básicas sobre como um invasor pode ser capaz de comprometer um sistema de ajuda a criar reduções sabe apropriadas estão no lugar do começo.

Você pode aprender sobre arquitetura de segurança IoT lendo [Arquitetura de segurança do Internet das coisas](../iot-suite/iot-security-architecture.md).

Este artigo discute os seguintes tópicos:

- [Segurança começa com um modelo de risco](../iot-suite/iot-security-architecture.md#security-starts-with-a-threat-model)
- [Segurança no IoT](../iot-suite/iot-security-architecture.md#security-in-iot)
- [A arquitetura de referência do Azure IoT de modelagem de risco](../iot-suite/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Segurança do Access

O IoT apresenta desafios exclusivos de segurança, privacidade e conformidade para empresas em todo o mundo. Ao contrário da tecnologia tradicional virtuais onde esses problemas giram em torno de software e como ele é implementado, IoT preocupações o que acontece quando o virtuais e o mundo físico converge. Proteger IoT soluções requer garantindo seguro de provisionamento de dispositivos, conectividade segura entre esses dispositivos e a nuvem e proteção de dados segura na nuvem durante processamento e armazenamento. Entretanto, trabalhando em relação a essa funcionalidade, são dispositivos com recursos limitados, distribuição geográfica de implantações e um grande número de dispositivos em uma solução.

Você pode aprender a lidar com segurança nessas áreas lendo [segurança de Internet das coisas desde o início](../iot-suite/securing-iot-ground-up.md).

O artigo discute os seguintes tópicos:

- [Infraestrutura de segura desde o início](../iot-suite/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
- [Microsoft Azure – infraestrutura IoT segura para sua empresa](../iot-suite/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Práticas recomendadas

Proteger uma infraestrutura de IoT exige uma estratégia de segurança em profundidade rigorosa. Iniciando de proteção de dados na nuvem, protegendo a integridade dos dados em trânsito na Internet pública e fornecendo a capacidade de forma segura provisionar dispositivos, cada camada cria maior garantia de segurança na infraestrutura do geral.

Você pode aprender sobre segurança de Internet das coisas práticas recomendadas lendo [práticas recomendadas de segurança de Internet das coisas](../iot-suite/iot-security-best-practices.md).

O artigo discute os seguintes tópicos:

- [Fabricante de hardware IoT/integrador](../iot-suite/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
- [Desenvolvedor de soluções IoT](../iot-suite/iot-security-best-practices.md#iot-solution-developer)
- [Implantador de solução IoT](../iot-suite/iot-security-best-practices.md#iot-solution-deployer)
- [Operador de solução IoT](../iot-suite/iot-security-best-practices.md#iot-solution-operator)
