<properties
   pageTitle="Internet das práticas recomendadas de segurança de coisas | Microsoft Azure"
   description="O artigo fornece uma lista de curated da Microsoft Internet das coisas segurança melhores práticas e recomendações gerais."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="internet-of-things-security-best-practices"></a>Internet das práticas recomendadas de segurança de itens

Protegendo a infraestrutura de Internet das coisas (IoT) é uma tarefa crítica para qualquer pessoa envolvidas IoT soluções. Devido ao número de dispositivos envolvidos e a natureza distribuída desses dispositivos, o impacto de um evento de segurança relacionadas a comprometer de milhões de dispositivos IoT não é simples e pode ter impacto generalizado.

Por esse motivo, segurança IoT precisa de uma abordagem de segurança em camadas. Dados precisam ser segura na nuvem e como ela entrará em redes públicas e particulares. Métodos precisam ser in-loco com segurança provisionar próprios dispositivos IoT. Cada camada, do dispositivo, rede, para a nuvem back-end necessidades garantias de alta segurança.

Práticas recomendadas de IoT podem ser categorizadas da seguinte maneira:

- IoT fabricante do hardware ou integrador
- Desenvolvedor de soluções IoT
- Implantador de solução IoT
- Operador de solução IoT

Este artigo resume [Internet das coisas práticas recomendadas de segurança](../iot-suite/iot-security-best-practices.md). Consulte esse artigo para obter informações mais detalhadas.

## <a name="iot-hardware-manufacturer-or-integrator"></a>IoT fabricante do hardware ou integrador

Siga as práticas recomendadas abaixo se você for um fabricante do hardware IoT ou um integrador de hardware:

- **Requisitos mínimos de hardware escopo**: o design de hardware deve incluir recursos mínimos necessários para a operação de hardware e nada mais. 
- **Verifique o hardware à prova de violação**: construir em mecanismos para detectar violação física de hardware, como abrir a folha de rosto do dispositivo, removendo uma parte do dispositivo, etc. 
- **Construir em torno de hardware seguro**: se [COGS](https://en.wikipedia.org/wiki/Cost_of_goods_sold) permitirem, criar recursos de segurança como armazenamento seguro e criptografado e funcionalidade de inicialização baseado em TPM de módulo de plataforma confiável.
- **Fazer atualizações seguro**: atualizar firmware durante o ciclo de vida do dispositivo é inevitável.

## <a name="iot-solution-developer"></a>Desenvolvedor de soluções IoT

Siga as práticas recomendadas abaixo se você for um desenvolvedor de solução IoT:

- **Metodologia de desenvolvimento de software seguro de acompanhamento**: o desenvolvimento de software seguro requer Terra-up pensar sobre segurança do início do projeto totalmente para sua implementação, teste e implantação.
- **Escolha software de fonte aberta com cuidado**: software de fonte aberta oferece a oportunidade de desenvolver soluções rapidamente.
- **Integrar com cuidado**: muitas das falhas de segurança de software existem no limite da bibliotecas e APIs. 

## <a name="iot-solution-deployer"></a>Implantador de solução IoT

Siga as práticas recomendadas abaixo se você for um implantador de solução IoT:

- **Implantar o hardware com segurança**: IoT implantações podem exigir hardware para ser implantado em locais não seguras, tais como espaços públicos ou localidades sem supervisão.
- **Manter chaves de autenticação seguros**: durante a implantação, cada dispositivo requer IDs de dispositivo e associados chaves de autenticação geradas pelo serviço na nuvem. Manter estas teclas física seguros mesmo após a implantação. Qualquer chave comprometida pode ser usado por um dispositivo mal-intencionado para aparentam um dispositivo existente.

## <a name="iot-solution-operator"></a>Operador de solução IoT

Siga as práticas recomendadas abaixo se você for um operador de solução IoT:

- **Manter os sistemas atualizados**: Certifique-se de sistemas operacionais de dispositivos e todos os drivers de dispositivo são atualizados para as versões mais recentes. 
- **Proteger contra atividades maliciosas**: se o sistema operacional permite, coloque os recursos mais recentes de antivírus e antimalware em cada sistema operacional do dispositivo. 
- **Auditoria frequentemente**: IoT infraestrutura de auditoria para segurança relacionadas problemas é chave ao responder a ocorrências de segurança.
- **Proteger física a infraestrutura de IoT**: Os pior segurança ataques IoT infraestrutura são iniciados usando acesso físico a dispositivos.
- **Proteger credenciais de nuvem**: as credenciais de autenticação de nuvem usadas para configuração e operação de uma implantação IoT são possivelmente a maneira mais fácil de acessar e comprometem a um sistema de IoT. 
