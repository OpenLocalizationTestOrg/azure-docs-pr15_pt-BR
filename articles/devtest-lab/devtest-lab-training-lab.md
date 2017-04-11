<properties
    pageTitle="Usar o Azure DevTest Labs para treinamento | Microsoft Azure"
    description="Saiba como usar o Azure DevTest Labs para cenários de treinamento."
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="sdanie"/>

# <a name="use-azure-devtest-labs-for-training"></a>Usar o Azure DevTest Labs para treinamento

Azure Labs DevTest podem ser usados para implementar o muitos cenários principais, além de desenvolvimento/teste. Um desses cenários é configurar um laboratório de treinamento. Azure Labs DevTest permite que você crie um laboratório onde você pode fornecer modelos personalizados que cada estagiário pode usar para criar ambientes idênticos e isolados treinamento. Você pode garantir que os ambientes de treinamento estão disponíveis para cada estagiário somente quando precisam e contêm recursos suficientes - como máquinas virtuais - necessários para o treinamento. Por fim, você pode compartilhar facilmente o laboratório com estagiários, o que eles podem acessar um único clique.   

![Usar DevTest Labs para treinamento](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure Labs DevTest atende aos seguintes requisitos necessários para conduzir treinamento em qualquer ambiente virtual: 


-   Estagiários não consegue ver VMs criadas por outros estagiários
-   Cada máquina de treinamento deve ser idêntica
-   Estagiários podem provisionar rapidamente seus ambientes de treinamento
-   Controle de custo, garantindo que estagiários não conseguem acessar mais VMs que precisam para o treinamento e também desligamento VMs quando eles não estiverem usando-los
-   Compartilhar facilmente o laboratório de treinamento com cada estagiário
-   Reutilizar o laboratório de treinamento


Neste artigo, você aprenderá diversos recursos do Azure DevTest laboratórios que podem ser usados para atender aos requisitos de treinamento descrito anteriormente e as etapas detalhadas que você pode seguir para configurar um laboratório de treinamento.  


## <a name="implementing-training-with-azure-devtest-labs"></a>Implementação de treinamento com laboratórios de DevTest do Azure

1. **Criar o laboratório** 

    Laboratórios são o ponto de partida nos exercícios de DevTest do Azure. Depois de criar um laboratório, você pode executar tarefas tais como adicionar usuários (estagiários) ao laboratório, definir políticas para controlar os custos, defina as imagens de máquina virtual que podem criar rapidamente e muito mais.   

    Saiba mais clicando nos links na tabela a seguir:

  	| Tarefa                                                            | O que você aprender                                                    |
|-----------------------------------------------------------------|----------------------------------------------------------------------|
| [Criar um laboratório nos exercícios de DevTest do Azure](devtest-lab-create-lab.md) | Saiba como criar um laboratório nos Azure DevTest exercícios no portal do Azure. |

2. **Criar VMs de treinamento em minutos usando imagens prontas marketplace e personalizados** 
    
    Você pode escolher imagens prontas de uma grande variedade de imagens no mercado do Azure e disponibilizá-los para os estagiários no laboratório. Se as imagens prontas não atender às suas necessidades, você pode criar uma imagem personalizada pela criação de um laboratório máquina virtual usando uma imagem pronta do Azure Marketplace, instalar o software que você precisa para o treinamento e salvar a máquina virtual como uma imagem personalizada no laboratório. 

    Saiba mais clicando nos links na tabela a seguir:

  	| Tarefa                                                                              | O que você aprender                                                                                                                                  |
|-----------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| [Configurar imagens do Azure Marketplace](devtest-lab-configure-marketplace-images.md) | Saiba como você pode imagens do Azure Marketplace branca; disponibilizando para seleção somente as imagens desejadas para o treinamento.                 |
| [Criar uma imagem personalizada](devtest-lab-create-template.md)                           | Crie uma imagem personalizada pré-instalando o software que necessário para o treinamento para que estagiários podem criar rapidamente uma máquina virtual usando a imagem personalizada. |

3. **Criar modelos reutilizáveis para máquinas de treinamento** 

    Uma fórmula nos Azure DevTest exercícios é uma lista de valores de propriedade padrão usado para criar uma máquina virtual. Você pode criar uma fórmula no laboratório escolhendo uma imagem, um tamanho de máquina virtual (uma combinação de CPU e memória RAM) e uma rede virtual. Cada estagiário pode ver a fórmula no laboratório e usá-lo para criar uma máquina virtual. 

    Saiba mais clicando nos links na tabela a seguir:

  	| Tarefa                                                                         | O que você aprender                                                                                                          |
|------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------|
| [Gerenciar fórmulas de DevTest Labs para criar VMs](devtest-lab-manage-formulas.md) | Saiba como você pode criar uma fórmula selecionando uma imagem, tamanho de máquina virtual (combinação de CPU e RAM) e uma rede virtual. |

4. **Controlar os custos**

    Azure Labs DevTest permite que você defina uma política no laboratório para especificar o número máximo de VMs que podem ser criados por um estagiário no laboratório. 

    Se você estiver conduzindo treinamento de vários dia e deseja parar todas as VMs em uma hora específica do dia e, em seguida, reinicie automaticamente-los do dia seguinte, pode facilmente realizar que definindo autodesligamento e início automático políticas no laboratório. 

    Finalmente, ao treinamento é concluído você pode excluir todas as VMs ao mesmo tempo executando um único script do PowerShell. 

    Saiba mais clicando nos links na tabela a seguir:

  	| Tarefa                                                                                                                                    | O que você aprender                                                      |
|-----------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------|
| [Definir políticas de laboratório](devtest-lab-set-lab-policy.md)                                                                                    | Controlar os custos definindo políticas no laboratório.                       |
| [Excluir todo o laboratório VMs usando um script do PowerShell](devtest-lab-faq.md#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) | Exclua todos os laboratórios em uma operação quando o treinamento estiver concluído. |

5. **Compartilhar o laboratório com cada estagiário**

    Laboratórios podem ser acessados diretamente usando um link que você compartilha com seu estagiários. Seu estagiários não ainda precisam ter uma conta do Azure, desde que eles têm uma [conta da Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Estagiários não consigo ver VMs criadas por outros estagiários.  

    Saiba mais clicando nos links na tabela a seguir:

  	| Tarefa                                                                                                                                | O que você aprender                                                   |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
| [Adicionar um estagiário para um laboratório nos exercícios de DevTest do Azure](devtest-lab-add-devtest-user.md)                                                     | Use o portal do Azure para adicionar estagiários ao seu laboratório de treinamento.       |
| [Adicionar estagiários ao laboratório usando um script PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) | Use o PowerShell para automatizar estagiários adicionando ao seu laboratório de treinamento. |
| [Obter um link para o laboratório](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab)                                                  | Saiba como um laboratório pode ser acessado diretamente por meio de um hiperlink.        |

6. **Reutilizar o laboratório** 

    Você pode automatizar a criação de laboratório, incluindo configurações personalizadas, criando um modelo do Gerenciador de recursos e usá-lo para criar laboratórios idênticos novamente. 

    Saiba mais clicando nos links na tabela a seguir:

  	| Tarefa                                                                                                                               | O que você aprender                                                      |
|------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------|
| [Criar um laboratório usando um modelo do Gerenciador de recursos](devtest-lab-faq.md#how-do-i-create-a-lab-from-an-azure-resource-manager-template) | Crie laboratórios nos exercícios de DevTest do Azure usando modelos do Gerenciador de recursos. |

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]  

