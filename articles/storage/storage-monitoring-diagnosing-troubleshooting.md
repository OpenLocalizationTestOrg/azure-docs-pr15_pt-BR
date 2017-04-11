<properties
    pageTitle="Monitorar, diagnosticar e solucionar problemas de armazenamento | Microsoft Azure"
    description="Use recursos, como a análise de armazenamento, log do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e solucionar problemas relacionados ao armazenamento do Azure."
    services="storage"
    documentationCenter=""
    authors="jasonnewyork"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="jahogg"/>

# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Monitorar, diagnosticar e solucionar problemas de armazenamento do Microsoft Azure

[AZURE.INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Visão geral

Diagnosticar e solucionar problemas em um aplicativo distribuído hospedado em um ambiente de nuvem podem ser mais complexas do que em ambientes tradicionais. Aplicativos podem ser implantados em uma infraestrutura PaaS ou IaaS, no local, em um dispositivo móvel ou em uma combinação de ambos. Normalmente, o tráfego de rede do seu aplicativo pode percorrer redes públicas e privadas e seu aplicativo pode usar várias tecnologias de armazenamento como tabelas de armazenamento do Microsoft Azure, Blobs, filas, ou arquivos além dos outros dados armazena tais como relacionais e bancos de dados do documento.

Para gerenciar esses aplicativos com êxito monitore-los proativamente e entender como diagnosticar e solucionar problemas de todos os aspectos da-los e suas tecnologias dependentes. Como um usuário de serviços de armazenamento do Azure, continuamente monitore os serviços de armazenamento que o aplicativo usa para alterações inesperadas no comportamento (como mais lento que tempos de resposta normal) e usar o log para coletar dados mais detalhados e analisar um problema em profundidade. As informações de diagnóstico obtidos tanto monitoramento e registro em log o ajudará a determinar a causa raiz do problema seu aplicativo encontrou. Em seguida, você pode solucionar o problema e determinar as etapas apropriadas, que você pode tomar para remediá-lo. Armazenamento do Azure é um serviço Azure núcleo e formulários uma parte importante da maioria das soluções que os clientes implantar a infraestrutura do Azure. Armazenamento do Azure inclui recursos para simplificar o monitoramento, diagnosticar e solucionar problemas de armazenamento em seus aplicativos baseados na nuvem.

> [AZURE.NOTE] Contas de armazenamento com um tipo de replicação de armazenamento redundantes de zona (ZRS) não tem o métricas ou o recurso de log habilitado no momento. Além disso, o serviço de arquivos do Azure não suporta log neste momento.

Para um guia prático para solução de problemas de ponta a ponta em aplicativos de armazenamento do Azure, consulte [Ponta a ponta usando métricas de armazenamento do Azure e o registro em log, o AzCopy e o analisador de mensagem de solução de problemas](storage-e2e-troubleshooting.md).

+ [Introdução]
    + [Como este guia é organizado]
+ [Monitorar seu serviço de armazenamento]
    + [Monitoramento de integridade do serviço]
    + [Capacidade de monitoramento]
    + [Disponibilidade de monitoramento]
    + [Monitorar o desempenho]
+ [Diagnosticar problemas de armazenamento]
    + [Problemas de integridade de serviço]
    + [Problemas de desempenho]
    + [Diagnosticar erros]
    + [Problemas de emulador de armazenamento]
    + [Ferramentas de log de armazenamento]
    + [Usando ferramentas de log de rede]
+ [Rastreamento de ponta a ponta]
    + [Dados do log de correlação]
    + [ID de solicitação do cliente]
    + [Identificação de solicitação do servidor]
    + [Carimbos de hora]
+ [Guia de solução de problemas]
    + [Métricas mostram AverageE2ELatency alto e baixo AverageServerLatency]
    + [Métricas mostram AverageE2ELatency baixa e baixa AverageServerLatency, mas o cliente está enfrentando alta latência]
    + [Métricas mostram AverageServerLatency alto]
    + [Você está enfrentando atrasos inesperados na entrega de mensagens em uma fila]
    + [Métricas mostram um aumento no PercentThrottlingError]
    + [Métricas mostram um aumento no PercentTimeoutError]
    + [Métricas mostram um aumento no PercentNetworkError]
    + [O cliente está recebendo mensagens de HTTP 403 (proibido)]
    + [O cliente está recebendo mensagens de HTTP 404 (não encontrado)]
    + [O cliente está recebendo mensagens de HTTP 409 (conflito)]
    + [Métricas mostram PercentSuccess baixa ou entradas de log de análise tem operações com status de transação da ClientOtherErrors]
    + [Métricas de capacidade mostram um aumento inesperado no uso de capacidade de armazenamento]
    + [Você está enfrentando reinicialização inesperada de máquinas virtuais que têm um grande número de VHDs anexados]
    + [Seu problema surge da usando o emulador de armazenamento para desenvolvimento ou teste]
    + [Você está encontrando problemas para instalar o SDK do Azure para .NET]
    + [Você tem um problema diferente com um serviço de armazenamento]
    + [Solução de problemas de arquivos do Azure com Windows e Linux](storage-troubleshoot-file-connection-problems.md)
+ [Apêndices]
    + [Apêndice 1: Usando Fiddler para capturar o tráfego HTTP e HTTPS]
    + [Apêndice 2: Usando Wireshark para capturar o tráfego de rede]
    + [Apêndice 3: Usando o analisador de mensagem do Microsoft para capturar o tráfego de rede]
    + [Apêndice 4: Usar o Excel para exibir métricas e dados de log]
    + [Apêndice 5: Monitoramento com ideias de aplicativo de serviços de equipe do Visual Studio]

## <a name="introduction"></a>Introdução

Este guia mostra como usar recursos como análise de armazenamento do Azure, cliente efetuar login na biblioteca de cliente de armazenamento do Azure e outras ferramentas de terceiros para identificar, diagnosticar e solucionar problemas de armazenamento do Azure problemas relacionados ao.

![][1]

*Figura 1 monitoramento, diagnóstico e solução de problemas*

Este guia destina-se a ser lido principalmente por desenvolvedores de serviços online que usam serviços de armazenamento do Azure e profissionais de TI responsável por gerenciar tais serviços onlinehttps. Os objetivos deste guia são:

- Para ajudá-lo a manter a integridade e o desempenho de suas contas de armazenamento do Azure.
- Para fornecer as ferramentas para ajudá-lo a decidir se uma questão ou problema em um aplicativo se relacionam ao armazenamento do Azure e processos necessários.
- Para fornecer orientação acionável para resolver problemas relacionados ao armazenamento do Azure.

### <a name="how-this-guide-is-organized"></a>Como este guia é organizado

A seção "[monitoramento seu serviço de armazenamento]" descreve como monitorar a integridade e o desempenho dos seus serviços de armazenamento do Azure usando métricas de análise de armazenamento do Azure (métricas de armazenamento).

A seção "[problemas de armazenamento diagnosticando]" descreve como diagnosticar problemas usando Azure armazenamento a análise de registro em log (log de armazenamento). Ele também descreve como habilitar o log do lado do cliente usando os recursos em uma das bibliotecas do cliente como a biblioteca de cliente de armazenamento para .NET ou o SDK do Azure para Java.

A seção "[rastreamento de ponta a ponta]" descreve como você pode correlação as informações contidas em vários arquivos de log e dados de métricas.

A seção "[guia de solução de problemas]" fornece orientação para solução de problemas para alguns dos relacionados ao armazenamento problemas comuns que você pode encontrar.

"[Apêndices]" incluem informações sobre como usar outras ferramentas como Wireshark e Netmon para analisar dados de pacote de rede, Fiddler para analisar mensagens HTTP/HTTPS e analisador de mensagem do Microsoft para correlação dados de log.


## <a name="monitoring-your-storage-service"></a>Monitorar seu serviço de armazenamento

Se você estiver familiarizado com o monitoramento de desempenho do Windows, você pode pensar métricas de armazenamento como sendo um equivalente de armazenamento do Azure de contadores do Monitor de desempenho do Windows. Métricas de armazenamento, você encontrará um conjunto abrangente de métricas (contadores na terminologia do Monitor de desempenho do Windows) como disponibilidade do serviço, número total de solicitações de serviço ou porcentagem de solicitações com êxito ao serviço. Para obter uma lista completa das métricas disponíveis, consulte [Esquema de tabela de métricas de análise de armazenamento](http://msdn.microsoft.com/library/azure/hh343264.aspx). Você pode especificar se deseja que o serviço de armazenamento para coletar e agregar métricas cada hora ou cada minuto. Para obter mais informações sobre como habilitar métricas e monitorar suas contas de armazenamento, consulte [Habilitando métricas de armazenamento e exibição de dados de métricas](http://go.microsoft.com/fwlink/?LinkId=510865).

Você pode escolher quais métricas por hora que você deseja exibir no [Portal do Azure](https://portal.azure.com) e configurar regras que notificar os administradores por email sempre que uma métrica por hora excede um determinado limite. Para obter mais informações, consulte [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). 

O serviço de armazenamento coleta métricas usando um melhor esforço, mas não é possível registrar todas as operações de armazenamento.

No Portal do Azure, é possível exibir métricas como disponibilidade, total de solicitações e números de latência média de uma conta de armazenamento. Uma regra de notificação também tiver sido configurada para alertar um administrador se disponibilidade cai abaixo de um determinado nível. Visualizem este dados, uma área possível para investigação é a porcentagem de sucesso do serviço de tabela sendo abaixo de 100% (para obter mais informações, consulte a seção "[métricas mostram PercentSuccess baixa ou entradas de log de análise tem operações com status de transação da ClientOtherErrors]").

Monitore continuamente seus aplicativos do Azure para garantir que eles estejam íntegra e desempenho conforme esperado por:

- Estabelecer algumas métricas de linha de base para o aplicativo que permite que você compare dados atuais e identificar quaisquer alterações significativas no comportamento do armazenamento do Azure e o seu aplicativo. Os valores de suas métricas de linha de base, em muitos casos, será aplicativo específico e você deve estabelece-los quando você estiver testando o aplicativo de desempenho.
- Gravação métricas de minuto e usá-los para monitorar ativamente erros inesperados e anomalias como picos em erro contagem ou solicitam taxas.
- Gravação métricas por hora e usá-los para monitorar valores médios como média contagens de erros e solicitam taxas.
- Investigar possíveis problemas usando ferramentas de diagnóstico, conforme discutido posteriormente na seção "[problemas de armazenamento diagnosticando]."

Os gráficos na Figura 3 abaixo ilustram como a média que ocorre para métricas por hora pode ocultar picos na atividade. As métricas por hora aparecem para mostrar uma taxa constante de solicitações, enquanto o minuto métricas revelam as flutuações que são realmente ocorrendo.

![][3]

O restante desta seção descreve que métricas monitore e por quê.

### <a name="monitoring-service-health"></a>Monitoramento de integridade do serviço

Você pode usar o [Portal do Azure](https://portal.azure.com) para exibir a integridade do serviço de armazenamento (e outros serviços do Azure) em todas as regiões do Azure em todo o mundo. Isso permite que você veja imediatamente se um problema fora do seu controle está afetando o serviço de armazenamento na região que você usa para seu aplicativo.

O [Portal do Azure](https://portal.azure.com) também pode fornecer notificações de ocorrências que afetam os vários serviços do Azure.
Observação: Essas informações estavam disponíveis anteriormente, juntamente com os dados históricos, no [Painel de serviços do Azure](http://status.azure.com).

Enquanto o [Portal Azure](https://portal.azure.com) coleta informações de integridade de dentro do Azure dos data centers (monitoramento de dentro para fora), você também pode considerar a adoção de uma abordagem de fora para gerar transações sintéticas que periodicamente acessarem seu aplicativo web hospedado no Azure de vários locais. Os serviços oferecidos pela [Dynatrace](http://www.dynatrace.com/en/synthetic-monitoring) e obtenção de informações do aplicativo para Visual Studio Team Services são exemplos desta abordagem fora-in. Para obter mais informações sobre a obtenção de informações do aplicativo para Visual Studio Team Services, consulte o apêndice "[Apêndice 5: monitoramento com ideias de aplicativo para Visual Studio Team Services](#appendix-5)."

### <a name="monitoring-capacity"></a>Capacidade de monitoramento

Métricas de armazenamento armazena apenas métricas de capacidade para o serviço de blob porque blobs normalmente conta para a proporção maior de dados armazenados (no momento da gravação, não é possível usar métricas de armazenamento para monitorar a capacidade de suas tabelas e filas). Você pode encontrar esses dados na tabela **$MetricsCapacityBlob** se você tiver habilitado o monitoramento do serviço Blob. Métricas de armazenamento registra esses dados uma vez por dia, e você pode usar o valor da **RowKey** para determinar se a linha contém uma entidade que se relacionam com dados de usuário (valor de **dados**) ou dados de análise (valor **analytics**). Cada entidade armazenada contém informações sobre a quantidade de armazenamento usada (**capacidade** medido em bytes) e o número atual de contêineres (**ContainerCount**) e blobs (**ObjectCount**) em uso na conta de armazenamento. Para saber mais sobre as métricas de capacidade armazenadas na tabela **$MetricsCapacityBlob** , consulte [Esquema de tabela de métricas de análise de armazenamento](http://msdn.microsoft.com/library/azure/hh343264.aspx).

> [AZURE.NOTE] Você deve monitorar esses valores para um aviso antecipado que você está se aproximando os limites de capacidade da sua conta de armazenamento. No Portal do Azure, você pode adicionar regras de alerta para notificá-lo se o uso de armazenamento agregado excede ou fica abaixo de limites que você especificar.

Para obter ajuda Estimando o tamanho de vários objetos de armazenamento como blobs, consulte o postagem de blog [Compreendendo Azure armazenamento cobrança – largura de banda, transações e capacidade](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a>Disponibilidade de monitoramento

Você deve monitorar a disponibilidade dos serviços de armazenamento em sua conta de armazenamento monitorando o valor na coluna **disponibilidade** nas tabelas por hora ou minuto métricas — **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. A coluna de **disponibilidade** contém um valor de porcentagem que indica a disponibilidade do serviço ou a operação de API representada pela linha (o **RowKey** mostra se a linha contiver métricas para o serviço como um todo ou para uma operação de API específica).

Qualquer valor menor que 100% indica que algumas solicitações de armazenamento estão falhando. Você pode ver por que eles estão falhando examinando as outras colunas dos dados de métricas que mostram os números das solicitações com tipos de erro diferente como **ServerTimeoutError**. Você deve esperar ver a **disponibilidade** ficar temporariamente abaixo de 100% por razões como tempos limite do servidor temporárias enquanto o serviço move partições a solicitação de melhor balanceamento de carga; a lógica de repetição em seu aplicativo cliente deve tratar tais condições intermitentes. O artigo [operações de fez Analytics de armazenamento e mensagens de Status](http://msdn.microsoft.com/library/azure/hh343260.aspx) lista os tipos de transação que métricas de armazenamento inclui em seu cálculo de **disponibilidade** .

No [Portal do Azure](https://portal.azure.com), você pode adicionar regras de alerta para notificá-lo se a **disponibilidade** de um serviço cai abaixo de um limite que você especificar.

A seção "[guia de solução de problemas]" deste guia descreve alguns problemas comuns de serviço de armazenamento relacionados a disponibilidade.

### <a name="monitoring-performance"></a>Monitorar o desempenho

Para monitorar o desempenho dos serviços de armazenamento, você pode usar as seguintes métricas das tabelas métricas por hora e minuto.

- Os valores nas colunas **AverageE2ELatency** e **AverageServerLatency** mostram o tempo médio o serviço de armazenamento ou tipo de operação de API está demorando para processar solicitações. **AverageE2ELatency** é uma medida de latência de ponta a ponta que inclui o tempo levado para ler a solicitação e enviar a resposta além o tempo necessário para processar a solicitação (portanto inclui latência de rede quando a solicitação atinge o serviço de armazenamento); **AverageServerLatency** é uma medida de apenas o tempo de processamento e, portanto, exclua qualquer latência de rede relacionada a comunicação com o cliente. Consulte a seção "[métricas mostram AverageE2ELatency alto e baixo AverageServerLatency]" posteriormente neste guia para uma discussão sobre por que pode haver uma diferença significativa entre esses dois valores.
- Os valores nas colunas **TotalIngress** e **TotalEgress** mostram a quantidade total de dados, em bytes, ao e indo cancelar seu serviço de armazenamento ou por meio de um tipo específico de operação de API.
- Os valores na coluna **TotalRequests** mostram o número total de solicitações de que o serviço de armazenamento de operação de API está recebendo. **TotalRequests** é o número total de solicitações que recebe o serviço de armazenamento.

Normalmente, você irá monitorar alterações inesperadas em qualquer um desses valores como um indicador que você tenha um problema que exige investigação.

No [Portal do Azure](https://portal.azure.com), você pode adicionar regras de alerta para notificá-lo se qualquer uma das medições de desempenho para este outono de serviço abaixo ou exceder um limite que você especificar.

A seção "[guia de solução de problemas]" deste guia descreve alguns problemas comuns de serviço de armazenamento relacionados ao desempenho.


## <a name="diagnosing-storage-issues"></a>Diagnosticar problemas de armazenamento

Há várias maneiras que você poderá ficar atento um problema ou problema em seu aplicativo, que incluem:

- Uma falha principal que faz com que o aplicativo falhar ou deixem de funcionar.
- Alterações significativas de valores da linha de base nas métricas estiver monitorando conforme descrito na seção anterior "[seu serviço de armazenamento de monitoramento]."
- Relatórios de usuários do seu aplicativo que alguns determinada operação não foi concluída conforme o esperado ou que alguns recursos não está funcionando.
- Erros gerados dentro de seu aplicativo que aparecem em arquivos de log ou por meio de qualquer outro método de notificação.

Normalmente, problemas relacionados aos serviços de armazenamento do Azure recaem em uma das quatro categorias:

- Seu aplicativo tem um problema de desempenho, relatados pelos usuários, ou exibido por alterações nas métricas de desempenho.
- Há um problema com a infraestrutura de armazenamento do Azure em uma ou mais regiões.
- Seu aplicativo está encontrando um erro, relatados pelos usuários, ou exibido por um aumento em um das métricas de contagem de erro que você monitorar.
- Durante o desenvolvimento e teste, talvez você esteja usando o emulador de armazenamento local; Você pode encontrar alguns problemas relacionados especificamente para uso da emulador de armazenamento.

As seções a seguir descrevem as etapas que você deverá seguir para diagnosticar e solucionar problemas em cada uma dessas quatro categorias. A seção "[guia de solução de problemas]" mais adiante neste guia fornece mais detalhes para alguns problemas comuns que você pode encontrar.

### <a name="service-health-issues"></a>Problemas de integridade de serviço

Problemas de integridade de serviço são normalmente fora do seu controle. O [Portal do Azure](https://portal.azure.com) fornece informações sobre problemas contínuos com o Azure serviços, incluindo serviços de armazenamento. Se você tiver optado por armazenamento de localização geográfica redundantes de acesso de leitura quando você criou sua conta de armazenamento, em seguida, em caso de seus dados não está disponível no local principal, seu aplicativo pode alternar temporariamente para a cópia somente leitura no local secundário. Para fazer isso, seu aplicativo deve ser capaz de alternar entre o uso de locais de armazenamento primário e secundário e possam trabalhar em um modo de funcionalidade reduzida com dados somente leitura. As bibliotecas de cliente de armazenamento do Azure permitem que você defina uma política de repetição que possa ler do armazenamento secundário no caso de falha de leitura do armazenamento principal. Seu aplicativo também precisa estar ciente de que os dados no local secundário eventualmente consistentes. Para obter mais informações, consulte o blog postar [Opções de redundância de armazenamento do Azure e armazenamento redundante de localização geográfica de acesso de leitura](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a>Problemas de desempenho

O desempenho de um aplicativo pode ser subjetivo, especialmente a partir de uma perspectiva do usuário. Portanto, é importante ter métricas de linha de base disponíveis para ajudá-lo a identificar onde talvez haja um problema de desempenho. Vários fatores podem afetar o desempenho de um serviço de armazenamento do Azure da perspectiva do aplicativo cliente. Esses fatores podem funcionar no serviço de armazenamento, no cliente ou na infraestrutura de rede; Portanto, é importante ter uma estratégia para identificar a origem do problema de desempenho.

Depois de ter identificado o local provavelmente a causa do problema de desempenho de métricas, em seguida, você pode usar os arquivos de log para encontrar informações detalhadas para diagnosticar e solucionar o problema.

A seção "[guia de solução de problemas]" posteriormente neste guia fornece mais informações sobre alguns desempenho comuns relacionado problemas que você pode encontrar.

### <a name="diagnosing-errors"></a>Diagnosticar erros

Usuários do seu aplicativo podem notificá-lo de erros relatados pelo aplicativo cliente. Métricas de armazenamento também registra contagens de tipos de erro diferente de seus serviços de armazenamento como **NetworkError**, **ClientTimeoutError**ou **AuthorizationError**. Enquanto métricas de armazenamento somente registros contagens de tipos de erro diferente, você pode obter mais detalhes sobre solicitações individuais examinando logs de rede, no lado do cliente e servidor. Normalmente, o código de status HTTP retornado pelo serviço de armazenamento lhe dará uma indicação de porque a solicitação falhou.

> [AZURE.NOTE] Lembre-se de que você deve esperar ver alguns erros intermitentes: por exemplo, erros devido a condições de rede temporário ou erros do aplicativo.

Os recursos a seguir são úteis para Noções básicas sobre códigos de status e de erro relacionados ao armazenamento:

- [Códigos de erro de API REST comuns](http://msdn.microsoft.com/library/azure/dd179357.aspx)
- [Códigos de erro de serviço de blob](http://msdn.microsoft.com/library/azure/dd179439.aspx)
- [Códigos de erro de serviço de fila](http://msdn.microsoft.com/library/azure/dd179446.aspx)
- [Códigos de erro de serviço de tabela](http://msdn.microsoft.com/library/azure/dd179438.aspx)
- [Códigos de erro de serviço de arquivo](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a>Problemas de emulador de armazenamento

O SDK do Azure inclui um emulador de armazenamento que você pode executar em uma estação de trabalho de desenvolvimento. Este emulador simula na maioria do comportamento dos serviços de armazenamento do Azure e é útil durante o desenvolvimento e teste, permitindo que você execute aplicativos que usam serviços de armazenamento do Azure sem a necessidade de uma assinatura do Azure e uma conta de armazenamento do Azure.

A seção "[guia de solução de problemas]" deste guia descreve alguns problemas comuns encontrados usando o emulador de armazenamento.

### <a name="storage-logging-tools"></a>Ferramentas de log de armazenamento

Log de armazenamento fornece log do lado do servidor de solicitações de armazenamento em sua conta de armazenamento do Azure. Para obter mais informações sobre como habilitar o log do lado do servidor e acessar os dados de log, consulte [Habilitando o log de armazenamento e acessando dados de Log](http://go.microsoft.com/fwlink/?LinkId=510867).

A biblioteca de cliente de armazenamento para .NET permite coletar dados de log do lado do cliente que se relacionam com operações de armazenamento executadas pelo seu aplicativo. Para obter mais informações, consulte [log do lado do cliente com a biblioteca de cliente de armazenamento do .NET](http://go.microsoft.com/fwlink/?LinkId=510868).

> [AZURE.NOTE] Em algumas circunstâncias (como falhas de autorização de SAS), um usuário pode relatar um erro para o qual você não pode encontrar nenhum dado de solicitação os logs de armazenamento do servidor. Você pode usar os recursos de log da biblioteca de cliente de armazenamento para investigar se a causa do problema é no cliente ou usar ferramentas de monitoramento de rede para investigar a rede.

### <a name="using-network-logging-tools"></a>Usando ferramentas de log de rede

Você pode capturar o tráfego entre o cliente e servidor para fornecer informações detalhadas sobre os dados que o cliente e servidor estiver trocando e as condições da rede subjacente. Ferramentas de log de rede úteis incluem:

- [Fiddler](http://www.telerik.com/fiddler) é uma web gratuita depuração proxy que permite que você examine os cabeçalhos e os dados de carga de mensagens de solicitação e resposta HTTP e HTTPS. Para obter mais informações, consulte [Apêndice 1: usando Fiddler para capturar o tráfego HTTP e HTTPS](#appendix-1).
- [Monitor de rede da Microsoft (Netmon)](http://www.microsoft.com/download/details.aspx?id=4865) e [Wireshark](http://www.wireshark.org/) são analisadores de protocolo de rede gratuito que permitem que você exibir informações detalhadas de pacote para uma ampla variedade de protocolos de rede. Para obter mais informações sobre Wireshark, consulte "[Apêndice 2: usando Wireshark para capturar o tráfego de rede](#appendix-2)".
- Analisador de mensagem da Microsoft é uma ferramenta da Microsoft que substitui o Netmon e que além de capturar dados de pacote de rede, ajuda você a exibir e analisar os dados de log capturados de outras ferramentas. Para obter mais informações, consulte "[Apêndice 3: usando o analisador de mensagem com a Microsoft para capturar o tráfego de rede](#appendix-3)".
- Se você quiser executar um teste de conectividade básica para verificar se o computador cliente pode se conectar ao serviço de armazenamento do Azure pela rede, você não pode fazer isso usando a ferramenta padrão **ping** no cliente. No entanto, você pode usar a [ferramenta de **tcping** ](http://www.elifulkerson.com/projects/tcping.php) para verificar a conectividade.

Em muitos casos, os dados de log do log de armazenamento e a biblioteca de cliente do armazenamento será suficientes para diagnosticar um problema, mas em alguns cenários, talvez seja necessário que essas ferramentas de log de rede podem fornecer informações mais detalhadas. Por exemplo, usando Fiddler para exibir mensagens HTTP e HTTPS permite exibir os dados de cabeçalho e carga enviados de e para os serviços de armazenamento, que permitem que você examine como um aplicativo cliente tentativas operações de armazenamento. Analisadores de protocolo como Wireshark operam no nível do pacote, permitindo que você visualize dados TCP, que permitem que você solucionar problemas de conectividade e pacotes perdidos. Analisador de mensagem pode operar em camadas HTTP e TCP.

## <a name="end-to-end-tracing"></a>Rastreamento de ponta a ponta

Rastreamento de ponta a ponta usando uma variedade de arquivos de log é uma técnica útil para investigar possíveis problemas. Você pode usar as informações de data/hora dos seus dados de métricas como uma indicação de onde começar procurando nos arquivos de log para obter as informações detalhadas que ajudarão você a solucionar o problema.

### <a name="correlating-log-data"></a>Dados do log de correlação

Ao exibir logs de aplicativos cliente, rede rastreia e solicitações de log de armazenamento de servidor é essencial sejam capazes de correlação entre os arquivos de log diferente. Os arquivos de log incluem um número de diferentes campos que são úteis como identificadores de correlação. O id de solicitação do cliente é o campo mais úteis para usar para correlação entradas em logs de diferentes. No entanto, às vezes, pode ser útil para usar o id de solicitação do servidor ou carimbos de hora. As seções a seguir fornecem mais detalhes sobre essas opções.

### <a name="client-request-id"></a>ID de solicitação do cliente

A biblioteca de cliente do armazenamento gera automaticamente um id de solicitação de cliente exclusivo para cada solicitação.

- No log do lado do cliente que cria a biblioteca de cliente do armazenamento, a id de solicitação do cliente é exibida no campo **ID de solicitação de cliente** de cada entrada de log relacionadas à solicitação.
- Em um rastreamento de rede como um capturado pelo Fiddler, a id de solicitação do cliente fica visível em mensagens de solicitação como o valor de cabeçalho **x-ms-cliente-solicitação-id** HTTP.
- No log de armazenamento registro em log do lado do servidor, a id de solicitação do cliente é exibida na coluna de ID de solicitação de cliente.

> [AZURE.NOTE]É possível para várias solicitações compartilhar a mesma identificação de solicitação de cliente porque o cliente pode atribuir esse valor (embora a biblioteca de cliente do armazenamento atribui automaticamente um novo valor). No caso de tentativas do cliente, todas as tentativas compartilham a mesma id de solicitação de cliente. No caso de um lote enviado do cliente, o lote tem uma id de solicitação de cliente único.


### <a name="server-request-id"></a>Identificação de solicitação do servidor

O serviço de armazenamento gera automaticamente ids de solicitação do servidor.

- No log de armazenamento registro em log do lado do servidor, a identificação de solicitação do servidor aparece coluna do **cabeçalho de identificação da solicitação** .
- Em um rastreamento de rede como um capturado pelo Fiddler, a identificação de solicitação do servidor aparece nas mensagens de resposta como o valor do cabeçalho **x-ms-solicitação-id** HTTP.
- No log do lado do cliente que cria a biblioteca de cliente do armazenamento, a identificação de solicitação do servidor aparece na coluna **Texto de operação** para a entrada de log mostrando detalhes da resposta do servidor.

> [AZURE.NOTE] O serviço de armazenamento sempre atribui um servidor exclusivo identificação da solicitação para cada solicitação recebida, para que cada tentativa de repetição do cliente e cada operação incluídas em um lote tem um id de solicitação de servidor exclusivo.

Se a biblioteca de cliente de armazenamento gera um **StorageException** no cliente, a propriedade **RequestInformation** contém um objeto de **RequestResult** que inclui uma propriedade **ServiceRequestID** . Você também pode acessar um objeto de **RequestResult** de uma instância de **OperationContext** .

Exemplo de código abaixo demonstra como definir um valor de **ClientRequestId** personalizado anexando um objeto **OperationContext** a solicitação para o serviço de armazenamento. Ele também mostra como recuperar o valor de **ServerRequestId** de mensagem de resposta.

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
    OperationContext oc = new OperationContext();
    oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

    try
    {
        CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
        ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
        var downloadToPath = string.Format("./{0}", blob.Name);
        using (var fs = File.OpenWrite(downloadToPath))
        {
            blob.DownloadToStream(fs, null, null, oc);
            Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
        }
    }
    catch (StorageException storageException)
    {
        Console.WriteLine("Storage exception {0} occurred", storageException.Message);
        // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
        foreach (var result in oc.RequestResults)
        {
                Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
        }
    }


### <a name="timestamps"></a>Carimbos de hora

Você também pode usar carimbos de hora para localizar entradas de log relacionados, mas tenha cuidado de qualquer distorção de relógio entre o cliente e servidor que pode existir. Você deve pesquisar mais ou menos 15 minutos para entradas de servidor com base no carimbo de hora no cliente correspondentes. Lembre-se de que os metadados de blob para os blobs contendo métricas indicam o intervalo de tempo para as métricas armazenados no blob; Isso é útil se você tiver muitos blobs métricas para o mesmo minuto ou hora.

## <a name="troubleshooting-guidance"></a>Guia de solução de problemas

Esta seção ajudará você com o diagnóstico e solução de problemas de alguns dos problemas comuns de seu aplicativo pode encontrar ao usar os serviços de armazenamento do Azure. Use a lista abaixo para localizar as informações relevantes para o seu problema específico.

**Árvore de decisão de solução de problemas**

----------

O problema está relacionada ao desempenho de um dos serviços de armazenamento?

- [Métricas mostram AverageE2ELatency alto e baixo AverageServerLatency]
- [Métricas mostram AverageE2ELatency baixa e baixa AverageServerLatency, mas o cliente está enfrentando alta latência]
- [Métricas mostram AverageServerLatency alto]
- [Você está enfrentando atrasos inesperados na entrega de mensagens em uma fila]

----------

O problema relaciona a disponibilidade de um dos serviços de armazenamento?

- [Métricas mostram um aumento no PercentThrottlingError]
- [Métricas mostram um aumento no PercentTimeoutError]
- [Métricas mostram um aumento no PercentNetworkError]

----------

Seu aplicativo cliente está recebendo uma resposta do HTTP 4XX (como 404) de um serviço de armazenamento?

- [O cliente está recebendo mensagens de HTTP 403 (proibido)]
- [O cliente está recebendo mensagens de HTTP 404 (não encontrado)]
- [O cliente está recebendo mensagens de HTTP 409 (conflito)]

----------

[Métricas mostram PercentSuccess baixa ou entradas de log de análise tem operações com status de transação da ClientOtherErrors]

----------

[Métricas de capacidade mostram um aumento inesperado no uso de capacidade de armazenamento]

----------

[Você está enfrentando reinicialização inesperada de máquinas virtuais que têm um grande número de VHDs anexados]

----------

[Seu problema surge da usando o emulador de armazenamento para desenvolvimento ou teste]

----------

[Você está encontrando problemas para instalar o SDK do Azure para .NET]

----------

[Você tem um problema diferente com um serviço de armazenamento]

----------

### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Métricas mostram AverageE2ELatency alto e baixo AverageServerLatency

A ilustração abaixo do [Portal Azure](https://portal.azure.com) ferramenta de monitoramento mostra um exemplo onde o **AverageE2ELatency** é significativamente maior do que o **AverageServerLatency**.

![][4]

Observe que o serviço de armazenamento somente calcula a métrica **AverageE2ELatency** para solicitações bem-sucedidas e, ao contrário de **AverageServerLatency**, inclui a hora em que o cliente leva para enviar os dados e receber a confirmação do serviço de armazenamento. Portanto, uma diferença entre **AverageE2ELatency** e **AverageServerLatency** poderia ser devido ao aplicativo cliente sendo lento para responder ou devido a condições na rede.

> [AZURE.NOTE] Você também pode exibir **E2ELatency** e **ServerLatency** individuais para operações de armazenamento dos dados de log log de armazenamento.

#### <a name="investigating-client-performance-issues"></a>Investigar problemas de desempenho do cliente

Razões possíveis para o cliente respondendo lentamente incluem tendo um número limitado de conexões disponíveis ou threads ou sendo poucos recursos como largura de banda de CPU, memória ou rede. Você pode ser capaz de resolver o problema modificando o código do cliente para ser mais eficiente (por exemplo usando chamadas assíncronas para o serviço de armazenamento), ou usando uma máquina Virtual maiores (com mais cores e mais memória).

Para os serviços de fila e tabela, o algoritmo de Nagle também pode causar alto **AverageE2ELatency** em comparação com **AverageServerLatency**: para obter mais informações, consulte a postagem [algoritmo do Nagle é não amigável em direção a solicitações de pequenas](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). Você pode desativar o algoritmo de Nagle no código, usando a classe **ServicePointManager** no namespace **System.Net** . Você deve fazer isso antes de você fazer as chamadas para a tabela ou os serviços de fila no seu aplicativo desde que isso não afeta as conexões que já estão abertos. O exemplo a seguir é proveniente de método **Application_Start** em uma função de trabalho.

    var storageAccount = CloudStorageAccount.Parse(connStr);
    ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
    tableServicePoint.UseNagleAlgorithm = false;
    ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
    queueServicePoint.UseNagleAlgorithm = false;

Você deve verificar os logs do lado do cliente para ver quantas solicitações que seu aplicativo cliente está enviando e verificar se há .NET gerais relacionados gargalos de desempenho no seu cliente como CPU, coleta de lixo .NET, utilização de rede ou memória. Como ponto de partida para aplicativos do cliente .NET de solução de problemas, consulte [depuração, rastreamento e perfil](http://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Investigar problemas de latência de rede

Normalmente, alta latência de ponta a ponta causada pela rede é devido a condições temporárias. Você pode investigar ambos os problemas de rede temporárias e persistente como pacotes ignorados usando ferramentas como Wireshark ou analisador de mensagem do Microsoft.

Para obter mais informações sobre como usar o Wireshark para solucionar problemas de rede, consulte "[Apêndice 2: usando Wireshark para capturar o tráfego de rede]."

Para obter mais informações sobre como usar o analisador de mensagem do Microsoft para solucionar problemas de rede, consulte "[Apêndice 3: usando o analisador de Microsoft Message para capturar o tráfego de rede]."

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Métricas mostram AverageE2ELatency baixa e baixa AverageServerLatency, mas o cliente está enfrentando alta latência

Neste cenário, a causa mais provável é um atraso nas solicitações de armazenamento contatar o serviço de armazenamento. Você deve investigar por solicitações do cliente não estão fazendo-la por meio de serviço blob.

Um motivo possível para o cliente atrasar enviando solicitações, há um número limitado de conexões disponíveis ou threads.

Você também deve verificar se o cliente está executando várias tentativas e investigar o motivo se esse for o caso. Para determinar se o cliente está executando várias tentativas, você pode:

- Examine os logs de análise de armazenamento. Se várias tentativas estão ocorrendo, você verá várias operações com o mesmo ID de solicitação de cliente, mas com IDs de solicitação de servidor diferente.
- Examine os logs de cliente. O registro extenso indicará que ocorreu uma nova tentativa.
- Depurar seu código e verifique as propriedades do objeto **OperationContext** associado à solicitação. Se a operação tem repetida, a propriedade **RequestResults** incluirá vários IDs de solicitação de servidor exclusivo. Você também pode verificar as horas de início e término para cada solicitação. Para obter mais informações, consulte o exemplo de código na seção de [identificação de solicitação do servidor].

Se não existem problemas no cliente, você deve investigar possíveis problemas de rede como perda de pacotes. Você pode usar ferramentas como Wireshark ou analisador de mensagem do Microsoft para investigar problemas de rede.

Para obter mais informações sobre como usar o Wireshark para solucionar problemas de rede, consulte "[Apêndice 2: usando Wireshark para capturar o tráfego de rede]."

Para obter mais informações sobre como usar o analisador de mensagem do Microsoft para solucionar problemas de rede, consulte "[Apêndice 3: usando o analisador de Microsoft Message para capturar o tráfego de rede]."

### <a name="metrics-show-high-AverageServerLatency"></a>Métricas mostram AverageServerLatency alto

No caso de alta **AverageServerLatency** para solicitações de download de blob, você deve usar os logs de log de armazenamento para ver se há solicitações repetidas para o mesmo blob (ou conjunto de blobs). Blob carregar solicitações, você deve investigar quais Bloquear tamanho o cliente está usando (por exemplo, bloqueia menores que 64K tamanho pode resultar em custos gerais indiretos, a menos que as leituras sejam também em menos de 64K blocos), e se vários clientes estão carregando blocos para o mesmo blob em paralelo. Você também deve verificar as métricas por minuto picos do número de solicitações que resultam em ultrapassando o por segundo destinos de escalabilidade: Consulte também "[métricas mostram um aumento no PercentTimeoutError]".

Se você estiver vendo alta **AverageServerLatency** blob baixar solicitações quando houver solicitações repetidas o mesmo blob ou conjunto de blobs, você deve considerar cache esses blobs usando o Cache do Azure ou a rede de entrega de conteúdo (CDN) do Azure. Para solicitações de carregamento, você pode melhorar a produtividade usando um tamanho maior de bloco. Para consultas a tabelas, também é possível implementar o cache do cliente em clientes que executam as mesmas operações de consulta e onde os dados não mudarem com frequência.

Valores altos de **AverageServerLatency** também podem ser um sintoma mal projetadas tabelas ou consultas que resultam em operações de varredura ou que seguem o antiacrescentar padrão de / preceder. Para obter mais informações, consulte "[métricas mostram um aumento no PercentThrottlingError]".

> [AZURE.NOTE] Você pode encontrar uma lista de verificação abrangente desempenho lista de verificação aqui: [lista de verificação de escalabilidade e desempenho de armazenamento do Microsoft Azure](storage-performance-checklist.md).

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Você está enfrentando atrasos inesperados na entrega de mensagens em uma fila

Se você estiver enfrentando um atraso entre o momento em que um aplicativo adiciona uma mensagem para uma fila e a hora em que ele fica disponível para ler da fila, você deve executar as seguintes etapas para diagnosticar o problema:

- Verifique se que o aplicativo com êxito está adicionando as mensagens na fila. Verifique se o aplicativo está não repetindo o método **AddMessage** várias vezes antes de sucesso. Os logs de biblioteca de cliente do armazenamento mostrará qualquer tentativas de operações de armazenamento.
- Verifique se não há nenhum relógio distorção entre a função de trabalho que adiciona a mensagem na fila e a função de trabalho que lê a mensagem da fila que torna aparecem como se há um atraso no processamento.
- Verifique se a função de trabalho que lê as mensagens da fila está falhando. Se um cliente fila chama o método **GetMessage** mas falha ao responder com uma confirmação, a mensagem permanecerá invisível na fila até que o período de **invisibilityTimeout** expirar. Neste ponto, a mensagem fica disponível para processar novamente.
- Verifique se o comprimento da fila está aumentando ao longo do tempo. Isso pode ocorrer se você não tiver trabalhadores suficientes disponíveis para processar todas as mensagens que outros funcionários estão colocando na fila. Você também deve verificar as métricas para ver se solicitações delete são falha e a contagem de dequeue em mensagens que podem indicar repetidas tentativas para excluir a mensagem.
- Examine os logs de log de armazenamento para qualquer operação de fila que têm mais alta que os valores de **E2ELatency** e **ServerLatency** esperados sobre um período de tempo maior que o normal.


### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Métricas mostram um aumento no PercentThrottlingError

Limitação erros ocorrem quando você exceder os destinos de escalabilidade de um serviço de armazenamento. O serviço de armazenamento faz isso para garantir que nenhum cliente único ou inquilino pode usar o serviço ao custo de outras pessoas. Para obter mais informações, consulte [metas de desempenho e escalabilidade de armazenamento do Azure](storage-scalability-targets.md) para obter detalhes sobre os destinos de escalabilidade para contas de armazenamento e metas de desempenho para partições nas contas de armazenamento.

Se a métrica **PercentThrottlingError** mostrar um aumento na porcentagem de solicitações que estão falhando com um erro de limitação, você precisará investigar uma das duas situações:

- [Aumento temporário de PercentThrottlingError]
- [Aumento permanente de erro de PercentThrottlingError]

Um aumento no **PercentThrottlingError** geralmente ocorre ao mesmo tempo como um aumento no número de solicitações de armazenamento, ou quando você estiver inicialmente carregar testar seu aplicativo. Isso pode também ocorrer no cliente como "503 servidor ocupado" ou mensagens de status HTTP "500 tempo limite da operação" de operações de armazenamento.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Aumento temporário de PercentThrottlingError

Se você estiver vendo picos no valor de **PercentThrottlingError** que coincidem com períodos de alta atividade para o aplicativo, você deve implementar uma exponencial (não linear) retirada estratégia para repetições no seu cliente: isso Reduza a carga imediata na partição e ajudar seu aplicativo para diminuir os picos de tráfego. Para obter mais informações sobre como implementar políticas de repetir usando a biblioteca de cliente do armazenamento, consulte [Namespace Microsoft.WindowsAzure.Storage.RetryPolicies](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx).

> [AZURE.NOTE] Você também poderá ver picos no valor de **PercentThrottlingError** que não coincidem com períodos de alta atividade para o aplicativo: Veja a causa mais provável é o serviço de armazenamento movendo partições para melhorar a balanceamento de carga.

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Aumento permanente de erro de PercentThrottlingError

Se você estiver vendo um valor consistentemente alto **PercentThrottlingError** seguinte testa um aumento permanente em seus volumes de transações, ou quando você estiver executando o carregamento inicial em seu aplicativo, em seguida, você precisa avaliar como o seu aplicativo estiver usando partições de armazenamento e se ele está se aproximando os destinos de escalabilidade para uma conta de armazenamento. Por exemplo, se você estiver vendo a otimização de erros em uma fila (que contagens como uma única partição), em seguida, considere usar filas adicionais para distribuir as transações entre várias partições. Se você estiver vendo a otimização de erros em uma tabela, você precisa considerar usando um esquema de partição diferentes para distribuir suas transações entre várias partições usando uma ampla variedade de valores de chave de partição. Uma causa comum desse problema é o anticolocar padrão de / acrescentar onde você selecionar a data como a chave da partição e, em seguida, todos os dados em um determinado dia é escrito para uma partição: sob carga, isso pode resultar em um gargalo de gravação. Você deve considerar um design de partição diferentes ou avaliar se usar o armazenamento de blob pode ser uma solução melhor. Você também deve verificar se a otimização está ocorrendo como resultado de picos em seu tráfego e investigar maneiras de suavização seu padrão de solicitações.

Se você distribuir suas transações em várias partições, você ainda deverá estar ciente dos limites de escalabilidade definidas para a conta de armazenamento. Por exemplo, se você usou dez filas o número máximo de 2.000 mensagens de 1KB por segundo cada processamento, será no limite geral de 20.000 mensagens por segundo para a conta de armazenamento. Se você precisar processar mais de 20.000 entidades por segundo, você deve considerar usando várias contas de armazenamento. Você também deve ter em mente que o tamanho das suas solicitações e entidades tem impacto sobre quando o serviço de armazenamento acelera seus clientes: se você tiver maiores solicitações e entidades, você pode ser limitado mais cedo.

Design da consulta ineficaz também pode causar a acertar os limites de escalabilidade para partições de tabela. Por exemplo, uma consulta com um filtro que só seleciona um por cento das entidades em uma partição, mas que verifica todas as entidades em uma partição precisará acessar cada entidade. Cada entidade ler será considera o número total de transações nessa partição; Portanto, você pode acessar facilmente os destinos de escalabilidade.

> [AZURE.NOTE] O teste de desempenho deve revelar qualquer designs de consulta ineficaz em seu aplicativo.

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Métricas mostram um aumento no PercentTimeoutError

Seu métricas mostram um aumento no **PercentTimeoutError** por um dos seus serviços de armazenamento. Ao mesmo tempo, o cliente recebe um alto volume de mensagens de status HTTP "500 tempo limite da operação" de operações de armazenamento.

> [AZURE.NOTE] Você pode ver os erros de tempo limite temporariamente como o serviço de armazenamento carregar solicitações de saldos movendo uma partição para um novo servidor.

A métrica **PercentTimeoutError** é uma agregação das seguintes métricas: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**e **SASServerTimeoutError**.

Os tempos limite do servidor são causados por um erro no servidor. Os limites de cliente ocorrem porque uma operação no servidor excedeu o tempo limite especificado pelo cliente; Por exemplo, um cliente usando a biblioteca de cliente do armazenamento pode definir um tempo limite para uma operação usando a propriedade **ServerTimeout** da classe **QueueRequestOptions** .

Tempos limite do servidor indica um problema com o serviço de armazenamento que requer o investigação. Você pode usar métricas para ver se você estiver atingir os limites de escalabilidade do serviço e identificar qualquer picos de tráfego que podem estar causando esse problema. Se o problema for intermitente, pode ser devido a balanceamento de carga de atividade no serviço. Se o problema é persistente e não é causado pelo seu aplicativo atingir os limites de escalabilidade do serviço, você deverá aumentar um problema de suporte. Para tempos limite de cliente, você deve decidir se o tempo limite está definido como um valor apropriado no cliente e qualquer alteração que o valor de tempo limite definido no cliente ou investigar como você pode melhorar o desempenho das operações no serviço de armazenamento, por exemplo, otimizando suas consultas de tabela ou reduzir o tamanho de suas mensagens.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>Métricas mostram um aumento no PercentNetworkError

Seu métricas mostram um aumento no **PercentNetworkError** por um dos seus serviços de armazenamento. A métrica **PercentNetworkError** é uma agregação das seguintes métricas: **NetworkError**, **AnonymousNetworkError**e **SASNetworkError**. Ocorrem quando o serviço de armazenamento detecta um erro de rede quando o cliente faz uma solicitação de armazenamento.

A causa mais comum desse erro é um cliente desconectando antes que um tempo limite expire no serviço de armazenamento. Você deve investigar o código no seu cliente compreender por que e quando o cliente se desconecta do serviço de armazenamento. Você também pode usar Wireshark, analisador de mensagem do Microsoft ou Tcping para investigar problemas de conectividade de rede do cliente. Essas ferramentas são descritas nos [apêndices].

### <a name="the-client-is-receiving-403-messages"></a>O cliente está recebendo mensagens de HTTP 403 (proibido)

Se seu aplicativo cliente é jogar erros de HTTP 403 (proibido), uma causa provável é que o cliente está usando um expirado compartilhados acesso assinatura (SAS) quando ele envia uma solicitação de armazenamento (embora outras possíveis causas incluem relógio chaves inválido distorção e cabeçalhos vazios). Se uma chave SAS expirada é a causa, você não verá as entradas dos dados de log armazenamento registro em log do lado do servidor. A tabela a seguir mostra uma amostra de log do lado do cliente gerado pela biblioteca de cliente de armazenamento que ilustra este problema ocorrendo:

Fonte|Detalhamento|Detalhamento|Id de solicitação do cliente|Texto de operação
---|---|---|---|---
Microsoft.WindowsAzure.Storage|Informações|3|85d077ab-...|Iniciando operação local principal modo local PrimaryOnly por.
Microsoft.WindowsAzure.Storage|Informações|3|85d077ab-...|Iniciando síncrona solicitação para https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;sr = c&amp;si = mypolicy&amp;sig = OFnd4Rd7z01fIvh % 2BmcR6zbudIH2F5Ikm % 2FyhNYZEmJNQ % 3D&amp;versão da api = 2014-02-14.
Microsoft.WindowsAzure.Storage|Informações|3|85d077ab-...|Aguardando resposta.
Microsoft.WindowsAzure.Storage|Aviso|2|85d077ab-...|Exceção ao aguardando resposta: O servidor remoto retornou um erro: (403) proibido..
Microsoft.WindowsAzure.Storage|Informações|3|85d077ab-...|Resposta recebida. Código de status = 403, solicitar ID = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 =, ETag =.
Microsoft.WindowsAzure.Storage|Aviso|2|85d077ab-...|Exceção durante a operação: O servidor remoto retornou um erro: (403) proibido..
Microsoft.WindowsAzure.Storage|Informações|3 |85d077ab-...|Verificando se a operação deve ser repetida. Repetir contagem = 0, código de status HTTP = 403, exceção = o servidor remoto retornado um erro: (403) proibido..
Microsoft.WindowsAzure.Storage|Informações|3|85d077ab-...|O próximo local foi definido principal, baseada no modo local.
Microsoft.WindowsAzure.Storage|Erro|1|85d077ab-...|Política de repetição não permitiu para uma nova tentativa. Falha com o servidor remoto retornou um erro: proibido (403).

Neste cenário, você deve investigar por que o token SAS está expirando antes do cliente envia o token no servidor:

- Normalmente, você não deve definir uma hora de início quando você cria uma SAS para um cliente usar imediatamente. Se houver diferenças pequeno relógio entre o host gerando as associações de segurança usando a hora atual e o serviço de armazenamento, é possível para o serviço de armazenamento para receber uma associações de segurança que ainda não é válida.
- Você não deve definir um tempo de expiração muito curto em uma SAS. Novamente, as diferenças de pequeno relógio entre o host gerando as associações de segurança e o serviço de armazenamento podem levar a uma SAS está expirando anteriores ao previsto.
- Faz o parâmetro de versão na chave SAS (por exemplo **VA = 2015-04-05**) corresponde à versão da biblioteca de cliente de armazenamento você está usando? Recomendamos que você sempre usar a versão mais recente da [Biblioteca do cliente de armazenamento](https://www.nuget.org/packages/WindowsAzure.Storage/).
- Se você gerar suas chaves de acesso do armazenamento, isso pode invalidar qualquer tokens SAS existentes. Se você gerar tokens SAS com um tempo de expiração longo para aplicativos clientes do cache, isso pode ser um problema.

Se você estiver usando a biblioteca de cliente de armazenamento para gerar tokens SAS, é fácil criar um token válido. No entanto, se você estiver usando a API REST de armazenamento e construindo os tokens SAS manualmente cuidadosamente leia o tópico [Delegando Access com uma assinatura de acesso compartilhado](http://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-404-messages"></a>O cliente está recebendo mensagens de HTTP 404 (não encontrado)
Se o aplicativo cliente recebe uma mensagem de HTTP 404 (não encontrado) do servidor, isso significa que o objeto que o cliente estava tentando usar (como uma entidade, tabela, blob, contêiner ou fila) não existe no serviço de armazenamento. Há vários motivos possíveis para isso, tais como:

- [O cliente ou outro processo excluído anteriormente o objeto]
- [Um problema de autorização de assinatura de acesso compartilhado (SAS)]
- [Código de JavaScript do lado do cliente não tem permissão para acessar o objeto]
- [Falha de rede]

#### <a name="client-previously-deleted-the-object"></a>O cliente ou outro processo excluído anteriormente o objeto
Em cenários em que o cliente está tentando ler, atualizar ou excluir dados em um serviço de armazenamento é geralmente fácil identificar nos logs do lado do servidor uma operação anterior que excluiu o objeto em questão do serviço de armazenamento. Frequentemente, os dados do log mostram que o outro usuário ou processo excluiu o objeto. No log de armazenamento registro em log do lado do servidor, o tipo de operação e solicitada objeto-colunas de chave mostram quando um cliente excluído um objeto.

No cenário em que um cliente está tentando inserir um objeto, talvez não seja imediatamente óbvio por que isso resulta em uma resposta de HTTP 404 (não encontrado), considerando que o cliente está criando um novo objeto. No entanto, se o cliente estiver criando um blob deve ser capaz de localizar o contêiner de blob, se o cliente estiver criando uma mensagem que ele deve ser capaz de localizar uma fila, e se o cliente está adicionando uma linha deve ser capaz de encontrar a tabela.

Você pode usar o log do lado do cliente da biblioteca do cliente de armazenamento para obter uma compreensão mais detalhada da quando o cliente envia solicitações específicas para o serviço de armazenamento.

Log do lado do cliente seguinte gerado pela biblioteca de cliente de armazenamento ilustra o problema quando o cliente não consegue encontrar o contêiner para o blob que está criando. Esse log inclui detalhes das operações de armazenamento a seguir:

ID da solicitação|Operação
---|---
07b26a5d-...|Método de **DeleteIfExists** para excluir o contêiner de blob. Observe que essa operação inclui uma solicitação de **cabeça** para verificar a existência do contêiner.
e2d06d78...|Método de **CreateIfNotExists** para criar o contêiner de blob. Observe que essa operação inclui uma solicitação de **cabeça** que verifica a existência do contêiner. O **chefe** retorna uma mensagem 404 mas continua.
de8b1c3c-...|Método de **UploadFromStream** para criar o blob. A solicitação de **colocar** falha com uma mensagem 404

Entradas de log:

ID da solicitação |  Texto de operação
---|---
07b26a5d-...|Iniciando síncrona solicitação para https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
07b26a5d-...|StringToSign = HEAD...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, de 2014 03 de Jun 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
07b26a5d-...|Aguardando resposta.
07b26a5d-... | Resposta recebida. Código de status = 200, solicitar ID = eeead849-... Conteúdo-MD5 =, ETag = &quot;0x8D14D2DC63D059B&quot;.
07b26a5d-... | Cabeçalhos de resposta processados com êxito, prosseguir com o restante da operação.
07b26a5d-... | Baixando corpo de resposta.
07b26a5d-... | Operação concluída com êxito.
07b26a5d-... | Iniciando síncrona solicitação para https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
07b26a5d-... | StringToSign = DELETE...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, de 2014 03 de Jun 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
07b26a5d-... | Aguardando resposta.
07b26a5d-... | Resposta recebida. Código de status = 202, solicitar ID = 6ab2a4cf-..., Content-MD5 =, ETag =.
07b26a5d-... | Cabeçalhos de resposta processados com êxito, prosseguir com o restante da operação.
07b26a5d-... | Baixando corpo de resposta.
07b26a5d-... | Operação concluída com êxito.
e2d06d78-... | Iniciando solicitação assíncrona para https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td>
e2d06d78-... | StringToSign = HEAD...x-ms-client-request-id:e2d06d78-...x-ms-date:Tue, de 2014 03 de Jun 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
e2d06d78-...| Aguardando resposta.
de8b1c3c-... | Iniciando síncrona solicitação para https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt.
de8b1c3c-... |  StringToSign = colocar... 64.qCmF+TQLPhq/YYK50mP9ZQ==...x-MS-blob-Type:BlockBlob.x-MS-Client-Request-ID:de8b1c3c-...x-MS-Date:TUE, de 2014 03 de Jun 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt.
de8b1c3c-... | Preparando para gravar os dados de solicitação.
e2d06d78-... | Exceção ao aguardando resposta: O servidor remoto retornou um erro: (404) não encontrado..
e2d06d78-... | Resposta recebida. Código de status = 404, solicitar ID = 353ae3bc-..., Content-MD5 =, ETag =.
e2d06d78-... | Cabeçalhos de resposta processados com êxito, prosseguir com o restante da operação.
e2d06d78-... | Baixando corpo de resposta.
e2d06d78-... | Operação concluída com êxito.
e2d06d78-... | Iniciando solicitação assíncrona para https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
e2d06d78-...|StringToSign = colocar... 0...x-MS-Client-Request-ID:e2d06d78-...x-MS-Date:TUE, de 2014 03 de Jun 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
e2d06d78-... | Aguardando resposta.
de8b1c3c-... | Dados de solicitação de gravação.
de8b1c3c-... | Aguardando resposta.
e2d06d78-... | Exceção ao aguardando resposta: O servidor remoto retornou um erro: (409) conflito..
e2d06d78-... | Resposta recebida. Código de status = 409, solicitar ID = c27da20e-..., Content-MD5 =, ETag =.
e2d06d78-... | Baixando corpo de resposta de erro.
de8b1c3c-... | Exceção ao aguardando resposta: O servidor remoto retornou um erro: (404) não encontrado..
de8b1c3c-... | Resposta recebida. Código de status = 404, solicitar ID = 0eaeab3e-..., Content-MD5 =, ETag =.
de8b1c3c-...| Exceção durante a operação: O servidor remoto retornou um erro: (404) não encontrado..
de8b1c3c-... | Política de repetição não permitiu para uma nova tentativa. Falha com o servidor remoto retornou um erro: (404) não encontrado..
e2d06d78-... | Política de repetição não permitiu para uma nova tentativa. Falha com o servidor remoto retornou um erro: (409) conflito..

Neste exemplo, o log mostra que o cliente é intercalação solicitações do método **CreateIfNotExists** (solicitação id e2d06d78 …) com as solicitações do método **UploadFromStream** (de8b1c3c-...); Isso está acontecendo porque o aplicativo cliente é invocar esses métodos assíncrona. Você deve modificar o código assíncrono no cliente para garantir que ele criará o contêiner antes de tentar carregar todos os dados em um blob no contêiner. Ideal, você deve criar todos os seus contêineres com antecedência.

#### <a name="SAS-authorization-issue"></a>Um problema de autorização de assinatura de acesso compartilhado (SAS)

Se o aplicativo cliente tentar usar uma chave SAS que não inclui as permissões necessárias para a operação, o serviço de armazenamento retornará uma mensagem de HTTP 404 (não encontrado) para o cliente. Ao mesmo tempo, você também verá um valor diferente de zero para **SASAuthorizationError** em métricas.

A tabela a seguir mostra uma amostra de mensagem de log do lado do servidor do arquivo de log log de armazenamento:

<table>
  <tr>
    <td>Hora de início de solicitação</td>
    <td>2014-05-30T06:17:48.4473697Z</td>
  </tr>
  <tr>
    <td>Tipo de operação</td>
    <td>GetBlobProperties</td>
  </tr>
  <tr>
    <td>Status da solicitação</td>
    <td>SASAuthorizationError</td>
  </tr>
  <tr>
    <td>Código de status HTTP</td>
    <td>404</td>
  </tr>
  <tr>
    <td>Tipo de autenticação</td>
    <td>SAS</td>
  </tr>
  <tr>
    <td>Tipo de serviço</td>
    <td>Blob</td>
  </tr>
  <tr>
    <td>URL da solicitação</td>
    <td>
    https://domemaildist.blob.Core.Windows.NET/azureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;amp; sr = c&amp;amp; si = mypolicy&amp;amp; sig = XXXXX&amp;amp; versão da api = 2014-02-14&amp;armazenamento.</td>
  </tr>
  <tr>
    <td>Cabeçalho de identificação de solicitação</td>
    <td>a1f348d5-8032-4912-93ef-b393e5252a3b</td>
  </tr>
  <tr>
    <td>ID de solicitação do cliente</td>
    <td>2d064953-8436-4ee0-aa0c-65cb874f7929</td>
  </tr>
</table>

Você deve investigar por que seu aplicativo cliente está tentando realizar uma operação que não foram concedida permissões para.

#### <a name="JavaScript-code-does-not-have-permission"></a>Código de JavaScript do lado do cliente não tem permissão para acessar o objeto

Se você estiver usando um cliente JavaScript e o serviço de armazenamento está retornando mensagens HTTP 404, procure os seguintes erros de JavaScript no navegador:

    SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
    SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.

> [AZURE.NOTE] Você pode usar as ferramentas de desenvolvedor F12 no Internet Explorer para rastrear mensagens trocadas entre o navegador e o serviço de armazenamento para solucionar problemas de JavaScript do lado do cliente.

Esses erros ocorrem porque o navegador da web implementa a restrição de segurança de [mesma diretiva de origem](http://www.w3.org/Security/wiki/Same_Origin_Policy) que impede a chamando uma API em um domínio diferente do domínio que a página proveniente de uma página da web.

Para contornar o problema de JavaScript, você pode configurar Cross Origin recurso Compartilhamento (CORS) para o serviço de armazenamento que está acessando o cliente. Para obter mais informações, consulte [suporte de compartilhamento de recurso com a entre origens (CORS) para serviços de armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).

O exemplo de código a seguir mostra como configurar o serviço de blob para permitir que o JavaScript em execução no domínio Contoso acessar um blob no seu serviço de armazenamento de blob:

    CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
    // Set the service properties.
    ServiceProperties sp = client.GetServiceProperties();
    sp.DefaultServiceVersion = "2013-08-15";
    CorsRule cr = new CorsRule();
    cr.AllowedHeaders.Add("*");
    cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
    cr.AllowedOrigins.Add("http://www.contoso.com");
    cr.ExposedHeaders.Add("x-ms-*");
    cr.MaxAgeInSeconds = 5;
    sp.Cors.CorsRules.Clear();
    sp.Cors.CorsRules.Add(cr);
    client.SetServiceProperties(sp);

#### <a name="network-failure"></a>Falha de rede

Em algumas circunstâncias, os pacotes de rede perdidas podem levar ao serviço de armazenamento retornar mensagens de HTTP 404 para o cliente. Por exemplo, quando seu aplicativo cliente está excluindo uma entidade do serviço de tabela que você ver o cliente lançar uma exceção de armazenamento relatório um "HTTP 404 (não encontrado)" mensagem de status do serviço de tabela. Quando você investigar a tabela no serviço de armazenamento de tabela, você verá que o serviço excluir a entidade como solicitado.

Os detalhes de exceção no cliente incluem id da solicitação (7e84f12d...) atribuído pelo serviço de tabela para a solicitação: você pode usar essas informações para localizar os detalhes da solicitação em logs de armazenamento de servidor pesquisando na coluna **cabeçalho de identificação de solicitação** no arquivo de log. Você também pode usar as métricas para identificar quando falhas como esta ocorrerem e pesquise os arquivos de log com base no período de tempo que as métricas registrado esse erro. Essa entrada de log mostra que a exclusão falhou com uma mensagem de status "Cliente HTTP (404) outro erro". A mesma entrada de log também inclui a identificação de solicitação gerada pelo cliente na coluna **id de solicitação do cliente** (813ea74f...).

Log do lado do servidor também inclui outra entrada com o mesmo valor de **identificação da solicitação de cliente** (813ea74f...) para uma operação de exclusão com êxito para a mesma entidade e do mesmo cliente. Essa operação de exclusão bem-sucedida ocorreu muito logo antes de excluir a falha na solicitação.

A causa mais provável desse cenário é que o cliente enviou uma solicitação de exclusão para a entidade no serviço de tabela, que foi bem-sucedida, mas não recebeu uma confirmação do servidor (talvez devido a um problema de rede temporária). O cliente então automaticamente repetida a operação (usando a mesma **identificação da solicitação de cliente**) e essa repetir falha porque a entidade já foi excluída.

Se esse problema ocorre com frequência, você deve investigar por que o cliente não está conseguindo receber confirmações do serviço da tabela. Se o problema for intermitente, você deve interceptar o erro "HTTP (404) não encontrado" e efetuar no cliente, mas permitir que o cliente continuar.

### <a name="the-client-is-receiving-409-messages"></a>O cliente está recebendo mensagens de HTTP 409 (conflito)

A tabela a seguir mostra um extrato do log do lado do servidor para duas operações de cliente: **DeleteIfExists** seguido imediatamente **CreateIfNotExists** usando o mesmo nome de contêiner de blob. Observe que cada resulta da operação de cliente em duas solicitações enviadas ao servidor, primeiro uma solicitação de **GetContainerProperties** para verificar se existe o contêiner, seguido de solicitação de **DeleteContainer** ou **CreateContainer** .

Carimbo de hora|Operação|Resultado|Nome de contêiner|Id de solicitação do cliente
---|---|---|---|---
05:10:13.7167225|GetContainerProperties|200|mmcont|c9f52c89-...
05:10:13.8167325|DeleteContainer|202|mmcont|c9f52c89-...
05:10:13.8987407|GetContainerProperties|404|mmcont|bc881924-...
05:10:14.2147723|CreateContainer|409|mmcont|bc881924-...

O código no aplicativo cliente exclui e em seguida, recria imediatamente um contêiner de blob usando o mesmo nome: o método de **CreateIfNotExists** (cliente solicitar ID bc881924-…) eventualmente falhar com o erro de HTTP 409 (conflito). Quando um cliente exclui blob contêineres, tabelas ou filas há um breve período antes do nome se torna disponível novamente.

O aplicativo cliente deve usar nomes de contêiner exclusivo sempre que ele cria novos contêineres se o padrão de excluir/recriar for comum.

### <a name="metrics-show-low-percent-success"></a>Métricas mostram PercentSuccess baixa ou entradas de log de análise tem operações com status de transação da ClientOtherErrors

A métrica **PercentSuccess** captura a porcentagem de operações que foram bem-sucedidas com base em seu código de Status de HTTP. Operações com códigos de status de 2XX contam com êxito, enquanto as operações com códigos de status em intervalos 3XX, 4XX e 5XX são contadas como malsucedido e diminuir o valor de métrica **PercentSucess** . Nos arquivos de log de armazenamento do servidor, essas operações são registradas com um status de transação de **ClientOtherErrors**.

É importante observar que essas operações foram concluídas com êxito e, portanto, não afetam outras métricas como disponibilidade. Alguns exemplos de operações que execute com êxito, mas que pode resultar em códigos de status HTTP malsucedidos incluem:
- **ResourceNotFound** (Não encontrado 404), por exemplo, de uma solicitação GET para um blob que não existe.
- **ResouceAlreadyExists** (Conflito 409), por exemplo de uma operação de **CreateIfNotExist** onde o recurso já existe.
- **ConditionNotMet** (Não modificados 304), por exemplo de uma operação de condicional como quando um cliente envia um valor de **ETag** e um cabeçalho HTTP **If-nenhum-correspondência** para solicitar uma imagem somente se ele tiver sido atualizado desde a última operação.

Você pode encontrar uma lista de códigos de erro API REST comuns que os serviços de armazenamento retornam na página de [Códigos de erro de API REST comuns](http://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Métricas de capacidade mostram um aumento inesperado no uso de capacidade de armazenamento


Se você vir súbita, alterações inesperadas no uso de capacidade em sua conta de armazenamento, você pode investigar os motivos examinando primeiro suas métricas de disponibilidade; Por exemplo, um aumento no número de exclusão com falha solicitações podem levar a um aumento na quantidade de armazenamento de blob que você está usando como operações de limpeza específicas do aplicativo que podem ter esperava ser liberar espaço talvez não esteja funcionando conforme esperado (por exemplo, porque os tokens SAS usados para liberar espaço tem expirado).

### <a name="you-are-experiencing-unexpected-reboots"></a>Você está enfrentando reinicialização inesperada do Azure máquinas virtuais têm um grande número de VHDs anexados

Se uma máquina Virtual do Azure (máquina virtual) tem um grande número de VHDs anexados que estão na mesma conta de armazenamento, você poderá exceder os destinos de escalabilidade para uma conta de armazenamento individual causando a máquina virtual falha. Você deve verificar as métricas minutos para a conta de armazenamento (**TotalRequests**/**TotalIngress**/**TotalEgress**) picos que excedeu os destinos de escalabilidade para uma conta de armazenamento. Consulte a seção "[que métricas mostram um aumento no PercentThrottlingError]" para obter assistência determinar se a otimização ocorreu em sua conta de armazenamento.

Em geral, cada entrada individual ou operação de saída em um VHD de uma máquina Virtual traduz para operações de **Página obter** ou **Colocar** em blob de página subjacente. Portanto, você pode usar o IOPS estimado para seu ambiente para ajustar VHDs quantos você pode ter em uma conta de armazenamento única baseada no comportamento específico do seu aplicativo. Não é recomendável ter mais de 40 discos em uma conta de armazenamento único. Consulte [metas de desempenho e escalabilidade de armazenamento do Azure](storage-scalability-targets.md) para obter detalhes sobre os destinos de escalabilidade atual para contas de armazenamento, em particular a taxa de total de solicitação e a largura de banda total para o tipo de conta de armazenamento que você está usando.
Se você estiver ultrapassando os destinos de escalabilidade para sua conta de armazenamento, você deve colocar seu VHDs em várias contas de armazenamento diferentes para reduzir a atividade em cada conta individual.

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>Seu problema surge da usando o emulador de armazenamento para desenvolvimento ou teste

Normalmente você usa o emulador de armazenamento durante o desenvolvimento e teste para evitar a necessidade de uma conta de armazenamento do Azure. Os problemas comuns que podem ocorrer quando você estiver usando o emulador de armazenamento são:

- [Recurso "X" não está funcionando no emulador armazenamento]
- [Erro "o valor de um dos cabeçalhos HTTP não está no formato correto" ao usar o emulador de armazenamento]
- [Executar o emulador de armazenamento requer privilégios administrativos]

#### <a name="feature-X-is-not-working"></a>Recurso "X" não está funcionando no emulador armazenamento

Emulador de armazenamento não oferece suporte a todos os recursos dos serviços de armazenamento do Azure como o serviço de arquivo. Para obter mais informações, consulte [usar o emulador de armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md).

Para os recursos que não oferece suporte para o emulador de armazenamento, use o serviço de armazenamento do Azure na nuvem.

#### <a name="error-HTTP-header-not-correct-format"></a>Erro "o valor de um dos cabeçalhos HTTP não está no formato correto" ao usar o emulador de armazenamento

Você está testando seu aplicativo que usam a biblioteca de cliente de armazenamento contra o emulador de armazenamento local e método chama como **CreateIfNotExists** falhar com a mensagem de erro "o valor de um dos cabeçalhos HTTP não está no formato correto." Isso indica que a versão do emulador de armazenamento que você está usando não suporta a versão da biblioteca de cliente de armazenamento que você está usando. A biblioteca de cliente do armazenamento adiciona o cabeçalho **x-ms-version** todas as solicitações faz. Se o emulador de armazenamento não reconhece o valor no cabeçalho **x-ms-version** , ele rejeita a solicitação.

Você pode usar os logs de armazenamento biblioteca cliente para ver o valor do **cabeçalho x-ms-version** está enviando. Você também pode ver o valor do **cabeçalho x-ms-version** se você usa o Fiddler para rastrear as solicitações de seu aplicativo cliente.

Este cenário normalmente ocorre se você instalar e usar a versão mais recente da biblioteca cliente armazenamento sem atualizar o emulador de armazenamento. Você deve instalar a versão mais recente do emulador de armazenamento, ou use o armazenamento em nuvem em vez de emulador para desenvolvimento e teste.

#### <a name="storage-emulator-requires-administrative-privileges"></a>Executar o emulador de armazenamento requer privilégios administrativos

Você é solicitado credenciais de administrador quando você executa o emulador de armazenamento. Isso ocorre apenas quando você estiver inicializar o emulador de armazenamento pela primeira vez. Após você ter inicializados emulador de armazenamento, você não precisam privilégios administrativos para executá-la novamente.

Para obter mais informações, consulte [usar o emulador de armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md). Observe que você também pode inicializar o emulador de armazenamento no Visual Studio, que também exigem privilégios administrativos.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Você está encontrando problemas para instalar o SDK do Azure para .NET

Quando você tenta instalar o SDK, ele falha tentando instalar o emulador de armazenamento em sua máquina local. O log de instalação contém uma das seguintes mensagens:

- CAQuietExec: Erro: não é possível acessar a instância do SQL
- CAQuietExec: Erro: não é possível criar o banco de dados

A causa é um problema com a instalação existente do LocalDB. Por padrão, o emulador de armazenamento usa LocalDB para manter dados quando ele simula os serviços de armazenamento do Azure. Você pode redefinir sua instância LocalDB executando os seguintes comandos em uma janela do prompt de comando antes de tentar instalar o SDK.

    sqllocaldb stop v11.0
    sqllocaldb delete v11.0
    delete %USERPROFILE%\WAStorageEmulatorDb3*.*
    sqllocaldb create v11.0

O comando **Excluir** remove quaisquer arquivos de banco de dados antigo instalações anteriores do emulador de armazenamento.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Você tem um problema diferente com um serviço de armazenamento

Se as seções de solução anterior não incluir o problema que você está tendo com um serviço de armazenamento, você deve adotar a seguinte abordagem para diagnosticar e solucionar o problema.

- Verifique seu métricas para ver se há alguma alteração de seu comportamento esperado de linha de base. De métricas, você poderá determinar se o problema for temporário ou permanente e quais operações de armazenamento em que o problema está afetando.
- Você pode usar as informações de métricas para ajudá-lo a pesquisar os dados de log do lado do servidor para obter informações mais detalhadas sobre quaisquer erros que estão ocorrendo. Essas informações podem ajudá-lo a solucionar problemas e resolver o problema.
- Se as informações nos logs do lado do servidor não é suficientes para solucionar o problema com êxito, você pode usar os logs do lado do cliente de biblioteca de cliente de armazenamento para investigar o comportamento do seu aplicativo cliente e ferramentas como Fiddler, Wireshark e analisador de mensagem do Microsoft para investigar sua rede.

Para obter mais informações sobre como usar o Fiddler, consulte "[Apêndice 1: usando Fiddler para capturar o tráfego HTTP e HTTPS]."

Para obter mais informações sobre como usar o Wireshark, consulte "[Apêndice 2: usando Wireshark para capturar o tráfego de rede]."

Para obter mais informações sobre como usar o analisador de mensagem do Microsoft, consulte "[Apêndice 3: usando o analisador de Microsoft Message para capturar o tráfego de rede]."

## <a name="appendices"></a>Apêndices

Os apêndices descrevem diversas ferramentas que podem ser úteis quando você estiver diagnosticar e solucionar problemas com o armazenamento do Azure (e outros serviços). Essas ferramentas não fazem parte de armazenamento do Azure e alguns são produtos de terceiros. Assim, as ferramentas abordadas desses apêndices não estão cobertas por qualquer contrato de suporte que você pode ter com o Microsoft Azure ou armazenamento do Azure e, portanto, como parte do processo de avaliação que você deve examinar licenciamento e suporte opções disponíveis dos provedores dessas ferramentas.

### <a name="appendix-1"></a>Apêndice 1: Usando Fiddler para capturar o tráfego HTTP e HTTPS

[Fiddler](http://www.telerik.com/fiddler) é uma ferramenta útil para analisar o tráfego HTTP e HTTPS entre seu aplicativo cliente e o serviço de armazenamento do Azure que você está usando.

> [AZURE.NOTE] Fiddler pode decodificar tráfego HTTPS; Leia a documentação de Fiddler cuidadosamente para compreender como ele faz isso e para compreender as implicações de segurança.

Este apêndice fornece uma breve explicação passo a passo de como configurar Fiddler para capturar o tráfego entre o computador local em que você instalou Fiddler e os serviços de armazenamento do Azure.

Depois que você tenha iniciado Fiddler, ele iniciará capturando tráfego de HTTP e HTTPS em sua máquina local. Estes são alguns comandos úteis para controlar Fiddler:

- Parar e iniciar a captura tráfego. No menu principal, vá para **arquivo** e clique em **Capturar o tráfego** para ativar e desativar a captura.
- Salve dados de tráfego capturado. No menu principal, vá ao **arquivo**, clique em **Salvar**e, em seguida, clique em **Todas as sessões**: Isso permite que você salve o tráfego em um arquivo morto de sessão. Você pode recarregar um arquivo morto de sessão posterior para análise ou enviá-lo a se solicitados suporte da Microsoft.

Para limitar a quantidade de tráfego que captura Fiddler, você pode usar filtros que você configurar na guia **filtros** . A captura de tela a seguir mostra um filtro que captura apenas o tráfego enviado para o ponto de extremidade de armazenamento de **contosoemaildist.table.core.windows.net** :

![][5]

### <a name="appendix-2"></a>Apêndice 2: Usando Wireshark para capturar o tráfego de rede

[Wireshark](http://www.wireshark.org/) é um analisador de protocolo de rede que permite exibir informações detalhadas de pacote para uma ampla variedade de protocolos de rede.

O procedimento a seguir mostra como capturar informações detalhadas de pacote para o tráfego do computador local em que você instalou Wireshark para o serviço de tabela em sua conta de armazenamento do Azure.

1.  Inicie o Wireshark em sua máquina local.
2.  Na seção **Iniciar** , selecione a interface de rede local ou interfaces que estão conectadas à internet.
3.  Clique em **Opções de captura**.
4.  Adicione um filtro para a caixa de texto de **Filtro de captura** . Por exemplo, o **host contosoemaildist.table.core.windows.net** configurará Wireshark para capturar apenas pacotes enviados de ou para o ponto de extremidade do serviço de tabela na conta de armazenamento **contosoemaildist** . Confira a [lista completa de filtros capturar](http://wiki.wireshark.org/CaptureFilters).

    ![][6]

5.  Clique em **Iniciar**. Wireshark agora capturará todos os pacotes enviar de ou para o ponto de extremidade do serviço de tabela que você use seu aplicativo cliente em sua máquina local.
6.  Quando tiver terminado, no menu principal, clique em **capturar** e **Parar**.
7.  Para salvar os dados capturados em um arquivo de captura Wireshark, no menu principal, clique em **arquivo** e em seguida **Salvar**.

WireShark realçará todos os erros que existem na janela **packetlist** . Você também pode usar a janela **Informações de especialista** (clique em **análise**, **Informações de especialista**) para exibir um resumo de erros e avisos.

![][7]

Você também pode optar por exibir os dados TCP como a camada de aplicativo possa vê-lo clicando em dados TCP e selecionando **siga fluxo TCP**. Isso é especialmente útil se você capturados seu despejo sem um filtro de captura. Para obter mais informações, consulte [seguir fluxos de TCP] (http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [AZURE.NOTE] Para obter mais informações sobre como usar o Wireshark, consulte o [Guia do usuário Wireshark](http://www.wireshark.org/docs/wsug_html_chunked).

### <a name="appendix-3"></a>Apêndice 3: Usando o analisador de mensagem do Microsoft para capturar o tráfego de rede

Você pode usar o analisador de mensagem do Microsoft para capturar o tráfego HTTP e HTTPS de maneira similar à Fiddler e capturar o tráfego de rede de maneira similar à Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Configurar uma sessão de rastreamento de web usando o analisador de mensagem do Microsoft

Para configurar uma sessão de rastreamento de web para o tráfego de HTTP e HTTPS usando o analisador de mensagem do Microsoft, execute o aplicativo Analisador de mensagem do Microsoft e, no menu **arquivo** , clique em **Captura/rastreamento**. Na lista de cenários de rastreamento disponíveis, selecione **Web Proxy**. Em seguida, no painel de **Configuração de cenário de rastreamento** , na caixa de texto **HostnameFilter** , adicione os nomes dos seus pontos de extremidade de armazenamento (você pode consultar esses nomes no [Portal do Azure](https://portal.azure.com)). Por exemplo, se o nome da sua conta de armazenamento do Azure for **contosodata**, você deve adicionar o seguinte para a caixa de texto **HostnameFilter** :

    contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net

> [AZURE.NOTE] Um caractere de espaço separa os nomes de host.

Quando você estiver pronto para iniciar a coleta de dados de rastreamento, clique no botão **Iniciar com** .

Para obter mais informações sobre o rastreamento de analisador de mensagem do Microsoft **Proxy da Web** , consulte [Microsoft-PEF-WebProxy provedor](http://technet.microsoft.com/library/jj674814.aspx).

O rastreamento de **Proxy Web** interno no analisador de mensagem do Microsoft se baseia Fiddler. ele pode capturar o tráfego HTTPS do lado do cliente e exibir mensagens HTTPS não criptografadas. O rastreamento de **Proxy Web** funciona Configurando um proxy local para todo tráfego HTTP e HTTPS que fornece acesso às mensagens não criptografadas.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnosticar problemas de rede usando o analisador de mensagem do Microsoft

Além de usar o rastreamento de analisador de mensagem do Microsoft **Web Proxy** para capturar os detalhes do tráfego HTTP/HTTPs entre o aplicativo cliente e o serviço de armazenamento, você também pode usar o rastreamento de **Camada de Link Local** interno para capturar informações de pacote de rede. Isso permite capturar dados semelhantes ao que você pode capturar com Wireshark e diagnosticar problemas de rede como descartados pacotes.

A captura de tela a seguir mostra um exemplo de rastreamento de **Camada de Link Local** com algumas mensagens **informativas** na coluna **DiagnosisTypes** . Clicar em um ícone na coluna **DiagnosisTypes** mostra os detalhes da mensagem. Neste exemplo, o servidor retransmitidos mensagem #305 porque não recebeu uma confirmação do cliente:

![][9]

Quando você cria a sessão de rastreamento no analisador de mensagem do Microsoft, você pode especificar filtros para reduzir a quantidade de ruído no rastreamento. Na página **capturar / rastrear** onde você define o rastreamento, clique no link **Configurar** ao lado de **Microsoft-Windows-NDIS-PacketCapture**. A captura de tela a seguir mostra uma configuração que filtra o tráfego TCP para os endereços IP dos três serviços de armazenamento:

![][10]

Para obter mais informações sobre o rastreamento de camada de Link Local do Microsoft mensagem analisador, consulte [Microsoft-PEF-NDIS-PacketCapture provedor](http://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4"></a>Apêndice 4: Usar o Excel para exibir métricas e dados de log

Muitas ferramentas permitem que você baixe os dados de métricas de armazenamento do armazenamento de tabela Azure em um formato delimitado que torna mais fácil carregar os dados para o Excel para análise e a exibição. Log de armazenamento de dados de armazenamento de blob do Microsoft Azure já está em um formato delimitado que você pode carregar no Excel. No entanto, você precisará adicionar títulos de coluna apropriado com base nas informações no [Formato de Log de análise de armazenamento](http://msdn.microsoft.com/library/azure/hh343259.aspx) e no [Esquema de tabela de métricas de análise de armazenamento](http://msdn.microsoft.com/library/azure/hh343264.aspx).

Para importar seus dados de log de armazenamento para Excel após o download do armazenamento de blob:

- No menu **dados** , clique em **Do texto**.
- Navegue até o arquivo de log que você deseja exibir e clique em **Importar**.
- Na etapa 1 do **Assistente de importação de texto**, selecione **delimitado**.

Na etapa 1 do **Assistente de importação de texto**, selecione **-e-vírgula** como o delimitador somente e escolha aspas duplas como o **qualificador de texto**. Em seguida, clique em **Concluir** e escolha onde colocar os dados na pasta de trabalho.

### <a name="appendix-5"></a>Apêndice 5: Monitoramento com ideias de aplicativo de serviços de equipe do Visual Studio

Você também pode usar o recurso de obtenção de informações do aplicativo para Visual Studio Team Services como parte do seu monitoramento de desempenho e disponibilidade. Esta ferramenta pode:

- Verifique se que seu serviço da web está disponível e ágil. Se seu aplicativo é um site da web ou um aplicativo de dispositivo que usa um serviço da web, ele pode testar sua URL cada poucos minutos de locais no mundo e avise se há um problema.
- Diagnosticar rapidamente qualquer problema de desempenho ou exceções em seu serviço da web. Descubra se CPU ou outros recursos estão sendo alongados, obter rastreamentos de pilha de exceções e pesquise facilmente rastreamentos de log. Se o desempenho do aplicativo cai abaixo dos limites aceitáveis, pode enviaremos um email. Você pode monitorar serviços web do .NET e Java.

Você pode encontrar mais informações ao [o que é o aplicativo ideias?](../application-insights/app-insights-overview.md).

<!--Anchors-->
[Introdução]: #introduction
[Como este guia é organizado]: #how-this-guide-is-organized

[Monitorar seu serviço de armazenamento]: #monitoring-your-storage-service
[Monitoramento de integridade do serviço]: #monitoring-service-health
[Capacidade de monitoramento]: #monitoring-capacity
[Disponibilidade de monitoramento]: #monitoring-availability
[Monitorar o desempenho]: #monitoring-performance

[Diagnosticar problemas de armazenamento]: #diagnosing-storage-issues
[Problemas de integridade de serviço]: #service-health-issues
[Problemas de desempenho]: #performance-issues
[Diagnosticar erros]: #diagnosing-errors
[Problemas de emulador de armazenamento]: #storage-emulator-issues
[Ferramentas de log de armazenamento]: #storage-logging-tools
[Usando ferramentas de log de rede]: #using-network-logging-tools

[Rastreamento de ponta a ponta]: #end-to-end-tracing
[Dados do log de correlação]: #correlating-log-data
[ID de solicitação do cliente]: #client-request-id
[Identificação de solicitação do servidor]: #server-request-id
[Carimbos de hora]: #timestamps

[Guia de solução de problemas]: #troubleshooting-guidance
[Métricas mostram AverageE2ELatency alto e baixo AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Métricas mostram AverageE2ELatency baixa e baixa AverageServerLatency, mas o cliente está enfrentando alta latência]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Métricas mostram AverageServerLatency alto]: #metrics-show-high-AverageServerLatency
[Você está enfrentando atrasos inesperados na entrega de mensagens em uma fila]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Métricas mostram um aumento no PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Aumento temporário de PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Aumento permanente de erro de PercentThrottlingError]: #permanent-increase-in-PercentThrottlingError
[Métricas mostram um aumento no PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Métricas mostram um aumento no PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[O cliente está recebendo mensagens de HTTP 403 (proibido)]: #the-client-is-receiving-403-messages
[O cliente está recebendo mensagens de HTTP 404 (não encontrado)]: #the-client-is-receiving-404-messages
[O cliente ou outro processo excluído anteriormente o objeto]: #client-previously-deleted-the-object
[Um problema de autorização de assinatura de acesso compartilhado (SAS)]: #SAS-authorization-issue
[Código de JavaScript do lado do cliente não tem permissão para acessar o objeto]: #JavaScript-code-does-not-have-permission
[Falha de rede]: #network-failure
[O cliente está recebendo mensagens de HTTP 409 (conflito)]: #the-client-is-receiving-409-messages

[Métricas mostram PercentSuccess baixa ou entradas de log de análise tem operações com status de transação da ClientOtherErrors]: #metrics-show-low-percent-success
[Métricas de capacidade mostram um aumento inesperado no uso de capacidade de armazenamento]: #capacity-metrics-show-an-unexpected-increase
[Você está enfrentando reinicialização inesperada de máquinas virtuais que têm um grande número de VHDs anexados]: #you-are-experiencing-unexpected-reboots
[Seu problema surge da usando o emulador de armazenamento para desenvolvimento ou teste]: #your-issue-arises-from-using-the-storage-emulator
[Recurso "X" não está funcionando no emulador armazenamento]: #feature-X-is-not-working
[Erro "o valor de um dos cabeçalhos HTTP não está no formato correto" ao usar o emulador de armazenamento]: #error-HTTP-header-not-correct-format
[Executar o emulador de armazenamento requer privilégios administrativos]: #storage-emulator-requires-administrative-privileges
[Você está encontrando problemas para instalar o SDK do Azure para .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Você tem um problema diferente com um serviço de armazenamento]: #you-have-a-different-issue-with-a-storage-service

[Apêndices]: #appendices
[Apêndice 1: Usando Fiddler para capturar o tráfego HTTP e HTTPS]: #appendix-1
[Apêndice 2: Usando Wireshark para capturar o tráfego de rede]: #appendix-2
[Apêndice 3: Usando o analisador de mensagem do Microsoft para capturar o tráfego de rede]: #appendix-3
[Apêndice 4: Usar o Excel para exibir métricas e dados de log]: #appendix-4
[Apêndice 5: Monitoramento com ideias de aplicativo de serviços de equipe do Visual Studio]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png
