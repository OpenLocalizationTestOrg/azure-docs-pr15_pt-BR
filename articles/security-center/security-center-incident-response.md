<properties
   pageTitle="Usando o Centro de segurança do Azure para uma resposta de incidente | Microsoft Azure"
   description="Este documento explica como usar o Centro de segurança do Azure para um cenário de resposta ao incidente."
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
   ms.date="09/20/2016"
   ms.author="yurid"/>

# <a name="using-azure-security-center-for-an-incident-response"></a>Usando o Centro de segurança do Azure para uma resposta de incidente
Muitas organizações Saiba como responder a ocorrências de segurança somente depois de sofrer um ataque. Para reduzir os custos e danos, é importante ter uma resposta incidente planejar no lugar antes de um ataque ocorrerá. Você pode usar o Centro de segurança do Azure em diferentes estágios de uma resposta de incidente.

## <a name="incident-response-planning"></a>Planejamento de resposta a incidente

Um plano eficaz depende em três recursos principais: poder proteger, detectar e responder a ameaças. Proteção é sobre como evitar ocorrências, detecção é sobre como identificar ameaças antecipado e resposta é sobre como remover o invasor e restaurar sistemas para atenuar o impacto de uma violação.

Este artigo usará os estágios de resposta a incidente de segurança do artigo [Resposta de segurança do Microsoft Azure na nuvem](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678) , conforme mostrado no diagrama a seguir:

![Ciclo de vida de resposta ao incidente](./media/security-center-incident-response/security-center-incident-response-fig1.png)

Você pode usar o Centro de segurança durante os estágios de detectar, avaliar e diagnosticar. Aqui estão exemplos de como o Centro de segurança podem ser úteis durante os três estágios de resposta de incidente inicial:

- **Detectar**: revisar a primeira indicação de uma investigação do evento.
    - Exemplo: revisar a verificação inicial que um alerta de segurança de alta prioridade foi gerado no painel do Centro de segurança.
- **Avaliar**: realizar a avaliação inicial para obter mais informações sobre as atividades suspeitas.
    - Exemplo: obter mais informações sobre o alerta de segurança.
- **Diagnosticar**: conduzir uma investigação técnica e identificar estratégias de retenção, atenuação e solução alternativa.
    - Exemplo: siga as etapas de correção descritas pela Central de segurança esse alerta de segurança em particular.

O cenário que segue mostra como aproveitar o Centro de segurança durante os estágios de detectar, avaliar e diagnosticar/responder um incidente de segurança. No Centro de segurança, um [incidente de segurança](security-center-incident.md) é uma agregação de todos os alertas para um recurso que alinhar com padrões de [eliminar cadeia](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) . Ocorrências de aparecem no bloco de [alertas de segurança](security-center-managing-and-responding-alerts.md) e blade. Um incidente revela na lista de alertas relacionadas, que permite que você obtenha mais informações sobre cada ocorrência. Central de segurança também apresenta autônomo alertas de segurança que também podem ser usados para rastrear uma atividade suspeita.

## <a name="scenario"></a>Cenário

Contoso recentemente migrado alguns dos seus recursos locais para o Azure, incluindo alguns baseado na máquina virtual cargas de trabalho de linha de negócios e bancos de dados SQL. Atualmente, Core computador segurança incidente resposta equipe ERISC da Contoso () tem um problema investigar problemas de segurança por causa de inteligência de segurança não sejam integrada com suas ferramentas de resposta a incidente atual. Essa falta de integração apresenta um problema durante o estágio de detectar (muitos falsos positivos), bem como durante os estágios de avaliação e diagnosticar. Como parte da migração, ele decidiram optar no Centro de segurança para ajudá-lo a resolver esse problema.

A primeira fase de migração terminar após eles onboarded todos os recursos e endereçados todas as recomendações de segurança da Central de segurança. Contoso CSIRT é o ponto central para lidar com ocorrências de segurança do computador. A equipe consiste em um grupo de pessoas com responsabilidades para lidar com qualquer incidente de segurança. Os membros da equipe tenham definido claramente deveres para garantir que nenhuma área de resposta seja deixada descobertos.

Para este cenário, vamos abordar a foco nas funções das seguintes personas que fazem parte da Contoso CSIRT:

![Ciclo de vida de resposta ao incidente](./media/security-center-incident-response/security-center-incident-response-fig2.png)

Isabel é em operações de segurança. Suas responsabilidades incluem:

- Monitoramento e responder a ameaças de segurança o tempo todo.
- Aumentando ao proprietário de carga de trabalho de nuvem ou analista de segurança conforme necessário.

SAM é analista de segurança e suas responsabilidades incluem:

- Investigar ataques.
- Alertas de correção.
- Trabalhando com os proprietários de carga de trabalho para determinar e aplicar reduções.

Como você pode ver, Isabel e Sam tem responsabilidades diferentes, e elas devem trabalhar juntos para compartilhar informações do Centro de segurança.

## <a name="recommended-solution"></a>Solução recomendada

Como Isabel e Sam tem funções diferentes, ela usará o diferentes áreas da Central de segurança para obter informações relevantes para suas atividades diárias. Isabel usará **alertas de segurança** como parte do seu monitoramento diário.

![Alertas de segurança](./media/security-center-incident-response/security-center-incident-response-fig3.png)

Isabel usará alertas de segurança durante os estágios detectar e avaliar. Após Isabel concluir a avaliação inicial, ela pode escalonar o problema para Samuel se investigação adicional é necessária. Neste ponto, Sam usará as informações fornecidas pela Central de segurança, às vezes, em conjunto com outras fontes de dados, mover para o estágio de diagnosticar.


## <a name="how-to-implement-this-solution"></a>Como implementar esta solução

Para ver como você usaria o Centro de segurança do Azure em um cenário de resposta ao incidente, podemos será siga as etapas de Isabel nos estágios detectar e avaliar e, em seguida, confira o que acontece Sam para diagnosticar o problema.

### <a name="detect-and-assess-incident-response-stages"></a>Detectar e avaliar estágios de resposta ao incidente

Isabel conectado ao portal do Azure e está trabalhando no console do Centro de segurança. Como parte do seu diária monitorar as atividades, ela iniciou a revisão alertas de segurança de alta prioridade, executando as seguintes etapas:

1. Clique no bloco de **alertas de segurança** e acesso a lâmina de **alertas de segurança** .
    ![Blade de alerta de segurança](./media/security-center-incident-response/security-center-incident-response-fig4.png)

    > [AZURE.NOTE] Para este cenário, Isabel vai realizar uma avaliação no alerta de atividade de SQL maliciosos, como visto na figura anterior.
2. Clique no alerta de **atividade de SQL mal-intencionado** e examine os recursos de atacado na lâmina **SQL mal-intencionado atividade** :  ![detalhes do incidente](./media/security-center-incident-response/security-center-incident-response-fig5.png)

    Este blade, Isabel pode fazer as anotações sobre os recursos de atacado, como quantas vezes esse ataque aconteceu e quando ele foi detectado.
3. Clique no **ataque recursos** para obter mais informações sobre esse ataque.

Após ler a descrição, Isabel é convencida que isso não é um falso positivo e que ela deverá escalar nesse caso para Sam.

### <a name="diagnose-incident-response-stage"></a>Diagnosticar estágio de resposta ao incidente

SAM recebe o caso de Isabel e inicia revisando as etapas de correção que o Centro de segurança sugerido.

![Ciclo de vida de resposta ao incidente](./media/security-center-incident-response/security-center-incident-response-fig6.png)

### <a name="additional-resources"></a>Recursos adicionais

A equipe de resposta a incidente também pode tirar proveito do recurso de [Segurança central Power BI](security-center-powerbi.md) para ver diferentes tipos de relatórios. Esses relatórios podem ajudá-los durante investigações para visualizar, analisar e filtrar recomendações e alertas de segurança. Para empresas que usam suas informações de segurança e a solução de gerenciamento (SIEM) de evento durante o processo de investigação, eles também podem [integrar o Centro de segurança com a solução](security-center-integrating-alerts-with-log-integration.md). Você também pode integrar logs de auditoria Azure e eventos de segurança de máquina virtual (VM) usando a [ferramenta de integração do Azure log](https://blogs.msdn.microsoft.com/azuresecurity/2016/07/21/microsoft-azure-log-integration-preview/). Para investigar um ataque, você pode usar essas informações em conjunto com as informações que fornece o Centro de segurança.


## <a name="conclusion"></a>Conclusão

Montar uma equipe antes que um incidente ocorra é muito importante para sua organização e positiva influenciam como ocorrências são tratadas. Ter as ferramentas certas para monitorar os recursos pode ajudar a essa equipe tomar medidas precisas para remediar um incidente de segurança. Centro de segurança [recursos de detecção](security-center-detection-capabilities.md) podem ajudar TI rapidamente responder a ocorrências de segurança e corrigir problemas de segurança.
