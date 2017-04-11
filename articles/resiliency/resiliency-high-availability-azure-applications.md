<properties
   pageTitle="Alta disponibilidade para aplicativos do Azure | Microsoft Azure"
   description="Visão geral técnica e informações detalhadas sobre como projetar e criar aplicativos para alta disponibilidade no Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="high-availability-for-applications-built-on-microsoft-azure"></a>Alta disponibilidade em aplicativos criados no Microsoft Azure

Um aplicativo altamente disponível absorve variações de disponibilidade, carga e temporárias falhas no hardware e serviços dependentes. O aplicativo continua operar a um usuário aceitável e o nível de resposta sistemático, conforme definido pelo necessidades de negócios ou contratos de nível de serviço de aplicativo (SLAs).

##<a name="azure-high-availability-features"></a>Recursos de alta disponibilidade Azure

Azure tem muitos recursos de plataforma interna que oferecem suporte a aplicativos altamente disponíveis. Esta seção descreve alguns desses recursos principais. Para uma análise mais abrangente da plataforma, consulte [orientações técnicas resiliência Azure](./resiliency-technical-guidance.md).

###<a name="fabric-controller"></a>Controlador de tecidos

Controlador de tecidos Azure provisiona e monitora a condição das instâncias de computação Azure. Controlador de tecidos verifica o status do hardware e software das instâncias de máquina host e convidado. Quando detecta uma falha, ele impõe SLAs retirando automaticamente as instâncias de máquina virtual. O conceito de falhas e atualização de domínios ainda mais compatível com o SLA de computação.

Quando várias instâncias de função de serviço de nuvem são implantadas, o Azure implanta essas instâncias em domínios de falha diferente. Um limite de domínio de falhas é basicamente um rack de hardware diferente na mesma região. Domínios de falha reduzem a probabilidade de que uma falha de hardware localizados interromperá o serviço de um aplicativo. Você não pode gerenciar o número de domínios de falha que estão alocados para seu colega ou funções da web. O controlador de tecidos usa recursos dedicados que são separados aplicativos hospedados Azure. Ela tem 100% de atividade porque serve como o núcleo do sistema Azure. Ele monitora e gerencia instâncias de função em domínios de falha.

O diagrama a seguir mostra o Azure recursos compartilhados que o controlador de tecidos implanta e gerencia em domínios de falha diferente.

![Exibição simplificada domínio de isolamento de falhas](./media/resiliency-high-availability-azure-applications/fault-domain-isolation.png)

Atualização domínios são semelhantes aos domínios de falha na função, mas eles oferecem suporte a atualizações em vez de falhas. Um domínio de atualização é uma unidade lógica de separação de instância que determina quais instâncias em um determinado serviço serão atualizadas em um ponto no tempo. Por padrão, para sua implantação de serviço hospedado, domínios de atualização cinco são definidos. No entanto, você pode alterar esse valor no arquivo de definição de serviço. Por exemplo, suponha que você tem oito instâncias de sua função web. Haverá duas instâncias em três domínios de atualização e duas instâncias em um domínio de atualização. Azure define a sequência de atualização, mas ele se baseia no número de domínios de atualização. Para obter mais informações sobre domínios de atualização, consulte [atualizar um serviço de nuvem](../cloud-services/cloud-services-update-azure-service.md).

###<a name="features-in-other-services"></a>Recursos em outros serviços

Além dos recursos de plataforma que oferecem suporte a disponibilidade de computação de alto, Azure incorpora recursos de alta disponibilidade seus outros serviços. Por exemplo, o armazenamento do Azure mantém três réplicas de todos os dados de fila, tabela e blob. Isso também permite a opção de replicação geográfica para armazenar backups de blobs e tabelas em uma região secundária. A rede de distribuição de conteúdo do Azure permite blobs sejam armazenadas em cache em todo o mundo para redundância e escalabilidade. Banco de dados do SQL Azure mantém várias réplicas também.

Além da série de [orientações técnicas resiliência](https://aka.ms/bctechguide) de artigos, consulte o documento de [Práticas recomendadas para o Design de serviços de em grande escala em serviços de nuvem do Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/) . Esses documentos fornecem uma discussão mais aprofundada dos recursos de disponibilidade da plataforma Windows Azure.

Embora Azure oferece vários recursos que oferecem suporte à alta disponibilidade, é importante entender suas limitações:

- Para computação, Azure garante que as funções estão disponíveis e em execução, mas ele não sabe se o seu aplicativo está em execução ou sobrecarregado.
- Para o banco de dados do SQL Azure, os dados são replicados sincronia dentro da região. Você pode escolher active geográfica replicação, que permite até quatro cópias de banco de dados adicionais na mesma região (ou regiões diferentes). Essas réplicas de banco de dados não são backups no momento. Bancos de dados SQL fornecem recursos de backup no momento. Para saber mais sobre os recursos de banco de dados de SQL no momento, leia [Ponto de banco de dados do SQL Azure em vez de restaurar](https://azure.microsoft.com/blog/azure-sql-database-point-in-time-restore/).
- Para o armazenamento do Azure, dados de tabela e blob são replicados por padrão para uma região alternativa. No entanto, você não consegue acessar as réplicas até Microsoft escolhe alternar para o site alternativo. Houver uma falha de região somente no caso de uma interrupção do serviço de toda a região prolongadas e não há nenhum SLA geográfica failover horário. Também é importante observar que qualquer corrupção de dados rapidamente páginas às réplicas.

Por esses motivos, você deve complementar os recursos de disponibilidade de plataforma com recursos de disponibilidade de aplicativos específicos. Recursos específicos do aplicativo disponibilidade incluem o recurso de instantâneo blob para criar backups point-in-time dos dados blob.

###<a name="availability-sets-for-azure-virtual-machines"></a>Disponibilidade define para máquinas virtuais do Azure

A maioria deste artigo se concentra nos serviços de nuvem, que usam uma plataforma como um modelo de serviço (PaaS). No entanto, também existem recursos de disponibilidade específicos para máquinas virtuais do Azure, que usa uma infraestrutura como um modelo de serviço (IaaS). Para obter alta disponibilidade com máquinas virtuais, você deve usar conjuntos de disponibilidade. Um conjunto de disponibilidade serve uma função semelhante para falhas e atualização de domínios. Dentro de um conjunto de disponibilidade, o Azure posiciona as máquinas virtuais de uma maneira que impede falhas de hardware localizados e atividades de manutenção de desligar a todas as máquinas desse grupo. Conjuntos de disponibilidade são necessárias para obter o SLA do Azure para a disponibilidade de máquinas virtuais.

O diagrama a seguir fornece uma representação de dois conjuntos de disponibilidade que web do grupo e máquinas virtuais de SQL Server, respectivamente.

![Disponibilidade define para máquinas virtuais do Azure](./media/resiliency-high-availability-azure-applications/availability-set-for-azure-virtual-machines.png)

>[AZURE.NOTE] No diagrama anterior, o SQL Server está instalada e em execução em máquinas virtuais. Isso é diferente da discussão anterior do banco de dados do SQL Azure, que fornece um banco de dados como um serviço gerenciado.

##<a name="application-strategies-for-high-availability"></a>Estratégias de aplicativo para alta disponibilidade

A maioria das estratégias de aplicativo para alta disponibilidade envolvem redundância ou a remoção do disco rígidas dependências entre componentes de aplicativo. Design de aplicativo deve oferecer suporte a tolerância durante o tempo de inatividade esporádico do Azure ou serviços de terceiros. As seções a seguir descrevem os padrões de aplicativos para aumentar a disponibilidade dos seus serviços de nuvem.

###<a name="asynchronous-communication-and-durable-queues"></a>Comunicação assíncrona e filas duráveis

Considere a possibilidade de comunicação assíncrona entre serviços flexíveis para aumentar a disponibilidade de aplicativos do Azure. Nesse padrão, escreva mensagens para filas de armazenamento ou filas de barramento de serviço do Azure para processamento posterior. Quando você escrever a mensagem para a fila, controle imediatamente retorna ao remetente da mensagem. Outra camada do aplicativo trata a mensagem processamento, normalmente implementado como uma função de trabalho. Se a função de trabalho falhar, as mensagens são acumulados na fila até que o serviço de processamento será restaurado. Desde que a fila está disponível, não há nenhuma dependência direta entre o remetente front-end e o processador de mensagens. Isso elimina o requisito de chamadas de serviço síncrono que pode ser um gargalo em aplicativos distribuídos.

Uma variação desta usa armazenamento do Azure (blobs, tabelas, filas) ou filas de barramento de serviço como um local de failover para chamadas de banco de dados com falha. Por exemplo, uma chamada síncrona dentro de um aplicativo para outro serviço (como Azure SQL Database) falha repetidamente. Você talvez consiga serializar dados em armazenamento durável. Posteriormente quando o serviço ou banco de dados estiver online novamente, o aplicativo pode enviar novamente a solicitação de armazenamento. A diferença nesse modelo é que o local intermediário não é uma parte constante do fluxo de trabalho do aplicativo. Ele é usado apenas em cenários de falha.

Em ambos os cenários, comunicação assíncrona e armazenamento intermediário impedem um serviço de back-end inativo desativação todo o aplicativo. Filas servem como um intermediário lógico. Para obter mais orientações sobre como escolher o serviço de fila correto, consulte [filas Azure e barramento de serviço do Azure – comparados e comparados](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md).

###<a name="fault-detection-and-retry-logic"></a>Lógica de detecção e repetir de falhas

Um ponto importante no design de aplicativo altamente disponíveis é usar a lógica de repetição dentro de código para lidar com um serviço que está temporariamente para baixo. O [Bloco de aplicativo de tratamento de falhas temporárias](https://msdn.microsoft.com/library/hh680934.aspx), desenvolvido pela equipe do Microsoft Patterns and Practices, ajuda os desenvolvedores de aplicativo nesse processo. A palavra "transitório" significa uma condição temporária que dura apenas por um período relativamente curto. No contexto deste artigo, manipulando falhas temporárias é parte do desenvolvimento de um aplicativo altamente disponível. Exemplos de condições temporárias erros de rede intermitente e perda de conexões de banco de dados.

O bloco de aplicativo de tratamento de falhas temporárias é uma maneira simplificada para você manipular falhas dentro de seu código de maneira normal. Você pode usá-lo para melhorar a disponibilidade de seus aplicativos, adicionando robusta lógica de manipulação de falhas temporária. Na maioria dos casos, a lógica de repetição manipula breve interrupção e reconecta o remetente e o receptor após uma ou mais tentativas. Uma tentativa de repetição bem-sucedida normalmente vai percebida para usuários do aplicativo.

Os desenvolvedores têm três opções para gerenciar seu lógica de repetição: intervalo incremental, fixo e exponenciais. Aguarda incremental mais antes cada repetição de uma maneira linear crescente (por exemplo, 1, 2, 3 e 4 segundos). Intervalo fixo aguarda a mesma quantidade de tempo entre cada tentativa (por exemplo, 2 segundos). Para uma opção mais aleatória, a retirada exponencial aguarda mais entre as tentativas. No entanto, ele usa comportamento exponencial (por exemplo, 2, 4, 8 e 16 segundos).

A estratégia de alto nível dentro de seu código é:

1. Defina sua estratégia de repetição e a política.
1. Tente a operação que pode resultar em uma falha de temporária.
1. Se ocorre falha temporária, invoca a política de repetição.
1. Se todas as repetições falharem, capture uma exceção final.

Teste sua lógica de repetição em falhas simuladas para garantir que as tentativas em operações sucessivas não resultar em um atraso longo inesperado. Faça isso antes de decidir falha a tarefa geral.

###<a name="reference-data-pattern-for-high-availability"></a>Padrão de dados de referência de alta disponibilidade

Dados de referência são os dados de somente leitura de um aplicativo. Estes dados fornecem o contexto de negócios dentro do qual o aplicativo gera dados de transações durante o curso de uma operação de negócios. Dados de transações são uma função no momento os dados de referência. Portanto, sua integridade depende o instantâneo dos dados de referência no momento da transação. Esta é uma definição razoavelmente ampliada, mas ele deve ser suficiente para nosso objetivo aqui.

Dados de referência no contexto de um aplicativo são necessários para o funcionamento do aplicativo. Os respectivos aplicativos criar e manter dados de referência; sistemas de gerenciamento (MDM) de dados mestres geralmente executam esta função. Esses sistemas são responsáveis pelo ciclo de vida dos dados de referência. Catálogo de produtos, funcionário mestre, partes mestre e equipamento master são exemplos de dados de referência. Dados de referência também podem se originar de fora da organização, por exemplo, códigos postais ou taxas de impostos. Estratégias para aumentar a disponibilidade dos dados de referência são normalmente menos difícil daquelas para os dados de transações. Dados de referência tem a vantagem de ser principalmente imutável.

Você pode fazer o Azure funções da web e trabalhador consumam autônomo em tempo de execução de dados de referência implantando os dados de referência junto com o aplicativo. Se o tamanho do armazenamento local permite que tal uma implantação, este é um estado ideal. Bancos de dados incorporados (SQL, NoSQL) ou arquivos XML implantados em um sistema de arquivos local ajudarão a autonomia de unidades de escala de computação Azure. No entanto, você deve ter um mecanismo para atualizar os dados em cada função sem a necessidade de reimplantação. Para fazer isso, coloque todas as atualizações para os dados de referência para um ponto de extremidade de armazenamento de nuvem (por exemplo, o armazenamento de Blob do Azure ou o banco de dados SQL). Adicione código para cada função que baixa as atualizações de dados para os nós de computação na inicialização de função. Como alternativa, adicione código que permite que um administrador executar um download forçado em instâncias de função.

Para aumentar a disponibilidade, as funções também devem conter um conjunto de dados de referência no caso de armazenamento para baixo. Isso permite que as funções iniciar com um conjunto básico de dados de referência até que o recurso de armazenamento fica disponível para as atualizações.

![Alta disponibilidade de aplicativos por meio de nós de computação autônomo](./media/resiliency-high-availability-azure-applications/application-high-availability-through-autonomous-compute-nodes.png)

Uma consideração para esse padrão é a implantação e a velocidade de inicialização para suas funções. Se você estiver implantando ou baixando grandes quantidades de dados de referência na inicialização, isso pode aumentar a quantidade de tempo que leva para girar novas implantações ou instâncias de função. Isso pode ser uma opção aceitável em troca para a autonomia de ter os dados de referência imediatamente disponíveis em cada função em vez de dependendo de serviços de armazenamento externo.

###<a name="transactional-data-pattern-for-high-availability"></a>Padrão de dados de transações de alta disponibilidade

Dados de transações são os dados que o aplicativo gera em um contexto de negócios. Dados de transações são uma combinação do conjunto de processos de negócios que implementa o aplicativo e os dados de referência que ofereça suporte a esses processos. Exemplos de dados de transações podem incluir pedidos, avisos de remessas avançados, faturas e oportunidades de CRM (gerenciamento) de relação de clientes. Os dados de transações gerados, portanto, serão inseridos com sistemas externos do registro mantendo ou para processamento posterior.

Tenha em mente que fazem referência a dados podem alterar nos sistemas que são responsáveis por esses dados. Por esse motivo, dados de transações devem salvar o contexto de dados de referência no momento para que ele tenha mínimo dependências externas sua consistência semântico. Por exemplo, considere a remoção de um produto do catálogo alguns meses após uma ordem for executada. A prática recomendada é incorporar tantos contexto de dados de referência como viável a transação. Isso preserva a semântica associada à transação, mesmo se os dados de referência forem alterados após a transação é capturada.

Conforme mencionado anteriormente, o arquiteturas que usam acoplamento e comunicação assíncrona abrem-se para níveis superiores de disponibilidade. Isso se aplica a dados de transações também, mas a implementação é mais complexa. Noções transações tradicionais geralmente contam com o banco de dados para garantir a transação. Quando você introduz camadas intermediárias, o código do aplicativo corretamente deve manipular os dados em várias camadas para garantir a consistência e durabilidade suficientes.

A sequência a seguir descreve um fluxo de trabalho que separa a captura de dados de transações de seu processamento:

1. Nó de computação Web: presente fazem referência aos dados.
1. Armazenamento externo: salvar dados de transações intermediários.
1. Nó de computação Web: concluir a transação do usuário final.
1. Nó de computação Web: enviar os dados de transações concluídos, juntamente com o contexto de dados de referência, para armazenamento durável temporário que é garantir que dê uma resposta previsível.
1. Nó de computação Web: sinalizar a conclusão de usuário final da transação.
1. Nó de computação de plano de fundo: extrair os dados de transações, pós-processar-se necessário e enviá-lo para seu local de armazenamento final no sistema atual.

O diagrama a seguir mostra uma implementação possível desse design em um serviço de nuvem hospedados Azure.

![Alta disponibilidade por meio de acoplamento](./media/resiliency-disaster-recovery-high-availability-azure-applications/application-high-availability-through-loose-coupling.png)

As setas tracejadas no diagrama anterior indicam processamento assíncrono. A função web front-end não está ciente desse processamento assíncrono. Isso leva para o armazenamento da transação ao seu destino final com referência do sistema atual. Devido a latência que apresenta este modelo assíncrono, os dados de transações não está imediatamente disponíveis para consulta. Portanto, cada unidade dos dados da transação precisa ser salvo em um cache ou precisa de uma sessão de usuário para atender a IU imediata.

A função web é autônoma do restante da infraestrutura. Seu perfil de disponibilidade é uma combinação da função da web e fila do Azure e não a infraestrutura inteira. Além de alta disponibilidade, essa abordagem permite a função web dimensionar horizontalmente, independentemente do armazenamento de back-end. Este modelo de alta disponibilidade pode ter um impacto sobre a economia de operações. Componentes adicionais como filas Azure e funções de trabalho podem afetar custos mensais de uso.

Observe que o diagrama anterior mostra uma implementação dessa abordagem desacoplado para dados de transações. Há muitas outras implementações possíveis. A lista a seguir fornece algumas alternativas:

 * Uma função de trabalho pode ser colocada entre a função da web e a fila do armazenamento.
 * Uma fila de barramento de serviço pode ser usada em vez de uma fila de armazenamento do Azure.
 * O destino final pode ser armazenamento Azure ou um provedor de banco de dados diferente.
 * Cache Azure pode ser usado na camada da web para fornecer os requisitos de cache imediatos após a transação.

###<a name="scalability-patterns"></a>Padrões de escalabilidade

É importante observar que a escalabilidade do serviço de nuvem diretamente afeta a disponibilidade. Se o aumento de carga faz com que seu serviço de resposta, a impressão de usuário é o aplicativo para baixo. Siga as práticas recomendadas para escalabilidade com base em sua carga de aplicativo esperado e expectativas futuras. A escala de alta envolve várias considerações, como o uso de única versus várias contas de armazenamento, compartilhamento entre vários bancos de dados e estratégias de cache. Para uma análise aprofundada desses padrões, consulte [Práticas recomendadas para o Design de serviços de em grande escala em serviços de nuvem do Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/).

##<a name="next-steps"></a>Próximas etapas

Este artigo faz parte de uma série de artigos voltada para [recuperação de dados e alta disponibilidade em aplicativos criados no Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). Próximo artigo desta série é [recuperação de dados em aplicativos criados no Microsoft Azure](./resiliency-disaster-recovery-azure-applications.md).
