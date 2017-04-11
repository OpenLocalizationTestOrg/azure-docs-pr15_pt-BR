<properties
   pageTitle="Gerenciando sua imagem de máquina virtual no Azure Marketplace | Microsoft Azure"
   description="Guia detalhado sobre como gerenciar sua imagem de máquina virtual no Azure Marketplace após a publicação inicial."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="hascipio;"/>

# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Guia de produção POST para ofertas de máquina virtual em do Azure Marketplace

Este artigo explica como você pode atualizar uma oferta de máquina Virtual ao vivo em do Azure Marketplace. Ele também orienta você sobre o processo de adicionar um ou mais novos SKUs a uma oferta existente e remover uma oferta de máquina Virtual ou SKU ao vivo do Azure Marketplace.

Após um oferta/SKU é transferido no [Portal do Azure](http://portal.azure.com), é possível alterar os campos fornecidos abaixo:

- **Oferecer identificador:** [Portal de publicação -> máquinas virtuais -> selecione sua oferta -> guia imagens de máquina virtual -> oferecem identificador]
- **Identificador SKU:** [Portal de publicação -> máquinas virtuais -> selecione sua oferta -> SKUs guia -> Adicionar uma SKU]
- **Namespace do publisher:** [Portal de publicação -> máquinas virtuais -> explicação passo a passo guia -> diga-nos sobre sua empresa (encontrados em "Etapa 2 registrar sua empresa") -> Publisher Namespace -> Namespace]

Depois que a oferta/SKU é listada no [Azure Marketplace](http://azure.microsoft.com/marketplace), você não pode alterar os campos fornecidos abaixo:

- **Oferecer identificador:** [Portal de publicação -> máquinas virtuais -> selecione sua oferta -> guia imagens de máquina virtual -> oferecem identificador]
- **Identificador SKU:** [Portal de publicação -> máquinas virtuais -> selecione sua oferta -> SKUs guia -> Adicionar uma SKU]
- **Namespace do publisher:** [Portal de publicação -> máquinas virtuais -> explicação passo a passo guia -> diga-nos sobre sua empresa (encontrados em etapa 2 registrar) Publisher Namespace -> Namespace]
- **Portas** [Portal de publicação -> máquinas virtuais -> selecione sua oferta -> guia imagens de máquina virtual -> Abrir portas]
- **Alteração de preços de listados SKU (s)**
- **Alteração de modelo de listados SKU (s) de cobrança**
- **Remoção de cobrança regiões de listados SKU (s)**
- **Alterando a contagem de disco de dados de listados SKU (s)**



## <a name="1-how-to-update-the-technical-details-of-a-sku"></a>1. como atualizar os detalhes técnicos de uma SKU

Você pode adicionar uma nova versão para a SKU listada e republicar sua oferta seguindo as etapas fornecidas abaixo:

1. Login para o [portal de publicação](https://publish.windowsazure.com).
2. Navegue até a guia **máquinas virtuais** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **Imagens de máquina virtual** .
4. Na seção **SKUs** da guia **Imagens de máquina virtual** , localize o SKU que você deseja atualizar.
5. Depois disso, adicione um novo número de versão da SKU e clique no botão **"+"** . A nova versão deve ser do formato X.Y.Z onde X, Y, Z são inteiros. Alterações de versão só devem ser incrementais.
6. Na caixa **URL de VHD do sistema operacional** , adicione a assinatura de acesso compartilhado que URI criada para o sistema operacional VHD e salvar as alterações.

    >[AZURE.IMPORTANT] Você não pode incrementar/diminuir a contagem de disco de dados de uma SKU listada. Você precisa criar uma nova SKU nesse caso. Consulte a seção [3. Como adicionar uma nova SKU em uma oferta listada](#3-how-to-add-a-new-sku-under-a-live-offer) para obter orientações detalhadas.

7. Depois de fazer as alterações, navegue até a guia **Publicar** e clique no botão **PUSH para teste**. Para obter orientações detalhadas sobre teste sua oferta no ambiente de teste, consulte este [link](marketplace-publishing-vm-image-test-in-staging.md)
8. Depois de testar sua oferta no teste, navegue até a guia **Publicar** na publicação portal e clique no botão **SOLICITAR aprovação para notificações por PUSH para produção** para republicar sua oferta do Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku"></a>2. como atualizar os detalhes técnicos de uma oferta ou uma SKU

Você pode atualizar o abrangem (marketing, legal, suporte, categorias) detalhes da oferta ao vivo ou SKU do Azure Marketplace.

### <a name="21-update-the-offer-description-and-logos"></a>2.1 Atualize a descrição da oferta e logotipos

Você pode atualizar os detalhes da oferta e republicar sua oferta seguindo as etapas abaixo:

1. Login para o [portal de publicação](https://publish.windowsazure.com).
2. Navegue até a guia **máquinas virtuais** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **MARKETING** .
4. Clique no botão de **Inglês (EUA)** .
5. No menu do lado esquerdo, clique na guia **detalhes** . Na seção *Descrição* da guia **detalhes** pode atualize o título de oferta, oferecer resumo, oferecer resumo longo e salvar as alterações.

    >[AZURE.NOTE] Por favor cuidam dos seguintes enquanto você estiver atualizando os detalhes SKU.
    **Não digite texto duplicado na descrição da oferta e a descrição de SKU. Não digite texto duplicado sob o título SKU e a oferta longo resumo. Não digite texto duplicado sob o título SKU e o resumo de oferta.**

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)

6. Na seção *LOGOTIPOS* da guia **detalhes** , você poderá atualizar os logotipos. No entanto, certifique-se de que os logotipos sigam as [diretrizes do Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (consulte a seção Etapa 1: conteúdo de marketing Marketplace fornecer -> detalhes -> diretrizes de logotipo do Azure Marketplace).

    >[AZURE.NOTE] Ícone de banner é opcional. Você pode escolher não carregar um ícone de banner. No entanto, depois que o ícone de banner é carregado, não há nenhuma provisionar para excluí-la da publicação portal. Nesse caso, você deve seguir as [diretrizes de ícone banner](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (consulte a seção Etapa 1: conteúdo de marketing Marketplace fornecer -> detalhes -> diretrizes adicionais para o banner de logotipo do banner).

7. Depois de fazer as alterações, navegue até a guia **Publicar** e clique no botão **PUSH para teste**. Para obter orientações detalhadas sobre como testar sua oferta no ambiente de teste consulte este [link](marketplace-publishing-vm-image-test-in-staging.md).
8. Depois de testar sua oferta no teste, navegue até a guia **Publicar** na publicação portal e clique no botão **SOLICITAR aprovação para notificações por PUSH para produção** para republicar sua oferta do Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="22-update-the-sku-description"></a>2.2. Atualize a descrição do SKU

Você pode atualizar os detalhes SKU e republicar sua oferta seguindo as etapas abaixo:

1. Logon para o [portal de publicação](https://publish.windowsazure.com)
2. Navegue até a guia **máquinas virtuais** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **MARKETING** .
4. Clique no botão de **Inglês (EUA)** .
5. No menu do lado esquerdo, clique na guia **PLANOS** . Na seção *SKUs* da guia **PLANOS** , você pode atualizar o título SKU, SKU resumo e detalhes de descrição do SKU e salvar as alterações.

    >[AZURE.NOTE] Por favor cuidam dos seguintes enquanto você estiver atualizando os detalhes SKU. **Não digite texto duplicado na descrição da oferta e a descrição de SKU. Não digite texto duplicado em título do SKU e a oferta longo resumo. Não digite texto duplicado sob o título de SKU e o resumo de oferta.**

6. Depois de fazer as alterações, navegue até a guia **Publicar** e clique no botão **PUSH para teste**. Para obter orientações detalhadas sobre teste sua oferta no ambiente de teste, consulte este link
7. Depois de testar sua oferta no teste, navegue até a guia **Publicar** na publicação portal e clique no botão **SOLICITAR aprovação para notificações por PUSH para produção** para republicar sua oferta do Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="23-change-the-existing-links-or-add-new-links"></a>2.3 alterar os vínculos existentes ou adicionar novos links

Você pode alterar os vínculos existentes ou adicionar novos links e então republicar sua oferta seguindo as etapas abaixo:

1. Logon para o [portal de publicação](https://publish.windowsazure.com)
2. Navegue até a guia **máquinas virtuais** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **MARKETING** .
4. Clique no botão de **Inglês (EUA)** .
5. No menu do lado esquerdo, clique na guia **LINKS** .
6. Se você quiser adicionar um novo link, sob os *Links* seção clique no botão **Adicionar LINK** . A caixa de diálogo *"Adicionar Link"* será aberta. Na caixa de diálogo, você pode adicionar o título do link e URL campos e salvar as alterações. Você pode inserir qualquer link que contém informações que podem ajudar os clientes.
7. Se você quiser atualizar ou excluir um link existente, em seguida, selecione o link apropriado e clique no botão Editar ou no botão Excluir adequadamente.

    >[AZURE.NOTE] Certifique-se de que os links que você inseriu nesta seção estão funcionando corretamente, conforme esses links são validadas durante o processo de solicitação de produção.

8. Depois de fazer as alterações, navegue até a guia **Publicar** e clique no botão **PUSH para teste**. Para obter orientações detalhadas sobre como testar sua oferta no ambiente de teste consulte este [link](marketplace-publishing-vm-image-test-in-staging.md).
9. Depois de testar sua oferta no teste, navegue até a guia **Publicar** na publicação portal e clique no botão **SOLICITAR aprovação para notificações por PUSH para produção** para republicar sua oferta do Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="24-change-an-existing-sample-image-or-add-a-new-sample-image"></a>2,4 alterar uma imagem de exemplo existente ou adicionar uma nova imagem de exemplo

Você pode alterar um exemplo de imagens existentes ou adicionar novas imagens de exemplo e então republicar sua oferta seguindo as etapas abaixo:

>[AZURE.NOTE] Imagem de apenas uma amostra é exibida na [https://portal.azure.com](https://portal.azure.com).

1. Logon para o [portal de publicação](https://publish.windowsazure.com)
2. Navegue até a guia **máquinas virtuais** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **MARKETING** .
4. Clique no botão de **Inglês (EUA)** .
5. No menu do lado esquerdo, clique na guia **Imagens de exemplo** .
6. Se você quiser adicionar uma nova imagem de exemplo, em seguida, na seção *Exemplo de imagens* , clique no botão **Carregar uma nova imagem** e salve as alterações.

    >[AZURE.NOTE] Incluir uma imagem de exemplo é uma etapa opcional.

7. Se você quiser atualizar ou excluir uma imagem de exemplo existente, em seguida, localize a imagem de exemplo apropriado e clique no botão **Substituir imagem** ou no botão Excluir adequadamente.

8. Depois de fazer as alterações, navegue até a guia **Publicar** e clique no botão **PUSH para teste**. Para obter orientações detalhadas sobre como testar sua oferta no ambiente de teste consulte este [link](marketplace-publishing-vm-image-test-in-staging.md).
9. Depois de testar sua oferta no teste, navegue até a guia **Publicar** na publicação portal e clique no botão **SOLICITAR aprovação para notificações por PUSH para produção** para republicar sua oferta do Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="25-update-the-legal-content"></a>2.5 atualizar o conteúdo legal

Você pode atualizar o conteúdo legal e republicar sua oferta seguindo as etapas abaixo:

1. Logon para o [portal de publicação](https://publish.windowsazure.com)
2. Navegue até a guia **máquinas virtuais** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **MARKETING** .
4. Clique no botão de **Inglês (EUA)** .
5. No menu do lado esquerdo, clique na guia **LEGAL** . Na seção *Legal* , você pode atualizar suas políticas/termos de uso. Insira ou cole os políticas/termos na caixa de texto *Termos de uso* e salvar as alterações.
6. O limite de caracteres para os termos de uso legais é 1.000.000 caracteres.
7. Depois de fazer as alterações, navegue até a guia **Publicar** e clique no botão **PUSH para teste**. Para obter orientações detalhadas sobre teste sua oferta no ambiente de teste, consulte este [link](marketplace-publishing-vm-image-test-in-staging.md)
8. Depois de testar sua oferta no teste, navegue até a guia **Publicar** na publicação portal e clique no botão **SOLICITAR aprovação para notificações por PUSH para produção** para republicar sua oferta do Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="26-update-the-support-information"></a>2.6 atualizar as informações de suporte

Você pode atualizar as informações de suporte e republicar sua oferta seguindo as etapas abaixo:

1. Logon para o [portal de publicação](https://publish.windowsazure.com)
2. Navegue até a guia **máquinas virtuais** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **suporte** .
4. Na seção de *Engenharia entre em contato com* a guia **suporte** , você pode atualizar os detalhes do contato. Esses detalhes são usados para comunicação interna entre o parceiro e Microsoft apenas.
5. Sob a seção de *Suporte ao cliente* da guia de **suporte** , você pode atualizar os detalhes de contato de suporte como **nome, Email, telefone** e **URL de suporte**. Esses detalhes são usados para comunicação interna entre o parceiro e Microsoft apenas.

    >[AZURE.NOTE] Se você quiser fornecer somente de email de suporte, forneça um número de telefone fictício sob a seção de **Suporte ao cliente** . Nesse caso, seu email fornecido será usado.

6. Depois de fazer as alterações, navegue até a guia **Publicar** e clique no botão **PUSH para teste**. Para obter orientações detalhadas sobre teste sua oferta no ambiente de teste, consulte este [link](marketplace-publishing-vm-image-test-in-staging.md)
7. Depois de testar sua oferta no teste, navegue até a guia **Publicar** na publicação portal e clique no botão **SOLICITAR aprovação para notificações por PUSH para produção** para republicar sua oferta do Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="27-update-the-categories"></a>2.7 atualizar as categorias

Você pode atualizar a seção de categorias para sua oferta e republicar sua oferta seguindo as etapas abaixo:

1. Logon para o [portal de publicação](https://publish.windowsazure.com)
2. Navegue até a guia **máquinas virtuais** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **categorias** .
4. Na seção *categorias* , você pode atualizar as categorias para sua oferta e salvar as alterações. Você pode selecionar até cinco categorias para a Galeria do Azure Marketplace.
5. Depois de fazer as alterações, navegue até a guia **Publicar** e clique no botão **PUSH para teste**. Para obter orientações detalhadas sobre teste sua oferta no ambiente de teste, consulte este [link](marketplace-publishing-vm-image-test-in-staging.md)
6. Depois de testar sua oferta no teste, navegue até a guia **Publicar** na publicação portal e clique no botão **SOLICITAR aprovação para notificações por PUSH para produção** para republicar sua oferta do Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="3-how-to-add-a-new-sku-under-a-listed-offer"></a>3. como adicionar uma nova SKU em uma oferta listada

Você pode adicionar uma nova SKU em sua oferta ao vivo seguindo as etapas fornecidas abaixo:

1. Login para o [portal de publicação](https://publish.windowsazure.com).
2. Navegue até a guia **máquinas virtuais** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **SKUs** . Após essa clique no botão **Adicionar um SKU**.  Será aberta uma caixa de diálogo Novo. Insira um identificador SKU em minúsculas. Marque a caixa de seleção para trazer o-própria cobrança model(BYOL) se você deseja publicar o novo SKU com o modelo de cobrança BYOL. Caso contrário, desmarque a caixa de seleção para BYOL. Após essa clique na marca de escala na caixa de diálogo para criar uma nova SKU. Se você não optar para o modelo de cobrança BYOL para a nova SKU, em seguida, o modelo de cobrança será automaticamente definido para cada hora para a nova SKU. Se você quiser ativar a avaliação gratuita de 30 dias para o modelo de cobrança por hora, em seguida, clique na opção "Um mês" para "é uma avaliação gratuita disponível?". Caso contrário, selecione "Sem avaliação". [Observação: A opção "é uma avaliação gratuita disponível?" só é mostrada se você não selecionou BYOL na caixa de diálogo ao criar o novo SKU.]

    >[AZURE.IMPORTANT] A opção "Ocultar este SKU do Marketplace porque ele sempre deve ser comprado por meio de um modelo de solução" deve ser marcado como "Sim" apenas se você for aprovado para publicação de uma oferta de modelo de solução do Azure Marketplace. Caso contrário, essa opção sempre deve ser marcada como "Não".

4. Agora no menu do lado esquerdo, clique na guia **Imagens de máquina virtual** e descubra o SKU novato que você criou.
5. Para configurar a nova SKU, consulte a etapa 5 deste [link](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) para obter orientação.
6. Para adicionar o material de marketing para a nova SKU, consulte a seção Etapa 1: conteúdo de marketing Marketplace fornecer -> detalhes -> números de ponto de 2 a 5 deste [link](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Para adicionar as informações sobre preços para a nova SKU, consulte a seção 2.1. Definir preços máquina virtual deste [link](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)
8. Depois de fazer as alterações, navegue até a guia **Publicar** e clique no botão **PUSH para teste**. Para obter orientações detalhadas sobre teste sua oferta no ambiente de teste, consulte este [link](marketplace-publishing-vm-image-test-in-staging.md)
9. Depois de testar sua oferta no teste, navegue até a guia **Publicar** na publicação portal e clique no botão **SOLICITAR aprovação para notificações por PUSH para produção** para republicar sua oferta do Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="4-how-to-change-the-data-disk-count-for-a-listed-sku"></a>4. como alterar a contagem de disco de dados para uma SKU listada

Você não pode incrementar/diminuir a contagem de disco de dados de uma SKU listada. Você precisa criar uma nova SKU nesse caso. Consulte a seção [3. Como adicionar uma nova SKU em uma oferta live](#3-how-to-add-a-new-sku-under-a-live-offer) para obter orientações detalhadas.

## <a name="5---how-to-delete-a-listed-offer-from-the-azure-marketplace"></a>5. como excluir uma oferta listada do Azure Marketplace

Há vários aspectos que precisam ser cuidados no caso de uma solicitação para remover uma oferta ao vivo. Siga as etapas abaixo para obter orientações da equipe de suporte para remover uma oferta listada do Azure Marketplace:

1.  Elevar um tíquete usando esse [link](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681)
2.  Selecione o tipo de problema como **"Gerenciando ofertas"** e categoria como **"Modificando uma oferta e/ou SKU já presentes em produção"**
3.  Enviar a solicitação

A equipe de suporte o guiará durante o processo de exclusão de oferta/SKU.

>[AZURE.NOTE] Você sempre pode excluir a oferta enquanto ele está em um status de rascunho (ou seja, não no temporário ou produção) clicando no botão **Descartar rascunho** sob a guia **histórico** .

## <a name="6-how-to-delete-a-listed-sku-from-the-azure-marketplace"></a>6. como excluir uma SKU listada do Azure Marketplace

Você pode excluir uma SKU listada do Azure Marketplace seguindo as etapas fornecidas abaixo:

1. Login para o [portal de publicação](https://publish.windowsazure.com).
2. Navegue até a guia **máquinas virtuais** e selecione sua oferta.
3. No painel lateral à esquerda, clique na guia **SKUS** .
4. Selecione o SKU que você deseja excluir e clique no botão Excluir contra SKU.
5. Depois, navegue até a guia Publicar na publicação portal e clique no botão **SOLICITAR aprovação para notificações por PUSH para produção** para republicar a oferta do Azure Marketplace.
6. Depois que a oferta novamente obtém publicada do Azure Marketplace, o SKU será excluído do Azure Marketplace e o Portal do Azure.

## <a name="7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace"></a>7. como excluir a versão atual de uma SKU listada do Azure Marketplace

Você pode excluir a versão atual de uma SKU listada do Azure Marketplace seguindo as etapas fornecidas abaixo. Quando o processo for concluído, o SKU será revertido para a versão anterior.

1. Login para o [portal de publicação](https://publish.windowsazure.com).
2.  Navegue até a guia **máquinas virtuais** e selecione sua oferta.
3.  No painel lateral à esquerda, clique na guia **Imagens de máquina virtual** .
4.  Selecione o SKU cuja versão atual que você deseja excluir e clique no botão Excluir contra essa versão.
5.  Depois, navegue até a guia **Publicar** na publicação portal e clique no botão **SOLICITAR aprovação para notificações por PUSH para produção** para republicar a oferta do Azure Marketplace.
6.  Depois que a oferta novamente obtém publicada do Azure Marketplace, a versão atual da SKU listado será excluída do Azure Marketplace e o Portal do Azure. O SKU será revertido para a versão anterior.

## <a name="8-how-to-revert-listing-price-to-production-values"></a>8. como reverter preço de listagem aos valores de produção
Posso alterou o preço de uma SKU listada (ou eu tiver removido as regiões cobranças de uma SKU listada). Desde que ela não é suportada do Azure Marketplace, eu quero reverter minhas alterações para os valores de produção. Como obter que?

Siga as etapas fornecidas abaixo:

1. Login para o [portal de publicação](https://publish.windowsazure.com).
2. Navegue até a guia **máquinas virtuais** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia de **preços** .
4. Na guia de preços, selecione uma região cujos preços que você deseja redefinir.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)

5. No caso de SKUs com o modelo de cobrança por hora, redefina os preços para todas as cores como eles estão na produção para a região selecionada. Para SKUs com o modelo de cobrança BYOL, disponibilizar o SKU na região marcando a caixa de seleção contra o SKU sob a seção EXTERNALLY-LICENSED (BYOL) SKU disponibilidade (consulte a captura de tela abaixo).

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)

6. Agora, clique no botão **AUTOPRICE outros MERCADOS com base em Diante preços em Estados Unidos**.

    >[AZURE.NOTE] O rótulo do botão pode ser diferente dependendo da região que você selecionou. Desde que selecionamos Estados Unidos ao criar este documento, portanto o botão é rotulado como "Automático preço outros mercados com base em preços nos Estados Unidos" na captura de tela abaixo.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)

7. O Assistente de preço automático será aberta. Primeira página exibe a seleção para mercado base. Verifique a seção e ir para a próxima página clicando no botão **"->"** .

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)

8. Opção para selecionar as cores e planos será exibida na página 2. Selecione os planos desejados e as cores e clique em "->" botão.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)

9. Página 3 exibe os mercados/regiões. Clique no botão de alternância tudo para selecionar todas as regiões ou manualmente, marque as caixas para região. Clique no botão "->" para mover para a próxima página.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)

10. Página 4 exibe as taxas do exchange. Clique no botão Concluir para concluir as etapas. O assistente irá redefinir os preços de acordo com a sua seleção.

11. Agora, navegue até a guia de preços e clique no botão "Resumo e alterações de modo de exibição".
Selecione "Rascunho" na seção "Exibir versão" e "Produção" na seção "Comparar com" (consulte a captura de tela abaixo). Se você não vir nenhuma diferença de preços, isso significa preços tenham sido revertidas para os valores de produção com êxito.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)

12. Depois de fazer as alterações, navegue até a guia Publicar e clique no botão **PUSH para teste**. Para obter orientações detalhadas sobre teste sua oferta no ambiente de teste, consulte este [link](marketplace-publishing-vm-image-test-in-staging.md)
13. Depois de testar sua oferta no teste, navegue até a guia Publicar na publicação portal e clique no botão **SOLICITAR aprovação para notificações por PUSH para produção** para republicar sua oferta do Azure Marketplace.

## <a name="9-how-to-revert-billing-model-to-production-values"></a>9. como reverter o modelo de cobrança para valores de produção
Mudei o modelo de cobrança de uma SKU listado. Desde que ela não é suportada do Azure Marketplace, eu quero reverter minhas alterações para os valores de produção. Como obter que?

Siga as etapas abaixo:

1. Login para o [portal de publicação](https://publish.windowsazure.com).
2. Navegue até a guia **máquinas virtuais** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **SKUS** .
4. Clique em botão de editar para reverter o modelo de cobrança. Uma janela será aberta. Marque ou desmarque a caixa de seleção **'Licenciamento e cobrança é feita externamente do Azure (também conhecidos como trazer seu próprio licença)'** adequadamente.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)

5. Uma vez, consulte a resposta da pergunta 8 neste documento para reverter o preço.
6. Depois que navegue até a guia **Publicar** na publicação portal e push a oferta de teste para testá-lo. Uma vez que você terminar com testes a oferta, em seguida, clique no botão **SOLICITAR aprovação para notificações por PUSH para produção** para republicar sua oferta do Azure Marketplace.

## <a name="10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value"></a>10. como reverter a configuração de visibilidade de uma SKU listada para o valor de produção

Siga as etapas abaixo:

1. Login para o [portal de publicação](https://publish.windowsazure.com).
2. Navegue até a guia **máquinas virtuais** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **SKUS** .
4. Selecione seu SKU e reverter a configuração de visibilidade da SKU para o valor de produção.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)

5. Uma vez que você terminar com as alterações, em seguida, clique no botão **SOLICITAR aprovação para notificações por PUSH para produção** para republicar sua oferta do Azure Marketplace.

## <a name="see-also"></a>Consulte também
- [Introdução: Como publicar uma oferta do Azure Marketplace](marketplace-publishing-getting-started.md)
- [Noções básicas sobre a obtenção de informações do vendedor de relatório](marketplace-publishing-report-seller-insights.md)
- [Noções básicas sobre relatórios de pagamento](marketplace-publishing-report-payout.md)
- [Como alterar sua incentivos de revendedor do provedor de solução de nuvem](marketplace-publishing-csp-incentive.md)
- [Solução de problemas comuns de publicação no mercado](marketplace-publishing-support-common-issues.md)
- [Obtenha suporte como um fornecedor](marketplace-publishing-get-publisher-support.md)
- [Criando uma imagem de máquina virtual local](marketplace-publishing-vm-image-creation-on-premise.md)
- [Criar uma máquina virtual executando o Windows no portal do Azure preview](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
