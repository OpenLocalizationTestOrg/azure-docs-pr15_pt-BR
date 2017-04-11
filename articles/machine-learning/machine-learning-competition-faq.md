<properties
    pageTitle="Cortana inteligência competição perguntas Frequentes | Microsoft Azure"
    description="Perguntas frequentes sobre o Microsoft Cortana inteligência competição."
    services="machine-learning"
    documentationCenter=""
    authors="hning86"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="haining;chlovel;garye"/>

# <a name="microsoft-cortana-intelligence-competitions-faq"></a>Microsoft Cortana inteligência competição perguntas Frequentes

**O que é Cortana inteligência competição?**

A Microsoft está anunciando competição de inteligência de Cortana. Cortana inteligência competição nos permite unir uma comunidade global de entusiastas de dados, coletivamente Resolvendo alguns dos problemas de ciência de dados mais complexos do mundo. Cortana inteligência competição permitir entusiastas de dados em todo o mundo competir e criar modelos de ciências de dados altamente precisas e inteligentes. Nossa competição hospedada é baseadas em conjuntos de dados exclusivos que foram disponibilizados publicamente pela primeira vez. Participantes podem ganhar prêmios ou obter reconhecimento via nossa placar de líderes pública 10 superior. Vá [aqui](http://aka.ms/CIComp) para acessar a home page do competição.

**Com que frequência o Microsoft será lançada competição novo?**

Lançaremos 1º festa, competição pertence à Microsoft regularmente, aproximadamente a cada 8-12 semanas. 

**Onde posso fazer perguntas gerais sobre ciência de dados?**

Use nosso [Fórum de aprendizado de máquina do Microsoft Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning).

**Como eu insiro uma competição?**

Acesse a página inicial de competição por meio da Galeria de inteligência de Cortana. Esta página contém competição todos que estejam em execução. Cada concorrência será têm detalhados instruções e regras de participação, prêmios e duração na sua página de inscrição. Vá [aqui](http://aka.ms/CIComp) para acessar a home page do competição.  

1. Encontre a concorrência que você gostaria de participar na Galeria de inteligência de Cortana, leia todas as instruções e inspeção tutorial do vídeo, clique no botão "Insira concorrência" para copiar o experimento Starter em seu espaço de trabalho existente do Azure aprendizado de máquina. Se você ainda não tiver acesso a um espaço de trabalho, você deve criar uma prévia. Executar o experimento Starter, observe a métrica de desempenho e usar sua criatividade para melhorar o desempenho do modelo. Você provavelmente passa a maior parte do seu tempo nesta etapa.   

2. Crie um experimento previsão com o modelo de treinamento fora seu experimento Starter. Em seguida, cuidadosamente ajustar a entrada e saída esquema do serviço da web para garantir que eles sigam o requisito especificado na documentação concorrência. O documento tutorial geralmente será têm informações detalhadas e instruções sobre como fazer isso. Você também pode assistir ao vídeo tutorial se disponível.   

3. Implante um serviço web fora seu experimento previsão. Testar seu serviço web usando o botão de teste ou o modelo do Excel criado automaticamente garantir que ela está funcionando corretamente.   

4. Enviar seu serviço da web como a entrada de concorrência e ver sua pontuação pública na página Galeria de inteligência de Cortana concorrência. E se você tornar o placar de líderes de comemoração!  
Depois de enviar uma entrada com êxito, você pode ir para o experimento Starter copiada, iteramos e atualizar seu experimento previsão, atualizar o serviço web e envie uma nova entrada.   

**Pode usar ferramentas de código aberto para participar destas competição?**

Os participantes de concorrência aproveitam Studio de aprendizado de máquina do Azure, um serviço baseado em nuvem no pacote de inteligência de Cortana para desenvolvimento dos modelos de ciências de dados e criar entradas de concorrência para envio. ML Studio não apenas fornece uma interface gráfica para construir experiências de aprendizado de máquina, ele também permite que você exiba seus próprios scripts R e/ou Python para execução nativa. O tempo de execução R e Python no Studio ML vêm com um conjunto sofisticado de pacotes de R/Python Abrir origem, e você poderá importar seus próprios pacotes como parte da experiência também. ML Studio também tem um serviço de bloco de anotações de JuPyteR interno para você liberar exploração de dados de estilo. Claro, você sempre pode baixar os conjuntos de dados usados na competição e explore na ferramenta favorita fora ML Studio. 

**É necessário ser um cientista de dados para entrar?**

Não. Na verdade, recomendamos entusiastas de dados, curiosidade sobre ciência de dados e outras cientistas dados a inserir nosso concurso. Nós criamos documentos de suporte para permitir que todos competir. Nosso público-alvo é:

* Os desenvolvedores de dados, cientistas de dados, BI e profissionais de análise: quem é responsável por produzir conteúdo de dados e análise para consumir por outras pessoas.

* Administradores de dados: aqueles que têm o conhecimento sobre os dados, o que significa e como ele se destina a ser usado e para qual finalidade.

* Estudantes e pesquisadores: aqueles que são de aprendizagem e Obtendo dados relacionados habilidades por meio de programas acadêmicos em universidades ou participantes de massa de cursos abertos Online (MOOCs)


**Pode inserir com meus colegas como uma equipe?**

A plataforma de concorrência atualmente não oferece suporte a participação de equipe. Cada entrada de concorrência está vinculada a uma identidade de usuário único. 

**Eu preciso pagar participar de uma relação à concorrência?**

Competição é gratuitas participar. No entanto, você, precisam acessar um espaço de trabalho de aprendizado de máquina do Azure para participar. Você pode criar um espaço de trabalho livre sem um cartão de crédito simplesmente conectando com uma conta da Microsoft válida, ou uma conta do Office 365. Se você já é um cliente do Azure ou pacote de inteligência de Cortana, você pode criar e usar um espaço de trabalho padrão sob a mesma assinatura do Azure. Se você gostaria de adquirir uma assinatura do Azure você pode ir [aqui] (https://azure.microsoft.com/pricing). Observe que as taxas padrão serão aplicada ao usar um espaço de trabalho padrão para construir experiências. Ver informações sobre preços de aprendizado de máquina Azure [aqui](https://azure.microsoft.com/pricing/details/machine-learning/). 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**O que são pontuações públicas e privadas?**

Na maioria dos competição, você observará que você receberá uma pontuação pública para cada submissão que fazer, geralmente dentro de 10 a 20 minutos. Mas após a concorrência termina, você receberá uma pontuação privada que é usada para classificação final. Veja aqui o que acontece:

* O conjunto de dados inteiro usado na competição aleatoriamente é dividido com stratification em treinamento e dados de teste (o restante). A divisão aleatória é stratified para garantir que as distribuições de etiquetas no treinamento e dados de teste sejam consistentes.
 
* Os dados de treinamento são carregados e fornecidos como parte da experiência Starter na configuração de módulo de importar dados.

* Os dados de testes ainda mais são divididos em dados testes pública e privada, usando a mesma stratification.

* Os dados de testes públicos são usados para a fase inicial de pontuação. O resultado é conhecido como pontuação pública e é o que você vê no seu histórico de envio quando você enviar sua entrada. Essa pontuação é calculada para cada entrada que você enviar. Essa pontuação pública é usada para classificar você sobre o placar de líderes de público.

* Os dados de testes particulares são usados para o final round de pontuação após a concorrência encerrada. Isso é conhecido como pontuação particular. 

* Para cada participante, um número fixo, que pode variar dependendo da concorrência, entre suas entradas com as pontuações públicas altos são selecionadas automaticamente para inserir o round pontuação particular. A entrada com maior pontuação particular é selecionada para inserir a classificação final, que, por fim, determinado os vencedores do prêmio.  

**Clientes podem hospedar uma competição em nossa plataforma?**

Agradecemos organizações 3º terceiros para parceiros conosco e hospedar competição pública e privada em nossa plataforma. Temos uma equipe de integração de concorrência que serão feliz discutir o processo de integração de tal competição.  Por favor entrar em contato na [compsupport@microsoft.com](mailto:compsupport@microsoft.com) para obter mais detalhes. 

**Quais são as limitações de envios?**

Uma típica concorrência pode optar por limitar o número de entradas que você pode enviar dentro de uma extensão de 24 horas (UTC tempo 00:00:00 a 23:59:59) e o número total de entradas, que você pode enviar ao longo da duração da concorrência. Você receberá mensagens de erro adequado quando uma limitação é excedida. 

**O que acontece se eu ganhar uma competição?**

Microsoft verificará os resultados do placar de líderes de particular e, em seguida, podemos entrará em contato com você. Lembre-se de que seu endereço de email no seu perfil de usuário é atualizado.

**Como será obter o dinheiro prêmio se eu ganhar uma competição?**

Se você for um vencedor de concorrência, você precisará assinar uma declaração de qualificação, licença e liberação de. Este formulário reitera as regras de concorrência. Vencedores precisam preencher um formulário de imposto nos W-9 ou um formulário W-8BEN se não estiverem taxpayers dos EUA. Podemos contatar todos vencedores como parte do processo de pagamento de prêmios usando seus emails de registro. Consulte nossos [termos e condições](http://aka.ms/comptermsandconditions) para obter detalhes adicionais.

**O que acontece se mais de uma entrada recebe a mesma pontuação?**

A hora de envio é o separador padrão tie. A entrada enviada anteriormente outranks a entrada enviada mais tarde.

**Posso participar usando o espaço de trabalho de convidado?**

Não. Você deve usar gratuitamente ou um espaço de trabalho padrão para participar. Você pode abrir o experimento de starter concorrência em um espaço de trabalho de convidado. Mas não será capaz de criar uma entrada válida para envio. 

**Posso participar usando um espaço de trabalho em qualquer região Azure?**

Atualmente plataforma de concorrência suporta apenas entradas de envio de um espaço de trabalho na região Sul Central conosco Azure. Todos os espaços de trabalho gratuitos ao vivo no Centro Sul dos EUA. Mas se você optar por usar um espaço de trabalho padrão, verifique se que ele reside na região Sul Central conosco Azure. Caso contrário, o envio não funcionará. 

**Vamos manter competição soluções/entradas dos usuários?**

Entradas de usuário são mantidas somente para fins de avaliação identificar as soluções premiadas. Consulte nossos [termos e condições](http://aka.ms/comptermsandconditions) para obter informações específicas.
