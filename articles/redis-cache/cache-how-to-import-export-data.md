<properties 
    pageTitle="Importar e exportar dados no Azure relacionada Cache | Microsoft Azure" 
    description="Saiba como importar e exportar dados para e do armazenamento de blob com suas instâncias de Cache relacionada do Azure premium" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="import-and-export-data-in-azure-redis-cache"></a>Importar e exportar dados no Cache de relacionada do Azure

Importação/exportação é uma operação de gerenciamento de dados do Azure relacionada Cache, que permite importar dados para o Cache do Azure relacionada ou exportar dados do Azure relacionada Cache por importar e exportar um instantâneo de banco de dados de Cache relacionada (RDB) de um cache premium em um blob de página em uma conta de armazenamento do Azure. Importação/exportação permite migrar entre diferentes instâncias do Azure relacionada Cache ou preencher o cache com dados antes de usar.

Este artigo fornece um guia para importação e exportação de dados com Cache relacionada do Azure e fornece as respostas para perguntas frequentes.

>[AZURE.IMPORTANT] Importação/exportação está na visualização e está disponível somente para o [nível premium](cache-premium-tier-intro.md) caches.

## <a name="import"></a>Importar

Importar pode ser usado para trazer relacionada compatível RDB arquivos de qualquer servidor relacionada em execução em qualquer nuvem ou ambiente, incluindo relacionada em execução no Linux, Windows ou qualquer provedor de nuvem como Amazon Web Services e outras pessoas. Importar dados é uma maneira fácil de criar um cache com dados preenchidos. Durante o processo de importação, Azure relacionada Cache carrega os arquivos RDB de armazenamento do Azure na memória e então insere as chaves para o cache.

>[AZURE.NOTE] Antes de iniciar a operação de importação, certifique-se de que seu arquivo de banco de dados relacionada (RDB) ou arquivos carregados em blobs de página no armazenamento do Azure, na mesma região e assinatura como sua instância de Cache relacionada do Azure. Para obter mais informações, consulte [Introdução ao armazenamento de Blob do Azure](../storage/storage-dotnet-how-to-use-blobs.md). Se você exportou seu arquivo RDB usando o recurso de [Exportação do Azure relacionada Cache](#export) , seu arquivo RDB já está armazenado em um blob de página e está pronto para importar.

1. Para importar um ou mais blobs de cache exportado, [Navegue até o cache](cache-configure.md#configure-redis-cache-settings) no portal do Azure e clique em **Importar dados** da lâmina **configurações** da sua instância de cache.

    ![Importar dados][cache-import-data]

2. Clique em **Escolher Blob(s)** e selecione a conta de armazenamento que contém os dados para importar.

    ![Escolher conta de armazenamento][cache-import-choose-storage-account]

3. Clique no recipiente que contém os dados para importar.

    ![Escolha o contêiner][cache-import-choose-container]

4. Selecione um ou mais blobs importar clicando na área à esquerda do nome do blob e, em seguida, clique em **Selecionar**.

    ![Escolha blobs][cache-import-choose-blobs]

5. Clique em **Importar** para iniciar o processo de importação.

    >[AZURE.IMPORTANT] O cache não é acessível por clientes do cache durante o processo de importação, e quaisquer dados existentes no cache são excluídos.

    ![Importar][cache-import-blobs]

    Você pode monitorar o andamento da operação de importação seguindo as notificações do portal do Azure ou exibindo os eventos no [log de auditoria](cache-configure.md#support-amp-troubleshooting-settings).

    ![Progresso da importação][cache-import-data-import-complete] 


## <a name="export"></a>Exportar

Exportação permite que você exporte os dados armazenados no Azure relacionada Cache relacionada compatível RDB (s). Você pode usar esse recurso para mover dados de uma instância de Cache relacionada do Azure para outro ou para outro servidor relacionada. Durante o processo de exportação, um arquivo temporário é criado na máquina virtual que hospeda a instância do servidor de Cache relacionada do Azure e o arquivo é carregado para a conta de armazenamento designado. Quando a operação de exportação for concluído com qualquer um status de sucesso ou falha, o arquivo temporário é excluído.

1. Para exportar o conteúdo atual do cache de armazenamento, [Navegue até o cache](cache-configure.md#configure-redis-cache-settings) no portal do Azure e clique em **Exportar dados** da lâmina **configurações** da sua instância de cache.

    ![Escolha o contêiner de armazenamento][cache-export-data-choose-storage-container]

2. Clique em **Escolher contêiner de armazenamento** e selecione a conta de armazenamento desejada. A conta de armazenamento deve estar na mesma assinatura e região como seu cache.

    >[AZURE.IMPORTANT] Importação/exportação funciona com blobs de página, que são suportados pelo classic e contas de armazenamento do ARM, mas não são suportados pelo [contas de armazenamento de Blob](../storage/storage-blob-storage-tiers.md#blob-storage-accounts) neste momento.

    ![Conta de armazenamento][cache-export-data-choose-account]

3. Escolha o contêiner de blob desejado e clique em **Selecionar**. Para usar o novo um contêiner, clique em **Adicionar contêiner** para adicioná-lo primeiro e, em seguida, selecione-o na lista.

    ![Escolha o contêiner de armazenamento][cache-export-data-container]

4. Digite um **prefixo de nome de Blob** e clique em **Exportar** para iniciar o processo de exportação. O prefixo de nome de blob é usado para prefixar os nomes dos arquivos gerados por esta operação de exportação.

    ![Exportar][cache-export-data]

    Você pode monitorar o andamento da operação de exportação seguindo as notificações do portal do Azure ou exibindo os eventos no [log de auditoria](cache-configure.md#support-amp-troubleshooting-settings).

    ![][cache-export-data-export-complete]

    Caches permanecem disponíveis para uso durante o processo de exportação.


## <a name="importexport-faq"></a>Perguntas Frequentes de importação/exportação

Esta seção contém perguntas frequentes sobre o recurso de importação/exportação.

-   [Quais preços camadas podem usar importação/exportação?](#what-pricing-tiers-can-use-importexport)
-   [Pode importar dados de qualquer servidor relacionada?](#can-i-import-data-from-any-redis-server)
-   [Meu cache estará disponível durante uma operação de importação/exportação?](#will-my-cache-be-available-during-an-importexport-operation)
-   [Pode usar importação/exportação com cluster relacionada?](#can-i-use-importexport-with-redis-cluster)
-   [Como funciona a importação/exportação com uma configuração de bancos personalizado?](#how-does-importexport-work-with-a-custom-databases-setting)
-   [Importação/exportação é diferente de persistência relacionada?](#how-is-importexport-different-from-redis-persistence)
-   [É possível automatizar usando o PowerShell, CLI ou outros clientes do gerenciamento de importação/exportação?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
-   [Recebi um erro de tempo limite durante a minha operação de importação/exportação. O que significa?](#i-received-a-timeout-error-during-my-importexport-operation.-what-does-it-mean)
-   [Recebi um erro ao exportar meus dados para o armazenamento de Blob do Azure. O que aconteceu?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage.-what-happened)


### <a name="what-pricing-tiers-can-use-importexport"></a>Quais preços camadas podem usar importação/exportação?

Importação/exportação está disponível somente no premium preços camada.

### <a name="can-i-import-data-from-any-redis-server"></a>Pode importar dados de qualquer servidor relacionada?

Sim, além de importar os dados exportados de instâncias do Azure relacionada Cache, você pode importar arquivos RDB de qualquer servidor relacionada executando o Windows, em qualquer nuvem ou ambiente, como Linux, ou nuvem provedores como Amazon Web Services. Para fazer isso, carregue o arquivo RDB do servidor desejado relacionada em um blob de página em uma conta de armazenamento do Azure e importe-os para sua instância do Azure relacionada Cache premium. Por exemplo, talvez você queira exportar os dados do seu cache de produção e importe-os para um cache usado como parte de um ambiente de teste para teste ou migração. 

### <a name="will-my-cache-be-available-during-an-importexport-operation"></a>Meu cache estará disponível durante uma operação de importação/exportação?

-   **Exportar** - Caches permanecem disponíveis e você pode continuar a usar seu cache durante uma operação de exportação.
-   **Importar** - Caches ficam indisponíveis quando inicia uma operação de importação e se tornar disponível para uso quando a operação de importação for concluída.


### <a name="can-i-use-importexport-with-redis-cluster"></a>Pode usar importação/exportação com cluster relacionada?

Sim, e você pode importar/exportar entre um cache agrupado e um cache não-clusterizados. Desde relacionada cluster [apenas dá suporte para o banco de dados 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), todos os dados em bancos de dados diferente de 0 não serão importados. Quando os dados de cache agrupadas são importados, as teclas são redistribuídas entre os fragmentos do cluster. 

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Como funciona a importação/exportação com uma configuração de bancos personalizado?

Alguns níveis de preços tem [limites de bancos de dados](cache-configure.md#databases)diferentes, portanto, há algumas considerações ao importar se você tiver configurado um valor personalizado para o `databases` definindo durante a criação do cache.

-   Ao importar para um nível de preços com baixo `databases` limite que o nível do qual você exportou:
    -   Se você estiver usando o número padrão de `databases` que é 16 para todos os níveis de preços, nenhum dado é perdido.
    -   Se você estiver usando um número personalizado de `databases` que fica dentro dos limites para a camada à qual você está importando, nenhum dado é perdido.
    -   Se os dados exportados continham dados em um banco de dados que excede os limites da nova camada, os dados desses bancos de dados maiores não são importados.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Importação/exportação é diferente de persistência relacionada?

Azure persistência de Cache relacionada permite persistir dados armazenados em relacionada ao armazenamento do Azure. Quando persistência é configurada, o Azure relacionada Cache persistir um instantâneo do cache relacionada em um formato binário relacionada em disco com base em uma frequência de backup configurável. Se ocorrer um evento grave que desabilita cache primário e réplica, os dados do cache são restaurados automaticamente usando o instantâneo mais recente. Para obter mais informações, consulte [como configurar persistência de dados para um Premium Azure relacionada Cache](cache-how-to-premium-persistence.md).

Importação / exportação permite que você exiba os dados ou exportar do Azure relacionada Cache. Ele não configurar backup e restauração usando persistência relacionada.


### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>É possível automatizar usando o PowerShell, CLI ou outros clientes do gerenciamento de importação/exportação?

Sim, para PowerShell instruções ver [para importar um cache relacionada](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) e [Exportar um cache relacionada](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache).



### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Recebi um erro de tempo limite durante a minha operação de importação/exportação. O que significa?

Se você permanecer na lâmina **Importar dados** ou **Exportar dados** por mais de 15 minutos antes de iniciar a operação, você receberá um erro semelhante ao seguinte.

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Para resolver isso, iniciar a importação ou exportação operação antes de 15 minutos expirou.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Recebi um erro ao exportar meus dados para o armazenamento de Blob do Azure. O que aconteceu?

Importação/exportação só funciona com arquivos RDB armazenados como blobs de página. Outros tipos de blob não são suportados no momento, incluindo contas de armazenamento de blob com níveis quente e interessantes.


## <a name="next-steps"></a>Próximas etapas
Saiba como usar mais recursos premium do cache.

-   [Introdução para o nível Premium de Cache do Azure relacionada](cache-premium-tier-intro.md)    

  
<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png








