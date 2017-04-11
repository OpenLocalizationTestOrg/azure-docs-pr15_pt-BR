<properties
    pageTitle="Use os conjuntos de dados de exemplo no Studio de aprendizado de máquina | Microsoft Azure"
    description="Descrições dos conjuntos de dados usados em modelos de exemplo incluídos no Studio ML. Você pode usar esses conjuntos de dados de exemplo para suas experiências."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="garye"/>


# <a name="use-the-sample-data-sets-in-azure-machine-learning-studio"></a>Use os conjuntos de dados de exemplo no Studio de aprendizado de máquina do Azure

[top]: #machine-learning-sample-datasets

Quando você cria um novo espaço de trabalho no aprendizado de máquina do Azure, um número de conjuntos de dados de exemplo e experiências é incluído por padrão. Muitas desses conjuntos de dados de exemplo são usadas pelos modelos de exemplo na [Galeria de inteligência de Cortana do Azure](http://gallery.cortanaintelligence.com/)e outras pessoas estão incluídas como exemplos de vários tipos de dados geralmente usados em aprendizado de máquina.

Alguns desses conjuntos de dados estão disponíveis no armazenamento de Blob do Azure. Para esses conjuntos de dados da tabela abaixo fornece um link direto. Você pode usar esses conjuntos de dados em suas experiências usando os [Dados de importação] [ import-data] módulo.

O restante desses conjuntos de dados de amostra são listados em **Conjuntos de dados salvo** na paleta de módulo à esquerda da tela experimento quando você abrir ou cria uma nova experiência no Studio ML.
Você pode usar qualquer um desses conjuntos de dados em seu próprio experimento arrastando-o à tela experimento.


<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


<table>

<tr>
  <th align=left>Nome do conjunto de dados</th>
  <th align=left>Descrição do conjunto de dados</th>
</tr>

<tr>
  <td valign=top>Conjunto de dados de censo renda binário classificação adultos</td>
  <td valign=top>
Um subconjunto de 1994 censo banco de dados usando o trabalho adultos sobre a idade de 16 com um índice de renda ajustado de > 100.<p> </p><b>Uso:</b> classificar as pessoas que usam demografia prever se uma pessoa ganha mais de 50K ano.<p> </p><b>Pesquisa relacionada:</b> Kohavi, r, Becker, B., (1996). Repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizado de máquina UCI. Irvine, CA: Universidade da Califórnia, escola de informações e ciência </td>
</tr>

<tr ID=airport-codes-dataset>
  <td valign=top>Conjunto de dados de códigos de aeroportos</td>
  <td valign=top>
Códigos de aeroportos do EUA.<p> </p>Este dataset contém uma linha para cada aeroporto EUA, fornecendo o número de identificação do aeroporto e nome junto com a cidade de localização e o estado.
  </td>
</tr>

<tr>
  <td valign=top>Dados de preço automóvel (matéria)</td>
  <td valign=top>
Informações sobre os automóveis por fazer e modelar, incluindo o preço, recursos, como o número de cilindros e MPG, bem como uma pontuação de risco seguros.<p> </p>A pontuação de risco inicialmente associado ao preço automático e, em seguida, ajustado para risco real em um processo conhecido para actuaries como symboling. Um valor de 3 indica que o auto é arriscado e um valor de -3 que ele é provavelmente muito seguro.<p> </p><b>Uso:</b> prever a pontuação de risco por recursos, usando a classificação de regressão ou multivariate. <p> </p><b>Pesquisa relacionada:</b> Schlimmer, J.C. (1987). Repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizado de máquina UCI. Irvine, CA: Universidade da Califórnia, escola de informações e ciência </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top>Conjunto de dados de locação UCI de bicicleta</td>
  <td valign=top>
Dataset de locação de bicicleta UCI com base em dados reais da empresa de Capital Bikeshare que mantém uma rede de locação de bicicleta em Washington DC.<p> </p>O conjunto de dados tem uma linha para cada hora de cada dia em 2011 e 2012, para um total de 17,379 linhas. O intervalo de locações de bicicleta por hora é de 1 a 977.

  </td>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top>Imagem RGB Bill Gates</td>
  <td valign=top>
Arquivo de imagem disponível publicamente convertido em dados CSV.<p> </p>O código para converter a imagem é fornecido na página de detalhes do modelo <strong>quantização usando o K-significa agrupamento de cor</strong> .
  </td>
</tr>

<tr>
  <td valign=top>Dados de doação Sanguínea</td>
  <td valign=top>
Um subconjunto de dados do banco de dados Sanguínea doador da cidade o Centro de Hsin-Chu Sanguínea Transfusion serviço, Taiwan.<p> </p>Dados de doador incluem os meses desde a última doação) e frequência, ou o número total de doações, tempo desde a última doação e quantidade de Sanguínea doada.<p> </p><b>Uso:</b> o objetivo é prever por meio de classificação se o doador doada Sanguínea em março de 2007, onde 1 indica um Doador durante o período de destino e 0 um não doador. <p> </p><b>Pesquisa relacionada:</b> Yeh, I.C., (2008). Repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizado de máquina UCI. Irvine, CA: Universidade da Califórnia, escola de informações e ciência <p> </p>Yeh, eu-Cheng, Yang, rei-Jang e Ting, toque-Ming, "descoberta de dados de Conhecimento no modelo RFM usando a sequência de Bernoulli," sistemas especializados com aplicativos, 2008, <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top>Revisões de livros da Amazon</td>
  <td valign=top>
Revisões de livros na Amazon, tirado de site amazon.com por pesquisadores da Universidade da Pensilvânia (<a href="http://www.cs.jhu.edu/~mdredze/datasets/sentiment/">sentimento</a>). Consulte o artigo de pesquisa, "biografias, Bollywood, caixas de explosão e liquidificadores: adaptação de domínio para classificação sentimento" John Blitzer, Mark Dredze e Fernando Pereira; Associação de computação linguística (ACL), 2007.<p> </p>O conjunto de dados original tem 975K revisões com classificações de 1, 2, 3, 4 ou 5. As análises foram gravadas em inglês e são desde o período de tempo 1997-2007. Este conjunto de dados foi convertidos revisões de 10K.
  </td>
</tr>

<tr>
  <td valign=top>Dados de câncer de mama</td>
  <td valign=top>
Um dos três relacionados câncer conjuntos de dados fornecidos pelo Instituto de oncológicas frequentemente exibido nas especificações de aprendizado de máquina. Combina informações de diagnóstico com recursos de análise de laboratório de 300 amostras de atualização.<p> </p><b>Uso:</b> classificar o tipo de câncer, com base em 9 atributos, algumas das quais estiverem lineares e outras categorias. <p> </p><b>Pesquisa relacionada:</b> O.L. Wohlberg, W.H., rua, W.N. e Mangasarian, (1995). Repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizado de máquina UCI. Irvine, CA: Universidade da Califórnia, escola de informações e ciência </td>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Recursos de câncer de mama <td valign=top>
Dataset contém informações de regiões suspeito, 102K (candidatos) de imagens de raio x, cada descrito pelos 117 recursos. Os recursos são proprietários e seus significados não é exibido pelos criadores de conjunto de dados (Siemens saúde). 
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Informações de câncer de mama</td>
  <td valign=top>
O conjunto de dados contém informações adicionais para cada região suspeito de imagem de raio x. Cada exemplo fornece informações (por exemplo, etiqueta, pacientes ID, coordenadas de patch em relação a imagem inteira) sobre o número de linha correspondente no dataset mama câncer recursos. Cada paciente tem um número de exemplos. Para pacientes que têm um câncer, alguns exemplos são positivos e alguns são negativos. Para pacientes que não têm um câncer, todos os exemplos for negativos. O conjunto de dados tem exemplos de 102K. O conjunto de dados é baseado, 0,6% dos pontos são positivos, o restante for negativo. O conjunto de dados foi disponibilizado por Siemens saúde.
  </td>
</tr>

<tr ID=crm-appetency-labels-shared>
  <td valign=top>Rótulos de Appetency CRM compartilhados</td>
  <td valign=top>
Rótulos do desafio de previsão de relação do cliente KDD xícara 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr ID=crm-churn-labels-shared>
  <td valign=top>Rótulos de variação CRM compartilhados</td>
  <td valign=top>
Rótulos do desafio de previsão de relação do cliente KDD xícara 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td valign=top>Conjunto de dados CRM compartilhado</td>
  <td valign=top>
Estes dados oriundos o desafio de previsão de relação do cliente KDD xícara 2009 (<a href="http://www.sigkdd.org/kdd-cup-2009-customer-relationship-prediction - orange_small_train.data.zip">orange_small_train.data.zip</a>).<p> </p>O dataset contém 50K clientes da empresa francês Telecom laranja. Cada cliente tem 230 recursos anonymized, 190 dos quais são numéricos e 40 são categorias. Os recursos são muito esparsos.
  </td>
</tr>

<tr ID=crm-upselling-labels-shared>
  <td valign=top>Rótulos de vendas extras CRM compartilhados</td>
  <td valign=top>
Rótulos do desafio de previsão de relação do cliente KDD xícara 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td valign=top>Dados de regressão de eficiência de energia</td>
  <td valign=top>
Uma coleção de perfis de energia simulado, com base em 12 formas diferentes de construção. Edifícios diferem em relação às diferenciados por 8 recursos, como glazing área, distribuição de área de glazing e orientação.<p> </p><b>Uso:</b> usar regressão ou classificação para prever a eficiência energética classificação com base como uma das duas respostas real importantes. Para classificação de classe vários, é arredondar a variável de resposta para o inteiro mais próximo. <p> </p><b>Pesquisa relacionada:</b> Xifara, r. & Tsanas, r. (2012). Repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizado de máquina UCI. Irvine, CA: Universidade da Califórnia, escola de informações e ciência </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>Dados de atrasos de voos</td>
  <td valign=top>
Dados de desempenho de pontuais de voos passageiro tirados da coleta de dados de TranStats dos EUA departamento de transporte (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">No prazo</a>).<p> </p>O conjunto de dados abrange o período de tempo de abril outubro de 2013. Antes de carregar ao Azure ML Studio, o conjunto de dados foi processado da seguinte maneira:<ul><li>O conjunto de dados foi filtrado para cobrir apenas 70 mais ocupados aeroportos nos Estados dos EUA</li><li>Voos cancelados foram rotulados como atrasada por mais de 15 minutos</li><li>Voos desviados foram filtrados</li><li>As seguintes colunas foram selecionadas: ano, mês, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, cancelado</li></ul>
</td>
</tr>

<tr>
  <td valign=top>Desempenho em tempo de voo (matéria)</td>
  <td valign=top>
Registros de chegadas de voos de avião e partidas nos Estados Unidos de outubro de 2011.<p> </p><b>Uso:</b> prever atrasos de voos. <p> </p><b>Pesquisa relacionada:</b> do departamento de US de transporte <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>.
  </td>
</tr>

<tr>
  <td valign=top>Floresta aciona dados</td>
  <td valign=top>
Contém dados de clima, como índices de temperatura e umidade e a velocidade do vento, de uma área do nordeste Portugal, combinado com registros da floresta acionado.<p> </p><b>Uso:</b> este é uma tarefa de regressão difícil, onde o objetivo é prever a área gravada da floresta acionado. <p> </p><b>Pesquisa relacionada:</b> Cortez, p, & Morais, r. (2008). Repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizado de máquina UCI. Irvine, CA: Universidade da Califórnia, escola de informações e ciência <p> </p>[Cortez e Morais, 2007] Cortez de p e r. Morais. Uma abordagem de mineração de dados para prever floresta acionado usando dados meteorológicas. Em j Neves, F. M. Santos e j Machado Eds., as novas tendências inteligência Artificial, procedimentos de 2007 a partir de 13 EPIA - português conferência em inteligência Artificial, dezembro, Guimarães, Portugal, páginas 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Disponível em: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top>Conjunto de dados do alemão UCI de cartão de crédito</td>
  <td valign=top>
UCI Statlog (cartão de crédito alemão) dataset (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Dados + de + crédito + alemão-Statlog</a>), usando o arquivo de german.data.<p> </p>O dataset classifica pessoas, descritas por um conjunto de atributos, como os riscos de crédito alta ou baixa. Cada exemplo representa uma pessoa. Há 20 recursos, numéricos e categorias e um rótulo de binário (o valor de risco de crédito). Entradas de risco de crédito alta têm rótulo = 2, entradas de risco de crédito baixa têm rótulo = 1. O custo de classifique um exemplo de baixo risco como alto é 1, enquanto o custo de classifique um exemplo de alto risco como baixa é 5.
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>Títulos de filme IMDB</td>
  <td valign=top>
O conjunto de dados contém informações sobre filmes que foram classificados no Twitter tweets: ID de filme IMDB, nome de filme e gênero, ano de produção. Há 17K filmes no dataset. O conjunto de dados foi introduzido em papel "S. Dooms, T. De Pessemier e Martens l. MovieTweetings: um filme classificação Dataset coletados do Twitter. Workshop em Crowdsourcing e computação humana para sistemas de recomendação, CrowdRec RecSys 2013".
  </td>
</tr>

<tr>
  <td valign=top>Dados de classe Iris dois</td>
  <td valign=top>
Isto é, talvez, o banco de dados melhor conhecido podem ser encontrados nas especificações de reconhecimento do padrão. O conjunto de dados é relativamente pequeno, que contém 50 exemplos cada de medidas pétala de três tipos de iris.<p> </p><b>Uso:</b> prever o tipo de iris das medidas.  <p> </p><b>Pesquisa relacionada:</b> Fisher, R.A. (1988). Repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizado de máquina UCI. Irvine, CA: Universidade da Califórnia, escola de informações e ciência </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>Tweets de filme</td>
  <td valign=top>
O conjunto de dados é uma versão estendida do dataset Tweetings de filme. O dataset tem 170K classificações de filmes, extraídas de tweets bem estruturados no Twitter. Cada instância representa um tweet e é uma tupla: ID de usuário, ID de filme IMDB, classificação, carimbo de hora, número de Favoritos para este tweet e número de retweets deste tweet. O conjunto de dados foi disponibilizado disse r., S. Dooms, B. Loni e Tikk D. para sistemas de recomendação desafio de 2014.
  </td>
</tr>

<tr>
  <td valign=top>Dados MPG para os diversos automóveis</td>
  <td valign=top>
Este conjunto de dados é uma versão ligeiramente modificada do dataset fornecida pela biblioteca StatLib da Carnegie Mellon University. O conjunto de dados foi usado no 1983 American estatísticas associação Exposition.<p> </p>Os dados de listas consumo de combustível para os diversos automóveis em milhas por galão, junto com informações como o número de cilindros, deslocamento de mecanismo, potência, peso total e aceleração.<p> </p><b>Uso:</b> prever economia de combustível com base em atributos de discretos de múltiplos valores 3 e 5 atributos de contínuos. <p> </p><b>Pesquisa relacionada:</b> StatLib, Carnegie Mellon University, (1993). Repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizado de máquina UCI. Irvine, CA: Universidade da Califórnia, escola de informações e ciência </td>
</tr>

<tr>
  <td valign=top>Conjunto de dados de Pima índios Diabetes binário classificação</td>
  <td valign=top>
Um subconjunto de dados do Instituto Nacional de Diabetes e Digestive e piscina infecciosas banco de dados. O conjunto de dados foi filtrado para focalizar feminino pacientes do patrimônio índios Pima. Os dados incluem dados médicos como glicose e níveis de insulin, bem como fatores de vida.<p> </p><b>Uso:</b> prever se o assunto tem diabetes (classificação binária). <p> </p><b>Pesquisa relacionada:</b> Sigillito, V. (1990). Repositório de aprendizado de máquina UCI <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml "</a>. Irvine, CA: Universidade da Califórnia, escola de informações e ciência </td>
</tr>

<tr>
  <td valign=top>Dados de cliente do restaurante</td>
  <td valign=top>
Um conjunto de metadados sobre clientes, incluindo demografia e preferências.<p> </p><b>Uso:</b> usar este conjunto de dados, em combinação com os outros conjuntos de dados de duas restaurante, treinar e testar um sistema de recomendação. <p> </p><b>Pesquisa relacionada:</b> Bache, K. e Lichman, M. (2013). Repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizado de máquina UCI. Irvine, CA: Universidade da Califórnia, escola de informações e ciência.
  </td>
</tr>

<tr>
  <td valign=top>Dados de recurso do restaurante</td>
  <td valign=top>
Um conjunto de metadados sobre restaurantes e seus recursos, como o tipo de alimentação, estilo de jantar e local.<p> </p><b>Uso:</b> usar este conjunto de dados, em combinação com os outros conjuntos de dados de duas restaurante, treinar e testar um sistema de recomendação. <p> </p><b>Pesquisa relacionada:</b> Bache, K. e Lichman, M. (2013). Repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizado de máquina UCI. Irvine, CA: Universidade da Califórnia, escola de informações e ciência.
  </td>
</tr>

<tr>
  <td valign=top>Classificações de restaurante</td>
  <td valign=top>
Contém classificações fornecidas pelos usuários para restaurantes em uma escala de 0 a 2.<p> </p><b>Uso:</b> usar este conjunto de dados, em combinação com os outros conjuntos de dados de duas restaurante, treinar e testar um sistema de recomendação. <p> </p><b>Pesquisa relacionada:</b> Bache, K. e Lichman, M. (2013). Repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizado de máquina UCI. Irvine, CA: Universidade da Califórnia, escola de informações e ciência.
  </td>
</tr>

<tr>
  <td valign=top>Conjunto de dados de classe vários Annealing aço</td>
  <td valign=top>
Este dataset contém uma série de registros de aço recozimento tentativas com os atributos físicos (largura, espessura, tipos de tipo (arame, planilha, etc.) de resultante aço.<p> </p><b>Uso:</b> prever qualquer um dos dois atributos de classe numéricos; dureza ou força. Você também pode analisar correlações entre atributos.<p> </p>Notas aço siga um conjunto padrão, definido pelo SAE e outras organizações. Você está procurando uma determinada 'Notas' (a variável de classe) e entender os valores necessários. <p> </p><b>Pesquisa relacionada:</b> Sterling, D. & Buntine, w. (ND). Repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizado de máquina UCI. Irvine, CA: Universidade da Califórnia, escola de informações e ciência <p> </p>Um guia útil para notas aço pode ser encontrado aqui: <a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td valign=top>Dados de telescope</td>
  <td valign=top>
Registros de energia alta gama partícula picos juntamente com ruído de fundo, ambas simulada usando um processo de Monte Carlo.<p> </p>O objetivo da simulação foi melhorar a precisão do baseado em Terra atmosféricas Cherenkov gama telescopes, usar métodos estatísticos para diferenciar entre o sinal desejado (Pancadas de chuva de radiação Cherenkov) e o ruído de fundo (hadronic Pancadas de chuva iniciadas por raios cósmicos na atmosfera superior).<p> </p>Os dados foram previamente processados para criar um cluster alongado com o long eixo é orientado para o Centro de câmera. As características dessa elipse, (normalmente chamado de parâmetros de Hillas) estão entre os parâmetros de imagem que podem ser usados para discriminação.<p> </p><b>Uso:</b> prever se a imagem de um chá representa ruído sinal ou plano de fundo.<p> </p><b>Observações:</b> precisão de classificação simples não é significativo para esses dados, desde a classificação de um evento de plano de fundo como sinal é pior do que a classificação de um evento de sinal como plano de fundo. Comparação de classificador diferentes gráfico ROC deve ser usado. A probabilidade de aceitar um evento de plano de fundo como sinal deve ser abaixo de um dos seguintes limites: 0,01, 0,02, 0,05, 0,1 ou 0,2.<p> </p>Além disso, observe que o número de eventos de plano de fundo (h, para hadronic Pancadas de chuva) é subestimado, enquanto em medidas reais, a classe h ou ruído representa a maioria dos eventos. <p> </p><b>Pesquisa relacionada:</b> Bock, R.K. (1995). Repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de aprendizado de máquina UCI. Irvine, CA: Universidade da Califórnia, escola de informações </td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>Conjunto de dados do tempo</td>
  <td valign=top>
Observações baseado em Terra clima por hora de NOAA (<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">dados mesclados de 201304 para 201310</a>).<p> </p>Os dados meteorológicos aborda observações feitas a partir de estações de clima aeroporto, abrangendo o período de tempo de abril outubro de 2013. Antes de carregar ao Azure ML Studio, o conjunto de dados foi processado da seguinte maneira:<ul><li>IDs de clima estação foram mapeados para identificações de aeroportos correspondente</li><li>Estações do tempo não associadas 70 aeroportos mais ocupados foram filtradas</li><li>A coluna Data foi dividida em colunas separadas de ano, mês e dia</li><li>As seguintes colunas foram selecionadas: AirportID, ano, mês, dia, hora, fuso horário, SkyCondition, visibilidade, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, altímetro</li></ul>
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top>Wikipedia SP 500 Dataset</td>
  <td valign=top>
Dados são derivados da Wikipédia (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) com base em artigos de cada empresa S & P 500, armazenado como dados XML.<p> </p>Antes de carregar ao Azure ML Studio, o conjunto de dados foi processado da seguinte maneira:<ul><li>Extrair o conteúdo de texto para cada empresa específica</li><li>Remover a formatação do wiki</li><li>Remover caracteres não alfanuméricos</li><li>Converter todo o texto em minúsculas</li><li>Categorias de empresa conhecidos foram adicionadas</li></ul><p> </p>Observe que algumas empresas um artigo não pôde ser encontrado, portanto, o número de registros é menor que 500.
  </td>
</tr>





<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>
O conjunto de dados contém dados do cliente e indicações sobre sua resposta a uma campanha de endereçamento direta. Cada linha representa um cliente. O dataset contém 9 recursos sobre dados demográficos do usuário e passaram comportamento e 3 colunas de rótulo (visitar, conversão e passam).  Visite é uma coluna binária que indica que um cliente visitado após a campanha de marketing, conversão indica um cliente comprou algo e passam é o valor gasto.  O conjunto de dados foi disponibilizado por Kevin Hillstrom para MineThatData email Analytics e mineração desafio de dados.
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>
Recursos de exemplos de teste no RCV1-V2 Reuters notícias dataset. O dataset tem artigos de notícias 781K juntamente com suas identificações (primeira coluna do conjunto de dados). Cada artigo é indexado, stopworded e caule. O conjunto de dados foi disponibilizado por David. D. Lewis.
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top>
Recursos de exemplos de treinamento no RCV1-V2 Reuters notícias dataset. O dataset tem artigos de notícias 23K juntamente com suas identificações (primeira coluna do conjunto de dados). Cada artigo é indexado, stopworded e caule. O conjunto de dados foi disponibilizado por David. D. Lewis.
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
DataSet da descoberta de dados de Conhecimento da Copa 1999 KDD e ferramentas concorrência (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>) de mineração de dados.<p> </p>O dataset baixado e armazenados no armazenamento de Blob do Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) e inclui treinamento e testes de conjuntos de dados. O conjunto de dados de treinamento tem aproximadamente 126K linhas e 43 colunas, incluindo os rótulos; 3 colunas fazem parte das informações de rótulo e 40 colunas, consiste recursos numéricos e de cadeia de caracteres/categórica, estão disponíveis para o modelo de treinamento. Os dados de teste tem aproximadamente 22,5 K testar exemplos com as mesmas 43 colunas como os dados de treinamento.

  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td valign=top>
Atribuições de tópico para artigos de notícias no RCV1-V2 Reuters notícias dataset. Artigo de notícias pode ser atribuído a vários tópicos. O formato de cada linha é "<topic name>  <document id> 1". O dataset contém atribuições de tópico 2.6M. O conjunto de dados foi disponibilizado por David. D. Lewis.
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
Estes dados oriundos o desafio de avaliação de desempenho de KDD xícara 2010 aluno (<a href="http://www.kdd.org/kdd-cup-2010-student-performance-evaluation">avaliação de desempenho de aluno</a>). Os dados usados são o conjunto de treinamento de Algebra_2008_2009 (Stamper, J., Niculescu-Mizil, r., Ritter, s, Gordon, G.J. e Koedinger, K.R. (2010). Álgebra eu 2008-2009. Conjunto de dados de desafio de KDD xícara 2010 educacionais mineração desafio de dados. Encontrá-lo em <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a> ou <a href="http://www.kdd.org/sites/default/files/kddcup/site/2010/files/algebra_2008_2009.zip">algebra_2008_2009.zip</a>.<p> </p>O dataset baixado e armazenados no armazenamento de Blob do Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) e contém arquivos de log de um aluno ensino particular sistema. Os recursos fornecidos incluem a identificação do problema e sua descrição breve, ID do estudante, carimbo de hora e quantas tentativas o aluno feito antes de solucionar o problema da maneira correta. O conjunto de dados original tem registros 8.9M, este conjunto de dados tiver sido convertidos para as primeiras linhas de 100K. O dataset tem 23 separado por tabulação colunas de vários tipos: numéricos, categorias e o carimbo de hora.

  </td>
</tr>




</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
