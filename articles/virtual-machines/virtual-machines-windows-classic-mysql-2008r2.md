<properties
    pageTitle="Criar uma máquina virtual executando MySQL | Microsoft Azure"
    description="Crie uma máquina virtual Azure executando o Windows Server 2012 R2 e o banco de dados MySQL usando o modelo clássico de implantação."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="cynthn"/>


# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2012-r2"></a>Instalar MySQL em uma máquina virtual criada com o modelo clássico de implantação executando o Windows Server 2012 R2

[MySQL](http://www.mysql.com) é um popular Abrir origem, o banco de dados SQL. Este tutorial mostra como instalar e executar a versão de comunidade do MySQL 5.6.23 como um servidor MySQL em uma máquina virtual executando o Windows Server 2012 R2. Para obter instruções sobre como instalar MySQL no Linux, consulte: [como instalar MySQL no Azure](virtual-machines-linux-mysql-install.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="create-a-virtual-machine-running-windows-server-2012-r2"></a>Criar uma máquina virtual executando o Windows Server 2012 R2

Se você ainda não tiver uma máquina virtual executando o Windows Server 2012 R2, você pode usar este [tutorial](virtual-machines-windows-classic-tutorial.md) para criar a máquina virtual. 

## <a name="attach-a-data-disk"></a>Anexar um disco de dados

Após a máquina virtual é criada, opcionalmente, você pode anexar um disco de dados adicionais. Isso é recomendado para cargas de trabalho de produção e para evitar falta de espaço na unidade do sistema operacional (c), que inclui o sistema operacional.

Veja [como anexar um disco de dados para uma máquina virtual Windows](virtual-machines-windows-classic-attach-disk.md) e siga as instruções para anexar um disco vazio. Defina a configuração de cache de host como **Nenhum** ou **somente leitura**.

## <a name="log-on-to-the-virtual-machine"></a>Faça logon na máquina virtual

Em seguida, você usará o [logon na máquina virtual](virtual-machines-windows-classic-connect-logon.md) para que possa instalar MySQL.

##<a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>Instalar e executar o MySQL Community Server na máquina virtual

Siga estas etapas para instalar, configurar e executar a versão de comunidade do servidor MySQL:

> [AZURE.NOTE] Estas etapas são para os 5.6.23.0 versão da comunidade do MySQL e Windows Server 2012 R2. Sua experiência pode ser diferente versões diferentes do MySQL ou o Windows Server.

1.  Depois que você conectou à máquina virtual usando a área de trabalho remota, clique em **Internet Explorer** desde a tela inicial.
2.  Selecione o botão de **Ferramentas** no canto superior direito (o ícone de roda cogged) e clique em **Opções da Internet**. Clique na guia **segurança** , clique no ícone **Sites confiáveis** e, em seguida, clique no botão **Sites** . Adicionar http://*. mysql.com à lista de sites confiáveis. Clique em * *Fechar**e clique em * *Okey**.
3.  Na barra de endereço do Internet Explorer, digite http://dev.mysql.com/downloads/mysql/.
4.  Use o site do MySQL para localizar e baixar a versão mais recente do instalador do MySQL para Windows. Ao escolher o instalador do MySQL, baixe a versão que tem o completo (por exemplo, o mysql-instalador-comunidade-5.6.23.0.msi com um tamanho de arquivo de 282.4 MB) do conjunto de arquivos e, em seguida, salve o instalador.
5.  Quando o instalador do download for concluído, clique em **Executar** para iniciar a instalação.
6.  Na página **Contrato de licença** , aceite o contrato de licença e clique em **Avançar**.
7.  Na página **Escolher um tipo de instalação** , clique no tipo de instalação desejado e clique em **Avançar**. Estas etapas pressupõem que a seleção do tipo de instalação do **servidor apenas** .
8.  Na página de **instalação** , clique em **Executar**. Quando a instalação estiver concluída, clique em **Avançar**.
9.  Na página de **Configuração do produto** , clique em **Avançar**.
10. Na página **tipo e uma rede** , especifica sua configuração desejada conectividade e tipo de opções, incluindo a porta TCP, se necessário. Selecione **Mostrar opções avançadas**e clique em **Avançar**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_TypeNetworking.png)

11. Na página **contas e funções** , especifique uma senha forte de raiz de MySQL. Adicionar outras contas de usuário de MySQL conforme necessário e clique em **Avançar**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AccountsRoles_Filled.png)

12. Na página do **Serviço do Windows** , especifique as alterações nas configurações padrão para executar o servidor MySQL como um serviço do Windows conforme necessário e clique em **Avançar**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_WindowsService.png)

13. Na página **Opções avançadas** , especifique as alterações nas opções de log conforme necessário e clique em **Avançar**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AdvOptions.png)

14. Na página de **Aplicar a configuração do servidor** , clique em **Executar**. Quando concluir as etapas de configuração, clique em **Concluir**.
15. Na página de **Configuração do produto** , clique em **Avançar**.
16. Na página **Instalação concluída** , clique em **Log de copiar para área de transferência** , se desejar examiná-la mais tarde e, em seguida, clique em **Concluir**.
17. Na tela Iniciar, digite **mysql**e clique em **Cliente de linha de comando do MySQL 5.6**.
18. Insira a senha raiz especificada na etapa 11 e será apresentada com uma instrução onde você pode emitir comandos para configurar MySQL. Para obter os detalhes de comandos e sintaxe, consulte [Manuais de referência do MySQL](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html).

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_CommandPrompt.png)

19. Você também pode configurar definições de padrão de configuração de servidor, como as unidades e diretórios base e dados com entradas no arquivo C:\Program Files (x86) \MySQL\MySQL servidor 5.6\my-default.ini. Para obter mais informações, consulte [5.1.2 padrões de configuração do servidor](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html).

## <a name="configure-endpoints"></a>Configurar pontos de extremidade

Se quiser que o serviço de servidor MySQL esteja disponível para computadores de cliente MySQL na Internet, você deve configurar um ponto de extremidade da porta TCP na qual o serviço de servidor MySQL está atendendo e criar uma regra adicional do Firewall do Windows. Esta é a porta TCP 3306, a menos que você especificou uma porta diferente na **rede e tipo de** página (etapa 10 do procedimento anterior).


> [AZURE.NOTE] Você deve considerar cuidadosamente as implicações de segurança de fazer isso, pois isso tornará o serviço de servidor MySQL disponível para todos os computadores na Internet. Você pode definir o conjunto de endereços IP de origem que têm permissão para usar o ponto de extremidade com uma lista de controle de acesso (ACL). Para obter mais informações, consulte [como configurar o pontos de extremidade para uma máquina Virtual](virtual-machines-windows-classic-setup-endpoints.md).


Para configurar um ponto de extremidade do serviço do servidor MySQL:

1.  No portal do Azure clássico, clique em **máquinas virtuais**, clique no nome da máquina virtual MySQL e clique em **pontos de extremidade**.
2.  Na barra de comandos, clique em **Adicionar**.
3.  Na página **Adicionar um ponto de extremidade para uma máquina virtual** , clique na seta à direita.
4.  Se você estiver usando o padrão porta MySQL TCP de 3306, clique **MySQL** no **nome**e, em seguida, clique na marca de seleção.
5.  Se você estiver usando uma porta TCP diferente, digite um nome exclusivo no **nome**. Selecione **TCP** no protocolo, digite o número da porta na **Porta pública** e **Privada porta**e, em seguida, clique na marca de seleção.

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>Adicionar uma regra de Firewall do Windows para permitir o tráfego de MySQL

Para adicionar uma regra de Firewall do Windows que permite MySQL tráfego da Internet, execute o seguinte comando em um prompt de comando do Windows PowerShell elevado na máquina virtual servidor MySQL.

    New-NetFirewallRule -DisplayName "MySQL56" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public


    
## <a name="test-your-remote-connection"></a>Testar sua conexão remota


Para testar sua conexão remoto para o serviço de servidor MySQL em execução na máquina virtual Azure, primeiro você deve determinar o nome DNS correspondente ao serviço de nuvem que contém a máquina virtual executando o servidor MySQL.

1.  No portal do Azure clássico, clique em **máquinas virtuais**, clique no nome da sua máquina virtual do servidor MySQL e clique em **Painel de controle**.
2.  No painel de máquina virtual, observe o valor de **Nome DNS** sob a seção de **Resumo de geral rápida** . Aqui está um exemplo:

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_DNSName.png)

3.  De um computador local executando MySQL ou o cliente de MySQL, execute o seguinte comando para efetuar login como um usuário de MySQL.

        mysql -u <yourMysqlUsername> -p -h <yourDNSname>

    Por exemplo, para o dbadmin3 de nome de usuário de MySQL e o nome DNS testmysql.cloudapp.net da máquina virtual, use o comando a seguir.

        mysql -u dbadmin3 -p -h testmysql.cloudapp.net


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como executar MySQL, consulte a [Documentação de MySQL](http://dev.mysql.com/doc/).
