
<properties
    pageTitle="Azure Active Directory híbrido identidade considerações de design - determinar os requisitos de controle de acesso | Microsoft Azure"
    description="Aborda os pilares de identidade e identificar requisitos de acesso para recursos para usuários em um ambiente híbrido."
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

# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Determinar os requisitos de controle de acesso para sua solução de identidade híbrido
Quando uma organização está criando sua solução de identidade híbrido eles também podem usar esta oportunidade para analisar os requisitos de acesso para os recursos que estão planejando para disponibilizá-lo para os usuários. O acesso a dados cruzado todos os quatro pilares da identidade, que são:

- Administração
- Autenticação
- Autorização
- Auditoria

As seções a seguir assumirá autenticação e autorização em mais detalhes, administração e auditoria fazem parte do ciclo de vida de identidade híbrido. Leia [tarefas de gerenciamento de identidade determinar híbrido](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) para obter mais informações sobre esses recursos.

>[AZURE.NOTE]
Leia [A quatro pilares de identidade - gerenciamento de identidades na caixa idade de híbrido](http://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) para obter mais informações sobre cada um desses pilares.

## <a name="authentication-and-authorization"></a>Autenticação e autorização
Há cenários diferentes para autenticação e autorização, esses cenários terão requisitos específicos que devem ser preenchidos pela solução de identidade híbrido que a empresa vai adotar. Cenários que envolvem comunicação do Business to Business (B2B) podem adicionar um desafio extra para administradores de TI, desde que eles precisam garantir que o método de autenticação e a autorização usado pela organização possa se comunicar com seus parceiros de negócios. Durante o processo de criação de requisitos de autenticação e a autorização, certifique-se de que as seguintes perguntas são respondidas:

- Sua organização autenticar e autorizar somente os usuários localizados no seu sistema de gerenciamento de identidade?
 - Há planos para cenários de B2B?
 - Se escolher Sim, você já sabe quais protocolos (SAML, OAuth, Kerberos, Tokens ou certificados) serão usados para conectar-se ambas as empresas?
- A solução de identidade híbrido que você vai adotar suporte significa esses protocolos?

Outro ponto importante a considerar é onde o repositório de autenticação que será usado por usuários e parceiros será localizado e o modelo administrativo a ser usado. Considere as seguintes opções de dois principais:
- Centralizado: neste modelo credenciais do usuário, políticas e administração podem ser local centralizado ou na nuvem.
- Híbrido: neste modelo credenciais do usuário, políticas e administração será local centralizado e um replicados na nuvem.

O modelo de sua organização adotará varia de acordo com suas necessidades de negócios, que você deseja responder às seguintes perguntas para identificar onde o sistema de gerenciamento de identidade residirão e o modo administrativo usar:

- Sua organização tem atualmente um gerenciamento de identidades local?
 - Se Sim, eles planeja mantê-lo?
 - Existem quaisquer requisitos de conformidade ou regulamento que sua organização deve seguir que define onde o sistema de gerenciamento de identidade deve residem?
- Sua organização usa logon único para aplicativos localizados no local ou na nuvem?
 - Se escolher Sim, a adoção de um modelo de identidade híbrido afeta esse processo?

## <a name="access-control"></a>Controle de acesso
Enquanto autenticação e a autorização são os principais elementos para habilitar o acesso a dados corporativos por meio de validação do usuário, também é importante controlar o nível de acesso que esses usuários terão e o nível de administradores de acesso terão sobre os recursos que estão gerenciando. Sua solução de identidade híbrido deve ser capaz de fornecer acesso granular aos recursos, delegação e controle de acesso de base de função. Certifique-se de que a pergunta a seguir são respondidas sobre controle de acesso:

- Sua empresa tem mais de um usuário com privilégio elevado para gerenciar seu sistema de identidade?
 - Se escolher Sim, cada usuário precisa do mesmo nível de acesso?
- Sua empresa precisaria acesso aos usuários para gerenciar recursos específicos de representante?
 - Se escolher Sim, frequência isso acontece?
- Sua empresa precisaria integrar recursos de controle de acesso entre locais e nuvem recursos?
- Sua empresa precisaria limitar o acesso aos recursos de acordo com algumas condições?
- Sua empresa teria qualquer aplicativo que precise acessar alguns recursos de controle personalizado?
 - Se escolher Sim, onde estão os aplicativos localizados (local ou na nuvem)?
 - Se escolher Sim, onde são aqueles direcionar recursos localizados (local ou na nuvem)?

>[AZURE.NOTE]
Certifique-se de fazer anotações de cada resposta e entender racional a resposta. [Definir estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) irão sobre as opções disponíveis e vantagens/desvantagens de cada opção.  Respondendo essas perguntas você selecionará a opção que melhor atenda às suas necessidades de negócios.

## <a name="next-steps"></a>Próximas etapas

[Determinar os requisitos de resposta ao incidente](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Consulte também
[Visão geral de considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)
