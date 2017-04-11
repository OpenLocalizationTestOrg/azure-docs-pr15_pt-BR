<properties
    pageTitle="Azure Active Directory híbrido identidade considerações de design - determinar os requisitos de autenticação multifator"
    description="Com o controle de acesso condicional, o Azure Active Directory verifica as condições específicas que você escolher quando a autenticação do usuário e antes de permitir acesso ao aplicativo. Depois que essas condições forem atendidas, o usuário autenticado e permissão para acessar o aplicativo."
    documentationCenter=""
    services="active-directory"
    authors="femila"
    manager="billmath"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Determinar os requisitos de autenticação multifator para sua solução de identidade híbrido

Neste mundo de mobilidade, com usuários acessando dados e os aplicativos na nuvem e de qualquer dispositivo, proteger essas informações tornou fundamental.  Todos os dias há um novo título sobre uma violação de segurança.  Embora, não há nenhuma garantia contra falhas desse tipo, autenticação multifator, fornece uma camada adicional de segurança para ajudar a evitar essas violações.
Começar avaliando os requisitos de organizações para autenticação multifator. Isto é, o que é a organização tentando seguro.  Essa avaliação é importante definir os requisitos técnicos para configurar e habilitar os usuários de organizações para autenticação multifator.

>[AZURE.NOTE]
Se você não estiver familiarizado com MFA e o que ela faz, é altamente recomendável que você leia o artigo [o que é a autenticação multifator Azure?](../multi-factor-authentication/multi-factor-authentication.md) anterior para continuar a ler esta seção.

Certifique-se de atender o seguinte:

- Sua empresa está tentando proteger aplicativos da Microsoft? 
- Como esses aplicativos são publicados?
- Sua empresa dá acesso remoto para permitir que os funcionários acessem aplicativos local?

Em caso afirmativo, que tipo de acesso remoto? Você também precisa avaliar onde os usuários que estão acessando esses aplicativos será localizados. Essa avaliação é outro passo importante para definir a estratégia de autenticação multifator adequada. Certifique-se de responder às seguintes perguntas:

- Onde os usuários serão localizado?
- Podem ser localizados em qualquer lugar?
- Sua empresa deseja estabelecer restrições de acordo com o local do usuário?

Depois de compreender esses requisitos, é importante também avaliar requisitos do usuário para autenticação multifator. Essa avaliação é importante porque ele definirá os requisitos para implementar autenticação multifator. Certifique-se de responder às seguintes perguntas:

- Os usuários estão familiarizados com autenticação multifator?
- Alguns usos serão necessários para fornecer autenticação adicional?  
 - Se escolher Sim, o tempo todo, quando provenientes de redes externas ou aplicativos específicos ao acessar ou outras condições?
- Os usuários exigem treinamento sobre como configurar e implementar autenticação multifator?
- Quais são os principais cenários de sua empresa deseja habilitar a autenticação multifator para seus usuários?

Depois de responder as perguntas anteriores, você poderá entender se houver autenticação multifator já implementado no local. Essa avaliação é importante definir os requisitos técnicos para configurar e habilitar os usuários de organizações para autenticação multifator. Certifique-se de responder às seguintes perguntas:

- Sua empresa precisa proteger contas com privilégios com MFA?
- Sua empresa precisa habilitar MFA para determinados aplicativo por razões de conformidade?
- Sua empresa precisa habilitar MFA para todos os usuários qualificados desses aplicativos ou somente os administradores?
- Você precisa tem MFA sempre habilitado ou somente quando os usuários estão conectados fora da sua rede corporativa?


## <a name="next-steps"></a>Próximas etapas
[Definir uma estratégia de adoção de identidade híbrido](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)


## <a name="see-also"></a>Consulte também
[Visão geral de considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)
