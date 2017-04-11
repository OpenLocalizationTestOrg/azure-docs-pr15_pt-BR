<properties
    pageTitle="Analisar desempenho de borda no Azure CDN | Microsoft Azure"
    description="Analise o desempenho de nó de borda do Microsoft Azure CDN. Análise de desempenho de borda fornece uso de largura de banda e o tráfego de informações granular para a CDN."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analisar desempenho de nó de borda do Microsoft Azure CDN

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Visão geral
Análise de desempenho de borda fornece uso de largura de banda e o tráfego de informações granular para a CDN. Essas informações, em seguida, podem ser usadas para gerar estatísticas de tendências, que permitem que você obtenha mais informações sobre como seus ativos estão sendo armazenados em cache e entregue para seus clientes. Por sua vez, isso permite que você formar uma estratégia sobre como otimizar a entrega de seu conteúdo e determinar quais problemas devem ser resolvidos para aproveitar melhor a CDN. Como resultado, não apenas você poderá melhorar o desempenho de entrega de dados, mas você também poderá reduzir os custos CDN.

> [AZURE.NOTE] Todos os relatórios usam a notação de UTC/GMT ao especificar uma data/hora.

## <a name="reports-and-log-collection"></a>Coleta de log e relatórios

Dados de atividade CDN devem ser coletados pelo módulo de análise de desempenho de borda antes que ela pode gerar relatórios sobre ele. Esse processo conjunto ocorre uma vez por dia e ele aborda a atividade que ocorrem durante o dia anterior. Isso significa que as estatísticas de um relatório representam uma amostra de estatísticas do dia no momento ele foi processada e não necessariamente contêm o conjunto completo de dados para o dia atual. A função principal desses relatórios é avaliar o desempenho. Eles não devem ser usados para fins de cobranças ou estatísticas numéricas exatas.

> [AZURE.NOTE] Os dados não processados do qual relatórios analíticos de desempenho de borda são gerados estão disponíveis para pelo menos de 90 dias.

## <a name="dashboard"></a>Painel de controle

Painel de análise de desempenho de borda rastreia atual e histórico tráfego CDN por meio de um gráfico e estatísticas. Use esse painel para detectar tendências de longo prazo e recentes sobre o desempenho de tráfego CDN para sua conta.

Esse painel consiste em:

* Um gráfico interativo que permite a visualização das principais métricas e tendências.
* Uma linha do tempo que fornece uma sensação de padrões de longo prazo de tendências e métricas-chave.
* Principais métricas e informações estatísticas sobre como nossa rede CDN melhora o tráfego de site conforme medido pelo desempenho geral, uso e a eficiência.

### <a name="accessing-the-edge-performance-dashboard"></a>Acessando o painel de desempenho de borda

1. Da lâmina CDN perfil, clique no botão **Gerenciar** .

    ![Botão de gerenciar blade de perfil CDN](./media/cdn-edge-performance/cdn-manage-btn.png)

    O portal de gerenciamento de CDN é aberta.

2. Passe o mouse sobre a guia de **análise** , em seguida, passe o mouse sobre o submenu de **Análise de desempenho de borda** .  Clique em **Painel de controle**.

    Painel de análise de nó de borda é exibido.

### <a name="chart"></a>Gráfico

O painel contém um gráfico que rastreia uma métrica ao longo do período de tempo selecionado na linha do tempo que aparece diretamente abaixo dela.  Uma linha do tempo que gráficos para o último backup dos dois anos de atividade de CDN é exibida diretamente abaixo do gráfico.

#### <a name="using-the-chart"></a>Usando o gráfico

* Por padrão, a taxa de eficiência do cache de últimos 30 dias vai ser plotada.
* Esse gráfico é gerado a partir de dados agrupados diariamente.
* Passando o mouse sobre um dia no gráfico linha indicará a data e o valor da métrica nessa data.
* Clique em realçar fins de semana para alternar uma sobreposição de luz barras verticais cinzas que representam fins de semana para o gráfico. Esse tipo de sobreposição é útil para identificar padrões de tráfego nos finais de semana.
* Clique em Exibir um ano atrás para alternar uma sobreposição de atividade do ano anterior durante o mesmo período de tempo para o gráfico. Este tipo de comparação fornece percepção padrões de uso CDN a longo prazo. Canto superior direito do gráfico contém uma legenda que indica o código de cor de cada gráfico de linha.

#### <a name="updating-the-chart"></a>Atualizar o gráfico

* Intervalo de tempo: Execute um destes procedimentos:
    * Selecione a região desejada na linha do tempo. O gráfico será atualizado com os dados que corresponde ao período de tempo selecionado.
    * Clique duas vezes no gráfico para exibir todos os dados de históricos disponíveis até no máximo dois anos.
* Métrica: Clique no ícone de gráfico que aparece ao lado a métrica desejada. Linha do tempo e o gráfico serão atualizados com dados para a métrica correspondente.


### <a name="key-metrics-and-statistics"></a>Estatísticas e métricas-chave

#### <a name="efficiency-metrics"></a>Métricas de eficiência

A finalidade dessas métricas é ver se a eficiência do cache pode ser melhorada. Os principais benefícios derivados de eficiência do cache são:

* Carga reduzida no servidor de origem que pode levar a:
    * Melhor desempenho do servidor web.
    * Redução dos custos operacionais.
* Aprimorado aceleração de entrega de dados, pois mais solicitações serão atendidas diretamente da CDN.

Campo | Descrição
------|------------
Eficiência do cache | Indica a porcentagem de dados transferidos que foi servidas do cache. Esta métrica medidas quando uma versão em cache do conteúdo solicitado foi fornecida diretamente da CDN (servidores de borda) para solicitantes (por exemplo, o navegador da web)
Taxa de visitas | Indica a porcentagem de solicitações que foram servidas do cache. Esta métrica medidas quando uma versão em cache do conteúdo solicitado foi fornecida diretamente da CDN (servidores de borda) para solicitantes (por exemplo, o navegador da web).
% de Bytes remotos - nenhuma configuração de Cache | Indica a porcentagem de tráfego que foi fornecida de servidores de origem para a CDN (servidores de borda) que serão não ser armazenados em cache como resultado do recurso de Cache de Bypass (mecanismo de regras de HTTP).
% de Bytes remotos - Cache expirada | Indica a porcentagem de tráfego que foi fornecida de servidores de origem para a CDN (servidores de borda) como resultado de revalidação conteúda obsoleta.

#### <a name="usage-metrics"></a>Métricas de uso

A finalidade dessas métricas é fornecer percepção medidas de corte de custos a seguir:

* Minimizar custos operacionais por meio da CDN.
* Reduzindo gastos de CDN por meio de compactação e a eficiência do cache.

> [AZURE.NOTE] Números de volume de tráfego representam tráfego que foi usado em cálculos de razões e porcentagens e pode mostrar apenas uma parte do tráfego total para clientes de grande volume.

Campo | Descrição
------|------------
Média de Bytes Check-Out | Indica o número médio de bytes transferidos para cada solicitação servida da CDN (servidores de borda) ao solicitante (por exemplo, o navegador da web).
Nenhuma taxa de Config bytes de Cache | Indica a porcentagem de tráfego servida da CDN (servidores de borda) ao solicitante (por exemplo, o navegador da web) que serão não ser armazenados em cache devido ao recurso de Cache de ignorar.
Taxa de Byte compactado | Indica a porcentagem de tráfego enviado da CDN (servidores de borda) para solicitantes (por exemplo, o navegador da web) em um formato compactado.
Bytes Check-Out | Indica a quantidade de dados, em bytes, que foram entregues a partir a CDN (servidores de borda) ao solicitante (por exemplo, o navegador da web).  
Bytes em | Indica a quantidade de dados, em bytes, enviado de solicitantes (por exemplo, o navegador da web) para a CDN (servidores de borda).
Bytes remoto | Indica a quantidade de dados, em bytes, enviados de servidores de origem CDN e atendimento ao cliente para a CDN (servidores de borda).

#### <a name="performance-metrics"></a>Métricas de desempenho

A finalidade dessas métricas é acompanhar o desempenho geral de CDN para o tráfego.

Campo | Descrição
------|------------
Taxa de transferência | Indica a taxa média na qual conteúdo foi transferido da CDN para um solicitante.
Duração | Indica o tempo médio, em milissegundos, levou para produzir um ativo para um solicitante (por exemplo, o navegador da web).
Taxa de solicitação compactado | Indica a porcentagem de visitas que foram entregues a partir a CDN (servidores de borda) ao solicitante (por exemplo, o navegador da web) em um formato compactado.
Taxa de erro de 4xx | Indica a porcentagem de ocorrências geradas por um código de status 4xx.
Taxa de erro 5xx | Indica a porcentagem de ocorrências geradas por um código de status 5xx.
Visitas | Indica o número de solicitações de CDN conteúdo.

#### <a name="secure-traffic-metrics"></a>Proteger o tráfego métricas

A finalidade dessas métricas é controlar o desempenho de CDN tráfego HTTPS.

Campo | Descrição
------|------------
Proteger Cache eficiência | Indica a porcentagem de dados transferidos para solicitações HTTPS que foram atendidas do cache. Esta métrica medidas quando uma versão em cache do conteúdo solicitado foi fornecida diretamente da CDN (servidores de borda) para solicitantes (por exemplo, o navegador da web) em HTTPS.
Taxa de transferência segura | Indica a taxa média na qual conteúdo foi transferido da CDN (servidores de borda) para solicitantes (por exemplo, servidores web) por HTTPS.
Duração média de segura | Indica o tempo médio, em milissegundos, levou para produzir um ativo para um solicitante (por exemplo, o navegador da web) em HTTPS.
Ocorrências de seguras | Indica o número de solicitações HTTPS para CDN conteúdo.
Proteger Bytes Check-Out | Indica a quantidade de tráfego HTTPS, em bytes, que foram entregues a partir a CDN (servidores de borda) ao solicitante (por exemplo, o navegador da web).

## <a name="reports"></a>Relatórios

Cada relatório neste módulo contém um gráfico e as estatísticas de uso de largura de banda e o tráfego de diferentes tipos de métricas (por exemplo, códigos de status HTTP, códigos de status do cache, solicitação URL, etc.). Essas informações podem ser usadas para nos aprofundar como o conteúdo está sendo atendido aos seus clientes e para ajustar o comportamento CDN para melhorar o desempenho de entrega de dados.

### <a name="accessing-the-edge-performance-reports"></a>Acessar os relatórios de desempenho de borda

1. Da lâmina CDN perfil, clique no botão **Gerenciar** .

    ![Botão de gerenciar blade de perfil CDN](./media/cdn-edge-performance/cdn-manage-btn.png)

    O portal de gerenciamento de CDN é aberta.

2. Passe o mouse sobre a guia de **análise** , em seguida, passe o mouse sobre o submenu de **Análise de desempenho de borda** .  Clique no **objeto grande HTTP**.

    A tela de relatórios de análise de nó de borda é exibida.

Relatório | Descrição
-------|------------
Resumo diário | Permite exibir as tendências de tráfego diária durante um período especificado. Cada barra neste gráfico representa uma data específica. O tamanho da barra indica o número total de visitas que ocorreram na data.
Resumo por hora | Permite a exibição por hora tendências de tráfego durante um período de tempo especificado. Cada barra neste gráfico representa uma única hora em uma determinada data. O tamanho da barra indica o número total de visitas ocorridas durante tal hora.
Protocolos | Exibe o detalhamento de tráfego entre os protocolos HTTP e HTTPS. Um gráfico de rosca indica a porcentagem de visitas que ocorreram para cada tipo de protocolo.
Métodos HTTP | Permite que você obtenha uma rápida noção do qual HTTP métodos estão sendo usados para solicitar seus dados. Normalmente, os métodos de solicitação HTTP mais comuns são GET, cabeça e POSTAGEM. Um gráfico de rosca indica a porcentagem de visitas que ocorreram para cada tipo de método de solicitação HTTP.
URLs | Contém um gráfico que mostra os 10 principais URLs de solicitada. Uma barra será exibida para cada URL. A altura da barra indica quantas ocorrências geradas pelo URL específica ao longo do período coberto pelo relatório. Estatísticas para as 100 primeiras solicitada que URLs são exibidos diretamente abaixo este gráfico.
CNAMEs | Contém um gráfico que mostra a parte superior 10 CNAMEs usados para solicitar ativos ao longo do tempo do período de um relatório. Estatísticas para as 100 primeiras solicitada que CNAMEs são exibidos diretamente abaixo este gráfico.
Origens | Contém um gráfico que mostra a CDN 10 principais ou servidores de origem do cliente do qual ativos foram solicitados durante um período de tempo especificado. Estatísticas para as 100 primeiras solicitada CDN ou cliente servidores de origem são exibidos diretamente abaixo este gráfico. Servidores de origem do cliente são identificados pelo nome definido na opção nome do diretório.
Localização geográfica aparece | Mostra quanto do seu tráfego está sendo roteado por meio de uma determinado ponto-de-presença (POP). A abreviação de três letras representa um POP no nosso rede CDN.
Clientes | Contém um gráfico que exibe os principais 10 clientes que solicitado ativos durante um período de tempo especificado. Para os fins deste relatório, todas as solicitações originárias o mesmo endereço IP são consideradas do mesmo cliente. Estatísticas de 100 clientes do superiores são exibidas diretamente abaixo este gráfico. Esse relatório é útil para determinar os padrões de atividade de download para os clientes superiores.
Status de cache | Fornece uma divisão detalhada de comportamento de cache, que pode revelar abordagens para melhorar a experiência geral do usuário final. Como o desempenho mais rápido for proveniente de ocorrências do cache, você pode otimizar velocidades de entrega de dados por minimizar erros de cache e ocorrências de cache expirada.
Nenhum detalhes | Contém um gráfico que mostra os 10 principais URLs para ativos para o qual a atualização de conteúdo de cache não foi verificada durante um período de tempo especificado. Estatísticas para as URLs de 100 superiores para esses tipos de ativos são exibidas diretamente abaixo este gráfico.
Detalhes CONFIG_NOCACHE | Contém um gráfico que mostra as URLs de 10 principais para ativos que não foram armazenados em cache devido a configuração de CDN do cliente. Esses tipos de recursos foram served diretamente do servidor de origem. Estatísticas para as URLs de 100 superiores para esses tipos de ativos são exibidas diretamente abaixo este gráfico.
Detalhes UNCACHEABLE | Contém um gráfico que mostra os 10 principais URLs para ativos que podem não ser armazenados em cache devido a dados de cabeçalho de solicitação. Estatísticas para as URLs de 100 superiores para esses tipos de ativos são exibidas diretamente abaixo este gráfico.
Detalhes TCP_HIT | Contém um gráfico que mostra as URLs de 10 principais para ativos que são atendidas imediatamente do cache. Estatísticas para as URLs de 100 superiores para esses tipos de ativos são exibidas diretamente abaixo este gráfico.
Detalhes TCP_MISS | Contém um gráfico que mostra os 10 principais URLs para ativos que tenham um status de cache de TCP_MISS. Estatísticas para as URLs de 100 superiores para esses tipos de ativos são exibidas diretamente abaixo este gráfico.
Detalhes TCP_EXPIRED_HIT | Contém um gráfico que mostra as 10 URLs superiores para obsoletos ativos que foram fornecidos diretamente da POP. Estatísticas para as URLs de 100 superiores para esses tipos de ativos são exibidas diretamente abaixo este gráfico.
Detalhes TCP_EXPIRED_MISS | Contém um gráfico que mostra as 10 principais URLs obsoletos ativos para o qual uma nova versão tinha a serem recuperados do servidor de origem. Estatísticas para as URLs de 100 superiores para esses tipos de ativos são exibidas diretamente abaixo este gráfico.
Detalhes TCP_CLIENT_REFRESH_MISS | Contém um gráfico de barras que exibe as URLs de 10 principais para ativos foram recuperados de um servidor de origem devido a uma solicitação de sem cache do cliente. Estatísticas para as URLs de 100 superiores para esses tipos de solicitações são exibidas diretamente abaixo esse gráfico.
Tipos de solicitação de cliente | Indica o tipo de solicitações que foram feitas por clientes HTTP (por exemplo, navegadores). Este relatório inclui um gráfico de rosca que fornece um sentido como para como solicitações estão sendo tratadas. Informações de largura de banda e o tráfego para cada tipo de solicitação são exibidas abaixo do gráfico.
Agente de usuário | Contém um gráfico de barras exibindo os agentes de 10 usuário superior para solicitar seu conteúdo por meio de nosso CDN. Normalmente, um agente de usuário é um navegador da web, media player ou um navegador do telefone celular. Estatísticas para os principais agentes de 100 usuário são exibidas diretamente abaixo esse gráfico.
Referenciadores | Contém um gráfico de barras exibindo os 10 principais referenciadores ao conteúdo acessado por meio de nosso CDN. Normalmente, uma referência é a URL da página da web ou recurso que vincule a seu conteúdo. Informações detalhadas são fornecidas abaixo do gráfico para os Referenciadores 100 primeiras.
Tipos de compactação | Contém um gráfico de rosca que divide ativos solicitados por se eles tenham sido compactados por nossos servidores de borda. A porcentagem de ativos compactados é dividida por tipo de compactação usada. Informações detalhadas são fornecidas abaixo do gráfico para cada tipo de compactação e o status.
Tipos de arquivo | Contém um gráfico de barras que exibe os tipos de 10 arquivo principais que foram solicitados por meio de nosso CDN para sua conta. Para os fins deste relatório, um tipo de arquivo é definido pela extensão de nome de arquivo do ativo e tipo de mídia de Internet (por exemplo,. HTML \[texto/html\],. htm \[texto/html\],. aspx \[texto/html\], etc.). Informações detalhadas são fornecidas abaixo do gráfico para os tipos de 100 arquivo superior.
Arquivos exclusivos | Contém um gráfico que plota o número total de ativos exclusivos que foram solicitados em um determinado dia durante um período de tempo especificado.
Resumo de autenticação de token | Contém um gráfico de pizza que fornece uma rápida visão geral sobre se solicitada ativos foram protegidos por autenticação baseada em Token. Ativos protegidos são exibidos no gráfico de acordo com os resultados da sua autenticação tentativa.
Autenticação de token negar detalhes | Contém um gráfico de barras que permite exibir as solicitações de 10 principais que foram negadas devido a autenticação baseada em Token.
Códigos de resposta HTTP | Fornece uma divisão dos códigos de status de HTTP (por exemplo, 200 Okey, 403 Proibido, 404 não encontrado, etc.) que foram entregues aos clientes HTTP por nossos servidores de borda. Um gráfico de pizza permite avaliar rapidamente como seus ativos estavam atendidos. Dados estatísticos detalhados são fornecidos para cada código de resposta abaixo do gráfico.
404 erros | Contém um gráfico de barras que permite exibir as solicitações de 10 principais que resultou em um código de resposta 404 não encontrado.
403 erros | Contém um gráfico de barras que permite exibir as solicitações de 10 principais que resultou em um código de resposta 403 Proibido. Um código de resposta proibido 403 ocorre quando uma solicitação é negada por um servidor de origem do cliente ou um servidor de borda na nossa POP.
4xx erros | Contém um gráfico de barras que permite exibir as solicitações de 10 principais que resultou em um código de resposta no intervalo 400. Excluídos desse relatório são 403 404 códigos de resposta proibido e não foi encontrado. Normalmente, um código de resposta 4xx ocorre quando uma solicitação é negada como resultado de um erro de cliente.
504 erros | Contém um gráfico de barras que permite exibir as solicitações de 10 principais que resultou em um código de resposta 504 do tempo limite do Gateway. Um código de resposta de tempo limite do Gateway 504 ocorre quando um tempo limite quando um proxy HTTP está tentando se comunicar com outro servidor. No caso de nosso CDN, um código de resposta de tempo limite do Gateway 504 normalmente ocorre quando um servidor de borda é capaz de estabelecer a comunicação com um servidor de origem do cliente.
502 erros | Contém um gráfico de barras que permite exibir as solicitações de 10 principais que resultou em um código de resposta 502 Gateway incorreto. Um código de resposta 502 Gateway incorreto ocorre quando uma falha de protocolo HTTP entre um servidor e um proxy HTTP. No caso de nosso CDN, um código de resposta 502 Gateway incorreto normalmente ocorre quando um servidor de origem do cliente retorna uma resposta inválida para um servidor de borda. Uma resposta é inválida, se ele não pode ser analisado ou se ela estiver incompleta.
Erros de 5xx | Contém um gráfico de barras que permite exibir as solicitações de 10 principais que resultou em um código de resposta no intervalo de 500.  Excluídos desse relatório são 502 Gateway incorreto e 504 códigos de resposta de tempo limite do Gateway.

## <a name="see-also"></a>Consulte também
* [Visão geral do Azure CDN](cdn-overview.md)
* [Estatísticas em tempo real no Microsoft Azure CDN](cdn-real-time-stats.md)
* [Substituindo o comportamento HTTP padrão usando o mecanismo de regras](cdn-rules-engine.md)
* [Relatórios HTTP avançadas](cdn-advanced-http-reports.md)
