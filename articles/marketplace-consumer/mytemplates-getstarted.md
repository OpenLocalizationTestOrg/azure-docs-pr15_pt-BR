<properties
   pageTitle="Começar a usar modelos privados | Microsoft Azure"
   description="Adicionar, gerenciar e compartilhar seus modelos privados usando o portal do Azure, o Azure CLI ou PowerShell."
   services="marketplace-customer"
   documentationCenter=""
   authors="VybavaRamadoss"
   manager="asimm"
   editor=""
   tags="marketplace, azure-resource-manager"
   keywords=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2016"
   ms.author="vybavar"/>

# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Começar a usar modelos particulares no Portal do Azure

Um modelo do [Gerenciador de recursos do Azure](../resource-group-authoring-templates.md) é um modelo declarativo usado para definir sua implantação. Você pode definir os recursos para implantar uma solução e especificar parâmetros e variáveis que permitem valores para diferentes ambientes de entrada. O modelo consiste em JSON e expressões que você pode usar para construir valores para a sua implantação.

Você pode usar o novo recurso de **modelos** no [Portal do Azure](https://portal.azure.com) juntamente com o provedor de recursos de **Microsoft.Gallery** como uma extensão do [Azure Marketplace](https://azure.microsoft.com/marketplace/) para permitir que os usuários criar, gerenciar e implantar modelos de particulares de uma biblioteca pessoal.

Este documento orienta você por meio da adição, gerenciar e compartilhar um particular **modelo** usando o Portal do Azure.

## <a name="guidance"></a>Orientação

As sugestões a seguir o ajudarão a aproveitar dos **modelos** ao trabalhar com suas soluções:

- Um **modelo** é um recurso encapsular que contém um modelo do Gerenciador de recursos e metadados adicionais. Ele se comporta de forma muito semelhante a um item no mercado. A principal diferença é que se trata de um item particular em vez dos itens de Marketplace públicos.
- A biblioteca de **modelos** funciona bem para os usuários que precisam personalizar suas implantações.
- **Modelos** funcionam bem para os usuários que precisam de um repositório simple dentro do Azure.
- Comece com um modelo existente do Gerenciador de recursos. Encontre modelos no [github](https://github.com/Azure/azure-quickstart-templates) ou [Exportar modelo](../resource-manager-export-template.md) de um grupo de recursos existente.
- **Modelos** estão vinculados ao usuário que publica-los. O nome do Editor fica visível para qualquer pessoa que tenha acesso de leitura.
- **Modelos** são recursos do Gerenciador de recursos e não podem ser renomeados uma vez publicadas.

## <a name="add-a-template-resource"></a>Adicionar um recurso de modelo

Há duas maneiras de criar um recurso de **modelo** no portal do Azure.

### <a name="method-1--create-a-new-template-resource-from-a-running-resource-group"></a>Método 1: Criar um novo recurso de modelo de um grupo de recursos em execução

1. Navegar para um grupo de recursos existentes no Portal do Azure. Selecione **Exportar modelo** nas **configurações**.
2. Depois que o modelo do Gerenciador de recursos é exportado, use o botão **Salvar modelo** salvá-lo no repositório de **modelos** . Encontrar detalhes completos para exportar modelo [aqui](../resource-manager-export-template.md).
<br /><br />
![Exportação de grupo de recursos](media/rg-export-portal1.PNG)  <br />

3. Selecione o botão de comando **Salvar modelo** .
<br /><br />

4. Insira as seguintes informações:

    - Nome – nome do objeto de modelo (Observação: Este é o nome de um gerente de recursos do Azure com base. Aplicam todas as restrições de nomes e não pode ser alterado depois de criada).
    - Descrição – resumo rápido sobre o modelo.

    ![Salvar modelo](media/save-template-portal1.PNG)  <br />

5. Clique em **Salvar**.

    > [AZURE.NOTE] A lâmina de modelo de exportação mostra notificações quando o modelo exportado do Gerenciador de recursos tem erros, mas você ainda poderá salvar este modelo de Gerenciador de recursos para os modelos. Certifique-se de que você deseja verifica e corrigir problemas de modelo de qualquer Gerenciador de recursos antes de reimplantar o modelo exportado do Gerenciador de recursos.

### <a name="b-method-2--add-a-new-template-resource-from-browse"></a>B. Método 2: Adicionar um novo recurso de modelo de procurar

Você também pode adicionar um novo **modelo** de rascunho usando o + adicionar botão de comando em **Procurar > modelos**. Você precisará fornecer um nome, descrição e o modelo do Gerenciador de recursos JSON.

![Adicionar modelo](media/add-template-portal1.PNG)  <br />

> [AZURE.NOTE] Microsoft.Gallery baseia um locatário provedor recurso Azure. O recurso de modelo está vinculado ao usuário que o criou. Ele não está vinculado a qualquer assinatura específica. Uma assinatura deve ser escolhido somente quando implantar um modelo.

## <a name="view-template-resources"></a>Recursos do modelo de exibição

Todos os **modelos** disponíveis para você podem ser visto **Procurar > modelos**. Isso inclui **modelos** que você criou, bem como aquelas que foram compartilhados com você com vários níveis de permissões. Mais detalhes na seção [controle de acesso](#access-control-for-a-tenant-resource-provider) abaixo.

![Modelo de exibição](media/view-template-portal1.PNG)  <br />

Você pode exibir os detalhes de um **modelo** clicando em um item na lista.

![Modelo de exibição](media/view-template-portal2c.png)  <br />

## <a name="edit-a-template-resource"></a>Editar um recurso de modelo

Você pode iniciar o fluxo de edição de um **modelo** clicando em direita o item da lista de procurar ou escolhendo o botão de comando Editar.

![Editar modelo](media/edit-template-portal1a.PNG)  <br />

Você pode editar a descrição ou texto de modelo do Gerenciador de recursos. Você não pode editar o nome desde que é um nome de recurso do Gerenciador de recursos. Quando você edita o modelo do Gerenciador de recursos JSON podemos irá validar para garantir que ele seja JSON válida. Escolha **Okey** e **Salvar** para salvar seu modelo atualizado.

![Editar modelo](media/edit-template-portal2a.PNG)  <br />

Depois que o **modelo** é salvo, você verá uma notificação de confirmação.

![Editar modelo](media/edit-template-portal3b.png)  <br />

## <a name="deploy-a-template-resource"></a>Implantar um recurso de modelo

Você pode implantar qualquer **modelo** que você tem permissões de **leitura** . O fluxo de implantação inicia a lâmina de implantação do Azure modelo padrão. Preencha os valores para os parâmetros de modelo do Gerenciador de recursos para continuar com a implantação.

![Implantar o modelo](media/deploy-template-portal1b.png)  <br />

## <a name="share-a-template-resource"></a>Compartilhar um recurso de modelo

Um recurso de **modelo** pode ser compartilhado com seus colegas. Compartilhamento se comporta de maneira semelhante a [atribuição de função para qualquer recurso no Azure](../active-directory/role-based-access-control-configure.md). O **modelo** proprietário fornece permissões para outros usuários que podem interagir com um recurso de modelo. Pessoa ou grupo de pessoas que você compartilha o **modelo** com poderão ver o modelo do Gerenciador de recursos e suas propriedades de galeria.

### <a name="access-control-for-the-microsoftgallery-resources"></a>Controle de acesso para os recursos de Microsoft.Gallery

Função | Permissões
---|----
Proprietário | Permite que o controle total sobre o recurso de modelo, incluindo o compartilhamento
Leitor | Permite a leitura e Execute(Deploy) do recurso de modelo
Colaborador | Permite a permissão de edição e exclusão do recurso de modelo. Usuário não é possível compartilhar o modelo com outras pessoas

Selecione **compartilhamento** no item procurar clicando direito ou na lâmina modo de exibição de um item específico. Isso inicia uma experiência de compartilhamento.

![Modelo de compartilhamento](media/share-template-portal1a.png)  <br />

 Agora você pode escolher uma função e um usuário ou grupo para fornecer acesso a um determinado **modelo**. As funções disponíveis são proprietário, leitor e colaborador. Mais detalhes na seção [controle de acesso](#access-control-for-a-tenant-resource-provider) acima.

![Modelo de compartilhamento](media/share-template-portal2b.png)  <br />

![Modelo de compartilhamento](media/share-template-portal3b.png)  <br />

Clique em **Selecionar** e **Okey**. Agora você pode ver os usuários ou grupos que você adicionou ao recurso.

![Modelo de compartilhamento](media/share-template-portal4b.png)  <br />

> [AZURE.NOTE] Um modelo só pode ser compartilhado com usuários e grupos no mesmo locatário Azure Active Directory. Se você compartilhar um modelo com um endereço de email que não esteja no seu locatário, um convite será enviado solicitando que o usuário para participar do locatário como um convidado.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a criação de modelos do Gerenciador de recursos, consulte [modelos de coautoria](../resource-group-authoring-templates.md)
- Para compreender as funções que você pode usar em um modelo do Gerenciador de recursos, consulte [funções de modelo](../resource-group-template-functions.md)
- Para obter orientações sobre a criação de seus modelos, consulte [práticas recomendadas para a criação de modelos do Gerenciador de recursos do Azure](../best-practices-resource-manager-design-templates.md)
