<properties
    pageTitle="Azure AD Connect: Usuário entrar | Microsoft Azure"
    description="Azure AD Connect usuário entrar para configurações personalizadas."
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
    ms.date="10/16/2016"
    ms.author="billmath"/>



# <a name="azure-ad-connect-user-sign-on-options"></a>Azure AD conectar usuário logon opções

Azure AD Connect permite que os usuários se inscrevam aos recursos de nuvem e local usando as mesmas senhas. Este artigo descreve conceitos-chave para cada modelo de identidade para ajudá-lo a escolher a identidade que você deseja usar para sua entrada no Azure AD.

Se você já estiver familiarizado com o modelo de identidade do Azure AD e deseja saber mais sobre um método específico, basta clicar abaixo no tópico apropriado.

* [Sincronização de senha](#password-synchronization)
* [SSO federado (com ADFS)](#federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm)


## <a name="choosing-a-user-sign-in-method"></a>Escolhendo um método de entrada do usuário
Para a maioria das organizações que desejam apenas permitir que o usuário entre Office 365, aplicativos SaaS e outro Azure AD com base em recursos, a opção de sincronização de senha padrão é recomendada.
Algumas organizações, no entanto, tem determinados razões para usar um sinal de federados na opção como o AD FS.  Eles incluem:

- Sua organização já tiver do AD FS ou um 3º provedor de Federação implantado de terceiros
- Sua política de segurança impede sincronizando hash de senha para a nuvem
- Você exige que os usuários experiência perfeita SSO (sem prompts de senha adicional) quando acessar os recursos de nuvem do domínio ingressou máquinas na rede corporativa
- Você exige alguns recursos específicos que do AD FS tem
    - Autenticação multifator de local usando um provedor de terceiros ou cartões inteligentes (Saiba mais sobre provedores MFA de terceiros para o AD FS no Windows Server 2012 R2)
    - Recursos de integração do Active Directory como bloqueio de conta suaves ou diretiva de horas de trabalho e senha do AD
    - Condicional acesso aos dois locais e recursos usando o registro do dispositivo, junção Azure AD ou políticas Intune MDM em nuvem

### <a name="password-synchronization"></a>Sincronização de senha
Com a sincronização de senha, hash de senhas de usuário serão sincronizados com o Active Directory local Azure AD.  Quando as senhas são alteradas ou redefinir no local, as novas senhas são sincronizadas imediatamente ao Azure AD para que seus usuários sempre podem usar a mesma senha para os recursos de nuvem como faziam local.  As senhas nunca são enviadas ao Azure AD nem armazenadas no Azure AD em texto não criptografado.
Sincronização de senha pode ser usada em conjunto com senha regravação para habilitar o autoatendimento redefinição de senha de serviço no Azure AD.

<center>![Nuvem](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[Obter mais informações sobre a sincronização de senha](active-directory-aadconnectsync-implement-password-synchronization.md)


### <a name="federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm"></a>Federação usando um novo ou existente AD FS no Windows Server 2012 R2 farm
Com federados logon, seus usuários entrar em serviços do Azure AD com base com suas senhas de local e, enquanto estiver na rede corporativa, sem precisar digitar suas senhas novamente.  A opção de federação com o AD FS permite que você implantar uma nova ou especifique um existente do AD FS no Windows Server 2012 R2 farm.  Se você optar por especificar um farm existente, o Azure AD Connect configurará a relação de confiança entre o farm e Azure AD para que os usuários podem entrar em.

<center>![Nuvem](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="to-deploy-federation-with-ad-fs-in-windows-server-2012-r2-you-will-need-the-following"></a>Para implantar federação com o AD FS no Windows Server 2012 R2, você precisará do seguinte

Se você estiver implantando um novo farm:

- Um servidor do Windows Server 2012 R2 para o servidor de Federação.
- Um servidor do Windows Server 2012 R2 para o Proxy de aplicativo Web.
- um arquivo. pfx com um certificado SSL para o seu nome de serviço de Federação pretendido, como fs.contoso.com.

Se você estiver implantando um novo farm ou usando um farm existente:

- Credenciais de administrador local nos seus servidores de Federação.
- Credenciais de administrador local em qualquer grupo de trabalho (sem junção ao domínio) servidores no qual você pretende implantar a função de Proxy de aplicativo Web.
- O computador em que você executar o assistente deve ser capaz de se conectar a outras máquinas no qual você deseja instalar o AD FS ou Proxy de aplicativo Web por meio de gerenciamento remoto do Windows.

[Configurando SSO com o AD FS](./aad-connect/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) 

#### <a name="sign-on-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Entrar em utilizando uma versão anterior do AD FS ou uma solução de terceiros

Se você já configurou o sinal de nuvem em utilizando uma versão anterior do AD FS (como o AD FS 2.0) ou um provedor de federação de terceiros, você pode optar por ignorar a entrada do usuário em configuração via Azure AD Connect.  Isso permitirá que você obtenha a sincronização mais recente e outros recursos do Azure AD Connect enquanto estiver usando sua solução existente para entrar no.

[Lista de compatibilidade do Azure AD federação de terceiros](./active-directory-aadconnect-federation-compatibility.md)

## <a name="user-sign-in-and-user-principal-name-upn"></a>Acesso do usuário e nome de usuário principal (UPN)

### <a name="understanding-user-principal-name"></a>Noções básicas sobre UPN

No Active Directory, o sufixo UPN padrão é o nome DNS do domínio no qual conta de usuário criada. Na maioria dos casos, esse é o nome de domínio registrado como o domínio corporativo na Internet. No entanto, você pode adicionar mais sufixos UPN usando e relações de domínios do Active Directory.

O UPN do usuário está no formato username@domai. Por exemplo, um active directory contoso.com usuário John pode ter UPN john@contoso.com. O UPN do usuário baseia-se na RFC 822. Embora o email e UPN compartilhem o mesmo formato, o valor de nome UPN para um usuário pode ou não pode ser igual ao endereço de email do usuário.

### <a name="user-principal-name-in-azure-ad"></a>Nome de usuário principal no Azure AD

Assistente do Azure AD Connect deverá usar o atributo userPrincipalName ou permitem especificar o atributo (na instalação personalizada) para ser usado do local como o nome de usuário principal no Azure AD. Este é o valor que será usado para entrar no Azure AD. Se o valor do atributo de UPN do usuário não corresponder a um domínio verificado no Azure AD, em seguida, Azure AD substituirá com um padrão. onmicrosoft.com valor.

Cada diretório no Active Directory do Azure vem com um nome de domínio interno no contoso.onmicrosoft.com formulário que permite começar a usar o Azure ou outros serviços da Microsoft. Você pode melhorar e simplificar a experiência de entrada usando domínios personalizados. Para obter informações sobre o domínio personalizado nomes no Azure AD e como verificar um domínio, leia [Adicionar seu nome de domínio personalizado ao Azure Active Directory](active-directory-add-domain.md#add-your-custom-domain-name-to-azure-active-directory)

## <a name="azure-ad-sign-in-configuration"></a>Azure AD entrar configuração

### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Azure AD entrar configuração com Azure AD Connect
Azure AD processo de conexão é dependente Azure AD seja capaz de corresponder o sufixo UPN de um usuário que está sendo sincronizado com um dos domínios personalizados verificados no diretório Azure AD. Azure AD Connect fornece ajuda enquanto estiver configurando Azure AD entrar, para que entrar experiência do usuário na nuvem é semelhante à experiência no local. Azure AD Connect lista os sufixos UPN definidos para os domínios e tenta encontrar uma correspondência com um domínio personalizado no Azure AD e ajuda você a com a ação apropriada que precisa ser executada.
A página de entrada do Azure AD lista os sufixos UPN definidos para o Active Directory local e exibe o status correspondente em relação a cada sufixo. Os valores de status podem ser uma do abaixo:

| Estado | Descrição | Ação necessária |
|:----|:----|:----|
|Verificado| Azure AD Connect encontrou que uma correspondência verificado domínio no Azure AD. Todos os usuários para este domínio podem entrar usando suas credenciais de local| Nenhuma ação é necessária |
|Não verificado| Azure AD Connect pode encontrar um domínio personalizado correspondente no Azure AD, mas ela não será verificada. Sufixo UPN dos usuários deste domínio será alterado para padrão. onmicrosoft.com sufixo após sincronização se o domínio não será verificado. | Verifique o domínio personalizado no Azure AD. [Saiba Mais](./active-directory-add-domain.md#verify-the-domain-name-with-azure-ad) |  
|Não adicionado| Azure AD Connect não pôde encontrar um domínio personalizado correspondente ao sufixo UPN. Sufixo UPN de usuários deste domínio será alterado para padrão. onmicrosoft.com se o domínio não for adicionado e verifeid no Azure.| Adicionar e verificar um domínio personalizado correspondente ao sufixo UPN [Saiba mais](./active-directory-add-domain.md)|

Azure AD entrada na página de lista a suffix(s) UPN definido para o Active Directory local e o domínio personalizado correspondente no Azure AD com o status atual de verificação. Em instalação personalizada, agora você pode selecionar o atributo de nome de usuário principal na página de **entrada no Azure AD** .

![Azure AD-na página de entrada](./media/active-directory-aadconnect-user-signin/custom_azure_sign_in.png)

Você pode clicar no botão Atualizar para buscar novamente o status mais recente dos domínios personalizados do Azure AD.

###<a name="selecting-attribute-for-user-principal-name-in-azure-ad"></a>Selecionando o atributo de nome de usuário principal no Azure AD

UserPrincipalName - o atributo userPrincipalName é o atributo que os usuários utilizarão quando eles entrar no Azure AD e o Office 365. Os domínios usados, também conhecido como o-sufixo UPN, devem ser verificados no Azure AD antes que os usuários são sincronizados. É altamente recomendável manter o userPrincipalName de atributo padrão. Se esse atributo está não roteáveis e não pode ser verificado é possível selecionar outro atributo, por exemplo enviar por email, como o atributo mantendo o ID de entrada. Isso é conhecido como ID alternativo O valor do atributo ID alternativa deve acompanhar o padrão de RFC822. Uma ID alternativa pode ser usada com a federação SSO como a solução de entrada e de senha Single Sign-On (SSO).

>[AZURE.NOTE] Usar uma ID alternativa não é compatível com todas as cargas de trabalho do Office 365. Para obter mais informações, consulte [Configurar ID de logon alternativo](https://technet.microsoft.com/library/dn659436.aspx).

#### <a name="different-custom-domain-states-and-effect-on-azure-sign-in-experience"></a>Estados de domínio personalizado diferente e efeito na experiência de entrada Azure
É muito importante entender a relação entre os estados de domínio personalizado no seu diretório Azure AD e os sufixos UPN definido no local. Vamos dar uma olhada as diferentes possíveis Azure entrar experiências quando você estiver configurando a sincronização usando o Azure AD conectar.

Para obter as informações abaixo, vamos supor que estamos preocupados com o contoso.com de sufixo UPN que é usada no diretório local como parte do UPN, por exemplo user@contoso.com.

###### <a name="express-settings--password-synchronization"></a>Configurações expressas / sincronização de senha
| Estado         | Efeito na experiência do usuário entrar Azure |
|:-------------:|:----------------------------------------|
| Não adicionado | Nesse caso nenhum domínio personalizado para contoso.com foi adicionado no diretório do Azure AD. Usuários que têm o UPN local com sufixo @contoso.com, não será capaz de utilizar o UPN local para entrar no Azure. Em vez disso, eles precisarão usar um novo UPN fornecido a ele por Azure AD adicionando o sufixo do diretório do Azure AD padrão. Por exemplo, se você estiver sincronizando os usuários Azure AD diretório azurecontoso.onmicrosoft.com, em seguida, o usuário local user@contoso.com receberá um UPN deuser@azurecontoso.onmicrosoft.com|
| Não verificado | Neste caso temos um domínio personalizado contoso.com adicionadas no diretório do Azure AD, mas ele ainda não será verificado. Se você prosseguir com a sincronização usuários sem verificar o domínio, em seguida, os usuários serão atribuídos um novo UPN por Azure AD exatamente como fazia no cenário 'Não adicionado'.|
| Verificado | Nesse caso, temos um domínio personalizado contoso.com já adicionado e verificada no Azure AD para o sufixo UPN. Os usuários poderão usar seu locais UPN, por exemplo, user@contoso.com, para entrar no Azure depois que eles são sincronizados Azure AD|

###### <a name="ad-fs-federation"></a>Federação do AD FS
Não é possível criar uma federação com o padrão. domínio onmicrosoft.com no Azure AD ou um domínio personalizado não verificado no Azure AD. Quando você estiver executando o Assistente do Azure AD Connect, se você selecionar um domínio não verificado para criar uma federação com depois Azure AD Connect solicitará com registros necessários seja criado onde seu DNS está hospedado para o domínio. Para obter mais informações, consulte [aqui](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Se você selecionou entrar opção do usuário como "Federação com o AD FS", você deve ter um domínio personalizado para continuar com a criação de uma federação no Azure AD. Para nossa discussão, isso significa que podemos deve ter um domínio personalizado contoso.com adicionadas no diretório do Azure AD.

| Estado         | Efeito na experiência do usuário entrar Azure |
|:-------------:|:----------------------------------------|
| Não adicionado | Nesse caso Azure AD Connect não pôde encontrar um domínio personalizado correspondente para o contoso.com de sufixo UPN no diretório do Azure AD. Você precisa adicionar um domínio personalizado contoso.com se você precisar de usuários para entrar usando o AD FS com seu UPN local como user@contoso.com.|
| Não verificado | Nesse caso Azure AD Connect solicitará que você com apropriado detalhes sobre como você pode verificar seu domínio em um estágio posterior|
| Verificado | Neste caso você pode prosseguir com a configuração sem nenhuma providência|  

## <a name="changing-user-sign-in-method"></a>Alterar o método de entrada do usuário

Você pode alterar o método de entrada do usuário da federação para sincronização de senha usando o avaialble de tarefas no Azure AD Connect após a configuração inicial do Azure AD Connect usando o assistente. Execute o Assistente do Azure AD Connect novamente e você verá uma lista de tarefas que você pode executar. Selecione **Alterar usuário entrar** na lista de tarefas. 

![Alterar o acesso do usuário"](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Na próxima página, você será solicitado a fornecer as credenciais para Azure AD.

![Conectar ao Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

Na página de **entrada do usuário** , selecione **A sincronização de senha**. Isso alterará o diretório de federado para um gerenciado.

![Conectar ao Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin3.png)

>[AZURE.NOTE] Se você estiver criando apenas uma opção temporária à sincronização de senha, marque o **não converta contas de usuário**. Não verificar a opção conduzirá a conversão de cada usuário federado e ele pode levar horas.
  
## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).

Saiba mais sobre [Azure AD Connect: conceitos de Design](active-directory-aadconnect-design-concepts.md)


