<properties 
    pageTitle="Servidor de autenticação multifator de autenticação do IIS e do Azure"
    description="Esta é a página de autenticação multifator do Azure que ajudarão na implantação IIS autenticação e servidor de autenticação multifator do Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="iis-authentication"></a>Autenticação do IIS

A seção de autenticação do IIS do servidor de autenticação multifator Azure permite habilitar e configurar a autenticação do IIS para integração com aplicativos de web do Microsoft IIS. O servidor de autenticação multifator Azure instala um plug-in que pode filtrar solicitações sendo feitas para o servidor de web IIS para adicionar autenticação multifator do Azure. O plug-in do IIS oferece suporte para a autenticação baseada em formulário e a autenticação de HTTP integrada do Windows. Confiáveis que IPS também pode ser configurado para isenção endereços IP internos de autenticação de dois fatores.


![Autenticação do IIS](./media/multi-factor-authentication-get-started-server-iis/iis.png)


## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Usando a autenticação do IIS baseados em formulário com o servidor do Azure autenticação multifator

Para proteger um aplicativo web do IIS que usa autenticação baseada em formulário, instale o servidor de autenticação multifator Azure no servidor web IIS e configurar o servidor pelo procedimento a seguir.

1. Dentro do servidor de autenticação multifator Azure clique no ícone de autenticação do IIS no menu à esquerda.
2. Clique na guia baseados em formulário.
3. Clique em Adicionar … botão.
4. Para detectar o nome de usuário, variáveis de domínio e senha automaticamente, insira a URL de logon (por exemplo, https://localhost/contoso/auth/login.aspx) dentro da caixa de diálogo Auto-Configure Form-Based site e clique em Okey.
5. Marque a caixa de correspondência de usuário de exigir autenticação de vários fatores se todos os usuários foram ou serão importados para o servidor e sujeitos a autenticação multifator. Se um número significativo de usuários ainda não foram importado para o servidor e/ou será isentar de autenticação multifator, deixe a caixa desmarcada.
6. Se as variáveis de página não podem ser detectadas automaticamente, clique em especificar manualmente … botão na caixa de diálogo Auto-Configure Form-Based site.
7. Na caixa de diálogo Add Form-Based site, digite a URL para a página de login no campo URL enviar e insira um nome de aplicativo (opcional). O nome do aplicativo aparece em relatórios de autenticação multifator de Azure e pode ser exibido em mensagens de autenticação de SMS ou aplicativo móvel. Consulte o arquivo de ajuda para obter mais informações sobre a URL de enviar.
8. Selecione o formato de solicitação correto. Isso é definido para "POSTAGEM ou obter" para a maioria dos aplicativos web.
9. Insira a variável Username, a variável de senha e a variável de domínio (se ele aparece na página de login). Talvez você precise navegar até a página de login em um navegador da web, clique com botão direito na página e selecione "Exibir fonte" para encontrar os nomes das caixas de entrada de dentro da página.
10. Marque a caixa de correspondência de usuário de exigir autenticação do Azure multifator se todos os usuários foram ou serão importados para o servidor e sujeitos a autenticação multifator. Se um número significativo de usuários ainda não foram importado para o servidor e/ou será isentar de autenticação multifator, deixe a caixa desmarcada. Consulte o arquivo de ajuda para obter informações adicionais sobre este recurso.
11.  Clique no botão Avançado... botão Revisar as configurações avançadas, incluindo a capacidade de selecionar um arquivo de página personalizado negação, cache autenticação bem-sucedida ao site por um período de tempo usando os cookies e selecione se deseja autenticar as credenciais primárias contra o domínio do Windows, um diretório LDAP ou um servidor RADIUS. Ao concluir clique no botão Okey para retornar à caixa de diálogo Add Form-Based site. Consulte o arquivo de ajuda para obter mais informações sobre as configurações avançadas.
12. Clique no botão Okey.
13. Depois que as variáveis de URL e página foram detectadas ou inseridas, os dados do site serão exibida no painel baseados em formulário.
14. Consulte os IIS Ativar Plug-ins para seção de servidor de autenticação multifator Azure diretamente abaixo para concluir a configuração de autenticação do IIS.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Usando autenticação do Windows integrado com o servidor do Azure autenticação multifator

Para proteger um aplicativo web do IIS que usa autenticação integrada Windows HTTP, instale o servidor de autenticação multifator Azure no servidor web IIS e configurar o servidor pelo procedimento a seguir.

1. Dentro do servidor de autenticação multifator Azure clique no ícone de autenticação do IIS no menu à esquerda.
2. Clique na guia HTTP. Clique na guia baseados em formulário.
3. Clique em Adicionar … botão.
4. Na caixa de diálogo Adicionar Base URL, digite a URL para o site onde a autenticação HTTP é executada (por exemplo, http://localhost/owa) no campo Base URL e insira um nome de aplicativo (opcional). O nome do aplicativo aparece em relatórios de autenticação multifator de Azure e pode ser exibido em mensagens de autenticação de SMS ou aplicativo móvel.
5. Ajuste o tempo limite ocioso e máximo tempos sessão se o padrão não é suficiente.
6. Marque a caixa de correspondência de usuário de exigir autenticação de vários fatores se todos os usuários foram ou serão importados para o servidor e sujeitos a autenticação multifator. Se um número significativo de usuários ainda não foram importado para o servidor e/ou será isentar de autenticação multifator, deixe a caixa desmarcada. Consulte o arquivo de ajuda para obter informações adicionais sobre este recurso.
7. Marque a caixa de cache de cookie se desejado.
8. Clique no botão Okey.
9. Consulte a seção de [Plug-ins do IIS habilitar para servidor de autenticação multifator Azure](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server) diretamente abaixo para concluir a configuração de autenticação do IIS.


## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Ativar IIS Plug-ins do servidor do Azure autenticação multifator

Depois de ter configurado o baseados em formulário ou URLs de autenticação de HTTP e configurações, você deve selecionar os locais onde os plug-ins do IIS de autenticação multifator Azure devem ser carregados e ativados no IIS. Use o procedimento a seguir:

1. Se executando no IIS 6, clique na guia ISAPI e selecione o site que o aplicativo web é executado em (por exemplo, Site padrão) para habilitar o filtro ISAPI de autenticação multifator Azure plug-in para esse site.
2. Se executando no IIS 7 ou superior, clique na guia módulo nativo e selecione servidor, website(s) ou aplicativos para habilitar o plug-in de IIS os níveis desejados.
3. Clique na caixa de autenticação Enable IIS na parte superior da tela. Azure autenticação multifator agora é proteger o aplicativo do IIS selecionado. Certifique-se de que os usuários foram importados para o servidor. Consulte a seção de IPs confiáveis abaixo se você gostaria de branca que endereços de IP interno que autenticação de dois fatores não é necessária ao fazer logon no site desses locais.


## <a name="trusted-ips"></a>IPs confiáveis

O IPs confiáveis permite aos usuários ignorar autenticação multifator do Azure para solicitações de site provenientes de endereços IP específicos ou sub-redes. Por exemplo, você talvez queira usuários isentos de autenticação multifator do Azure durante o registro em log do escritório. Para isso, especifique a sub-rede do office como uma entrada de IPs confiáveis. Para configurar o IPs confiáveis use o procedimento a seguir:

1. Na seção autenticação do IIS, clique na guia de IPs confiáveis.
2. Clique em Adicionar … botão.
3. Quando for exibida a caixa de diálogo Adicionar IPs confiáveis, selecione o único IP, o intervalo IP ou o botão de rádio de sub-rede.
4. ntral o endereço IP, intervalo de endereços IP ou sub-rede que deve estar na lista branca. Se inserindo uma sub-rede, selecione a máscara de rede apropriada e clique no botão Okey. Lista branca agora foi adicionada.
