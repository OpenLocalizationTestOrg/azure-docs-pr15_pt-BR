<properties
    pageTitle="Azure Active Directory híbrido identidade considerações de design - visão geral | Microsoft Azure"
    description="Visão geral e mapa de conteúdo da guia de considerações de design de identidade híbrido"
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

# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Considerações de Design do Active Directory do Azure híbrido identidade

Dispositivos baseados em consumidor estão proliferando o mundo corporativo e baseado em nuvem aplicativos do software como serviço (SaaS) são fáceis de adotar. Como resultado, mantendo o controle de acesso dos usuários ao aplicativo plataformas interno dos data centers e nuvem é um desafio.  Soluções de identidade da Microsoft abrangem locais e recursos baseados na nuvem, criando uma identidade de usuário único para autenticação e autorização para todos os recursos, independentemente de local. Chamamos essa identidade híbrido. Há design diferente e opções de configuração para identidade de híbrido usando soluções da Microsoft e, em alguns casos pode ser difícil determinar qual combinação será melhor atender às necessidades da sua organização. Este guia de considerações de Design de identidade híbrido ajudará você a entender como projetar uma solução de identidade híbrido que melhor se adapte os negócios e tecnologia precisa para sua organização.  Este guia Detalhes uma série de etapas e tarefas que você pode seguir para ajudá-lo a projetar uma solução de identidade híbrido que atende aos requisitos exclusivos da sua organização. Durante as etapas e tarefas, guia apresentará as tecnologias relevantes e opções de recursos disponíveis para as organizações reunir funcional e qualidade de serviço (como disponibilidade, escalabilidade, desempenho, capacidade de gerenciamento e segurança) requisitos de nível. Especificamente, as metas de guia híbrido identidade design considerações são responda às seguintes perguntas: 

- Quais são as perguntas preciso perguntar e responder para direcionar um design de identidade específicas de híbrido para um domínio de tecnologia ou problema que melhor atenda às minhas necessidades?
- Quais sequência de atividades devem concluir para projetar uma solução de identidade híbrido para o domínio de tecnologia ou problema? 
- Quais opções de configuração e tecnologia de identidade híbrido estão disponíveis para me ajudar a atender às minhas necessidades? Quais são as compensações entre essas opções para que eu possa selecionar a melhor opção para minha empresa?


## <a name="who-is-this-guide-intended-for"></a>Quem este guia destina?
 CIO, CITO, Chief identidade arquitetos, Enterprise Architects e arquitetos de TI responsável pela criação de uma solução de identidade híbrida para empresas de médio ou grandes.

## <a name="how-can-this-guide-help-you"></a>Como este guia pode ajudar você? 
Você pode usar este guia para entender como projetar uma solução de identidade híbrida que seja capaz de integrar um sistema de gerenciamento de identidade baseado em nuvem com sua solução de identidade do local atual. A gráfico a seguir mostra um exemplo de uma solução de identidade híbrida que permite que administradores de TI gerenciar a integração de sua solução atual do Active Directory do Windows Server localizado no local com o Microsoft Azure Active Directory para permitir que os usuários usar Single Sign-On (SSO) entre aplicativos localizados na nuvem e locais.

![](./media/hybrid-id-design-considerations/hybridID-example.png)


A ilustração acima é um exemplo de uma solução de identidade híbrido que está utilizando os serviços de nuvem a integração com recursos locais para fornecer uma experiência única para o processo de autenticação do usuário final e para facilitar o gerenciamento desses recursos IT. Embora isso pode ser um cenário muito comum, design de identidade de híbrido de cada organização é provavelmente será diferente de exemplo ilustrado na Figura 1 devido a requisitos diferentes. Este guia fornece uma série de etapas e tarefas que você pode seguir para projetar uma solução de identidade híbrido que atende aos requisitos exclusivos da sua organização. Durante as seguintes etapas e tarefas, o guia apresenta as tecnologias relevantes e opções de recursos disponíveis para atender aos requisitos de nível de qualidade do serviço para sua organização e funcional.

**Suposições**: você tiver alguma experiência com o Windows Server, serviços de domínio Active Directory e Azure Active Directory. Neste documento, vamos supor que você está procurando como essas soluções podem atender às suas necessidades de negócios por conta própria ou em uma solução integrada.

## <a name="design-considerations-overview"></a>Visão geral de considerações de design
Este documento fornece um conjunto de etapas e tarefas que você pode seguir para projetar uma solução de identidade híbrido que melhor atenda às suas necessidades. As etapas são apresentadas em uma sequência ordenada. Considerações de design que você saiba em etapas posteriores podem exigir que você altere decisões que você fez nas etapas anteriores, no entanto, devido às opções de design em conflito. Cada tentativa para alertá-lo para possíveis conflitos de design em todo o documento. 

Você chegará no design que melhor atenda às suas necessidades somente após iteração pelas etapas quantas vezes conforme necessário para incorporar todas as considerações dentro do documento. 

| Fase de identidade híbrido                                             | Lista de tópicos                                                                                                                                                                                       |
|-------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Determinar os requisitos de identidade                                   | [Determinar as necessidades de negócios](active-directory-hybrid-identity-design-considerations-business-needs.md)<br> [Determinar os requisitos de sincronização de diretório](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Determinar os requisitos de autenticação multifator](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definir uma estratégia de adoção de identidade híbrido](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)                       |
| Plano para melhorar a segurança de dados por meio de solução de identidade fortes | [Determinar os requisitos de proteção de dados](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Determinar os requisitos de gerenciamento de conteúdo](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Determinar os requisitos de controle de acesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Determinar os requisitos de resposta ao incidente](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definir estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)  |
| Plano do ciclo de vida de identidade híbrido                                | [Determinar as tarefas de gerenciamento de identidade híbrido](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Gerenciamento de sincronização](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Determinar estratégia de adoção de gerenciamento de identidades híbrido](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |     


##<a name="download-this-guide"></a>Baixe este guia
Você pode baixar uma versão em pdf do guia de considerações de Design de identidade híbrida da [Galeria do Technet](https://gallery.technet.microsoft.com/Azure-Hybrid-Identity-b06c8288). 

                                                             
