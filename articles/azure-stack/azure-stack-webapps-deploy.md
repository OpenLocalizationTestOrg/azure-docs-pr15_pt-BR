<properties
    pageTitle="Adicionar um provedor de recursos de aplicativos Web a pilha Azure | Microsoft Azure"
    description="Orientações detalhadas para implantar Web Apps em pilha do Azure"
    services="azure-stack"
    documentationCenter=""
    authors="ccompy, apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>

# <a name="add-a-web-apps-resource-provider-to-azure-stack"></a>Adicionar um provedor de recursos de aplicativos Web a pilha do Azure

> [AZURE.NOTE] As informações a seguir se aplica somente implantações de TP1 de pilha do Azure.

Adicionar um provedor de recursos de aplicativos Web à pilha de Azure tem sete etapas:

1.  Baixe componentes necessários.
2.  Crie certificados para ser usado por aplicativos de Web do Azure pilha.
3.  Use o instalador baixar, estágio e instalar aplicativos Web do Azure pilha. 
4.  Valide a instalação de aplicativos Web.
5.  Crie registros DNS para o Front-End e balanceadores de carga de servidor de gerenciamento.
6.  Registre o provedor de recursos de aplicativos Web recentemente implantado com BRAÇO.
7.  Unidade de teste o provedor de recursos de aplicativos Web.

## <a name="download-required-components"></a>Baixar componentes necessários

1.  Baixe o [instalador do serviço de aplicativo do Azure pilha preview](http://aka.ms/azasinstaller). 
2.  Baixe os [scripts de auxiliar de implantação do serviço de aplicativo do Azure pilha](http://aka.ms/azashelper). 
3.  Extrair os arquivos do arquivo zip auxiliar scripts, deverá haver três scripts:
    - Criar-AppServiceCerts.ps1
    - Criar-AppServiceDnsRecords.ps1
    - Register-AppServiceResourceProvider.ps1 

## <a name="create-certificates-to-be-used-by-azure-stack-web-apps"></a>Criar certificados para ser usado por aplicativos de Web de pilha do Azure

Primeiro script funciona com a autoridade de certificação pilha do Azure para criar 3 certificados que são necessários por aplicativos Web. Execute o script na ClientVM assegurando que você estiver executando o PowerShell como azurestack\administrator:
1.  Em uma sessão do PowerShell executando como **azurestack\administrator**, execute o script **AppServiceCerts.ps1 de criar** .  Isso cria três certificados, na mesma pasta que o script, que são necessários por aplicativos Web.
2.  Digite uma senha para proteger os arquivos pfx e anote-lo conforme você precisará insira-o no instalador de aplicativos do Azure pilha da Web.

## <a name="use-the-installer-to-download-and-install-azure-stack-web-apps"></a>Use o instalador para baixar e instalar o Azure pilha Web Apps

O instalador appservice.exe irá:
1.  Solicita que o usuário para aceitar a Microsoft e terceiros EULA.
2.  Colete informações de implantação do Azure pilha.
3.  Crie um contêiner de blob na conta de armazenamento do Azure pilha especificada.
4.  Baixe os arquivos necessários para instalar o provedor de recursos do Azure pilha Web App.
5.  Prepare a instalação para implantar o provedor de recursos do Web App no ambiente do Azure pilha.
6.  Carregue os arquivos para a conta de armazenamento do serviço de aplicativo.
7.  Apresentar as informações necessárias para iniciar o modelo do Gerenciador de recursos do Azure.

As etapas a seguir irão orientá-lo durante os estágios de instalação:

>[AZURE.NOTE] Você deve usar uma conta elevada (administrador local ou de domínio) para executar o instalador. Se você entrar como azurestack\azurestackuser, você será solicitado para credenciais elevadas. 

1.  Execute o appservice.exe como **azurestack\administrator**. 
2.  Clique em **implantar usando o Gerenciador de recursos do Azure**.

![Instalador de visualização técnica 1 de serviço de aplicativo do Azure pilha][1]

3.  Revisar e aceitar os termos de licença de pré-lançamento de Software Microsoft e clique em **Avançar**.
4.  Revisar e aceitar os termos de partylicense terceiro e clique em **Avançar**.
5.  Examine as informações de configuração do serviço de nuvem do aplicativo e clique em **Avançar**.

![Configuração de nuvem de serviço de aplicativo do Azure pilha serviço aplicativo visualização técnica 1][2]

6. Clique em **Conectar** (ao lado da caixa do Azure pilha assinaturas).

![Tela de configuração do nuvem da visualização técnica 1 para o serviço de aplicativo pilha Azure serviço aplicativo dois][3]

7.  Na janela autenticação de pilha do Azure fornecer sua **conta de administrador do serviço do Azure Active Directory** e a **senha**e clique em **Entrar**.
**Observação:** Esta é a conta do Active Directory do Azure que você forneceu quando você implantado Azure pilha.
    - Clique na **Seta para baixo** no lado direito da caixa ao lado de **Assinaturas de pilha do Azure** e selecione sua assinatura.

![Seleção de assinatura de visualização técnica 1 de serviço de aplicativo de pilha Azure][5]

8.  Clique na **Seta para baixo** no lado direito da caixa ao lado de **Locais de pilha do Azure**.
    - Selecione um **Local**.
9. Insira o **nome** para o administrador.
10. Digite uma **senha** para o administrador.
11. Examine os **detalhes do SQL Server** e faça alterações, se necessário.
12. Examine a **Conta de logon do administrador do sistema** e fazer alterações, se necessário.
13. Insira a **Senha de administrador do sistema**.
14. Clique em **Avançar**.  Neste ponto o instalador agora verificará os detalhes de conexão para o SQL Server fornecido.

![Seleção de assinatura de visualização técnica 1 de serviço de aplicativo de pilha Azure][4]    

15. Clique em **Procurar** ao lado do **Arquivo de certificado do SSL de padrão de aplicativos de Web** e navegue até o webapps **. AzureStack.Local** certificado [criado anteriormente](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
16. Insira a **senha do certificado** que você definir quando você criou os certificados.
17. Clique em **Procurar** ao lado do **Arquivo de certificado do recurso provedor SSL** e navegue até o gerenciamento de **. AzureStack.Local** certificado [criado anteriormente](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
18. Insira a **senha do certificado** que você definir quando você criou os certificados.
19. Clique em **Procurar** ao lado do **Arquivo de certificado do recurso provedor raiz** e navegue até o gerenciamento de **. AzureStack.Local** certificado [criado anteriormente](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
20. Clique em **próxima** o instalador verificará a senha de certificado fornecida.

![Detalhes do certificado de visualização técnica 1 de serviço de aplicativo de pilha Azure][6]

Implantação levará cerca de 45 a 60 minutos para ser concluída.

![Serviço de aplicativo de pilha Azure visualização técnica 1 o progresso da instalação][7]

21. Depois que o instalador for concluída com êxito, clique em **Sair**.

## <a name="validate-web-apps-installation"></a>Validar a instalação de aplicativos Web

1.  Em sua **Máquina de Host do Azure pilha** abra o **Gerenciador de Hyper-V**.
2.  Localize a **CN0-máquina virtual** e **conectar-se** para a máquina virtual.
![Gerenciador de Hyper-V de visualização técnica 1 de serviço de aplicativo de pilha Azure][8]
3.  Na área de trabalho desta máquina virtual clique duas vezes no **Console de gerenciamento de nuvem da Web**.
![Console de gerenciamento de visualização técnica 1 de serviço de aplicativo do Azure pilha][9]
4.  Navegue para **servidores gerenciados**.
5.  Quando todas as máquinas são **pronto** prossiga para a próxima etapa. 
![Status de servidores gerenciados 1 do Azure pilha serviço de aplicativo Technical Preview][10]

## <a name="create-dns-records-for-the-management-server-and-front-end-load-balancers"></a>Criar registros de DNS para o servidor de gerenciamento e balanceadores de carga de Front-End
1.  Abra uma instância do PowerShell como **azurestack\administrator**.
2.  Navegue até o local dos scripts baixados e extraídos na [etapa de pré-requisito](#Download-Required-Components).
3.  Executar o script **AppServiceDnsRecords.ps1 de criar** , isso cria entradas DNS para ativar as solicitações de aplicativo de portal e da web para ser roteados para os servidores Front-End.  Durante a implantação de BRAÇO de aplicativos Web, dois balanceadores de carga de Software (SLBs), foram criados no provedor de recursos de rede. Eles apontar para os servidores de gerenciamento e os servidores de Front-End. O portal e solicitações com base em ARM para o provedor de recursos de serviço de aplicativo do Azure pilha ir para o servidor de gerenciamento.

## <a name="register-the-newly-deployed-azure-stack-web-apps-provider-with-arm"></a>Registrar o provedor de Azure pilha Web Apps recentemente implantado com BRAÇO
1.  Abra uma instância do PowerShell como **azurestack\administrator**.
2.  Navegue até o local dos scripts baixados e extraídos na [etapa de pré-requisito](#Download-Required-Components).
3.  Execute o script de **Registro AppServiceResourceProvider.ps1** . 

>[AZURE.NOTE] Digite o nome de usuário e a senha **exatamente (incluindo maiusculas e minúsculas)** como ele foi inserido para o **Administrador de máquinas virtuais** e campos de **senha** na instalação ou seu registro de provedor do recurso falhará.

## <a name="test-drive-azure-stack-web-apps"></a>Teste unidade pilha Azure Web Apps

Agora que você tiver implantado e registrado o provedor de recursos de aplicativos Web, você pode testar para certificar-se de que os locatários podem implantar aplicativos web.

1.  No portal do Azure pilha, clique em novo, clique em Web + Mobile e clique em aplicativo Web.
2.  Na lâmina Web App, digite um nome na caixa de aplicativo Web.
3.  Em grupo de recursos, clique em novo e digite um nome na caixa de grupo de recursos. 
4.  Clique em local da plano de serviço de aplicativo e clique em criar novo.
5.  Na lâmina de plano de serviço de aplicativo, digite um nome na caixa de plano de serviço de aplicativo.
6.  Clique em nível de preço, clique livre-Shared ou compartilhado compartilhado, clique em Selecionar, clique em Okey e, em seguida, clique em criar.
7.  Em menos de um minuto, uma peça para o novo aplicativo web aparecerá no painel. Clique no bloco.
8.  Na lâmina web app, clique em Procurar para exibir o site padrão para este aplicativo.


**Implantar um site WordPress, DNN ou Django (opcional)**

1. No portal do Azure pilha, clique em "+", vá do Azure Marketplace, implantar um site Django e aguarde a conclusão bem-sucedida. A plataforma de web Django usa um banco de dados com base no sistema do arquivo e não requer quaisquer provedores de recursos adicionais, como SQL ou MySQL.  

2. Se você também implantou um provedor de recursos MySQL, você pode implantar um site de WordPress do Marketplace. Quando solicitado para parâmetros de banco de dados, inserir o nome de usuário como *User1@Server1* (com o nome de usuário e o nome de servidor de sua preferência).

3. Se você também implantou um provedor de recursos do SQL Server, você pode implantar um site DNN do Marketplace. Quando solicitado para parâmetros de banco de dados, escolha um banco de dados no computador que executa o SQL Server que está conectado ao seu provedor de recursos.

## <a name="next-steps"></a>Próximas etapas

Você também pode experimentar outros [plataforma como um serviço (PaaS) serviços](azure-stack-tools-paas-services.md), como o [provedor de recursos do SQL Server](azure-stack-sql-rp-deploy-short.md) e o [provedor de recursos de MySQL](azure-stack-mysql-rp-deploy-short.md).

<!--Image references-->
[1]: ./media/azure-stack-webapps-deploy/AppService_exe_Start.png
[2]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep1.png
[3]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2.png
[4]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2_populated.png
[5]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2_SubscriptionSelection.png
[6]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep3_Certificates.png
[7]: ./media/azure-stack-webapps-deploy/AppService_exe_InstallationProgress.png
[8]: ./media/azure-stack-webapps-deploy/HyperV.png
[9]: ./media/azure-stack-webapps-deploy/MMC.png
[10]: ./media/azure-stack-webapps-deploy/ManagedServers.png


<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[WebAppsDeployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
