<properties
   pageTitle="Relatório de inteligência de ameaças do Centro de segurança do Azure | Microsoft Azure"
   description="Este documento ajuda você a usar relatórios inteligente do Azure segurança Center ameaças durante uma investigação para encontrar mais informações sobre um alerta de segurança."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/17/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-threat-intelligence-report"></a>Relatório de inteligência de ameaças do Centro de segurança do Azure
Este documento explica como relatórios inteligente do Azure segurança Center ameaças pode ajudar você a saber mais sobre uma ameaças que gerou um alerta de segurança.

## <a name="what-is-a-threat-intelligence-report"></a>O que é um relatório de inteligência de ameaças?
Detecção de ameaças do Centro de segurança funciona monitorando informações de segurança de seus recursos Azure, rede e soluções de parceiros conectada. Ele analisa essas informações, muitas vezes correlação informações de várias fontes, identificar ameaças. Esse processo é parte dos [recursos de detecção](security-center-detection-capabilities.md)de Central de segurança. 

Quando o Centro de segurança identifica uma ameaças, ele irá disparar um [alerta de segurança](security-center-managing-and-responding-alerts.md), que contém informações detalhadas sobre um evento específico, incluindo sugestões de correção. Para ajudar a resposta ao incidente equipes investigar e corrigir ameaças, o Centro de segurança inclui um relatório de inteligência de ameaças que contém informações sobre as ameaças que foi detectada, incluindo informações como a: 

- Identidade do invasor ou associações (se esta informação está disponível)
- Objetivos dos ataques
- Campanhas de ataque atuais e históricos (se esta informação está disponível)
- Táticas dos ataques, ferramentas e procedimentos
- Indicadores associados do compromisso (IoC) como URLs e hash do arquivo
- Victimology, que é o setor e prevalência geográfica para ajudá-lo a determinar se seus recursos Azure são em risco
- Informações de atenuação e correção

>[AZURE.NOTE] A quantidade de informações em qualquer relatório específico variará; o nível de detalhes baseia-se na atividade e prevalência o malware.

Central de segurança tem três tipos de relatórios de ameaças, que podem variar de acordo com o ataque. Os relatórios disponíveis são:

- **Relatório de grupo de atividades**: fornece profunda se ataques, seus objetivos e táticas.
- **Relatório de campanha**: se concentra nos detalhes de campanhas de ataque específico. 
- **Relatório de resumo de ameaças**: abrange todos os itens nos dois relatórios anterior.

Esse tipo de informação é muito útil durante o processo de [resposta a incidente](security-center-incident-response.md) , onde não há uma investigação em andamento para entender a origem do ataque, motivações do invasor e o que fazer para atenuar esse problema seguindo em frente. 

## <a name="how-to-access-the-threat-intelligence-report"></a>Como acessar o relatório de inteligência de ameaças?

Você pode examinar seus alertas atuais examinando o bloco de **alertas de segurança** . Abra o Portal do Azure e siga as etapas abaixo para ver mais detalhes sobre cada alerta:

1. No painel central de segurança, você verá o bloco de **alertas de segurança** .

2. Clique no bloco para abrir a lâmina de **alertas de segurança** que contém mais detalhes sobre os alertas e clique em alerta de segurança do que você deseja obter mais informações sobre.

    ![Alertas de segurança](./media/security-center-threat-report/security-center-threat-report-fig1.png)

3. Nesse caso a lâmina **suspeito processo executado** mostra os detalhes sobre o alerta, conforme mostrado na figura abaixo:

    ![Detais de alerta de segurança](./media/security-center-threat-report/security-center-threat-report-fig2.png)

4.  A quantidade de informações disponíveis para cada alerta de segurança varia de acordo com o tipo de alerta. No campo de **relatórios** , você tem um link para o relatório de inteligência de ameaças. Clique nela e outra janela do navegador será exibido com o arquivo PDF.

    ![Seleção de armazenamento](./media/security-center-threat-report/security-center-threat-report-fig3.png)

A partir daqui, você pode baixar o arquivo PDF para este relatório e ler mais sobre o problema de segurança que foi detectado e tomar ações com base nas informações fornecidas.

## <a name="see-also"></a>Consulte também

Neste documento, você aprendeu como relatórios inteligente do Azure segurança Center ameaças pode ajudar durante uma investigação sobre alertas de segurança. Para saber mais sobre o Centro de segurança do Azure, consulte o seguinte:

- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md). Encontre perguntas frequentes sobre como usar o serviço.
- [Aproveitando o Centro de segurança do Azure para resposta a incidente](security-center-incident-response.md)
- [Recursos de detecção do Centro de segurança do Azure](security-center-detection-capabilities.md)
- [Guia de planejamento do Centro de segurança do azure e operações](security-center-planning-and-operations-guide.md). Saiba como planejar e compreenda as considerações de design para adotar o Centro de segurança do Azure.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md). Saiba como gerenciar e responder a alertas de segurança.
- [Manipulando incidente de segurança na Central de segurança do Azure](security-center-incident.md)
- [Blog de segurança do azure](http://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre conformidade e segurança do Azure.
