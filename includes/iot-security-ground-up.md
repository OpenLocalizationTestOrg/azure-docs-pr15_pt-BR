# <a name="internet-of-things-security-from-the-ground-up"></a>Segurança da Internet das coisas desde o início

Internet das coisas (IoT) apresenta desafios exclusivos de conformidade, privacidade e segurança para empresas em todo o mundo. Ao contrário da tecnologia de virtuais tradicional onde esses problemas girarem em torno de softwares e como ela é implementada, IoT diz respeito ao que acontece quando o virtuais e os mundos físicos convergem. Proteger soluções IoT, é necessário garantir provisionamento segura de dispositivos, conectividade segura entre esses dispositivos e a nuvem e proteção de dados seguro na nuvem durante o processamento e armazenamento. Trabalhando em relação a funcionalidade, entretanto, são dispositivos com recursos limitados, distribuição geográfica de implantações e um grande número de dispositivos dentro de uma solução.

Este artigo explora como o Microsoft Azure IoT Suite fornece uma solução de nuvem de Internet das coisas segura e privada. O pacote do Windows Azure IoT oferece uma solução completa de ponta a ponta, com segurança incorporada em cada estágio de ponta a ponta para cima. Na Microsoft, desenvolvimento de software seguro é parte da prática engenharia de software, como raiz em nossas décadas experiência de tempo de desenvolvimento de software seguro. Para garantir isso, o ciclo de vida SDL (Security Development) é a metodologia de desenvolvimento fundamentais, juntamente com uma ampla gama de serviços de segurança em nível de infra-estrutura, como garantia de segurança operacionais (OSA) e a unidade de Crimes digitais da Microsoft, Microsoft Security Response Center e Microsoft Malware Protection Center. 

O pacote do Windows Azure IoT oferece recursos exclusivos que quais tornar provisionamento, conectando-se a e armazenando dados de dispositivos IoT fácil e transparentes e, a maioria das all, seguro. Neste documento examinamos os recursos de segurança do Windows Azure IoT Suite e estratégias de implantação para garantir a segurança, privacidade e desafios de conformidade são tratadas. 

## <a name="introduction"></a>Introdução

Internet das coisas (IoT) é a onda do futuro, oferecendo empresas imediatas e reais oportunidades para reduzir os custos aumentam a receita e transformar seus negócios. No entanto, muitas empresas, são hesitar em implantar IoT em suas organizações devido a preocupações de segurança, privacidade e conformidade. Um ponto principal preocupação proveniente a exclusividade da infra-estrutura IoT, que combina o mundos físicos e virtuais juntas, individuais riscos inerentes esses dois mundos de composição. Segurança do IoT pertence a garantir a integridade do código sendo executado em dispositivos, fornecendo a autenticação de usuário e de dispositivo, definindo propriedade clara de dispositivos (bem como os dados gerados por esses dispositivos) e sendo resiliente a ataques de físicos e virtuais. 

Em seguida, há o problema de privacidade. As empresas querem transparência referentes ao conjunto de dados, no qual está sendo coletado e por quê, que possam vê-lo, que controla o acesso e assim por diante. Finalmente, há problemas gerais de segurança do equipamento, juntamente com as pessoas operacional-los e problemas de manutenção de padrões do setor de conformidade.

Dado o segurança, privacidade, transparência e preocupações de conformidade, escolher o provedor de soluções IoT direita permanece um desafio. Costura juntos partes individuais do IoT software e serviços fornecidos por uma variedade de fornecedores apresenta lacunas de conformidade que pode ser difíceis de detectar, quanto mais corrigir, transparência, privacidade e segurança. A opção do direito IoT provedor de serviço e de software se baseia em Localizando provedores que têm grande experiência executando serviços que atravessam segmentos verticais e regiões geográficas, mas também são podendo ser dimensionado de forma segura e transparente. Da mesma forma, é útil para o provedor selecionado ter décadas de experiência seguro software em execução no bilhões de máquinas no mundo todo para desenvolver e ter a capacidade de avaliar o cenário de ameaças trazido por este novo mundo do Internet das coisas.

## <a name="secure-infrastructure-from-the-ground-up"></a>Infra-estrutura segura desde o início 

A infraestrutura de [Nuvem da Microsoft](https://www.microsoft.com/enterprise/microsoftcloud/default.aspx#fbid=WzBsRQi6aGk) oferece suporte a mais de um bilhão de clientes em 127 países. Desenho em nossa experiência décadas-long construir o software enterprise e executar alguns dos serviços online maiores no mundo, fornecemos níveis mais altos de ameaça, privacidade, conformidade e segurança reforçada práticas de redução de risco que a maioria dos clientes poderia obter por conta própria.

Nosso [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/) fornece um processo de desenvolvimento de toda a empresa obrigatório que incorpora os requisitos de segurança para o ciclo de vida do software inteira. Para ajudar a garantir que as atividades operacionais seguirão o mesmo nível de práticas recomendadas de segurança, usamos as diretrizes de segurança rigoroso dispostas em nosso processo de garantia de segurança operacionais (OSA). Além disso, trabalhamos com empresas de auditoria de terceiros para que possamos atender nossas obrigações de conformidade e contamos com a participação em esforços amplo segurança através da criação de centros de excelência, incluindo o Microsoft Digital Crimes unidade, Microsoft Security Response Center e Microsoft Malware Protection Center uma verificação em andamento.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure - infra-estrutura segura de IoT pela sua empresa

Microsoft Azure oferece uma solução completa de nuvem, que combina uma coleção constantemente crescente de serviços de nuvem integrada — análise, aprendizado de máquina, armazenamento, segurança, rede e web — com um compromisso de líder do setor para a proteção e a privacidade de seus dados. Nossa estratégia [pressupõem violação](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) usa uma equipe"vermelha" dedicada de especialistas de segurança de software que simular ataques, testar a capacidade do Azure para detectar, proteger contra ameaças emergentes e recuperar contra violações. Nossa equipe de [resposta a incidentes global](https://www.microsoft.com/TrustCenter/Security/DesignOpSecurity) ininterruptamente funciona para atenuar os efeitos da ataques e atividades maliciosas. A equipe segue estabelecidos procedimentos de recuperação, comunicação e gerenciamento de incidentes e usa interfaces detectáveis e previsíveis com parceiros internos e externos.

Nossos sistemas fornecem detecção de invasão contínua e prevenção, prevenção do ataque de serviço, os testes de invasão regular e jurídicas ferramentas que ajudam a identificar e atenuar as ameaças. [A autenticação multifator](../articles/multi-factor-authentication/multi-factor-authentication.md) fornece uma camada adicional de segurança para usuários finais acessar a rede. E para o aplicativo e o provedor de host, oferecemos controle de acesso, monitoramento, antimalware, verificação de vulnerabilidade, patches e gerenciamento de configuração.

O Microsoft Azure IoT Suite aproveita a segurança e privacidade construídas na plataforma Windows Azure, juntamente com nossos processos SDL e OSA para desenvolvimento seguro e a operação de software da Microsoft. Estes procedimentos fornecem a proteção da infra-estrutura, a proteção de rede e recursos de gerenciamento de identidades e fundamentais para a segurança de qualquer solução. 

O [Hub do Azure IoT](../articles/iot-hub/iot-hub-what-is-iot-hub.md) dentro do [Pacote de IoT](../articles/iot-suite/iot-suite-what-is-azure-iot.md) oferece um serviço totalmente gerenciado que permite a comunicação de bidirecional segura e confiável entre IoT dispositivos e serviços do Azure como [Aprendizado de máquina do Windows Azure](../articles/machine-learning/machine-learning-what-is-machine-learning.md) e o [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) usando credenciais de segurança de cada dispositivo e controle de acesso.  

Para comunicar melhor os recursos de segurança e privacidade incorporados do pacote do Windows Azure IoT, podemos ter decompostas do pacote em três áreas de segurança principal. 

![Azure IoT Suite](media/iot-security-ground-up/securing-iot-ground-up-fig2.png)

### <a name="secure-device-provisioning-and-authentication"></a>Provisionamento de dispositivo seguro e autenticação

O pacote do Windows Azure IoT protege dispositivos enquanto eles estão check-out no campo, fornecendo uma chave de identidade exclusiva para cada dispositivo, que pode ser usado pela infraestrutura de IoT para se comunicar com o dispositivo enquanto ele está em operação. O processo é rápido e fácil de instalação. A chave gerada com uma ID do dispositivo selecionado pelo usuário constitui a base de um token usado em toda a comunicação entre o dispositivo e o Hub de IoT do Azure.

IDs de dispositivo podem ser associados um dispositivo durante a fabricação (isto é atualizada em um módulo de confiança de hardware) ou podem usar a fixa identidade como um proxy (por exemplo, números de série da CPU) existente. Como alterar essa informação de identificação do dispositivo não é simple, é importante apresentar IDs de dispositivo lógico, caso o dispositivo lógico, mas as alterações de hardware do dispositivo subjacente permanece o mesmo. Em alguns casos, a associação de uma identidade do dispositivo pode acontecer no momento da implantação de dispositivo (ou seja, um engenheiro de campo autenticado fisicamente configura um novo dispositivo durante a comunicação com o back-end de solução IoT). O [registro de identidade do Windows Azure IoT Hub](../articles/iot-hub/iot-hub-devguide.md) fornece armazenamento seguro de identidades de dispositivo e chaves de segurança para uma solução. Individuais ou grupos de identidades de dispositivo podem ser adicionados a uma lista de permissões ou uma lista de bloqueios, habilitando o controle completo sobre o acesso ao dispositivo.
 
Azure diretivas de controle de acesso de IoT Hub na nuvem habilitar ativação e desativação de qualquer dispositivo identidade, fornecendo uma maneira para desassociar um dispositivo de uma implantação IoT quando necessário. Essa associação e desassociação de dispositivos se baseia em cada identidade do dispositivo.

Recursos de segurança de dispositivo adicionais incluem o seguinte:

- Dispositivos não aceitar conexões de rede não solicitadas. Eles estabelecer todas as conexões e rotas de maneira somente de saída. Para um dispositivo receber um comando de back-end, o dispositivo deve iniciar uma conexão para verificar se há qualquer comando pendente processar. Depois que uma conexão entre o dispositivo e o IoT Hub é estabelecida com segurança, mensagens da nuvem para o dispositivo e o dispositivo para a nuvem pode ser enviado transparente.  
- Dispositivos somente se conectar ao ou estabelecer rotas para serviços conhecidos com os quais são peered, como um Hub de IoT do Azure.
- Autenticação e autorização de nível de sistema usam identidades por dispositivo, tornando credenciais de acesso e permissões perto-revogável instantaneamente.

### <a name="secure-connectivity"></a>Conectividade segura 

Durabilidade de mensagens é um recurso importante de qualquer solução IoT. A necessidade de permanentemente entregar comandos e/ou receber dados de dispositivos está sublinhada pelo fato de que IoT dispositivos conectados pela Internet ou outras redes semelhantes que podem ser confiáveis. Hub do Azure IoT oferece durabilidade de mensagens entre a nuvem e dispositivos através de um sistema de confirmações em resposta às mensagens. Durabilidade adicional para mensagens é obtida com o cache de mensagens no Hub do IoT para até sete dias de telemetria e dois dias de comandos.
 
Eficiência é importante para garantir conservação dos recursos e a operação em um ambiente com recursos limitados. HTTPS (HTTP seguro,) a versão segura de padrão do setor do protocolo http populares, é compatível com o Windows Azure IoT Hub, habilitando a comunicação eficiente. Protocolo de enfileiramento de mensagem (AMQP) e a mensagem enfileiramento telemetria transporte (MQTT), suportados pelo Hub do Azure IoT, avançadas destinam-se não apenas para eficiência em termos de uso de recursos, mas também a entrega de mensagem confiável. 

Escalabilidade requer a capacidade de forma segura interoperar com uma ampla gama de dispositivos. Hub do Azure IoT permite conexão segura para os dispositivos habilitados para IP tanto não habilitados para IP. Dispositivos habilitados para IP são capazes de se conectar diretamente e se comunicar com o IoT Hub através de uma conexão segura. Dispositivos habilitados para IP não são com recursos limitados e conecte-se somente sobre protocolos de comunicação de distância curta, como Zwave, ZigBee e Bluetooth. Um gateway de campo é usado para agregar esses dispositivos e executa a conversão de protocolo para permitir uma comunicação bidirecional segura com a nuvem.

Recursos de segurança de conexão adicionais incluem o seguinte:

- O caminho de comunicação entre dispositivos e Azure IoT Hub ou entre gateways e o Hub do Azure IoT, está protegido usando a segurança de camada de transporte (TLS) padrão do setor com Hub do Azure IoT autenticado usando o protocolo de x. 509.
- Para proteger os dispositivos de conexões de entrada não solicitadas, Azure IoT Hub não abre qualquer conexão ao dispositivo. O dispositivo inicia todas as conexões. 
- Hub do Azure IoT permanentemente armazena mensagens para dispositivos e aguardará para se conectar ao dispositivo. Esses comandos são armazenados por dois dias, permitindo que os dispositivos que se conectam esporadicamente, devido a preocupações de energia ou conectividade, para receber esses comandos. Hub do Azure IoT mantém uma fila de cada dispositivo para cada dispositivo.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Seguro processamento e armazenamento na nuvem 

De comunicações criptografadas de processamento de dados na nuvem, o pacote do Windows Azure IoT ajuda a manter dados seguros. Ele oferece flexibilidade para implementar adicionais de criptografia e o gerenciamento de chaves de segurança. Usando o Windows Azure Active Directory (AAD) para autenticação de usuário e a autorização, Azure IoT Suite poderá fornecer um modelo de autorização baseada em política para dados na nuvem, habilitando o gerenciamento de acesso fácil que pode ser auditado e revisado. Este modelo também permite que quase instantânea revogação de acesso a dados na nuvem e de dispositivos conectados ao Azure IoT Suite.

Estando na nuvem, dados podem ser processado e armazenado em qualquer fluxo de trabalho definidos pelo usuário. Acesso a cada parte dos dados é controlado com o Windows Azure Active Directory, dependendo do serviço de armazenamento usado.
   
Todas as chaves usadas pela infraestrutura de IoT são armazenadas na nuvem em armazenamento seguro, com a capacidade de passar no caso de chaves precisam ser provisionados novamente. Dados podem ser armazenados em [DocumentDB](../articles/documentdb/documentdb-introduction.md) ou em [bancos de dados do SQL](../articles/sql-database/sql-database-faq.md), permitindo a definição do nível de segurança desejado. Além disso, o Windows Azure fornece uma maneira para monitorar e auditar todo o acesso aos seus dados para alertá-lo de qualquer invasão ou acesso não autorizado.

## <a name="conclusion"></a>Conclusão

Internet das coisas começa com suas coisas — as coisas mais importantes para empresas. IoT pode oferecer valor incrível para uma empresa reduzindo custos, aumentando a receita e transformando corporativos. Sucesso dessa transformação depende amplamente escolhendo o provedor de serviço e o software de IoT à direita. Isso significa que a localização de um provedor que não apenas catalyzes essa transformação por Compreendendo e necessidades comerciais, mas também fornece serviços e software criadas com segurança, privacidade, transparência e conformidade como considerações de design principal. A Microsoft tem extensa experiência com desenvolvendo e implantando serviços e software seguro e continua a ser um líder nesta nova era da Internet das coisas. 

O Microsoft Azure IoT Suite incorpora as medidas de segurança por design, habilitar monitoramento seguro dos ativos para melhorar a eficiência e desempenho operacional da unidade para habilitar a inovação e empregar a análise de dados avançada para transformar as empresas. Com sua abordagem em camadas na direção de segurança, vários recursos de segurança e padrões de design, o Azure IoT Suite ajuda a implantar uma infraestrutura que pode ser confiável para transformar qualquer negócio. 

## <a name="additional-information"></a>Informações adicionais

Cada solução pré-configurada do Azure IoT Suite cria instâncias de serviços do Azure, como o seguinte:

- [**Hub do Azure IoT**](https://azure.microsoft.com/services/iot-hub/): seu gateway que se conecta a nuvem para "coisas". Você pode dimensionar a milhões de conexões por processo e hub grandes volumes de dados com suporte de autenticação por dispositivo ajudá-lo a proteger sua solução.
- [**DocumentDB do Windows Azure**](https://azure.microsoft.com/services/documentdb/): um serviço de banco de dados escalável, totalmente indexados para dados semi-estruturados que gerencia os metadados para os dispositivos que você provisionar, como as propriedades de segurança, configuração e atributos. DocumentDB oferece o processamento de alto desempenho e alta taxa de transferência, não reconhece o esquema de indexação de dados e uma interface de consulta avançada do SQL.
- [**Análise de fluxo do Azure**](https://azure.microsoft.com/services/stream-analytics/): em tempo real fluxo de processamento na nuvem que permite a você desenvolver e implantar uma solução de análise de baixo custo para descobrir em tempo real ideias de dispositivos, sensores, infraestrutura e aplicativos rapidamente. Os dados desse serviço totalmente gerenciado podem ser dimensionados para qualquer volume ao mesmo tempo, resiliência, baixa latência e alta taxa de transferência.
- [**Serviços de aplicativo do Azure**](https://azure.microsoft.com/services/app-service/): uma plataforma de nuvem para criar poderosa web e aplicativos móveis que se conectam aos dados em qualquer lugar; na nuvem ou no local. Crie aplicativos móveis para iOS, Android e Windows o envolvimento. Integre seu Software como um serviço (SaaS) e aplicativos corporativos com conectividade imediata de dezenas de serviços em nuvem e aplicativos empresariais. Código no IDE e de idioma favorito — .NET, NodeJS, PHP, Python ou Java — para criar aplicativos da web e APIs mais rapidamente.
- [**Lógica de aplicativos**](https://azure.microsoft.com/services/app-service/logic/): recurso de aplicativos da lógica do serviço de aplicativo do Windows Azure ajuda integrar sua solução IoT para sua linha existente de sistemas comerciais e automatizar os processos de fluxo de trabalho. Lógica de aplicativos permite aos desenvolvedores de fluxos de trabalho de design que inicia a partir de um gatilho e, em seguida, executar uma série de etapas — regras e ações que usam conectores poderosas de integrar com seus processos de negócios. Lógica Apps oferece imediata de conectividade para um grande ecossistema de SaaS, baseadas em nuvem e aplicativos no local.
- [**Armazenamento de blob do Azure**](https://azure.microsoft.com/services/storage/): armazenamento na nuvem confiável e econômica para os dados que seus dispositivos de enviar para a nuvem.
