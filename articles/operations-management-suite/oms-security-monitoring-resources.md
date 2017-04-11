<properties
   pageTitle="O monitoramento de recursos de segurança do pacote de gerenciamento de operações e solução de auditoria | Microsoft Azure"
   description="Este documento ajuda você a usar a segurança de OMS e recursos para monitorar seus recursos e identificar problemas de segurança de auditoria."
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

# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>Monitoramento de recursos de segurança do pacote de gerenciamento de operações e solução de auditoria

Este documento ajuda você a usar o OMS segurança e recursos de auditoria para monitorar seus recursos e identificar problemas de segurança.

## <a name="what-is-oms"></a>O que é OMS?

Pacote de gerenciamento de operações da Microsoft (OMS) é a solução de gerenciamento de TI que ajuda você a gerenciar e proteger seu local e infraestrutura de nuvem com base de nuvem da Microsoft. Para obter mais informações sobre OMS, leia o artigo do [Pacote de gerenciamento de operações](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="monitoring-resources"></a>Recursos de monitoramento

Sempre que possível, você desejará impedir que ocorrências de segurança acontecendo em primeiro lugar. Entretanto, é impossível evitar todas as ocorrências de segurança. Quando um incidente de segurança acontecer, você precisará garantir que seu impacto seja minimizado.  Há três recomendações críticas que podem ser usadas para minimizar o número e o impacto das ocorrências de segurança:

- Avalie regularmente vulnerabilidades no seu ambiente.
- Verificar com frequência todos os sistemas de computador e dispositivos de rede para garantir que eles tenham todos os patches mais recentes instalados.
- Verificar com frequência todos os logs e mecanismos de log, incluindo logs de eventos do sistema operacional, logs específicos de aplicativos e logs de sistema de detecção de intrusos.

Segurança de OMS e permite que a solução auditoria TI monitorar ativamente todos os recursos, que podem ajudar a minimiza o impacto das ocorrências de segurança. Segurança de OMS e auditoria tem domínios de segurança que podem ser usados para monitoramento de recursos. Os domínios de segurança fornece acesso rápido a uma opções, para monitoramento de segurança domínios a seguir serão abordados em mais detalhes:

- Avaliação de malware
- Avaliação de atualização
- Identidade e acesso

> [AZURE.NOTE] Para obter uma visão geral de todas essas opções, leia [Introdução com a solução de auditoria e segurança do pacote de gerenciamento de operações](oms-security-getting-started.md).

### <a name="monitoring-system-protection"></a>Monitoramento de proteção do sistema

Em uma defesa profunda, cada camada de proteção é importante para o estado de segurança geral do seu ativo. Computadores com ameaças detectadas e computadores com proteção insuficiente são mostrados no bloco avaliação de Malware em domínios de segurança. Usando as informações sobre a avaliação de Malware, você pode identificar um plano para aplicar proteção aos servidores que precisam dela. Para acessar essa opção, siga as etapas abaixo:

1. No painel principal do **Pacote de gerenciamento de operações do Microsoft** clique bloco de **segurança e auditoria** .

    ![Segurança e auditoria](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)

2. No painel de ferramentas **de segurança e auditoria** , clique em **Avaliação de Antimalware** em **Domínios de segurança**. No painel de **Avaliação de Antimalware** aparece como mostrado abaixo:

![Avaliação de malware](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

Você pode usar o painel de **Avaliação de Malware** para identificar os seguintes problemas de segurança:

- **Ameaças ativas**: computadores que foram comprometidos e têm ameaças ativas no sistema.
- **Ameaças Remediated**: computadores que foram comprometidos, mas as ameaças foram remediadas.
- **Assinatura desatualizada**: computadores que têm proteção contra malware habilitada, mas a assinatura está desatualizada.
- **Sem proteção em tempo real**: computadores que não tenham antimalware instalado.

### <a name="monitoring-updates"></a>Atualizações de monitoramento 

Aplicar as atualizações mais recentes de segurança é uma prática recomendada de segurança e ele deve ser incorporado em sua estratégia de gerenciamento de atualização. Serviço de agente de monitoramento do Microsoft (HealthService.exe) lê as informações de atualização de computadores monitoradas e envia essas informações atualizadas para o serviço OMS na nuvem para processamento. O serviço do Microsoft Agent de monitoramento está configurado como um serviço automático e ele deve estar sempre em execução no computador de destino.

![atualizações de monitoramento](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

Lógica é aplicada aos dados de atualização e os dados de registros do serviço de nuvem. Se forem encontradas atualizações ausentes, eles serão mostrados no painel **atualizações** . Você pode usar o painel de **atualizações** para trabalhar com atualizações ausentes e desenvolver um plano para aplicá-las aos servidores que precisam deles. Siga as etapas abaixo para acessar o painel de **atualizações** :

1. No painel principal do **Pacote de gerenciamento de operações do Microsoft** clique bloco de **segurança e auditoria** .
2. No painel de ferramentas **de segurança e auditoria** , clique em **Avaliação de atualização** em **Domínios de segurança**. No painel de atualização é exibida conforme mostrado abaixo:

![avaliação de atualização](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

Neste painel, você pode executar uma avaliação de atualização para entender o estado atual dos seus computadores e tratar as ameaças mais importantes. Usando o bloco de **crítica ou atualizações de segurança** , os administradores de TI poderão acessar informações detalhadas sobre as atualizações que estão faltando conforme mostrado abaixo:

![resultado da pesquisa](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

Este relatório incluem informações críticas que podem ser usadas para identificar o tipo de ameaças este sistema é vulnerável a, que inclui os artigos da Microsoft KB associados com a atualização de segurança e o Bulletin de MS com mais detalhes sobre a vulnerabilidade.

### <a name="monitoring-identity-and-access"></a>Monitoramento de identidades e acesso

Com usuários trabalhando em qualquer lugar, usando diferentes dispositivos e acessar uma grande quantidade de aplicativos de nuvem e no local, é fundamental que suas credenciais estão protegidas. Ataques de roubo de credencial são aqueles em que um invasor inicialmente obtém acesso a regular credenciais de um usuário para acessar um sistema dentro da rede. Muitas vezes, esse ataque inicial é apenas uma maneira de obter acesso à rede, a meta é descobrir contas privilégios. 

Ataques permanecerá na rede, usando disponível gratuitamente ferramentas para extrair credenciais das sessões de outras contas de logon. Dependendo da configuração do sistema, essas credenciais podem ser extraídas na forma de hash, tíquetes ou senhas de texto sem formatação par.  

> [AZURE.NOTE] máquinas que são diretamente expostas à Internet verá muitos tentativas que tentam fazer login usando todos os tipos de nomes de usuário conhecidos (por exemplo, administrador). Na maioria dos casos é Okey se os nomes de usuário conhecidos não são usados e se a senha é suficientemente forte.

É possível identificar esses intrusos antes que eles afetem uma conta com privilégios. Você pode aproveitar **OMS segurança e solução de auditoria** para monitorar a identidades e acesso. Siga as etapas abaixo para acessar o painel de **identidade e acesso** :

1. No painel principal do **Pacote de gerenciamento de operações do Microsoft** , clique em segurança e auditoria lado a lado.
2. No painel de ferramentas **de segurança e auditoria** , clique em **identidades e acesso** em **Domínios de segurança**. No painel de **identidade e acesso** aparece como mostrado abaixo:

![identidade e acesso](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

Como parte de sua estratégia de monitoramento regular, você deve incluir o monitoramento de identidade. Administrador de TI deve parecer se houver um nome de usuário válida específico que tem muitas tentativas. Isso pode indicar qualquer invasor que adquiriu o nome de usuário real e tente força bruta ou uma ferramenta automática essa senha embutida usa que expirou.

Habilitar este dashboard IT para identificar rapidamente possíveis ameaças relacionadas a identidade e acesso aos recursos da empresa. É determinado importante também identifique tendências possíveis, por exemplo no bloco logon ao longo do tempo, você pode ver por período de tempo quantas vezes uma tentativa de logon foi executada. Nesse caso, o computador **servidor de arquivos** recebidos 35 tentativas de logon. Você pode explorar mais detalhes sobre este computador clicando nela. 

![mais detalhes](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

O relatório gerado para este computador traz detalhes importantes sobre este padrão. Ter notado que a coluna **conta** oferece a você a conta de usuário que foi usada para tentar acessar o sistema, a coluna **TIMEGENERATED** oferece a você o intervalo de tempo em que a tentativa foi feita e a coluna **LOGONTYPENAME** fornece o local onde essa tentativa foi feita. Se essas tentativas fossem executadas localmente no sistema por um programa, na coluna **processo** seria ser mostrando o nome do processo. Em cenários de onde vem a tentativa de logon de um programa, você já tem o nome do processo disponível e agora você pode executar mais investigação no sistema de destino.

## <a name="see-also"></a>Consulte também

Neste documento, você aprendeu como usar a segurança de OMS e solução para monitorar seus recursos de auditoria. Para saber mais sobre a segurança do OMS, consulte os seguintes artigos:

- [Visão geral do pacote de gerenciamento (OMS) operações](operations-management-suite-overview.md)
- [Introdução aos segurança do pacote de gerenciamento de operações e solução de auditoria](oms-security-getting-started.md)
- [Monitoramento e responder a alertas de segurança na segurança do pacote de gerenciamento de operações e solução de auditoria](oms-security-responding-alerts.md)