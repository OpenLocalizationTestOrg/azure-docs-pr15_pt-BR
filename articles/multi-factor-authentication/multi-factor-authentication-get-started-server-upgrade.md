<properties 
    pageTitle="Atualizando o agente de PhoneFactor ao servidor do Azure autenticação multifator"
    description="Este documento descreve como começar com o servidor de MFA do Azure e como atualizar do agente phonefactor mais antigo."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="upgrading-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Atualizando o agente de PhoneFactor ao servidor do Azure autenticação multifator

Atualizando da V5. x do agente de PhoneFactor ou mais antigo para o servidor de autenticação multifator Azure requer o agente de PhoneFactor e componentes afiliadas antes que o servidor de autenticação multifator e seus componentes afiliadas podem ser instalados.

## <a name="to-upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Para atualizar o agente de PhoneFactor para o servidor de autenticação multifator do Azure
<ol>
<li>Primeiro, faça backup do arquivo de dados de PhoneFactor. O local de instalação padrão é C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata.


<li>Se o Portal de usuário estiver instalado:</li>
<ol>
<li>Navegue até a pasta de instalação e fazer backup do arquivo Web. config. O local de instalação padrão é C:\inetpub\wwwroot\PhoneFactor.</li>


<li>Se você tiver adicionado temas personalizados para o portal, faça o backup de sua pasta personalizada abaixo do diretório de C:\inetpub\wwwroot\PhoneFactor\App_Themes.</li>


<li>Desinstale o Portal de usuário por meio do agente de PhoneFactor (disponível somente se tiver instalado no mesmo servidor como o agente de PhoneFactor) ou Windows programas e recursos.</li></ol>




<li>Se o serviço da Web App Mobile estiver instalado:
<ol>
<li>Vá para a pasta de instalação e fazer backup do arquivo Web. config. O local de instalação padrão é C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.</li>
<li>Desinstalar o serviço Web de aplicativo móvel entre as janelas programas e recursos.</li></ol>

<li>Se o SDK do serviço Web estiver instalado, desinstale-o por meio do agente de PhoneFactor ou Windows programas e recursos.

<li>Desinstale o agente de PhoneFactor entre as janelas programas e recursos.

<li>Instale o servidor de autenticação multifator. Observe que o caminho de instalação será selecionado no registro a partir da instalação anterior do agente de PhoneFactor para que ele deve instalar no mesmo local (por exemplo, C:\Program Files\PhoneFactor). Novas instalações terá um padrão diferente a instalar o caminho (por exemplo, C:\Program Files\Multi fator autenticação de servidor). O arquivo de dados para a esquerda pelo agente de PhoneFactor anterior deve ser atualizado durante a instalação, para que os usuários e configurações devem permanecer lá depois de instalar o novo servidor de autenticação multifator.

<li>Se solicitado, ative o servidor de autenticação multifator e certifique-se de que ele é atribuído ao grupo de replicação correto.

<li>Se o SDK do serviço Web instalado anteriormente, instale o SDK do serviço Web novo com a Interface de usuário do servidor de autenticação multifator. Observe que o nome do diretório virtual padrão agora é "MultiFactorAuthWebServiceSdk" em vez de "PhoneFactorWebServiceSdk". Se você quiser usar o nome anterior, você deve alterar o nome do diretório virtual durante a instalação. Caso contrário, se você permitir a instalação usar o novo nome de padrão, você precisará trocar a URL de todos os aplicativos que fazem referência o SDK do serviço Web como o Portal de usuário e o serviço móvel do Web App para apontar o local correto.

<li>Se o Portal de usuário instalado anteriormente no servidor do agente de PhoneFactor, instale o novo Portal de usuário de autenticação multifator com a Interface de usuário do servidor de autenticação multifator. Observe que o nome do diretório virtual padrão agora é "MultiFactorAuth" em vez de "PhoneFactor". Se você quiser usar o nome anterior, você deve alterar o nome do diretório virtual durante a instalação. Caso contrário, se você permitir a instalação usar o novo nome de padrão, clique no ícone de Portal de usuário no servidor de autenticação multifator e atualizar a URL do Portal do usuário na guia Configurações.

<li>Se o Portal de usuário e/ou serviço móvel do aplicativo da Web foi instalado anteriormente em um servidor diferente do agente PhoneFactor:
<ol>
<li>Vá para o local de instalação (por exemplo, C:\Program Files\PhoneFactor) e copie o installer(s) apropriado para o outro servidor. Há instaladores de 32 bits e 64 bits para o Portal de usuário e o serviço móvel do Web App. Eles são chamados MultiFactorAuthenticationUserPortalSetupXX.msi e MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi respectivamente.</li>
<li>Para instalar o Portal de usuário no servidor web, abra um prompt de comando como administrador e execute o MultiFactorAuthenticationUserPortalSetupXX.msi. Observe que o nome do diretório virtual padrão agora é "MultiFactorAuth" em vez de "PhoneFactor". Se você quiser usar o nome anterior, você deve alterar o nome do diretório virtual durante a instalação. Caso contrário, se você permitir a instalação usar o novo nome de padrão, clique no ícone de Portal de usuário no servidor de autenticação multifator e atualizar a URL do Portal do usuário na guia Configurações. Usuários existentes precisa ser informado sobre a nova URL.</li>
<li>Vá para o Portal do usuário (por exemplo, C:\inetpub\wwwroot\MultiFactorAuth) do local de instalação e edite o arquivo Web. config. Copie os valores nas seções appSettings e applicationSettings do arquivo Web. config original que foi feito o backup antes da atualização para o novo arquivo Web. config. Se o novo nome de diretório virtual padrão foi mantido ao instalar o SDK do serviço da Web, altere a URL na seção applicationSettings para apontar para o local correto. Se quaisquer outros padrões foram alterados no arquivo Web. config anterior, aplica essas alterações para o novo arquivo Web. config.</li>
<li>Para instalar o serviço da Web App Mobile no servidor web, abra um prompt de comando como administrador e execute o MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi. Observe que o nome do diretório virtual padrão agora é "MultiFactorAuthMobileAppWebService" em vez de "PhoneFactorPhoneAppWebService". Se você quiser usar o nome anterior, você deve alterar o nome do diretório virtual durante a instalação. Talvez você queira escolher um nome mais curto para tornar mais fácil para os usuários finais digite em seus dispositivos móveis. Caso contrário, se você permitir a instalação usar o novo nome de padrão, clique no ícone do aplicativo Mobile no servidor de autenticação multifator e atualizar o aplicativo móvel URL do serviço Web.</li>
<li>Vá para o serviço da Web App Mobile instalar local (por exemplo, C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) e edite o arquivo Web. config. Copie os valores nas seções appSettings e applicationSettings do arquivo Web. config original que foi feito o backup antes da atualização para o novo arquivo Web. config. Se o novo nome de diretório virtual padrão foi mantido ao instalar o SDK do serviço da Web, altere a URL na seção applicationSettings para apontar para o local correto. Se quaisquer outros padrões foram alterados no arquivo Web. config anterior, aplica essas alterações para o novo arquivo Web. config.</li></ol>
