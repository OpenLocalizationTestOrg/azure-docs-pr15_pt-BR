<properties
    pageTitle="Perguntas frequentes sobre a análise de logon | Microsoft Azure"
    description="Respostas para perguntas frequentes sobre o serviço de análise de Log."
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

# <a name="log-analytics-faq"></a>Perguntas frequentes sobre a análise de log

Essa FAQ Microsoft é uma lista de perguntas frequentes sobre a análise de Log no Microsoft operações gerenciamento Suite (OMS). Se você tiver dúvidas adicionais sobre a análise de Log, vá para o [Fórum de discussão](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e envie suas perguntas. Alguém de nossa comunidade ajudará você a obter suas respostas. Se uma pergunta comumente é solicitada, adicionaremos-lo para este artigo para que ele pode ser encontrado rapidamente e facilmente.

## <a name="general"></a>Geral

**P. que verificações são executadas pela AD e soluções de avaliação de SQL?**

R. A consulta a seguir mostra uma descrição de todas as verificações executada atualmente:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Os resultados podem ser exportados para o Excel para análise posterior.

* *P: por que vejo algo diferente de *OMS* no SCOM Administration? * *

R: dependendo da qual atualização Rollup do SCOM que você estiver usando, você poderá ver um nó do *System Center Advisor*, *Ideias operacionais*ou *A análise de Log*.

A atualização de cadeia de caracteres de texto para *OMS* está incluída em um pacote de gerenciamento, o que precisa ser importados manualmente. Siga as instruções no artigo KB de acúmulo do SCOM atualização mais recente e atualize o console OMS para ver as atualizações mais recentes no nó *OMS* .

* *P: existe um *local* versão do OMS? * *

R: não. Análise de log processa e armazena muito grandes quantidades de dados. Como um serviço de nuvem, a análise de Log é capaz de escala-up se necessário e evitar algum impacto no desempenho ao seu ambiente.

Além disso, sendo um meio de serviço de nuvem que você não precisa acompanhar a infraestrutura de análise de Log e em execução e pode receber atualizações de recurso frequente e correções.

## <a name="configuration"></a>Configuração
**P. posso alterar o nome do contêiner de tabela/blob usado para ler do Azure diagnóstico (WAD)?**  

R.  Não, isso não é possível no momento, mas está planejado para uma versão futura.

**P. quais endereços fazer o uso de serviços OMS? Como garantir que o meu firewall permite apenas o tráfego para os serviços de OMS?**  

R. O serviço de análise de Log é criado na parte superior do Azure e os pontos de extremidade recebem IPs que estão nos [Intervalos de IP do Microsoft Azure data center](http://www.microsoft.com/download/details.aspx?id=41653).

Como as implantações de serviço são feitas, altere os endereços IP reais dos serviços OMS. Os nomes DNS para permitir através do firewall estão documentados em [definir configurações de proxy e firewall na análise de Log](log-analytics-proxy-firewall.md).

**P. Posso usar rota expressa para conexão com o Azure. Meu tráfego de análise de Log usará minha conexão de rota expressa?**  

R. Os tipos diferentes de tráfego de rota expressa descritos na [documentação de rota expressa](./expressroute/expressroute-faqs.md#supported-services).

O tráfego para a análise de Log usa o circuito de rota expressa correspondência público.

**P. existe uma maneira simple e fácil para mover um espaço de trabalho de análise de Log existente para outra assinatura do espaço de trabalho/Azure de análise de Log?**  Temos estão testando e espaços de trabalho OMS de várias do cliente que estamos foram testando em nossa assinatura do Azure, e elas estão mudando para produção queremos movê-los para seu próprios assinatura Azure/OMS.  

R. O `Move-AzureRmResource` cmdlet permitirá que você mover um espaço de trabalho de análise de Log e também uma conta de automação de uma assinatura do Azure para outro. Para obter mais informações, consulte [Mover AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

Essa alteração também pode ser feita no portal do Azure.

Você não pode mover dados de um espaço de trabalho de análise de Log para outro ou alterar a região que dados de análise de Log estão armazenados em.

**P: como posso adicionar OMS para SCOM?**

R: atualizando para o pacote cumulativo mais recente e importando pacotes de gerenciamento permitirá conectem SCOM a análise de Log.

Observe que a conexão SCOM para análise de Log só está disponível para SCOM 2012 SP1 e superior.

**P: como confirmar que um agente é capaz de se comunicar com a análise de Log?**

R: para garantir que o agente possa se comunicar com o OMS, vá para: painel de controle, configurações, **Microsoft Agent de monitoramento**e segurança.

Na guia de **Análise de Log do Azure (OMS)** , procure uma marca de seleção verde. Um ícone de marca de seleção verde confirma que o agente é capaz de se comunicar com o serviço OMS.

Um ícone de aviso amarelo significa que o agente está tendo problemas de comunicação com o OMS. Um motivo comum é o serviço do Microsoft Agent de monitoramento foi interrompido e precisa ser reiniciado.

**P: como eu cancelo um agente de comunicar-se com a análise de Log?**

R: no SCOM, remova o computador da lista de gerenciados OMS. Isso interrompe todas as comunicações por meio de SCOM para esse agente. Para agentes conectados diretamente à OMS, você pode pará-los de comunicar-se com o OMS através de: painel de controle, configurações, **Microsoft Agent de monitoramento**e segurança.
Em **Análise de Log do Azure (OMS)**, remova todos os espaços de trabalho listados.

## <a name="agent-data"></a>Dados do agente

**P. a quantidade de dados para pode enviar o agente para análise de Log? Existe uma quantidade máxima de dados por cliente?**  
R. O plano gratuito define uma ponta diária de 500 MB por espaço de trabalho. Os planos padrão e premium têm sem limite na quantidade de dados que são carregados. Como um serviço de nuvem, a análise de Log no OMS projetado para dimensionar automaticamente até alça o volume provenientes de um cliente – mesmo se estiver terabytes por dia.

O agente de análise de Log foi projetado para garantir que ele tenha um espaço reduzido e faz alguma compactação de dados básico. Um dos nossos clientes realmente escreveu um blog sobre os testes que elas realizadas contra nosso agente e como impressionados fossem. O volume de dados varia de acordo com as soluções permite que seus clientes. Você pode encontrar informações detalhadas sobre o volume de dados e ver a divisão por solução sob o **uso** de bloco na página de visão geral do OMS.

Para obter mais informações, você pode ler um [blog de cliente](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) sobre o espaço baixo do agente OMS.

**P. quanta largura de banda de rede é usada pelo agente de gerenciamento do Microsoft (MMA) ao enviar dados para a análise de Log?**

R. Largura de banda é uma função na quantidade de dados enviados. Dados são compactados quando eles são enviados pela rede.

**P. a quantidade de dados é enviada por agente?**

R. Isso depende em:

- as soluções que você tiver ativado
- o número de logs e contadores de desempenho sendo coletados
- o volume de dados em logs de

A camada de preços gratuita é uma boa maneira de integrado vários servidores e medidor o volume de dados típica. Geral uso é mostrado na página **uso** .
Para computadores que são capazes de executar o agente de WireData, você pode ver a quantidade de dados está sendo enviado usando a seguinte consulta:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```



## <a name="next-steps"></a>Próximas etapas

- [Começar a usar a análise de Log](log-analytics-get-started.md) para saber mais sobre a análise de Log e get em funcionamento em minutos.
