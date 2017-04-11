<properties
   pageTitle="Central de segurança do Azure perguntas frequentes (FAQ) | Microsoft Azure"
   description="Estas perguntas Frequentes respondem dúvidas sobre o Centro de segurança do Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/27/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-frequently-asked-questions-faq"></a>O Centro de segurança do Azure perguntas frequentes (FAQ)

Estas perguntas Frequentes respondem dúvidas sobre o Centro de segurança do Azure, um serviço que ajuda você a evitar, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos seus recursos do Microsoft Azure.

## <a name="general-questions"></a>Perguntas gerais

### <a name="what-is-azure-security-center"></a>O que é o Centro de segurança do Azure?
O Centro de segurança do Azure ajuda você a evitar, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos seus recursos Azure. Ele fornece o monitoramento e a política de gerenciamento de segurança integrada em suas assinaturas, ajuda a detectar ameaças que poderiam ir percebidas e funciona com uma enorme variedade de soluções de segurança.

### <a name="how-do-i-get-azure-security-center"></a>Como faço para o Centro de segurança do Azure?
O Centro de segurança do Azure está habilitado com sua assinatura do Microsoft Azure e acessado a partir do [portal do Azure](https://azure.microsoft.com/features/azure-portal/). ([Entrar no portal](https://portal.azure.com), selecione **Procurar**e role até **O Centro de segurança**).  

## <a name="billing"></a>Cobrança

### <a name="how-does-billing-work-for-azure-security-center"></a>Como funciona o faturamento para o Centro de segurança do Azure?
Central de segurança é oferecida em dois níveis: livre e padrão.

A camada livre permite que você defina políticas de segurança e receber alertas de segurança, ocorrências e recomendações que orientação você durante o processo de configuração controles necessários. Com a camada gratuita, você também pode monitorar o estado de segurança de seus recursos Azure e soluções de parceiros integradas com sua assinatura do Azure.

A camada padrão fornece as detecções de recursos mais avançados de camada livre: ameaças intelligence, análise de comportamento, análise de falha e detecção de anomalias. Uma avaliação gratuita de 90 dias da camada padrão está disponível. Para atualizar, selecione o nível de preços na [política de segurança](security-center-policies.md#setting-security-policies-for-subscriptions). Consulte o [Centro de segurança preços](security-center-pricing.md) para saber mais.

## <a name="data-collection"></a>Coleta de dados

Centro de segurança coleta dados de suas máquinas virtuais para avaliar seu estado de segurança, recomendações de segurança e ameaças o alerta. Quando você primeiro acessar o Centro de segurança, coleta de dados está habilitada em todas as máquinas virtuais em sua assinatura. Coleta de dados é recomendável, mas você pode recusa, [Desabilitando o conjunto de dados](#how-do-i-disable-data-collection) na política de segurança central.

### <a name="how-do-i-disable-data-collection"></a>Como posso desabilitar a coleta de dados?

Você pode desabilitar a **coleta de dados** para uma assinatura na diretiva de segurança a qualquer momento. ([Entrar no portal do Azure](https://portal.azure.com), selecione **Procurar**, selecione **O Centro de segurança**e selecione **política**.)  Quando você seleciona uma assinatura, um novo blade é aberto e oferece a opção para desativar a **coleta de dados** . Selecione a opção **Excluir agentes** na faixa de opções principais para remover a agentes de máquinas virtuais existentes.

> [AZURE.NOTE] Políticas de segurança podem ser definidas no nível de assinatura Azure e nível de grupo de recursos, mas você deve selecionar uma assinatura para desativar a coleta de dados.

### <a name="how-do-i-enable-data-collection"></a>Como habilitar o conjunto de dados?
Você pode habilitar a coleta de dados para sua Azure inscrições na diretiva de segurança. Para habilitar a coleta de dados, [entrar no portal do Azure](https://portal.azure.com), selecione **Procurar**, selecione **O Centro de segurança**e selecione **política**. Defina o **conjunto de dados** para **na** e configurar as contas de armazenamento onde deseja que os dados a serem coletados para (consulte pergunta "[onde meus dados são armazenados?](#where-is-my-data-stored)"). Quando **a coleta de dados** está habilitado, ele automaticamente coleta informações de configuração e eventos de segurança de todas as máquinas virtuais com suporte na assinatura.

> [AZURE.NOTE] Políticas de segurança podem ser definidas no nível de assinatura Azure e nível de grupo de recursos, mas a configuração do conjunto de dados ocorre assinatura somente no nível do.

### <a name="what-happens-when-data-collection-is-enabled"></a>O que acontece quando o conjunto de dados está habilitado?
Coleta de dados é habilitada por meio do agente de monitoramento do Azure e a extensão de monitoramento de segurança do Azure. A extensão de monitoramento de segurança do Azure procura vários configuração relevantes de segurança e o envia em rastreamentos de [Rastreamento de eventos do Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Além disso, o sistema operacional cria entradas de log de eventos.  O agente de monitoramento do Azure lê entradas de log de eventos e ETW rastreia e copiá-los à sua conta de armazenamento para análise.  Esta é a conta de armazenamento que você configurou na política de segurança. Para obter mais informações sobre a conta de armazenamento, consulte pergunta "[onde meus dados são armazenados?](#where-is-my-data-stored)"

### <a name="does-the-monitoring-agent-or-security-monitoring-extension-impact-the-performance-of-my-servers"></a>A extensão do agente de monitoramento ou monitoramento de segurança afeta o desempenho do meu servidor (es)?
O agente de extensão consome uma quantidade nominal de recursos do sistema e deve ter pouco impacto sobre o desempenho.

### <a name="where-is-my-data-stored"></a>Onde são armazenados os meus dados?
Para cada região no qual você tem máquinas virtuais em execução, você deve escolher a conta de armazenamento onde os dados coletados dessas máquinas virtuais estão armazenados. Isso facilita a manter dados na mesma área geográfica para fins de Soberania de dados e privacidade. Você pode escolher a conta de armazenamento para uma assinatura na diretiva de segurança. ([Entrar no portal do Azure](https://portal.azure.com), selecione **Procurar**, selecione **O Centro de segurança**e selecione **política**.) Quando você clica em uma assinatura, abre uma nova lâmina. Selecione **contas de armazenamento de escolher** para selecionar uma região.

> [AZURE.NOTE] Políticas de segurança podem ser definidas no nível de assinatura Azure e nível de grupo de recursos, mas selecionar uma região para sua conta de armazenamento ocorre no nível de assinatura apenas.

Para saber mais sobre o armazenamento do Azure e contas de armazenamento, consulte a [Documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/) e [contas de armazenamento do Azure sobre](../storage/storage-create-storage-account.md).

## <a name="using-azure-security-center"></a>Usando o Centro de segurança do Azure

### <a name="what-is-a-security-policy"></a>O que é uma política de segurança?
Uma política de segurança define o conjunto de controles que são recomendados para recursos dentro da inscrição especificada ou o grupo de recursos. Na Central de segurança do Azure, você define políticas para assinaturas do Azure e grupos de recursos de acordo com os requisitos de segurança da sua empresa e do tipo de aplicativos ou sigilo dos dados de cada assinatura.

Por exemplo, os recursos usados para desenvolvimento ou teste podem ter requisitos de segurança diferentes daqueles usados para aplicativos de produção. Da mesma forma, aplicativos com dados regulamentados como IIP (informações de identificação pessoal) podem exigir um nível mais alto de segurança. As políticas de segurança habilitadas no Centro de segurança do Azure levarão monitoramento e recomendações de segurança. Para saber mais sobre políticas de segurança, consulte [monitoramento na Central de segurança do Azure de integridade de segurança](security-center-monitoring.md).

> [AZURE.NOTE] No caso de um conflito entre diretiva de nível de assinatura e política de nível de grupo de recursos, a diretiva de nível de grupo de recursos tem precedência.

### <a name="who-can-modify-a-security-policy"></a>Quem pode modificar uma política de segurança?
Políticas de segurança são configuradas para cada assinatura ou grupo de recursos. Para modificar uma política de segurança no nível de grupo de recursos ou nível de assinatura, você deve ser um proprietário ou colaborador dessa assinatura.

Para saber como configurar uma política de segurança, consulte [configuração de diretivas de segurança na Central de segurança do Azure](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>O que é uma recomendação de segurança?
O Centro de segurança do Azure analisa o estado de segurança de seus recursos Azure. Quando vulnerabilidades de segurança potenciais são identificadas, recomendações são criadas. As recomendações orientação-lo durante o processo de configurar o controle necessário. Exemplos são:

- Provisionamento de antimalware para ajudar a identificar e remover softwares mal-intencionados
- Configurando [Grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md) e regras para tráfego de controle para máquinas virtuais
- Provisionamento de um firewall de aplicativo da web para ajudar a proteger contra ataques direcionados a seus aplicativos web
- Implantando atualizações de sistema ausentes
- Configurações do sistema operacional que não coincidem com as linhas de base recomendadas de endereçamento

Somente as recomendações que estão habilitadas no políticas de segurança são mostradas aqui.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Como posso ver o estado de segurança atual dos meus recursos Azure?
Um bloco de **integridade de recursos** na **Central de segurança** lâmina mostra a postura geral de segurança do seu ambiente dividido por máquinas virtuais, aplicativos da web e outros recursos. Cada recurso tem mostrando um indicador se qualquer possíveis vulnerabilidades de segurança foram identificadas. Clicando no bloco de integridade de recursos exibe os recursos e identifica onde atenção é necessária ou podem haver problemas.

### <a name="what-triggers-a-security-alert"></a>O que dispara um alerta de segurança?
O Centro de segurança do Azure automaticamente coleta, analisa e funde dados do log de seus recursos Azure, rede e soluções de parceiros como antimalware e firewalls. Quando forem detectadas ameaças, um alerta de segurança é criado. Detecção de alguns exemplos:

- Comprometido máquinas virtuais se comunicar com os endereços IP maliciosos conhecidos
- Avançado malware detectado usando o relatório de erros do Windows
- Ataques de força bruta contra máquinas virtuais
- Alertas de segurança de soluções de segurança de parceiro integradas como antimalware ou Firewalls de aplicativo Web

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Qual é a diferença entre ameaças detectadas e alertado pelo Microsoft Security Response Center versus o Centro de segurança do Azure?
O Centro de resposta de segurança da Microsoft (MSRC) executa selecione segurança monitoramento da rede Azure e infraestrutura e recebe reclamações de inteligência e abuse ameaças de terceiros. Quando MSRC fica ciente de que os dados do cliente tem sido acessados por uma festa ilegal ou não autorizada ou que uso do cliente do Azure não cumprir os termos para uso aceitável, um gerente de incidente de segurança notifica o cliente. Notificação normalmente ocorre enviando um email para os contatos de segurança especificados na Central de segurança do Azure ou o proprietário da assinatura Azure se um contato de segurança não for especificado.

Central de segurança é um serviço Azure que monitora o ambiente do cliente Azure continuamente e aplica-se a análise para detectar automaticamente uma ampla variedade de atividade mal-intencionados. Essas detecções são reproduzidas como alertas de segurança no painel do Centro de segurança.

### <a name="how-are-permissions-handled-in-azure-security-center"></a>Como as permissões são tratadas na Central de segurança do Azure?
O Centro de segurança do Azure suporta acesso baseado em função. Para saber mais sobre o controle de acesso baseado em função (RBAC) no Azure, consulte [Controle de acesso baseado em função do Azure Active Directory](../active-directory/role-based-access-control-configure.md).

Quando um usuário abre a Central de segurança, somente recomendações e alertas que estão relacionadas aos recursos que o usuário tem acesso ao serão vistas. Isso significa que os usuários verão somente os itens relacionados a recursos onde o usuário está atribuído a função do proprietário, colaborador ou leitor a assinatura ou o grupo de recursos que pertence um recurso.

Se você precisar:

- **Editar uma política de segurança**, você deve ser um proprietário ou colaborador da assinatura.
- **Aplicar uma recomendação**, você deve ser um proprietário ou colaborador da assinatura.
- **Tem visibilidade o estado de segurança em todas as suas assinaturas**, você deve ser um proprietário, colaborador ou leitor (administrador de TI, equipe de segurança) de cada assinatura.
- **Tem visibilidade o estado de segurança de seus recursos**, você deve ser um grupo de recursos proprietário, colaborador ou leitor (DevOps).

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Quais recursos Azure são monitorados pela Central de segurança do Azure?
O Centro de segurança do Azure monitora os seguintes recursos Azure:

- Máquinas virtuais (VMs) (incluindo [Os serviços de nuvem](../cloud-services/cloud-services-choose-me.md))
- Redes virtuais Azure
- Serviço do SQL Azure
- Soluções de parceiros integradas com sua assinatura do Azure como um firewall de aplicativo web no VMs e no [Ambiente de serviço de aplicativo](../app-service/app-service-app-service-environments-readme.md)

## <a name="virtual-machines"></a>Máquinas virtuais

### <a name="what-types-of-virtual-machines-will-be-supported"></a>Que tipos de máquinas virtuais serão compatíveis?
Monitoramento de integridade de segurança e recomendações estão disponíveis para VMs (máquinas virtuais) criadas usando os dois a [classic e modelos de implantação do Gerenciador de recursos](../azure-classic-rm.md).

Há suporte para VMs do Windows:

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

Suporte Linux VMs:

- Ubuntu versões 12.04, 14.04, 16.04
- Debian versões 7, 8
- CentOS versões 6. \*, 7.*
- Versões do Red Hat Enterprise Linux (RHEL) 6. \*, 7.*
- SUSE Linux Enterprise Server (SLES) versões 11. \*, 12.*
- Versões de Linux Oracle 6. \*, 7.*

Também há suporte para VMs em execução em um serviço de nuvem. Funções da web e trabalhador executando em produção slots são monitorados dos serviços de nuvem somente. Para saber mais sobre o serviço de nuvem, consulte [Visão geral de serviços de nuvem](../cloud-services/cloud-services-choose-me.md).

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Por que a Central de segurança do Azure não reconhece a solução de antimalware em execução no meu máquina virtual do Azure?

O Centro de segurança do Azure só tem visibilidade antimalware instalado através do Azure extensões. Por exemplo, o Centro de segurança não é capaz de detectar antimalware que estava vem pré-instalado em uma imagem que você forneceu ou se você instalou o antimalware em suas máquinas virtuais usando seus próprio processos (como sistemas de gerenciamento de configuração).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Por que recebo a mensagem "Digitalizar dados ausentes" para minha máquina virtual?

Pode levar alguns instantes (geralmente menor que uma hora) para dados de verificação preencher depois de coleta de dados está habilitada na Central de segurança do Azure. Verificações não preencherá para VMs em um estado parado.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Por que recebo a mensagem "Agente de máquina virtual está ausente?"

O agente de máquina virtual deve ser instalado em VMs para habilitar coleta de dados. O agente de máquina virtual é instalado por padrão para VMs que são implantadas do Azure Marketplace. Para obter informações sobre como instalar o agente de máquina virtual em outras VMs, consulte o postagem de blog [agente de máquina virtual e extensões](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
