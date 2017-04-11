<properties
    pageTitle="Certificado de orientação de renovação para usuários do Office 365 e do Active Directory do Azure | Microsoft Azure"
    description="Este artigo explica a usuários do Office 365 como resolver problemas com emails notificação-los sobre como renovar um certificado."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Renovar certificados de federação para o Office 365 e o Active Directory do Azure

##<a name="overview"></a>Visão geral

Para a federação bem-sucedida entre Azure Active Directory (AD Azure) e os serviços de Federação do Active Directory (AD FS), os certificados usados pelo AD FS entre tokens de segurança para o Azure AD devem coincidir o que está configurado no Azure AD. Qualquer incompatibilidade pode levar a confiança interrompida. Azure AD garante que essas informações são mantidas em sincronia quando você implanta o AD FS e Proxy de aplicativo Web (para acesso extranet).

Este artigo fornece informações adicionais para gerenciar seu certificados de assinatura do token e mantê-las em sincronia com Azure AD, nos seguintes casos:

* Não estiver implantando o Proxy de aplicativo da Web e, portanto, os metadados de Federação não estão disponível no extranet.
* Você não está usando a configuração padrão do AD FS para token de certificados de assinatura.
* Você está usando um provedor de identidade de terceiros.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Configuração padrão do AD FS para certificados de assinatura do token

A autenticação de token e o símbolo descriptografar certificados são certificados autoassinados geralmente e são bons para um ano. Por padrão, o AD FS inclui um processo de renovação automática chamado **AutoCertificateRollover**. Se você estiver usando AD FS 2.0 ou posterior, Office 365 e Azure AD atualizar automaticamente seu certificado antes que ela expire.

### <a name="renewal-notification-from-the-office-365-portal-or-an-email"></a>Notificação de renovação do portal do Office 365 ou um email

>[AZURE.NOTE] Se você recebeu um email ou uma notificação portal pedindo para renovar seu certificado para o Office, consulte [Gerenciando muda para certificados de assinatura do token](#managecerts) para verificar se você precisa realizar qualquer ação. A Microsoft está ciente de um possível problema que pode levar notificações de renovação de certificado está sendo enviado, mesmo quando nenhuma ação é necessária.

Azure AD tenta monitorar os metadados de Federação e atualize o token de certificados de assinatura, conforme indicado por esses metadados. 30 dias antes da expiração do token certificados de assinatura Azure AD verifica se novos certificados disponíveis, pesquisando os metadados de Federação.

* Se ele pode pesquisar os metadados de Federação e recuperar os novos certificados com êxito, sem notificação por email ou aviso no portal do Office 365 é emitido para o usuário.
* Se não for possível recuperar o novo token certificados de assinatura, ou porque os metadados de Federação não são acessível ou sobreposição de certificado automática não estiver habilitada, Azure AD emite uma notificação por email e um aviso no portal do Office 365.

![Notificação de portal do Office 365](./media/active-directory-aadconnect-o365-certs/notification.png)

>[AZURE.IMPORTANT] Se você estiver usando AD FS, para garantir a continuidade de negócios, verifique se seus servidores tem as seguintes atualizações para que não ocorrem falhas de autenticação para problemas conhecidos. Isso reduz problemas conhecidos de servidor de proxy do AD FS para este períodos de renovação futuras e renovação:
>
>Server 2012 R2 - [Windows Server acúmulo de maio de 2014](http://support.microsoft.com/kb/2955164)
>
>Server 2008 R2 e 2012 - [Falha de autenticação de proxy no Windows 2008 R2 SP1 ou no Windows Server 2012](http://support.microsoft.com/kb/3094446)

## Verificar se os certificados precisam ser atualizados<a name="managecerts"></a>

### <a name="step-1-check-the-autocertificaterollover-state"></a>Etapa 1: Verificar o estado de AutoCertificateRollover

No seu servidor do AD FS, abra o PowerShell. Verifique se o valor de AutoCertificateRollover é definido como True.

    Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

[AZURE.NOTE] Se você estiver usando AD FS 2.0, primeiro execute Add-Pssnapin Microsoft.Adfs.Powershell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Etapa 2: Confirmar que o AD FS e Azure AD são sincronizados

No seu servidor do AD FS, abra o prompt do Azure AD PowerShell e se conectar ao Azure AD.

>[AZURE.NOTE] Você pode baixar o Azure AD PowerShell [aqui](https://technet.microsoft.com/library/jj151815.aspx).

    Connect-MsolService

Verificar os certificados configurados no AD FS e Azure AD confiar propriedades para o domínio especificado.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

Se as impressões digitais em ambas as saídas corresponderem, seus certificados são sincronizados com o Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Etapa 3: Verificar se seu certificado está prestes a expirar

Na saída do Get-MsolFederationProperty ou Get-AdfsCertificate, verifique a data em "Não após." Se a data for menor do que 30 dias ausente, você deve agir.

| AutoCertificateRollover | Certificados em sincronia com Azure AD | Metadados de Federação são publicamente acessível | Validade | Ação |
|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|
| Sim | Sim | Sim | - | Nenhuma ação necessária. Consulte [token de renovar automaticamente o certificado de autenticação](#autorenew). |
| Sim | Não  | - | Menos de 15 dias | Renove imediatamente. Consulte [manualmente o certificado de autenticação de token de renovação](#manualrenew). |
| Não | - | - | Menos de 30 dias | Renove imediatamente. Consulte [manualmente o certificado de autenticação de token de renovação](#manualrenew). |

\[Não importa -]

## Renovar o token automaticamente o certificado de autenticação (recomendado)<a name="autorenew"></a>

Você não precisará executar as etapas manuais se ambas as opções a seguir forem verdadeiras:
- Você implantou o Proxy de aplicativo da Web, que pode habilitar o acesso aos metadados federação de extranet.
- Você está usando a configuração padrão do AD FS (AutoCertificateRollover é habilitado).

Verifique o seguinte para confirmar que o certificado pode ser atualizado automaticamente.

**1. a propriedade do AD FS AutoCertificateRollover deve ser definida como True.** Isso indica que o AD FS gerará automaticamente a autenticação de token novo e certificados de descriptografia token, antes do antigo aquelas expirarem.

**2. os metadados de Federação do AD FS são acessível publicamente.** Verifique se os metadados de Federação são acessível publicamente, navegando até a seguinte URL de um computador na internet pública (pessoas de lá a rede corporativa):


/federationmetadata/2007-06/federationmetadata.xml https:// (your_FS_name)

onde `(your_FS_name) `é substituído com o nome de host do serviço de Federação sua organização usa, como fs.contoso.com.  Se você é capaz de verificar ambos dessas configurações com êxito, você não precisa fazer mais nada.  

Exemplo: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml

## Renovar o token manualmente o certificado de autenticação<a name="manualrenew"></a>

Você pode optar por renovar o token certificados de assinatura manualmente. Por exemplo, os cenários a seguir funcionam melhor para renovação manual:
* Token de certificados de assinatura são certificados autoassinados não. O motivo mais comum para isso é que sua organização gerencia certificados do AD FS registrados de uma autoridade de certificação organizacional.
* Segurança de rede não permite que os metadados de Federação esteja disponível publicamente.

Nesses cenários, sempre que você atualizar o token de assinatura certificados, você deve também atualizar seu domínio do Office 365 usando o comando do PowerShell, MsolFederatedDomain de atualização.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Etapa 1: Verifique se o AD FS tem novo token certificados de assinatura

**Configuração não padrão**

Se você estiver usando uma configuração não padrão do AD FS (onde **AutoCertificateRollover** é definida como **False**), você provavelmente está usando certificados personalizados (não auto-assinado). Para obter mais informações sobre como renovar o token do AD FS certificados de assinatura, consulte [orientação para os clientes que não usam AD FS auto-assinado certificados](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Metadados de Federação não estão disponível publicamente**

Por outro lado, se **AutoCertificateRollover** estiver definida como **True**, mas os metadados de Federação não são publicamente acessível, verifique primeiro que novos certificados de assinatura de token foram gerados pelo AD FS. Confirme que você tem novo token certificados de assinatura fazendo as seguintes etapas:

1. Verifique se que você está conectado ao servidor do AD FS primário.
2. Verificar os certificados de assinatura atuais no AD FS abrindo uma janela de comando do PowerShell e executando o seguinte comando:

    PS c:\>autenticação de token Get-ADFSCertificate – CertificateType

    >[AZURE.NOTE] Se você estiver usando AD FS 2.0, você deve executar Add-Pssnapin Microsoft.Adfs.Powershell primeiro.

3. Examine a saída de comando em todos os certificados listados. Se o AD FS gerou um novo certificado, você deve ver dois certificados na saída: uma para a qual o valor de **IsPrimary** é **verdadeiro** e a data de **NotAfter** é dentro de 5 dias e um para o qual **IsPrimary** é **False** e **NotAfter** é cerca de um ano no futuro.

4. Se você só verá um certificado e a data de **NotAfter** é dentro de 5 dias, você precisa gerar um novo certificado.

5. Para gerar um novo certificado, execute o seguinte comando no prompt de comando do PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.

6. Verifique a atualização executando o seguinte comando novamente: c: PS\>autenticação de token Get-ADFSCertificate – CertificateType

Dois certificados devem estar listados agora, um dos quais tem uma data de **NotAfter** de aproximadamente um ano no futuro e para que o valor de **IsPrimary** é **Falso**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Etapa 2: Atualizar o novo token assinatura certificados para a relação de confiança do Office 365

Atualize o Office 365 com o token de novato assinatura certificados a ser usado para a relação de confiança, da seguinte maneira.

1.  Abra o módulo do Microsoft Azure Active Directory para Windows PowerShell.
2.  Executar $cred = Get-Credential. Quando esse cmdlet solicita credenciais, digite suas credenciais de conta de administrador de serviço de nuvem.
3.  Executar conectar-MsolService – $cred de credenciais. Esse cmdlet, você se conecta ao serviço de nuvem. Criar um contexto que você se conecta ao serviço de nuvem é necessário antes de executar qualquer um dos cmdlets adicionais instalados pela ferramenta.
4.  Se você estiver executando esses comandos em um computador que não seja o servidor de Federação primária do AD FS, execute Set-MSOLAdfscontext-computador <AD FS primary server>, onde <AD FS primary server> é o nome FQDN interno do servidor do AD FS primário. Esse cmdlet cria um contexto que você se conecta a do AD FS.
5.  Executar a atualização MSOLFederatedDomain – nome_do_domínio <domain>. Esse cmdlet atualiza as configurações do AD FS no serviço de nuvem e configura a relação de confiança entre os dois.


>[AZURE.NOTE] Se você precisar oferecer suporte a vários domínios de nível superior, como contoso.com e fabrikam.com, você deve usar a opção **SupportMultipleDomain** com qualquer cmdlets. Para obter mais informações, consulte [suporte para vários domínios de nível superior](active-directory-aadconnect-multiple-domains.md).

## Reparar confiança Azure AD usando Azure AD Connect<a name="connectrenew"></a>

Se você configurou seu farm do AD FS e confiança do Azure AD usando Azure AD Connect, você pode usar o Azure AD Connect para detectar se você precisa nenhuma ação para o token de certificados de assinatura. Se você precisar renovar os certificados, você pode usar o Azure AD Connect para fazê-lo.

Para obter mais informações, consulte [Reparar a relação de confiança](./active-directory-aadconnect-federation-management.md#repairing-the-trust).
