<properties
    pageTitle="Azure Active Directory híbrido identidade considerações de design - determinar os requisitos de sincronização de diretório | Microsoft Azure"
    description="Identificar quais requisitos são necessários para sincronizar todos os usuários entre = no local e nuvem para a empresa."
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

# <a name="determine-directory-synchronization-requirements"></a>Determinar os requisitos de sincronização de diretório
A sincronização é tudo sobre fornecer aos usuários uma identidade na nuvem com base em sua identidade de local. Estando ou não utilizarão conta sincronizada para autenticação ou a autenticação federada, os usuários ainda precisam ter uma identidade na nuvem.  Esta identidade precisará ser mantidos e atualizados periodicamente.  As atualizações podem levar muitos formulários, alterações de título para alterações de senha.  

Começar avaliando os requisitos de usuário e solução de identidade do local organizações. Essa avaliação é importante definir os requisitos técnicos para como identidades dos usuários serão criadas e mantidas na nuvem.  Para a maioria das organizações, do Active Directory é local e será o diretório local que os usuários poderão por sincronizado de, mas em alguns casos não será o caso.  

Certifique-se de responder às seguintes perguntas:


- Você tem uma floresta do AD, múltiplo ou nenhum?
 - Você quantos diretórios Azure AD vai ser sincronizando com?
 
    1. Você está usando filtragem?
    2. Você tem vários servidores do Azure AD Connect planejados?
  
- Atualmente, você tem uma sincronização ferramenta local?
  - Se Sim, não seus usuários se os usuários têm uma virtual/integração de diretórios de identidades?
- Você tem qualquer outro diretório local que você deseja sincronizar (por exemplo, diretório LDAP, banco de dados HR, etc)?
  - Você vai fazer qualquer GALSync?
  - O que é o estado atual do UPNs em sua organização? 
  - Você tem um diretório diferente que os usuários se autenticar?
  - Sua empresa usa o Microsoft Exchange?
    - Eles planeja de ter uma implantação híbrida do exchange?

Agora que você tem uma ideia sobre os requisitos de sincronização, você precisa determinar qual ferramenta é o correto para atender a esses requisitos.  A Microsoft fornece diversas ferramentas para realizar a sincronização e integração de diretório.  Consulte a [tabela de comparação de ferramentas de integração do híbrido identidade diretório](active-directory-hybrid-identity-design-considerations-tools-comparison.md) para obter mais informações. 
   
Agora que você tem seus requisitos de sincronização e a ferramenta que realizará essa para sua empresa, você precisa avaliar os aplicativos que usam esses serviços de diretório. Essa avaliação é importante definir os requisitos técnicos para integrar esses aplicativos para a nuvem. Certifique-se de responder às seguintes perguntas:

- Esses aplicativos serão movidos para a nuvem e use o diretório?
- Existem atributos especiais que precisam ser sincronizados na nuvem para que esses aplicativos podem usá-los com êxito?
- Esses aplicativos precisarão ser escrita novamente para tirar proveito de nuvem auth?
- Esses aplicativos continuará a live local enquanto os usuários acessá-los usando a identidade de nuvem?

Você também precisa determinar a sincronização de diretório de requisitos e restrições de segurança. Essa avaliação é importante para obter uma lista dos requisitos que serão necessários para criar e manter identidades do usuário na nuvem. Certifique-se de responder às seguintes perguntas:

- Onde o servidor de sincronização será localizado?
- Será domínio associado?
- O servidor será ser localizado em uma rede restrita atrás de um firewall, como DMZ?
  - Você poderá abrir as portas de firewall necessárias para oferecer suporte a sincronização?
- Você tem um plano de recuperação de desastres para o servidor de sincronização?
- Você tem uma conta com as permissões corretas para todas as florestas que você deseja sincronizar com?
 - Se sua empresa não souber a resposta para essa pergunta, examine a seção "Permissões para sincronização de senha" no artigo [instalar o serviço de sincronização do Azure Active Directory](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) e determinar se você já tiver uma conta com estas permissões ou se você precisa criar um.
- Se você tiver a sincronização de mutli-floresta está o servidor de sincronização conseguir chegar à cada floresta?
 
>[AZURE.NOTE]
Certifique-se de fazer anotações de cada resposta e entender racional a resposta. [Requisitos de resposta a incidente de determinar](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) irão sobre as opções disponíveis. Por ter respondidas essas perguntas que você selecionará a opção que melhor adequado para seu negócio precisa.

## <a name="next-steps"></a>Próximas etapas
[Determinar os requisitos de autenticação multifator](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Consulte também
[Visão geral de considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)
