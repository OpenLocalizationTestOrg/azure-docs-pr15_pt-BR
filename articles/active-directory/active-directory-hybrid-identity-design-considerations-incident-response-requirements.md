
<properties
    pageTitle="Azure Active Directory híbrido identidade considerações de design - determinar requisitos de incidente rResponse | Requisitos do Microsoft Azure "
    description="Determinar os recursos de monitoramento e relatórios para a solução de identidade híbrido que podem ser aproveitados por IT para realizar ações para identificar e reduzir um ameaças potenciais"
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Determinar os requisitos de resposta a incidente para sua solução de identidade híbrido

Empresas de médio ou grandes provavelmente terá uma [resposta de incidente de segurança](https://technet.microsoft.com/library/cc700825.aspx) para ajudar IT realizar ações adequadamente para o nível de incidente. O sistema de gerenciamento de identidade é um componente importante do processo de resposta a incidente porque ele pode ser usado para ajudar a identificar quem executou uma ação específica em relação ao destino. A solução de identidade híbrido deve ser capaz de fornecer os recursos de monitoramento e relatórios que podem ser aproveitados por IT para realizar ações para identificar e reduzir as ameaças possíveis. Em um plano de resposta de incidente típico, você terá as fases a seguir como parte do plano:

1.  Avaliação inicial.
2.  Comunicação incidente.
3.  Controle de danos e redução de riscos.
4.  Identificação do que era compromisso e gravidade.
5.  Preservação de evidências.
6.  Notificação para pessoas apropriadas.
7.  Recuperação do sistema.
8.  Documentação.
9.  Avaliação de danos e o custo.
10. Revisão de processo e plano.

Durante a identificação do que era compromisso e gravidade fases, será necessário identificar os sistemas que foram comprometidos, arquivos que foram acessados e determinam a confidencialidade desses arquivos. Seu sistema de identidade híbrido deve ser capaz de atender a esses requisitos para ajudá-lo identifica o usuário que fez essas alterações. 

## <a name="monitoring-and-reporting"></a>Monitoramento e relatórios
Quantas vezes o sistema de identidade também pode ajudar na fase de avaliação inicial principalmente se o sistema foi construído na auditoria e recursos de relatórios. Durante a avaliação inicial, administrador de TI deve ser capaz de identificar uma atividade suspeita ou o sistema deve ser capaz de disparador automaticamente com base em uma tarefa pré-configurado. Várias atividades podem indicar um possível ataque, no entanto, em outros casos, um sistema mal configurado pode levar a um número de falsos positivos em um sistema de detecção de intrusos. 

O sistema de gerenciamento de identidade deve ajudar que os administradores identificar e relatar essas atividades suspeitas. Geralmente esses requisitos técnicos podem ser executados por todos os sistemas de monitoramento e ter um recurso de relatórios que pode realçar possíveis ameaças. Use as perguntas abaixo para ajudá-lo a sua solução de identidade híbrido levando em requisitos de resposta a incidente considerações de design:

- Sua empresa tem uma resposta de incidente de segurança no lugar?
 - Se escolher Sim, é o sistema de gerenciamento de identidade atual usado como parte do processo?
- Sua empresa precisa identificar tentativas de logon suspeitas de usuários em diferentes dispositivos?
- Sua empresa precisa detectar possíveis comprometido as credenciais de usuário?
- Sua empresa precisa auditar acesso do usuário e a ação?
- Sua empresa precisa saber quando um usuário redefinir sua senha?

## <a name="policy-enforcement"></a>Aplicação da política

Durante o controle de danos e fase de redução de riscos, é importante reduzir os efeitos reais e possíveis de um ataque rapidamente. Neste ponto, essa ação que você terá pode fazer a diferença entre a menor e maior. A resposta exata dependerá de sua organização e a natureza do ataque que você enfrenta. Se a avaliação inicial concluiu que uma conta foi comprometida, você precisará impor diretiva para bloquear a esta conta. Isso é apenas um exemplo onde o sistema de gerenciamento de identidade será aproveitado. Use as perguntas abaixo para ajudá-lo a projetar sua solução de identidade híbrido levando em consideração como políticas serão impostas para reagir a um incidente em andamento:

- Sua empresa tem políticas in-loco para bloquear usuários do access a rede se for necessário?
 - Se escolher Sim, o solução atual integrar com o sistema de gerenciamento de identidade híbrido que você vai adotar?
- Sua empresa precisa impor condicional acesso dos usuários que estão em quarentena? 
 
>[AZURE.NOTE]
Certifique-se de fazer anotações de cada resposta e entender racional a resposta. [Definir estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) irão sobre as opções disponíveis e vantagens/desvantagens de cada opção.  Por ter respondidas essas perguntas que você selecionará a opção que melhor adequado para seu negócio precisa.

## <a name="next-steps"></a>Próximas etapas
[Definir estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Consulte também
[Visão geral de considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)
