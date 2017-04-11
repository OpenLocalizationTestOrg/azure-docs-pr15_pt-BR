<properties
    pageTitle="Manual do Azure Active Directory de proteção de identidade | Microsoft Azure"
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
    ms.date="08/22/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection-playbook"></a>Manual do Azure Active Directory de proteção de identidade 

Este manual ajuda você a:

- Preencher dados no ambiente de proteção de identidade por vulnerabilidades e simulando eventos de risco
- Configurar as políticas de acesso condicional baseado em risco e testar o impacto dessas diretivas


## <a name="simulating-risk-events"></a>Simular eventos de risco

Esta seção fornece etapas para simular os seguintes tipos de evento de risco:

- Suplementos de entrada de endereços IP anônimos (fácil)
- Suplementos de entrada de locais desconhecidos (moderada)
- Viagem impossível para locais atípicos (difícil)

Outros eventos de risco não podem ser simulados de maneira segura.


### <a name="sign-ins-from-anonymous-ip-addresses"></a>Suplementos de entrada de endereços IP anônimos

Este tipo de evento de risco identifica os usuários que têm conectado com êxito de um endereço IP que foi identificado como um endereço IP de proxy anônimo. Esses proxies são usados por pessoas que deseja ocultar o endereço IP do seu dispositivo e podem ser usadas intenções maliciosas.

**Para simular uma entrada de um IP anônimo, execute as seguintes etapas**:

1.  Baixe o [Navegador de com](https://www.torproject.org/projects/torbrowser.html.en).
2.  Usando o Tor Browser, navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com).   
3.  Digite as credenciais da conta que você deseja que apareça no relatório de **suplementos de entrada de endereços IP anônimos** .

O logon do aparecerá no painel de proteção de identidade dentro de 5 minutos. 


###<a name="sign-ins-from-unfamiliar-locations"></a>Suplementos de entrada de locais desconhecidos

O risco de locais familiarizado é um mecanismo de avaliação de entrar em tempo real que considera passaram locais de entrada (IP, Latitude / Longitude e ASN) para determinar o novo / familiarizados locais. O sistema armazena IPs anterior, Latitude / Longitude e ASNs de um usuário e considera esses arquivos para ser familiares locais. Um local de entrada é considerado familiarizado se o local de entrada não corresponder a qualquer um dos locais familiares existentes.

Proteção de identidade do Active Directory do Azure:  

 - tem um período inicial de aprendizagem de 14 dias durante os quais ele não sinalizar qualquer novos locais como locais desconhecidos.
 - ignora suplementos de entrada de dispositivos conhecidos e locais que estão geograficamente Fechar para um local familiar existente.

Para simular familiarizados locais, você precisa entrar de um local e o dispositivo que a conta não entrou de antes. 


**Para simular uma entrada de um local familiarizado, execute as seguintes etapas**:

1.  Escolha uma conta que tenha pelo menos um histórico de entrada de 14 dias. 

2.  Faça:
    
    a. Ao usar uma VPN, navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com) e digite as credenciais da conta que você deseja simular o evento de risco para.

    b. Solicite um colega em um local diferente para entrar usando as credenciais da conta (não recomendadas).

O logon do aparecerá no painel de proteção de identidade dentro de 5 minutos.
 
### <a name="impossible-travel-to-atypical-location"></a>Impossível viagem atípico local
Simular a condição de viagem impossível é difícil porque o algoritmo usa para eliminar falsos positivos como impossível viagem de dispositivos conhecidos ou suplementos de sinal de VPNs que são usados por outros usuários no diretório de aprendizado de máquina. Além disso, o algoritmo requer um histórico de entrada de 3 a 14 dias para o usuário antes que ela comece gerando eventos de risco.

**Para simular uma viagem impossível para atípico local, execute as seguintes etapas**:

1.  Usando o navegador padrão, navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com).  

2.  Digite as credenciais da conta à qual para que você deseja gerar um evento de risco viagem impossível.

3.  Altere seu agente de usuário. Você pode alterar o agente de usuário no Internet Explorer das ferramentas de desenvolvedor do ou alterar seu agente de usuário no Firefox ou no Chrome usando um complemento de seletores de agente de usuário.

4.  Altere seu endereço IP. Você pode alterar seu endereço de IP usando uma VPN, um complemento Tor, ou girando uma nova máquina no Azure em uma central de dados diferentes.

5.  Entrar para [https://myapps.microsoft.com](https://myapps.microsoft.com) usando as mesmas credenciais como antes e em poucos minutos após a entrada anterior.

O logon do aparecerá no painel de proteção de identidade dentro de 2 a 4 horas.<br>
Devido a modelos envolvidos de aprendizado de máquina complexa, há uma chance que ele será não estão selecionado.<br> Talvez você queira replicar estas etapas para várias contas do Azure AD.


## <a name="simulating-vulnerabilities"></a>Simular vulnerabilidades 
Vulnerabilidades são fraquezas em um ambiente do Azure AD que podem ser exploradas por um ator incorreto. Atualmente 3 tipos de vulnerabilidades forem reproduzidos em proteção de identidade do Azure AD que utilizam outros recursos do Azure AD. Essas vulnerabilidades serão exibidas no painel de proteção de identidade automaticamente depois que esses recursos estão configurados.

-   Azure AD [autenticação multifator?](../multi-factor-authentication/multi-factor-authentication.md)
-   Azure AD [Descoberta de aplicativo de nuvem](active-directory-cloudappdiscovery-whatis.md).
-   [Gerenciamento de identidades privilegiado](active-directory-privileged-identity-management-configure.md)do Azure AD. 



##<a name="user-compromise-risk"></a>Risco de compromisso do usuário

**Para testar o risco de compromisso do usuário, execute as seguintes etapas**:

1.  Entrar no [https://portal.azure.com](https://portal.azure.com) com credenciais de administrador global do seu locatário.

2.  Navegue até a **proteção de identidade**. 

3.  Na lâmina **proteção Azure AD identidade** principal, clique em **configurações**. 

4.  Na lâmina **Configurações do Portal** , em **regras de segurança**, clique em **usuário comprometer risco**. 

5.  Na lâmina **entrar risco** , desativar a **Habilitar a regra** e clique em **Salvar** configurações.

6.  Para uma conta de usuário específico, simular uma familiarizado locais ou anônimo evento de risco IP. Isto irá elevar o nível de risco do usuário para esse usuário para **média**.

7.  Aguarde alguns minutos e, em seguida, verifique se o nível de usuário para o usuário é **Médio**.

8.  Vá para a lâmina de **Configurações do Portal** .

9.  **Na lâmina **Usuário compromisso risco** , em **Habilitar regra**, selecione.** 

10. Selecione uma das seguintes opções:

    a. Para bloquear, selecione **Médio** em **bloco entrar**.

    b. Para impor a alteração de senha de segurança, selecione **Médio** em **exigir autenticação multifator**.

13. Clique em **Salvar**.

14. Agora você pode testar o acesso condicional baseado em risco entrando no usando um usuário com um nível de risco elevado. Se o risco de usuário é Medium, dependendo da configuração de sua política, seu entrar é seja bloqueada ou você é forçado a alterar sua senha. 
<br><br>
![Manual](./media/active-directory-identityprotection-playbook/201.png "Playbook")
<br>

 
##<a name="sign-in-risk"></a>Entrar risco

 
**Para testar um sinal em risco, execute as seguintes etapas:**

1.  Entrar no [https://portal.azure.com](https://portal.azure.com) com credenciais de administrador global do seu locatário.

2.  Navegue até a **proteção de identidade**.

3.  Na lâmina **proteção Azure AD identidade** principal, clique em **configurações**. 

4.  Na lâmina **Configurações do Portal** , em **regras de segurança**, clique em **entrar em risco**.

5.  Na lâmina **entrar risco **, selecione **em** **Habilitar a regra**. 

7.  Selecione uma das seguintes opções:

    a. Para bloquear, selecione **Médio** em **bloco entrar**

    b. Para impor a alteração de senha de segurança, selecione **Médio** em **exigir autenticação multifator**.

8.  Para bloquear, selecione médio em entrar de bloco.

9.  Para impor a autenticação multifator, selecione **Médio** em **exigir autenticação multifator**.

10. Clique em **Salvar**.

11. Agora você pode testar o acesso condicional baseado em risco por simular a locais desconhecidos ou eventos de risco IP anônimos porque eles são os dois eventos de risco **Médio** .

<br>
![Manual](./media/active-directory-identityprotection-playbook/200.png "Playbook")
<br>


## <a name="see-also"></a>Consulte também

 - [Proteção de identidade do Active Directory do Azure](active-directory-identityprotection.md)
