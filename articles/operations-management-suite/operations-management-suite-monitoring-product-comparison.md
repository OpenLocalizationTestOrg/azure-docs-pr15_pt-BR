<properties 
   pageTitle="Microsoft monitoramento de comparação de produto | Microsoft Azure"
   description="Pacote de gerenciamento de operações da Microsoft (OMS) é a solução de gerenciamento de TI que ajuda você a gerenciar e proteger seu local e infraestrutura de nuvem com base de nuvem da Microsoft.  Este artigo identifica diferentes serviços incluídos no OMS e fornece links para o seu conteúdo detalhado."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="microsoft-monitoring-product-comparison"></a>Comparação de produtos monitoramento da Microsoft

Este artigo fornece uma comparação entre System Center Operations Manager (SCOM) e a análise de Log no pacote de gerenciamento de operações (OMS) em termos de sua arquitetura, a lógica de como eles monitoram recursos e como eles executarem análise dos dados coletados.  Isso é para fornecer um entendimento fundamental de suas diferenças e pontos fortes relativos.  

## <a name="basic-architecture"></a>Arquitetura básica
### <a name="system-center-operations-manager"></a>System Center Operations Manager

Todos os componentes do SCOM são instalados no seu centro de dados.  [Agentes são instalados](http://technet.microsoft.com/library/hh551142.aspx) em máquinas Windows e Linux que são gerenciados pelo SCOM.  Agentes de se conectar aos [Servidores de gerenciamento](https://technet.microsoft.com/library/hh301922.aspx) que se comunicar com o SCOM banco de dados e data warehouse.  Agentes dependem de autenticação de domínio para se conectar aos servidores de gerenciamento.  Os fora de um domínio confiável podem executar a autenticação de certificado ou conectar a um [Servidor de Gateway](https://technet.microsoft.com/library/hh212823.aspx).

SCOM requer dois bancos de dados do SQL, um para dados operacionais e outro data warehouse para dar suporte a relatórios e análise de dados.  Um [Servidor de relatório](https://technet.microsoft.com/library/hh298611.aspx) executa o SQL Reporting Services para relatar dados de data warehouse. 

SCOM pode monitorar recursos de nuvem usando pacotes de gerenciamento de produtos como [Azure](https://www.microsoft.com/download/details.aspx?id=38414), [Office 365](https://www.microsoft.com/download/details.aspx?id=43708)e [AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html).  Esses pacotes de gerenciamento usam um ou mais agentes locais como proxies para descoberta de recursos de nuvem e fluxos de trabalho em execução para medir seu desempenho e sua disponibilidade.  Agentes proxy também são usados para [Monitorar dispositivos de rede](https://technet.microsoft.com/library/hh212935.aspx) e outros recursos externos.

Console de operações é um aplicativo do Windows que se conecta a um dos servidores de gerenciamento e permite que o administrador exibir e analisar os dados coletados e configurar o ambiente do SCOM.  Um console baseado na web pode ser hospedado em qualquer servidor do IIS e fornece a análise de dados através de um navegador.

![Arquitetura do SCOM](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Análise de log

A maioria dos componentes OMS estão na nuvem Azure para que possa implantar e gerenciá-lo com um custo mínimo e esforço administrativo.  Todos os dados coletados pela análise de Log é armazenado no repositório OMS.

Análise de log pode coletar dados de uma das três fontes:

- Máquinas físicas e virtuais executando o Windows, o [Agente de monitoramento Microsoft (MMA)](https://technet.microsoft.com/library/mt484108.aspx) ou Linux e o [Agente do pacote de gerenciamento de operações para Linux](https://technet.microsoft.com/library/mt622052.aspx).  Essas máquinas podem ser local ou máquinas virtuais do Azure ou em outra nuvem.
- Uma conta de armazenamento do Azure com dados de [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) coletadas pela função de trabalho Azure, função web ou máquina virtual.
- [Conexão a um grupo de gerenciamento do SCOM](https://technet.microsoft.com/library/mt484104.aspx).  Nessa configuração, os agentes se comunicar com os servidores de gerenciamento do SCOM que entregar os dados para o banco de dados do SCOM onde ele é entregue ao armazenamento de dados OMS então.
Os administradores analisar os dados coletados e configurar a análise de Log com o portal OMS que está hospedado no Azure e pode ser acessado a partir de qualquer navegador.  Aplicativos móveis para acessar esses dados estão disponíveis para as plataformas padrão.

![Arquitetura de análise de log](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>Integração SCOM e a análise de Log

Quando SCOM é usado como uma fonte de dados para análise de Log, você pode aproveitar os recursos de ambos os produtos em um híbrido monitoramento ambiente.  Você pode configurar agentes SCOM existentes por meio do Console de operações a ser gerenciado pelo OMS, além de continuar a executar os pacotes de gerenciamento do SCOM.  
Dados de um grupo de gerenciamento do SCOM conectado foi entregue para a análise de Log usando um dos quatro métodos:

- Dados de desempenho e eventos são coletados pelo agente e entregue para SCOM.  Servidores de gerenciamento do SCOM depois entregar os dados para a análise de Log.
- Alguns eventos como logs do IIS e eventos de segurança continuam a ser entregue diretamente para a análise de Log do agente.
- Algumas soluções serão entregar software adicional para o agente ou exigir que o software seja instalado para coletar dados adicionais.  Esses dados geralmente serão enviados diretamente para a análise de Log.
- Algumas soluções coletará dados diretamente a partir de servidores de gerenciamento do SCOM que não se origina do agente.  Por exemplo, a [solução de gerenciamento de alerta](https://technet.microsoft.com/library/mt484092.aspx) coleta alertas do SCOM depois que eles foram criados.

## <a name="monitoring-logic"></a>Lógica de monitoramento
SCOM e a análise de Log de trabalham com dados semelhantes coletados de agentes mas têm diferenças fundamentais no como definir e implementar seu lógica para coleta de dados e como eles analisam os dados que eles coletam.

### <a name="operations-manager"></a>Operations Manager
Monitoramento lógica para SCOM está implementada [pacotes de gerenciamento](https://technet.microsoft.com/library/hh457558.aspx) que contêm lógica para descobrir componentes para monitorar, medir a integridade desses componentes e para coleta de dados de análise.  Dados de monitoramento podem ser tão simples quanto coletar um contador de desempenho ou evento, ou pode usar lógica complexa implementada em um script.  Pacotes de gerenciamento que incluem a monitoração completa estão disponíveis para uma variedade de [aplicativos de terceiros e Microsoft](http://go.microsoft.com/fwlink/?LinkId=82105) além de hardware e dispositivos de rede.  Você pode [criar seus próprios pacotes de gerenciamento](http://aka.ms/mpauthor) para aplicativos personalizados.

Pacotes de gerenciamento contêm vários fluxos de trabalho que cada executa alguns função distinct monitoramento como um contador de desempenho de amostragem, verificando o estado de um serviço ou executando um script.  Cada fluxo de trabalho é executado independentemente e define seus próprios resultados como banco de dados que ele gravará e se ele gerará um alerta. 

Você pode substituir detalhes de fluxo de trabalho como a frequência que eles são executados, o limite de onde elas considerarem um erro e a gravidade do alerta que eles geram.  Você também pode fornecer funcionalidade adicional, adicionando seus próprios fluxos de trabalho.

![Substitui](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

Pacotes de gerenciamento são instalados no banco de dados do Operations Manager e distribuídos automaticamente aos agentes por servidores de gerenciamento.  Cada agente irá baixar pacotes de gerenciamento e carregar fluxos de trabalho relevantes para os aplicativos que tiverem instalado automaticamente.  Dados coletados pelo agente foi entregue para o servidor de gerenciamento de inserção para o SCOM banco de dados e data warehouse.  Console de operações permite exibir e analisar dados por meio de exibições personalizadas, painéis e relatórios incluídos no pacote de gerenciamento.

A distribuição dos pacotes de gerenciamento é ilustrada no diagrama a seguir.

![Fluxo de pacote de gerenciamento](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Análise de log
#### <a name="event-and-performance-collection"></a>Coleta de desempenho e eventos
Análise de log coleta eventos e contadores de desempenho de sistemas de agente usando fontes como log de eventos do Windows, logs do IIS e Syslog.  Você pode definir critérios para os quais dados são coletadas por meio do portal de análise de Log e, em seguida, criar consultas de Log para analisar os dados coletados.  Um conjunto de critérios padrão é definido quando você cria seu espaço de trabalho do OMS, e você pode definir dados adicionais para determinados aplicativos. 

![Definindo logs de eventos no Log de análise](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

Enquanto SCOM tem muitos fluxos de trabalho detalhados que normalmente definem critérios específicos para dados e a ação que deve ser executada em resposta, a análise de Log tem mais gerais critérios para coleta de dados.  Consultas de log e soluções fornecem mais critérios de destino para analisar e atuando em dados específicos na nuvem, ele é foi coletado.

#### <a name="solutions"></a>Soluções
Soluções fornecem lógica adicional para análise e coleta de dados.  Você pode selecionar soluções para adicionar à sua assinatura de OMS da Galeria de soluções.

![Galeria de soluções](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

Soluções principalmente executadas na nuvem fornecendo análise de eventos e contadores de desempenho coletados no repositório OMS.  Eles também podem definir os dados a serem coletados adicionais que podem ser analisados com consultas de Log ou pela interface de usuário adicionais fornecidos pela solução no painel OMS. 

Por exemplo, a [solução de controle de alteração](https://technet.microsoft.com/library/mt484099.aspx) detecta alterações de configuração em sistemas de agente e grava eventos para o repositório OMS que podem ser analisados com várias exibições de gráficas que resumem detectou alterações.  Você pode fazer buscas detalhadas do modo de exibição resumido em consultas de log que exibem os dados detalhados coletados pela solução.


Embora você possa selecionar quais soluções que você adiciona à sua assinatura, você atualmente não tem a capacidade de criar suas próprias soluções.  Você pode selecionar os eventos e contadores de desempenho para coletar e criar exibições personalizadas com base em suas próprias consultas de log.

A lógica de monitoramento para a análise de Log é resumida no diagrama a seguir.

![Fluxo de solução de análise de log](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>Monitoramento de integridade
### <a name="operations-manager"></a>Operations Manager
SCOM pode modelar os diferentes componentes de um aplicativo e fornecer um integridade em tempo real para cada um.  Isso permite que você não apenas exibir detectado erros e desempenho ao longo do tempo, mas também para validar a integridade real de um aplicativo ou sistema e cada um dos seus componentes a qualquer momento.  Porque ele entende os períodos de tempo que um aplicativo está disponível, o mecanismo de integridade no SCOM também dá suporte a contratos de nível de serviço (SLA) que analisar e gerar relatórios sobre a disponibilidade de um aplicativo ao longo do tempo.

Por exemplo, o modo de exibição abaixo mostra a integridade em tempo real de mecanismos de banco de dados do SQL monitorados pelo SCOM.  A integridade de cada um dos bancos de dados para um dos mecanismos de banco de dados é mostrada na parte inferior metade do modo de exibição.

![Modo de exibição de estado](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

O Gerenciador de integridade de um dos mecanismos de banco de dados é mostrado abaixo com monitores que são usados para determinar sua integridade geral.  Esses monitores estão definidos no pacote de gerenciamento de SQL e executados em todos os mecanismos de banco de dados SQL descobertos pelo SCOM.

![Gerenciador de integridade](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

Componentes em vários sistemas podem ser combinadas para medir a integridade de um aplicativo distribuído.  Isso pode ser especialmente útil para aplicativos linha de negócios que incluem vários componentes distribuídos.  Você pode criar um modelo que avalia a integridade de cada componente que rollup em disponibilidade para o aplicativo.

Active Directory é um exemplo de um pacote de gerenciamento que fornece um modelo para analisar seus componentes distribuídos.  O diagrama de exemplo abaixo mostra a integridade de todo o ambiente e a relação entre florestas, domínios e controladores de domínio.  Cada um desses componentes inclui subcomponentes e vários monitores semelhantes ao exemplo SQL acima.

![Modo de exibição de diagrama SCOM](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Análise de log
OMS não inclua um mecanismo comum para aplicativos de modelo ou medir sua integridade em tempo real.  Soluções individuais podem avaliar a integridade geral dos serviços determinados com base em dados coletados e eles podem instalar lógica personalizada no agente para executar análise em tempo real.  Como soluções executadas na nuvem com acesso ao repositório OMS, elas geralmente fornecem uma análise mais profunda que geralmente é realizado por pacotes de gerenciamento. 

Por exemplo, as [soluções de avaliação do AD e avaliação de SQL](https://technet.microsoft.com/library/mt484102.aspx) analisar os dados coletados e fornecer uma classificação para diferentes aspectos do ambiente.  Ele inclui recomendações para melhorias que podem ser feitas para melhorar a disponibilidade e o desempenho do ambiente.

![Solução de avaliação do AD](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>Análise de dados
SCOM e a análise de Log fornecem diferentes recursos para analisar os dados coletados.  SCOM tem painéis e visualizações no Console de operações para analisar dados recentes em uma variedade de formatos e relatórios para apresentar dados de data warehouse em formato tabular.  Análise de log fornece uma interface e a linguagem de consulta de registro completo para analisar dados no repositório OMS.  Quando SCOM é usado como fonte de dados para análise de Log, o repositório inclui os dados coletados pelo SCOM para que as ferramentas de análise de Log podem ser usadas para analisar dados de ambos os sistemas.

### <a name="operations-manager"></a>Operations Manager

#### <a name="views"></a>Modos de exibição
Modos de exibição no Console de operações permite que você exiba diferentes tipos de dados coletados pelo SCOM em diferentes formatos, normalmente tabulares de eventos, alertas e dados de estado e gráficos de dados de desempenho de linha.  Modos de exibição executam análise mínimo ou consolidação de dados, mas permitir que a filtragem de acordo com critérios específicos. 

![Modos de exibição](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

Pacotes de gerenciamento normalmente fornecerá vários modos de exibição, o aplicativo ou sistema que ele monitora de suporte.  Isso pode incluir modos de exibição de estado para os diferentes objetos que o pacote de gerenciamento detecta, modos de exibição de alertas para problemas detectados e modos de exibição de desempenho para contadores.

Modos de exibição são particularmente adequados para analisar o estado atual do ambiente, incluindo alertas abertas e o estado de integridade de sistemas monitorados e objetos.  Você pode fazer drill down evento detalhado ou um alerta de determinado de suporte para diagnosticar a causa de dados de desempenho. Da mesma forma, você pode exibir o desempenho e a saúde de diferentes componentes de um aplicativo para avaliar sua integridade atual.

#### <a name="dashboards"></a>Painéis
Painéis no Console de operações principalmente trabalham com os mesmos dados, como modos de exibição, mas são mais personalizáveis e podem incluir visualizações mais sofisticadas.  Um conjunto de painéis padrão estão disponíveis que você pode personalizar facilmente para seus próprios fins.  Você também pode usar um widget do PowerShell que pode exibir os dados retornados de uma consulta do PowerShell.

![Painel de controle](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

Os desenvolvedores têm a capacidade de adicionar componentes personalizados aos painéis incluírem em seus pacotes de gerenciamento.  Elas podem ser altamente especializadas para um aplicativo específico como o painel no pacote de gerenciamento de SQL mostrado abaixo.  Esse painel também pode ser usado como um modelo para versões personalizadas.

![Painel SQL](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>Relatórios
Relatórios no SCOM analisar dados de data warehouse em formato tabular.  Eles podem ser impressos e programados para entrega automatizada em formatos de arquivo diferentes, incluindo PDF, CSV e Word.  Relatórios de trabalham com dados de data warehouse então são especialmente adequados para análise das tendências de longo prazo.

Pacotes de gerenciamento normalmente fornecerá relatórios personalizados para um aplicativo específico.  Você também pode selecionar de uma biblioteca de relatórios genéricas que você pode personalizar para seus próprios aplicativos ou para executar análise ad hoc.

A seguir é um relatório de desempenho de exemplo mostrando os dados coletados pelo pacote de gerenciamento de diretório ativo.

![Relatório](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Análise de log
Análise de log tem uma [linguagem de consulta](https://technet.microsoft.com/library/mt484120.aspx) que você pode usar para executar análises em dados de vários aplicativos sem a necessidade de criar um modo de exibição personalizado ou relatório.  Como OMS é implementado na nuvem, o desempenho de consultas e análise de dados não estão sujeitos a quaisquer limitações de hardware e pode analisar rapidamente consultas incluindo milhões de registros. 

Consultas em análise de Log também são a base de outros tipos de funcionalidade.  Você pode salvar uma consulta, exportar seus resultados para o Excel ou tê-la automaticamente executada em intervalos regulares e gerar um alerta se seus resultados correspondem aos critérios específicos.  

![Fluxo de consulta de log](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

Abaixo está um exemplo de uma consulta de análise de Log.  Neste exemplo, todos os eventos com "iniciado" no nome são retornados e agrupados por evento ID.  O usuário simplesmente fornece a consulta e a análise de Log gera dinamicamente a interface do usuário para executar a análise.  Selecionando qualquer item na lista retornará os dados de evento detalhadas.

![Consulta de log](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

Além de fornecer análise ad hoc, consultas em análise de Log podem ser salvo para uso futuro e também são adicionadas ao seu [painel OMS](http://technet.microsoft.com/library/mt484090.aspx) conforme mostrado no exemplo a seguir.

![Painel OMS](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>Próximas etapas

- Implante [System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh205987.aspx).
- Inscreva-se para [A análise de Log](https://azure.microsoft.com/documentation/services/log-analytics).  
