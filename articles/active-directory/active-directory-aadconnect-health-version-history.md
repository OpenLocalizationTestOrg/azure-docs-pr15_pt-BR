<properties
    pageTitle="Histórico de versões de integridade do Azure AD Connect"
    description="Este documento descreve as versões do Azure AD conectar saúde e o que foi incluído nessas versões."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect integridade: Histórico de versão de versão

A equipe do Active Directory do Azure atualiza regularmente Azure AD conectar integridade com novos recursos e funcionalidades. Este artigo lista as versões e os recursos que foram lançados.

## <a name="october-2016"></a>Outubro de 2016
**Atualização do agente:**
- Agente de integridade de conectar-se de AD Azure do AD FS \(versão 2.6.408.0\)
    1. Aprimoramentos detectar endereços IP do cliente em solicitações de autenticação
    2. Correções de bugs relacionadas a alertas
- Agente de integridade de conectar-se de AD Azure para o AD DS (versão 2.6.408.0)
    1. Correções de bugs relacionadas a alertas.
- Agente de integridade de conectar-se de AD Azure para sincronização (versão 2.6.353.0) lançada com o Azure AD Connect versão 1.1.281.0
    1. Fornecer os dados necessários para os relatórios de erros de sincronização
    2. Correções de bugs relacionadas a alertas

**Novos recursos de visualização:**
- Relatórios de erros de sincronização do Azure AD conectar

**Novos recursos:**
- Azure AD conectar integridade do AD FS - campo de endereço IP está disponível no relatório sobre superiores 50 usuários com o nome de usuário/senha incorreta.

## <a name="july-2016"></a>Julho de 2016

**Novos recursos de visualização:**

- [Azure AD Connect integridade para o AD DS](active-directory-aadconnect-health-adds.md).


## <a name="january-2016"></a>Janeiro de 2016


**Atualização do agente:**

- Agente de integridade de conectar-se de AD Azure do AD FS (versão 2.6.91.1512)


**Novos recursos:**

- [Ferramenta de teste de conectividade do Azure AD conectar agentes de integridade](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)


## <a name="november-2015"></a>Novembro de 2015


**Novos recursos:**

- Suporte para o [controle de acesso baseado em função](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)


**Novos recursos de visualização:**

- [Azure AD conectar integridade para sincronização](active-directory-aadconnect-health-sync.md).

**Problemas corrigidos:**

- Correções visto durante registros de agente de erros.

## <a name="september-2015"></a>Setembro de 2015

**Novos recursos:**

- Errado relatório de senha do nome de usuário do AD FS
- Suporte para configurar não autenticados Proxy HTTP
- Suporte para configurar o agente no Server core
- Melhorias de alertas do AD FS
- Melhorias no Azure AD conectar agente de integridade do AD FS para conectividade e dados de carregamento.


**Problemas corrigidos:**

- Correções de bugs nos obtenção de informações de uso para os tipos de erro do AD FS.


## <a name="june-2015"></a>Junho de 2015

**Versão inicial do Azure AD conectar integridade do AD FS e Proxy do AD FS.**

**Novos recursos:**

- Alertas para monitorar servidores AD FS e Proxy do AD FS com notificações por email.
- Acesso fácil a topologia do AD FS e padrões em contadores de desempenho do AD FS.
- Tendência em solicitações de token bem-sucedida em servidores do AD FS agrupados por aplicativos, métodos de autenticação, solicitar etc do local de rede.
- Tendências de solicitação falha em servidores do AD FS agrupados por aplicativos, erro tipos etc.
- Implantação mais simples de agente usando credenciais de Administrador Global do Azure AD.  




## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre os [Serviços de infraestrutura e sincronização de identidade de monitorar seu local na nuvem](active-directory-aadconnect-health.md).
