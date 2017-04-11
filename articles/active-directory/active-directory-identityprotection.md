<properties
    pageTitle="Proteção de identidade do Active Directory do Azure | Microsoft Azure"
    description="Saiba como proteção de identidade do Azure AD permite limitar a capacidade de um ataque para explorar uma identidade comprometida ou dispositivo e proteger uma identidade ou um dispositivo que foi anteriormente suspeita ou comprometida."
    services="active-directory"
    keywords="proteção de identidade do active directory do Azure, descoberta de aplicativo de nuvem, gerenciando aplicativos, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection"></a>Proteção de identidade do Active Directory do Azure 

Azure Active Directory identidade proteção é um recurso do Azure AD Premium P2 edition que fornece uma visão consolidada em eventos de risco e possíveis vulnerabilidades afetando identidades da sua organização. Microsoft tem protegendo identidades baseado em nuvem para uma década e com proteção de identidade do Azure AD, Microsoft está disponibilizando esses mesmos sistemas de proteção para clientes corporativos. Proteção de identidade utiliza os recursos de detecção de anomalias do AD Azure existente (disponíveis através relatórios do Azure AD de atividades anormais) e apresenta novos tipos de evento de risco que podem detectar problemas em tempo real.



##<a name="getting-started"></a>Guia de Introdução

A maioria das violações de segurança ocorrem quando ataques obtenham acesso a um ambiente por roubo de identidade de um usuário. Ataques tornaram cada vez mais eficazes aproveitando violações de terceiros e usando ataques de phishing sofisticados. Depois de um invasor obtiver acesso ao mesmo uma conta de usuário privilegiado baixa, é relativamente simples para que elas sejam obter acesso aos recursos de empresa importantes por meio de movimento lateral. Portanto, é essencial para proteger todas as identidades e, quando uma identidade está comprometida, proativamente impedir a identidade comprometida sendo abusos. 

Descobrir comprometidas identidades não é tarefa fácil. Felizmente, pode ajudar a proteção de identidade: proteção de identidade usa algoritmos de aprendizado de máquina adaptativa e heurística para detectar problemas e riscos eventos que podem indicar que uma identidade foi comprometida.
 
Usando esses dados, proteção de identidade gera relatórios e alertas que permite investigar esses eventos de risco e execute a ação de atenuação ou correção apropriado.
 
Mas Azure Active Directory proteção de identidade é mais do que uma ferramenta de monitoramento e relatório. Proteção de identidade com base em eventos de risco, calcula um nível de risco do usuário para cada usuário, permitindo que você configure baseada em riscos políticas para proteger automaticamente as identidades da sua organização.  Essas políticas baseada em riscos, além de outros controles de acesso condicional fornecidos pelo Active Directory do Azure e EMS, podem bloquear ou oferecer ações de correção adaptativa que incluem redefinições de senha e imposição de autenticação multifator automaticamente.  

####<a name="explore-identity-protections-capabilities"></a>Explore os recursos de proteção de identidade 

**Detectar eventos de risco e arriscadas contas:**  

- Detectar 6 tipos de evento de risco usando aprendizado de máquina e regras heurísticas 

- Calcular níveis de risco do usuário

- Fornecendo recomendações personalizadas para melhorar a postura geral de segurança realçando vulnerabilidades



**Investigar eventos de risco:**

- Notificações de envio para eventos de risco

- Investigar eventos de risco usando informações relevantes e contextuais

- Fornecendo fluxos de trabalho básicos para rastrear investigações

- Fornecendo acesso fácil às ações de correção como redefinição de senha



**Políticas de acesso condicional baseado em risco:**

- Política para atenuar suplementos entrada arriscados bloqueando suplementos de entrada ou exigindo desafios de autenticação multifator.

- Política para contas de usuário arriscada segura ou bloco

- Política para exigir que os usuários registrar para autenticação multifator


## <a name="detection-and-risk"></a>Detecção e riscos

### <a name="risk-events"></a>Eventos de risco

Eventos de risco são eventos que foram marcados como suspeito pelo proteção de identidade e indicam que uma identidade pode ter sido comprometida. Para obter uma lista completa de eventos de risco, consulte [tipos de eventos de risco detectados pelo Azure Active Directory proteção de identidade](active-directory-identityprotection-risk-events-types.md). 


### <a name="risk-level"></a>Nível de risco

O nível de risco para um evento de risco é uma indicação (alta, média ou baixa) da gravidade do evento de risco. O nível de risco ajuda os usuários de proteção de identidade a priorizar as ações que eles devem ser seguidas para reduzir o risco à sua empresa. A gravidade do evento risco representa a força do sinal como previsão do compromisso de identidade, combinado com a quantidade de ruído que ele apresenta normalmente. 

- **Alta**: alta confiabilidade e evento de risco alta gravidade. Esses eventos são indicadores fortes que foi comprometida a identidade do usuário e as contas de usuário afetadas devem ser remediadas imediatamente.

- **Médio**: gravidade alta, mas o evento de risco de confiança inferior, ou vice-versa. Esses eventos são potencialmente perigosos e quaisquer contas de usuário afetadas devem ser remediadas.

- **Baixo**: baixa confiança e evento de risco baixa gravidade. Este evento não pode exigir uma ação imediata, mas quando combinado com outros eventos de risco, pode fornecer uma forte indicação que a identidade está comprometida. 


![Nível de risco] (./media/active-directory-identityprotection/01.png "Nível de risco")

 

Eventos de risco ou são identificados em **tempo real**, ou em pós-processamento após o evento de risco já coloque (offline). Atualmente a maioria dos eventos de risco em proteção de identidade são calculados offline e aparecem em proteção de identidade dentro de 2 a 4 horas. Enquanto avaliada em tempo real, os eventos de risco em tempo real serão mostrado no Console de proteção de identidade dentro de 5 a 10 minutos.

Vários clientes legados não suportamos prevenção e detecção de eventos de risco em tempo real. Como resultado, suplementos de sinal desses clientes não podem ser detectados ou impedidos em tempo real.


## <a name="investigation"></a>Investigação
Sua jornada por meio de proteção de identidade normalmente começa com o painel de proteção de identidade. 

![Correção] (./media/active-directory-identityprotection/1000.png "Correção")

Painel lhe dá acesso a:
 
- Relatórios como **usuários sinalizados para risco**, riscos e **eventos** de **vulnerabilidades**
- Configurações como a configuração de **Políticas de segurança**, **notificações** e **registro de autenticação multifator**
 

Normalmente é seu ponto de partida para investigação, que é o processo de revisão as atividades, logs e outras informações relevantes relacionados a um evento de risco para decidir se as etapas de solução ou de minimização são necessárias, e como a identidade foi comprometida e entender como a identidade comprometida foi usada.

Você pode vincular suas atividades de investigação [notificações](active-directory-identityprotection-notifications.md) que Azure Active Directory proteção envia por email.

As seções a seguir fornecerá mais detalhes e as etapas que estão relacionadas a uma investigação.  



## <a name="what-is-a-user-risk-level"></a>O que é um nível de risco de usuário?

Um nível de risco do usuário é uma indicação (alta, média ou baixa) da probabilidade de que a identidade do usuário foi comprometida. Ele é calculado com base em eventos de risco o usuário que estão associados a identidade do usuário. 

O status de um evento de risco está **ativo** ou **fechada**. Somente os eventos de risco que estão **ativos** contribuem para o cálculo de risco do usuário. 

O nível de risco do usuário é calculado usando as entradas a seguir:

- Eventos de risco ativo afetar o usuário
- Nível de risco desses eventos 
- Se as ações de correção foram tomadas 

![Riscos de usuário] (./media/active-directory-identityprotection/1001.png "Riscos de usuário")



Você pode usar os níveis de risco do usuário para criar políticas de acesso condicional para impedir que os usuários arriscados entrar ou forçá-los com segurança alterar sua senha. 


## <a name="closing-risk-events-manually"></a>Fechar eventos de risco manualmente

Na maioria dos casos, você terá de ações de correção como uma segura redefinição de senha para fechar automaticamente eventos de risco. No entanto, isso talvez não sempre seja possível.  
Isto é, por exemplo, o caso, quando:

- Um usuário com eventos de risco ativo foi excluído
- Uma investigação revela que um evento de risco relatada tem sido realizar pelo usuário legítimo

Porque eventos de risco que estão **ativos** contribuem para o cálculo de risco do usuário, você pode precisar diminuir manualmente um nível de risco fechando eventos de risco manualmente.  
Durante o curso da investigação, você pode optar por fazer qualquer uma destas ações para alterar o status de um evento de risco:

![Ações] (./media/active-directory-identityprotection/34.png "Ações")

- **Resolver** - se após investigar um evento de risco, você tirou uma ação de correção apropriado fora da proteção de identidade e você achar que o evento de risco deve ser considerado fechado, marcar o evento como resolvido. Resolvido eventos definirá o status do evento risco para fechado e o evento de risco não mais será contribuir para risco do usuário.

- **Marcar como falsos positivos** - em alguns casos, você pode investigar um evento de risco e descobre que ela foi sinalizada incorretamente como um arriscada. Você pode ajudar a reduzir o número de tais ocorrências marcando o evento de risco como falsos positivos. Isso ajudará a algoritmos para melhorar a classificação de eventos semelhantes no futuro de aprendizado de máquina. O status dos eventos de falsos positivos é **fechado** e já não será contribuem para o risco de usuário.

- **Ignorar** - se você não entraram em qualquer ação de correção, mas deseja que o evento de risco a ser removido da lista ativa, você pode marcar um evento de risco ignorar e o status de evento será fechado. Eventos ignorados não contribuem para o risco de usuário. Essa opção só deve ser usada em circunstâncias incomuns. 

- **Reativar** - eventos de risco manualmente fechadas (escolhendo **resolver**, **falso positivo**ou **Ignorar**) pode ser reativada, definindo o status do evento para **ativo**. Eventos de risco reativadas contribuem para o cálculo de nível de risco do usuário. Eventos de risco fechados através de correção (como redefinir uma senha de segurança) não podem ser reativados. 




**Para abrir a caixa de diálogo de configuração relacionados**:

1. Na lâmina **proteção de identidade do Azure AD** , em **investigar**, clique em **eventos de risco**.

    ![Redefinição de senha manual] (./media/active-directory-identityprotection/1002.png "Redefinição de senha manual")

2. Na lista **eventos de risco** , clique em um risco.

    ![Redefinição de senha manual] (./media/active-directory-identityprotection/1003.png "Redefinição de senha manual")

2. Na lâmina risco, clique com botão direito a um usuário.

    ![Redefinição de senha manual] (./media/active-directory-identityprotection/1004.png "Redefinição de senha manual")



### <a name="closing-all-risk-events-for-a-user-manually"></a>Fechar todos os eventos de risco para um usuário manualmente

Em vez de fechar manualmente eventos de risco para um usuário individualmente, Azure Active Directory proteção de identidade também oferece um método para fechar todos os eventos de um usuário com um único clique.


![Ações] (./media/active-directory-identityprotection/2222.png "Ações")

Quando você clica em **Descartar todos os eventos**, todos os eventos são fechados e o usuário afetado não está mais em risco.



## <a name="remediating-user-risk-events"></a>Correção eventos de risco do usuário

Uma correção é uma ação para proteger uma identidade ou um dispositivo que foi anteriormente suspeita ou comprometida. Uma ação de correção restaura a identidade ou dispositivo para um estado seguro e gerar eventos de risco anteriores associados a identidade ou dispositivo.

Para corrigir os eventos de risco do usuário, você pode:

- Executar uma segura redefinição de senha para remediar eventos de risco do usuário manualmente 

- Configurar uma política de segurança do usuário risco para atenuar ou remediar eventos de risco do usuário automaticamente

- Recriar a imagem no dispositivo infectado  


### <a name="manual-secure-password-reset"></a>Redefinição de senha de segurança manual

Redefinir uma senha de segurança é uma correção eficaz para muitos eventos de risco e, quando executada, automaticamente fecha esses eventos de risco e recalcula o nível de risco do usuário. Você pode usar o painel de proteção de identidade para iniciar uma senha redefinida para um usuário arriscado. 

A caixa de diálogo relacionada fornece dois métodos diferentes para redefinir uma senha:

**Redefinir senha** - selecione **exigir usuário para redefinir a senha** para permitir que o usuário auto recuperar se o usuário foi registrado para autenticação multifator. Durante próxima entrar do usuário, o usuário será necessária para resolver um desafio de autenticação multifator com êxito e, em seguida, forçado a alterar a senha. Esta opção não estará disponível se a conta de usuário não ainda estiver registrada autenticação multifator.

**Senha temporária** - selecione **gerar uma senha temporária** imediatamente invalidar a senha existente e criar uma nova senha temporária para o usuário. Envie a nova senha temporária para um endereço de email alternativo para o usuário ou para o gerente do usuário. Como a senha é temporária, o usuário será solicitado a alterar a senha após entrar.


![Política] (./media/active-directory-identityprotection/1005.png "Política")


**Para abrir a caixa de diálogo de configuração relacionados**:

1. Na lâmina **Azure AD identidade proteção** , clique em **usuários sinalizados para risco**.

    ![Redefinição de senha manual] (./media/active-directory-identityprotection/1006.png "Redefinição de senha manual")


2. Na lista de usuários, selecione um usuário com eventos de pelo menos um risco.

    ![Redefinição de senha manual] (./media/active-directory-identityprotection/1007.png "Redefinição de senha manual")


2. Na lâmina usuário, clique em **Redefinir senha**.

    ![Redefinição de senha manual] (./media/active-directory-identityprotection/1008.png "Redefinição de senha manual")





## <a name="user-risk-security-policy"></a>Política de segurança de risco do usuário

Uma política de segurança do usuário risco é uma política de acesso condicional que avalia o nível de risco a um usuário específico e aplica as ações de correção e atenuação com base em regras e condições predefinidas.


![Política de ridk de usuário] (./media/active-directory-identityprotection/1009.png "Política de ridk de usuário")


Proteção de identidade AD Azure ajuda você a gerenciar a atenuação e correção de usuários sinalizados para risco, permitindo que você:

- Configure os usuários e grupos a que a política se aplica: 

    ![Política de ridk de usuário] (./media/active-directory-identityprotection/1010.png "Política de ridk de usuário")


- Defina o usuário risco limite do nível (baixa, média ou alta) que aciona a política: 

    ![Política de ridk de usuário] (./media/active-directory-identityprotection/1011.png "Política de ridk de usuário")


- Defina os controles a serem aplicadas quando a política dispara:

    ![Política de ridk de usuário] (./media/active-directory-identityprotection/1012.png "Política de ridk de usuário")


- Alterne o estado de sua política:

    ![Política de ridk de usuário] (./media/active-directory-identityprotection/403.png "Registro MFA")


- Revise e avaliar o impacto de uma alteração antes de ativá-lo:

    ![Política de ridk de usuário] (./media/active-directory-identityprotection/1013.png "Política de ridk de usuário")


Escolher um limite **alto** reduz o número de vezes que uma política é disparada e minimiza o impacto para os usuários.
No entanto, ele exclui usuários **baixo** e **Médio** sinalizados para risco da política, que não pode proteger identidades ou dispositivos que estavam anteriormente suspeita ou comprometida.

Ao definir a política,

- Excluir usuários que tendem a gerar muitos falsos positivos (desenvolvedores, analistas de segurança)

- Excluir usuários em locais onde habilitando a política não é prático (por exemplo, nenhum acesso a assistência técnica)

- Use um limite **alto** durante a política inicial implementar o, ou se você deve minimizar desafios vistos pelos usuários finais.

- Use um limite de **baixa** se sua organização exigir maior segurança. Selecionar um limite de **baixo** introduz usuário adicionais entrar desafios, mas maior segurança.

O padrão recomendado para a maioria das organizações é configurar uma regra para um limite de **mídia** para obter um equilíbrio entre usabilidade e segurança.

Para obter uma visão geral da experiência do usuário relacionados, consulte:

- [Fluxo de recuperação de conta infectados](active-directory-identityprotection-flows.md#compromised-account-recovery).  

- [Conta de infectados bloqueada fluxo](active-directory-identityprotection-flows.md#compromised-account-blocked).  


**Para abrir a caixa de diálogo de configuração relacionados**:

1. Na lâmina **proteção de identidade do Azure AD** , na seção **Configurar** , clique em **política de risco do usuário**.

    ![Política de ridk de usuário] (./media/active-directory-identityprotection/1009.png "Política de ridk de usuário")






## <a name="mitigating-user-risk-events"></a>Atenuar eventos de risco do usuário
Os administradores podem definir uma política de segurança do usuário risco para bloquear usuários após entrar dependendo do nível de risco. 

Bloqueando um entrar:
 
- Impede que a geração de novos eventos de risco do usuário para o usuário afetado

- Permite que os administradores remediar os eventos de risco afetar a identidade do usuário e restaurá-lo para um estado seguro manualmente



## <a name="what-is-a-sign-in-risk-level"></a>O que é um nível de risco entrar?

Um nível de entrada risco é uma indicação (alta, média ou baixa) da probabilidade de que para um entrada-in específico, alguém está tentando se autenticar com a identidade do usuário. O nível de risco entrar é avaliado no momento de um entrar e considera eventos de risco e indicadores detectado em tempo real para essa entrada-in específico. 

## <a name="mitigating-sign-in-risk-events"></a>Atenuar o risco entrar eventos 
Um fator atenuante é uma ação para limitar a capacidade de um ataque de explorar uma identidade comprometida ou dispositivo sem restaurar a identidade ou dispositivo para um estado seguro. Uma atenuação não resolver anterior eventos de entrar riscos associados a identidade ou dispositivo.

Você pode usar acesso condicional em proteção de identidade do Azure AD a reduzir automaticamente eventos de risco entrar. Usando essas diretivas, você considerar o nível de risco do usuário ou a entrada do bloquear arriscadas sinal-ins ou exigem que o usuário para realizar autenticação multifator. Essas ações podem impedir que um invasor explorar uma identidade roubada para causar danos e podem fornecer algum tempo para proteger a identidade. 


## <a name="sign-in-risk-security-policy"></a>Política de segurança de entrada risco

Uma política de entrada risco é uma política de acesso condicional que avalia o risco a um sinal-in específico e aplica reduções com base em regras e condições predefinidas.

![Entrar política de risco] (./media/active-directory-identityprotection/1014.png "Entrar política de risco")


Proteção de identidade AD Azure ajuda você a gerenciar a atenuação de sinal-ins arriscadas, permitindo que você:

- Configure os usuários e grupos a que a política se aplica: 

    ![Entrar política de risco] (./media/active-directory-identityprotection/1015.png "Entrar política de risco")


- Defina o risco entrar limite do nível (baixa, média ou alta) que aciona a política: 

    ![Entrar política de risco] (./media/active-directory-identityprotection/1016.png "Entrar política de risco")


- Defina os controles a serem aplicadas quando a política dispara:  

    ![Entrar política de risco] (./media/active-directory-identityprotection/1017.png "Entrar política de risco")
    

- Alterne o estado de sua política:

    ![Registro MFA] (./media/active-directory-identityprotection/403.png "Registro MFA")

- Revise e avaliar o impacto de uma alteração antes de ativá-lo: 

    ![Entrar política de risco] (./media/active-directory-identityprotection/1018.png "Entrar política de risco")


### <a name="what-you-need-to-know"></a>O que você precisa saber

Você pode configurar uma política de segurança de risco entrar para exigir autenticação multifator:

![Entrar política de risco] (./media/active-directory-identityprotection/1017.png "Entrar política de risco")

No entanto, por razões de segurança, essa configuração funciona somente para usuários que já tenham sido registrados para autenticação multifator. Se a condição exigir autenticação multifator estiver satisfeita para um usuário que ainda não esteja registrado para autenticação multifator, o usuário está bloqueado. 

Como prática recomendada, se você quiser exigir autenticação multifator para entrada arriscadas-ins, faça o seguinte:

1. Habilite a [política de registro de autenticação multifator](#multi-factor-authentication-registration-policy) para os usuários afetados.
2. Exija que os usuários afetados para efetuar login uma sessão não arriscada para executar um registro MFA

Concluir essas etapas garante que a autenticação multifator é necessária para um entrar arriscada. 


### <a name="best-practices"></a>Práticas recomendadas

 
Escolher um limite **alto** reduz o número de vezes que uma política é disparada e minimiza o impacto para os usuários.  
 
No entanto, ele exclui **baixo** e **Médio** entrada suplementos sinalizados para risco da política, que não pode bloquear um invasor explore uma identidade comprometida. 

Ao definir a política, 

- Excluir usuários que não / não podem ter autenticação multifator

- Excluir usuários em locais onde habilitando a política não é prático (por exemplo, nenhum acesso a assistência técnica)

- Excluir usuários que tendem a gerar muitos falsos positivos (desenvolvedores, analistas de segurança)

- Use um limite **alto** durante a política inicial implementar o, ou se você deve minimizar desafios vistos pelos usuários finais.

- Use um limite de **baixa** se sua organização exigir maior segurança. Selecionar um limite de **baixo** introduz usuário adicionais entrar desafios, mas maior segurança.

O padrão recomendado para a maioria das organizações é configurar uma regra para um limite de **mídia** para obter um equilíbrio entre usabilidade e segurança.

 
A política de entrada risco é:

- Aplicado a todo o tráfego do navegador e suplementos logon usando autenticação moderna.
- Não é aplicado aos aplicativos usando protocolos de segurança mais antigos desabilitando o ponto de extremidade WS-Trust em IDP federado, como parte do ADFS.

A página de **Eventos de risco** no console de proteção de identidade lista todos os eventos:

- Esta política foi aplicada à
- Você pode examinar a atividade e determinar se a ação foi apropriada ou não 

Para obter uma visão geral da experiência do usuário relacionados, consulte:

- [Recuperação de entrar arriscada](active-directory-identityprotection-flows.md#risky-sign-in-recovery) 

- [Entrar arriscada bloqueado](active-directory-identityprotection-flows.md#risky-sign-in-blocked)  

- [Registro de autenticação multifator durante um entrar arriscada](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)  





**Para abrir a caixa de diálogo de configuração relacionados**:

1. Na lâmina **proteção de identidade do Azure AD** , na seção **Configurar** , clique em **entrar diretiva de risco**.

    ![Política de ridk de usuário] (./media/active-directory-identityprotection/1014.png "Política de ridk de usuário")





## <a name="multi-factor-authentication-registration-policy"></a>Política de registro de autenticação multifator

Azure autenticação multifator é um método de verificar quem é você que requer o uso de mais do que apenas um nome de usuário e senha. Ele fornece uma segunda camada de segurança para usuário entrada e transações.  
Recomendamos que você exige o Azure autenticação multifator para inscrições de usuário porque ele:

- Fornece autenticação forte com uma variedade de opções de verificação de fácil

- Desempenha um papel fundamental preparar sua organização para proteger e recuperar da conta compromete

![Política de ridk de usuário] (./media/active-directory-identityprotection/1019.png "Política de ridk de usuário")



Para obter mais detalhes, consulte [o que é a autenticação multifator Azure?](../multi-factor-authentication/multi-factor-authentication.md)


Proteção de identidade AD Azure ajuda você a gerenciar o rolo-out de registro de autenticação multifator Configurando uma política que permite que você: 



- Configure os usuários e grupos a que a política se aplica: 

    ![Política MFA] (./media/active-directory-identityprotection/1020.png "Política MFA")



- Definir os controles a serem aplicadas quando a política dispara:  

    ![Política MFA] (./media/active-directory-identityprotection/1021.png "Política MFA")


- Alterne o estado de sua política:

    ![Política MFA] (./media/active-directory-identityprotection/403.png "Política MFA")

- Exiba o status atual do registro: 

    ![Política MFA] (./media/active-directory-identityprotection/1022.png "Política MFA")


Para obter uma visão geral da experiência do usuário relacionados, consulte:

- [Fluxo de registro de autenticação multifator](active-directory-identityprotection-flows.md#multi-factor-authentication-registration).  

- [Registro de autenticação multifator durante um entrar arriscada](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in).  





**Para abrir a caixa de diálogo de configuração relacionados**:

1. Na lâmina **proteção de identidade do Azure AD** , na seção **Configurar** , clique em **registro de autenticação multifator**.

    ![Política MFA] (./media/active-directory-identityprotection/1019.png "Política MFA")





## <a name="next-steps"></a>Próximas etapas

 - [Canal 9: Azure AD e identidade de slides: visualização de proteção de identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [Tipos de eventos de risco detectados pelo Azure Active Directory proteção de identidade](active-directory-identityprotection-risk-events-types.md)
 - [Vulnerabilidades detectadas pelo Azure Active Directory proteção de identidade](active-directory-identityprotection-vulnerabilities.md)
 - [Notificações do Azure Active Directory de proteção de identidade](active-directory-identityprotection-notifications.md)
 - [Manual do Azure Active Directory de proteção de identidade](active-directory-identityprotection-playbook.md)
 - [Glossário do Azure Active Directory de proteção de identidade](active-directory-identityprotection-glossary.md)

 - [Entrar experiências com proteção de identidade do Azure AD](active-directory-identityprotection-flows.md)

 - [Ativar a proteção de identidade do Active Directory do Azure](active-directory-identityprotection-enable.md)
 - [Azure Active Directory identidade proteção de - como desbloquear usuários](active-directory-identityprotection-unblock-howto.md)

 - [Introdução ao Azure Active Directory proteção de identidade e Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)


