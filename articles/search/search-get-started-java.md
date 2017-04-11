<properties
    pageTitle="Introdução ao Azure pesquisa em Java | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Como criar um aplicativo de pesquisa de nuvem hospedado no Azure usando Java como sua linguagem de programação."
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

# <a name="get-started-with-azure-search-in-java"></a>Introdução ao Azure pesquisa em Java
> [AZURE.SELECTOR]
- [Portal](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Aprenda a criar um aplicativo de pesquisa Java personalizado que usa a pesquisa do Azure para sua experiência de pesquisa. Este tutorial usa a [API de restante do serviço de pesquisa do Azure](https://msdn.microsoft.com/library/dn798935.aspx) para construir os objetos e as operações usadas neste exercício.

Para executar este exemplo, você deve ter um serviço de pesquisa do Azure, que você pode se inscrever para cima no [Portal do Azure](https://portal.azure.com). Consulte [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para obter instruções passo a passo.

Para criar e testar este exemplo, usamos os seguintes softwares:

- [Eclipse IDE para desenvolvedores do Java EE](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Certifique-se de baixar a versão EE. Uma das etapas de verificação requer um recurso que é localizado somente nesta edição.

- [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

## <a name="about-the-data"></a>Sobre os dados

Este aplicativo exemplo usa dados dos [Estados Unidos geológica serviços (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), filtrado no estado da Rhode ilha para reduzir o tamanho do conjunto de dados. Usaremos esses dados para criar um aplicativo de pesquisa que retorna edifícios de marco como hospitais e escolas, bem como geológica recursos como fluxos Lagos e Cúpulas.

Neste aplicativo, o programa de **SearchServlet.java** cria e carrega o índice usando uma construção [indexador](https://msdn.microsoft.com/library/azure/dn798918.aspx) , recuperando dataset USGS filtrado de um banco de dados do SQL Azure público. Credenciais predefinidas e informações de conexão com a fonte de dados online são fornecidas no código de programa. Em termos de acesso a dados, nenhuma configuração adicional é necessária.

> [AZURE.NOTE] Podemos aplicado um filtro este conjunto de dados para ficar abaixo do limite de 10.000 documento da camada preços gratuito. Se você usar a camada padrão, esse limite não se aplica, e você pode modificar este código para usar um conjunto de dados maior. Para obter detalhes sobre a capacidade para cada nível de preços, consulte [limites e restrições](search-limits-quotas-capacity.md).

## <a name="about-the-program-files"></a>Sobre os arquivos de programa

A lista a seguir descreve os arquivos que são relevantes para este exemplo.

- Search.jsp: Fornece a interface de usuário
- SearchServlet.java: Fornece métodos (semelhantes a um controlador no MVC)
- SearchServiceClient.java: Solicitações de alças HTTP
- SearchServiceHelper.java: Uma classe auxiliar que fornece métodos estáticos
- Document.Java: Fornece o modelo de dados
- config: define a URL do serviço de pesquisa e a chave de api
- POM.XML: Uma dependência Maven

<a id="sub-2"></a>
## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Localizar o nome do serviço e a chave de api do seu serviço de pesquisa do Azure

Todas as chamadas de API REST em Azure pesquisa exigem que você forneça a URL do serviço e uma chave de api. 

1. Entre [Portal do Azure](https://portal.azure.com).
2. Na barra de salto, clique em **serviço de pesquisa** para listar todos os serviços de pesquisa do Azure provisionados para sua assinatura.
3. Selecione o serviço que você deseja usar.
4. No painel de serviço, você verá blocos para informações essenciais, bem como o ícone de chave para acessar as chaves de administrador.

    ![][3]

5. Copie a URL do serviço e uma tecla de administrador. Você precisará-las mais tarde, quando você adicioná-los para o arquivo **config** .

## <a name="download-the-sample-files"></a>Baixar os arquivos de amostra

1. Vá para [AzureSearchJavaDemo](https://github.com/AzureSearch/AzureSearchJavaIndexerDemo) Github.

2. Clique em **Baixar ZIP**, salvar o arquivo. zip no disco e, em seguida, extrair todos os arquivos que ela contém. Considere a possibilidade de extrair os arquivos na área de trabalho Java para tornar mais fácil localizar o projeto mais tarde.

3. Os arquivos de amostra são somente leitura. Propriedades da pasta de atalho e desmarque o atributo somente leitura.

Todas as modificações de arquivo subsequentes e declarações de execução serão feitas contra arquivos nesta pasta.  

## <a name="import-project"></a>Importar projeto

1. Em Eclipse, escolha **arquivo** > **importação** > **Geral** > **Projetos existentes no espaço de trabalho**.

    ![][4]

2. Em **Selecione o diretório raiz**, navegue até a pasta que contém os arquivos de exemplo. Selecione a pasta que contém a pasta de .project. O projeto deve aparecer na lista de **projetos** como um item selecionado.

    ![][12]

3. Clique em **Concluir**.

4. Use o **Explorador de projeto** para exibir e editar os arquivos. Se ainda não estiver aberta, clique em **janela** > **Mostrar modo de exibição** > **Explorador de projeto** ou use o atalho para abri-lo.

## <a name="configure-the-service-url-and-api-key"></a>Configurar a URL do serviço e a api-chave

1. No **Explorador de projeto**, clique duas vezes em **config** para editar as configurações de configuração que contém o nome do servidor e a api-chave.

2. Consulte as etapas neste artigo, você encontra a URL do serviço e a api a chave no [Portal do Azure](https://portal.azure.com), obtenha os valores que agora, você entrará em **config**.

3. No **config**, substitua "Chave Api" com a api-chave para o serviço. Em seguida, o nome do serviço (o primeiro componente do http://servicename.search.windows.net a URL) substitui "nome do serviço" no mesmo arquivo.

    ![][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>Configurar os ambientes de projeto, compilação e runtime

1. No Eclipse, no Explorador de projeto, clique com botão direito do projeto > **Propriedades** > **Aspectos do projeto**.

2. Selecione **módulo dinâmico da Web**, **Java**e **JavaScript**.

    ![][6]

3. Clique em **Aplicar**.

4. **Janela**selecionar > **Preferências** > **Server** > **ambientes de tempo de execução** > **Adicionar.**.

5. Expanda Apache e selecione a versão do servidor Apache Tomcat instalado anteriormente. Em nosso sistema, podemos instalado versão 8.

    ![][7]

6. Na página seguinte, especifique o diretório de instalação do Tomcat. Em um computador Windows, isso provavelmente será C:\Program Files\Apache Software Foundation\Tomcat *versão*.

6. Clique em **Concluir**.

7. **Janela**selecionar > **Preferências** > **Java** > **instalado JREs** > **Adicionar**.

8. Em **Adicionar JRE**, selecione **Máquina virtual padrão**.

10. Clique em **Avançar**.

11. Na definição de JRE, em JRE inicial, clique em **diretório**.

12. Navegue até **Os arquivos de programa** > **Java** e selecione o JDK instalada anteriormente. É importante selecionar o JDK como JRE.

13. No JREs instalado, escolha o **JDK**. Suas configurações devem ser semelhantes ao seguinte captura de tela.

    ![][9]

14. Opcionalmente, selecione **janela** > **Navegador da Web** > **Internet Explorer** para abrir o aplicativo em uma janela de navegador externo. Usar um navegador externo fornece uma melhor experiência de aplicativo Web.

    ![][8]

Agora você concluiu as tarefas de configuração. Em seguida, você irá criar e executar o projeto.

## <a name="build-the-project"></a>Criar o projeto

1. No Explorador de projeto, clique com botão direito no nome do projeto e escolha **Executar como** > **Maven construir …** para configurar o projeto.

    ![][10]

8. Configuração de editar, em metas, digite "instalação limpa" e clique em **Executar**.

Mensagens de status são saída para a janela de console. Você deve ver sucesso CONSTRUIR indicando o projeto construído sem erros.

## <a name="run-the-app"></a>Executar o aplicativo

Nesta última etapa, você executará o aplicativo em um ambiente de tempo de execução de servidor local.

Se você ainda não especificou um ambiente de tempo de execução do servidor no Eclipse, você precisará fazer isso primeiro.

1. No Explorador de projeto, expanda o **conteúdo da Web**.

5. Clique com botão direito **Search.jsp** > **Executar como** > **executado no servidor**. Selecione o servidor Apache Tomcat e clique em **Executar**.

> [AZURE.TIP] Se você usou um espaço de trabalho não padrão para armazenar seu projeto, você precisará modificar a **Configuração executar** para apontar para o local do projeto para evitar um erro de inicialização do servidor. No Explorador de projeto, clique com botão direito **Search.jsp** > **Executar como** > **Executar configurações**. Selecione o servidor Apache Tomcat. Clique em **argumentos**. Clique em **Sistema de arquivos** ou **espaço de trabalho** para definir a pasta que contém o projeto.

Quando você executa o aplicativo, você verá uma janela do navegador, fornecendo uma caixa de pesquisa para inserir termos.

Aguarde cerca de um minuto antes de clicar em **Pesquisar** para dar o tempo de serviço para criar e carregar o índice. Se você receber um erro de HTTP 404, basta esperar um pouco mais antes de tentar novamente.

## <a name="search-on-usgs-data"></a>Pesquisar dados USGS

O conjunto de dados USGS inclui registros relevantes para o estado da Rhode ilha. Se você clicar em uma caixa de pesquisa vazia de **pesquisa** , você obterá as entradas de 50 superiores, que é o padrão.

Inserindo um termo de pesquisa terá o mecanismo de pesquisa algo para ir no. Tente inserir um nome regional. "Roger Williams" foi primeiro governador da Ilha Rhode. Vários parques, edifícios e escolas são nomeadas após a ele.

![][11]

Você também pode tentar qualquer um dos seguintes termos:

- Pawtucket
- Pembroke
- Planejando + cabo

## <a name="next-steps"></a>Próximas etapas

Este é o primeiro tutorial de pesquisa do Azure com base em Java e dataset USGS. Ao longo do tempo, nós será estender esse tutorial para demonstrar os recursos de pesquisa adicionais que talvez você queira usar em suas soluções personalizadas.

Se você já tem algumas informações básicas na pesquisa do Azure, você pode usar este exemplo como trampolim para ainda mais experimento, talvez aumentando a [página de pesquisa](search-pagination-page-layout.md)ou implementando [navegação com faceta](search-faceted-navigation.md). Você também pode melhorar após a página de resultados de pesquisa adicionando contagens e lotes documentos para que os usuários podem percorrer os resultados.

Começando a pesquisa Azure? É recomendável avaliar outros tutoriais para desenvolver uma compreensão das quais você pode criar. Visite nossa [página de documentação](https://azure.microsoft.com/documentation/services/search/) para localizar mais recursos. Você também pode exibir os links em nosso [Tutorial lista e vídeo](search-video-demo-tutorial-list.md) para acessar mais informações.

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
