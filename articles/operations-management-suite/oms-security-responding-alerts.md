<properties
   pageTitle="Monitoramento e responder a alertas de segurança na solução de auditoria e segurança do pacote de gerenciamento de operações | Microsoft Azure"
   description="Este documento ajuda você a usar a opção de inteligência de ameaças disponível no OMS segurança e auditoria para monitorar e responder a alertas de segurança."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>Monitoramento e responder a alertas de segurança na segurança do pacote de gerenciamento de operações e solução de auditoria

Este documento ajuda você a usar a opção de inteligência de ameaças disponível no OMS segurança e auditoria para monitorar e responder a alertas de segurança.

## <a name="what-is-oms"></a>O que é OMS?

Pacote de gerenciamento de operações da Microsoft (OMS) é a solução de gerenciamento de TI que ajuda você a gerenciar e proteger seu local e infraestrutura de nuvem com base de nuvem da Microsoft. Para obter mais informações sobre OMS, leia o artigo do [Pacote de gerenciamento de operações](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="threat-intelligence"></a>Inteligência de ameaças

Em um ambiente corporativo, onde os usuários têm acesso amplo à rede e usam uma variedade de dispositivos para se conectar a dados corporativos, é fundamental que você pode monitorar ativamente seus recursos e responder rapidamente às ocorrências de segurança. Isso é determinado importante da perspectiva do ciclo de vida de segurança porque algumas cybersecurity ameaças não podem gerar alertas ou atividades suspeitas que podem ser identificadas por controles técnicos de segurança tradicionais. 

Usando a opção de **Inteligência de ameaças** disponível no OMS segurança e auditoria, os administradores de TI pode identificar ameaças de segurança contra o ambiente, por exemplo, identifique se um determinado computador faz parte de um [botnet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection). Computadores podem se tornar nós em um botnet quando ataques ilicitamente instalar malware que secretamente conecta este computador para o comando e o controle. Ele também pode identificar possíveis ameaças provenientes de canais de comunicação secreto, como [darknet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents). 

Para criar essa inteligência de ameaças, OMS segurança e auditoria usam dados provenientes de várias fontes dentro da Microsoft. Segurança de OMS e auditoria utilizará esses dados para identificar ameaças em potencial contra seu ambiente.

O painel de inteligência de ameaças é composto por três opções principais:
- Servidores com tráfego mal-intencionado de saída
- Tipos de ameaças detectadas
- Mapa de inteligência de ameaças

> [AZURE.NOTE] Para obter uma visão geral de todas essas opções, leia [Introdução com a solução de auditoria e segurança do pacote de gerenciamento de operações](oms-security-getting-started.md).

### <a name="responding-to-security-alerts"></a>Respondendo a alertas de segurança

Uma das etapas de um processo de [resposta de incidente de segurança](https://technet.microsoft.com/library/cc512623.aspx) é identificar a gravidade dos sistemas de compromisso. Nesta fase, você deve executar as seguintes tarefas:

- Determine a natureza do ataque
- Determinar o ponto de ataque de origem
- Determine a intenção do ataque. O ataque foi direcionado especificamente a sua organização para adquirir determinadas informações ou foi aleatório?
- Identificar os sistemas que foram comprometidos
- Identificar os arquivos que foram acessados e determinam a confidencialidade desses arquivos

Você pode aproveitar informações de **Inteligência de ameaças** em segurança de OMS e solução de auditoria para ajudá-lo com essas tarefas. Siga as etapas abaixo para acessar essas opções de **Inteligência de ameaças** :

1. No painel principal do **Pacote de gerenciamento de operações do Microsoft** clique bloco de **segurança e auditoria** .

    ![Segurança e auditoria](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)

2. No painel de **segurança e auditoria** , você verá as opções de **Inteligência de ameaças** no lado direito, conforme mostrado abaixo:

    ![Ameaças intel](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

Estas três peças, você terá uma visão geral das ameaças atuais. No **servidor com saída tráfego mal-intencionado** que será capaz de identificar se houver qualquer computador que você está monitorando (dentro ou fora da sua rede) que está enviando o tráfego mal-intencionado com a Internet. 

O bloco de **tipos de ameaças detectadas** mostra um resumo das ameaças que são atuais "por aí", se você clicar neste bloco você verá mais detalhes sobre essas ameaças como mostrado abaixo:

![Tipos de ameaças detectada](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

Você pode extrair mais informações sobre cada ameaças clicando nela. O exemplo a seguir mostra mais detalhes sobre Botnet:

![mais detalhes sobre uma ameaças](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

Conforme descrito no início desta seção, essas informações podem ser muito útil durante um caso de resposta ao incidente. Ele também pode ser importante durante uma investigação jurídica, onde você precisa localizar a origem do ataque, qual sistema foi comprometido e a linha do tempo. Neste relatório pode identificar facilmente alguns detalhes importantes sobre o ataque, tais como: a origem do ataque, o IP local que foi comprometida e o estado da sessão atual da conexão. 

O **mapa de inteligência de ameaças** ajudará você a identificar os locais atuais em todo o mundo que possuem tráfego mal-intencionado. Há laranja (de entrada) e (de saída) setas vermelhas neste mapa que identificam a direção de tráfego, se você clicar em uma dessas setas, ele será mostrado o tipo de ameaças e a direção de tráfego, conforme mostrado abaixo:

![mapa de intel ameaças](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

> [AZURE.NOTE] Você pode ver uma demonstração sobre como usar esse recurso durante um incidente processo de resposta assistindo a apresentação [ameaças de segurança de data center minimizar com investigação guiada usando o pacote de gerenciamento de operações](https://myignite.microsoft.com/videos/5000) fornecidos em Microsoft Ignite.

## <a name="see-also"></a>Consulte também

Neste documento, você aprendeu como usar a opção de **Inteligência de ameaças** em segurança de OMS e solução de auditoria para responder a alertas de segurança. Para saber mais sobre a segurança do OMS, consulte os seguintes artigos:

- [Visão geral do pacote de gerenciamento (OMS) operações](operations-management-suite-overview.md)
- [Introdução aos segurança do pacote de gerenciamento de operações e solução de auditoria](oms-security-getting-started.md)
- [O monitoramento de recursos de segurança do pacote de gerenciamento de operações e solução de auditoria](oms-security-monitoring-resources.md)
