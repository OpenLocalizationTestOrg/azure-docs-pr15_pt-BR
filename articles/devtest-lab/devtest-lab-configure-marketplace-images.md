<properties
    pageTitle="Configurar definições de imagem do Azure Marketplace nos exercícios do Azure DevTest | Microsoft Azure"
    description="Configurar quais imagens do Azure Marketplace podem ser usadas ao criar uma máquina virtual nos exercícios de DevTest do Azure"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="tarcher"/>

# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Configurar definições de imagem do Azure Marketplace nos exercícios de DevTest do Azure

DevTest Labs dá suporte à criação VMs com base em imagens do Azure Marketplace dependendo de como você configurou imagens do Azure Marketplace para ser usado em seu laboratório. Este artigo mostra como especificar que, se houver, imagens do Azure Marketplace podem ser usadas ao criar VMs em um laboratório.

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Selecionar quais imagens do Azure Marketplace permitidas ao criar uma máquina virtual

1. Entrar no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Mais serviços**e, em seguida, selecione **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado. 

1. Na lâmina do laboratório, selecione **a configuração**.
    
1. Na lâmina de **configuração** do laboratório, selecione **imagens do Marketplace**

1. Especifique se deseja que todas as imagens do Azure Marketplace qualificadas esteja disponível para ser usado como uma base de uma nova VM. Se você selecionar **Sim**, todas as imagens do Azure Marketplace que atendem a todos os critérios a seguir são permitidas no laboratório:

    - A imagem cria uma única VM **e**
    - A imagem usa o Gerenciador de recursos do Azure para provisionar VMs **e**
    - A imagem não requer a compra de um plano extra licenciamento
    
    Se quiser sem imagens a serem permitidas ou que você deseja especificar quais imagens podem ser usadas, selecione **não**.
 
    ![Opção para permitir que todas as imagens de Marketplace ser usado como base imagens para VMs](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
 
1. Se você selecionar **não** para a etapa anterior, a caixa de seleção de **Imagens permitidos/Selecionar tudo** está habilitada. Você pode usar esta opção junto com a caixa de pesquisa para rapidamente, marcar ou desmarcar todos os itens exibidos na lista.
Você também pode selecionar as imagens do Azure Marketplace que você deseja permitir para a criação de máquina virtual individualmente marcando a caixa de seleção correspondente cada imagem.
Selecione nada na lista se você não quiser permitir que qualquer imagens do Azure Marketplace a serem usadas no laboratório.

    ![Você pode especificar quais imagens do Azure Marketplace podem ser usadas como imagens base para VMs](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas

Depois de ter configurado como imagens do Azure Marketplace permitidas ao criar uma máquina virtual, a próxima etapa é [Adicionar uma máquina virtual para seu laboratório](./devtest-lab-add-vm-with-artifacts.md).