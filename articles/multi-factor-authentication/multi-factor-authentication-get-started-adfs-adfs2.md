<properties
    pageTitle="Usar o servidor do Azure MFA com o AD FS 2.0 | Microsoft Azure"
    description="Esta é a página de autenticação multifator do Azure que descreve como começar com o Azure MFA e AD FS 2.0."
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

# <a name="secure-cloud-and-on-premises-resources-using-azure-multi-factor-authentication-server-with-ad-fs-20"></a>Proteger os recursos de nuvem e local usando o servidor de autenticação multifator Azure com o AD FS 2.0

Este artigo é para as organizações que esteja federadas Active Directory do Azure e deseja proteger os recursos que são locais ou na nuvem. Proteger seus recursos usando o servidor de autenticação multifator do Azure e configurá-lo para trabalhar com o AD FS para que a verificação é acionada para pontos de extremidade de valor alto.

Esta documentação aborda usando o servidor de autenticação multifator Azure com o AD FS 2.0.  Obter mais informações sobre [recursos de nuvem e local de Protegendo usando o servidor de autenticação multifator Azure com o Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md).


## <a name="secure-ad-fs-20-with-a-proxy"></a>Proteja AD FS 2.0 com um proxy
Para proteger o AD FS 2.0 com um proxy, instale o servidor de autenticação multifator Azure no servidor proxy ADFS e configurar o servidor.

### <a name="configure-iis-authentication"></a>Configurar a autenticação do IIS

1. Em servidor de autenticação multifator Azure, clique no ícone de **Autenticação do IIS** no menu à esquerda.
2. Clique na guia **Baseados em formulário** .
3. Clique na **Adicionar...** botão.
<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. Detectar automaticamente o nome de usuário, senha e domínio variáveis, digite a URL de Login (como https://sso.contoso.com/adfs/ls) dentro da caixa de diálogo Auto-Configure Form-Based site e clique em Okey.
5. Marque a caixa de **usuário exigir autenticação do Azure multifator corresponder** se todos os usuários foram ou serão importados para o servidor e sujeitos a verificação em duas etapas. Se um número significativo de usuários ainda não foram importado para o servidor e/ou será isentar de verificação em duas etapas, deixe a caixa desmarcada. Para obter informações adicionais sobre este recurso, consulte o arquivo de Ajuda.
6. Se as variáveis de página não podem ser detectadas automaticamente, clique na **Especificar manualmente...** botão na caixa de diálogo Auto-Configure Form-Based site.
7. Na caixa de diálogo Add Form-Based site, digite a URL para a página de logon do ADFS no campo URL enviar (como https://sso.contoso.com/adfs/ls) e insira um nome de aplicativo (opcional). O nome do aplicativo aparece em relatórios de autenticação multifator de Azure e pode ser exibido em mensagens de autenticação de SMS ou aplicativo móvel. Consulte o arquivo de ajuda para obter mais informações sobre a URL de enviar.
8. Definir o formato de solicitação "Postar ou obter."
9. Insira a variável Username (ctl00$ ContentPlaceHolder1$ UsernameTextBox) e a variável de senha (ctl00$ ContentPlaceHolder1$ PasswordTextBox). Se sua página de login baseados em formulário exibe uma caixa de texto domínio, insira a variável de domínio também. Talvez seja necessário navegar até a página de login em um navegador da web, clique com botão direito na página e selecione **Exibir fonte** para localizar os nomes das caixas de entrada dentro da página de login.
10. Marque a caixa de **usuário exigir autenticação do Azure multifator corresponder** se todos os usuários foram ou serão importados para o servidor e sujeitos a verificação em duas etapas. Se um número significativo de usuários ainda não foram importado para o servidor e/ou será isentar de verificação em duas etapas, deixe a caixa desmarcada.
<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. Clique em **Avançado...** botão Revisar configurações avançadas. Você pode definir configurações incluindo a capacidade de selecionar um arquivo de página personalizado negação, para autenticação bem-sucedida no site usando cookies em cache e selecionar como autenticar as credenciais principais.
12. Desde que o servidor de proxy do AD FS não provavelmente ser associado ao domínio, você pode usar o LDAP para se conectar ao seu controlador de domínio para importação de usuário e pré-autenticação. Na caixa de diálogo Advanced Form-Based site, clique na guia **Autenticação primária** e selecione **Ligação LDAP** para o tipo de pré-autenticação autenticação.
13. Ao concluir, clique no botão **Okey** para retornar à caixa de diálogo Add Form-Based site. Consulte o arquivo de ajuda para obter mais informações sobre as configurações avançadas.
14. Clique no botão **Okey** para fechar a caixa de diálogo.
15. Depois que as variáveis de URL e página foram detectadas ou inseridas, os dados de site exibe no painel baseados em formulário.
16. Clique na guia **Módulo nativo** e selecione o servidor, o site que o proxy do AD FS estiver executando em (como "Site padrão") ou o aplicativo de proxy do AD FS (como "ls" em "adfs") para habilitar o plug-in do IIS o nível desejado.
17. Clique na caixa de **autenticação do IIS habilitar** na parte superior da tela.
18. A autenticação do IIS está ativada.

### <a name="configure-directory-integration"></a>Configurar a integração de diretório

Você habilitou a autenticação do IIS, mas para executar a autenticação de pré-lançamento para o Active Directory (AD) via LDAP, você deve configurar a conexão LDAP ao controlador de domínio.

1. Clique no ícone de **Integração de diretório** .
2. Na guia Configurações, selecione o botão de rádio de **configuração de LDAP específica de uso** .
<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
3. Clique na **Editar...** botão.
4. Na caixa de diálogo Editar configuração de LDAP, preencha os campos com as informações necessárias para se conectar ao controlador de domínio AD. Descrições dos campos são incluídas na tabela abaixo. Essas informações também estão incluídas no arquivo de Ajuda do servidor de autenticação multifator do Azure.
5. Teste a conexão LDAP clicando no botão de **teste** .
<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
6. Se o teste de conexão LDAP foi bem-sucedido, clique no botão **Okey** .

### <a name="configure-company-settings"></a>Configurar definições de empresa

1. Em seguida, clique no ícone **Configurações da empresa** e selecione a guia de **Resolução de nome de usuário** .
2. Selecione o botão de rádio de **atributo de identificador exclusivo de usar LDAP para nomes de usuário correspondente** .
3. Se os usuários inserir seu nome de usuário no formato "domínio \ nomedeusuário", o servidor precisa ser capaz de remover o domínio desativar o nome de usuário quando ele cria a consulta LDAP. Isso pode ser feito por meio de uma configuração de registro.
4. Abra o editor do registro e vá para HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/positivo redes/PhoneFactor em um servidor de 64 bits. Se estiver em um servidor de 32 bits, leve a "Wow6432Node" fora o caminho. Criar uma chave do Registro DWORD chamada "UsernameCxz_stripPrefixDomain" e defina o valor como 1. Azure autenticação multifator agora é proteger o proxy do AD FS.

Certifique-se de que os usuários foram importados do Active Directory para o servidor. Consulte a [seção de IPs confiáveis](#trusted-ips) se você gostaria de endereços IP internos de lista branca para que a verificação não é necessária ao entrar no site desses locais.

<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## <a name="ad-fs-20-direct-without-a-proxy"></a>Direcionar o AD FS 2.0 sem um proxy

Você pode proteger AD FS quando o proxy do AD FS não é usado. Instalar o servidor de autenticação multifator Azure no servidor do AD FS e configurar o servidor pelas seguintes etapas:

1. Em servidor de autenticação multifator Azure, clique no ícone de **Autenticação do IIS** no menu à esquerda.
2. Clique na guia **HTTP** .
3. Clique na **Adicionar...** botão.
4. Na caixa de diálogo Adicionar Base URL, digite a URL do site do ADFS onde autenticação HTTP será executada (como https://sso.domain.com/adfs/ls/auth/integrated) no campo Base URL. Em seguida, insira um nome de aplicativo (opcional). O nome do aplicativo aparece em relatórios de autenticação multifator de Azure e pode ser exibido em mensagens de autenticação de SMS ou aplicativo móvel.
5. Se desejar, ajuste o tempo limite ocioso e máximo tempos sessão.
6. Marque a caixa de **usuário exigir autenticação do Azure multifator corresponder** se todos os usuários foram ou serão importados para o servidor e sujeitos a verificação em duas etapas. Se um número significativo de usuários ainda não foram importado para o servidor e/ou será isentar de verificação em duas etapas, deixe a caixa desmarcada. Para obter informações adicionais sobre este recurso, consulte o arquivo de Ajuda.
7. Marque a caixa de cache de cookie se desejado.
<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. Clique no botão **Okey** .
9. Clique na guia **Módulo nativo** e selecione o servidor, o site (como "Site padrão") ou o aplicativo de ADFS (como "ls" em "adfs") para habilitar o plug-in do IIS o nível desejado.
10. Clique na caixa de **autenticação do IIS habilitar** na parte superior da tela. Azure autenticação multifator agora está protegendo ADFS.

Certifique-se de que os usuários foram importados do Active Directory para o servidor. Consulte a seção de IPs confiáveis se você gostaria de endereços IP internos de lista branca para que a verificação não é necessária ao entrar no site desses locais.


## <a name="trusted-ips"></a>IPs confiáveis
IPs confiáveis permitem aos usuários ignorar autenticação multifator do Azure para solicitações de site provenientes de endereços IP ou sub-redes específicas. Por exemplo, você talvez queira usuários isentos de verificação em duas etapas quando eles entrar do escritório. Para isso, especifique a sub-rede do office como uma entrada de IPs confiáveis.

### <a name="to-configure-trusted-ips"></a>Para configurar IPs confiáveis


1. Na seção autenticação do IIS, clique na guia de **IPs confiáveis** .
1. Clique na **Adicionar...** botão.
1. Quando for exibida a caixa de diálogo Adicionar IPs confiáveis, selecione um dos botões de opção **IP única**, **intervalo IP**ou **sub-rede** .
1. Insira o endereço IP, intervalo de endereços IP ou sub-rede que deve estar na lista branca. Se inserindo uma sub-rede, selecione a máscara de rede apropriada e clique no botão **Okey** . O IP confiável agora foi adicionado.


<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>
