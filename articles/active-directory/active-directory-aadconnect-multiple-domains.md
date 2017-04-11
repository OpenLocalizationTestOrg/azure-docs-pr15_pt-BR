<properties
    pageTitle="Azure AD Connect vários domínios"
    description="Este documento descreve instalar e configurar vários domínios de nível superior com o Office 365 e Azure AD."
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

# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Suporte a vários domínio para federar com o Azure AD
A documentação a seguir fornece orientação sobre como usar vários domínios de nível superior e subdomínios ao federar com o Office 365 ou domínios do Azure AD.

## <a name="multiple-top-level-domain-support"></a>Suporte a vários domínios de nível superior
Federar vários, domínios de nível superior com o Azure AD requer alguma configuração adicional que não é necessária ao federar com um domínio de nível superior.

Quando um domínio federado com o Azure AD, várias propriedades são definidas no domínio no Azure.  Uma importante é IssuerUri.  Este é um URI que é usado pelo Azure AD para identificar o domínio que o token está associado.  O URI não precisa resolver para nada que ele deve ser um URI válido.  Por padrão, Azure AD define como o valor do identificador de serviço de federação no seu local do AD FS configuração.

>[AZURE.NOTE]O identificador de serviço de Federação é um URI que identifica exclusivamente um serviço de Federação.  O serviço de Federação é uma instância do AD FS que funciona como o serviço de token de segurança. 

Você pode vew IssuerUri usando o comando do PowerShell `Get-MsolDomainFederationSettings - DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Um problema surge quando queremos adicionar mais de um domínio de nível superior.  Por exemplo, digamos que você tenha federação de configuração entre Azure AD e seu ambiente local.  Para este documento estou usando bmcontoso.com.  Agora posso adicionou um domínio de primeiro nível, segundo, bmfabrikam.com.

![Domínios](./media/active-directory-multiple-domains/domains.png)

Ao tentar converter nosso domínio bmfabrikam.com seja federado, podemos receber um erro.  O motivo para isso é, Azure AD tem uma restrição que não permite que a propriedade IssuerUri ter o mesmo valor para mais de um domínio.  
  

![Erro de Federação](./media/active-directory-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>Parâmetro de SupportMultipleDomain

Solução alternativa para isso, precisamos adicionar um IssuerUri diferente, que pode ser feito usando o `-SupportMultipleDomain` parâmetro.  Este parâmetro é usado com os seguintes cmdlets:
    
- `New-MsolFederatedDomain`
- `Convert-MsolDomaintoFederated`
- `Update-MsolFederatedDomain`

Este parâmetro torna Azure AD configurar o IssuerUri para que ele se baseia no nome do domínio.  Isso será exclusivo em diretórios no Azure AD.  Usar o parâmetro permite que o comando do PowerShell ser concluída com êxito.

![Erro de Federação](./media/active-directory-multiple-domains/convert.png)

Examinando as configurações de nosso novo domínio bmfabrikam.com, você pode ver o seguinte:

![Erro de Federação](./media/active-directory-multiple-domains/settings.png)

Observe que `-SupportMultipleDomain` não altera os outros pontos de extremidade que ainda são configurados para apontar para o nosso serviço de Federação em adfs.bmcontoso.com.

Outra coisa que `-SupportMultipleDomain` faz é que ele garante que o sistema do AD FS inclui o valor de emissor adequado em tokens emitidos para Azure AD. Isso é feito por fazendo a parte de domínio dos usuários UPN e configurá-lo como o domínio em IssuerUri, ou seja, o sufixo https://{upn} / serviços/adfs/confiabilidade. 

Portanto, durante a autenticação para Azure AD ou Office 365, o elemento IssuerUri no símbolo do usuário é usado para localizar o domínio no Azure AD.  Se uma correspondência não pode ser encontrada que a autenticação falhará. 

Por exemplo, se um usuário do UPN é bsimon@bmcontoso.com, o elemento IssuerUri os problemas do token AD FS será definido como http://bmcontoso.com/adfs/services/trust. Isto irá corresponder à configuração do Azure AD e autenticação terá êxito.

A regra de declaração personalizada que implementa essa lógica é o seguinte:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


>[AZURE.IMPORTANT]Para poder usar a opção - SupportMultipleDomain quando tentar adicionar novo ou converter já domínios adicionados, é necessário ter configuração sua confiança federada para suportá-los originalmente.  


## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Como atualizar a relação de confiança entre o AD FS e Azure AD
Se você não pode configurar a confiança federada entre AD FS e sua instância do Azure AD, você talvez precise recriar essa relação de confiança.  Isso ocorre porque, quando ele é originalmente configuração sem o `-SupportMultipleDomain` parâmetro, o IssuerUri está definida com o valor padrão.  A captura de tela abaixo, você pode ver que o IssuerUri está definido como https://adfs.bmcontoso.com/adfs/services/trust.

Agora, se nós adicionou com êxito um novo domínio no portal do Azure AD e, em seguida, tentar convertê-la usando `Convert-MsolDomaintoFederated -DomainName <your domain>`, podemos receber o seguinte erro.

![Erro de Federação](./media/active-directory-multiple-domains/trust1.png)

Se você tentar adicionar o `-SupportMultipleDomain` alternar podemos receberá o seguinte erro:

![Erro de Federação](./media/active-directory-multiple-domains/trust2.png)

Simplesmente tentando executar `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` no domínio original também resultará em um erro.

![Erro de Federação](./media/active-directory-multiple-domains/trust3.png)

Use as etapas abaixo para adicionar um domínio de primeiro nível adicional.  Se você já adicionou um domínio e não usou o `-SupportMultipleDomain` parâmetro começar com as etapas para remover e atualizando seu domínio original.  Se você não adicionou um domínio de primeiro nível ainda você pode começar com as etapas para adicionar um domínio usando o PowerShell do Azure AD Connect.

Use as etapas a seguir para remover a relação de confiança do Microsoft Online e atualizar o domínio original.

2.  No seu servidor de Federação do AD FS abrir **Gerenciamento do AD FS.** 
2.  À esquerda, expanda **Relações de confiança** e **Contar confia de festa**
3.  À direita, exclua a entrada de **Plataforma de identidade do Microsoft Office 365** .
![Remover o Microsoft Online](./media/active-directory-multiple-domains/trust4.png)
1.  Em um computador que tenha instalado o [Active Directory módulo Azure para Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) , execute o seguinte: `$cred=Get-Credential`.  
2.  Insira o nome de usuário e senha de um administrador global do domínio Azure AD com que você é federando
2.  No PowerShell insira`Connect-MsolService -Credential $cred`
4.  No PowerShell insira `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Isso é para o domínio original.  Isso usando os domínios acima seria:`Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`


Use as etapas a seguir para adicionar o novo domínio de nível superior usando o PowerShell

1.  Em um computador que tenha instalado o [Active Directory módulo Azure para Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) , execute o seguinte: `$cred=Get-Credential`.  
2.  Insira o nome de usuário e senha de um administrador global do domínio Azure AD com que você é federando
2.  No PowerShell insira`Connect-MsolService -Credential $cred`
3.  No PowerShell insira`New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Use as etapas a seguir para adicionar o novo domínio de nível superior usando Azure AD Connect.

1.  Iniciar o Azure AD Connect da área de trabalho ou no menu Iniciar
2.  Escolha "Adicionar um domínio AD Azure adicional" ![adicionar adicional domínio do Azure AD](./media/active-directory-multiple-domains/add1.png)
3.  Insira sua Azure AD e credenciais do Active Directory
4.  Selecione o segundo domínio que você deseja configurar para a federação.
![Adicionar um domínio do Azure AD](./media/active-directory-multiple-domains/add2.png)
5.  Clique em instalar


### <a name="verify-the-new-top-level-domain"></a>Verifique se o novo domínio de nível superior
Usando o comando do PowerShell `Get-MsolDomainFederationSettings - DomainName <your domain>`você pode exibir o IssuerUri atualizado.  Captura de tela abaixo mostra a Federação configurações foram atualizadas no nosso http://bmcontoso.com/adfs/services/trust domínio original

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

E o IssuerUri em nosso novo domínio tiver sido definida para https://bmfabrikam.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/settings2.png)


##<a name="support-for-sub-domains"></a>Suporte para subdomínios
Quando você adiciona um subdomínio, devido os domínios de maneira Azure AD manipulado, ele será herdam as configurações do pai.  Isso significa que o IssuerUri precisa coincidir com os pais.

Assim digamos, por exemplo que posso ter bmcontoso.com e, em seguida, adicionar corp.bmcontoso.com.  Isso significa que o IssuerUri de um usuário corp.bmcontoso.com precisam ser **http://bmcontoso.com/adfs/services/trust.**  No entanto, a regra padrão implementada acima para Azure AD, irá gerar um token com um emissor como **http://corp.bmcontoso.com/adfs/services/trust.** não coincidirão com o domínio que necessário valor e a autenticação falhará.

### <a name="how-to-enable-support-for-sub-domains"></a>Como habilitar o suporte para subdomínios
Para contornar esse problema o AD FS terceira confiança de terceiros para Microsoft Online precisa ser atualizado.  Para fazer isso, você deve configurar uma regra de declaração personalizada para que ela ignora qualquer subdomínios de sufixo UPN do usuário ao construir o valor de emissor personalizado. 

A declaração a seguir serão siga este procedimento:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));

Use as etapas a seguir para adicionar uma declaração personalizada para dar suporte a subdomínios.

1.  Abrir AD FS gerenciamento
2.  A relação de confiança do Microsoft Online RP clique com botão direito e escolha Editar declarar regras
3.  Selecione a terceira regra de declaração e substituir ![declaração de edição](./media/active-directory-multiple-domains/sub1.png)
4.  Substitua a declaração atual:
    
        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
        
    com
    
        `c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));`
    
![Substituir declaração](./media/active-directory-multiple-domains/sub2.png)
5.  Clique em Okey.  Clique em Aplicar.  Clique em Okey.  Feche o gerenciamento do AD FS.

