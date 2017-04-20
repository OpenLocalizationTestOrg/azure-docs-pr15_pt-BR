# <a name="internet-of-things-security-best-practices"></a>Práticas recomendadas de segurança Internet das coisas

Para proteger uma Internet das coisas (IoT) a infra-estrutura exige uma estratégia de segurança-em-profundidade rigorosa. Essa estratégia exige a proteger os dados na nuvem, proteja a integridade de dados em trânsito na Internet pública e dispositivos de provisão de forma segura. Cada camada compilações maior garantia de segurança na infra-estrutura geral.

## <a name="secure-an-iot-infrastructure"></a>Proteger uma infraestrutura IoT

Essa estratégia de segurança em camadas pode ser desenvolvida e podem ser executada com participação ativa de vários participantes envolvidos com a fabricação, desenvolvimento e implantação de dispositivos IoT e infra-estrutura. A seguir é uma descrição detalhada desses participantes.  

- **Hardware de IoT fabricante/integrador**: geralmente, esses são os fabricantes de hardware IoT sendo implantado, integradores montando hardware de vários fabricantes ou fornecedores de fornecimento de hardware para uma implantação IoT fabricado ou integrado de outros fornecedores.
- **Desenvolvedor da solução IoT**: O desenvolvimento de uma solução IoT é normalmente feito por um desenvolvedor da solução. Esse desenvolvedor talvez seja parte de uma equipe interna ou um integrador (SI) especializado nesta atividade. O desenvolvedor da solução IoT pode desenvolver vários componentes da solução IoT desde o início, integrar vários componentes disponíveis no mercado ou de código-fonte aberto ou adotar soluções pré-configurado com adaptação secundária.
- **IoT implantador de solução**: solução após uma IoT foi desenvolvida, ele precisa ser implantado no campo. Isso envolve a implantação de hardware, interconexão de dispositivos e a implantação de soluções nos dispositivos de hardware ou na nuvem.
- **Operador de solução IoT**: após IoT a solução será implantada, ele requer operações de longo prazo, monitoramento, atualizações e manutenção. Isso pode ser feito por uma equipe interna que compreende especialistas em tecnologia de informação, operações de hardware e equipes de manutenção e especialistas de domínio que monitoram o comportamento correto da infra-estrutura de IoT geral.

As seções a seguir fornecem as práticas recomendadas para cada um desses players para ajudar a desenvolver, implantar e operar uma infra-estrutura segura de IoT.

## <a name="iot-hardware-manufacturerintegrator"></a>Hardware de IoT fabricante/integrador

A seguir estão as práticas recomendadas para IoT fabricantes de hardware e integradores de hardware.

- **Hardware de escopo para requisitos mínimos**: O design de hardware deve incluir os recursos mínimos necessários para a operação de hardware e nada mais. Um exemplo é incluir portas USB apenas se necessário para a operação do dispositivo. Estes recursos adicionais abra o dispositivo para ataques indesejadas que deve ser evitada.
- **Verifique o hardware à prova de violação**: Crie em mecanismos para detectar violações físico, como abertura de capa o dispositivo ou remover uma parte do dispositivo. Esses sinais de violar pode ser parte do fluxo de dados carregado para a nuvem, que poderia operadores desses eventos de alerta.
- **Construir em torno de hardware seguro**: permite que se COGS, criar recursos de segurança como armazenamento seguro e criptografado ou inicializar a funcionalidade baseada em Trusted Platform Module (TPM). Esses recursos tornam mais seguro e ajudam a proteger a infra-estrutura IoT geral de dispositivos.
- **Fazer atualizações seguro**: as atualizações de Firmware durante o ciclo de vida do dispositivo são inevitáveis. Criar dispositivos com caminhos seguros para atualizações e criptográfica assurance das versões de firmware permitirá que o dispositivo ser seguro durante e após atualizações.

## <a name="iot-solution-developer"></a>IoT desenvolvedor da solução

A seguir estão as práticas recomendadas para desenvolvedores de soluções IoT:

- **Metodologia de desenvolvimento de software seguro Follow**: desenvolvimento de software seguro requer chão-up pensando em segurança, desde à criação do projeto completamente a sua implementação, teste e implantação. As opções de plataformas, linguagens e ferramentas são influenciadas com essa metodologia. Microsoft Security Development Lifecycle fornece uma abordagem passo a passo para o desenvolvimento de software seguro.
- **Escolha o software de código aberto com cuidado**: software do código-fonte aberto oferece uma oportunidade de rapidamente desenvolver soluções. Quando você escolhe o software de código aberto, considere o nível de atividade da comunidade para cada componente do código-fonte aberto. Uma comunidade ativa garante que o software é suportado e que os problemas são descobertos e endereçados. Como alternativa, um software de código aberto obscuro e inativa talvez não tenham suporte e problemas provavelmente não serão descobertos.
- **Integra-se com cuidado**: muitas falhas de segurança do software existirem no limite da bibliotecas e APIs. Funcionalidade que não pode ser necessária para a implantação atual ainda pode estar disponível por meio de uma camada de API. Para garantir a segurança geral, certifique-se de verificar todas as interfaces de componentes sendo integrados para falhas de segurança.      

## <a name="iot-solution-deployer"></a>IoT implantador de solução

A seguir estão as práticas recomendadas para aos distribuidores de solução IoT:

- **Implantar o hardware com segurança**: IoT implantações podem exigir hardware seja implantado em locais não seguros, como em espaços públicos ou localidades sem supervisão. Em tais situações, certifique-se de que a implantação de hardware é à prova o máximo. Se houver USB ou outras portas disponíveis no hardware, certifique-se de que eles são abordados com segurança. Muitos vetores de ataque podem usá-los como pontos de entrada.
- **Mantenha as chaves de autenticação seguros**: durante a implantação, cada dispositivo requer IDs de dispositivo e associados chaves de autenticação geradas pelo serviço de nuvem. Mantenha essas chaves fisicamente seguros mesmo após a implantação. Qualquer chave comprometida pode ser usado por um dispositivo mal-intencionado para se mascarar como um dispositivo existente.

## <a name="iot-solution-operator"></a>Operador de solução IoT

Estas são as práticas recomendadas para os operadores de solução IoT:

- **Manter o sistema atualizado**: Certifique-se de que todos os drivers de dispositivo e sistemas operacionais de dispositivos são atualizados para as versões mais recentes. Se você ativar as atualizações automáticas no Windows 10 (IoT ou outros SKUs), Microsoft mantém ele atualizado, fornecendo um sistema operacional seguro para dispositivos IoT. Mantendo os outros sistemas operacionais (por exemplo, Linux) ajuda a atualizado garantir que eles também estão protegidos contra ataques mal-intencionados.
- **Proteger contra atividades maliciosas**: se permite que o sistema operacional, instale os recursos mais recentes de antivírus e antimalware em cada sistema operacional do dispositivo. Isso pode ajudar a atenuar as ameaças externas mais. Você pode proteger os sistemas operacionais mais modernos contra ameaças, de acordo com as etapas apropriadas.
- **Auditoria com frequência**: auditoria IoT infraestrutura para problemas relacionados a segurança é a chave ao responder a incidentes de segurança. Maioria dos sistemas operacionais fornecem o log de eventos do internas que deve ser revisado com frequência para certificar-se de que nenhuma violação de segurança ocorreu. Informações de auditoria podem ser enviadas como um fluxo de telemetria separado para o serviço de nuvem onde podem ser analisado.
- **Proteja fisicamente a infraestrutura IoT**: os piores ataques de segurança em relação a infraestrutura IoT iniciados usando físico acesso a dispositivos. Uma prática de segurança importantes é proteger contra uso mal-intencionado das portas USB e outro acesso físico. Uma chave para as violações revelando que poderia ter ocorrido está fazendo logon de acesso físico, como o uso da porta USB. Novamente, o Windows 10 (IoT e outros SKUs) habilita o log detalhado desses eventos.
- **Credenciais de nuvem Protect**: usadas para a configuração e a operação de uma implantação IoT de credenciais de autenticação de nuvem são possivelmente a maneira mais fácil para acessar e comprometer um sistema IoT. Proteger as credenciais ao alterar a senha com frequência e evite usando essas credenciais em máquinas públicas.

Recursos dos diferentes dispositivos IoT variam. Alguns dispositivos podem ser computadores que executam sistemas operacionais para desktop comuns e alguns dispositivos podem estar executando sistemas operacionais de desempenho muito leve. Práticas recomendadas de segurança descritas anteriormente podem ser aplicadas a esses dispositivos em vários graus. Se for fornecido, implantação e segurança práticas recomendadas adicionais dos fabricantes desses dispositivos devem ser seguidas.

Alguns dispositivos herdados e restritos podem não ter sido projetados especificamente para implantação IoT. Esses dispositivos podem não têm a capacidade de criptografar dados, conecte-se com a Internet ou fornecer auditoria avançada. Nesses casos, um gateway de campo moderno e seguro pode agregam dados de dispositivos herdados e fornecer a segurança necessária para conectar esses dispositivos pela Internet. Gateways de campo podem fornecer autenticação segura, negociação de sessões criptografadas, recebimento dos comandos de nuvem e muitos outros recursos de segurança.
