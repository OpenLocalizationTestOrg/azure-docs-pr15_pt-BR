<properties
    pageTitle="Configurar LDAP seguro (LDAPS) nos serviços de domínio do Azure AD | Microsoft Azure"
    description="Configurar LDAP seguro (LDAPS) para um domínio gerenciado de serviços de domínio do Azure AD"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurar LDAP seguro (LDAPS) para um domínio gerenciado de serviços de domínio do Azure AD
Este artigo mostra como você pode habilitar o protocolo de acesso diretório (LDAP) de Lightweight seguro do seu domínio de serviços de domínio do Azure AD gerenciado. LDAP seguro também é conhecido como ' LDAP Lightweight Directory Access Protocol () na Secure Sockets Layer (SSL) / segurança de camada de transporte (TLS)'.

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisa:

1. Uma **assinatura do Azure**válida.

2. Um **diretório do Azure AD** - seja sincronizado com um diretório local ou um diretório somente na nuvem.

3. **Serviços de domínio do Azure AD** deve estar habilitado para o diretório do Azure AD. Se você ainda não tiver feito isso, siga todas as tarefas descritas no [guia de Introdução](./active-directory-ds-getting-started.md).

4. Um **certificado a ser usado para habilitar LDAP seguro**.
    - **Recomendado** - obter um certificado de sua autoridade de certificação corporativa ou autoridade de certificação pública. Essa opção de configuração é mais segura.
    - Como alternativa, você também pode optar por [criar um certificado autoassinado](#task-1---obtain-a-certificate-for-secure-ldap) conforme mostrado neste artigo.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Requisitos para o certificado LDAP seguro
Adquira um certificado válido pelas diretrizes a seguir, antes de habilitar LDAP seguro. Você encontrar falhas se você tentar ativar LDAP seguro para seu domínio gerenciado com um certificado inválido/incorretos.

1. **Emissor confiável** - o certificado deve ser emitido por uma autoridade confiável para os computadores que precisam se conectar ao domínio usando LDAP seguro. Esta autoridade pode ser autoridade de certificação de empresa da sua organização ou uma autoridade de certificação pública confiável para esses computadores.

2. **Vida útil** - o certificado deve ser válido para pelo menos os próximos meses de 3 a 6. Acesso seguro de LDAP para seu domínio gerenciado é interrompido quando o certificado expirar.

3. **Nome de assunto** - o nome de assunto do certificado deve ser um caractere curinga do seu domínio gerenciado. Por exemplo, se seu domínio é denominado 'contoso100.com', o nome de assunto do certificado deve ser ' *. contoso100.com'. Defina o nome DNS (nome de entidade alternativo) para esse nome curinga.

3. **Uso da chave** - o certificado deve ser configurado para usar o seguinte - assinaturas digitais e codificação de chave.

4. **Finalidade do certificado** - o certificado deve ser válido para autenticação de servidor SSL.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Tarefa 1 - obter um certificado para LDAP seguro
A primeira tarefa envolve a obtenção de um certificado usado para o acesso seguro do LDAP ao domínio gerenciado. Você tem duas opções:

- Obter um certificado de uma autoridade de certificação. Autoridade pode ser da sua organização certificação empresarial ou uma autoridade de certificação pública.

- Crie um certificado autoassinado.


### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Opção (recomendado) - obter um certificado LDAP seguro de uma autoridade de certificação
Se sua organização implanta a infraestrutura de chave pública (PKI) uma empresa, você precisa obter um certificado enterprise autoridade de certificação (CA) para sua organização. Se sua organização obtém seus certificados de uma autoridade de certificação pública, você precisa obter um certificado LDAP seguro da autoridade de certificação pública.

Ao solicitar um certificado, certifique-se de que você siga os requisitos descritos [exigência para o certificado LDAP seguro](#requirements-for-the-secure-ldap-certificate).

> [AZURE.NOTE] Computadores cliente que precisam se conectar ao domínio gerenciado usando LDAP seguro devem confiar o emissor do certificado LDAPS.


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Opção B - criar um certificado autoassinado para LDAP seguro
Você pode optar por criar um certificado autoassinado para LDAP seguro, se:

- certificados em sua organização não são emitidos por uma autoridade de certificação corporativa ou
- Você não espera usar um certificado de uma autoridade de certificação pública.

**Criar um certificado autoassinado usando o PowerShell**

No seu computador com Windows, abra uma nova janela do PowerShell como **administrador** e digite os seguintes comandos, para criar um novo certificado auto-assinado.

    $lifetime=Get-Date

    New-SelfSignedCertificate -Subject *.contoso100.com -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment -Type SSLServerAuthentication -DnsName *.contoso100.com

No exemplo anterior, substitua 'contoso100.com' com o nome de domínio DNS do seu domínio de serviços de domínio do Azure AD gerenciado.

![Selecione Directory do Azure AD](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

O certificado auto-assinado recém-criado é colocado no repositório de certificados da máquina local.


## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>Tarefa 2: exportar o LDAP seguro de certificado para um. Arquivo PFX
Antes de começar essa tarefa, certifique-se de que você tiver obtido o certificado LDAP seguro da sua autoridade de certificação corporativa ou de uma autoridade de certificação pública ou criou um certificado auto-assinado.

Execute as seguintes etapas, para exportar o certificado LDAPS para um. Arquivo PFX.

1. Pressione o botão **Iniciar** e digite **R**. Na caixa de diálogo **Executar** , digite **mmc** e clique em **Okey**.

    ![Iniciar o console MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)

2. No prompt de **Controle de conta de usuário** , clique em **Sim** para iniciar o MMC (Console de gerenciamento da Microsoft) como administrador.

3. No menu **arquivo** , clique em **Adicionar/Remover Snap-in...**.

    ![Adicionar snap-in ao console MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)

4. Na caixa de diálogo **Adicionar ou Remover Snap-ins** , selecione o snap-in de **certificados** e clique o **Adicionar >** botão.

    ![Adicionar snap-in Certificados ao console MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)

5. No Assistente de **snap-in Certificados** , selecione a **conta do computador** e clique em **Avançar**.

    ![Adicionar snap-in de certificados para a conta de computador](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)

6. Na página **Selecionar computador** , selecione **computador Local: (o computador onde este console está sendo executado)** e clique em **Concluir**.

    ![Adicionar snap-in Certificados - selecione computador](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)

7. Na caixa de diálogo **Adicionar ou Remover Snap-ins** , clique em **Okey** para adicionar o snap-in de certificados ao MMC.

    ![Adicionar snap-in de certificados ao MMC - concluído](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)

8. Na janela do MMC, clique para expandir **Raiz do Console**. Você deve ver o snap-in Certificados carregado. Clique em **certificados (computador Local)** para expandir. Clique para expandir o nó **pessoal** , seguido o nó **certificados** .

    ![Repositório de certificados pessoais aberto](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)

9. Você deve ver o certificado auto-assinado criamos. Você pode examinar as propriedades do certificado para garantir que a impressão digital corresponda que relatados na windows PowerShell quando você criou o certificado.

10. Selecione o certificado auto-assinado e o **botão direito do mouse**. No menu de atalho, selecione **Todas as tarefas** e selecione **Exportar...**.

    ![Certificado de exportação](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)

11. No **Assistente para exportação de certificado**, clique em **Avançar**.

    ![Assistente de exportação do certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)

12. Na página **Exportar chave particular** , selecione **Sim, exportar a chave privada**e clique em **Avançar**.

    ![Exportar chave privada do certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [AZURE.WARNING] Você deve exportar a chave privada junto com o certificado. Se você fornecer um PFX que não contenha a chave privada para o certificado, permitindo que LDAP seguro para seu domínio gerenciado falhará.

13. Na página **Formato do arquivo de exportação** , selecione **troca de informações pessoais - PKCS #12 (. PFX)** como o formato de arquivo para o certificado exportado.

    ![Formato de arquivo de certificado de exportação](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [AZURE.NOTE] Somente o. Formato de arquivo PFX é suportado. Não exporte o certificado para o. Formato de arquivo CER.

14. Na página de **segurança** , selecione a opção de **senha** e digite uma senha para proteger a. Arquivo PFX. Lembre-se dessa senha desde que ele será necessário na próxima tarefa. Clique em **Avançar** para continuar.

    ![Senha para exportação de certificado ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [AZURE.NOTE] Anote esta senha. Você precisa permitindo LDAP seguro para este domínio gerenciado na [tarefa 3 - habilitar LDAP seguro para o domínio gerenciado](#task-3---enable-secure-ldap-for-the-managed-domain)

15. Na página **arquivo a ser exportado** , especifique o nome do arquivo e o local onde você deseja exportar o certificado.

    ![Caminho de exportação de certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)

16. Na página seguinte, clique em **Concluir** para exportar o certificado para um arquivo PFX. Você verá a caixa de diálogo de confirmação quando o certificado foi exportado.

    ![Exportar certificado concluído](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="task-3---enable-secure-ldap-for-the-managed-domain"></a>Tarefa 3 - habilitar LDAP seguro para o domínio gerenciado
Para habilitar LDAP seguro, execute as seguintes etapas de configuração:

1. Navegue até o **[portal clássico Azure](https://manage.windowsazure.com)**.

2. Selecione o nó do **Active Directory** no painel esquerdo.

3. Selecione o diretório do Azure AD (também conhecido como 'locatário'), para o qual você habilitou os serviços de domínio do Azure AD.

    ![Selecione Directory do Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Clique na guia **Configurar** .

    ![Configurar o guia do diretório](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Role para baixo até a seção intitulada **Serviços de domínio**. Você deve ver uma opção intitulada **LDAP seguro (LDAPS)** , conforme mostrado na seguinte captura de tela:

    ![Seção de configuração de serviços de domínio](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)

6. Clique no botão **Configurar certificado …** para exibir a caixa de diálogo **Configurar certificado para LDAP seguro** .

    ![Configurar um certificado para LDAP seguro](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)

7. Clique no ícone de pasta abaixo **PFX arquivo com certificado** para especificar o arquivo PFX, que contém o certificado que você deseja usar para acesso seguro do LDAP ao domínio gerenciado. Também, insira a senha que você especificou ao exportar o certificado para o arquivo PFX. Em seguida, clique no botão concluído na parte inferior.

    ![Especificar senha e arquivos PFX LDAP seguro](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)

8. A seção de **Serviços de domínio** do guia **Configurar** deve obter esmaecida e está na **pendente …** estado por alguns minutos. Durante esse período, o certificado LDAPS será verificado precisão e LDAP seguro é configurado para o seu domínio gerenciado.

    ![Proteger LDAP - pendente](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

    > [AZURE.NOTE] Ele leva cerca de 10 a 15 minutos para habilitar LDAP seguro para seu domínio gerenciado. Se o certificado LDAP seguro fornecido não coincidir com os critérios necessários, LDAP seguro não está habilitado para seu diretório e você verá uma falha. Por exemplo, o nome de domínio está incorreto, o certificado expirou ou expira breve etc.

9. Quando LDAP seguro com êxito está habilitado para seu domínio gerenciado, o **pendente …** mensagem deve desaparecer. Você deve ver a impressão digital do certificado exibido.

    ![LDAP seguro habilitado](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>


## <a name="task-4---enable-secure-ldap-access-over-the-internet"></a>Tarefa 4 - habilitar o acesso seguro LDAP pela internet
**Tarefa opcional** - se você não planeja acessar o domínio gerenciado usando LDAPS pela internet, pule esta tarefa de configuração.

Antes de começar essa tarefa, certifique-se de que você concluiu as etapas descritas na [tarefa 3](#task-3---enable-secure-ldap-for-the-managed-domain).

1. Você deve ver uma opção para **Habilitar seguro LDAP acesso pela INTERNET** na seção da página **Configurar** **Serviços de domínio** . Essa opção estiver definida como **não** por padrão, desde que o acesso à internet para o domínio gerenciado sobre LDAP seguro é desabilitado por padrão.

    ![LDAP seguro habilitado](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)

2. Botão de alternância **Habilitar o acesso de LDAP seguro através do INTERNET** como **Sim**. Clique no botão **Salvar** no painel inferior.
    ![LDAP seguro habilitado](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)

3. A seção de **Serviços de domínio** do guia **Configurar** deve obter esmaecida e está na **pendente …** estado por alguns minutos. Após alguns instantes, o acesso à internet para seu domínio gerenciado sobre LDAP seguro está habilitado.

    ![Proteger LDAP - pendente](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

    > [AZURE.NOTE] Ele leva cerca de 10 minutos para habilitar o acesso à internet por LDAP seguro para seu domínio gerenciado.

4. Quando acesso LDAP seguro para seu domínio gerenciado pela internet com êxito está habilitado, os **pendente …** mensagem deve desaparecer. Você deve ver o endereço IP externo que pode ser usado para acessar seu diretório LDAP no campo **Endereço de IP EXTERNOS para acesso de LDAPS**.

    ![LDAP seguro habilitado](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## <a name="task-5---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Tarefa 5 - configurar DNS para acessar o domínio gerenciado da internet
**Tarefa opcional** - se você não planeja acessar o domínio gerenciado usando LDAPS pela internet, pule esta tarefa de configuração.

Antes de começar essa tarefa, certifique-se de que você concluiu as etapas descritas na [tarefa 4](#task-4---enable-secure-ldap-access-over-the-internet).

Depois que você habilitou acesso LDAP seguro pela internet para o seu domínio gerenciado, você precisa atualizar o DNS para que os computadores cliente podem encontrar esse domínio gerenciado. No final da tarefa 4, um endereço IP externo é exibido na guia **Configurar** no **IP endereço para LDAPS acesso externo**.

Configure o seu provedor DNS externo para que o nome DNS do domínio gerenciado (por exemplo, ' contoso100.com') aponta para este endereço IP externo. Em nosso exemplo, precisamos criar a entrada DNS a seguir:

    contoso100.com  -> 52.165.38.113

Isso - agora você está pronto para se conectar ao domínio gerenciado usando LDAP seguro pela internet.

> [AZURE.WARNING] Lembre-se de que os computadores cliente devem confiar o emissor do certificado LDAPS para conseguir se conectar com êxito ao domínio gerenciado usando LDAPS. Se você estiver usando uma autoridade de certificação corporativa ou de uma autoridade de certificação confiável publicamente, você não precisa fazer mais nada, desde que esses emissores de certificados de confiança de computadores cliente. Se você estiver usando um certificado autoassinado, você precisa instalar a parte pública do certificado auto-assinado na loja de certificação confiável no computador cliente.

<br>

## <a name="related-content"></a>Conteúdo relacionado

- [Administrar um domínio gerenciado de serviços de domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)
