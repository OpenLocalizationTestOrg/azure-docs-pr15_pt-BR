<properties
    pageTitle="Azure Active Directory híbrido identidade considerações de design - determinar os requisitos de gerenciamento de conteúdo | Microsoft Azure"
    description="Fornece percepção como determinar os requisitos de gerenciamento de conteúdo da sua empresa. Normalmente quando um usuário tem seu próprio dispositivo ele pode ter também várias credenciais que serão alternados acordo com o aplicativo que ele usa. É importante diferenciar qual conteúdo foi criado usando credenciais pessoais versus aquelas criados usando credenciais corporativas. Sua solução de identidade deve ser capaz de interagir com a nuvem serviços para fornecer uma experiência perfeita ao usuário final durante a garantir a sua privacidade e aumentar a proteção contra perda de dados."
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

# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Determinar os requisitos de gerenciamento de conteúdo para a sua solução de identidade híbrido

Noções básicas sobre os requisitos de gerenciamento de conteúdo para o seu negócio direta pode afetar a sua decisão sobre qual solução de identidade híbrido para usar. Com a proliferação de vários dispositivos e a capacidade dos usuários para trazer seus próprios dispositivos ([BYOD](http://aka.ms/byodcg)), a empresa deve proteger seus próprios dados, mas ele também deve manter privacidade do usuário intacta. Normalmente quando um usuário tem seu próprio dispositivo ele pode ter também várias credenciais que serão alternados acordo com o aplicativo que ele usa. É importante diferenciar qual conteúdo foi criado usando credenciais pessoais versus aquelas criados usando credenciais corporativas. Sua solução de identidade deve ser capaz de interagir com a nuvem serviços para fornecer uma experiência perfeita ao usuário final durante a garantir a sua privacidade e aumentar a proteção contra perda de dados. 

Sua solução de identidade será aproveitada por controles técnicos diferentes para fornecer gerenciamento de conteúdo, conforme mostrado na figura abaixo:
 
![](./media/hybrid-id-design-considerations/securitycontrols.png)

**Controles de segurança que vai ser aproveitando seu sistema de gerenciamento de identidade**

Em geral, os requisitos de gerenciamento de conteúdo utilizará seu sistema de gerenciamento de identidade nas seguintes áreas:

- Privacidade: identifica o usuário que possui um recurso e aplicar os controles apropriados para manter a integridade.
- Classificação de dados: identificar o usuário ou o grupo e o nível de acesso a um objeto de acordo com sua classificação. 
- Proteção de perda de dados: o responsável por proteção de dados para evitar perda de controles de segurança precisará interagir com o sistema de identidade para validar a identidade do usuário. Isso também é importante para a finalidade de trilha de auditoria.

>[AZURE.NOTE]
Leia a [classificação de dados para preparação de nuvem](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) para obter mais informações sobre as práticas recomendadas e diretrizes para classificação de dados.

Quando planejar sua solução de identidade híbrido garantir que as seguintes perguntas são respondidas de acordo com os requisitos da organização:

- Sua empresa tem controles de segurança no lugar para impor a privacidade dos dados?
 - Se Sim, esses controles de segurança será capazes de integrar com a solução de identidade híbrido que você vai adotar?
- Sua empresa usa a classificação de dados?
 - Se escolher Sim, é capaz de integrar com a solução de identidade híbrido que você vai adotar solução atual?
- Sua empresa tem atualmente qualquer solução de perda de dados? 
 - Se escolher Sim, é capaz de integrar com a solução de identidade híbrido que você vai adotar solução atual?
- Sua empresa precisa acesso aos recursos de auditoria?
 - Em caso afirmativo, que tipo de recursos?
 - Se escolher Sim, o nível de informações é necessário?
 - Se escolher Sim, onde o log de auditoria deve residem? Local ou na nuvem?
- Sua empresa precisa criptografar qualquer emails que contêm dados confidenciais (números de identificação fiscal, números de cartão de crédito, etc)?
- Sua empresa precisa criptografar todos os documentos/conteúdo compartilhado com parceiros de negócios externos?
- Sua empresa precisa aplicar políticas corporativas em determinados tipos de emails (faça sem responder a todos, não encaminhar)?
 
>[AZURE.NOTE]
Certifique-se de fazer anotações de cada resposta e entender racional a resposta. [Definir estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) irão sobre as opções disponíveis e vantagens/desvantagens de cada opção.  Por ter respondidas essas perguntas que você selecionará a opção que melhor adequado para seu negócio precisa.


## <a name="next-steps"></a>Próximas etapas
[Determinar os requisitos de controle de acesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Consulte também
[Visão geral de considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)
