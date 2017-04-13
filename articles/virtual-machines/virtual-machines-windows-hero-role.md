<properties
    pageTitle="Instalar o IIS em sua máquina de virtual do Windows primeiro | Microsoft Azure"
    description="Experimentar sua primeira máquina virtual Windows instalando o IIS e abrindo a porta 80 usando o portal do Azure."
    keywords=""
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="cynthn"/>

# <a name="experiment-with-installing-a-role-on-your-windows-vm"></a>Experimentar instalando uma função em sua máquina virtual Windows
    
Quando tiver sua primeira máquina virtual (VM) em funcionamento, você pode mover para instalar software e serviços. Neste tutorial, vamos usar o Gerenciador de servidor na VM Windows Server para instalar o IIS. Em seguida, vamos criar um grupo de segurança de rede (NSG) usando o portal do Azure para abrir a porta 80 para o tráfego IIS. 

Se você já não criou sua máquina virtual primeiro, você deve voltar para [criar sua primeira máquina virtual Windows no portal do Azure](virtual-machines-windows-hero-tutorial.md) antes de continuar com este tutorial.

## <a name="make-sure-the-vm-is-running"></a>Certificar-se de que a máquina virtual está em execução

1. Abra o [portal do Azure](https://portal.azure.com).
2. No menu hub, clique em **máquinas virtuais**. Selecione a máquina virtual na lista.
3. Se o status for **interrompido (Deallocated)**, clique no botão **Iniciar** na lâmina **Essentials** da máquina virtual. Se o status for **em execução**, você pode mover para a próxima etapa.

## <a name="connect-to-the-virtual-machine-and-sign-in"></a>Conectar-se à máquina virtual e entrar

1.  No menu hub, clique em **máquinas virtuais**. Selecione a máquina virtual na lista.

3. Na lâmina da máquina virtual, clique em **Conectar**. Isso cria e faz o download de um protocolo de área de trabalho remota (arquivo. rdp) é como um atalho para se conectar à sua máquina. Talvez você queira salvar o arquivo em sua área de trabalho para fácil acesso. **Abrir** este arquivo para se conectar à sua máquina virtual.

    ![Captura de tela do portal do Azure mostrando como conectar à sua máquina virtual](./media/virtual-machines-windows-hero-tutorial/connect.png)

4. Você recebe um aviso que o. rdp é de um fornecedor desconhecido. Isso é normal. Na janela da área de trabalho remota, clique em **Conectar** para continuar.

    ![Captura de tela de um aviso sobre um fornecedor desconhecido](./media/virtual-machines-windows-hero-tutorial/rdp-warn.png)

5. Na janela de segurança do Windows, digite o nome de usuário e a senha da conta local que você criou quando você criou a máquina virtual. O nome de usuário é inserida como *vmname*& #92; *nome de usuário*, clique em **Okey**.

    ![Captura de tela de inserir o nome de máquina virtual, nome de usuário e senha](./media/virtual-machines-windows-hero-tutorial/credentials.png)
    
6.  Você recebe um aviso que o certificado não pode ser verificado. Isso é normal. Clique em **Sim** para verificar a identidade da máquina virtual e término logon.

    ![Captura de tela mostrando uma mensagem sobre a verificar a identidade da máquina virtual](./media/virtual-machines-windows-hero-tutorial/cert-warning.png)


Se você tiver problemas quando você tenta se conectar, consulte [Solucionar problemas de área de trabalho remota conexões para uma baseado no Windows Azure Máquina Virtual](virtual-machines-windows-troubleshoot-rdp-connection.md).


## <a name="install-iis-on-your-vm"></a>Instalar o IIS em sua máquina virtual

Agora que você estiver conectado à máquina virtual, podemos será instalar uma função de servidor de modo que você possa experimentar mais.

1. Abra o **Gerenciador de servidor** se ele ainda não estiver aberto. Clique no menu **Iniciar** e clique em **Gerenciador de servidor**.
2. No **Gerenciador de servidor**, selecione **Servidor Local** no painel esquerdo. 
3. No menu, selecione **Gerenciar** > **Adicionar funções e recursos**.
4. No Assistente de recursos, na página **Tipo de instalação** e adicionar funções escolha **instalação baseada em função ou baseada em recursos**e clique em **Avançar**.

    ![Captura de tela mostrando a guia Assistente de recursos e adicionar funções para tipo de instalação](./media/virtual-machines-windows-hero-tutorial/role-wizard.png)

5. Selecione a máquina virtual do pool de servidor e clique em **Avançar**.
6. Na página de **Funções de servidor** , selecione **Servidor Web (IIS)**.

    ![Captura de tela mostrando a guia Assistente de recursos e adicionar funções para funções de servidor](./media/virtual-machines-windows-hero-tutorial/add-iis.png)

7. No menu pop-up sobre a adição de recursos necessários para o IIS, certifique-se de que **incluem ferramentas de gerenciamento** está selecionada e clique em **Adicionar recursos**. Quando fecha o pop-up, clique em **Avançar** no assistente.

    ![Captura de tela mostrando pop-up para confirmar adicionando a função do IIS](./media/virtual-machines-windows-hero-tutorial/confirm-add-feature.png)

8. Na página recursos, clique em **Avançar**.
9. Na página **Função de servidor de Web (IIS)** , clique em **Avançar**. 
10. Na página **Serviços de função** , clique em **Avançar**. 
11. Na página **confirmação** , clique em **instalar**. 
12. Quando a instalação estiver concluída, clique em **Fechar** no assistente.



## <a name="open-port-80"></a>Abra a porta 80 

Em ordem para sua máquina virtual aceitar o tráfego de entrada pela porta 80, você precisa adicionar uma regra de entrada para o grupo de segurança de rede. 

1. Abra o [portal do Azure](https://portal.azure.com).
2. Em **máquinas virtuais** , selecione a máquina virtual que você criou.
3. Nas configurações máquinas virtuais, selecione **interfaces de rede** e selecione a interface de rede existente.

    ![Captura de tela mostrando a configuração de máquina virtual para interfaces de rede](./media/virtual-machines-windows-hero-tutorial/network-interface.png)

4. No **Essentials** para a interface de rede, clique no **grupo de segurança de rede**.

    ![Captura de tela mostrando a seção Essentials para a interface de rede](./media/virtual-machines-windows-hero-tutorial/select-nsg.png)

5. Na lâmina **Essentials** para o NSG, você deve ter um padrão entrada regra existente para **rdp permitir padrão** que permite que você acesse a máquina virtual. Você irá adicionar outra regra de entrada para permitir o tráfego do IIS. Clique em **regra de segurança de entrada**.

    ![Captura de tela mostrando a seção Essentials para o NSG](./media/virtual-machines-windows-hero-tutorial/inbound.png)

6. Em **regras de segurança de entrada**, clique em **Adicionar**.

    ![Captura de tela mostrando o botão Adicionar uma regra de segurança](./media/virtual-machines-windows-hero-tutorial/add-rule.png)

7. Em **regras de segurança de entrada**, clique em **Adicionar**. Digite **80** no intervalo de portas e verifique se **que permitir** está marcada. Quando terminar, clique em **Okey**.

    ![Captura de tela mostrando o botão Adicionar uma regra de segurança](./media/virtual-machines-windows-hero-tutorial/port-80.png)
 
Para obter mais informações sobre NSGs, regras de entrada e saída, consulte [Permitir acesso externo para sua máquina virtual usando o portal do Azure](virtual-machines-windows-nsg-quickstart-portal.md)
 
## <a name="connect-to-the-default-iis-website"></a>Conectar ao site do IIS padrão

1. No portal do Azure, clique em **máquinas virtuais** e selecione sua máquina virtual.
2. Na lâmina **Essentials** , copie o **endereço IP público**.

    ![Captura de tela mostrando onde encontrar o endereço IP público da sua máquina virtual](./media/virtual-machines-windows-hero-tutorial/ipaddress.png)

2. Abra um navegador e na barra de endereços, digite seu endereço IP público assim: http://<publicIPaddress> e clique em **Enter** para ir para esse endereço.
3. Seu navegador deve abrir a página da web IIS padrão. Parece que algo parecido com isto:

    ![Captura de tela mostrando a aparência de página padrão IIS em um navegador](./media/virtual-machines-windows-hero-tutorial/iis-default.png)

    

## <a name="next-steps"></a>Próximas etapas

- Você também pode experimentar [anexar um disco de dados](virtual-machines-windows-attach-disk-portal.md) para sua máquina virtual. Discos de dados fornecem mais armazenamento para sua máquina virtual.
