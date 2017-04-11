<properties
   pageTitle="Banco de dados do SQL Azure Azure estudo de caso - Snelstart | Microsoft Azure"
   description="Saiba mais sobre como SnelStart usa o banco de dados SQL para expandido rapidamente seus serviços de negócios em uma taxa de 1.000 novos Azure SQL bancos de dados por mês"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/08/2016"
   ms.author="carlrab"/>

# <a name="with-azure-snelstart-has-rapidly-expanded-its-business-services-at-a-rate-of-1000-new-azure-sql-databases-per-month"></a>Com o Azure, SnelStart expandiu rapidamente os seus serviços de negócios em uma taxa de 1.000 novos Azure SQL bancos de dados por mês

![SnelStartLogo](./media/sql-database-implementation-snelstart/snelstartlogo.png)

SnelStart torna popular financeira e business-software de gerenciamento para empresas de pequeno e médio porte (SMB) nos países baixos. Seus 55.000 clientes são atendidos por uma equipe de 110 funcionários, incluindo uma equipe de TI 35. Movendo-se contra softwares da área de trabalho para um software-como um serviço (SaaS) oferecendo criados no Azure, SnelStart feita a maioria dos serviços internos, automatizar o gerenciamento usando o ambiente familiar em c# e otimizar o desempenho e escalabilidade por nenhuma das empresas sobre ou sob-provisionamento usando pools elástica banco de dados. Usar o Azure fornece SnelStart a fluidez de clientes móveis entre locais e na nuvem.

> [AZURE.VIDEO azure-sql-database-case-study-snelstart]

##<a name="why-snelstart-extended-services-from-the-desktop-to-the-cloud"></a>Por que o SnelStart estendido serviços da área de trabalho na nuvem

> "Trabalhar com o Azure significa podemos fornecer softwares mais rápidos, rapidamente reagir à demanda dos clientes e dimensionar soluções quando aumentam a exigências."

> — Henry foi, arquitetura de Software

SnelStart executou um negócio de software bem-sucedida por anos, usando um modelo de desenvolvimento tradicional: código, empacotar, enviar e repita. Ao longo do tempo, o ritmo das mudanças aumentou com mais rapidez e mais rápido. Normas alterados com frequência e clientes necessária maneiras mais fáceis de processar registros financeiros e colaborar com seus contadores e governo para manter-se com essas alterações.

"Software de remessa aos clientes era caro e limitação," acordo com Henry foi, arquitetura de software. "Produção, embalagem e custos de remessa limitado frequência podemos pôde liberar software. Tivemos atualizações de pacote de entregas periódicas, mas que tornou mais difícil atender às necessidades de nossos clientes. Podemos poderia nunca ter certeza de que nossos clientes atualizados para a versão mais recente do produto. Portanto, tivemos suporte a várias versões, fazendo o trabalho de suporte muito difícil bem."

Foi adiciona, "podemos queria uma maneira de atualizações de programa e lançamento em acelerada impor um ritmo, para que possamos poderia inovar com mais rapidez e criar novos serviços para nossos clientes. Nós também queria uma maneira de automatizar processos mais para simplificar a necessidades de negócios administração de nossos clientes."

Para SnelStart, a solução era estender seus serviços tornando-se um provedor de SaaS baseado em nuvem. A plataforma do Azure SQL Database ajudar SnelStart chegar lá sem gerar sobrecarga IT principal que uma solução de infraestrutura-como um serviço (IaaS) deve ter necessários.

O modelo de nuvem também permite SnelStart corrigir erros e fornecer novos recursos rapidamente, sem precisar baixar e atualizar o software de clientes. Acordo com foi, "usando serviços de nuvem Azure nos permite rapidamente agir sobre a alteração de requisitos de terceiros. Em vez de ter que enviar uma nova versão para milhares de clientes, podemos pode adaptar as informações enviadas de nosso aplicativo da área de trabalho para novos formatos obrigatório por terceiros."

##<a name="a-modern-company-with-traditional-roots"></a>Uma empresa moderna com raízes tradicionais

SnelStart é uma empresa moderna, ágil, alta tecnologia com raízes humilde encontros para 1964, quando os fundadores iniciou a empresa como um fabricante de partes de instrumentos musicais. O coração dos negócios SnelStart software realmente começou superando na década de 1980, durante a proliferação do computador pessoal. A empresa necessária uma alternativa melhor para o software de contabilidade disponível no momento, portanto, levou questões em suas própria mãos. Em 1982, a empresa criou a fundação do que eventualmente se tornaria SnelStart software de contabilidade. Desde o início, o software foi admirado por sua simplicidade e a velocidade — tanto que a empresa eventualmente alterados foco e se reinventou em uma empresa de software.

Em 1995, SnelStart lançado seu primeiro aplicativo contábeis para Windows. O aplicativo, criado em bancos de dados do Microsoft Visual Basic 1.0 e o Microsoft Access ajudar crescer o cliente base para mais de 5.000 usuários.

Hoje, SnelStart é um provedor de principal de um aplicativo de administração de negócios direcionadas para SMBs holandês e empreendedores autônomo e linha de negócios (LOB). Como Carlo Kuip, arquiteto de TI, diz: "nosso objetivo é fornecer automação de 100 por cento dos serviços de administração de negócios para nossos clientes."

##<a name="optimizing-performance-and-cost-with-elastic-pools"></a>Otimizar o desempenho e custo com pools Elástico

SnelStart foi um grande escala pioneiro de pools elástica banco de dados. Pools Elástico ajudam a empresa limitar os custos e gerenciar requisitos de desempenho com mais eficiência. Acordo com foi, "usando pools de banco de dados elástica, podemos otimizar desempenho com base nas necessidades de nossos clientes, sem excesso de provisionamento. Se tivemos provisionar com base na carga de pico, seria bastante caro. Em vez disso, a opção para compartilhar recursos entre vários bancos de dados de pouco uso nos permite criar uma solução que executa bem e é econômico. "

##<a name="azure-sql-databases-help-containerize-data-for-isolation-and-security"></a>Bancos de dados do SQL Azure ajuda containerize dados para isolamento e segurança 

Banco de dados do SQL Azure permite SnelStart facilmente e transparente mover clientes dados de administração de negócios para o Azure local. O banco de dados do SQL Azure é um contêiner conveniente que fornece isolamento de um limite para autenticação, autorização e recursos fáceis de backup e restauração. Bancos de dados fornecem um modelo conceitual adequado para administração de negócios. Acordo com Carlo Kuip, arquiteto de TI, "itens dentro esse limite de contêiner contêm dados confidenciais que é fundamentais para uma empresa e armazenar esses itens em um banco de dados isolado mantém-los bem protegidos. Podemos gerenciar autorização no nível do banco de dados e até mesmo automatizar o gerenciamento e a escala-out desses bancos de dados sem a necessidade de administradores de banco de dados (DBAs) em sua equipe."

Depósito de dados SQL Azure também desempenha um papel na história SnelStart segurança e gerenciamento, ajudando a empresa coletar dados de telemetria, como detecção de intrusos, log de atividades do usuário e conectividade.

##<a name="azure-removes-overhead-so-that-developers-can-spend-more-time-delivering-value"></a>Azure remove sobrecarga para que os desenvolvedores podem investir mais tempo fornecimento de valor 

O modelo de plataforma Windows Azure removido sobrecarga de infraestrutura e habilitado SnelStart automatizar implantações usando bibliotecas de gerenciamento do c#. Como Kuip informa, ", pudemos crescer nossas operações atuais com uma equipe muito pequena aumentando simultaneamente as opções de recuperação de desastres, velocidade e escalabilidade para nossos clientes. A mudança para o desenvolvimento de serviços liberada recursos concentrar-se em novos serviços e recursos, em vez de apenas atualizando código existente para manter-se com as novas normas ou códigos de imposto." Acrescenta, ", automatizar o gerenciamento e usando o SaaS oferecendo, podemos fornecer mais valor para nossos clientes sem precisar fazer grandes investimentos em equipe operacional." Por exemplo, usando pools de banco de dados do Azure e elástica, SnelStart conseguiu adicionar uma variedade de novos recursos, incluindo a integração de dados do cliente mais eficiente com bancos, cobrança novos serviços, verificações de plano de fundo de pequenas empresas e serviços de email.

> "Apenas os primeiros meses de 2016, podemos expandido nossas implantações Azure SQL Database de aproximadamente 5,500 mais de 12.000 e estamos atualmente adicionando cerca de 1.000 bancos de dados por mês."

> — Henry foi, arquitetura de Software

Gerenciamento de banco de dados é mais automatizado usando o recurso de trabalhos elástica. Como Kuip informa, "altamente Agradecemos a descoberta automática de bancos de dados em uma instância de [servidor] do banco de dados do SQL." Isso permite SnelStart executar operações de gerenciamento em sua base sem sobrecarga adicional de clientes dinamicamente crescente.

SnelStart também está desenvolvendo uma API que atua como um intermediário entre dados de cliente e aplicativos criados por parceiros de software de terceiros. Como Kuip estados, "essa API permitirá que outros fornecedores adicionar funcionalidade ao nosso software, como eliminando a entrada de dados para faturas e outros documentos." Os clientes não terão mais digitar manualmente faturas para o seu software de contabilidade de pequenas empresas; o software de SnelStart será exchange diretamente as informações necessárias. Isso permite aos clientes ingressar em suas tarefas de administração de negócios com o ecossistema de informação que é surgem de transformação digital no setor.  

##<a name="how-azure-services-enable-saas-for-snelstart"></a>Como os serviços do Azure habilitar SaaS para SnelStart

Usando o Azure, SnelStart pode servir seus clientes e seus contadores mais uniforme na nuvem. Por exemplo, clientes e contadores podem compartilhar informações acessando diretamente a API do cliente do SnelStart, hospedado no Azure. Kuip informa, "esses serviços reutilizáveis estão disponíveis para nossos atendimento web apps, e fornecem comuns infraestrutura e as funções para permitir o acesso para administração de negócios para clientes e software de terceiros usado por nossos clientes. Exemplos fornecendo recursos de configuração do produto, gerenciar regras de firewall e gerenciar processos de execução demorada como backups."

> Nosso objetivo é fornecer automação de 100 por cento dos serviços de administração de negócios para nossos clientes." 

> — Carlo Kuip, arquiteto de TI

Além disso, os serviços web SnelStart permitem clientes e contadores acesse facilmente os dados em pools Elástico Azure SQL Database. Este modelo SaaS, juntamente com elasticidade de banco de dados e Gerenciador de recursos do Azure, fornece SnelStart com recursos de escalabilidade que complementam cada implantação do Azure. A implementação é totalmente automatizada usando bibliotecas de gerenciamento do c#.

![Arquitetura de SnelStart](./media/sql-database-implementation-snelstart/figure1.png)

Figura 1. A partir de junho de 2016, SnelStart tem mais de 11.000 bancos de dados e mais de 50 pools elástica banco de dados
 
##<a name="simplicity-from-the-cloud"></a>Simplicidade da nuvem

Desde movendo para uma solução Azure baseado em nuvem, SnelStart foi capaz de dar suporte ao cliente rápido crescimento, oferecendo serviços e recursos inovadoras. Acordo com foi, "com o Azure, nós podemos fornecer atualizações quase contínua de nossos clientes, sem expandir nossa equipe de operações. E podemos obter todos os outros excelentes Azure recursos — como escalabilidade e recuperação de desastres — agrupados em. "

> "Quando foi realmente sobre em Redmond... Recebi uma chamada de um desenvolvedor de volta ao Holanda, transmita para mim sobre um problema específico. Pudemos obter Microsoft para oferecer uma alteração em seu ambiente de produção dentro de 48 horas para solucioná-nosso."

> — Henry foi, arquitetura de Software

SnelStart também aprecia a parceria forte que eles desenvolveu com a equipe de banco de dados do Microsoft Azure SQL. Como os estados de Kuip, "temos discussões sobre os recursos e como usar tecnologia, que é algo apreciado em ambos os lados."
O objetivo imediato SnelStart é continuam aumentando seus clientes satisfeitos base. Como foram informa, "Sem as limitações de recursos e técnicos que tivemos como um ISV, há sem limite quanto nós pode crescer."


## <a name="more-information"></a>Mais informações

- Para saber mais sobre os pools do Azure elástica banco de dados, consulte [pools de elástica banco de dados](sql-database-elastic-pool.md).

- Para saber mais sobre funções da Web e funções de trabalho, consulte [funções de trabalho](../fundamentals-introduction-to-azure.md#compute). 

- Para saber mais sobre o depósito de dados do SQL Azure, consulte [SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)

- Para saber mais sobre SnelStart, consulte [SnelStart](http://www.snelstart.nl).


