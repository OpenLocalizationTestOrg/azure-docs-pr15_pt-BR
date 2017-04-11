<properties
    pageTitle="O que é Azure pesquisa | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Pesquisa Azure é um serviço de pesquisa de nuvem hospedado totalmente gerenciado. Saiba mais nesta visão geral de recurso."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="what-is-azure-search"></a>O que é Azure pesquisa?

Pesquisa Azure é uma solução de pesquisa-como um serviço de nuvem que delegue gerenciamento de servidor e infraestrutura para a Microsoft, deixando com um serviço de prontos para uso que você pode preencher com seus dados e depois use para adicionar pesquisa à sua web ou aplicativo móvel. Pesquisa Azure permite adicionar facilmente uma experiência de pesquisa robusto para seus aplicativos usando uma simples [API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) ou [.NET SDK](search-howto-dotnet-sdk.md) sem gerenciamento de infraestrutura de pesquisa ou se tornar um especialista em pesquisa.

## <a name="give-your-users-a-powerful-search-experience"></a>Dar aos usuários uma experiência de pesquisa avançada

**Consultas avançadas** podem ser formuladas usando a [sintaxe de consulta simples](https://msdn.microsoft.com/library/azure/dn798920.aspx), que oferece operadores lógicos, operadores de pesquisa de frase, operadores de sufixo, operadores de precedência. Além disso, a [sintaxe de consulta Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) pode habilitar a pesquisa confusa, pesquisa de proximidade, aumentando de termos e expressões regulares. Pesquisa Azure também suporta analisadores léxicos personalizados para permitir que o seu aplicativo para lidar com consultas de pesquisa complexas usando correspondência fonético e expressões regulares.

**Suporte a idioma** é [incluída para 56 idiomas diferentes](https://msdn.microsoft.com/library/azure/dn879793.aspx). Usando Lucene analisadores e analyzers Microsoft (refinados por anos de linguagem natural de processamento do Office e o Bing), a pesquisa do Azure pode analisar texto na caixa de pesquisa do seu aplicativo para tratar inteligente linguística específicas de idioma incluindo tempos verbais, gênero, substantivos plurais irregulares (por exemplo, ' mouse' versus 'mouse'), word eliminação composição, a quebra automática de linha (para idiomas sem espaços) e muito mais.

**Sugestões de pesquisa** pode ser habilitado para barras de pesquisa preenchida automaticamente e consultas com previsão de escrita. [Documentos de real no seu índice são sugeridos](https://msdn.microsoft.com/library/azure/dn798936.aspx) como usuários insira pesquisa parcial de entrada.

**Realce de ocorrências** [permite que](https://msdn.microsoft.com/library/azure/dn798927.aspx) os usuários vejam o trecho do texto em cada resultado que contém as correspondências para sua consulta. Você pode selecionar e escolher quais campos retornam trechos realçados.

**Navegação com faceta** facilmente é adicionado à sua página de resultados de pesquisa com a pesquisa do Azure. Usando [apenas um único parâmetro de consulta](https://msdn.microsoft.com/library/azure/dn798927.aspx), a pesquisa do Azure retornará todas as informações necessárias para construir uma experiência de pesquisa Facetado na interface de usuário do seu aplicativo para permitir que os usuários a busca detalhada e filtrar resultados de pesquisa (por exemplo, filtrar itens de catálogo pelo intervalo de preços ou marca).

Suporte **geoespaciais** permite processar, filtrar e exibir locais geográficos inteligente. Pesquisa Azure permite que os usuários explorar os dados com base na proximidade de um resultado de pesquisa para um local específico ou em uma região geográfica específica. Este vídeo explica como funciona: [canal 9: dados de pesquisa do Azure e geoespaciais](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data).

**Filtros** podem ser usados para incorporar navegação com faceta a interface do usuário do seu aplicativo com facilidade, aprimorar formulação de consulta e filtro com base em ou desenvolvedor-especificado pelo usuário critérios. Crie poderosos filtros usando a [sintaxe de OData](https://msdn.microsoft.com/library/azure/dn798921.aspx).

## <a name="empower-your-developers-with-an-easy-to-use-service"></a>Habilitar os desenvolvedores com um serviço fácil de usar

**Alta disponibilidade** garante uma experiência de serviço de pesquisa muito confiável. Quando dimensionada corretamente, [pesquisa Azure oferece um SLA 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Não **totalmente gerenciado** como uma solução de ponta a ponta, Azure pesquisa requer absolutamente nenhum gerenciamento de infraestrutura. Seu serviço pode ser facilmente adequado às suas necessidades dimensionando em duas dimensões para lidar com mais armazenamento de documento, maiores cargas de consulta ou ambos.

**Integração de dados** usando [indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx) permite a pesquisa do Azure para rastrear automaticamente Azure SQL Database, Azure DocumentDB ou [armazenamento de Blob do Azure](search-howto-indexing-azure-blob-storage.md) para sincronizar o conteúdo do índice de pesquisa com seu armazenamento de dados principal.

**Violação de documentos** está disponível (atualmente em preview) [para ler e formatos de arquivo principais de índice](search-howto-indexing-azure-blob-storage.md) inclusive Microsoft Office, bem como documentos PDF e HTML.

**Análise de tráfego de pesquisa** são [coletadas e analisadas facilmente](search-traffic-analytics.md) desbloquear percepções do qual os usuários estão digitando na caixa de pesquisa.

**Pontuação simples** é dos principais benefícios em pesquisa do Azure. [Perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx) são usadas para permitir que as organizações relevância de modelo como uma função de valores nos próprios documentos. Por exemplo, você pode querer produtos mais recentes ou descontado produtos para que apareçam maiores nos resultados da pesquisa. Você também pode criar perfis de pontuação usando marcas de pontuação personalizada com base em Preferências de pesquisa do cliente você controladas e armazenadas separadamente.

**Classificação** é oferecidos para vários campos através do esquema de índice e, em seguida, alternado em tempo de consulta com um parâmetro de pesquisa único.

**Paginação** e a otimização de seus resultados de pesquisa é [simples com o ajuste preciso controle](search-pagination-page-layout.md) que pesquisa Azure ofereça seus resultados de pesquisa.  

**Explorador de pesquisa** permite que você emitir consultas em relação a todos os seus índices certo de portal Azure da sua conta para que possa facilmente testar consultas e refinar os perfis de pontuação.

## <a name="how-it-works"></a>Como ele funciona

### <a name="1-provision-service"></a>1. serviço de provisionar
Você pode criar um serviço de pesquisa do Azure usando o [Portal do Azure](https://portal.azure.com/) ou a [API de gerenciamento de recursos do Azure](https://msdn.microsoft.com/library/azure/dn832684.aspx).

Dependendo de como você configura o serviço de pesquisa, você vai usar a camada gratuita de serviço que é compartilhada com outros assinantes de pesquisa do Azure, ou um [pago camada](https://azure.microsoft.com/pricing/details/search/) que dedica recursos para ser usado somente por seu serviço. Ao provisionar o seu serviço, você também escolher a região do data center que hospeda seu serviço.

Dependendo de qual nível de serviço escolhido, você pode dimensionar seu serviço em duas dimensões: 1) adicionar réplicas para aumentar sua capacidade para lidar com cargas de consulta pesado e 2) adicionar partições para adicionar armazenamento para mais documentos. Manipulando produtividade de armazenamento e consulta do documento separadamente, você pode personalizar o seu serviço de pesquisa para suas necessidades específicas.

### <a name="2-create-index"></a>2. criar o índice
Antes de você pode carregar seu conteúdo no seu serviço de pesquisa do Azure, primeiro você deve definir um índice de pesquisa do Azure. Um índice é como uma tabela de banco de dados que contém os dados e pode aceitar consultas de pesquisa. Definir o esquema de índice para mapear para a estrutura dos documentos que você deseja pesquisar, semelhante a campos em um banco de dados.

O esquema desses índices pode ser criado no Portal do Azure ou por programação [usando o SDK do .NET](search-howto-dotnet-sdk.md) ou [API REST](https://msdn.microsoft.com/library/azure/dn798941.aspx). Depois que o índice está definido, em seguida, você pode carregar seus dados ao serviço de pesquisa do Azure onde ele é subsequentemente indexado.

### <a name="3-index-data"></a>3. dados de índice
Depois que você definiu os campos e os atributos do índice, você está pronto para carregar seu conteúdo no índice. Você pode usar um modelo de envio ou recepção carregar dados para o índice.

O modelo de pull é fornecido por meio de indexadores que podem ser configurados para sob demanda ou agendadas atualizações (consulte [operações de indexador (API de restante do serviço de pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn946891.aspx), permitindo que você) a inclusão facilmente a dados e alterações de dados de um DocumentDB do Azure, banco de dados do SQL Azure, armazenamento de Blob do Azure ou hospedado em um máquina virtual do Azure do SQL Server.

O modelo de envio é fornecido por meio do SDK ou APIs REST usado para enviar documentos atualizados para um índice. Você pode enviar dados de praticamente qualquer conjunto de dados usando o formato JSON. Consulte [Adicionar, atualizar, ou excluir documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx) ou [como usar o SDK do .NET)](search-howto-dotnet-sdk.md) para obter orientações sobre o carregamento de dados.

### <a name="4-search"></a>4. pesquisa
Depois que você tiver preenchido seu índice de pesquisa do Azure, você pode agora [consultas de pesquisa de problema](https://msdn.microsoft.com/library/azure/dn798927.aspx) ao seu ponto de extremidade do serviço usando solicitações HTTP simples com API REST ou o SDK do .NET.

## <a name="try-it-now-for-free"></a>Experimente agora (gratuitamente!)
Você pode tentar pesquisar Azure hoje! Se você já tiver uma conta do Azure, é possível [provisionar um serviço na camada gratuito](search-create-service-portal.md).

Se você não tiver uma conta do Microsoft Azure que você pode experimentar uma sessão gratuita 60 minutos sem nenhum Inscreva-se necessário. Vá para o [Serviço de aplicativo do Azure tente](http://go.microsoft.com/fwlink/p/?LinkId=618214) e selecione "Web App." Selecione o modelo de "ASP.NET + Azure Search" para começar.
