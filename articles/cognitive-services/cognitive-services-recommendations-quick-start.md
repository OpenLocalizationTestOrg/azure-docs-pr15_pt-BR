<properties
    pageTitle="Guia de início rápido: API de recomendações de aprendizado de máquina | Microsoft Azure"
    description="Azure aprendizado de máquina recomendações - guia de início rápido"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="luisca"/>

# <a name="quick-start-guide-for-the-cognitive-services-recommendations-api"></a>Guia de início rápido para a API de recomendações serviços cognitivas

Este documento descreve como integrado ao seu serviço ou aplicativo usar a [API de recomendações](http://go.microsoft.com/fwlink/?LinkId=759710).
Você pode encontrar mais detalhes sobre a API de recomendações e outros serviços cognitivas [aqui](http://go.microsoft.com/fwlink/?LinkId=759709). Neste guia, você também pode encontrar a [Referência à API recomendações](http://go.microsoft.com/fwlink/?LinkId=759348) úteis.


<a name="Overview"></a>
## <a name="general-overview"></a>Visão geral

Este documento é um guia passo a passo. Nosso objetivo é orientá-lo pelas etapas necessárias para treinar um modelo e para indicar a recursos que permitirão consumir o modelo do seu ambiente de produção.

Neste exercício levará cerca de 30 minutos.

Para usar a [API de recomendações](http://go.microsoft.com/fwlink/?LinkId=759710), você precisa executar as seguintes etapas:

1. Criar um modelo, um modelo é um contêiner de seus dados de uso, dados do catálogo e o modelo de recomendação.
1. Importar dados de catálogo - um catálogo contém informações de metadados sobre os itens.
1. Importar dados de uso - dados de uso podem ser carregados em uma das maneiras de 2 (ou ambas):
  -  Carregar um arquivo que contém os dados de uso.
  -  Enviando eventos de aquisição de dados.
  Normalmente você carrega um arquivo de uso para poder criar um modelo de recomendação inicial (inicialização) e usá-lo até que o sistema reúne suficiente dados usando o formato de aquisição de dados.
1. Criar um modelo de recomendação - esta é uma operação assíncrona no qual o sistema de recomendação leva todos os dados de uso e cria um modelo de recomendação. Essa operação pode levar alguns minutos ou várias horas dependendo do tamanho dos dados e os parâmetros de configuração de compilação. Quando disparo a compilação, você obterá uma ID de compilação. Usá-lo para verificar quando o processo de compilação foi encerrado antes de começar a consumir recomendações.
1. Consuma recomendações - recomendações de Get para um item específico ou uma lista de itens.

<a name="GetStarted"></a>
### <a name="lets-get-started"></a>Vamos começar!

Você irá iniciar construindo um modelo de recomendações. Em seguida, podemos vai orientá-lo sobre como usar os resultados gerados pelo modelo para recomendações de energia em um site de comércio.

<a name="Ex1Task1"></a>
#### <a name="task-1---signing-up-for-the-recommendations-api"></a>Tarefa 1: inscrever a API de recomendações ####

Nesta tarefa, você vai se inscrever no serviço recomendações API e criar um modelo de recomendações.

1. Acesse o **Portal do Azure** em [http://portal.azure.com/](http://portal.azure.com/) e entre com sua conta do Azure.

1.  Clique em **+ novo**.

1. Selecione a opção de **inteligência de dados** .

1. Selecione o produto de **APIs de serviços cognitivas** .
Este produto permitirá que você inicie uma assinatura para qualquer um dos serviços cognitivas APIs (nominal, análises de texto, visão do computador, etc.). Hoje, nos concentraremos na API recomendações.

1. Na página de aterrissagem cognitivas API de serviços, insira o **nome da conta** para sua assinatura de recomendações. (Para instace: "MyRecommendations"). Esse nome não deve ter espaços.

1. Em **tipo de API**, selecione **recomendações**.

1. Na **camada de preços**, você pode selecionar um plano. Você pode selecionar o nível de **livre** para 10.000 transações/mês. Este é um plano livre, portanto, é uma boa maneira de começar a experimentar o sistema. Quando você passa para produção, recomendamos que você considerar o volume de solicitação e alterar o tipo de plano de acordo. (Observação: você pode ter apenas uma assinatura de nível gratuito por vez)

1. Selecione um **Grupo de recursos**ou crie um novo caso você não tenha um.

1. Você pode alterar outros elementos na caixa de diálogo Criar. Devemos destacar que o provedor de recursos hoje é suportado apenas de centros de dados dos Estados Unidos.

1. Quando terminar com todas as seleções, clique em **criar**.

1. Aguarde alguns minutos para o recurso a ser implantado.
Quando ele é implantado, você pode ir para a seção de **chaves** na lâmina **configurações** onde será fornecida uma chave primária e secundária para usar a API.  Copie a chave primária, como ele será necessário ao criar seu primeiro modelo.

<a name="Ex1Task2"></a>
#### <a name="task-2---did-you-bring-your-data"></a>Tarefa 2: você fez trazer seus dados? ####

A API de recomendações aprenderá de seu catálogo e suas transações para fornecer recomendações de BOM produto. Isso significa que você precisa feed-la com dados boas sobre seus produtos (chamamos isso um arquivo de **catálogo** ) e um conjunto de transações grandes o suficiente para que ele encontrar interessantes padrões de consumo (chamamos esse **uso**).

1. Normalmente você prefere consultar seu banco de dados de transações essas informações.
Caso você não tiver à mão, fornecemos alguns dados de exemplo para você com base em dados de transação da Microsoft Store.

 Você pode baixar os dados do [aqui](http://aka.ms/RecoSampleData). Copiar e descompactar o MsStoreData.Zip para uma pasta no computador local.

 > **Observação:** O código de exemplo que você baixar e executar na tarefa 3 tem dados de exemplo já incorporados nele – para que esta tarefa seja opcional.  Dito isso, esta tarefa permitirá que você baixe mais realistas conjuntos de dados e permitem que você compreender as entradas para a API de recomendações melhor.

1.  Agora vamos dar uma olhada no arquivo de catálogo. Navegue até o local onde você copiou os dados.
 Abra o arquivo de catálogo no **Bloco de notas**.

 Você notará que o arquivo de catálogo é bastante simple. Ela tem o seguinte formato`<itemid>,<item name>,<product category>`

 >  AAA-04294, OfficeLangPack 2013 32/64 E34 o DwnLd Online, Office <br>
 > AAA-04303, DwnLd on-line do OfficeLangPack 2013 32/64 ET, Office  <br>
 > C9F-00168, KRUSELL Kiruna Inverter capa para Nokia Lumia 635 - Camel, Acessórios

 Devemos destacar que um arquivo de catálogo pode ser muito mais sofisticado, por exemplo, você pode adicionar metadados sobre os produtos (chamamos esses *recursos de item*). Você deve ver a seção [formato de catálogo](http://go.microsoft.com/fwlink/?LinkID=760716) a referência à API para obter mais detalhes sobre o formato de catálogo.

1. Vamos fazer o mesmo com os dados de uso. Você notará que a data de uso está no formato `<User Id>,<Item Id>,<Time Stamp>,<Event>`.

  > 00037FFEA61FCA16, 288186200, 2015/08/04T11:02:52, 0003BFFDD4C2148C de compra, 297833400, 2015/08/04T11:02:50, 0003BFFDD4C2118D de compra, 297833300, 2015/08/04T11:02:40, 00030000D16C4237 de compra, 297833300, 2015/08/04T11:02:37, 0003BFFDD4C20B63 de compra, 297833400, 2015/08/04T11:02:12, 00037FFEC8567FB8 de compra, 297833400, 2015/08/04T11:02:04, compra

Observe que os três primeiros elementos são obrigatórios. O tipo de evento é opcional. Você pode verificar o [formato de uso](http://go.microsoft.com/fwlink/?LinkID=760712) para obter mais informações sobre esse tópico.

 > **A quantidade de dados que você precisa?**
 <p>
Bem, ela realmente depende os dados de uso em si. O sistema aprende quando os usuários compram itens diferentes. Para alguns compilações como FBT, é importante saber quais itens são comprados nas mesmas transações. (Chamamos essa *ocorrências co*). Uma boa regra prática é ter a maioria dos itens ser em 20 transações ou mais, para que se você tivesse 10.000 itens em seu catálogo, recomendamos que você tenha pelo menos 20 vezes esse número de transações ou sobre 200.000 transações.
Novamente, isso é uma regra geral. Você precisará experimentar com seus dados.
</p>

<a name="Ex1Task3"></a>
####Tarefa 3: criar um modelo de recomendações####

Agora que você tem uma conta e você tiver dados, vamos criar seu primeiro modelo.

Nesta tarefa, você usará o aplicativo de exemplo para criar seu primeiro modelo.

1. Primeiro você deve estar ciente da [Referência da API de recomendações](http://go.microsoft.com/fwlink/?LinkId=759348).

1. Baixe o [Aplicativo de exemplo](http://go.microsoft.com/fwlink/?LinkID=759344) em uma pasta local.

1. Abra a solução de **RecommendationsSample.sln** localizada na pasta **c#** no Visual Studio.

1. Abra o arquivo **SampleApp.cs** . Observe que as etapas a seguir no arquivo:
 + Criar um modelo
 + Adicionar um arquivo de catálogo
 + Adicionar um arquivo de uso
 + Disparar uma compilação para o modelo
 + Obtenha uma recomendação com base em um par de itens
<p></p>

1. Substitua o valor do campo **AccountKey** com a chave da tarefa 1.

1. Percorrer a solução, e você verá como um modelo é criado.

1. Tente substituir os arquivos de catálogo e o uso que você acabou de baixar para criar um novo modelo para a Microsoft Store, ou para recomendações de catálogo. Você precisará alterar o nome do modelo e os itens para o qual você solicitar recomendações.

1. Quando o modelo é criado, anote a **ID do modelo** como você precisará ao solicitar recomendações em seu ambiente de produção.

>  Saiba mais sobre como criar tipos e como avaliar a qualidade da compilações [aqui](cognitive-services-recommendations-buildtypes.md).

<a name="Ex1Task4"></a>
### <a name="putting-your-model-in-production"></a>Colocar seu modelo em produção! ###

Agora que você sabe como criar um modelo e consumir recomendações, a próxima etapa é colocá-lo em produção em seu site, o aplicativo móvel ou integrar seu sistema de CRM ou ERP.
Obviamente, cada uma dessas implementações seria diferente. Desde que a API de recomendações são solicitados como um serviço da web, você poderá ligá-lo de qualquer um desses ambientes diferentes com facilidade.

**Importante:**  Se você quiser mostrar recomendações de um cliente público (por exemplo, o site de comércio), você deve criar um servidor proxy para fornecer as recomendações. Isso é importante para que sua chave API não é exposta a entidades (potencialmente não confiáveis) externas.

Aqui estão algumas ideias dos locais onde você pode usar recomendações:

### <a name="product-page"></a>Página do produto

**Recomendações de item**
<p>Se o modelo foi treinamento em dados de compra, ele permitirá que seu cliente para *descobrir produtos que costumam ser de seu interesse às pessoas que adquiriram o item de origem*.</p>
<p>Se o modelo foi treinamento em clique em dados, ele permitirá que seu cliente para *descobrir produtos que costumam ser visitados por pessoas que visitou o item de origem*. Esse tipo de modelo pode retornar itens semelhantes.</p>

**Frequentemente juntos comprou recomendações**
<p>R frequentemente juntos comprou compilação poderia treinamento, para que você possa obter conjuntos de itens costumam ser adquiridos junto com este item.</p>

### <a name="check-out-page"></a>Fazer check-out de página

**Recomendações de item**
<p>Recomendações modelo pode levar como uma lista de itens de entrada. Portanto, você poderia passar todos os itens do carrinho como entrada para obter recomendações.
Nesse caso, o modelo fornecerá recomendações que interessam fornecidas todos os itens do carrinho.
</p>

### <a name="landing-page"></a>Página inicial

**Recomendações de usuário**
<p>
Recomendações modelo pode tomar como a id de usuário de entrada.  Isso vai usar o histórico de transações por esse usuário para fornecer recomendações personalizadas para o usuário especificado.
</p>

Confira a [Documentação de recomendações de Item obter](http://go.microsoft.com/fwlink/?LinkID=760719).

<a name="Ex1Task6"></a>
### <a name="whats-next"></a>O que vem a seguir?
Parabéns, se você tiver feito-isso agora! Para saber que mais você pode visitar a [Referência de API de recomendações](http://go.microsoft.com/fwlink/?LinkId=759348) de completa se você tiver dúvidas, não hesite em contato conosco emmlapi@microsoft.com
