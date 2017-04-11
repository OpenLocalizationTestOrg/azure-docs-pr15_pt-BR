<properties
    pageTitle="Perguntas Frequentes de aprendizado de máquina Azure | Microsoft Azure"
    description="Introdução de aprendizado de máquina Azure: perguntas Frequentes abrangendo cobrança, recursos e limitações de um serviço de nuvem para modelagem de previsão simplificada."
    keywords="aprendizado Introdução, modelagem de previsão, o que é máquina aprendizado de máquina"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/26/2016"
    ms.author="garye"/>

# <a name="azure-machine-learning-frequently-asked-questions-faq-billing-capabilities-limitations-and-support"></a>Azure aprendizado de máquina perguntas frequentes (FAQ): cobrança, recursos, limitações e suporte

Estas perguntas Frequentes respondem dúvidas sobre aprendizado de máquina do Azure, um serviço de nuvem para desenvolver modelos de previsão e operacionalização soluções por meio de serviços da web. Essas perguntas Frequentes abordam dúvidas sobre como usar o serviço, incluindo o modelo de cobrança, recursos, limitações e suporte.

## <a name="general-questions"></a>Perguntas gerais

**O que é Azure aprendizado de máquina?**

Azure aprendizado de máquina é um serviço totalmente gerenciado que você pode usar para criar, testar, operar e gerenciar previsão soluções analíticas na nuvem. Com apenas um navegador, você pode entrar, carregar dados e iniciar imediatamente experiências de aprendizado de máquina. Modelagem de previsão de arrastar e soltar, uma paleta grande de módulos e uma biblioteca da inicialização tarefas de aprendizado de máquina comuns do modelos torna simples e rápido.  Para obter mais informações, consulte [Visão geral do serviço de aprendizado de máquina do Azure](https://azure.microsoft.com/services/machine-learning/). Para obter uma introdução de aprendizado de máquina que aborda conceitos e terminologia principal, consulte [Introdução ao Azure aprendizado de máquina](machine-learning-what-is-machine-learning.md).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**O que é Studio de aprendizado de máquina?**

Studio de aprendizado de máquina é um ambiente de ambiente de trabalho que você acessa por meio de um navegador da web. Studio de aprendizado de máquina hospeda uma paleta de módulos com uma interface de composição visual que permite que você crie uma ponta a ponta, o fluxo de trabalho de ciência de dados na forma de um experimento.

Para obter mais informações sobre Studio de aprendizado de máquina, consulte [Novidades Studio de aprendizado de máquina?](machine-learning-what-is-ml-studio.md)

**O que é o serviço de API de aprendizado de máquina?**

O serviço de API de aprendizado de máquina permite implantar modelos de previsão, como aqueles criados no Studio de aprendizado de máquina, como scalable, tolerância, serviços da web. Os serviços da web criados pelo serviço de API de aprendizado de máquina são APIs REST que fornecem uma interface para comunicação entre aplicativos externos e seus modelos de análise de previsão.

Para obter mais informações, consulte [conectar a um serviço da web de aprendizado de máquina](machine-learning-connect-to-azure-machine-learning-web-service.md).

**Onde meus serviços web clássico estão listados? Onde meu novo serviços web do Gerenciador de recursos do Azure com base estão listados?**

Serviços Web clássico e o novo Gerenciador de recursos do Azure com base Web serviços estão listados no portal de [Serviços da Web de aprendizado de máquina do Microsoft Azure](https://services.azureml.net/) . 

Serviços Web do clássicos também estão listados no [Studio de aprendizado de máquina](http://studio.azureml.net) na guia de serviços da Web.

## <a name="microsoft-azure-machine-learning-web-service-questions"></a>Perguntas de serviço Web de aprendizado de máquina do Microsoft Azure

**Quais são os serviços de Web de aprendizado de máquina Azure?**

Serviços de Web de aprendizado de máquina fornecem uma interface entre um aplicativo e um modelo de pontuação de fluxo de trabalho de aprendizado de máquina. Usando o serviço da web de aprendizado de máquina do Azure, um aplicativo externo possa se comunicar com um modelo de pontuação de fluxo de trabalho de aprendizado de máquina em tempo real. Uma chamada de serviço da web de aprendizado de máquina retorna resultados de previsão para um aplicativo externo. Para fazer com que um serviço da web de aprendizado de máquina chamada, você passar uma chave de API que foi criada quando você implantou o serviço da web. O serviço da web de aprendizado de máquina baseia-se no restante, uma opção de arquitetura populares para projetos de programação da web.

Azure aprendizado de máquina tem dois tipos de serviços:

* Serviço de solicitação-resposta (RR) - uma baixa latência, altamente escalável serviço que fornece uma interface para os modelos de estado criado e implantado a partir do Studio de aprendizado de máquina.
* Serviço de execução de lote (BES) - um assíncrono que pontuações um lote para registros de dados de serviço.

Há várias maneiras para consumir a API REST e acessar o serviço web. Por exemplo, você pode escrever um aplicativo em c#, R ou Python usando o código de exemplo gerado para você quando você implantou o serviço da web.

O código de amostra está disponível em: consumir a página para o serviço da Web no portal do Azure serviços de Web de aprendizado de máquina.
A API página de Ajuda no painel de serviço Web no Studio de aprendizado de máquina.

Ou você pode usar a pasta de trabalho do Microsoft Excel de exemplo criada para você (também está disponível no painel de serviço web no Studio).

**Quais são as atualizações principais com os novos serviços da Web de ML Azure?**

Para saber mais sobre os novos serviços de Web de aprendizado de máquina Azure, consulte a [documentação relacionada](machine-learning-whats-new.md).

## <a name="machine-learning-studio-questions"></a>Perguntas de Studio de aprendizado de máquina

### <a name="importing-and-exporting-data-for-machine-learning"></a>Importar e exportar dados para aprendizado de máquina

**Aprendizado de máquina oferece suporte a quais fontes de dados?**

Dados podem ser carregados em um experimento Studio de aprendizado de máquina em uma das três maneiras: carregar um arquivo local como um conjunto de dados, usando um módulo para importar dados de serviços de nuvem de dados ou importando um conjunto de dados salvo de outro experimentar. Para saber mais sobre os formatos de arquivo com suporte, consulte [Importar dados de treinamento para Studio de aprendizado de máquina](machine-learning-data-science-import-data.md).


#### <a id="ModuleLimit"></a>Como grande conjunto de dados possível para meu módulos?

Módulos no Studio de aprendizado de máquina oferecem suporte a conjuntos de dados de até 10 GB de dados numéricos condensado denso para casos comuns de uso. Se um módulo levar mais de uma entrada, 10 GB é o total de todos os tamanhos de entrada. Você também pode obter amostra maiores conjuntos de dados por meio de seção ou Azure SQL Database consultas ou aprendendo por contagens pré-processamento, antes de inclusão.  

Os seguintes tipos de dados podem expandir em conjuntos de dados maiores durante normalização de recurso e limitam-se a menos de 10 GB:

- Esparso
- Categorias
- Cadeias de caracteres
- Dados binários

Os seguintes módulos limitam-se com conjuntos de dados menor que 10GB:

- Módulos de recomendação
- Módulo SMOTE
- Script módulos: R, Python, SQL
- Módulos onde o tamanho de dados de saída pode ser maior que tamanho de dados de entrada, como ingressar ou recurso hash.
- Validação cruzada, ajustar o modelo Hyperparameters, Ordinal regressão e uni-vs-All Multiclass, quando o número de iterações é muito grande.

Para conjuntos de dados maiores que alguns GB, você deve carregar dados armazenamento do Azure ou Azure SQL Database ou use HDInsight, em vez de diretamente ao carregar do arquivo local.


####<a id="UploadLimit"></a>Quais são os limites para carregamento de dados?
Para conjuntos de dados maiores que algumas GB carregar dados armazenamento do Azure ou Azure SQL Database ou use HDInsight, em vez de diretamente ao carregar do arquivo local.

**Pode ler dados de Amazon S3?**

Se você tiver uma pequena quantidade de dados e desejar para expô-lo por meio de uma URL http, você pode usar os [Dados de importação] [ import-data] módulo. Para qualquer quantidades maiores de dados para transferi-la ao armazenamento do Azure primeiro e depois use os [Dados de importação] [ import-data] módulo para trazê-lo para o seu experimento.
<!--
<SEE CLOUD DS PROCESS>
-->

**Existe um recurso de entrada de imagem interna?**

Você pode aprender sobre recursos de entrada de imagem em [Importar imagens] [ image-reader] referência.

### <a name="modules"></a>Módulos

**O algoritmo, fonte de dados, formato de dados ou operação de transformação de dados que estou procurando não está no Azure Studio de aprendizado de máquina. Quais são minhas opções?**

Você pode visitar o [Fórum de comentários do usuário](http://go.microsoft.com/fwlink/?LinkId=404231) para ver solicitações de recursos que vamos controlar. Adicione seu voto a uma solicitação, se um recurso que você está procurando já foi solicitado. Se o recurso que você está procurando não existir, crie uma nova solicitação. Você pode exibir o status da sua solicitação neste fórum muito. Estamos acompanhar esta lista de perto e atualizar o status de disponibilidade de recursos com frequência. Além disso, com o suporte interno para R e Python transformações personalizadas podem ser criadas conforme necessário.


**Posso importar meu código existente para Studio de aprendizado de máquina?**

Sim, você pode trazer seu código R ou Python existente para o Studio de aprendizado de máquina, executá-lo no mesmo experimento com alunos de aprendizado de máquina do Azure e implantar a solução como um serviço web por meio de aprendizado de máquina do Azure. Para obter mais informações, consulte [estender seu experimento com R](machine-learning-extend-your-experiment-with-r.md) e [scripts no Studio de aprendizado de máquina de Azure de aprendizado de máquina de Python executar](machine-learning-execute-python-scripts.md).

**É possível usar algo parecido com [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) para definir um modelo?**

Não, que não é suportado, porém código personalizado de R e Python pode ser usado para definir um módulo.

**Quantos módulos eu podem executar em paralelo em meu experimento?**  

Você pode executar até quatro módulos em paralelo em um experimento.


### <a name="data-processing"></a>Processamento de dados

**Existe uma capacidade de visualizar dados (além visualizações de R) interativamente dentro do experimento?**

Clicando na saída de um módulo, você pode visualizar os dados e obter estatísticas.

**Ao visualizar resultados ou dados no navegador, o número de linhas e colunas está limitado, por que?**

Como os dados está sendo transmitidos para o navegador e podem ser grandes, o tamanho dos dados está limitado aos impedir desacelerando Studio de aprendizado de máquina. Para visualizar todos os dados/resultado, é melhor baixar os dados e usar o Excel ou outra ferramenta.

### <a name="algorithms"></a>Algoritmos

**Quais algoritmos existentes são compatíveis com Studio de aprendizado de máquina?**

Studio de aprendizado de máquina fornece algoritmos de estado-de-arte, como árvores de decisão Scalable aumentou, sistemas de recomendação Bayesiana, redes neurais profunda, e Jungles de decisão desenvolvidos Microsoft Research. Pacotes de aprendizado de máquina de código-fonte aberto Scalable como Vowpal Wabbit também estão incluídos. Studio de aprendizado de máquina suporta algoritmos de aprendizado de máquina para classificação multiclass e binária, regressão e cluster. Consulte a lista completa de [Módulos de aprendizado de máquina][machine-learning-modules].

**Você sugere automaticamente à direita algoritmo de aprendizado de máquina para usar em Meus dados?**

Não, porém, existem várias maneiras no Studio de aprendizado de máquina para comparar os resultados de cada algoritmo para determinar o um certo para o seu problema.

**Você tem qualquer diretrizes sobre um algoritmo de separação outro para os algoritmos fornecidos?**
Veja [como escolher um algoritmo](machine-learning-algorithm-choice.md).

**Os algoritmos fornecidos escritos em R ou Python?**

Não, esses algoritmos principalmente escritos em idiomas compilados para fornecer o melhor desempenho.

**São quaisquer detalhes dos algoritmos fornecidos?**

A documentação fornece algumas informações sobre os algoritmos e os parâmetros fornecidos para ajuste descritos para otimizar o algoritmo para seu uso.  

**Existe nenhum suporte para aprendizado online?**

Não, atualmente só programação treinamento é suportado.

**Para visualizar as camadas de um modelo de rede Neural usando o módulo interno?**

Não.

**Pode criar meus próprios módulos em c# ou outro idioma?**

Atualmente novos módulos personalizados só podem ser criados em R.

### <a name="r-module"></a>Módulo de R

**Quais pacotes de R estão disponíveis no Studio de aprendizado de máquina?**

Studio de aprendizado de máquina dá suporte a pacotes de CRAN R 400 + hoje e aqui está a [lista atual](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) de pacotes todos incluídos. Consulte também [estender seu experimento com R](machine-learning-extend-your-experiment-with-r.md) para saber como recuperar essa lista por conta própria. Se o pacote desejado não estiver nesta lista, forneça o nome do pacote no [Fórum de comentários do usuário](http://go.microsoft.com/fwlink/?LinkId=404231).

**É possível criar um módulo de R personalizado?**

Sim, consulte [módulos personalizados de R autor no aprendizado de máquina do Azure](machine-learning-custom-r-modules.md) para obter mais informações.

**Existe um ambiente REPL por R?**

Não, não há nenhum ambiente de REPL por R no studio.

### <a name="python-module"></a>Módulo de Python

**É possível criar um módulo de Python personalizado?**

Não atualmente, mas você pode usar um ou mais [Executar scripts de Python] [ python] módulos para obter o mesmo resultado.

**Há um ambiente REPL para Python?**

Você pode usar os blocos de anotações de Jupyter no Studio de aprendizado de máquina. Para obter mais informações, consulte [apresentando Jupyter blocos de anotações no Azure Studio de aprendizado de máquina] (http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## <a name="web-service"></a>Serviço da Web

###<a name="retraining-models-programmatically"></a>Treinamento modelos programaticamente

**Como faço modelos de aprendizado de máquina Azure treinar novamente programaticamente?**

Use as APIs novos treinamentos. Para obter mais informações, consulte [modelos de aprendizado de máquina treinar novamente programaticamente](machine-learning-retrain-models-programmatically.md). Código de exemplo também está disponível na [Demonstração de treinamento do Microsoft Azure máquina aprendizado](https://azuremlretrain.codeplex.com/).

### <a name="create"></a>Criar

**Pode implantar o modelo localmente ou em um aplicativo sem uma conexão de internet?**

Não.


**Há uma latência de linha de base esperada para todos os serviços web?**

Consulte os [limites de assinatura do Azure](../azure-subscription-service-limits.md)

### <a name="use"></a>Usar

**Quando eu quiser executar meu modelo de previsão como um serviço de execução de lote versus um serviço de resposta à solicitação?**

O serviço de resposta à solicitação (RR) é um serviço web de baixa latência, alta escala que é usado para fornecer uma interface para modelos de estado que são criadas e implantadas do ambiente experimento. O serviço de execução em lotes (BES) é um serviço de pontuação assíncrona um lote de registros de dados. A entrada para BES é semelhante a entrada de dados usada em RR. A principal diferença é que o BES lê um bloco de registros de uma variedade de fontes, como os serviços de Blob e tabela no, banco de dados do SQL Azure, Azurehdinsight (consulta seção) e fontes de HTTP. Para obter mais informações, consulte [como consumir serviços da web de aprendizado de máquina](machine-learning-consume-web-services.md).

**Como posso atualizar o modelo para o serviço web implantado?**

Atualizar um modelo de previsão para um serviço já implantado é tão simple quanto modificando e reexecutando o experimento que você usou para criar e salvar o modelo de treinamento. Quando você tiver uma nova versão do modelo de treinamento disponível, Studio de aprendizado de máquina perguntará se você deseja atualizar seu serviço da web. Para obter detalhes sobre como atualizar um serviço web implantado, consulte [implantar um serviço da web de aprendizado de máquina](machine-learning-publish-a-machine-learning-web-service.md).

Você também pode usar as APIs de treinamento.
Para obter mais informações, consulte [modelos de aprendizado de máquina treinar novamente programaticamente](machine-learning-retrain-models-programmatically.md). Código de exemplo também está disponível na [Demonstração de treinamento do Microsoft Azure máquina aprendizado](https://azuremlretrain.codeplex.com/).

**Como monitorar o meu serviço web implantado em produção?**

Depois que um modelo de previsão foi implantado, você pode monitorá-lo do Azure clássico portal (somente para serviços de web clássico) ou o portal de serviços de Web de aprendizado de máquina Azure. Cada serviço implantado tem seu próprio painel onde você pode ver o monitoramento informações para esse serviço. Para obter mais informações sobre como gerenciar seus serviços web implantado, consulte [Gerenciar um espaço de trabalho de aprendizado de máquina do Azure](machine-learning-manage-workspace.md)e [Gerenciar um serviço Web usando o portal de serviços de Web de aprendizado de máquina Azure](machine-learning-manage-new-webservice.md) .

**Existe um lugar onde posso ver a saída do meu RR/BES?**

RR, a resposta do serviço web normalmente é onde você pode ver o resultado. Você também pode escrevê-lo ao armazenamento de blob do Microsoft Azure. Para BES, a saída é gravada em um blob por padrão. Você também pode escrever a saída para um banco de dados ou usando os [Exportar dados] de tabela[ export-data] módulo.

**Pode criar serviços web somente a partir de modelos criados no Studio de aprendizado de máquina?**

Não, você também pode criar serviços web diretamente de blocos de anotações de Jupyter e RStudio.

**Onde posso encontrar informações sobre os códigos de erro?**

Consulte [Códigos de erro de módulo de aprendizado de máquina](https://msdn.microsoft.com/library/azure/dn905910.aspx) para uma lista de códigos de erro e descrições.

## <a name="scalability"></a>Escalabilidade

**Qual é a escalabilidade do serviço da web?**

Atualmente, o ponto de extremidade padrão está provisionado com 20 solicitações RR simultâneas por ponto de extremidade. Você pode expandir isso para 200 solicitações simultâneas por ponto de extremidade e você pode dimensionar cada serviço web para pontos de extremidade de 10.000 por serviço web, conforme descrito em [escala um serviço Web](machine-learning-scaling-webservice.md). Para BES, cada ponto de extremidade permite 40 solicitações de processamento por vez e solicitações adicionais além 40 solicitações são enfileiradas. Essas solicitações enfileiradas executadas automaticamente como a saia de fila.


**Trabalhos de R são distribuídos entre nós?**

Não.  


**A quantidade de dados pode usar para treinamento?**

Módulos no Studio de aprendizado de máquina oferecem suporte a conjuntos de dados de até 10 GB de dados numéricos condensado denso para casos comuns de uso. Se um módulo levar mais de uma entrada, o tamanho total para todas as entradas juntos é 10 GB. Você também pode obter amostra maiores conjuntos de dados por meio de seção ou Azure SQL Database consultas ou por pré-processamento com [aprendendo com contagens] [ counts] módulos antes de inclusão.  

Os seguintes tipos de dados podem expandir em conjuntos de dados maiores durante normalização de recurso e limitam-se a menos de 10 GB:

- esparso
- categorias
- cadeias de caracteres
- dados binários

Os seguintes módulos limitam-se com conjuntos de dados menor que 10 GB:

- Módulos de recomendação
- Módulo SMOTE
- Script módulos: R, Python, SQL
- Módulos onde o tamanho de dados de saída pode ser maior que tamanho de dados de entrada, como ingressar ou recurso hash.
- Validação cruzada, ajustar o modelo Hyperparameters, Ordinal regressão e uni-vs-All Multiclass, quando o número de iterações é muito grande.

Para conjuntos de dados maiores que alguns GB, você deve carregar dados para o armazenamento do Azure ou banco de dados do SQL Azure, ou use HDInsight, em vez de diretamente upload de um arquivo local.


**Existe qualquer vetor limitações de tamanho?**

Linhas e colunas limitam cada a limitação de .NET do Max Int: 2.147.483.647.

**O tamanho da máquina virtual sendo usado para executar o serviço da web pode ser ajustada?**

Não.  

## <a name="security-and-availability"></a>Segurança e disponibilidade

**Quem tem acesso ao ponto de extremidade de http para o serviço web por padrão? Como restringir o acesso ao ponto de extremidade?**

Depois de um serviço web é implantado, um ponto de extremidade padrão é criado para esse serviço. O ponto de extremidade padrão pode ser chamado usando sua chave de API. Pontos de extremidade adicionais podem ser adicionados com suas próprias chaves de portal clássico do Azure ou programaticamente usando as APIs de gerenciamento de serviço Web. Teclas de acesso são necessários para fazer chamadas para o serviço da web. Para obter mais informações, consulte [conectar a um serviço da web de aprendizado de máquina](machine-learning-connect-to-azure-machine-learning-web-service.md).


**O que acontece se a minha conta de armazenamento do Azure não pode ser encontrada?**

Studio de aprendizado de máquina depende de uma conta de armazenamento do Azure fornecido pelo usuário para salvar dados intermediários ao executar o fluxo de trabalho. Esta conta de armazenamento é fornecido Studio de aprendizado de máquina no momento em um espaço de trabalho é criado. Após o espaço de trabalho é criado, se a conta de armazenamento é excluída e não pode mais ser encontrada, o espaço de trabalho deixarão de funcionar e todas as experiências desse espaço de trabalho falhará.

Se você excluiu acidentalmente a conta de armazenamento, recrie a conta de armazenamento com o mesmo nome na mesma região como a conta de armazenamento excluída. Depois disso, sincronizado novamente a tecla de acesso.


**O que acontece se minha chave de acesso da conta de armazenamento está fora de sincronia?**

Studio de aprendizado de máquina depende de uma conta de armazenamento do Azure fornecido pelo usuário para armazenar dados intermediários ao executar o fluxo de trabalho. Esta conta de armazenamento é fornecido Studio de aprendizado de máquina no momento em um espaço de trabalho for criado e as teclas de acesso estão associadas a esse espaço de trabalho. Se as teclas de acesso são alteradas, após o espaço de trabalho é criado, o espaço de trabalho não pode mais acessar a conta de armazenamento. Deixarão de funcionar e todas as experiências desse espaço de trabalho falhará.

Se você tiver alterado as teclas de acesso de conta de armazenamento, sincronizado novamente as teclas de acesso no espaço de trabalho usando o portal de clássico Azure.  


## <a name="azure-marketplace"></a>Azure Marketplace

Consulte as [perguntas Frequentes para publicar e usar aplicativos no mercado de aprendizado de máquina](machine-learning-marketplace-faq.md).

## <a name="support-and-training"></a>Suporte e treinamento

**Onde posso obter treinamento para aprendizado de máquina do Azure?**

[Centro de documentação de aprendizado de máquina Azure](https://azure.microsoft.com/services/machine-learning/) hospeda tutoriais em vídeo e guias explicativos. Estes guias fornecem uma introdução aos serviços e percorrer o ciclo de vida de ciência de dados de importação de dados, a limpeza de dados, a criação de modelos de previsão e implantá-los em produção com aprendizado de máquina do Azure.

Estamos adicionando material novo para o Centro de aprendizado de máquina continuamente. Você pode enviar solicitações para material de aprendizagem adicionais sobre o Centro de aprendizado de máquina no [Fórum de comentários do usuário](https://windowsazure.uservoice.com/forums/257792-machine-learning).

Você também pode encontrar treinamento em [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**Como para obter suporte para aprendizado de máquina do Azure?**

Para obter suporte técnico para aprendizado de máquina do Azure, vá para [Suporte do Azure](/support/options/) e selecione **Aprendizado de máquina**.

Azure aprendizado de máquina também possui um Fórum da comunidade no MSDN onde você pode fazer perguntas relacionadas ao aprendizado de máquina do Azure. O fórum é monitorado pela equipe de aprendizado de máquina do Azure. Visite o [Fórum do Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).

## <a name="billing-questions"></a>Perguntas sobre cobrança

**Como funciona a cobrança de aprendizado de máquina?**

Há dois componentes para o serviço de aprendizado de máquina do Azure. O Studio de aprendizado de máquina e serviços da Web de aprendizado de máquina.

Enquanto estiver avaliando Studio de aprendizado de máquina, você pode usar a camada de cobrança gratuita.  A camada gratuita também permite que você implantar um serviço web clássico com capacidade limitada.

Quando você tiver decidido que aprendizado de máquina do Azure atenda às suas necessidades, você pode se inscrever para a camada padrão. Para inscrever-se, você deve ter uma assinatura do Microsoft Azure.

Na camada padrão você é cobrado mensalmente para cada espaço de trabalho que você define no Studio de aprendizado de máquina. Quando você executa um experimento no studio, você será cobrado por recursos de computação quando você estiver executando um experimento. Quando você implantar um serviço Web clássico, transações e calcular horas são faturadas em uma base pré-pago (PAYG).

Os novos serviços de Web de aprendizado de máquina apresentar cobrança planos que permitem a capacidade de previsão mais nos custos. Preços hierárquico oferece taxas descontadas aos clientes que precisam de uma grande quantidade de capacidade.

Quando você cria um plano, você confirmar um custo fixo que vem com uma quantidade incluída de horas de computação de API e transações de API. Se precisar de mais quantidades incluídas, você pode adicionar instâncias adicionais para o seu plano. Se você precisar quantidades muito mais incluídas, você pode escolher um plano de nível superior que fornece consideravelmente que mais incluíam quantities e uma taxa de descontada melhor.

Depois que as quantidades incluídas no instâncias existentes são usadas para cima, uso adicionais cobrada de acordo com a taxa de excedente associada a camada de plano de cobrança.

Observação: Quantidades incluídas são realocadas a cada 30 dias e não utilizadas quantidades incluídas não ser transferidos para o próximo período.

Para informações sobre preços e cobrança adicional, consulte [Preços de aprendizado de máquina](https://azure.microsoft.com/pricing/details/machine-learning/).

**O aprendizado de máquina possui uma avaliação gratuita?**

 Azure aprendizado de máquina tem uma opção de assinatura gratuita (consulte [Preços de aprendizado de máquina](https://azure.microsoft.com/pricing/details/machine-learning/) para obter detalhes), e Studio de aprendizado de máquina tem uma avaliação de avaliação rápida de 8 horas disponível (efetuar login no [Studio de aprendizado de máquina](https://studio.azureml.net/?selectAccess=true&o=2) para esta avaliação).

 Além disso, quando você se inscrever para uma avaliação gratuita Azure, você pode tentar qualquer serviços do Azure para um mês. Para saber mais sobre a avaliação gratuita Azure, visite [Perguntas Frequentes de avaliação gratuita do Azure](/pricing/free-trial-faq/).

**O que é uma transação?**

Uma transação representa uma chamada de API que aprendizado de máquina do Azure responde. Transações de chamadas de serviço de solicitação-resposta (RR) e serviço de execução de lote (BES) são agregadas e cobrada de acordo com seu plano de cobrança.

**Posso usar as quantidades de transação incluídos em um plano para transações RR e BES?**

Sim, suas transações do seu RR e BES agregadas e cobrada de acordo com seu plano de cobrança.

**O que é uma hora de computação API?**

Uma hora de computação API é a unidade de cobrança para as chamadas de API de tempo para executar usando os recursos de computação ML. Todas as chamadas são agregadas para fins de cobrança.

**Quanto tempo leva uma chamada de produção típico API?**

Tempos de chamada de produção API podem variar significativamente, geralmente variando de centenas de milissegundos a alguns segundos, mas podem exigir minutos dependendo da complexidade do modelo de aprendizado de máquina e processamento de dados. A melhor maneira de estimar tempos de chamada de produção API é um modelo do serviço de aprendizado de máquina de padrão de referência.

**O que é uma hora Studio calcular?**

Uma hora de calcular Studio é a unidade de cobrança para o tempo de agregação que usam suas experiências calcular recursos no studio.

**Na nova Web Services, o que é a camada de desenvolvimento/teste destinada?**

Serviços Web do novo Azure ML oferecem vários níveis que você pode usar para provisionar seu plano de cobrança. A camada de desenvolvimento/teste é uma camada que fornece limitadas quantidades incluídas que permitem testar seu experimento como novo serviço web sem gera custos. Você tem a oportunidade de "Iniciar o pneus" para ver como ele funciona.

**Há encargos de armazenamento separado?**

A camada gratuitos de aprendizado de máquina não exigem ou permitir armazenamento separado. O nível padrão de aprendizado de máquina exige que os usuários têm uma conta de armazenamento do Azure. Armazenamento do Azure é [cobrado separadamente](https://azure.microsoft.com/pricing/details/storage/).

**Como funciona o alto-disponibilidade de suporte de aprendizado de máquina?**

Tempos de chamada de produção API podem variar significativamente, geralmente variando de centenas de milissegundos a alguns segundos, mas podem exigir minutos dependendo da complexidade do modelo de aprendizado de máquina e processamento de dados. A melhor maneira de estimar tempos de chamada de produção API é um modelo do serviço de aprendizado de máquina de padrão de referência.

**Que tipos específicos de recursos de computação Minhas chamadas de produção API serão ser executadas?**

O serviço de aprendizado de máquina é um serviço de vários locatários e recursos de computação real usados no back-end variam e são otimizados para desempenho e capacidade de previsão.

### <a name="management-of-new-web-services"></a>Gerenciamento de novos serviços da Web

**O que acontece se eu excluir meu plano?**

O plano é removido da sua assinatura, e você será cobrado por um uso Rateado.

Observação: Você não pode excluir um plano que está em uso por um serviço web. Para excluir o plano, você deve atribuir um novo plano ao serviço da web ou excluir o serviço web.

**O que é uma instância de plano?**

Uma instância de plano é uma unidade de quantidades incluídas que você pode adicionar ao seu plano de cobrança. Quando você seleciona um nível de cobrança para seu plano de cobrança, ele vem com uma instância. Se precisar de mais quantidades incluídas, você pode adicionar instâncias de camada de cobrança selecionada para o seu plano.

**Quantas instâncias de plano poderá adicionar?**

Você pode ter uma instância da camada de desenvolvimento/teste em uma assinatura.

Para níveis S1, S2 e S3, você pode adicionar até necessárias.

Observação: Dependendo do seu uso previsto, pode ser mais econômico para atualizar para um nível superior ao quantidades incluído em vez de adicionar instâncias para o nível atual.

**O que acontece quando eu altero o plano níveis (Atualizar / downgrade)?**

Plano antigo é excluído e o uso atual é cobrado em uma base rata. Um novo plano com as quantidades incluídos completos da camada atualizado/downgrade é criado para o restante do período.

Observação: Quantidades incluídas estão alocadas por período e quantidades não usadas não ser transferidos.

**O que acontece quando eu aumentar as instâncias em um plano?**

Quantidades são incluídas em uma base rata e podem demorar 24 horas para ser eficaz.

**O que acontece quando excluo uma instância de um plano?**

A instância é removida da sua assinatura, e você é cobrado para um uso Rateado.


### <a name="signing-up-for-new-web-services-plans"></a>Inscrição no plano de novos serviços da Web

**Como entrar para cima para um plano?**

Você tem duas maneiras de criar planos de cobranças.

Quando você implanta um novo serviço web, você pode escolher um plano existente ou criar um novo plano.

Planos criados dessa maneira são na sua região padrão e seu serviço da web será implantado no região.

Se você quiser implantar os serviços em regiões diferente de sua região padrão, talvez você queira definir seus planos de cobranças antes de implantar seu serviço

Nesse caso, você pode fazer para o portal de serviços de Web de aprendizado de máquina Azure e navegue até a página de plano. A partir daí, você pode adicionar e excluir planos, bem como modificar planos existentes.

**Qual plano deve escolher começar?**

Recomendamos que você começar com o padrão S1 nível e monitorar o seu serviço para uso. Se você achar que você está usando suas quantidades incluídas rapidamente, você pode adicionar instâncias ou mover para um nível superior e obter melhor descontadas taxas. Você pode ajustar seu plano de cobrança conforme necessário em todo o seu ciclo de cobrança.

**Quais são as regiões estão os novos planos disponíveis no?**

Os novos planos de cobranças estão disponíveis nas regiões a três produção na qual oferecemos suporte os novos serviços da web:

* Centro Sul dos EUA
* Europa Ocidental
* Sudeste da Ásia

**Tenho serviços da web em várias regiões. É necessário um plano para cada região?**

Sim. Plano de preços variam por região. Quando você implanta um serviço da web para outra região, você precisa atribuí-la um plano específico para essa região.

### <a name="new-web-services---overages"></a>Novos serviços da Web - excedente

**Como verificar se meu uso do serviço da web está em excedente?**

Você pode ver o uso em todos os seus planos na página de plano no portal do Azure serviços de Web de aprendizado de máquina. entre no portal e clique na opção de menu planos.

Nas transações e computação colunas da tabela, você pode ver as quantidades incluídas o plano e a porcentagem usada.

**O que acontece quando eu uso o as quantidades de incluir na camada de desenvolvimento/teste?**

Serviços que têm um nível de desenvolvimento/teste atribuído a elas são parados até o próximo período ou movê-los para uma das camadas pagas.

**Serviços Web do clássico e excedente de novos serviços da Web, como são os preços calculados para cargas de trabalho de resposta solicitar (RR) e em lotes (BES)?**

Para uma carga de trabalho de RR, cobrado para cada chamada de transação API feitas e o tempo de computação associadas a essas solicitações. Sua transação de API de produção RR os custos são calculados como o número total de API chama que você faça multiplicada pelo preço de por 1.000 transações (rateado por transação individual). Os custos de RR API produção API calcular horas são calculados como a quantidade de tempo necessário para cada chamada de API para executar, multiplicada pelo número total de transações de API multiplicada pelo preço por hora de computação de API de produção.

Por exemplo para excedente S1 padrão, 1.000.000 transações de API que leve 0.72 segundos cada para executar resultaria em (1.000.000 *transações de API de r $0,50 / 1K) em US $500 nos custos de transação da API de produção e (1.000.000* 0.72 s * $2 / hr) US $400 em produção API calcular horas, para um total de r $900.

Para uma carga de trabalho BES, cobrado da mesma maneira, no entanto, os custos de transação da API representam o número de trabalhos em lotes enviados e os custos de computação representam o tempo de computação associado a esses trabalhos em lotes. Os custos de transação de API de produção BES são calculados como o número total de trabalhos enviados multiplicada pelo preço por 1.000 transações (rateado por transação individual). Os custos de BES API produção API calcular horas são calculados como a quantidade de tempo necessário para cada linha em seu trabalho para executar multiplicada pelo número total de linhas em seu trabalho multiplicada pelo número total de trabalhos multiplicada pelo preço por hora de computação de API de produção. Ao usar a Calculadora de aprendizado de máquina, medidor de transação representa o número de trabalhos que você pretende enviar e a hora por campo de transação representa o tempo combinado necessário para todas as linhas em cada trabalho para executar.

Por exemplo, com excedente S1 padrão, se você enviar 100 trabalhos por dia que cada consist de 500 linhas que levam 0.72 segundos, em seguida, os custos de excedente mensais seria (100 trabalhos por dia = 3.100 trabalhos/mo *transações de API de r $0,50 / 1K) $1,55 nos custos de transação da API de produção e (500 linhas* sec 0.72 *3.100 trabalhos* $2/hr) $620 em produção API calcular horas , para um total de r $621.55.

### <a name="azure-ml-classic-web-services"></a>Serviços Web do Azure ML clássico

**O pré-pago ainda está disponível?**
Sim, serviços Web do clássico ainda estão disponíveis no aprendizado de máquina do Azure.  

### <a name="azure-machine-learning-free-and-standard-tier"></a>Nível padrão e gratuito de aprendizado de máquina Azure

**O que está incluído na camada gratuitos de aprendizado de máquina Azure?**

A camada gratuitos de aprendizado de máquina Azure destina-se a fornecer uma introdução detalhada ao Studio de aprendizado de máquina do Azure. Tudo o que você precisa é uma conta da Microsoft para se inscrever. A camada gratuita inclui acesso gratuito para um espaço de trabalho do Azure Studio de aprendizado de máquina por [conta da Microsoft](https://www.microsoft.com/account/default.aspx). Ele inclui a capacidade de usar até 10 GB de armazenamento e a capacidade de colocar em operação modelos como APIs preparação. Cargas de trabalho de nível gratuito não estão cobertas por um SLA e destinam-se para desenvolvimento e uso pessoal apenas. Liberar camada cargas de trabalho é €™ dados do access t conectando-se a um SQL server local.

**O que está incluído no nível padrão de aprendizado de máquina do Azure e planos?**

O nível padrão de aprendizado de máquina Azure é uma versão de produção paga do Azure Studio de aprendizado de máquina. A taxa mensal do Azure ML serviço Studio é cobrada em um acordo por mês de espaço de trabalho e rateado por meses parciais. Azure horas de experimento ML Studio são faturadas por hora de computação para experimento ativa. Cobrança é rateada por horas parciais.  

O serviço de API do Azure ML é cobrado dependendo se é um serviço web clássico ou um novo serviço web.

Os encargos seguintes são agregados por espaço de trabalho para sua assinatura.

* Assinatura de espaço de trabalho de aprendizado de máquina - a assinatura de espaço de trabalho de aprendizado de máquina é uma taxa mensal que fornece acesso a um espaço de trabalho ML Studio e é necessário para executar experiências no studio e utilizando a produção APIs.
* Studio horas - Experimente este agregados de medidor todos calcular encargos acumulados executando experiências no Studio ML e executando chamadas API de produção no ambiente de teste.
* Dados do Access conectando a um servidor SQL no local em seus modelos para seu treinamento e pontuação.
* Para serviços Web clássico:
    * API de produção calcular horas - esse medidor inclui encargos de computação acumulados pelos serviços da Web em execução em produção.
    * Transações de API de produção (em 1000s) - este medidor inclui encargos acumulados por chamada para seu serviço da web de produção.

Além de encargos anteriores, no caso de novos serviços Web, encargos são agregados ao plano selecionado:

* API de S2/S1/S3 padrão plano (unidades) - este medidor representa o tipo de instância selecionada para novos serviços da Web
* API de excedente S2/S1/S3 padrão calcular horas - esse medidor inclui encargos de computação acumulados pelos novos serviços Web executando em produção após as quantidades incluídas no instâncias existentes são usadas para cima. Uso da adicional cobrada de acordo com o overate taxa associada a camada de plano de S2/S1/S3.
* Transações padrão da API de excedente S2/S1/S3 (em 1,000s) - este medidor inclui encargos acumulados por chamada para seu novo serviço da Web de produção após as quantidades incluídas no instâncias existentes são usadas para cima. Uso da adicional cobrada de acordo com o overate taxa associada a camada de plano de S2/S1/S3.
* Computação de API de quantidade incluído horas - com os novos serviços da Web, este medidor representa a quantidade incluída de API calcular horas
* Quantidade API transações incluídas (em 1,000s) - com os novos serviços da Web, essa medidor representa a quantidade incluída de API transações


**Como entrar para cima para camada Azure ML livre?**

Tudo o que você precisa é uma conta da Microsoft. Vá para o [início de aprendizado de máquina do Azure](https://azure.microsoft.com/services/machine-learning/)e clique em **Iniciar agora**. Faça logon com sua conta da Microsoft e um espaço de trabalho no livre camada será criada para você. Você pode começar a explorar e criar experiências de aprendizado de máquina imediatamente.

**Como entrar para cima para camada Azure ML padrão?**

Primeiro você deve ter acesso a uma assinatura do Azure para criar um espaço de trabalho ML padrão. Você pode inscrever-se para uma assinatura de Azure avaliação gratuita de 30 dias e a atualização mais recente para uma assinatura paga do Azure ou adquirir uma assinatura paga do Azure imediatamente. Você pode criar um espaço de trabalho de aprendizado de máquina a partir do portal de clássico do Microsoft Azure Após obter acesso à assinatura. Veja as [instruções passo a passo](https://azure.microsoft.com/trial/get-started-machine-learning-b/).

Como alternativa, você pode ser convidado por um proprietário de espaço de trabalho ML padrão para acessar o espaço de trabalho do proprietário.

**Posso especificar minha conta de armazenamento de blob do Microsoft Azure para usar com o nível gratuito?**

Não, a camada padrão é equivalente a versão do serviço de aprendizado de máquina que estava disponível antes das camadas foram introduzidas.

**Pode implantar meu modelos de aprendizado como APIs na camada gratuita de máquina?**

Sim, você pode colocar em operação modelos de aprendizado de máquina para serviços de API de teste como parte da camada gratuita. Para colocar o serviço de API preparação em produção e obter um ponto de extremidade de produção para o serviço de operationalized, você deve usar o nível padrão.

**Qual é a diferença entre Azure gratuito avaliação e camada gratuitos de aprendizado de máquina Azure?**

A [versão de avaliação gratuita do Microsoft Azure](https://azure.microsoft.com/free/) oferece créditos que podem ser aplicados a qualquer serviço Azure por um mês. A camada gratuitos de aprendizado de máquina Azure oferece acesso contínuo especificamente para o serviço de aprendizado de máquina do Azure para cargas de trabalho não seja de produção.

**Como faço para mover um experimento do livre camada para a camada padrão?**

Para copiar suas experiências do livre camada para a camada padrão:

1.  Faça logon em Studio de aprendizado de máquina do Azure e verifique se que você pode ver o espaço de trabalho livre e o espaço de trabalho padrão no seletor de espaço de trabalho na barra de navegação superior.
2.  Alterne para o espaço de trabalho livre se você estiver no espaço de trabalho padrão.
3.  No modo de exibição de lista experimento, selecione um experimento que você gostaria de copiar e clique no botão de comando Copiar.
4.  Selecione o espaço de trabalho padrão na caixa de diálogo pop-up e clique no botão Copiar.
    Todos os conjuntos de dados associados, modelo de treinamento, etc. são copiados junto com o experimento na área de trabalho padrão.
6.  Você precisará executar novamente o experimento e republicar o seu serviço da web no espaço de trabalho padrão.

### <a name="studio-workspace"></a>Espaço de trabalho Studio

**Verei faturas diferentes para diferentes espaços de trabalho?**

Encargos de espaço de trabalho são divididos separadamente para cada medidor aplicável em uma única lista.

**Que tipos específicos de recursos de computação minhas experiências serão ser executadas?**

O serviço de aprendizado de máquina é um serviço de vários locatários e recursos de computação real usados no back-end variam e são otimizados para desempenho e capacidade de previsão.

### <a name="guest-access"></a>Acesso de convidado

**O que é o acesso de convidado ao Azure Studio de aprendizado de máquina?**

Acesso de convidado é uma experiência de avaliação restrita que permite criar e executar experiências no Studio de aprendizado de máquina Azure sem nenhum custo e sem autenticação. Sessões Convidado são não-persistente (não podem ser salvos) e limitado a 8 horas. Outras limitações incluem falta de suporte R e Python, falta de teste APIs e a capacidade de armazenamento e tamanho do dataset restrito. Por comparação, os usuários que escolher para entrar com uma conta da Microsoft têm acesso total a camada de livre de Studio de aprendizado de máquina descrito acima que inclui um espaço de trabalho persistente e recursos mais abrangentes. Escolha sua experiência de aprendizado de máquina livre clicando em **Introdução** no [https://studio.azureml.net](https://studio.azureml.net)e selecionando imaginar acesso ou entrar com uma conta da Microsoft.

<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx
