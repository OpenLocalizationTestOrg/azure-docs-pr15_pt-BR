<properties
    pageTitle="Identidade de híbrido do Azure Active Directory considerações de design - como determinar a tarefas de gerenciamento de identidade híbrido | Microsoft Azure"
    description="Com o controle de acesso condicional, o Azure Active Directory verifica as condições específicas que você escolher quando a autenticação do usuário e antes de permitir acesso ao aplicativo. Depois que essas condições forem atendidas, o usuário autenticado e permissão para acessar o aplicativo."
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

# <a name="plan-for-hybrid-identity-lifecycle"></a>Planejar para o ciclo de vida de identidade híbrido 

Identidade é uma dos fundamentos de sua estratégia de acesso de mobilidade e aplicativo da empresa. Se você estiver entrando seu dispositivo móvel ou um aplicativo de SaaS, sua identidade é fundamental para obter acesso a tudo. Em seu nível mais alto, uma solução de gerenciamento de identidades engloba Unificação e sincronizando entre seus repositórios de identidade que inclui automatizar e centralizar o processo de provisionamento de recursos. A solução de identidade deve ser uma identidade centralizada em locais e nuvem e também usar alguma forma de federação de identidade para manter a autenticação centralizada e com segurança compartilhar e colaborar com usuários externos e empresas. Recursos variam de sistemas operacionais e aplicativos para as pessoas na ou associado a uma organização. Estrutura organizacional pode ser alterada para acomodar as políticas e procedimentos de provisionamento.

Também é importante ter uma solução de identidades voltada para permitir que seus usuários fornecendo-lhes experiências de autoatendimento para mantê-los produtivo. A solução de identidade é mais robusta se ele permite logon único para usuários em todos os recursos que precisam de acesso em todos os administradores níveis podem usar padronizados procedimentos de gerenciamento de credenciais de usuário. Alguns níveis de administração podem ser reduzidas ou eliminadas, dependendo a amplitude da solução de gerenciamento de provisionamento. Além disso, você pode com segurança distribuir recursos de administração, manual ou automaticamente, entre várias organizações. Por exemplo, um administrador de domínio pode servir somente as pessoas e recursos nesse domínio. Esse usuário pode realizar tarefas administrativas e de provisionamento, mas não está autorizado a realizar tarefas de configuração, como criar fluxos de trabalho.


## <a name="determine-hybrid-identity-management-tasks"></a>Determinar as tarefas de gerenciamento de identidade híbrido
Distribuir tarefas administrativas em sua organização melhora a precisão e a eficiência de administração e melhora o equilíbrio da carga de trabalho de uma organização. A seguir estão as tabelas dinâmicas que definem um sistema de gerenciamento de identidade robusta.

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)


Para definir as tarefas de gerenciamento de identidade híbridos, você deve compreender algumas características essenciais da organização que vai ser adotando identidade híbrido. É importante entender os repositórios atuais sendo usados para fontes de identidade. Conhecendo os elementos principais, você terá os requisitos básicos e com base no que você precisará fazer perguntas mais granulares que conduzirá a uma melhor decisão de design para sua solução de identidade.  

Ao definir esses requisitos, certifique-se de que pelo menos as seguintes perguntas são respondidas

- Opções de configuração: 
 - A solução de identidade híbrido oferece suporte a um sistema de provisionamento e gerenciamento de acesso à conta robustos?
 - São usuários, grupos e senhas indo para ser gerenciado?
 - O gerenciamento do ciclo de vida de identidades está respondendo? 
      - Quanto tempo leva suspensão de conta de atualizações de senha?
      
- Gerenciamento de licenças: 
 - Faz o gerenciamento de licenças híbrido identidade solução alças?
     - Em caso afirmativo, quais recursos estão disponíveis?
- A solução lidar com gerenciamento de licença baseada em grupo? 
      -Se Sim, é possível atribuir um grupo de segurança a ele? 
       -Se Sim, o diretório de nuvem atribuirá automaticamente licenças para todos os membros do grupo? 
        -O que acontece se um usuário subsequentemente é adicionado à ou removido do grupo, será uma licença automaticamente atribuída ou removida conforme apropriada? 

- Integração com outros provedores de identidade de terceiros:
- Essa solução híbrido pode ser integrada com provedores de identidade de terceiros para implementar o logon único?
- É possível unificar todos os provedores de identidade diferente em um sistema de identidade consistente?
- Em caso afirmativo, como e quais são elas e quais recursos estão disponíveis?

## <a name="synchronization-management"></a>Gerenciamento de sincronização
Uma das metas de um Gerenciador de identidades, para poder exibir todos os provedores de identidade e mantê-las sincronizadas. Manter os dados sincronizados com base em um provedor de identidade mestre autoritativas. Em um cenário de identidade híbridos, com um modelo de gerenciamento sincronizada, você gerenciar todas as identidades de usuários e dispositivos em um servidor local e sincronizar as contas e, opcionalmente, senhas para a nuvem. O usuário insere as mesma senha locais como ele ou ela faz na nuvem e ao entrar, a senha é verificada pela solução de identidade. Esse modelo usa uma ferramenta de sincronização de diretórios.
 
![](./media/hybrid-id-design-considerations/Directory_synchronization.png)Design adequado a sincronização de sua solução de identidade híbrido garantir que as questões a seguir são respondidas: • quais são as soluções de sincronização disponíveis para a solução de identidade híbrido?
• Quais são o recursos disponíveis de logon único?
• Quais são as opções para a federação de identidade entre B2B e B2C?

## <a name="next-steps"></a>Próximas etapas
[Determinar estratégia de adoção de gerenciamento de identidades híbrido](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)


## <a name="see-also"></a>Consulte também
[Visão geral de considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)

