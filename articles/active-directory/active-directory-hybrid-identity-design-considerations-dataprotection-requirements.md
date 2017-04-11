<properties
    pageTitle="Azure Active Directory híbrido identidade considerações de design - determinar os requisitos de proteção de dados | Microsoft Azure"
    description="Ao planejar sua solução de identidade híbridos, identifique os requisitos de proteção de dados para sua empresa e quais opções estão disponíveis para melhor fulfil esses requisitos."
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

#<a name="plan-for-enhancing-data-security-through-strong-identity-solution"></a>Plano para melhorar a segurança de dados por meio de solução de identidade fortes

A primeira etapa para proteger os dados é identificar quem pode acessar os dados e como parte desse processo, que você precisa ter uma identidade solução que pode se integra ao seu sistema para fornecer recursos de autenticação e a autorização. Autenticação e autorização geralmente são confundidas uns com os outros e incompreendido de suas funções. Na verdade, são bastante diferentes, como mostrado na figura abaixo:

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)
 
**Estágios do ciclo de vida de gerenciamento de dispositivo móvel**

Ao planejar sua solução de identidade híbrido você deve compreender os requisitos de proteção de dados para sua empresa e quais opções estão disponíveis para melhor fulfil esses requisitos.
 
>[AZURE.NOTE]
Depois de concluir o planejamento de segurança de dados, examine a [determinar os requisitos de autenticação multifator](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md) para garantir que suas seleções sobre os requisitos de autenticação multifator não foram afetadas pelas decisões que você fez nesta seção.

## <a name="determine-data-protection-requirements"></a>Determinar os requisitos de proteção de dados
Na idade de mobilidade, a maioria das empresas tem um objetivo comum: permitir que seus usuários ser mais produtivo em seus dispositivos móveis ao local ou remotamente em qualquer lugar para aumentar a produtividade. Enquanto isso poderia ser uma meta comum, empresas que têm essa exigência também será preocupações sobre a quantidade de ameaças que deve ser minimizado para proteger os dados da empresa e manter a privacidade do usuário. Cada empresa pode ter requisitos diferentes em relação a isso; regras de conformidade diferente que variam de acordo com a qual setor a empresa está agindo conduzirá a decisões de design diferente. 

No entanto, há alguns aspectos de segurança que devem ser explorados e validados, independentemente do setor, que são explicados na próxima seção.

## <a name="data-protection-paths"></a>Caminhos de proteção de dados

![](./media/hybrid-id-design-considerations/data-protection-paths.png)
 
**Caminhos de proteção de dados**

No diagrama acima, o componente de identidade será a primeira a ser verificada antes de dados são acessados. No entanto, esses dados podem ser em diferentes estados durante o tempo que ele foi acessado. Cada número este diagrama representa um caminho no qual dados podem ser localizados em algum momento no tempo. Esses números são explicados abaixo:

1. Proteção de dados no nível do dispositivo.
2. Proteção de dados em trânsito.
3. Proteção de dados enquanto em outros locais.
4. Proteção de dados ao restante na nuvem.

Embora os técnicos controles que permitem a TI proteger os dados em si em cada um desses fases não diretamente oferecidas pela solução de identidade híbridos, é necessário que a solução de identidade híbrida é capaz de aproveitar locais e nuvem recursos de gerenciamento de identidade para identificar o usuário antes de conceder acesso aos dados. Quando planejar sua solução de identidade híbrido garantir que as seguintes perguntas são respondidas de acordo com os requisitos da organização:

## <a name="data-protection-at-rest"></a>Proteção de dados inativos
Independentemente de onde os dados estão inativos (dispositivo, nuvem ou local), é importante realizar uma avaliação para entender as necessidades de organização em relação a isso. Para essa área, certifique-se de que as perguntas a seguintes são feitas:

- Sua empresa precisa proteger os dados no restante?
 - Se escolher Sim, é a solução de identidade híbrido capaz de integrar com sua infraestrutura de local atual?
 - Se escolher Sim, é a solução de identidade híbrido capaz de integrar com suas cargas de trabalho localizadas na nuvem?
- O gerenciamento de identidades de nuvem é capaz de proteger as credenciais do usuário e outros dados armazenados na nuvem?

## <a name="data-protection-in-transit"></a>Proteção de dados em trânsito
Dados em trânsito entre o dispositivo e data center ou entre o dispositivo e a nuvem devem estar protegidos. No entanto, sendo em trânsito não significa necessariamente um processo de comunicações com um componente fora do seu serviço de nuvem; Move internamente, além disso, como entre duas redes virtuais. Para essa área, certifique-se de que as perguntas a seguintes são feitas:

- Sua empresa precisa proteger os dados em trânsito?
 - Se escolher Sim, é a solução de identidade híbrido capaz de integrar com controles de seguros como SSL/TLS?
- O gerenciamento de identidades de nuvem manter o tráfego para e dentro o armazenamento de diretório (dentro e entre data centers) assinado?


## <a name="compliance"></a>Conformidade
Requisitos de conformidade regulamentar, leis e normas variará de acordo com o setor de sua empresa pertence. Empresas em setores regulamentadas altas devem lidar com questões de gerenciamento de identidades relacionados a problemas de conformidade. Normas como Sarbanes-Oxley (SOX), a portabilidade de seguros de saúde e responsabilidade Act (HIPAA), a Gramm-Leach-Bliley Act (GLBA) e o pagamento Card Industry Data Security Standard (PCI DSS) são muito estritas sobre identidades e acesso. A solução de identidade híbrido que sua empresa adotará deve ter os principais recursos que atenderá às necessidades de um ou mais dessas normas. Para essa área, certifique-se de que as perguntas a seguintes são feitas:

- É a solução de identidade de híbrido compatíveis com os requisitos regulatórios para sua empresa?
- A solução de identidade híbrido foi construído na recursos que permitem que sua empresa seja compatível com requisitos regulatórios? 
 
>[AZURE.NOTE]
Certifique-se de fazer anotações de cada resposta e entender racional a resposta. [Definir estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) irão sobre as opções disponíveis e vantagens/desvantagens de cada opção.  Por ter respondidas essas perguntas que você selecionará a opção que melhor adequado para seu negócio precisa.

## <a name="next-steps"></a>Próximas etapas
 [Determinar os requisitos de gerenciamento de conteúdo](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)


## <a name="see-also"></a>Consulte também
[Visão geral de considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)
