<properties
   pageTitle="Banco de dados do SQL Azure Azure estudo de caso - GEP | Microsoft Azure"
   description="Saiba mais sobre como GEP usa o banco de dados SQL para alcançar clientes mais globais e obter mais eficiência"
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

# <a name="azure-gives-gep-global-reach-and-greater-efficiency"></a>Azure oferece alcance global GEP e eficiência

![Logotipo GEP](./media/sql-database-implementation-gep/geplogo.png)

GEP oferece softwares e serviços que permitem líderes de compras em todo o mundo maximizar seu impacto no desempenho financeiro, estratégias e operações dos seus negócios. Além dos serviços de consultoria e gerenciados, a empresa oferece inteligente por GEP®, uma plataforma de software abrangente de compras baseado na nuvem. No entanto, GEP encontrou limitações tentando soluções de suporte como inteligente por GEP com seu próprio dos data centers local: os investimentos necessários foram acentuados e requisitos regulatórios em outros países tornaria os investimentos necessários mais desanimadora ainda. Movendo-se para a nuvem, GEP tem liberar os recursos de TI, permitindo que ele se preocupar menos sobre as operações de TI e se concentrar mais no desenvolvimento de novas fontes de valor para seus clientes em todo o mundo.

## <a name="expanding-services-and-growth-by-using-azure"></a>Expandindo serviços e crescimento usando o Azure

INTELIGENTES por amor de clientes do GEP os recursos da plataforma e facilidade de uso; os clientes podem gerenciar seus processos de qualquer lugar, a qualquer momento e em qualquer dispositivo — laptop, tablet ou telefone. Movendo-se ao Microsoft Azure, GEP foi capaz acomodar seu rápido crescimento e seu potencial para expandir para novos mercados. De acordo com a VP de tecnologia do GEP, Dhananjay Nagalkar, "Microsoft Azure tem desempenha um papel fundamental no sucesso do GEP, permitindo-nos dimensionar rapidamente os serviços com agilidade e fornecendo dos data centers regionais que nos ajudam a atender às necessidades de nossos clientes globais regulamentares."

## <a name="the-limitations-of-a-do-it-yourself-datacenter"></a>As limitações de um data center tipo faça você mesmo

No 2013, líderes GEP reconheceu que eles necessária uma maneira para garantir que o desempenho e escalabilidade conforme eles aumentou seus clientes base. Nagalkar explicado, "para atender a essa demanda com nossos data centers existentes, seria tivemos que expandir a nossa infraestrutura e recursos de TI consideravelmente. O investimento e o período de tempo de que teria sido enorme." Locais físicos e máquinas virtuais requerem ampla configuração, gerenciamento, dimensionamento, backup e patches a uma taxa que teria sido dispendioso para GEP. Soluções de nuvem, por outro lado, oferecem simplicidade e conveniência que habilitado GEP concentrar-se mais em desenvolvimento em vez de gerenciar grandes — e crescimento — operações de TI. Nagalkar sabia que GEP poderia reduzir seu sobrecarga de compra, configuração e gerenciamento de infraestrutura migrando para a nuvem.

GEP também necessária uma maneira de resolver obstáculos regulamentares que mantidas-fora alguns mercados globais. Para muitos dos clientes em potencial Europeu da GEP, conformidade regulamentar seria necessário ter dados armazenados em suas regiões geográficas locais. Mas não seria prático para GEP construir vários data centers. "Investimentos de infraestrutura amplo e profissionais de TI custos poderia levar um impacto significativo para margens," de acordo com a Nagalkar. "Como resultado, podemos realmente tiveram ausente ativar clientes potenciais quem obrigatório dados armazenados localmente."

Nagalkar sabia que uma solução de nuvem pode ser a resposta para esse dilema. Se GEP foi possível mover a um provedor de nuvem com uma presença global, ele pode atender melhor seus clientes regulamentares e precisa de desempenho armazenando dados próximas locais físicos dos clientes.

## <a name="finding-smooth-skies-in-the-cloud"></a>Localizando skies suaves na nuvem

Nagalkar e sua equipe explorados várias opções de nuvem, mas a maioria foram infraestrutura-como um serviço (IaaS) – com base em soluções que prefere ter necessários GEP investindo muito em recursos de TI para configurar e gerenciar o serviço. A solução de (PaaS) de plataforma como serviço Azure acabou sendo muito melhor ajuste.

"Com o Azure, GEP não precise lidar com gerenciamento de banco de dados, a configuração de máquina virtual, patches ou outras tarefas de gerenciamento de infraestrutura," declarado Nagalkar. "Em vez disso, podemos pode se concentrar nossos recursos que fazemos melhor: aproveitando nossa experiência em compras escrever software que realmente fornece resultados para nossos clientes." 

Na verdade, mover para o Azure habilitou GEP reduzir a sua equipe de operações de TI, permitindo simultaneamente maior funcionalidade para os clientes.

Aproveitando dos data centers do Azure em todo o mundo, GEP pode estender facilmente seu alcance na Europa e Ásia. Os data centers globais habilitar GEP dimensionar com agilidade e para atender às necessidades dos clientes para dados armazenados localmente que reduz a latência e atende aos requisitos regulamentares.

## <a name="smart-by-gep-architecture"></a>INTELIGENTE por arquitetura GEP

GEP criadas inteligente por GEP desde o início no Azure. Uma motivação crítica para GEP foi a maior escalabilidade, menos tempo de inatividade e os custos de manutenção reduzida que GEP pode enfrentar com o banco de dados do SQL Azure em comparação com o qual GEP poderiam atingir local. No entanto, após movido para a nuvem, GEP descobertos novas oportunidades de desenvolvimento na nuvem, como protótipo rápido e engenharia lean para melhor atender às necessidades dos clientes. Desenvolvendo no Azure permitem GEP ausente fazer com o dores de cabeça que seus desenvolvedores podem ocorrer no local de licenciamento de software. O núcleo da inteligente por GEP é Azure SQL Database, embora GEP usa muitos outros serviços Azure rapidamente e facilmente continuem a melhorar inteligente por GEP.

![INTELIGENTE por arquitetura GEP](./media/sql-database-implementation-gep/figure1.png) Figura 1. INTELIGENTE por arquitetura GEP

## <a name="structured-data"></a>Dados estruturados

No centro do inteligente pelo aplicativo de GEP são as instâncias de banco de dados do Azure SQL que solução de energia o gerenciamento de aquisições enterprise. Quando GEP engenharia inteligente por GEP, ele viu Azure SQL Database como um ajuste perfeito para sua arquitetura, que permite a empresa para obter o mais alto grau de proteção de dados e atender aos requisitos regulamentares. GEP utiliza as várias camadas de proteção de dados que oferece de banco de dados do SQL Azure, incluindo:

- Criptografia de dados restante por meio da criptografia de dados transparente.
- Protegendo a autenticação por meio da integração Azure SQL Database com o Active Directory do Azure.
- Limitar o acesso a um subconjunto apropriado de dados com segurança em nível de linha.
- Masking dados em tempo real através de diretivas.
- Controlando eventos de banco de dados por meio de auditoria de banco de dados do Azure SQL.

> "Podemos usar todas essas opções sem fazer alterações principais de código e com impacto mínimo no desempenho," disse Nagalkar.

Usando o banco de dados do SQL Azure, GEP tem automaticamente os recursos de recuperação maiores do que poderia ter econômica engenharia no local devido aos recursos de tolerância criados para Azure SQL Database. GEP usa a capacidade de replicação de localização geográfica ativa no Azure SQL Database, juntamente com várias ativas, legíveis e onlinehttps secundárias réplicas (sempre na disponibilidade grupos) em diferentes regiões geográficas, para formar pares de alta disponibilidade. Duplicar inteligente por GEP dados entre regiões significa que, no caso de um desastre de toda a região, GEP pode facilmente recuperar dados do cliente com um objetivo de ponto de recuperação mínimo (RPO) e o objetivo de tempo de recuperação (RTO).

Cada inteligente pelo cliente GEP tem duas instâncias de banco de dados do Azure SQL: uma para processamento de transações online (OLTP) e outra para análise (por exemplo, cliente passam e análise de relatório). Pools de banco de dados elástica de banco de dados do SQL Azure habilitar GEP para gerenciar com facilidade milhares de bancos de dados globalmente para lidar com exigências de banco de dados-recurso imprevisíveis. Pools Elástico fornecem um meio de GEP garantir que os bancos de dados do cliente podem aumentar conforme necessários, sem provisionamento excessivo ou em provisionamento, permitindo também GEP para controlar os custos. Além disso, porque este é um serviço de PaaS, GEP obtém todos os novos Azure SQL Database recursos com atualizações automáticas.

## <a name="unstructured-and-semi-structured-data"></a>Dados não estruturados e semiestruturados

No entanto, alguns inteligente por dados do cliente GEP necessidades de armazenamento menos rigidamente estruturado. Para este tipo de dados, GEP utiliza armazenamento Azure Blob, o armazenamento de tabela do Azure e Azure relacionada Cache. Armazenamento de Blob do Azure armazena todos os anexos inteligente pelo carregamento de usuários GEP no aplicativo. Também é onde inteligente por GEP armazenamentos em conteúdo estático, como arquivos de JavaScript e folhas de estilos em cascata (CSS).

GEP armazena dados não-voltados ao cliente, como inteligente por dados de log GEP, em armazenamento de tabela do Azure, proporcionando GEP efetivamente ilimitado de armazenamento econômico e tempos de recuperação rápida sem precisar preocupar Configurando um esquema para os dados. GEP usa Azure relacionada Cache para um cache mestre.

## <a name="authentication-and-routing"></a>Roteamento e autenticação

Serviço de controle de acesso Azure (ACS) fornece inteligente pelos usuários GEP com uma ampla variedade de opções para assinar ao software. Azure ACS poderá federar-se com qualquer provedor de identidade que troca de dados de autenticação usando segurança declaração SAML Markup Language (), como o Active Directory Domain Services, Ping identidade, OneLogin ou SiteMinder. Isso ajuda a GEP implementar o logon único (SSO) para clientes sem se preocupar armazenar credenciais do usuário e a manutenção de políticas de senha do cliente.

Após entrar, os clientes têm acesso aos recursos corretos de negócios em inteligente por GEP. GEP usa o Gerenciador de tráfego Azure para redirecionar e solicitações de balanceamento de carga provenientes de dispositivos móveis clientes e sessões de navegador.

## <a name="other-azure-services"></a>Outros serviços do Azure

GEP utiliza um número de outros serviços do Azure para tornar inteligente por GEP responde ao cliente precisa. GEP usa serviços de nuvem Azure (funções da web e trabalhador) para apresentação de aplicativo do host e os serviços de lógica de negócios seguros. Serviços de nuvem possibilitam que os desenvolvedores gerencie a infraestrutura como código (IAC) e para implantar nova inteligente por aplicativos de GEP em uma fração do tempo que ele exigida com data centers local — tudo sem qualquer envolvimento do IT. Os desenvolvedores GEP podem usar os serviços de nuvem preparar o ambiente para testar novas versões sem afetar a implantação de produção atual. Depois de testado, GEP usa os recursos de troca de VIP de serviços de nuvem Azure para mover o código de preparação para o slot de produção dentro de um minuto, reduzindo assim o tempo de inatividade de implantação.

Para reduzir a latência do aplicativo, GEP usa a rede de entrega de conteúdo (CDN) do Azure para colocar conteúdo estático armazenado no armazenamento de Blob do Azure (como arquivos CSS e JavaScript) nos servidores de borda Fechar para os usuários. GEP usa barramento de serviço do Azure para dar suporte a padrões de arquitetura de aplicativo variando de publicação assinatura para parcialmente comando consulta responde diferenciação CQRS () e em camadas de arquitetura com acoplamento e comunicação assíncrona. GEP usa serviços de mídia do Azure para melhorar seu serviço de suporte ao cliente. GEP encontrado que ele poderia facilmente postar vídeos de suporte ao usuário em serviços de mídia do Azure. Estes vídeos respondam a perguntas comuns de usuário, que ajuda a melhorar inteligente por satisfação do usuário GEP levando alguns da carga de suporte de equipe de suporte ao cliente da GEP.

Para enviar milhares de transações e-mails gerados diariamente por inteligente por GEP, a empresa usa a API do .NET SendGrid para integrar com o Azure. Para desenvolvedores GEP, isso é fácil fazer — o complemento SendGrid Azure está disponível do Azure Marketplace. Os desenvolvedores GEP podem configurar inteligente por GEP usando o direito de pacote SendGrid NuGet no Microsoft Visual Studio; TI GEP pode monitorar o tráfego de email de SendGrid do software diretamente do Azure.

Finalmente, inteligente por GEP usa máquinas virtuais do Azure — o serviço do Azure IaaS — para hospedar aplicativos e serviços que não fazem sentido, no momento da engenharia, para substituir o software-como um serviço (SaaS) ou soluções PaaS. Por exemplo, GEP hospeda serviços de API de integração em máquinas virtuais para com locais planejamento de recursos empresariais) sistemas de ERP (como SAP, Oracle, PeopleSoft, JD Edwards, Microsoft Dynamics GP e Lawson clientes e soluções de SaaS do cliente com eficiência trocar documentos de compras, como faturas de integração do business-to-business (B2B).

> "Construção inteligente por GEP na nuvem Microsoft Azure completamente removeu a necessidade de locais IT, não apenas para GEP, mas também para operações de compras de nossos clientes." 

> — Dhananjay Nagalkar, VP de soluções de tecnologia

## <a name="expand-customer-satisfaction-without-expanding-it"></a>Expanda a satisfação do cliente sem expandir IT

Desde Migrando do local dos data centers para Azure e criando inteligente por GEP do zero na plataforma Windows Azure, o GEP aumentou escalabilidade e flexibilidade sem precisar expandir sua infraestrutura ou a equipe de TI. Na verdade, a empresa não foi adicionada recursos de TI em mais de quatro anos. O modelo de PaaS conveniente do Azure habilitou GEP reduzir seus gastos no gerenciamento de operações e suporte de fornecedor. Como resultado, GEP foi possível concentrar os recursos em desenvolvimento de software; e desenvolver em nuvem permite que os desenvolvedores GEP testar rapidamente novas ideias sem gastar tempo coordenação com IT ou preocupar locais requisitos de licenciamento. Banco de dados do SQL Azure ajuda GEP melhor garantir que seus clientes sempre desempenho e serviços excelentes.
 
## <a name="more-information"></a>Mais informações

- Home page do GEP: [GEP](http://www.gep.com)
- Inteligente, GEP: [inteligente por GEP](http://www.smartbygep.com)

##<a name="gep-contributors"></a>Colaboradores GEP

- Huzaifa Matawala, associar Director — arquitetura, GEP
- GEP Sathyan Narasingh, gerente de engenharia
- GEP Deepa Velukutty, arquitetura de banco de dados,
