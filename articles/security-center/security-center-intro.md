<properties
   pageTitle="Introdução à Central de segurança do Azure | Microsoft Azure"
   description="Saiba mais sobre o Centro de segurança do Azure, seus recursos principais e como ele funciona."
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
   ms.date="07/21/2016"
   ms.author="terrylan"/>

# <a name="introduction-to-azure-security-center"></a>Introdução à Central de segurança do Azure

Saiba mais sobre o Centro de segurança do Azure, seus recursos principais e como ele funciona.

> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo.

## <a name="what-is-azure-security-center"></a>O que é o Centro de segurança do Azure?
 Central de segurança ajuda a evitar, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos seus recursos Azure. Ele fornece o monitoramento e a política de gerenciamento de segurança integrada em suas assinaturas Azure, ajuda a detectar ameaças que poderiam ir percebidas e funciona com uma enorme variedade de soluções de segurança.

##  <a name="key-capabilities"></a>Principais recursos
 Centro de segurança oferece ameaças eficaz e fácil de usar recursos de prevenção, detecção e resposta que são criados no Azure. Principais recursos são:

| | |
|----- |-----|
| Impedir | Monitora o estado de segurança de seus recursos Azure |
| | Define políticas para suas assinaturas do Azure e grupos de recursos com base em requisitos de segurança da sua empresa, os tipos de aplicativos que você usa e a sensibilidade dos seus dados |
| | Recomendações de segurança de usos orientado por políticas para orientar os proprietários de serviços durante o processo de implementação necessário controles |
| | Implanta rapidamente eletrodomésticos da Microsoft e parceiros e serviços de segurança |
| Detectar |Coleta e analisa dados de segurança de seus recursos Azure, rede e soluções de parceiros como programas de antimalware e firewalls automaticamente |
| | Utiliza global de ameaças inteligência de produtos da Microsoft e serviços, a Microsoft Digital Crimes unidade (DCU), o Centro de resposta de segurança da Microsoft (MSRC) e feeds externos |
| | Aplica-se a análise avançada, incluindo aprendizado de máquina e análise de comportamento |
| Responder | Fornece ocorrências/alertas de segurança prioridades |
| | Oferece ideias para a fonte do ataque e dos recursos afetados |
| | Sugere maneiras para interromper o ataque atual e ajudar a evitar ataques futuros |

## <a name="introductory-walkthrough"></a>Explicação passo a passo introdutória
 Você pode acessar o Centro de segurança a partir do [portal do Azure](https://azure.microsoft.com/features/azure-portal/). [Entre portal do](https://portal.azure.com), selecione **Procurar**e role para a opção de **Centro de segurança** ou no bloco do **Centro de segurança** que você anteriormente fixados no painel portal.

![Bloco de segurança no portal do Azure][1]

Central de segurança, você pode definir políticas de segurança, monitorar as configurações de segurança e exibir alertas de segurança.

### <a name="security-policies"></a>Políticas de segurança

Você pode definir políticas para assinaturas do Azure e grupos de recursos de acordo com os requisitos de segurança da sua empresa. Você também pode adaptá-los para os tipos de aplicativos que você está usando ou a sensibilidade dos dados em cada assinatura. Por exemplo, os recursos usados para desenvolvimento ou teste podem ter requisitos de segurança diferentes daqueles usados para aplicativos de produção. Da mesma forma, aplicativos com dados regulamentados como IIP podem exigir um nível mais alto de segurança.

> [AZURE.NOTE] Para modificar uma política de segurança no nível do assinatura ou o nível de grupo de recursos, você deve ser o proprietário da assinatura ou um colaborador para ele.

Na lâmina **Central de segurança** , selecione o bloco de **política** para obter uma lista das assinaturas e grupos de recursos.   

![Blade de centro de segurança][2]

Na lâmina **política de segurança** , selecione uma assinatura para exibir os detalhes de política.

![Assinatura de blade de política de segurança][3]

**Coleta de dados** (veja acima) permite que o conjunto de dados de uma política de segurança. Habilitando fornece:

- Varredura diária dos suporte máquinas virtuais para monitoramento de segurança e recomendações.
- Coleção de eventos de segurança para a detecção de ameaças e análise.

**Escolher uma conta de armazenamento por região** (veja acima) permite que você escolher, para cada região no qual você tem máquinas virtuais executando, a conta de armazenamento onde os dados coletados dessas máquinas virtuais estão armazenados. Se você não escolher uma conta de armazenamento para cada região, ele será criado para você. Os dados coletados são logicamente isolados do dados de outros clientes por razões de segurança.

> [AZURE.NOTE] Coleta de dados e escolhendo uma conta de armazenamento por região está configurado no nível de assinatura.

Selecione a **política de prevenção** (veja acima) para abrir a lâmina de **política de prevenção** . **Mostrar recomendações para** permite escolher os controles de segurança que você deseja monitorar e recomendar com base nas necessidades de segurança dos recursos na assinatura.

Em seguida, selecione um grupo de recursos para exibir detalhes de política.

![Grupo de recursos de blade de política de segurança][4]

**Herança** (veja acima) permite que você defina o grupo de recursos como:

- Herdado (padrão) que significa todas as políticas de segurança para esse grupo de recursos são herdadas do nível de assinatura.
- Exclusivo que significa que o grupo de recursos terá uma política de segurança personalizada. Você precisará fazer alterações em **Mostrar recomendações para**.

> [AZURE.NOTE] Se houver um conflito entre diretiva de nível de assinatura e política de nível de grupo de recursos, a diretiva de nível de grupo de recursos tem precedência.

### <a name="security-recommendations"></a>Recomendações de segurança

 Centro de segurança analisa o estado de segurança de seus recursos Azure para identificar possíveis vulnerabilidades de segurança. Uma lista de recomendações orienta você pelo processo de Configurando controles necessários. Alguns exemplos incluem:

- Provisionamento antimalware para ajudar a identificar e remover softwares mal-intencionados
- Configurando grupos de segurança de rede e regras para tráfego de controle para máquinas virtuais
- Provisionamento de firewalls de aplicativo da web para ajudar a proteção contra ataques destino seus aplicativos da web
- Implantando atualizações de sistema ausentes
- Configurações do sistema operacional que não coincidem com as linhas de base recomendadas de endereçamento

Clique no bloco de **recomendações** para obter uma lista de recomendações. Clique em cada recomendação para exibir informações adicionais ou agir para resolver o problema.

![Recomendações de segurança na Central de segurança do Azure][5]

### <a name="resource-health"></a>Integridade do recurso

O bloco de **integridade de segurança do recurso** mostra a postura geral de segurança do ambiente por tipo de recurso, incluindo máquinas virtuais, aplicativos da web e outros recursos.   

Selecione um tipo de recurso no bloco **integridade de segurança do recurso** para exibir mais informações, incluindo uma lista de qualquer possíveis vulnerabilidades de segurança que foram identificados. (**Máquinas virtuais** é selecionada no exemplo abaixo.)

![Bloco de integridade de recursos][6]

### <a name="security-alerts"></a>Alertas de segurança

 Centro de segurança automaticamente coleta, analisa e integra dados do log de seus recursos Azure, rede e soluções de parceiros como programas de antimalware e firewalls. Quando forem detectadas ameaças, um alerta de segurança é criado. Detecção de alguns exemplos:

- Comprometido máquinas virtuais se comunicar com os endereços IP maliciosos conhecidos
- Avançado malware detectado usando o relatório de erros do Windows
- Ataques de força bruta contra máquinas virtuais
- Alertas de segurança dos firewalls e programas de antimalware integrado

Clicar no bloco de **alertas de segurança** exibe uma lista de prioridades alertas.

![Alertas de segurança][7]

Selecionar um alerta mostra mais informações sobre o ataque e sugestões de como remediá-lo.

![Detalhes de alerta de segurança][8]

### <a name="partner-solutions"></a>Soluções de parceiros

O bloco de **soluções de parceiros** permite que você monitor rapidamente o status de integridade de suas soluções de parceiros integrado com sua assinatura do Azure. Central de segurança exibe alertas provenientes as soluções.

Selecione o bloco de **soluções de parceiros** . Um blade é aberta exibindo uma lista de todas as soluções de parceiros conectada.

![Soluções de parceiros][9]

## <a name="get-started"></a>Introdução
Para começar a usar o Centro de segurança, você precisa de uma assinatura do Microsoft Azure. Central de segurança é habilitada com sua assinatura do Azure. Se você não tiver uma assinatura, você pode inscrever-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

 Você pode acessar o Centro de segurança a partir do [portal do Azure](https://azure.microsoft.com/features/azure-portal/). Consulte a [documentação do portal](https://azure.microsoft.com/documentation/services/azure-portal/) para saber mais.

[Introdução ao centro de segurança do Azure](security-center-get-started.md) rapidamente orienta você durante o monitoramento de segurança e componentes de gerenciamento de políticas da Central de segurança.

## <a name="see-also"></a>Consulte também
Neste documento, você foram introduzidas para o Centro de segurança, seus recursos principais e como começar. Para saber mais, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) — Aprenda a configurar políticas de segurança para assinaturas do Azure e grupos de recursos.
- [Gerenciando recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) — Saiba como recomendações ajudam a proteger seus recursos Azure.
- [Monitoramento na Central de segurança do Azure de integridade de segurança](security-center-monitoring.md) — Aprenda a monitorar a integridade dos seus recursos Azure.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerenciar e responder a alertas de segurança.
- [Monitoramento soluções de parceiros com o Centro de segurança do Azure](security-center-partner-solutions.md) — Aprenda a monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre o Centro de segurança do Azure](security-center-faq.md) — localizar perguntas frequentes sobre como usar o serviço.
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) — Obtenha as últimas notícias de segurança do Azure e informações.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.png
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png
