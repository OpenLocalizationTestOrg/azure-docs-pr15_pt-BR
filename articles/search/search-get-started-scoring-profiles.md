<properties 
    pageTitle="Como usar a pontuação perfis na pesquisa do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado" 
    description="Ajuste a classificação por meio de perfis de pontuação na pesquisa do Azure, um serviço de pesquisa de nuvem hospedado no Microsoft Azure da pesquisa." 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

# <a name="how-to-use-scoring-profiles-in-azure-search"></a>Como usar perfis de pontuação na pesquisa do Azure

Perfis de pontuação são um recurso do Microsoft Azure pesquisa que personalizar o cálculo de resultados de pesquisa, influenciar como os itens são classificados em uma lista de resultados de pesquisa. Você pode pensar pontuação perfis como uma forma de relevância de modelo, aumentando os itens que atendem a critérios predefinidos. Por exemplo, digamos que seu aplicativo é um site de reserva de hotel online. Aumentando o `location` campo, pesquisas de incluir um termo como Seattle resultará em pontuações mais altas para itens que possuem Seattle no `location` campo. Observe que você pode ter mais de um perfil de pontuação ou nenhum, se a pontuação de padrão é suficiente para seu aplicativo.

Para ajudá-lo a experimentar perfis de pontuação, você pode baixar um aplicativo de exemplo que usa perfis de pontuação para alterar o ordem de classificação dos resultados da pesquisa. O exemplo é um aplicativo de console – talvez não muito realista para desenvolvimento de aplicativos reais – mas útil assim como uma ferramenta de aprendizado. 

O aplicativo de exemplo demonstra pontuação comportamentos usando dados fictícios, chamados a `musicstoreindex`. A simplicidade do aplicativo exemplo torna mais fácil modificar perfis de pontuação e consultas e, em seguida, consulte os efeitos imediatos a ordem de classificação quando o programa é executado.

<a id="sub-1"></a>
## <a name="prerequisites"></a>Pré-requisitos

O aplicativo de amostra é escrito em c# usando o Visual Studio 2013. Se você ainda não tiver uma cópia do Visual Studio, tente o livre [Visual Studio 2013 Express edition](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) .

Você precisará de uma assinatura do Azure e um serviço de pesquisa do Azure para concluir este tutorial. Consulte [criar um serviço de pesquisa no portal](search-create-service-portal.md) para obter ajuda com a configuração de serviço.

[AZURE. INCLUIR [você precisa de uma conta do Azure para concluir este tutorial:](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## <a name="download-the-sample-application"></a>Baixe o aplicativo de amostra

Vá para [A demonstração de perfis de pontuação do Azure pesquisa](https://azuresearchscoringprofiles.codeplex.com/) no codeplex para baixar o aplicativo de exemplo descrito neste tutorial.

Na guia código-fonte, clique em **Baixar** para obter um arquivo zip da solução. 

 ![][12]

<a id="sub-3"></a>
## <a name="edit-appconfig"></a>Editar App

1. Após extrair os arquivos, abra a solução no Visual Studio para editar o arquivo de configuração.
1. No Solution Explorer, clique duas vezes em **App**. Este arquivo Especifica o ponto de extremidade do serviço e um `api-key` usado para autenticar a solicitação. Você pode obter esses valores a partir do Portal clássico.
1. Entre [Portal do Azure](https://portal.azure.com).
1. Ir para o painel de serviço de pesquisa do Azure.
1. Clique no bloco de **Propriedades** para copiar a URL do serviço
1. Clique no bloco de **teclas** para copiar o `api-key`.

Quando terminar de adicionar a URL e `api-key` para App, configurações de aplicativo devem ter esta aparência:

   ![][11]


<a id="sub-4"></a>
## <a name="explore-the-application"></a>Explore o aplicativo

Você está quase pronto para criar e executar o aplicativo, mas antes disso, examine os arquivos JSON usados para criar e preencher o índice.

**Schema.JSON** define o índice, incluindo os perfis de pontuação que são enfatizados nesta demonstração. Observe que o esquema define todos os campos usados no índice, incluindo campos não pesquisável, tais como `margin`, que você pode usar em um perfil de pontuação. Sintaxe de perfil de pontuação é documentada em [Adicionar um perfil de pontuação para um índice de pesquisa do Azure](http://msdn.microsoft.com/library/azure/dn798928.aspx).

**Data1-3.json** fornece os dados, 246 álbuns em poucos gêneros. Os dados são uma combinação de informações reais de álbum e artistas, com campos fictícios como `price` e `margin` usado para ilustrar operações de pesquisa. Os arquivos de dados estão em conformidade com o índice e são carregados para o serviço de pesquisa do Azure. Depois que os dados são carregados e indexados, você pode emitir consultas em relação a ele.

**Program.cs** executa as seguintes operações:

- Abre uma janela de console.

- Se conecta à pesquisa de Azure usando a URL do serviço e `api-key`.

- Exclui a `musicstoreindex` se existir.

- Cria um novo `musicstoreindex` usando o arquivo de schema.json.

- Preenche o índice usando os arquivos de dados.

- Consulta o índice usando quatro consultas. Observe que os perfis de pontuação são especificados como um parâmetro de consulta. Todas as consultas pesquisar o mesmo termo, 'melhor'. A primeira consulta demonstra pontuação padrão. As consultas de três restantes usam um perfil de pontuação.

<a id="sub-5"></a>
## <a name="build-and-run-the-application"></a>Criar e executar o aplicativo

Para descartar conectividade ou problemas de referência de assembly, criar e executar o aplicativo para garantir que não existem problemas para trabalhar primeiro. Você verá um aplicativo de console abrir em segundo plano. Todas as consultas de quatro executam em sequência sem pausa. Em muitos sistemas, todo o programa executa em 15 segundos. Se o aplicativo de console inclui uma mensagem informando "concluída. Pressione enter para continuar", o programa que foi concluída com êxito. 

Para comparar a consulta é executada, você pode marcar-copiar-colar os resultados da consulta do console e colá-los em um arquivo do Excel. 

A ilustração a seguir mostra os resultados do primeiro três consultas lado a lado. Todas as consultas usam termo de pesquisa, 'melhor', que aparece em vários títulos de álbum.

   ![][10]

A primeira consulta usa pontuação padrão. Como o termo de pesquisa aparece apenas na títulos de álbum, e nenhum outro critério especificado, tendo 'melhor' no título do álbum de itens são retornados na ordem em que o serviço de pesquisa encontrá-las. 

A segunda consulta usa um perfil de pontuação, mas observe que o perfil não tinha nenhum efeito. Os resultados são idênticos da primeira consulta. Isso ocorre porque o perfil pontuação aumenta um campo ('gênero') que não esteja germane à consulta. O termo de pesquisa 'melhor' não existe em qualquer campo 'gênero' de qualquer documento. Quando um perfil de pontuação não tem efeito, os resultados são iguais a pontuação de padrão.  

A terceira consulta é a primeira evidências de pontuação impacto de perfil. O termo de pesquisa é ainda 'melhor' para que estamos trabalhando com o mesmo conjunto de álbuns, mas porque o perfil pontuação fornece critérios adicionais que aumenta 'classificação' e 'última atualizados', alguns itens são propelled superior na lista.

A ilustração a seguir mostra a consulta quarta e última, aumentada pelo 'Margem'. O campo 'Margem' é não pesquisável e não pode ser retornado nos resultados da pesquisa. O valor de 'Margem' manualmente foi adicionado à planilha para ajudar a ilustrar o ponto de itens com margens superiores aparecem superiores na lista de resultados da pesquisa. 

   ![][9]

Agora que você tem tentei com perfis de pontuação, tente alterar o programa use sintaxe de consulta diferente, pontuação perfis ou dados mais elaborados. Links na próxima seção fornecem mais informações.

<a id="next-steps"></a>
## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre perfis de pontuação. Consulte [Adicionar um perfil de pontuação para um índice de pesquisa do Azure](http://msdn.microsoft.com/library/azure/dn798928.aspx) para obter detalhes.

Saiba mais sobre parâmetros de sintaxe e consulta de pesquisa. Consulte [Procurar documentos (API de REST de pesquisa do Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx) para obter detalhes.

Precisa voltar e saiba mais sobre a criação de índice? [Assista a este vídeo](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh) para entender as Noções básicas.

<!--Anchors-->
[Prerequisites]: #sub-1
[Download the sample application]: #sub-2
[Edit app.config]: #sub-3
[Explore the application]: #sub-4
[Build and run the application]: #sub-5
[Next steps]: #next-steps

<!--Image references-->
[12]: ./media/search-get-started-scoring-profiles/AzureSearch_CodeplexDownload.PNG
[11]: ./media/search-get-started-scoring-profiles/AzureSearch_Scoring_AppConfig.PNG
[10]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX1.PNG
[9]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX2.PNG 