<properties
    pageTitle="Azure Active Directory híbrido identidade considerações de design - determinar requisitos de identidade | Microsoft Azure"
    description="Identificar as necessidades de negócios da empresa que conduzirá a definir os requisitos para o design de identidade híbrido."
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

# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Determinar requisitos de identidade da sua solução de identidade híbrido
A primeira etapa em projetar uma solução de identidade híbrida é determinar os requisitos para a organização de negócios que será aproveitando a esta solução.  Identidade híbrido inicia como uma função de suporte (suporte para todas as outras soluções de nuvem fornecendo autenticação) e vai em para fornecem recursos novos e interessantes que desbloquear novas cargas de trabalho para os usuários.  Essas cargas de trabalho ou os serviços que você deseje adotar para seus usuários determinarão os requisitos para o design de identidade híbrido.  Esses serviços e cargas de trabalho precisam aproveitar identidade híbrido ambos os locais e na nuvem.  

É preciso passar sobre esses principais aspectos dos negócios entender o que é um requisito agora e o que a empresa planeja no futuro. Se você não tiver a visibilidade da estratégia de longo prazo para design de identidade híbrido, chances são que sua solução não será scalable conforme as necessidades dos negócios crescer e mudar.   T ele diagrama abaixo mostra um exemplo de uma arquitetura de identidade híbrido e as cargas de trabalho que estão sendo desbloqueadas para os usuários. Este é apenas um exemplo de todas as novas possibilidades que podem ser desbloqueadas e entregue com uma estratégia de identidade híbrido sólida. 
 
Alguns componentes que fazem parte da arquitetura de identidade híbrido![](./media/hybrid-id-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Determinar as necessidades de negócios
Cada empresa terá requisitos diferentes, mesmo se essas empresas fazem parte do setor mesmo, os negócios real requisitos podem variar. Você ainda pode aproveitar as práticas recomendadas do setor, mas basicamente é necessidades de negócios da empresa que conduzirá a definir os requisitos para o design de identidade híbrido. 

Certifique-se de perguntas a seguir para identificar suas necessidades de negócios:

- Sua empresa está procurando cortar custos operacionais de TI?
- Sua empresa está procurando para proteger ativos de nuvem (aplicativos SaaS, infraestrutura)?
- Sua empresa está procurando modernizar seu TI?
  - São seus usuários mais móvel e exigentes criar exceções em sua DMZ para permitir que o tipo diferente de tráfego para acessar recursos diferentes?
  - Sua empresa tem aplicativos herdados que necessárias para ser publicados para esses usuários modernos, mas não são fáceis de regravação?
  - Sua empresa precisa realizar todas essas tarefas e colocá-lo sob controle ao mesmo tempo?
- Sua empresa está procurando para proteger identidades dos usuários e redução de riscos colocando novas ferramentas que utilizam a experiência do Azure segurança especialização local Microsoft?
- Sua empresa está tentando eliminar as contas "externas" temidas no local e movê-los para a nuvem onde elas não forem mais uma ameaças inativas dentro do seu ambiente local?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analisar infraestrutura de identidade de local
Agora que você tem uma ideia sobre suas necessidades de negócios da empresa, você precisa avaliar sua infraestrutura de identidade de local. Essa avaliação é importante para definir os requisitos técnicos para integrar sua solução de identidade atual para o sistema de gerenciamento de identidade de nuvem. Certifique-se de responder às seguintes perguntas:

- Qual solução de autenticação e a autorização faz sua empresa usar local? 
- Sua empresa atualmente tem quaisquer serviços de sincronização local?
- Sua empresa usa qualquer provedores de identidade de terceiros (IdP)?

Você também precisa estar ciente dos serviços de nuvem que sua empresa pode ter. Executar uma avaliação para entender a integração atual com modelos de SaaS, IaaS ou PaaS em seu ambiente é muito importante. Certifique-se de perguntas a seguir durante essa avaliação:
- Sua empresa tem qualquer integração com um provedor de serviços de nuvem?
- Em caso afirmativo, quais serviços estão sendo usados?
- É essa integração atualmente em produção ou um piloto?


>[AZURE.NOTE]
Se você não tiver um mapeamento preciso de todos os seus aplicativos e serviços em nuvem, você pode usar a ferramenta de descoberta de aplicativo de nuvem. Esta ferramenta pode fornecer o departamento de TI visibilidade todos os seus negócios da organização e aplicativos de nuvem do consumidor. Que torna mais fácil do que nunca para descobrir sombra IT em sua organização, incluindo detalhes sobre padrões de uso e todos os usuários acessar seus aplicativos em nuvem. Para acessar essa ferramenta, vá para [https://appdiscovery.azure.com](https://appdiscovery.azure.com/)

## <a name="evaluate-identity-integration-requirements"></a>Avaliar requisitos de integração de identidade
Em seguida, você precisa avaliar os requisitos de integração de identidade. Essa avaliação é importante definir os requisitos técnicos para como irá autenticar usuários, qual será a aparência da presença da organização na nuvem, como a organização permitirá autorização e o que a experiência do usuário será. Certifique-se de responder às seguintes perguntas:

- Sua organização usará a federação, autenticação padrão ou ambos?
- É um requisito de Federação?  Devido o seguinte:
 - SSO baseada em Kerberos
 - Sua empresa tem um aplicativos de local (seja criado internamente ou 3º festa) que usa SAML ou recursos de Federação semelhantes.
 - MFA via cartões inteligentes. RSA SecurID, etc.
 - Regras de acesso do cliente que as perguntas abaixo de endereços:
     1. Posso bloquear todo o acesso externo para o Office 365 com base no endereço IP do cliente?
     1. Posso bloquear todo o acesso externo para o Office 365, com exceção do Exchange ActiveSync?
     1. Pode bloquear o acesso externo para o Office 365, exceto para aplicativos baseados em navegador (OWA, SPO)
     1. Pode bloquear todo o acesso externo para o Office 365 para membros de grupos do AD designados
- Preocupações/auditoria de segurança
- Investimento já existente em autenticação federada
- O nome da nossa organização usará para nosso domínio na nuvem?
- A organização tiver um domínio personalizado?
    1. É esse domínio público e facilmente verificados por meio de DNS?
    1. Se não for, em seguida, você tem um domínio público que pode ser usado para registrar um UPN alternativo no AD?
- São os identificadores de usuário consistente para representação de nuvem? 
- Organização ter aplicativos que exigem integração com serviços de nuvem?
- A organização tiver vários domínios e todos eles usará a autenticação padrão ou federada?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Avaliar aplicativos que são executados em seu ambiente
Agora que você tem uma ideia com relação ao seu local e infraestrutura de nuvem, você precisa avaliar os aplicativos executados nesses ambientes. Essa avaliação é importante definir os requisitos técnicos para integrar esses aplicativos para o sistema de gerenciamento de identidade de nuvem. Certifique-se de responder às seguintes perguntas:

- Onde nossos aplicativos serão live?
- Os usuários acessarão aplicativos local?  Na nuvem? Ou ambos?
- Existem planos para fazer as cargas de trabalho existente do aplicativo e movê-los para a nuvem?
- Existem planos para desenvolver novos aplicativos que residem locais ou na nuvem que usará nuvem autenticação?

## <a name="evaluate-user-requirements"></a>Avaliar as necessidades do usuário
Você também precisa avaliar as necessidades do usuário. Essa avaliação é importante para definir as etapas que serão necessários para integração e ajudar os usuários como eles de transição para a nuvem. Certifique-se de responder às seguintes perguntas:

- Os usuários acessarão aplicativos local?
- Os usuários acessarão aplicativos na nuvem?
- Como fazer usuários geralmente fazer login no seu ambiente local?
- Como serão os usuários entrar para a nuvem?

>[AZURE.NOTE]
Certifique-se de fazer anotações de cada resposta e entender racional a resposta. [Requisitos de resposta a incidente de determinar](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) irão sobre as opções disponíveis e profissionais/contras de cada opção.  Por ter respondidas essas perguntas que você selecionará a opção que melhor adequado para sua empresa precisa.

## <a name="next-steps"></a>Próximas etapas
[Determinar os requisitos de sincronização de diretório](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Consulte também
[Visão geral de considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)
