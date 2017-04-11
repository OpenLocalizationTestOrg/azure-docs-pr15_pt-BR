<properties
    pageTitle="Servidor MFA com o Windows Server 2012 R2 AD FS | Microsoft Azure"
    description="Este artigo descreve como começar com autenticação multifator do Azure e AD FS no Windows Server 2012 R2."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>


# <a name="secure-your-cloud-and-on-premises-resources-using-azure-multi-factor-authentication-server-with-ad-fs-in-windows-server-2012-r2"></a>Proteger os seus recursos de nuvem e local usando o servidor de autenticação multifator Azure com o AD FS no Windows Server 2012 R2

Se você usa os serviços de Federação do Active Directory (AD FS) e deseja nuvem seguro ou recursos locais, você pode configurar o servidor de autenticação multifator do Azure para trabalhar com o AD FS. Essa configuração aciona a verificação para pontos de extremidade de alto valor.

Neste artigo, vamos abordar usando o servidor de autenticação multifator Azure com o AD FS no Windows Server 2012 R2. Para obter mais informações, leia sobre como [recursos seguros de nuvem e local usando o servidor de autenticação multifator Azure com o AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## <a name="secure-windows-server-2012-r2-ad-fs-with-azure-multi-factor-authentication-server"></a>Proteger o Windows Server 2012 R2 do AD FS com o servidor do Azure autenticação multifator

Quando você instala o servidor de autenticação multifator Azure, você tem as seguintes opções:

- Instalar o servidor de autenticação multifator Azure localmente no mesmo servidor do AD FS
- Instalar o adaptador de autenticação multifator de Azure localmente no servidor do AD FS e instale o servidor de autenticação multifator em um computador diferente

Antes de começar, lembre-se das seguintes informações:

- Não é necessário instalar o servidor de autenticação multifator Azure em seu servidor do AD FS. No entanto, você deve instalar o adaptador de autenticação multifator do AD FS no Windows Server 2012 R2 que está executando o AD FS. Você pode instalar o servidor em um computador diferente se é uma versão compatível e instalar o adaptador do AD FS separadamente no seu servidor de Federação do AD FS. Consulte os procedimentos a seguir para aprender a instalar o adaptador separadamente.

- Quando o adaptador de servidor de MFA AD FS foi criado, ele foi previsto que o AD FS pode passar o nome da terceira parte para o adaptador. Em seguida, o nome do participante terceira poderia ser usado como o nome de um aplicativo. No entanto, isso acontece não deve ser o caso. Se sua organização estiver usando métodos de verificação do aplicativo móvel ou mensagem de texto, as cadeias de caracteres definidas nas configurações de empresa contêm um espaço reservado, <$*application_name*$>. Esse espaço reservado não é substituído automaticamente quando você usa o adaptador do AD FS. Recomendamos que você remova o espaço reservado as cadeias de caracteres apropriadas ao proteger do AD FS.

- A conta que você usa para entrar no deve ter direitos de usuário para criar grupos de segurança no serviço do Active Directory.

- O Assistente de instalação do adaptador de autenticação multifator AD FS cria um grupo de segurança chamado PhoneFactor Admins na sua instância do Active Directory. Ele adiciona a conta de serviço do seu serviço de Federação do AD FS a esse grupo. Recomendamos que você verifique em seu controlador de domínio que o grupo PhoneFactor Admins realmente é criado e que a conta de serviço do AD FS é um membro desse grupo. Se necessário, adicione manualmente a conta de serviço do AD FS ao grupo PhoneFactor Admins no controlador de domínio.

- Para obter informações sobre como instalar o SDK do serviço da Web com o portal de usuário, leia sobre [Implantando o portal de usuário para servidor de autenticação multifator Azure.](multi-factor-authentication-get-started-portal.md)


### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>Instalar o servidor de autenticação multifator Azure localmente no servidor do AD FS

1. Baixe e instale o servidor de autenticação multifator Azure no seu servidor do AD FS. Para obter informações de instalação, leia sobre [como começar com o servidor de autenticação multifator do Azure](multi-factor-authentication-get-started-server.md).
2. No console de gerenciamento do servidor de autenticação multifator Azure, clique no ícone **do AD FS** e selecione as opções de **inscrição de usuário de permitir** e **Permitir que usuários para selecionar o método**.
3. Selecione as opções adicionais que você gostaria de especificar para sua organização.
4. Clique em **instalar o AD FS adaptador**.
<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Se a janela do Active Directory for exibida, isso significa que duas coisas. Seu computador está associado a um domínio e configuração do Active Directory para proteger a comunicação entre o adaptador do AD FS e o serviço de autenticação multifator está incompleta. Clique em **Avançar** para concluir essa configuração automaticamente ou selecione o **Ignorar a configuração automática do Active Directory e configurar manualmente** caixa de seleção e clique em **Avançar**.
6. Se as janelas de Grupo Local for exibido, isso significa que duas coisas. Seu computador não está associado a um domínio e a configuração de grupo local para proteger a comunicação entre o adaptador do AD FS e o serviço de autenticação multifator está incompleta. Clique em **Avançar** para concluir essa configuração automaticamente ou selecione o **Ignorar a configuração automática de Grupo Local e configurar manualmente** caixa de seleção e clique em **Avançar**.
7. No Assistente de instalação, clique em **Avançar**. Servidor de autenticação multifator Azure cria o grupo PhoneFactor Admins e adiciona a conta de serviço do AD FS ao grupo PhoneFactor Admins.
<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. Na página do **Instalador do início** , clique em **Avançar**.
9. No instalador do adaptador autenticação multifator AD FS, clique em **Avançar**.
10. Clique em **Fechar** quando a instalação estiver concluída.
11. Quando o adaptador tiver sido instalado, você deve registrá-lo com o AD FS. Abra o Windows PowerShell e execute o seguinte comando:<br>
    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
   <center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Para usar seu adaptador recém registrada, edite a política de autenticação global no AD FS. No console de gerenciamento do AD FS, vá para o nó **Diretivas de autenticação** . Na seção **Autenticação multifator** , clique no link **Editar** ao lado de seção **Configurações globais** . Na janela **Editar a política de autenticação Global** , selecione **Autenticação multifator** como um método de autenticação adicionais e clique em **Okey**. O adaptador está registrado como WindowsAzureMultiFactorAuthentication. Reinicie o serviço do AD FS para o registro seja efetivada.

<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

Neste ponto, o servidor de autenticação multifator estiver configurado para ser um provedor de autenticação adicionais para usar com o AD FS.

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>Instalar uma instância autônoma do adaptador do AD FS usando o SDK do serviço Web
1. Instale o SDK do serviço Web no servidor que está executando o servidor de autenticação multifator.
2. Copie os seguintes arquivos da \Program Files\Multi-diretório de servidor de autenticação de fator no servidor em que você planeja instalar o adaptador do AD FS:
  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Cancelar o registro-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config
3. Execute o arquivo de instalação MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. No instalador do adaptador autenticação multifator AD FS, clique em **Avançar** para iniciar a instalação.
5. Clique em **Fechar** quando a instalação estiver concluída.

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>Editar o arquivo MultiFactorAuthenticationAdfsAdapter.config

Siga estas etapas para editar o arquivo MultiFactorAuthenticationAdfsAdapter.config:

1. Defina o nó **UseWebServiceSdk** como **true**.  
2. Defina o valor de **WebServiceSdkUrl** para a URL do SDK de serviço de Web de autenticação multifator. Por exemplo: *https://contoso.com/&lt;certificatename&gt;/MultiFactorAuthWebServicesSdk/PfWsSdk.asmx*, onde certificatename é o nome do seu certificado.  
3. Edite o script de registro MultiFactorAuthenticationAdfsAdapter.ps1 adicionando *- ConfigurationFilePath &lt;caminho&gt; * até o final da `Register-AdfsAuthenticationProvider` comando, onde * &lt;caminho&gt; * é o caminho completo para o arquivo de MultiFactorAuthenticationAdfsAdapter.config.

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>Configurar o SDK do serviço da Web com um nome de usuário e senha

Há duas opções para configurar o SDK do serviço Web. A primeira é com um nome de usuário e senha, o segundo é com um certificado de cliente. Siga estas etapas para a primeira opção ou saltar para o segundo.  

1. Defina o valor para **WebServiceSdkUsername** para uma conta que seja um membro do grupo de segurança PhoneFactor Admins. Use o &lt;domínio&gt;& #92; &lt;nome de usuário&gt; formato.  
2. Defina o valor de **WebServiceSdkPassword** como a senha da conta apropriado.

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>Configurar o SDK do serviço da Web com um certificado de cliente

Se você não quiser usar um nome de usuário e senha, siga estas etapas para configurar o SDK do serviço da Web com um certificado de cliente.

1. Obter um certificado de cliente de uma autoridade de certificação para o servidor que está executando o SDK do serviço Web. Saiba como [obter certificados de cliente](https://technet.microsoft.com/library/cc770328.aspx).  
2. Importe o certificado de cliente para o armazenamento de certificado pessoal do computador local no servidor que está executando o SDK do serviço Web. Certifique-se de que o público certificado está no repositório de certificados de certificados de raiz confiável.  
3. Exporte as chaves pública e privadas do certificado do cliente para um arquivo. pfx.  
4. Exporte a chave pública no formato Base64 para um arquivo. cer.  
5. No Gerenciador do servidor, verifique se o recurso de autenticação de mapeamento de certificado de cliente do servidor Web (IIS) \Web Server\Security\IIS está instalado. Se não estiver instalado, selecione **Adicionar funções e recursos** para adicionar esse recurso.  
6. No Gerenciador do IIS, clique duas vezes em **Editor de configuração** do site que contém o diretório virtual SDK de serviço Web. É importante fazer isso no nível do site e não no nível do diretório virtual.  
7. Vá para a seção de **system.webServer/security/authentication/iisClientCertificateMappingAuthentication** .  
8. Defina enabled como **true**.  
9. Defina oneToOneCertificateMappingsEnabled como **true**.  
10. Clique no botão **…** próximo oneToOneMappings e, em seguida, clique no link **Adicionar** .  
11. Abra o arquivo. cer na Base 64 exportado anteriormente. Remova *---começar certificado---* *---Encerrar certificado---*e qualquer quebras de linha. Copie a cadeia de caracteres resultante.  
12. Definir o certificado à cadeia de caracteres copiado na etapa anterior.  
13. Defina enabled como **true**.  
14. Defina o nome de usuário para uma conta que seja um membro do grupo de segurança PhoneFactor Admins. Use o &lt;domínio&gt;& #92; &lt;nome de usuário&gt; formato.  
15. Definir a senha para a senha da conta apropriada e, em seguida, feche o Editor de configuração.  
16. Clique no link de **Aplicar** .  
17. No diretório virtual SDK de serviço Web, clique duas vezes em **autenticação**.  
18. Verifique se representação ASP.NET e autenticação básica estão definidas como **ativado**e que todos os outros itens são definidos como **desativado**.  
19. No diretório virtual SDK de serviço Web, clique duas vezes em **Configurações de SSL**.  
20. Defina os certificados de cliente para **Aceitar**e clique em **Aplicar**.  
21. Copie o arquivo. pfx exportado anteriormente no servidor que está executando o adaptador do AD FS.  
22. Importe o arquivo. pfx para o armazenamento de certificado pessoal do computador local.  
23. Clique com botão direito e selecione **Gerenciar chaves privada**e conceder acesso de leitura para a conta que você usou para entrar serviço do AD FS.  
24. Abra o certificado de cliente e copie a impressão digital na guia **detalhes** .  
25. No arquivo MultiFactorAuthenticationAdfsAdapter.config, defina **WebServiceSdkCertificateThumbprint** como a cadeia de caracteres copiada na etapa anterior.  


Finalmente, para registrar o adaptador, executar o \Program Files\Multi-fator de autenticação Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 script do PowerShell. O adaptador está registrado como WindowsAzureMultiFactorAuthentication. Reinicie o serviço do AD FS para o registro seja efetivada.

## <a name="related-topics"></a>Tópicos relacionados

Para obter ajuda de solução de problemas, consulte as [Perguntas frequentes do Azure multifator autenticação](multi-factor-authentication-faq.md)
