<properties
    pageTitle="Otimizar seu ambiente com a solução de avaliação do Active Directory da análise de Log | Microsoft Azure"
    description="Você pode usar a solução de avaliação do Active Directory para avaliar o risco e a integridade dos seus ambientes de servidor em um intervalo regular."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="optimize-your-environment-with-the-active-directory-assessment-solution-in-log-analytics"></a>Otimizar seu ambiente com a solução de avaliação do Active Directory da análise de Log

Você pode usar a solução de avaliação do Active Directory para avaliar o risco e a integridade dos seus ambientes de servidor em um intervalo regular. Este artigo ajudará você a instalar e usar a solução para que você possa ser corretivas problemas em potencial.

Essa solução fornece uma lista de prioridades de recomendações específicas em sua infraestrutura de servidor implantado. As recomendações são categorizadas em quatro áreas de foco que ajudarão-lo rapidamente entender o risco e execute a ação.

As recomendações baseiam o conhecimento e a experiência obtida pelos engenheiros da Microsoft de milhares de visitas de cliente. Cada recomendação fornece orientações sobre por que um problema pode são importantes para você e como implementar as alterações sugeridas.

Você pode escolher áreas de foco que são mais importantes para sua organização e controlar seu progresso em direção executando um ambiente de saúde e gratuitos de risco.

Depois que você adicionou a solução e uma avaliação estiver concluída, resumo informações para as áreas de foco são mostradas no painel de **avaliação do AD** para a infraestrutura em seu ambiente. As seções a seguir descrevem como usar as informações no painel de **avaliação do AD** , onde você pode exibir e então realizar ações recomendadas para sua infraestrutura de servidor do Active Directory.

![imagem do bloco de avaliação de SQL](./media/log-analytics-ad-assessment/ad-tile.png)

![imagem do painel de avaliação de SQL](./media/log-analytics-ad-assessment/ad-assessment.png)


## <a name="installing-and-configuring-the-solution"></a>Instalando e configurando a solução
Use as informações a seguir para instalar e configurar as soluções.

- Agentes devem ser instalados em controladores de domínio que são membros do domínio a ser avaliada.
- A solução de avaliação do Active Directory requer o .NET Framework 4 instalado em cada computador que tenha um agente OMS.
- Adicione a solução de avaliação do Active Directory ao seu espaço de trabalho OMS usando o processo descrito em [soluções de adicionar a análise de Log da Galeria de soluções](log-analytics-add-solutions.md).  Não há nenhuma configuração adicional necessária.

    >[AZURE.NOTE] Depois que você adicionou a solução, o arquivo de AdvisorAssessment.exe é adicionado aos servidores com agentes. Dados de configuração é ler e, em seguida, enviados para o serviço OMS na nuvem para processamento. Lógica é aplicada aos dados recebidos e os dados de registros do serviço de nuvem.

## <a name="active-directory-assessment-data-collection-details"></a>Detalhes de conjunto de dados de avaliação do diretório ativos

Avaliação do Active Directory coleta dados WMI, dados do registro e dados de desempenho usando os agentes que você ativou.

A tabela a seguir mostra os métodos de coleta de dados para agentes, se Operations Manager (SCOM) é necessária e como dados geralmente são coletados por um agente.

| plataforma | Agente de direta | Agente do SCOM | Armazenamento do Azure | SCOM necessário? | Enviados via grupo de gerenciamento de dados do SCOM agente | frequência de conjunto |
|---|---|---|---|---|---|---|
|Windows|![Sim](./media/log-analytics-ad-assessment/oms-bullet-green.png)|![Sim](./media/log-analytics-ad-assessment/oms-bullet-green.png)|![Não](./media/log-analytics-ad-assessment/oms-bullet-red.png)|   ![Não](./media/log-analytics-ad-assessment/oms-bullet-red.png)|![Sim](./media/log-analytics-ad-assessment/oms-bullet-green.png)| 7 dias|


## <a name="understanding-how-recommendations-are-prioritized"></a>Noções básicas sobre como recomendações são priorizamos

Cada recomendação feita é dado um valor de ponderação que identifica a importância relativa da recomendação. Apenas as dez recomendações mais importantes são mostradas.

### <a name="how-weights-are-calculated"></a>Como pesos são calculados

Ponderações são valores agregados com base em três principais fatores:

- A *probabilidade* que um problema identificado causará problemas. Maior probabilidade equivale a uma pontuação geral maior para a recomendação.

- O *impacto* do problema na sua organização se ele causar um problema. Um impacto maior equivale a uma pontuação geral maior para a recomendação.

- O *esforço* necessário para implementar a recomendação. Um esforço maior equivale a uma pontuação geral menor para a recomendação.

A ponderação para cada recomendação é expresso como uma porcentagem do total pontuação disponível para cada área de foco. Por exemplo, se uma recomendação na área de foco de segurança e conformidade tiver uma pontuação de 5%, implementar essa recomendação aumentará seu % de pontuação por 5 de segurança e conformidade geral.

### <a name="focus-areas"></a>Áreas de foco

**Segurança e conformidade** - nesta área de foco mostra recomendações para requisitos de conformidade regulatória e técnico, legal e violações, políticas corporativas e possíveis ameaças de segurança.

**Disponibilidade e continuidade de negócios** - nesta área de foco mostra recomendações para disponibilidade do serviço, resiliência de sua infraestrutura e proteção de negócios.

**Desempenho e escalabilidade** - nesta área de foco mostra recomendações para ajudar a sua organização infraestrutura crescer, certifique-se de que seu ambiente de TI atende aos requisitos de desempenho atuais e é capaz de responder às necessidades de infraestrutura.

**Atualizar, migração e implantação** - nesta área de foco mostra recomendações para ajudá-lo a atualizar, migrar e implantar o Active Directory em sua infraestrutura existente.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Você deve objetivo pontuação 100% em cada área de foco?

Não necessariamente. As recomendações baseiam o conhecimento e experiências obtidas pelos engenheiros da Microsoft em milhares de visitas de cliente. Entretanto, sem infra-estruturas dois server são as mesmas e recomendações específicas podem ser mais ou menos relevantes para você. Por exemplo, algumas recomendações de segurança podem ser menos relevantes se suas máquinas virtuais não estão expostas na Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para serviços que fornecem o conjunto de dados ad hoc de baixa prioridade e relatórios. Problemas que são importantes para um negócio desenvolvido podem ser menos importantes para uma inicialização. Talvez você queira identificar quais áreas de foco são suas prioridades e examine como sua pontuação alterar ao longo do tempo.

Cada recomendação inclui orientações sobre por que é importante. Você deve usar este guia para avaliar se implementar a recomendação é adequada para você, considerando a natureza dos seus serviços de TI e as necessidades de negócios da sua organização.

## <a name="use-assessment-focus-area-recommendations"></a>Usar recomendações de área de foco de avaliação

Antes de você pode usar uma solução de avaliação do OMS, você deve ter a solução instalada. Para ler mais sobre como instalar soluções, consulte [soluções de adicionar a análise de Log da Galeria de soluções](log-analytics-add-solutions.md). Depois de instalado, você pode exibir o resumo de recomendações usando o bloco de avaliação na página Visão geral na OMS.

Exiba as avaliações de conformidade resumidos para sua infraestrutura e, em seguida, drill-em recomendações.


### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para exibir recomendações para uma área de foco e tomar medidas corretivas

1. Na página **Visão geral** , clique no bloco de **avaliação** para sua infraestrutura de servidor.
2. Na página de **avaliação** , revise as informações de resumo em uma das lâminas área foco e, em seguida, clique em um para exibir recomendações para a área foco.
3. Em qualquer uma das páginas de área de foco, você pode exibir as prioridades recomendações para seu ambiente. Clique em uma recomendação em **Objetos afetados** para exibir detalhes sobre por que a recomendação é feita.  
    ![imagem de recomendações de avaliação](./media/log-analytics-ad-assessment/ad-focus.png)
4. Você pode tomar ações corretivas sugeridas na **Ações sugeridas**. Quando o item foi resolvido, avaliações posteriores gravará recomendado ações foram realizadas e sua pontuação de conformidade aumentará. Itens corrigidos aparecem como **Objetos passado**.

## <a name="ignore-recommendations"></a>Ignorar recomendações

Se você tiver recomendações que você deseja ignorar, você pode criar um arquivo de texto que OMS usará para impedir que as recomendações apareçam nos resultados da avaliação.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Identificar recomendações que você irá ignorar

1.  Entre no seu espaço de trabalho e abrir pesquisa de Log. Use a seguinte consulta às recomendações de lista com falha para computadores em seu ambiente.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Aqui está uma captura de tela mostrando a consulta de pesquisa de Log: ![falha recomendações](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)

2.  Escolha recomendações que você deseja ignorar. Você usará os valores para RecommendationId no próximo procedimento.


### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Criar e usar um arquivo de texto IgnoreRecommendations.txt

1.  Crie um arquivo denominado IgnoreRecommendations.txt.
2.  Cole ou digite cada RecommendationId para cada recomendação que você deseja que a análise de Log para ignorar em uma linha separada e, em seguida, salve e feche o arquivo.
3.  Coloque o arquivo na seguinte pasta em cada computador onde você deseja OMS ignorar recomendações.
    - Em computadores com o Microsoft Monitoring Agent (conectado diretamente ou por meio do Operations Manager) - *unidade do sistema*: \Program Files\Microsoft Agent\Agent de monitoramento
    - No servidor de gerenciamento do Operations Manager - *unidade do sistema*: \Program Files\Microsoft Manager\Server de R2\Operations do System Center 2012

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar que recomendações são ignoradas

Depois da próxima agendada execuções de avaliação, por padrão a cada 7 dias, as recomendações especificadas são marcadas *ignorado* e não aparecerá no painel de avaliação.

1. Você pode usar as consultas de pesquisa de Log a seguir lista todas as recomendações ignoradas.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

2.  Se você decidir depois que você deseja ver ignorados recomendações, remover quaisquer arquivos de IgnoreRecommendations.txt ou você pode remover RecommendationIDs delas.

## <a name="ad-assessment-solutions-faq"></a>Soluções de avaliação do AD perguntas Frequentes

*Frequência uma avaliação é executado?*
- A avaliação é executado a cada 7 dias.

*Existe uma maneira de configurar a frequência de execução a avaliação?*
- Não neste momento.

*Se outro servidor para for detectado após adicionei uma solução de avaliação, será ele ser avaliado?*
- Sim, quando for detectado que é avaliado de então, cada 7 dias.

*Se um servidor é desativado, quando será-removido da avaliação?*
- Se um servidor não enviar dados para 3 semanas, ele será removido.

*O que é o nome do processo que faz a coleta de dados?*
- AdvisorAssessment.exe

*Quanto tempo leva para dados a serem coletados?*
- O conjunto de dados reais no servidor leva cerca de 1 hora. Pode levar mais tempo em servidores que têm um grande número de servidores do Active Directory.

*Tipo de dados é coletado?*
- Os seguintes tipos de dados são coletados:
    - WMI
    - Registro
    - Contadores de desempenho

*Existe uma maneira de configurar quando os dados são coletados?*
- Não neste momento.

*Por que exibir somente as recomendações de 10 principais?*
- Em vez de dando a você uma lista completa de impressionante das tarefas, recomendamos que você se concentrar endereçamento as recomendações prioridades primeiro. Depois de você solucioná-los, recomendações adicionais serão disponibilizados. Se você preferir ver a lista detalhada, você pode exibir todas as recomendações usando a pesquisa de Log.

*Existe uma maneira de ignorar uma recomendação?*
- Sim, consulte [recomendações de ignorar](#ignore-recommendations) seção acima.


## <a name="next-steps"></a>Próximas etapas

- Use [pesquisas de Log no Log de análise](log-analytics-log-searches.md) para exibir dados detalhados de avaliação do AD e recomendações.
