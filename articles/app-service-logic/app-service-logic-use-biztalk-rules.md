<properties
   pageTitle="Saiba mais sobre e criar um aplicativo BizTalk regras API em seu aplicativo de lógica | Microsoft Azure"
   description="Este tópico aborda os recursos do conector de regras de BizTalk e fornece instruções sobre o uso do"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/20/2016"
   ms.author="andalmia"/>

#<a name="biztalk-rules"></a>Regras de BizTalk

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Regras de negócios encapsula as políticas e as decisões que controlam processos de negócios. Essas políticas podem ser definidas anteriormente em manuais de procedimento, alguns contratos ou acordos ou podem existir como conhecimento ou especialização incorporadas em funcionários. Essas políticas são dinâmicas e sujeitos a mudança ao longo do tempo devidas a alterações em planos de negócios, regulamentos ou outros motivos.

Implementar essas políticas em linguagens de programação tradicionais requer coordenação e tempo considerável e não permite que não-programadores participem de criação e a manutenção de políticas de negócios. Regras de negócios do BizTalk fornece uma maneira para implementar essas diretivas e desassociar o restante do processo de negócios rapidamente. Isso permite fazer as alterações necessárias diretivas de negócios sem afetar o restante do processo de negócios.

##<a name="why-rules"></a>Por que as regras

Existem 3 principais motivos para usar regras de negócios do BizTalk no processo de negócios:

* Desassociar lógica de negócios de código do aplicativo
- Permitir que analistas de negócios ter mais controle sobre o gerenciamento de lógica de negócios
+ Alterações para lógica comercial acessem produção com mais rapidez

##<a name="rules-concepts"></a>Conceitos de regras

##<a name="vocabulary"></a>Vocabulário

Um _vocabulário_ é uma coleção de definições consiste nomes amigáveis para os objetos de computação usados em condições de regra e ações. Definições de vocabulário facilitam as regras de ler, compreender e compartilhar por pessoas em um domínio de negócios específico.

Vocabulários foram projetados para preencher o espaço entre semântica de negócios e implementação. Por exemplo, uma associação de dados para um status de aprovação pode aponte para um determinado coluna determinadas seguidas em determinadas banco de dados, representado por uma consulta SQL. Em vez de inserir esse tipo de representação complexo em uma regra, você pode em vez disso, criar uma definição de vocabulário, associada que associação de dados, com um nome amigável de "Status". Posteriormente você pode incluir "Status" em qualquer número de regras e o mecanismo de regra pode recuperar os dados correspondentes da tabela.

##<a name="rule"></a>Regra

Regras de negócios são instruções declarativas que regem a realização de processos de negócios. Uma regra consiste em uma condição e ações. A condição é avaliada e se ele será avaliado como true, o mecanismo de regra inicia uma ou mais ações.
Regras na estrutura de regras de negócios são definidas usando o seguinte formato:

_IF_ _condição_ _Em seguida_ _ação_

Considere o seguinte exemplo:

*Se valor for menor que ou igual a fundos disponíveis*  
*Realizar transação e confirmação de impressão*

Esta regra determina se uma transação será realizada aplicando lógica de negócios, na forma de uma comparação de dois valores monetários, na forma de um valor de transação e fundos disponíveis.
Você pode usar a regra comercial para criar, modificar e implantar regras de negócio. Como alternativa, você pode executar as tarefas anteriores programaticamente.

###<a name="conditions"></a>Condições

Uma condição é um expressão (booleana) que consiste em um ou mais predicados de verdadeiro/falso.
Em nosso exemplo, predicado menor que ou igual a é aplicado aos fundos disponíveis e quantidade. Esta condição será sempre avaliada como verdadeiro ou falso.
Predicados podem ser combinados com os operadores lógicos e, ou e não para formar uma expressão lógica potencialmente muito grande, mas serão sempre avaliada como verdadeiro ou falso.

###<a name="actions"></a>Ações

Ações são as consequências funcionais da avaliação de condição. Se uma condição de regra é atendida, uma ação correspondente ou ações são iniciadas.
Em nosso exemplo, "conduzir transação" e "Imprimir recibo" é ações que são executadas quando e somente quando, a condição (neste caso, "se quantidade é menor ou igual a fundos disponíveis") for verdadeira.
Ações são representadas na estrutura de regras de negócios, executando operações de conjunto em documentos XML.

##<a name="policy"></a>Política

Uma política é um agrupamento lógico de regras. Redija uma política, salvá-lo, testá-la e, quando estiver satisfeito com os resultados, usá-lo em um ambiente de produção.

###<a name="policy-composition"></a>Composição da política

Você pode compor políticas no portal de regras. Uma política pode conter um conjunto arbitrariamente grande de regras, mas normalmente você redige uma política de regras que pertencem a um domínio de negócios específicas dentro do contexto do aplicativo que usará a política.

###<a name="policy-testing"></a>Teste de política
Efetivamente, você pode realizar uma execução de teste da sua política antes de ser usada em um ambiente de produção. O Portal de regras permite que você forneça entradas para uma política, execute a política e exibir sua saída. A saída inclui logs, a execução da regra, a avaliação de condição e saídas resultantes.

##<a name="sample-scenario---insurance-claims"></a>Exemplo de cenário - solicitações de seguro
Vamos dar um cenário de exemplo e examiná-lo como podemos compor a lógica comercial para o mesmo.

![Texto ALT][1]

Em um cenário de solicitações de seguro realmente simple, requerente envia sua solicitação de seguro (via qualquer cliente como site, telefone aplicativo etc). Esta solicitação de declaração obtém enviada para a unidade de processamento de declaração da empresa e baseada no resultado do processamento, a declaração pode ser qualquer um dos aprovado, rejeitado ou enviadas ao longo de ainda mais o processamento manual.
A unidade de processamento de declaração em nosso cenário seria aquele abrangendo a lógica de negócios para o sistema. Dê uma olhada mais atenta esta unidade, podemos ver o seguinte:

![Texto ALT][2]

Vamos agora use regras de negócios para implementar essa lógica de negócios.


##<a name="creation-of-rules-api-app"></a>Criação do aplicativo de Api de regras


1. Faça logon no Portal do Azure
2. Selecione Novo -> Marketplace e em seguida, procure *BizTalk regras*
3. Selecione BizTalk regras na lista de resultados. Abre a lâmina BizTalk regras
4. Selecione o botão *criar* ![texto Alt][3]
1. Na nova lâmina que se abre, insira as seguintes informações:  
    1. Nome – dar um nome para seu aplicativo de API de regras
    1. Plano de serviço de aplicativo – selecione ou crie um novo plano de serviço de aplicativo
    1. Preços camada – escolha o nível de preços que você quiser este aplicativo reside em
    1. Grupo de recursos – Selecione ou crie um grupo de recursos onde o aplicativo deve reside em
    2. Assinatura - selecione a assinatura que deseja usar
    1. Local – escolha a localização geográfica onde quer que o aplicativo seja implantado.
4.  Selecione *criar*. Seu aplicativo de API do BizTalk regras seriam criado em poucos minutos.

##<a name="vocabulary-creation"></a>Criação de vocabulário
Depois de criar um aplicativo de API do BizTalk regras, a próxima etapa seria criar vocabulários. As expectativas é o desenvolvedor a pessoa mais comuns para fazer este exercício. Veja aqui como obter isso:


1. Lançamento sua BizTalk regras API do aplicativo a partir do portal indo para procurar -> API aplicativos - ><Your Rules API App>. Isso receberá você ao painel de aplicativo da API de regras semelhante à abaixo:

   ![Texto ALT][4]

2. Selecione "Definições de vocabulário". Isso mostra a tela de criação de vocabulário 3 Selecione "Adicionar" para começar a adicionar novas definições de vocabulário.
Há 2 tipos de vocabulário definições suportados – Literal e XML.

##<a name="literal-definition"></a>Definição de literal
1.  Depois de clicar em "Adicionar", um novo Blade "Adicionar definição" abre. Insira os seguintes valores
  1.    Nome – somente caracteres alfanuméricos são esperados sem caracteres especiais. Isso também deve ser exclusivo para sua lista de definição de vocabulário existente.
  2.    Descrição – campo opcional.
  3.    Tipo de definição – há 2 tipos suportados. Escolha Literal neste exemplo
  4.    Tipo de dados – Isso permite que os usuários possam selecionar o tipo de dados da definição. No momento há suporte para a tipos de 4 dados: eu.  Cadeia de caracteres – esses valores devem ser inseridos aspas duplas ("exemplo cadeia")  
    II. Booliana – isso pode ser verdadeiro ou falso  
    III.    Número – pode ser qualquer número decimal  
    IV. DateTime – isso significa que a definição de tipo de data. Dados devem ser inseridos usando este formato – dd/mm/aaaa hh AM\PM  
  5. Entrada – isso é onde você pode inserir o valor de sua definição. Os valores inseridos aqui devem estar de acordo com o tipo de dados escolhido. Você pode digitar um único valor, um conjunto de valores separados por vírgulas, ou um intervalo de valores usando a palavra-chave *para*. Por exemplo, você pode inserir valor exclusivo 1; um conjunto de 1, 2, 3; ou um intervalo de 1 a 5. Observe que o intervalo tem suporte apenas para os números.
  6. Selecione *Okey*.

![Texto ALT][5]
##<a name="xml-definition"></a>Definição XML
Se o tipo de vocabulário é escolhido como XML, as entradas a seguir precisa ser especificado  
  a.    Esquema – clicar sobre isso abrirá um novo usuário permitindo blade para escolher em uma lista de esquemas já carregados ou permitir para carregar um novo.
b.    XPATH – esta entrada obtém desbloqueados somente depois de escolher um esquema na etapa anterior. Clicar em isso exibirá o esquema que foi selecionado e permite ao usuário selecionar o nó para o qual uma definição de vocabulário precisa ser criado.  
  c.    FATO – essa entrada identifica qual objeto de dados deve ser tio o mecanismo de regras para processamento. Esta é uma propriedade avançada e é definida como o pai do XPATH selecionado por padrão. FATO se torna particularmente importante para cenários encadeamento e conjunto.

![Texto ALT][6]

### <a name="add-bulk"></a>Adicionar em massa
As etapas acima tem capturado a experiência de criação de definições de vocabulário. Uma vez criado, as definições de vocabulário criados aparecerão no formulário de lista. Há requisitos para poder gerar várias definições do mesmo esquema em vez de repetir as etapas acima toda vez. Isso é onde a capacidade de adicionar massa se torna muito útil.
Selecionar "Em massa adicionar", você será levado para um novo blade. A primeira etapa é selecionar o esquema para o qual várias definições devem ser criados. Selecionar isso abrirá um novo blade permitir que você seja escolhe em uma lista de esquemas já carregados ou permitir para carregar um novo.
Agora a propriedade XPATHS obtém desbloqueada. Selecionar isso abrirá o Visualizador de esquema onde vários nós podem ser selecionados.
Os nomes para várias definições criados padrão serão o nome do nó selecionado. Eles sempre podem ser modificados após a criação.

![Texto ALT][7]

##<a name="policy-creation"></a>Criação de políticas
Quando o desenvolvedor tiver criado vocabulários necessários, a expectativas é que o analista de negócios seria políticas da empresa de criar um através do Portal do Azure.  
    1. no aplicativo regras criadas, há uma lente de política clicando em que o usuário entrará na página de criação de política.  
    2. esta página mostrará a lista de diretivas que tem esse aplicativo específico de regras. O analista pode adicionar uma nova política simplesmente digitando um nome de política e pressionar a tecla tab duas vezes. Várias diretivas podem residem em um único aplicativo de API de regras.  
    3. selecionando a política criada levará o usuário para a página de detalhes de política onde um pode ver as regras que são na política.  
    ![Texto ALT][8]
 4.  Selecione "Adicionar" para adicionar uma nova regra. Você será levado para um novo blade.

##<a name="rule-creation"></a>Criação de regra
Uma regra é um conjunto de instruções de condição e uma ação. As ações são executadas se a condição for avaliada como true. Na lâmina criar regra, dê um nome de regra exclusiva (para essa política) e uma descrição (opcional).
A caixa de condição (se) pode ser usada para criar instruções condicionais complexas. A seguir estão as palavras-chave têm suportadas:  
1.  E – operador condicional  
2.  Ou – operador condicional  
3.  faz\_não\_existe  
4.  existe  
5.  FALSO  
6.  é\_igual\_para  
7.  é\_maior\_que  
8.  é\_maior\_que\_igual\_para  
9.  é\_em  
10. é\_menos\_que  
11. é\_menos\_que\_igual\_para  
12. é\_não\_em  
13. é\_não\_igual\_para  
14. Mod  
15. verdadeiro  

A caixa de Action(THEN) pode conter várias instruções, uma por linha, para criar ações que serão executadas. A seguir estão as palavras-chave têm suportadas:  
1.  é igual a  
2.  FALSO  
3.  verdadeiro  
4.  interromper  
5.  Mod  
6.  nulo  
7.  Atualizar  

As caixas de condição e uma ação fornecem Intellisense para ajudá-lo a criar uma regra rapidamente. Isso pode ser acionado por pressionar ctrl + espaço ou basta começar a digitar. Palavras-chave digitadas caracteres correspondentes serão automaticamente filtradas para baixo e mostradas. A janela intellisense mostrará todas as palavras-chave e definições de vocabulário.
![Texto ALT][9]

##<a name="explicit-forward-chaining"></a>Explícitas encadeamento de encaminhamento
Suporta BizTalk regras explícitas encadeamento para a frente, se os usuários gostaria de reavalie regras em resposta a determinadas ações, eles podem disparar isso usando determinadas palavras-chave. A seguir estão as palavras-chave têm suportadas:  
   1.   atualizar <vocabulary definition> – esta palavra-chave avalia novamente todas as regras que usam a definição de vocabulário especificado em sua condição.  
   2.   Interromper – esta palavra-chave interrompe todas as execuções de regra

##<a name="enabledisable-rules"></a>Regras de Enable\Disable
Cada regra de política pode ser habilitada ou desabilitada. Por padrão, todas as regras são habilitadas. Regras desativadas não quer ser executado durante a execução de política. Regras de Enable\Disable podem ser feitas tanto da lâmina regra diretamente – os comandos estão disponíveis na barra de comandos na parte superior da lâmina ou da política, o menu de contexto (com o botão direito em uma regra) tem a opção de enable\disable.

##<a name="rule-priority"></a>Prioridade da regra
Todas as regras de uma política são executadas em ordem. A prioridade de execução é determinada pela ordem em que ocorrem na política. Essa prioridade pode ser alterada simplesmente arrastando e soltando a regra.

##<a name="test-policy"></a>Política de teste
Você pode testar suas políticas usando o comando "Testar Policy" na lâmina testar política. Neste blade você pode ver uma lista de definições de vocabulário que são usados na política que exigem uma entrada do usuário. Os usuários podem adicionar manualmente valores para essas entradas para seu cenário de teste. Como alternativa, os usuários também podem optar por importar testar XMLs entradas. Quando todas as entradas estiverem na, o teste pode ser executado e saídas para cada definição de vocabulário serão exibidas na coluna saída para facilitar a comparação. Para exibir logs de amigável analista de negócios, clique em "Exibir Logs" para exibir os logs de execução. Para salvar os logs, a opção "Salvar Output" esteja disponível para armazenar todos os dados relacionados para análise independente de teste.

## <a name="using-rules-in-logic-apps"></a>Usando regras nos aplicativos de lógica
Depois que a política foi criada e testada, ele está pronto para consumo. Você pode criar um novo aplicativo de lógica selecionando lógica de aplicativos do lado esquerdo da home page do portal. Depois que seu aplicativo de lógica tiver sido criado, iniciá-lo e selecione *disparadores e ações*. Você pode selecionar o modelo de *Criar do zero* . Siga as etapas para adicionar seu aplicativo de API do BizTalk regras para o aplicativo de lógica. Quando isso é feito, haverá uma opção para escolher qual aplicativo de API de regras (ação) para o destino. Ações incluem a lista de políticas que serão executadas. Escolha uma diretiva específica após o qual as entradas necessários para a política precisa ser fornecida. Os usuários podem usar a saída do aplicativo API regras downstream para decisões posterior.

## <a name="using-rules-via-apis"></a>Usando regras por meio de APIs
O aplicativo de API de regras também podem ser chamado usando um conjunto sofisticado de APIs. Essa maneira como os usuários não restringiu usando apenas aplicativos de lógica e podem usar as regras em qualquer aplicativo fazendo chamadas REST. As APIs REST exatas disponíveis podem ser visualizadas clicando nas lentes de "Definição de API" no painel de regras.

![Texto ALT][10]

A seguir é um exemplo de como um pode usar essa API em C#

            // Constructing the JSON message to use in API call to Rules API App

            // xmlInstance is the XML message instance to be passed as input to our Policy
            string xmlInstance = "<ns0:Patient xmlns:ns0=\"http://tempuri.org/XMLSchema.xsd\">  <ns0:Name>Name_0</ns0:Name>  <ns0:Email>Email_0</ns0:Email>  <ns0:PatientID>PatientID_0</ns0:PatientID>  <ns0:Age>10.4</ns0:Age>  <ns0:Claim>    <ns0:ClaimDate>2012-05-31T13:20:00.000-05:00</ns0:ClaimDate>    <ns0:ClaimID>10</ns0:ClaimID>    <ns0:TreatmentID>12</ns0:TreatmentID>    <ns0:ClaimAmount>10000.0</ns0:ClaimAmount>    <ns0:ClaimStatus>ClaimStatus_0</ns0:ClaimStatus>    <ns0:ClaimStatusReason>ClaimStatusReason_0</ns0:ClaimStatusReason>  </ns0:Claim></ns0:Patient>";

            JObject input = new JObject();

            // The JSON object is to be of form {"<XMLSchemName>_<RootNodeName>":"<XML Instance String>".
            // In the below case, we are using XML Schema - "insruanceclaimsschema" and the root node is "Patient".
            // This is CASE SENSITIVE.
            input.Add("insuranceclaimschema_Patient", xmlInstance);
            string stringContent = JsonConvert.SerializeObject(input);


            // Making REST call to Rules API App
            HttpClient httpClient = new HttpClient();

            // The url is the Host URL of the Rules API App
            httpClient.BaseAddress = new Uri("https://rulesservice77492755b7b54c3f9e1df8ba0b065dc6.azurewebsites.net/");
            HttpContent httpContent = new StringContent(stringContent);
            httpContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json");

            // Invoking API "Execute" on policy "InsruranceClaimPolicy" and getting response JSON object. The url can be gotten from the API Definition Lens
            var postReponse = httpClient.PostAsync("api/Policies/InsuranceClaimPolicy?comp=Execute", httpContent).Result;

Observe que o aplicativo de API de regras acima tem suas configurações de segurança definidas como "Anon pública". Isso pode ser definido de dentro do App API usando - todas as configurações -> configurações do aplicativo -> nível de acesso.

![Texto ALT][11]

## <a name="editing-vocabulary-and-policy"></a>Vocabulário e política de edição
Uma das principais vantagens do usando as regras de negócios é que alterações a lógica comercial podem ser enviadas para produção muito mais rápida. Qualquer alteração feita em políticas e vocabulário é aplicada imediatamente em produção. Usuário simplesmente precisa navegar para a definição de vocabulário respectivos ou política e fazer as alterações para que ele entrar em vigor.

<!--Image references-->
[1]: ./media/app-service-logic-use-biztalk-rules/InsuranceScenario.PNG
[2]: ./media/app-service-logic-use-biztalk-rules/InsuranceBusinessLogic.png
[3]: ./media/app-service-logic-use-biztalk-rules/CreateRuleApiApp.png
[4]: ./media/app-service-logic-use-biztalk-rules/RulesDashboard.png
[5]: ./media/app-service-logic-use-biztalk-rules/LiteralVocab.PNG
[6]: ./media/app-service-logic-use-biztalk-rules/XMLVocab.PNG
[7]: ./media/app-service-logic-use-biztalk-rules/BulkAdd.PNG
[8]: ./media/app-service-logic-use-biztalk-rules/PolicyList.PNG
[9]: ./media/app-service-logic-use-biztalk-rules/RuleCreate.PNG
[10]: ./media/app-service-logic-use-biztalk-rules/APIDef.PNG
[11]: ./media/app-service-logic-use-biztalk-rules/PublicAnon.PNG
