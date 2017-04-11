<properties
   pageTitle="Soluções no pacote de gerenciamento de operações (OMS) | Microsoft Azure"
   description="Soluções estendem a funcionalidade do pacote de gerenciamento de operações (OMS), fornecendo cenários de pacotes de gerenciamento que os clientes podem adicionar ao seu espaço de trabalho do OMS.  Este artigo fornece detalhes sobre como personalizadas soluções criadas por clientes e parceiros."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="bwren" />

# <a name="management-solutions-in-operations-management-suite-oms-preview"></a>Soluções de gerenciamento no pacote de gerenciamento de operações (OMS) (prévia)

>[AZURE.NOTE]Esta é a documentação preliminar para soluções de gerenciamento do OMS que estão atualmente na visualização.    

Soluções de gerenciamento de estendem a funcionalidade do pacote de gerenciamento de operações (OMS), fornecendo cenários de pacotes de gerenciamento que os clientes podem adicionar ao seu ambiente.  Além de [soluções fornecidos pela Microsoft](../log-analytics/log-analytics-add-solutions.md), parceiros e clientes podem criar soluções de gerenciamento a ser usado no seu próprio ambiente ou disponibilizados para os clientes através da comunidade.

## <a name="finding-and-installing-management-solutions"></a>Localizando e instalando soluções de gerenciamento
Há vários métodos para localizar e instalar soluções de gerenciamento, conforme descrito nas seções a seguir.

### <a name="azure-marketplace"></a>Azure Marketplace
Soluções de gerenciamento fornecidos pela Microsoft e parceiros confiáveis podem ser instalados do Azure Marketplace no portal do Azure.

1. Faça logon no portal do Azure.
2. No painel esquerdo, selecione **mais serviços**.
3. Role para baixo até **soluções** ou digite *soluções* na caixa de diálogo **filtro** .
4. Clique no botão **+ Adicionar** .
5. Procurar soluções que você está interessado por navegar, clicando no botão de **filtro** ou digitando na caixa de **Pesquisa Everthing** .
6. Clique em um item de marketplace para exibir as informações detalhadas.
4. Clique em **criar** para abrir o painel **Adicionar solução** .
5. Você será solicitado a informações necessárias, como o [espaço de trabalho do OMS e conta de automação](#oms-workspace-and-automation-account) além dos valores para os parâmetros na solução.
6. Clique em **criar** para instalar a solução.

### <a name="oms-portal"></a>Portal OMS
Soluções de gerenciamento fornecida pela Microsoft podem estar instaladas na Galeria de soluções do portal do OMS.

1. Faça logon no portal do OMS.
2. Clique no bloco da **Galeria de soluções** .
2. Na página Galeria de soluções de OMS, saiba mais sobre cada solução disponível. Clique no nome da solução que você deseja adicionar ao OMS.
3. Na página para a solução que você escolheu, informações detalhadas sobre a solução são exibidas. Clique em **Adicionar**.
4. Um novo bloco para a solução que você adicionou aparece na visão geral de página em OMS e você pode começar a usá-la após o serviço OMS processa seus dados.

### <a name="azure-quickstart-templates"></a>Modelos de início rápido Azure
Os membros da comunidade podem enviar soluções de gerenciamento de modelos de início rápido do Azure.  Você pode baixar esses modelos para instalação posterior ou inspecioná-los para saber como [criar suas próprias soluções](#creating-a-solution).

1. Siga o processo descrito no [espaço de trabalho do OMS e conta de automação](#oms-workspace-and-automation-account) para vincular a um espaço de trabalho e uma conta.
2. Vá para [modelos de início rápido Azure](https://azure.microsoft.com/documentation/templates/).  
3. Procurar uma solução que você está interessado.
4. Selecione a solução dos resultados para exibir seus detalhes.
5. Clique no botão de **implantar no Azure** .
6. Você será solicitado a fornecer informações, como o grupo de recursos e o local além dos valores para os parâmetros na solução.
7. Clique em **adquirir** para instalar a solução.

### <a name="deploy-azure-resource-manager-template"></a>Implantar o modelo do Gerenciador de recursos do Azure
Soluções que você obtenha da comunidade do ou que você [mesmo cria](#creating-a-solution) são implementadas como um modelo do Gerenciador de recursos, e você pode usar qualquer um dos métodos padrão para [implantar um modelo](../resource-group-template-deploy-portal.md).  Observe que, antes de instalar a solução, você deve criar e vincular o [espaço de trabalho do OMS e conta de automação](#oms-workspace-and-automation-account).

## <a name="oms-workspace-and-automation-account"></a>Espaço de trabalho do OMS e conta de automação
A maioria das soluções de gerenciamento exigem um [espaço de trabalho OMS](../log-analytics/log-analytics-manage-access.md) contêm exibições e uma [conta de automação](../automation/automation-security-overview.md#automation-account-overview) para conter runbooks e recursos relacionados. O espaço de trabalho e a conta devem atender aos seguintes requisitos.

- Uma solução só pode usar um espaço de trabalho do OMS e uma conta de automação.  
- O espaço de trabalho do OMS e a conta de automação usado por uma solução devem ser vinculados uns aos outros. Um espaço de trabalho do OMS só pode ser vinculado a uma conta de automação e uma conta de automação só pode ser vinculada a um espaço de trabalho do OMS.
- Para ser vinculado, o espaço de trabalho do OMS e a conta de automação devem ser no mesmo grupo de recursos e região.  A exceção é um espaço de trabalho OMS na região Leste EUA e e a conta de automação no Leste dos EUA 2.

### <a name="creating-a-link-between-an-oms-workspace-and-automation-account"></a>Criar um vínculo entre um espaço de trabalho do OMS e a conta de automação
Como você especifica o espaço de trabalho do OMS e conta de automação depende do método de instalação para sua solução.

- Quando você instala uma solução Microsoft por meio do portal OMS, ele é instalado no espaço de trabalho atual do OMS e nenhuma conta de automação é necessária.

- Quando você instala uma solução por meio do Azure Marketplace, você será solicitado para um espaço de trabalho do OMS e a conta de automação e o vínculo entre elas é criado para você.  

- Para soluções fora do Azure Marketplace, você deve vincular o espaço de trabalho do OMS e a conta de automação antes de instalar a solução.  Você pode fazer isso selecionando qualquer solução no mercado do Azure e selecionando o espaço de trabalho do OMS e a conta de automação.  Você não precisa instalar realmente a solução porque o vínculo será criado assim que o espaço de trabalho do OMS e a conta de automação são selecionadas.  Depois de criado o link, então você pode usar esse espaço de trabalho do OMS e a conta de automação para qualquer solução. 

### <a name="verifying-the-link-between-an-oms-workspace-and-automation-account"></a>Verificando o vínculo entre um espaço de trabalho do OMS e a conta de automação
Você pode verificar o vínculo entre um espaço de trabalho do OMS e uma conta de automação usando o procedimento a seguir.

1. Selecione a conta de automação no portal do Azure.
2. Role até a parte inferior do painel **configurações** .
3. Se houver uma seção chamada **OMS recursos** no painel **configurações** , essa conta é anexada a um espaço de trabalho do OMS.
4. Selecione o **espaço de trabalho** para exibir os detalhes do espaço de trabalho OMS vinculadas a esta conta de automação.


## <a name="listing-management-solutions"></a>Soluções de gerenciamento de listagem
Use o procedimento a seguir para exibir as soluções de gerenciamento nos espaços de trabalho vinculados à sua assinatura do Azure.

1. Faça logon no portal do Azure.
2. No painel esquerdo, selecione **mais serviços**.
3. Role para baixo até **soluções** ou digite *soluções* na caixa de diálogo **filtro** .
4. Soluções instaladas em todos os seus espaços de trabalho serão listadas.

Observe que você pode exibir apenas as soluções do Microsoft instaladas no espaço de trabalho atual usando o portal OMS.

## <a name="removing-a-management-solution"></a>Removendo uma solução de gerenciamento
Quando uma solução de gerenciamento é removida, todos os recursos da solução também são removidos.  

1. Localize a solução no portal do Azure usando o procedimento na [listagem soluções](#listing-solutions).
2. Selecione a solução que você deseja remover.
3. Clique no botão **Excluir** .

## <a name="creating-a-management-solution"></a>Criando uma solução de gerenciamento
Guia completo sobre a criação de soluções de gerenciamento estão disponíveis em [Criando soluções no pacote de gerenciamento de operações (OMS)](operations-management-suite-solutions-creating.md). 


## <a name="next-steps"></a>Próximas etapas

- Pesquisar [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates) para amostras de modelos diferentes do Gerenciador de recursos.
- Crie suas próprias [soluções de gerenciamento](operations-management-suite-solutions-creating.md).
 