<properties 
    pageTitle="Configurar uma rede virtual usando um arquivo de configuração de rede" 
    description="Instruções para exportar e importar um arquivo de configuração de rede para o Portal de gerenciamento do Azure para criar ou modificar as redes virtuais. " 
    services="virtual-network" 
    documentationCenter="" 
    authors="jimdial" 
    manager="carmonm" 
    editor="tysonn"/>

<tags
    ms.service="virtual-network"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services" 
    ms.date="03/15/2016"
    ms.author="jdial"/>

# <a name="configure-a-virtual-network-using-a-network-configuration-file"></a>Configurar uma rede virtual usando um arquivo de configuração de rede

Você pode configurar uma rede virtual (VNet) usando o portal de gerenciamento do Azure ou usando um arquivo de configuração de rede.

## <a name="creating-and-modifying-a-network-configuration-file"></a>Criando e modificando um arquivo de configuração de rede 
A maneira mais fácil de criar um arquivo de configuração de rede é exportar as configurações de rede de uma configuração de rede virtual existente, modifique o arquivo para conter as configurações que você deseja configurar para suas redes virtuais.

Para editar o arquivo de configuração de rede, você pode simplesmente abrir o arquivo, faça as alterações apropriadas e salve o arquivo. Você pode usar qualquer editor de *xml* para fazer alterações no arquivo de configuração de rede. 

Estreitamente siga as orientações para [definições de esquema de arquivo de configuração de rede](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

Azure considera uma sub-rede com algo implantado-lo como **em uso**. Quando uma sub-rede está em uso, ela não pode ser modificada. Antes de modificar, mova algo que você implantou à sub-rede para uma sub-rede diferente que não está sendo modificada.   Consulte [Mover uma máquina virtual ou instância de função para uma sub-rede diferente](virtual-networks-move-vm-role-to-subnet.md).

## <a name="export-and-import-virtual-network-settings-using-the-management-portal"></a>Importar e exportar definições de rede virtual usando o Portal de gerenciamento  
Você pode importar e exportar definições de configuração de rede contidas no seu arquivo de configuração de rede usando o PowerShell ou o Portal de gerenciamento. As instruções a seguir o ajudará a exportação e importação usando o Portal de gerenciamento. 

### <a name="to-export-your-network-settings"></a>Para exportar as configurações de rede
Quando você exporta, todas as configurações para as redes virtuais em sua assinatura serão gravadas para um arquivo. XML. 

1. Log para o **Portal de gerenciamento**.
2. No Portal de gerenciamento, na parte inferior da página de **redes** , clique em **Exportar**. 
3. Na janela **Exportar configuração de rede** , verifique se você selecionou a assinatura à qual você deseja exportar as configurações de rede. Em seguida, clique na marca de seleção na parte inferior direita. 
4. Quando for solicitado, salve o arquivo de *NetworkConfig.xml* para o local de sua escolha.


### <a name="to-import-your-network-settings"></a>Importar as configurações de rede

1. No **Portal de gerenciamento**, no painel de navegação no canto inferior esquerdo, clique em **novo**.
2. Clique em **Serviços de rede** -> **rede Virtual** -> **configuração de importação**.
3. Na página **importar o arquivo de configuração de rede** , navegue até o arquivo de configuração de rede e, em seguida, clique na seta **próxima** .
4. Na página de **criação de sua rede** , você verá informações na tela mostrando quais seções de sua configuração de rede serão alteradas ou criadas. Se as alterações parecer corretas para você, clique na marca de seleção para prosseguir para atualizar ou criar sua rede virtual. 