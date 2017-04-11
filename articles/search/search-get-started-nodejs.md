<properties
    pageTitle="Introdução ao Azure pesquisa em NodeJS | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Percorra a criação de um aplicativo de pesquisa em um serviço de pesquisa de nuvem hospedado no Azure usando NodeJS como sua linguagem de programação."
    services="search"
    documentationCenter=""
    authors="EvanBoyle"
    manager="pablocas"
    editor="v-lincan"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.date="07/14/2016"
    ms.author="evboyle"/>

# <a name="get-started-with-azure-search-in-nodejs"></a>Introdução ao Azure pesquisa em NodeJS
> [AZURE.SELECTOR]
- [Portal](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Aprenda a criar um aplicativo de pesquisa NodeJS personalizado que usa a pesquisa do Azure para sua experiência de pesquisa. Este tutorial usa a [API de restante do serviço de pesquisa do Azure](https://msdn.microsoft.com/library/dn798935.aspx) para construir os objetos e as operações usadas neste exercício.

Usamos [NodeJS](https://nodejs.org) e NPM, [Sublime 3 de texto](http://www.sublimetext.com/3)e Windows PowerShell no Windows 8.1 para desenvolver e testar este código.

Para executar este exemplo, você deve ter um serviço de pesquisa do Azure, que você pode se inscrever para cima no [Portal do Azure](https://portal.azure.com). Consulte [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para obter instruções passo a passo.

## <a name="about-the-data"></a>Sobre os dados

Este aplicativo exemplo usa dados dos [Estados Unidos geológica serviços (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), filtrado no estado da Rhode ilha para reduzir o tamanho do conjunto de dados. Usaremos esses dados para criar um aplicativo de pesquisa que retorna edifícios de marco como hospitais e escolas, bem como geológica recursos como fluxos Lagos e Cúpulas.

Neste aplicativo, o programa de **DataIndexer** cria e carrega o índice usando uma construção [indexador](https://msdn.microsoft.com/library/azure/dn798918.aspx) , recuperando dataset USGS filtrado de um banco de dados do SQL Azure público. Credenciais e conexão informações na fonte de dados online são fornecidas no código de programa. Nenhuma configuração adicional é necessária.

> [AZURE.NOTE] Podemos aplicado um filtro este conjunto de dados para ficar abaixo do limite de 10.000 documento da camada preços gratuito. Se você usar a camada padrão, esse limite não se aplica. Para obter detalhes sobre a capacidade para cada nível de preços, consulte [limites de serviço de pesquisa](search-limits-quotas-capacity.md).


<a id="sub-2"></a>
## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Localizar o nome do serviço e a chave de api do seu serviço de pesquisa do Azure

Depois de criar o serviço, volte para o portal para obter a URL ou `api-key`. Conexões com o seu serviço de pesquisa exigem que você tenha a URL e um `api-key` para autenticar a chamada.

1. Entre [Portal do Azure](https://portal.azure.com).
2. Na barra de salto, clique em **serviço de pesquisa** para listar todos os serviços de pesquisa do Azure provisionados para sua assinatura.
3. Selecione o serviço que você deseja usar.
4. No painel de serviço, você verá blocos para informações essenciais, bem como o ícone de chave para acessar as chaves de administrador.

    ![][3]

5. Copie a URL do serviço, uma chave de administrador e uma chave de consulta. Você precisará posteriormente, todos os três quando você adicioná-los para o arquivo de config.js.

## <a name="download-the-sample-files"></a>Baixar os arquivos de amostra

Use qualquer uma das seguintes abordagens para baixar a amostra.

1. Vá para [AzureSearchNodeJSIndexerDemo](https://github.com/AzureSearch/AzureSearchNodeJSIndexerDemo).
2. Clique em **Baixar ZIP**, salve o arquivo. zip e, em seguida, extrair todos os arquivos que ela contém.

Todas as modificações de arquivo subsequentes e declarações de execução serão feitas contra arquivos nesta pasta.


## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>Atualize o config.js. com o URL do serviço de pesquisa e a api-chave

Usar a URL e api-chave que você copiou anteriormente, especifique a URL, administrador-chave e chave de consulta no arquivo de configuração.

Teclas de administrador concedem controle total sobre operações de serviço, incluindo a criação ou exclusão de um índice e carregar documentos. Em contraste, chaves de consulta são para operações de somente leitura, normalmente usadas por aplicativos de clientes que se conectam a pesquisa do Azure.

Neste exemplo, podemos incluir a chave de consulta para ajudar a reforçar a prática recomendada de uso da chave de consulta em aplicativos cliente.

A captura de tela a seguir mostra **config.js** abertos em um texto editor, com as entradas relevantes delimitadas para que você possa ver onde deseja atualizar o arquivo com os valores que são válidos para o serviço de pesquisa.

![][5]


## <a name="host-a-runtime-environment-for-the-sample"></a>Hospedar um ambiente de tempo de execução para amostra

O exemplo requer um servidor HTTP, que você pode instalar globalmente usando npm.

Use uma janela do PowerShell para os seguintes comandos.

1. Navegue até a pasta que contém o arquivo **package.json** .
2. Tipo de `npm install`.
2. Tipo de `npm install -g http-server`.

## <a name="build-the-index-and-run-the-application"></a>Construa o índice e executar o aplicativo

1. Tipo de `npm run indexDocuments`.
2. Tipo de `npm run build`.
3. Tipo de `npm run start_server`.
4. Direcionar o navegador em`http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>Pesquisar dados USGS

O conjunto de dados USGS inclui registros relevantes para o estado da Rhode ilha. Se você clicar em uma caixa de pesquisa vazia de **pesquisa** , você obterá as entradas de 50 superiores, que é o padrão.

Inserindo um termo de pesquisa terá o mecanismo de pesquisa algo para ir no. Tente inserir um nome regional. "Roger Williams" foi primeiro governador da Ilha Rhode. Vários parques, edifícios e escolas são nomeadas após a ele.

![][9]

Você também pode tentar qualquer um dos seguintes termos:

- Pawtucket
- Pembroke
- Planejando + cabo


## <a name="next-steps"></a>Próximas etapas

Este é o primeiro tutorial de pesquisa do Azure com base em NodeJS e dataset USGS. Ao longo do tempo, nós será estender esse tutorial para demonstrar os recursos de pesquisa adicionais que talvez você queira usar em suas soluções personalizadas.

Se você já tem algumas informações básicas na pesquisa do Azure, você pode usar este exemplo como trampolim para tentar suggesters (consultas com previsão de escrita ou AutoCompletar), filtros e navegação com faceta. Você também pode melhorar após a página de resultados de pesquisa adicionando contagens e lotes documentos para que os usuários podem percorrer os resultados.

Começando a pesquisa Azure? É recomendável avaliar outros tutoriais para desenvolver uma compreensão das quais você pode criar. Visite nossa [página de documentação](https://azure.microsoft.com/documentation/services/search/) para localizar mais recursos. Você também pode exibir os links em nosso [Tutorial lista e vídeo](search-video-demo-tutorial-list.md) para acessar mais informações.

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png
