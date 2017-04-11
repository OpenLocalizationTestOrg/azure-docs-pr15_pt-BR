
<properties
   pageTitle="Execução dos testes de desempenho Elasticsearch automatizados | Microsoft Azure"
   description="Descrição de como você pode executar os testes de desempenho no seu ambiente."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>
   
# <a name="running-the-automated-elasticsearch-performance-tests"></a>Execução dos testes de desempenho Elasticsearch automatizados

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo é [parte de uma série](guidance-elasticsearch.md). 

Os documentos de [ajuste de desempenho de inclusão de dados para Elasticsearch no Azure] e [ajuste de agregação de dados e o desempenho de consulta para Elasticsearch no Azure] descrevem um número de testes de desempenho que foram executados em um cluster de Elasticsearch de amostra.

Esses testes foram script para ativá-los ser executado de uma maneira automatizada. Este documento descreve como você pode repetir os testes no seu ambiente.

## <a name="prerequisites"></a>Pré-requisitos

Os testes automatizados requerem os seguintes itens:

-  Um cluster de Elasticsearch.

- Uma configuração de ambiente JMeter conforme descrito pelo documento [Criando um ambiente de teste de desempenho para Elasticsearch no Azure].

- [Python 3.5.1](https://www.python.org/downloads/release/python-351/) instalado no mestre JMeter máquina virtual.


## <a name="how-the-tests-work"></a>Como funcionam os testes
Os testes são executados usando JMeter. Um servidor mestre JMeter carrega um plano de teste e passa para um conjunto de servidores subordinados JMeter que realmente executar os testes. O servidor mestre JMeter coordenadas os servidores subordinados JMeter e acumula os resultados.

Os planos de teste a seguir são fornecidos:

* [elasticsearchautotestplan3nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan3nodes.jmx). Executa o teste de inclusão sobre um cluster de 3 nós.

* [elasticsearchautotestplan6nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodes.jmx). Executa o teste de inclusão sobre um cluster de nó de 6.

* [elasticsearchautotestplan6qnodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6qnodes.jmx). Executa o teste de inclusão e consulta ao longo de um cluster de nó de 6.

* [elasticsearchautotestplan6nodesqueryonly.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodesqueryonly.jmx). Executa o teste somente consulta sobre um cluster de nó de 6.


Você pode usar esses planos de teste como base para seus próprio cenários se precisar de mais ou menos nós.

Os planos de teste usam uma amostra de solicitação de JUnit para gerar e carregue os dados de teste. O plano de teste JMeter cria e executa este classificador e monitora a cada um de nós Elasticsearch dados de desempenho.  

## <a name="building-and-deploying-the-junit-jar-and-dependencies"></a>Criar e implantar o JUnit JAR e dependências
Antes de executar os testes de desempenho, que você deve baixar, compilar e implantar os testes JUnit localizados na pasta de desempenho/junitcode. Esses testes são referenciados pelo plano de teste JMeter. Para obter mais informações, consulte o procedimento "Importando um projeto de teste de JUnit existente para Eclipse" no documento [Implantando uma amostra de JMeter JUnit para testar o desempenho de Elasticsearch].

Há duas versões dos testes JUnit: 

- [Elasticsearch1.73](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch1.73). Use este código para realizar os testes de inclusão. Esses testes usam 1,73 Elasticsearch.

- [Elasticsearch2](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch2). Use este código para realizar os testes de consulta. Esses testes usam Elasticsearch 2.1 e posterior.

Copie o apropriado Java (arquivo JAR) juntamente com o restante das dependências para suas máquinas JMeter. O processo é descrito na [implantação de uma amostra de JMeter JUnit para testar o desempenho de Elasticsearch][]. 

> **Importante** Depois de implantar um teste JUnit, use JMeter para carregar e configurar os planos de teste que fazem referência a esse teste de JUnit e verifique se o grupo de segmento BulkInsertLarge faz referência a arquivo JAR correto, o nome da classe JUnit e testar método:
> 
> ![](./media/guidance-elasticsearch/performance-tests-image1.png)
> 
> Salve os planos de teste atualizado antes de executar testes.

## <a name="creating-the-test-indexes"></a>Criar os índices de teste
Cada teste executa inclusão e/ou consultas em um único índice especificado quando o teste é executado. Você deve criar o índice usando os esquemas descritos nos Apêndices aos documentos [ajuste de desempenho de inclusão de dados para Elasticsearch no Azure] e [ajuste de agregação de dados e o desempenho de consulta para Elasticsearch no Azure] e configurá-los de acordo com seu cenário de teste (valores de documento ativado/desativado, várias réplicas, etc.) Observe que os planos de teste presumem que o índice contém um único tipo chamado *ctip*.

## <a name="configuring-the-test-script-parameters"></a>Configurar os parâmetros de script de teste
Copie os seguintes arquivos de parâmetro de script de teste de máquina do servidor JMeter:

* [run.properties](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/run.properties). Este arquivo Especifica o número de segmentos de teste JMeter usar, a duração do teste (em segundos), o endereço IP de um nó (ou um balanceador de carga no cluster Elasticsearch,) e o nome do cluster:

  ```ini
  nthreads=3
  duration=300
  elasticip=<IP Address or DNS Name Here>
  clustername=<Cluster Name Here>
  ```
  
  Editar esse arquivo e especifique os valores apropriados para o seu cluster e de teste.

* [consulta-config-Win](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-win.ini) e [consulta-config-nix.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-nix.ini). Esses dois arquivos contêm as mesmas informações; o arquivo *win* está formatado para caminhos e nomes de arquivo do Windows e o arquivo *nix* é formatado para caminhos e nomes de arquivos do Linux:

  ```ini
  [DEFAULT]
  debug=true #if true shows console logs.

  [RUN]
  pathreports=C:\Users\administrator1\jmeter\test-results\ #path where tests results are saved.
  jmx=C:\Users\administrator1\testplan.jmx #path to the JMeter test plan.
  machines=10.0.0.1,10.0.0.2,10.0.0.3 #IPs of the Elasticsearch data nodes separated by commas.
  reports=aggr,err,tps,waitio,cpu,network,disk,response,view #Name of the reports separated by commas.
  tests=idx1,idx2 #Elasticsearch index(es) name(s) to test, comma delimited if more than one.
  properties=run.properties #Name of the properties file.
  ```

  Editar esse arquivo para especificar os locais dos resultados dos testes, o nome do plano de teste de JMeter execute, os endereços IP de nós de dados de Elasticsearch você está coletando métricas de desempenho de, os relatórios que contêm os dados de desempenho bruto que serão gerados, e o nome (ou nomes delimitado por vírgulas) o índice (s) em teste, se mais de um , testes serão executados após o outro. Se o arquivo de run.properties está localizado em uma pasta diferente ou o diretório, especifique o caminho completo para esse arquivo.

## <a name="running-the-tests"></a>Execução dos testes

* Copie o arquivo [test.py de consulta](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-test.py) para máquina do servidor JMeter, na mesma pasta como o run.properties e consulta-config-Win (consulta-config-nix.ini) arquivos.

* Certifique-se de que jmeter.bat (Windows) ou jmeter.sh (Linux) estão no caminho executável para seu ambiente.

* Execute o script test.py de consulta da linha de comando para executar testes:

  ```cmd
  py query-test.py
  ```

* Quando o teste for concluído, os resultados são armazenados como o conjunto de separados por vírgulas valores arquivos (CSV) especificados no arquivo de consulta-config-Win (consulta-config-nix.ini). Você pode usar o Excel para analisar e dados de gráfico.


[Ajuste de desempenho de inclusão de dados para Elasticsearch no Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Ajuste de desempenho de consulta para Elasticsearch no Azure e de agregação de dados]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Criando um ambiente de teste para Elasticsearch no Azure de desempenho]: guidance-elasticsearch-creating-performance-testing-environment.md
[Implantando um classificador JMeter JUnit para testar o desempenho de Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
