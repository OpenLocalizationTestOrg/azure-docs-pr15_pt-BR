<properties
    pageTitle="Conectar ao banco de dados do SQL usando o SQL Server Management Studio no Azure RemoteApp | Microsoft Azure"
    description="Use este tutorial para saber como usar o SQL Server Management Studio no Azure RemoteApp para segurança e desempenho ao se conectar ao banco de dados SQL"
    services="sql-database"
    documentationCenter=""
    authors="adhurwit"
    manager="jhubbard"/>

<tags
    ms.service="sql-database"
    ms.workload="data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="adhurwit"/>

# <a name="use-sql-server-management-studio-in-azure-remoteapp-to-connect-to-sql-database"></a>Usar o SQL Server Management Studio no Azure RemoteApp para se conectar ao banco de dados SQL

## <a name="introduction"></a>Introdução  
Este tutorial mostra como usar o SQL Server Management Studio (SSMS) no Azure RemoteApp para se conectar ao banco de dados SQL. Orienta você pelo processo de configuração de SQL Server Management Studio no Azure RemoteApp, explica os benefícios e mostra os recursos de segurança que você pode usar no Active Directory do Azure.

**Tempo estimado para concluir:** 45 minutos

## <a name="ssms-in-azure-remoteapp"></a>SSMS no Azure RemoteApp

Azure RemoteApp é um serviço RDS no Azure que oferece aplicativos. Você pode saber mais sobre isso aqui: [o que é o RemoteApp?](../remoteapp/remoteapp-whatis.md)

SSMS em execução no Azure RemoteApp fornece a mesma experiência de executando SSMS localmente.

![Captura de tela mostrando SSMS em execução no Azure RemoteApp][1]



## <a name="benefits"></a>Benefícios

Há muitos benefícios usando SSMS no Azure RemoteApp, incluindo:

- Porta 1433 no Azure SQL Server não precisa ser exposto externamente (fora do Azure).
- Não é preciso manter adicionando e removendo endereços IP no firewall do Azure SQL Server.
- Todas as conexões de RemoteApp Azure ocorrem por HTTPS na porta 443 usando criptografados protocolo de Desktop remoto
- É multiusuário e pode dimensionar.
- Não há um ganho de desempenho de ter SSMS na mesma região do banco de dados do SQL.
- Você pode fazer auditoria de uso do Azure RemoteApp com a edição Premium do Azure Active Directory que tem relatórios de atividades do usuário.
- Você pode habilitar a autenticação multifator (MFA).
- Acesso SSMS em qualquer lugar ao usar qualquer um dos clientes do Azure RemoteApp com suporte que inclui iOS, Android, Mac, Windows Phone e Windows PC.


## <a name="create-the-azure-remoteapp-collection"></a>Criar o conjunto de RemoteApp do Azure

Aqui estão as etapas para criar seu conjunto de RemoteApp Azure com SSMS:


### <a name="1-create-a-new-windows-vm-from-image"></a>1. Crie uma nova VM Windows da imagem
Use a imagem de "Windows Server remoto Desktop sessão Host Windows Server 2012 R2" da Galeria para tornar sua máquina virtual novo.


### <a name="2-install-ssms-from-sql-express"></a>2. Instale SSMS do SQL Express

Vá para a máquina virtual novo e navegue até esta página de download: [Microsoft® SQL Server® 2014 Express](https://www.microsoft.com/en-us/download/details.aspx?id=42299)

Não há uma opção para baixar somente SSMS. Após o download, vá para o diretório de instalação e execute a instalação para instalar o SSMS.

Você também precisa instalar o SQL Server 2014 Service Pack 1. É possível baixá-lo aqui: [Microsoft SQL Server 2014 Service Pack 1 (SP1)](https://www.microsoft.com/en-us/download/details.aspx?id=46694)

SQL Server 2014 Service Pack 1 inclui funcionalidade essencial para trabalhar com o Azure SQL Database.


### <a name="3-run-validate-script-and-sysprep"></a>3. Execute o script de validar e Sysprep

Na área de trabalho da máquina virtual é um script do PowerShell, chamado validar. Execute esse recurso clicando duas vezes. Ele verificará que a máquina virtual está pronta para ser usado para hospedar remoto de aplicativos. Quando a verificação for concluída, ele irá perguntar executar sysprep - escolher executá-lo.

Quando sysprep for concluído, ele será desligado a máquina virtual.

Para saber mais sobre a criação de uma imagem do Azure RemoteApp, consulte: [como criar uma imagem de modelo de RemoteApp no Azure](http://blogs.msdn.com/b/rds/archive/2015/03/17/how-to-create-a-remoteapp-template-image-in-azure.aspx)


### <a name="4-capture-image"></a>4. capturar imagem

Quando a máquina virtual parou de funcionar, encontrá-lo no portal do atual e capturá-lo.

Para saber mais sobre como capturar uma imagem, consulte [Capture uma imagem de uma máquina virtual do Windows Azure criada com o modelo clássico de implantação](../virtual-machines/virtual-machines-windows-classic-capture-image.md)


### <a name="5-add-to-azure-remoteapp-template-images"></a>5. adicionar imagens de modelo de RemoteApp do Azure

Na seção Azure RemoteApp do portal do atual, vá para a guia de imagens de modelo e clique em Adicionar. Na caixa pop-up, selecione "Importar uma imagem de sua biblioteca de máquinas virtuais" e escolha a imagem que você acabou de criar.



### <a name="6-create-cloud-collection"></a>6. criar conjunto de nuvem

No portal do atual, crie um novo conjunto de nuvem do RemoteApp do Azure. Escolha a imagem de modelo que você acabou de importar com SSMS instalado nele.

![Criar novo conjunto de nuvem][2]


### <a name="7-publish-ssms"></a>7. publicar SSMS

Na publicação na guia da sua nova coleção de nuvem, selecione publicar um aplicativo do Menu Iniciar e escolha SSMS na lista.

![Publicar o aplicativo][5]

### <a name="8-add-users"></a>8. adicionar usuários

Na guia acesso do usuário, você pode selecionar os usuários que terão acesso a esse conjunto de RemoteApp Azure que inclui somente SSMS.

![Adicionar usuário][6]


### <a name="9-install-the-azure-remoteapp-client-application"></a>9. instalar o aplicativo de cliente RemoteApp do Azure

Você pode baixar e instalar um cliente do Azure RemoteApp aqui: [Download | RemoteApp Azure](https://www.remoteapp.windowsazure.com/en/clients.aspx)



## <a name="configure-azure-sql-server"></a>Configurar o Azure SQL Server

É a única configuração necessária para garantir que os serviços do Azure está ativado para o firewall. Se você usar esta solução, então você não necessário adicionar todos os endereços IP para abrir o firewall. O tráfego de rede que é permitido para o SQL Server é de outros serviços de Azure.


![Permitir que o Azure][4]



## <a name="multi-factor-authentication-mfa"></a>Autenticação multifator (MFA)

MFA pode ser habilitado especificamente para este aplicativo. Vá para a guia de aplicativos do Azure Active Directory. Você encontrará uma entrada do Microsoft Azure RemoteApp. Se você clicar nesse aplicativo e, em seguida, configurar, você verá a página abaixo de onde você pode habilitar MFA para este aplicativo.

![Habilitar MFA][3]



## <a name="audit-user-activity-with-azure-active-directory-premium"></a>Atividade de usuário de auditoria com o Azure Active Directory Premium

Se você não tiver o Azure AD Premium, você precisa ativá-lo na seção licenças do seu diretório. Com Premium ativado, você pode atribuir aos usuários para o nível Premium.

Quando você vai para um usuário no seu Azure Active Directory, você pode, em seguida, ir para a guia atividade para ver informações de logon para o Azure RemoteApp.



## <a name="next-steps"></a>Próximas etapas

Depois de concluir todas as etapas acima, você será capaz de executar o cliente do Azure RemoteApp log-in e com um usuário atribuído. Você receberá SSMS como um dos seus aplicativos, e você pode executá-lo como você faria se estivesse instalado em seu computador com acesso ao Azure SQL Server.

Para obter mais informações sobre como fazer a conexão ao banco de dados do SQL, consulte [conectar ao banco de dados do SQL com o SQL Server Management Studio e executar uma consulta de T-SQL de exemplo](sql-database-connect-query-ssms.md).


Isso é tudo para agora. Aproveite!



<!--Image references-->
[1]: ./media/sql-database-ssms-remoteapp/ssms.png
[2]: ./media/sql-database-ssms-remoteapp/newcloudcollection.png
[3]: ./media/sql-database-ssms-remoteapp/mfa.png
[4]: ./media/sql-database-ssms-remoteapp/allowazure.png
[5]: ./media/sql-database-ssms-remoteapp/publish.png
[6]: ./media/sql-database-ssms-remoteapp/user.png
