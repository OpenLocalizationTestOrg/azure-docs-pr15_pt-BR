<properties
   pageTitle="Recursos de detecção na Central de segurança do Azure | Microsoft Azure"
   description="Este documento ajuda você a entender como funcionam os recursos de detecção do Centro de segurança do Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-detection-capabilities"></a>Recursos de detecção do Centro de segurança do Azure
Este documento discute os recursos de detecção avançada do Centro de segurança do Azure, que ajuda a identificar ameaças active direcionamento seus recursos do Microsoft Azure e fornece a obtenção de informações necessárias para responder rapidamente.

> [AZURE.NOTE] Detecções avançadas estão disponíveis na Central de segurança padrão nível do Azure. Uma avaliação gratuita de 90 dias está disponível. Você pode atualizar a partir da seleção de nível de preços na [Política de segurança](security-center-policies.md). Visite a [página do Centro de segurança](https://azure.microsoft.com/pricing/details/security-center/) para saber mais sobre os preços. 


## <a name="responding-to-todays-threats"></a>Respondendo a ameaças hoje
Ter havido alterações significativas no cenário das ameaças nos últimos 20 anos. No passado, as empresas normalmente apenas tinham preocupar Desfiguração do site de ataques individuais que foram principalmente interessado vendo "o que ele podem fazer". Os hackers de hoje são muito mais sofisticados e organizados. Eles geralmente têm objetivos estratégicos e financeiros específicos. Eles também têm mais recursos disponíveis, como eles podem ser financiados por país estados ou crime organizado.

Essa abordagem levou a um nível inédito de profissionalismo nas classificações de invasor. Não é mais eles estão interessados em desfiguração da web. Agora está interessado em roube informações, contas financeiras e dados particulares – todos os quais eles podem usar para gerar dinheiro no mercado aberto ou aproveite um comercial em particular, a posição de política ou militar. Ainda mais referentes à que esses ataques com um objetivo financeiro as ataques que violam redes para prejudicar a infraestrutura e pessoas.

Em resposta, as organizações geralmente implantar várias soluções de ponto, com foco em defesa do perímetro da empresa ou pontos de extremidade olhando para assinaturas de ataques conhecidos. Essas soluções tendem a gerar um alto volume de alertas de baixa fidelidade, que exigem um analista de segurança para triagem e investigar. A maioria das organizações não têm o tempo e o conhecimento necessários para responder a esses alertas – tantas ir periféricos.  Enquanto isso, ataques tiveram desenvolvido seus métodos para examinar muitos proteção baseada em assinatura e [adaptar-se aos ambientes de nuvem](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Novas abordagens são necessárias para identificar ameaças novas e acelerar a detecção e resposta mais rapidamente. 

## <a name="how-azure-security-center-detects-and-responds-to-threats"></a>Como o Centro de segurança do Azure detecta e responde a ameaças

Pesquisadores de segurança da Microsoft estão constantemente atento ameaças. Eles têm acesso a um conjunto extenso de telemetria obtida com a presença global da Microsoft na nuvem e locais. Essa coleção contatar wide e diverso de conjuntos de dados permite que a Microsoft descobrir novas ataque padrões e tendências em seus produtos de consumidor e empresa local, bem como seus serviços onlinehttps. Como resultado, o Centro de segurança rapidamente pode atualizar seus algoritmos de detecção como explorações novas e cada vez mais sofisticadas de lançamento de ataques. Essa abordagem ajuda você a acompanhar com um ambiente de ameaças movimentação rápida. 

Detecção de ameaças do Centro de segurança funciona automaticamente coletar informações de segurança de seus recursos Azure, rede e soluções de parceiros conectada. Ele analisa essas informações, muitas vezes correlação informações de várias fontes, identificar ameaças. Prioridades de alertas de segurança na Central de segurança juntamente com recomendações sobre como corrigir a ameaças.

![Apresentação e coleta de dados do Centro de segurança](./media/security-center-detection-capabilities/security-center-detection-capabilities-fig1.png)

Centro de segurança utiliza a análise de segurança avançada, que vai além de abordagens baseadas em assinatura. Inovações em grande volume e [aprendizado de máquina](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) tecnologias são aproveitadas para avaliar eventos pela estrutura do inteiro nuvem – detectar ameaças que seriam impossíveis identificar usando abordagens manuais e prever a evolução dos ataques. Essas análises de segurança incluem: 

- **Inteligência de ameaças integrado**: procura conhecidos atores más aproveitando inteligência de ameaças globais de produtos da Microsoft e serviços, a Microsoft Digital Crimes unidade (DCU), o Centro de resposta de segurança da Microsoft (MSRC) e feeds externos.
- **Análise de comportamento**: aplica padrões conhecidos para descobrir comportamentos mal-intencionados. 
- **Detecção de anomalias**: usa as estatísticas de criação de perfil para criar uma linha de base histórica. Ele alertas sobre desvios das linhas de base estabelecidas que atendem a um vetor de ataque potencial.


### <a name="threat-intelligence"></a>Inteligência de ameaças
A Microsoft tem uma enorme quantidade de inteligência de ameaças globais. Telemetria flui de várias fontes, como Azure, o Office 365, o Microsoft CRM online, o Microsoft Dynamics AX, o outlook.com, @ MSN.com, o Microsoft Digital Crimes unidade (DCU) e Centro de resposta de segurança da Microsoft (MSRC). Pesquisadores também recebem informações de inteligência de ameaças que são compartilhadas entre os provedores de serviços de nuvem principais e assina ameaças inteligência feeds de terceiros. O Centro de segurança do Azure pode usar essas informações para alertar você ameaças de conhecidos más atores. Alguns exemplos incluem:

- **Comunicação de saída para um endereço IP mal-intencionado**: o tráfego de saída para botnet conhecido ou darknet provavelmente indica que o recurso foi comprometido e invasor-tentar executar comandos de dados sistema ou exfiltrate. O Centro de segurança do Azure compara o tráfego de rede para o banco de dados de ameaças globais da Microsoft e o alerta caso ele detecte comunicação para um endereço IP mal-intencionados.

## <a name="behavioral-analytics"></a>Análise de comportamento

Análise de comportamento é uma técnica que analisa e compara dados a uma coleção de padrões conhecidos. No entanto, esses padrões não são assinaturas simples. Eles são determinados por meio de algoritmos que são aplicados aos conjuntos de dados grandes de aprendizado de máquina complexa. Eles também são determinados por meio da análise cuidadosa de comportamentos maliciosos por analistas especializados. O Centro de segurança do Azure pode usar a análise de comportamento para identificar recursos comprometidos com base em análise de logs de máquina virtual, registros de dispositivos de rede virtual, logs de tecidos, despejos e outras fontes. 

Além disso, há correlação com outros sinais para verificar se há evidências de uma campanha de ampla de suporte. Essa correlação ajuda a identificar eventos que forem consistentes com indicadores estabelecidas do compromisso. Alguns exemplos incluem:

- **Execução do processo de Suspicious**: ataques utilizam várias técnicas para executar softwares mal-intencionados sem detecção. Por exemplo, um invasor pode dar malware os mesmos nomes de arquivos de sistema legítimo, mas colocar esses arquivos em um local alternativo, use um nome que é muito semelhante a um arquivo benigno ou máscara a extensão do arquivo true. Centro de segurança modela comportamentos de processos e monitores processam execuções para detectar destaques como essas.  
- **Tentativas de malware oculto e exploração**: malware sofisticado é capaz de enganar produtos de antimalware tradicional por nunca gravar em disco ou criptografar componentes de software armazenados em disco.  No entanto, esse tipo de malware pode ser detectado usando uma análise de memória, como o malware deve deixar rastreamentos na memória para funcionar. Quando o software falha, um despejo captura uma parte da memória no momento do travamento.  Analisando a memória no despejo, o Centro de segurança do Azure pode detectar técnicas usadas para explorar vulnerabilidades no software, acessar dados confidenciais e clandestinamente persistir com uma máquina comprometida sem afetar o desempenho do seu computador.
- **Lateral movimentação e reconhecimento interno**: para persistir em uma comprometido rede e localizar/coleta dados valioso, ataques muitas vezes tentam mover lateralmente de máquina comprometida para outras pessoas dentro da mesma rede. Central de segurança monitora atividades de processo e faça logon para descobrir tenta expandir destaque mal-intencionado dentro da rede, como sondagem de rede de execução de comando remoto e enumeração de conta.
- **Scripts do PowerShell mal-intencionados**: PowerShell está sendo usado pelo ataques para executar código mal-intencionado em máquinas virtuais de destino para diversas finalidades. Centro de segurança analisa atividade do PowerShell evidências de atividades suspeitas. 
- **Ataques de saída**: invasores costumam considerar os recursos de nuvem com o objetivo de como usar esses recursos para montar ataques adicionais. Máquinas virtuais comprometidas, por exemplo, pode ser usadas para iniciar ataques de força bruta contra outras máquinas virtuais, enviar SPAM ou digitalizar portas abertas e outros dispositivos na internet. Aplicando aprendizado de máquina ao tráfego de rede, a Central de segurança pode detectar quando comunicações de saída de rede excederem o normais. No caso de SPAM, o Centro de segurança também correlaciona tráfego de email incomuns com inteligência do Office 365 para determinar se o email é provável mal-intencionado ou o resultado de uma campanha de email legítimas.  

### <a name="anomaly-detection"></a>Detecção de anomalia

O Centro de segurança do Azure também usa detecção de anomalias para identificar ameaças. Em contraste para análise de comportamento (que depende padrões conhecidos derivados das grandes conjuntos de dados) e detecção de anomalia mais "personalizada" se concentra em linhas de base que são específicas para as implantações. Aprendizado de máquina é aplicado para determinar a atividade normal para suas implantações e, em seguida, as regras são geradas para definir condições de exceção que podem representar um evento de segurança. Aqui está um exemplo:

- **Entrada bruta de RDP/SSH forçar ataques**: as implantações podem ter ocupadas máquinas virtuais com um monte de logon cada dia e outras máquinas virtuais que têm poucas ou qualquer logon. O Centro de segurança do Azure pode determinar a atividade de login de linha de base para essas máquinas virtuais e usar para definir o que estiver fora de atividade de login normal de aprendizado de máquina. Se o número de logon, ou a hora do dia do logon ou o local do qual o logon é solicitado ou outras características de login relacionados é significativamente diferentes da linha de base, um alerta pode ser gerado. Novamente, aprendizado de máquina determina o que é significativo.

## <a name="continuous-threat-intelligence-monitoring"></a>Monitoramento de inteligência de ameaças contínua

O Centro de segurança do Azure opera pesquisa e dados ciência equipes de segurança que continuamente monitoram alterações no panorama de ameaças. Isso inclui as seguintes iniciativas:

- **Monitoramento de inteligência de ameaças**: ameaças inteligência inclui mecanismos, indicadores, implicações e acionáveis conselhos sobre existentes ou novas ameaças. Essas informações são compartilhadas na comunidade de segurança e Microsoft monitora continuamente ameaças feeds de inteligência de fontes internas e externas.
- **Compartilhamento de sinal**: ideias de equipes de segurança amplo portfólio da Microsoft de nuvem e local dispositivos de ponto de extremidade de serviços, servidores e cliente são compartilhadas e analisadas. 
- **Especialistas de segurança da Microsoft**: compromisso contínuo com equipes da Microsoft que funcionam em campos de segurança especializada, como judiciais e detecção de ataque na web.
- **Ajuste de detecção**: algoritmos são executados em relação a conjuntos de dados reais do cliente e pesquisadores de segurança de trabalham com clientes para validar os resultados. Positivos True e falsos são usados para refinar algoritmos de aprendizado de máquina.

Esses esforços combinados culminar em detecções novas e aprimoradas, que você pode se beneficiar instantaneamente – não há nenhuma ação para aproveitar.

## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu como a detecção de Central de segurança do Azure recursos funcionam. Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [O Centro de segurança do Azure guia de planejamento e operações](security-center-planning-and-operations-guide.md)
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md)
- [Alertas de segurança por tipo na Central de segurança do Azure](security-center-alerts-type.md)
- [Monitoramento na Central de segurança do Azure de integridade de segurança](security-center-monitoring.md) — Aprenda a monitorar a integridade dos seus recursos Azure.
- [Monitoramento soluções de parceiros com o Centro de segurança do Azure](security-center-partner-solutions.md) — Aprenda a monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre o Centro de segurança do Azure](security-center-faq.md) — localizar perguntas frequentes sobre como usar o serviço.
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) — localizar postagens no blog sobre segurança do Azure e conformidade.
