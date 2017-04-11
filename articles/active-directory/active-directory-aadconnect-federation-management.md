<properties
    pageTitle="Active Directory Federation Services Management e personalização com o Azure AD Connect | Microsoft Azure"
    description="Gerenciamento de FS AD com o Azure AD Connect e a personalização do AD FS entrar experiência do usuário com Azure AD Connect e PowerShell."
    keywords="O AD FS, ADFS, o AD FS gerenciamento, AAD conectar, conectar, entrar, o AD FS personalização, repare a federação de confiança, O365, terceira parte"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/01/2016"
    ms.author="anandy"/>

# <a name="active-directory-federation-services-management-and-customization-with-azure-ad-connect"></a>Gerenciamento de serviços de Federação do diretório ativo e personalização com o Azure AD Connect

Tarefas de detalhes neste artigo relacionados a serviços de Federação do Active Directory (AD FS) que podem ser executadas usando o Microsoft Azure Active Directory Connect, além de outras tarefas comuns do AD FS que podem ser necessárias para uma configuração completa de um farm do AD FS.

| Tópico | O que ele cubra |
|:------|:-------------|
|**Gerenciamento do AD FS**|
|[Reparar a relação de confiança](#repairthetrust)| Reparando a confiança de federação com o Office 365 |
|[Adicionar um servidor do AD FS](#addadfsserver) | Expandindo o farm do AD FS com um servidor do AD FS adicional|
|[Adicionar um servidor de proxy do aplicativo de web do AD FS](#addwapserver) | Expandindo o farm do AD FS com um servidor WAP adicional|
|[Adicionar um domínio federado](#addfeddomain)| Adicionando um domínio federado|
| **Personalização do AD FS**|
|[Adicionar um logotipo da empresa personalizado ou ilustração](#customlogo)| Personalizar uma página de entrada do AD FS com um logotipo da empresa e uma ilustração |
|[Adicionar uma descrição de entrada](#addsignindescription) | Adicionando uma descrição de página de entrada |
|[Modificar regras de declaração do AD FS](#modclaims) | Modificando declarações do AD FS para vários cenários de Federação |

## <a name="ad-fs-management"></a>Gerenciamento do AD FS

Azure AD Connect fornece várias tarefas relacionadas ao AD FS que podem ser executado usando o Assistente do Azure AD Connect com intervenção mínima do usuário. Após terminar de instalar o Azure AD Connect executando o assistente, você pode executar o assistente novamente para executar tarefas adicionais.

### Reparar a relação de confiança<a name=repairthetrust></a>

Azure AD Connect pode verificar a integridade atual da relação de confiança do AD FS e Active Directory do Azure e executar as ações apropriadas para reparar a relação de confiança. Siga estas etapas para reparar seu Azure AD e confiança do AD FS.

1. Selecione **Reparar AAD e ADFS confiar** na lista de tarefas adicionais.
![Reparar AAD e ADFS confiar](media\active-directory-aadconnect-federation-management\RepairADTrust1.PNG)

2. Na página **conectar ao Azure AD** , forneça suas credenciais de administrador global do Azure AD e clique em **Avançar**.
![Conectar ao Azure AD](media\active-directory-aadconnect-federation-management\RepairADTrust2.PNG)

3. Na página de **credenciais de acesso remoto** , insira as credenciais para o administrador do domínio.
![Credenciais de acesso remoto](media\active-directory-aadconnect-federation-management\RepairADTrust3.PNG)

    Depois de clicar em **Avançar**, o Azure AD Connect verificar integridade de certificado e mostrar qualquer problema.

    ![Estado de certificados](media\active-directory-aadconnect-federation-management\RepairADTrust4.PNG)

    **Pronto para configurar** página mostrará a lista de ações que serão executadas para reparar a relação de confiança.

    ![Pronto para configurar](media\active-directory-aadconnect-federation-management\RepairADTrust5.PNG)

4. Clique em **instalar** para reparar a relação de confiança.

>[AZURE.NOTE] Azure AD Connect pode apenas reparo ou agir em certificados autoassinados. Azure AD Connect não pode reparar certificados de terceiros.

### Adicionar um servidor do AD FS<a name=addadfsserver></a>

> [AZURE.NOTE] Azure AD Connect requer o arquivo de certificado PFX para adicionar um servidor do AD FS. Portanto, você pode executar essa operação somente se você configurou o farm do AD FS usando Azure AD Connect.

1. Selecione **implantar um servidor de Federação adicional** e clique em **Avançar**.
![Servidor de Federação adicionais](media\active-directory-aadconnect-federation-management\AddNewADFSServer1.PNG)

2. Na página **conectar ao Azure AD** , insira suas credenciais de administrador global para Azure AD e clique em **Avançar**.
![Conectar ao Azure AD](media\active-directory-aadconnect-federation-management\AddNewADFSServer2.PNG)

3. Fornece credenciais de administrador de domínio.
![Credenciais de administrador de domínio](media\active-directory-aadconnect-federation-management\AddNewADFSServer3.PNG)

4. Azure AD Connect solicitará a senha do arquivo PFX que você forneceu ao configurar seu novo farm do AD FS com o Azure AD Connect. Clique em **Digitar a senha** para fornecer a senha para o arquivo PFX.
![Senha do certificado](media\active-directory-aadconnect-federation-management\AddNewADFSServer4.PNG)

    ![Especifique um certificado SSL](media\active-directory-aadconnect-federation-management\AddNewADFSServer5.PNG)

5. Na página **Servidores do AD FS** , insira o nome do servidor ou o endereço IP a ser adicionado ao farm do AD FS.
![Servidores do AD FS](media\active-directory-aadconnect-federation-management\AddNewADFSServer6.PNG)

6. Clique em **Avançar** e dar uma olhada em **Configurar** página final. Após Azure AD Connect adicionando os servidores ao farm do AD FS, você terá a opção para verificar a conectividade.
![Pronto para configurar](media\active-directory-aadconnect-federation-management\AddNewADFSServer7.PNG)

    ![Instalação concluída](media\active-directory-aadconnect-federation-management\AddNewADFSServer8.PNG)

### Adicionar um servidor de proxy do aplicativo de web do AD FS<a name=addwapserver></a>

> [AZURE.NOTE] Azure AD Connect requer o arquivo de certificado PFX para adicionar um servidor de proxy de aplicativo web. Portanto, você poderá executar essa operação somente se você configurou o farm do AD FS usando Azure AD Connect.

1. Selecione **Implantar o Proxy de aplicativo Web** da lista de tarefas disponíveis.
![Implantar o proxy de aplicativo web](media\active-directory-aadconnect-federation-management\WapServer1.PNG)

2. Forneça as credenciais de administrador global Azure.
![Conectar ao Azure AD](media\active-directory-aadconnect-federation-management\wapserver2.PNG)

3. Na página **certificado SSL especificar** , forneça a senha do arquivo PFX que você forneceu ao configurar o farm do AD FS com o Azure AD Connect.
![Senha do certificado](media\active-directory-aadconnect-federation-management\WapServer3.PNG)

    ![Especifique um certificado SSL](media\active-directory-aadconnect-federation-management\WapServer4.PNG)

4. Adicione o servidor a ser adicionado como um proxy de aplicativo web. Porque o servidor de proxy de aplicativo da web não pode ser associado ao domínio, o assistente solicitará credenciais administrativas no servidor que está sendo adicionado.
![Credenciais do servidor administrativo](media\active-directory-aadconnect-federation-management\WapServer5.PNG)

5. Na página **credenciais de confiança de Proxy** , fornece credenciais administrativas para configurar a relação de confiança de proxy e acessar o servidor primário no farm do AD FS.
![Credenciais de confiança de proxy](media\active-directory-aadconnect-federation-management\WapServer6.PNG)

6. Na página **pronto para configurar** , o assistente mostra a lista de ações que serão realizadas.
![Pronto para configurar](media\active-directory-aadconnect-federation-management\WapServer7.PNG)

7. Clique em **instalar** para concluir a configuração. Após a configuração está concluída, o assistente oferece a você a opção para verificar a conectividade com os servidores. Clique em **Verificar** para verificar a conectividade.
![Instalação concluída](media\active-directory-aadconnect-federation-management\WapServer8.PNG)

### Adicionar um domínio federado<a name=addfeddomain></a>

É fácil adicionar um domínio seja federado com o Azure AD usando Azure AD Connect. Azure AD Connect adiciona o domínio para a federação e modifica as regras de declaração para refletir o emissor corretamente quando você tiver vários domínios federados com Azure AD.

1. Para adicionar um domínio federado, selecione a tarefa **Adicionar adicional domínio do Azure AD**.
![Adicionais domínio do Azure AD](media\active-directory-aadconnect-federation-management\AdditionalDomain1.PNG)

2. Na próxima página do assistente, forneça as credenciais de administrador global do Azure AD.
![Conectar ao Azure AD](media\active-directory-aadconnect-federation-management\AdditionalDomain2.PNG)

3. Na página de **credenciais de acesso remoto** , forneça o domínio credenciais de administrador.
![Credenciais de acesso remoto](media\active-directory-aadconnect-federation-management\additionaldomain3.PNG)

4. Na próxima página, o assistente fornecerá uma lista de domínios do Azure AD com o qual você pode federar seu diretório local. Escolha o domínio na lista.
![Domínio do Azure AD](media\active-directory-aadconnect-federation-management\AdditionalDomain4.PNG)

    Depois de escolher o domínio, o assistente fornecerá informações apropriadas sobre outras ações que o assistente levará e o impacto da configuração. Em alguns casos, se você selecionar um domínio que ainda não será verificado no Azure AD, o assistente fornecerá informações para ajudá-lo a verificar o domínio. Consulte [Adicionar seu nome de domínio personalizado ao Azure Active Directory](active-directory-add-domain.md) para obter mais detalhes.

5. Clique em **Avançar**e **pronto para configurar** página mostrará a lista de ações que Azure AD Connect executará. Clique em **instalar** para concluir a configuração.
![Pronto para configurar](media\active-directory-aadconnect-federation-management\AdditionalDomain5.PNG)

## <a name="ad-fs-customization"></a>Personalização do AD FS

As seções a seguir fornecem detalhes sobre algumas das tarefas comuns que você pode ter que executar quando personalizar sua página de entrada do AD FS.

### Adicionar um logotipo da empresa personalizado ou ilustração<a name=customlogo></a>

Para alterar o logotipo da empresa que é exibido na página de **entrada** , use o seguinte cmdlet do Windows PowerShell e sintaxe.

> [AZURE.NOTE] As dimensões recomendadas para o logotipo são 260 x 35 @ 96 dpi com um tamanho de arquivo não maior do que 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [AZURE.NOTE] O parâmetro *TargetName* é necessário. O tema padrão que é lançado com o AD FS é denominado padrão.


### Adicionar uma descrição de entrada<a name=addsignindescription></a>

Para adicionar uma descrição de página de entrada para a **página de entrada**, use o seguinte cmdlet do Windows PowerShell e sintaxe.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### Modificar regras de declaração do AD FS<a name=modclaims></a>

O AD FS oferece suporte a um idioma de declaração avançada que você pode usar para criar regras de declaração personalizada. Para obter mais informações, consulte [A função da linguagem de regra de declaração](https://technet.microsoft.com/library/dd807118.aspx).

As seções a seguir descrevem como você pode escrever regras personalizadas para alguns cenários relacionados à Azure AD e federação AD FS.

#### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>ID imutável condicional em um valor presente no atributo

Azure AD Connect permite especificar um atributo a ser usado como uma âncora de origem quando objetos são sincronizados com o Azure AD. Se o valor do atributo personalizado não estiver vazio, talvez você queira emitir uma declaração de ID imutável. Por exemplo, você pode selecionar **ms-ds-consistencyguid** como o atributo para a âncora de origem e deseja emitir **ImmutableID** como **ms-ds-consistencyguid** , caso o atributo tem um valor em relação a ele. Se não houver nenhum valor contra o atributo, emita **objectGuid** como a ID de imutável.  Você pode construir o conjunto de regras de declaração personalizada, conforme descrito na seção a seguir.

**Regra 1: Atributos de consulta**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Nessa regra, você está consultando os valores de **ms-ds-consistencyguid** e **objectGuid** do usuário do Active Directory. Altere o nome da loja para um nome de armazenamento apropriado na sua implantação do AD FS. Também altere o tipo de declarações para um tipo de declarações apropriado para sua federação conforme definido para **objectGuid** e **ms-ds-consistencyguid**.

Além disso, usando **Adicionar** e não o **problema**, você evite a adição de um problema de saída para a entidade e pode usar os valores como valores intermediários. Você irá emitir a declaração em uma regra posterior após estabelecer qual valor a ser usado como a ID de imutável.

**Regra 2: Verificar se ms-ds-consistencyguid existe para o usuário**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Essa regra define um sinalizador de temporário chamado **idflag** que é definido para **useguid** se não houver nenhuma **ms-ds-concistencyguid** preenchido para o usuário. A lógica por trás disso é o fato de que o AD FS não permite declarações vazias. Então quando você adiciona declarações http://contoso.com/ws/2016/02/identity/claims/objectguid e http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid na regra 1, terminar com um **msdsconsistencyguid** reivindicar somente se o valor é preenchido para o usuário. Se não estiver preenchido, o AD FS vê que ele terá um valor vazio e descarta-lo imediatamente. Todos os objetos terão **objectGuid**, para que essa declaração sempre estará lá após a regra 1 é executada.

**Regra 3: Emitir ms-ds-consistencyguid como ID imutável se ele estiver presente**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Isso é uma verificação de **Exist** implícita. Se o valor para a declaração existir, problema que como a ID de imutável. O exemplo anterior usa a declaração de **identificador de nome** . Você precisará alterá-lo para o tipo de declaração apropriado para imutável ID no seu ambiente.

**Regra 4: Emitir objectGuid como ID imutável se não houver ms-ds-consistencyGuid**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

Nessa regra, você simplesmente verificando o sinalizador temporários **idflag**. Decidir se emitir a declaração com base em seu valor.

> [AZURE.NOTE] A sequência dessas regras é importante.

#### <a name="sso-with-a-subdomain-upn"></a>SSO com um subdomínio UPN

Você pode adicionar mais de um domínio seja federado usando Azure AD Connect, conforme descrito em [Adicionar um novo domínio federado](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain). Você deve modificar de UPN para que a identificação de Emissor corresponde ao domínio raiz e não o subdomínio, porque o domínio raiz federados também aborda o filho.

Por padrão, a regra de declaração de ID do emissor é definida como:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Declaração de ID do emissor padrão](media\active-directory-aadconnect-federation-management\issuer_id_default.png)

A regra padrão simplesmente leva o sufixo UPN e utiliza a declaração de ID do emissor. Por exemplo, João é um usuário no sub.contoso.com e contoso.com for federado com Azure AD. Insere John john@sub.contoso.com como o nome de usuário ao entrar no Azure AD e a ID do emissor padrão reivindicar regra no AD FS trata da seguinte maneira.

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Reivindicar valor:** http://sub.contoso.com/adfs/services/trust/

Para ter apenas o domínio raiz no valor de declaração do emissor, altere a regra de declaração para corresponder a seguir.

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [Opções de entrada do usuário](active-directory-aadconnect-user-signin.md).
