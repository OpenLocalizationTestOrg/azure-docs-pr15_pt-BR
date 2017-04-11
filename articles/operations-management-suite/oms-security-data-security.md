<properties
   pageTitle="Segurança de dados de solução de auditoria e segurança do pacote de gerenciamento de operações | Microsoft Azure"
   description="Este documento explica como os dados são gerenciados e protegidos de solução de auditoria e segurança do pacote de gerenciamento de operações."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="yurid"/>

# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Segurança de dados de solução de segurança do pacote de gerenciamento de operações e auditoria

Para ajudar clientes impedir, detectar e responder a ameaças, [solução de auditoria e segurança do pacote (OMS) de gerenciamento de operações](operations-management-suite-overview.md) coleta processos e dados sobre seus recursos, que inclui:

- Log de eventos de segurança
- Eventos de rastreamento para Windows (ETW)
- Eventos de auditoria AppLocker
- Log de Firewall do Windows
- Eventos de análise de ameaças avançados
- Resultados da avaliação de linha de base
- Resultados da avaliação de antimalware
- Resultados da avaliação de atualização/patch
- Fluxos de syslogs explicitamente habilitados no agente

Podemos fazer compromissos fortes para proteger a privacidade e segurança de dados. Microsoft adere às diretrizes estritas de segurança e conformidade — da codificação à operação de um serviço.
Este artigo explica como os dados são gerenciados e protegidos de segurança de OMS e solução de auditoria.

## <a name="data-sources"></a>Fontes de dados

Solução de auditoria e segurança OMS analisam dados de suas máquinas virtuais e computadores físicos onde o agente de OMS está instalado. Solução de auditoria e segurança OMS podem coletar informações sobre eventos de segurança, como eventos do Windows, logs de auditoria, logs do IIS e mensagens syslog configuração. Exemplos desses dados são: tipo de sistema operacional e versão, executando processos, nome da máquina, endereços IP, registrados em usuário e ID de locatário.  

## <a name="data-protection"></a>Proteção de dados

**Diferenciação de dados**: dados são mantidos separados logicamente em cada componente em todo o serviço. Todos os dados marcado por organização. Essa marcação persiste em todo o ciclo de vida de dados e ela é aplicada em cada camada do serviço. 

**Acesso a dados**: para fornecer recomendações de segurança e investigar possíveis ameaças de segurança, pessoal da Microsoft pode acessar informações coletadas ou analisados pelos serviços. Podemos cumprir os [Termos do Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e a [Declaração de privacidade](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), qual estado que Microsoft usarão não dados do cliente ou gerar informações dele finalidades comerciais publicidade ou semelhantes. Para fornecer recomendações de segurança e investigar possíveis ameaças de segurança, pessoal da Microsoft pode acessar informações coletadas ou analisados pelos serviços. Somente usamos dados do cliente conforme necessário para fornecer serviços Azure, incluindo fins compatíveis com fornecendo desses serviços. Manter todos os direitos de seus próprios dados.

**Uso dos dados**: a Microsoft usa inteligência de ameaças vistos em vários locatários e padrões para aprimorar nossos recursos de detecção e prevenção; podemos fazê-lo de acordo com os compromissos de privacidade descritos na nossa [Política de privacidade](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

> [AZURE.NOTE] Localização de dados é configurada no nível do espaço de trabalho OMS, durante a criação de espaço de trabalho, o que faz parte do processo de configuração inicial OMS segurança e auditoria.

## <a name="see-also"></a>Consulte também

Neste documento, você aprendeu como os dados são gerenciados e protegidos de OMS. Para saber mais sobre a solução de segurança de OMS e auditoria, consulte:

- [Visão geral do pacote de gerenciamento (OMS) operações](operations-management-suite-overview.md)
- [Monitoramento e responder a alertas de segurança na segurança do pacote de gerenciamento de operações e solução de auditoria](oms-security-responding-alerts.md)
- [O monitoramento de recursos de segurança do pacote de gerenciamento de operações e solução de auditoria](oms-security-monitoring-resources.md)

