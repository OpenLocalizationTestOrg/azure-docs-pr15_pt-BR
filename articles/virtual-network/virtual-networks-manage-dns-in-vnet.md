<properties 
   pageTitle="Gerenciar servidores DNS usados por uma rede virtual (VNet)"
   description="Saiba como adicionar e remover servidores DNS em uma rede virtual (vnet)"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="manage-dns-servers-used-by-a-virtual-network-vnet"></a>Gerenciar servidores DNS usados por uma rede virtual (VNet)

Você pode gerenciar a lista de servidores DNS usados em um VNet no Portal de gerenciamento ou no arquivo de configuração de rede. Você pode adicionar até 12 servidores DNS para cada VNet. Ao especificar servidores DNS, é importante confirmar que você lista os servidores DNS na ordem correta para seu ambiente. Listas de servidores DNS não funcionam alternada. Eles são usados na ordem em que eles são especificados. Se o primeiro servidor DNS na lista for capaz de ser acessada, o cliente usará esse servidor DNS, independentemente se o servidor DNS está funcionando corretamente ou não. Para alterar a ordem de servidor DNS para sua rede virtual, remova os servidores DNS na lista e adicioná-los na ordem desejada.

>[AZURE.WARNING] Depois que a lista DNS foi atualizada, você deve reiniciar máquinas virtuais localizadas na sua rede virtual para que ela atende as novas configurações de servidor DNS. Máquinas virtuais vão continuar a usar sua configuração atual até que eles são reiniciados.

## <a name="edit-a-dns-server-list-for-a-virtual-network-using-the-management-portal"></a>Editar uma lista de servidor DNS para uma rede virtual usando o Portal de gerenciamento

1. Faça logon no **Portal de gerenciamento**.

1. No painel de navegação, clique em **redes**e clique no nome da sua rede virtual na coluna **nome** .

1. Clique em **Configurar**.

1. Em **Servidores DNS**, você pode configurar o seguinte:

    - **Para registrar servidor (Adicionar) um novo DNS –** Basta digite o nome e o endereço IP nas caixas. Isso adiciona um servidor DNS à sua lista de servidores DNS de rede virtual e também registra o servidor DNS com o Azure.

    - **Para adicionar um servidor DNS que anteriormente registrado –** Se você já registrado um servidor DNS com o Azure, você pode selecioná-lo na lista pré-populadas.

    - **Para remover um servidor DNS da sua rede virtual –** Clique no X ao lado do servidor que você deseja remover. Observe que isso remove somente o servidor dessa lista de rede virtual. O servidor DNS permanece registrado no Azure para seu outras redes virtuais para usar. Para excluir um servidor DNS da sua assinatura, vá para a página de **redes -> servidores DNS** .

    - **Para reordenar os servidores DNS –** Remover todos os servidores DNS que estão listados e, em seguida, adicioná-los novamente na ordem desejada. Lembre-se de que isso não é uma lista DNS alternada.

    - **Para renomear um servidor DNS –** Realçar o servidor DNS na lista e, em seguida, digite o novo nome. Isso irá registrar um novo servidor DNS no Azure, bem como adicioná-lo à lista de servidores DNS para sua rede virtual. O servidor DNS antigo e seu endereço IP permanecerão registrados com o Azure. Você pode excluí-lo na página **DNS Servers** , se você não estiver usando-la para qualquer outra rede virtual.

1. Clique em **Salvar** na parte inferior da página para salvar sua nova configuração de servidores DNS.

1. Reinicie as máquinas virtuais localizadas na rede virtual para permitir que ele adquirir as novas configurações de DNS.

## <a name="edit-a-dns-server-list-using-a-network-configuration-file"></a>Editar uma lista de servidor DNS usando um arquivo de configuração de rede

Para editar uma lista de servidores DNS usando um arquivo de configuração de rede, primeiro você vai exportar as configurações do Portal de gerenciamento. Você, em seguida, edite o arquivo de configuração de rede e importe-o novamente por meio do Portal de gerenciamento. Abaixo está uma lista de alto nível das etapas para concluir este processo.

1. Exporte as configurações de rede virtual para um arquivo de configuração de rede. Para obter mais informações e etapas para exportar suas configurações de rede, consulte [Exportar definições de rede Virtual para um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md).

1. Especifica as informações do servidor DNS para sua rede virtual. Para obter mais informações sobre como especificar um servidor DNS, consulte [especificar um servidor DNS em um arquivo de configuração de rede Virtual](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md). Para obter informações adicionais sobre os arquivos de configuração de rede, consulte [Esquema de configuração de rede Virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) e [Configurar uma rede Virtual utilizando um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md).

1. Importe o arquivo de configuração de rede. Para obter mais informações e etapas para importar seu arquivo de configuração de rede, consulte [Importar um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md).

1. Reinicie as máquinas virtuais localizadas na rede virtual para permitir que ele adquirir as novas configurações de DNS.
