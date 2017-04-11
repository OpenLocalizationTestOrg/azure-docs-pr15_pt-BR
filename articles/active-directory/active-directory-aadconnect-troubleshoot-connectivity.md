<properties
    pageTitle="Azure AD Connect: Solucionar problemas de conectividade | Microsoft Azure"
    description="Explica como solucionar problemas de conectividade com o Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="billmath"/>

# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Solucionar problemas de conectividade com o Azure AD Connect
Este artigo explica como funciona a conectividade entre Azure AD Connect e Azure AD e como solucionar problemas de conectividade. Esses problemas são mais prováveis para serem vistos em um ambiente com um servidor proxy.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Solucionar problemas de conectividade no Assistente de instalação
Azure AD Connect está usando autenticação moderno (usando a biblioteca de ADAL) para autenticação. O Assistente de instalação e o mecanismo de sincronização adequado requerem Machine. config para ser configurados corretamente, pois esses são os aplicativos .NET.

Neste artigo mostraremos como Fabrikam se conecta ao Azure AD por meio de seu proxy. O servidor proxy é chamado fabrikamproxy e está usando a porta 8080.

Primeiro, precisamos garantir [**Machine. config**](active-directory-aadconnect-prerequisites.md#connectivity) está configurado corretamente.  
![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

>[AZURE.NOTE]
Em alguns blogs não sejam da Microsoft, ele é documentado que as alterações devem ser feitas miiserver.exe.config em vez disso. No entanto, esse arquivo é substituído em cada atualização assim mesmo que se ele funciona durante a instalação inicial, o sistema deixarão de funcionar na primeira atualização. Por que motivo a recomendação é atualizar Machine. config em vez disso.

O servidor proxy também deve ter os URLs necessários abertos. Na lista oficial é documentada no [Office 365 URLs e intervalos de endereços IP ](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Desses, a tabela a seguir é o mínimo absoluto sejam capazes de se conectar ao Azure AD todo. Esta lista não inclui os recursos opcionais, como senha write-back ou Azure AD conectar integridade. Ele é documentado aqui para ajudar na solução de problemas para a configuração inicial.

URL | Porta | Descrição
---- | ---- | ----
mscrl.microsoft.com | HTTP/80 | Usado para baixar listas CRL.
\*. verisign.com | HTTP/80 | Usado para baixar listas CRL.
\*. entrust.com | HTTP/80 | Usado para baixar listas CRL para MFA.
\*. no windows.net | 443 DO HTTPS | Usado para entrar no Azure AD.
Secure.aadcdn.microsoftonline-p.com | 443 DO HTTPS | Usado para MFA.
\*. microsoftonline.com | 443 DO HTTPS | Usado para configurar seu diretório Azure AD e importação/exportação de dados.

## <a name="errors-in-the-wizard"></a>Erros do Assistente
O Assistente de instalação está usando dois contextos de segurança diferentes. Na página **conectar ao Azure AD** está usando o usuário conectado no momento. Na página **Configurar** ele está mudando para a [conta executando o serviço para o mecanismo de sincronização](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts). As configurações de proxy, podemos fazer são globais para o computador para que se houver um problema, o problema será mais provavelmente já aparecer na página **conectar ao Azure AD** no assistente.

Esses são os erros mais comuns que você verá no Assistente de instalação.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>O Assistente de instalação não foi configurado corretamente
Esse erro será exibida quando o assistente próprio não pode acessar o proxy.
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

- Se você vir isto, verifique se que o [config](active-directory-aadconnect-prerequisites.md#connectivity) foi configurado corretamente.
- Se que parecer correta, siga as etapas em [conectividade de proxy de verificar](#verify-proxy-connectivity) se o problema está presente fora do assistente também.

### <a name="the-mfa-endpoint-cannot-be-reached"></a>O ponto de extremidade MFA não pode ser acessado
Esse erro será exibida se o ponto de extremidade **https://secure.aadcdn.microsoftonline-p.com** não podem ser acessadas e o administrador global tem MFA habilitado.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

- Se você vir isto, verifique se que o ponto de extremidade secure.aadcdn.microsoftonline-p.com foi adicionada para o proxy.

### <a name="the-password-cannot-be-verified"></a>A senha não pode ser verificada
Se o Assistente de instalação for bem-sucedida em conectando ao Azure AD, mas a própria senha não pode ser verificada, você verá isto: ![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

- É a senha uma senha temporária e deve ser alterada? É realmente a senha correta? Tente entrar https://login.microsoftonline.com (em outro computador que o servidor do Azure AD Connect) e verifique se que a conta é útil.

### <a name="verify-proxy-connectivity"></a>Verificar a conectividade de proxy
Para verificar se o servidor do Azure AD Connect tem real conectividade com a Internet e o Proxy, usaremos algumas PowerShell para ver se o proxy está permitindo solicitações da web ou não. Em um prompt do PowerShell, execute `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Tecnicamente a primeira chamada é para https://login.microsoftonline.com e isso também funcionará, mas o outro URI é mais rápido responder.)

PowerShell usará a configuração em Machine. config entrar em contato com o proxy. As configurações no winhttp/netsh não devem afetar esses cmdlets.

Se o proxy estiver configurado corretamente, você deve obter um status de sucesso: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

Se você receber **não é possível conectar ao servidor remoto** depois PowerShell está tentando fazer uma chamada direta sem usar o proxy ou DNS não está configurado corretamente. Verifique se que o arquivo **Machine. config** está configurado corretamente.
![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Se o proxy não estiver configurado corretamente, podemos obterá um erro: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png)
![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

Erro | Texto de erro | Comentário
---- | ---- | ---- |
403 | Proibido | O proxy não foi aberto para o URL solicitada. Revise a configuração de proxy e verifique se as [URLs](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) tiver sido aberto.
407 | Autenticação de proxy necessária | O servidor proxy necessário entrar e nenhum foi fornecido. Se o seu servidor proxy requer autenticação, verifique se ter isso configurado no Machine. config. Verifique também se que você está usando contas de domínio para o usuário executando o assistente bem como para a conta de serviço.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>O padrão de comunicação entre Azure AD Connect e Azure AD
Se você tiver seguido todas essas etapas acima e ainda não conseguir se conectar neste momento você poderá começar examinando logs de rede. Esta seção está documentando um padrão de conectividade normal e bem-sucedido. Ele também é listagem herrings de vermelho comuns que podem ser ignorados se você estiver lendo os logs de rede.

- Haverá chamadas para https://dc.services.visualstudio.com. Ele não é necessário para esta aberto no proxy para a instalação tenha êxito e eles podem ser ignorados.
- Você verá que a resolução dns listará os hosts reais para estar em outros namespaces não em microsoftonline.com e a nsatc.net de espaço de nome DNS. Entretanto, não haverá qualquer solicitações de serviço web nos nomes de servidor real e você não precisa adicioná-los para o proxy.
- O adminwebservice de pontos de extremidade provisioningapi (veja abaixo os logs) pontos de extremidade de descoberta e são usadas para localizar o ponto de extremidade real para usar e será diferente dependendo de sua região.

### <a name="reference-proxy-logs"></a>Logs de proxy de referência
Aqui está um despejo de um log de proxy real e a página do Assistente de instalação de onde ela foi retirada (entradas duplicadas para o mesmo ponto de extremidade foram removidas). Isso pode ser usado como uma referência para seus próprio logs de proxy e da rede. Os pontos de extremidade reais podem ser diferentes no seu ambiente (em particular aqueles em *itálico*).

**Conectar ao Azure AD**

Tempo | URL
--- | ---
1/11/2016 8:31 | Connect://login.microsoftonline.com:443
1/11/2016 8:31 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:32 | conectar: / /*bba800-âncora*. microsoftonline.com:443
1/11/2016 8:32 | Connect://login.microsoftonline.com:443
1/11/2016 8:33 | Connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:33 | conectar: / /*bwsc02-relay*. microsoftonline.com:443

**Configurar**

Tempo | URL
--- | ---
1/11/2016 8:43 | Connect://login.microsoftonline.com:443
1/11/2016 8:43 | conectar: / /*bba800-âncora*. microsoftonline.com:443
1/11/2016 8:43 | Connect://login.microsoftonline.com:443
1/11/2016 8:44 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | conectar: / /*bba900-âncora*. microsoftonline.com:443
1/11/2016 8:44 | Connect://login.microsoftonline.com:443
1/11/2016 8:44 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | conectar: / /*bba800-âncora*. microsoftonline.com:443
1/11/2016 8:44 | Connect://login.microsoftonline.com:443
1/11/2016 8:46 | Connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:46 | conectar: / /*bwsc02-relay*. microsoftonline.com:443

**Sincronização inicial**

Tempo | URL
--- | ---
1/11/2016 8:48 | Connect://login.Windows.NET:443
1/11/2016 8:49 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:49 | conectar: / /*bba900-âncora*. microsoftonline.com:443
1/11/2016 8:49 | conectar: / /*bba800-âncora*. microsoftonline.com:443

## <a name="authentication-errors"></a>Erros de autenticação
Esta seção aborda erros que podem ser retornados de ADAL (a biblioteca de autenticação usada pelo Azure AD Connect) e PowerShell. O erro explicado deverá ajudá-lo na compreender as próximas etapas.

### <a name="invalid-grant"></a>Conceder inválido
Nome de usuário inválido ou senha. Para obter mais informações, consulte [a senha não pode ser verificada](#the-password-cannot-be-verified) .

### <a name="unknown-user-type"></a>Tipo de usuário desconhecido
Seu diretório Azure AD não pode ser encontrado ou resolvido. Talvez você tenta fazer logon com um nome de usuário em um domínio não verificado?

### <a name="user-realm-discovery-failed"></a>Falha de descoberta de território de usuário
Problemas de configuração de rede ou proxy. A rede não pode ser alcançado, consulte [Solucionar problemas de conectividade no Assistente de instalação](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>A senha do usuário expirou
Suas credenciais expiraram. Altere sua senha.

### <a name="authorizationfailure"></a>AuthorizationFailure
Problema desconhecido.

### <a name="authentication-cancelled"></a>Autenticação cancelada
O desafio de autenticação multifator (MFA) foi cancelado.

### <a name="connecttomsonline"></a>ConnectToMSOnline
Autenticação for bem sucedida, mas Azure AD PowerShell tem um problema de autenticação.

### <a name="azurerolemissing"></a>AzureRoleMissing
Autenticação foi bem-sucedido. Você não for um administrador global.

### <a name="privilegedidentitymanagement"></a>PrivilegedIdentityManagement
Autenticação foi bem-sucedido. Gerenciamento de identidades privilegiado foi ativado e atualmente você não for um administrador global. Consulte [Gerenciamento de identidades privilegiado](active-directory-privileged-identity-management-getting-started.md) para obter mais informações.

### <a name="companyinfounavailable"></a>CompanyInfoUnavailable
Autenticação foi bem-sucedido. Não foi possível recuperar informações da empresa do Azure AD.

### <a name="retrievedomains"></a>RetrieveDomains
Autenticação foi bem-sucedido. Não foi possível recuperar informações de domínio do Azure AD.

## <a name="troubleshooting-steps-for-previous-releases"></a>Etapas de solução de problemas para versões anteriores.
Com as versões começando com o número de compilação 1.1.105.0 (lançado fevereiro de 2016), o Assistente de conexão foi retirado. Esta seção e a configuração não deve ser necessários, mas é mantido como referência.

Para o logon único no Assistente para trabalhar, winhttp deve ser configurado. Isso pode ser feito com [**netsh**](active-directory-aadconnect-prerequisites.md#connectivity).  
![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>O Assistente de conexão não foi configurado corretamente
Este erro aparecerá quando o Assistente de entrada não é possível acessar o proxy ou o proxy não está permitindo a solicitação.
![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

- Se você vir isto, examine a configuração de proxy em [netsh](active-directory-aadconnect-prerequisites.md#connectivity) e verifique se que ele está correto.
![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
- Se que parecer correta, siga as etapas em [conectividade de proxy de verificar](#verify-proxy-connectivity) se o problema está presente fora do assistente também.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
