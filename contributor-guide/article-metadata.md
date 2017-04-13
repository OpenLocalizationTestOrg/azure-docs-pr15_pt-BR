

#<a name="metadata-for-azure-technical-articles"></a>Metadados para artigos técnicos do Azure

Todos os artigos técnicos Azure contêm duas seções de metadados - uma seção de propriedades e uma seção de marcas. A seção Propriedades permite alguns automação de site e questões SEO, enquanto a seção de marcas permite muitas relatório de conteúdo interno. Ambas as seções são necessárias.

- [Sintaxe]
- [Uso]
- [Atributos e valores para a seção de propriedades]
- [Atributos e valores para a seção de marcas]

##<a name="syntax"></a>Sintaxe

A seção Propriedades usa a seguinte sintaxe:

    <properties
       pageTitle="Page title that displays in search results and the browser tab | Microsoft Azure"
       description="Article description that will be displayed on landing pages and in most search results"
       services="service-name"
       documentationCenter="dev-center-name"
       authors="GitHub-alias-of-only-one-author"
       manager="manager-alias"
       editor=""
       tags="optional"
       keywords="For use by SEO champs only. Separate terms with commas. Check with your SEO champ before you change content in this article containing these terms."/>

Seção marcas usa a seguinte sintaxe:

    <tags
       ms.service="required"
       ms.devlang="may be required"
       ms.topic="article"
       ms.tgt_pltfrm="may be required"
       ms.workload="na"
       ms.date="mm/dd/yyyy"
       ms.author="Your MSFT alias or your full email address;semicolon separates two or more"/>

##<a name="usage"></a>Uso

- O nome do elemento e nomes de atributo diferenciam maiusculas de minúsculas.
- O <properties> seção deve ser a primeira linha do seu arquivo.
- Deixe uma linha em branco após cada seção de metadados e antes do título da página para garantir que o título da página corretamente é convertido em HTML durante o processo de publicação.

## <a name="attributes-and-values-for-the-properties-section"></a>Atributos e valores para a seção de propriedades

![](./media/article-metadata/checkmark-small.png)**título da página**: obrigatório; importante SEO. O texto para este atributo aparece na guia do navegador e como o título em um resultado de pesquisa. Use 55-60 caracteres incluindo espaços e incluindo o identificador de site *| Microsoft Azure* (digitada como: espaço espaço de barra vertical Microsoft Azure).  O título da página deve ser diferente das H1.

![](./media/article-metadata/checkmark-small.png)**Descrição**: obrigatório; importante para a SEO (relevância) e funcionalidade de site. A descrição deve ter pelo menos 125 caracteres longa a 155 caracteres máximo incluindo espaços. Descreva a finalidade do seu conteúdo para que clientes saberá se escolhê-la em uma lista de resultados da pesquisa. O valor é:

- Este texto pode ser exibido como a descrição ou parágrafo abstrato nos resultados de pesquisa no Google.
- Este texto é exibido nos [resultados de índice do artigo](https://azure.microsoft.com/documentation/articles/).

![](./media/article-metadata/checkmark-small.png)**Serviços**: necessário para artigos que lidam com um serviço. Este valor ofter é conhecido como a "Libra massa a mensagens". Liste todos os serviços aplicáveis, separados por vírgulas. O primeiro serviço que você lista levarão a trilha de navegação para a página e o painel de navegação esquerdo que seja exibido com a página.

Em artigos que especificam um valor de serviços e um valor de documentationCenter, o valor de serviços levarão a navegação estrutural. Valores adicionais que você lista aparecerá como marcas no artigo publicado. Valores:

- Active directory
- ativo-diretório-b2c
- Active directory-ds
- aplicativo-service\api
- gerenciamento de API
- serviço de aplicativo
- aplicativo-servic\mobile
- aplicativo-service\web
- aplicativo-service\logic
- gateway de aplicativo
- obtenção de informações de aplicativo
- automação
- portal do Azure
- Gerenciador de recursos do Azure
- pilha do Azure
- backup
- em lotes
- práticas recomendadas
- serviços do BizTalk
- cache
- CDN
- Serviços de nuvem
- Catálogo de dados
- fábrica de dados
- análise de dados-Lucerne
- armazenamento de dados-Lucerne
- devtest laboratório
- DNS
- documentdb
- rota expressa
- hubs de evento
- hdinsight
- hub de IOT
- chave-cofre
- Balanceador de carga
- aprendizado de máquina
- Marketplace
- Serviços de mídia
- contrato de celular
- serviços móveis
- autenticação multi multifator
- hubs de notificação
- operacional de obtenção de informações
- operações de gerenciamento de pacote
- powerapps
- Gerenciador de recuperação
- cache relacionada
- RemoteApp
- gerenciamento de direitos
- Agendador
- pesquisa
- Centro de segurança
- barramento de serviço
- estrutura de serviço
- recuperação de site
- banco de dados SQL
- depósito de dados SQL
- relatório de SQL
- armazenamento
- armazenar
- storsimple
- análise de fluxo
- Gerenciador de tráfego
- máquinas virtuais
- rede virtual
- Visual studio-online
- gateway de VPN
- sites da Web

![](./media/article-metadata/checkmark-small.png)**documentationCenter**: necessário para artigos melhores centrados em dispositivos em destaque por meio de uma central de desenvolvimento. Especifique o Centro de desenvolvimento único ou o idioma que se aplica para o artigo. O valor que você listar levarão a trilha de navegação para a página. Em artigos que especificam um valor de serviços e um valor de documentationCenter, o valor de serviços levarão a navegação estrutural. Valores:

- **.NET**
- **nodejs**
- **Java**
- **PHP**
- **Python**
- **Ruby**
- **celular**: obsoleta. Substitua plataforma móvel específica.
- **IOS**: Verifing esse novo valor
- **Android**: verificando esse novo valor
- **Windows**: verificando esse novo valor
- **xamarin**: verificando esse novo valor

![](./media/article-metadata/checkmark-small.png)**autores**: obrigatória, um único valor. A conta GitHub o autor principal ou artigo EA de lista. Esse atributo unidades o autor do artigo publicado. Liste apenas um, apesar do nome plural do atributo.

![](./media/article-metadata/checkmark-small.png)**gerente**: obrigatório se você for um colaborador do Microsoft. Liste o alias de email do Gerenciador de publicação de conteúdo para a área de tecnologia. Se você for um colaborador de comunidade, inclua o atributo mas deixá-lo vazio para nós pode preenchê-lo.

![](./media/article-metadata/checkmark-small.png)**Editor**: não usado. Não usá-lo para outros fins.

![](./media/article-metadata/checkmark-small.png)**marcas**: opcional. Inclua somente se você deseja habilitar um link sob a trilha de artigo para a página de índice do artigo (http://azure.microsoft.com/documentation/articles/) a uma lista de prefiltered de artigos que correspondem a um dos valores aprovados. Esses valores devem oferece uma maneira de agrupar conteúdo quando o agrupamento de conteúdo não é específico do serviço. Essas marcas também podem fornecer rotular que indica a pilha de tecnologia que o artigo aplica-se ao. Este valor **não** suporte marcas de forma livre ou hashtags; as marcas devem estar habilitadas no site. Você pode fornecer vários valores de marcas para um artigo, separados por vírgulas. Os valores aprovados são:

  - arquitetura
  - Gerenciador de recursos do Azure
  - gerenciamento de serviço do Azure
  - cobrança
  - MySQL

![](./media/article-metadata/checkmark-small.png)**palavras-chave**: opcional. Para uso por apenas champs SEO. Termos separados por vírgulas. **Verifique com seu champ SEO antes de alterar ou excluir conteúdo neste artigo contendo esses termos.** Esse atributo registros palavras-chave a champ SEO destinou e está acompanhando para melhorar a ordem de pesquisa. As palavras-chave não processam no HTML publicado. Validação não exige esse atributo.

## <a name="attributes-and-values-for-the-tags-section"></a>Atributos e valores para a seção de marcas

![](./media/article-metadata/checkmark-small.png)**MS.Service**: obrigatório. Especifica o serviço do Azure, ferramenta ou recurso que o artigo aplica-se ao. Um valor por página.

 Se uma página se aplica a vários serviços, escolha o serviço para a qual ele mais se aplica diretamente; Por exemplo, um artigo que usa um aplicativo hospedado em sites para demonstrar a funcionalidade de barramento de serviço deve ter o valor de **barramento de serviço** , em vez de **sites da web**. Se uma página se aplica a vários serviços igualmente, escolha **várias**. Se uma página não aplicar a todos os serviços (Isso será raro), escolha **disp**.

 - **Active directory**
 - **ativo-diretório-b2c**
 - **Active directory-ds**
 - **gerenciamento de API**
 - **serviço de aplicativo**: aplica-se somente ao material conceitual geral no serviço de aplicativo
 - **api do serviço de aplicativo**
 - **lógica de serviço de aplicativo**
 - **aplicativo de serviço de celular**
 - **aplicativo de serviço de web**
 - **obtenção de informações de aplicativo**
 - **gateway de aplicativo**
 - **automação**
 - **Gerenciador de recursos do Azure**
 - **segurança do Azure**
 - **pilha do Azure**
 - **backup**
 - **em lotes**
 - **práticas recomendadas**
 - **serviços do BizTalk**
 - **cobrança**
 - **cache**
 - **CDN**
 - **Serviços de nuvem**
 - **Catálogo de dados**
 - **armazenamento de dados-Lucerne**
 - **análise de dados-Lucerne**
 - **devtest laboratório**
 - **rota expressa**
 - **hdinsight**
 - **Internet das coisas**
 - **hub de IOT**
 - **chave-cofre**
 - **aprendizado de máquina**
 - **Marketplace**: artigos sobre o Azure marketplace
 - **Serviços de mídia**
 - **contrato de celular**
 - **serviços móveis**
 - **autenticação multi multifator**
 - **vários**: página aplica-se a vários serviços igualmente
 - **disp**: A página não se aplica a todos os serviços (raros)
 - **hubs de notificação**
 - **operacional de obtenção de informações**
 - **powerapps**
 - **Gerenciador de recuperação**
 - **cache relacionada**
 - **RemoteApp**
 - **gerenciamento de direitos**
 - **Agendador**
 - **Centro de segurança**
 - **barramento de serviço**
 - **estrutura de serviço**
 - **recuperação do site**: serviços de recuperação antigo
 - **banco de dados SQL**
 - **depósito de dados SQL**
 - **relatório de SQL**
 - **armazenamento**
 - **armazenar**: artigos sobre serviços disponíveis por meio da loja do Azure
 - **storsimple**
 - **Gerenciador de tráfego**
 - **máquinas virtuais**
 - **rede virtual**
 - **Visual studio-online**
 - **gateway de VPN**
 - **sites da Web**

![](./media/article-metadata/checkmark-small.png)**MS.devlang**: obrigatório. Especifica a linguagem de programação que o artigo se aplica a. Único valor por página.

 Se uma página se aplica a duas linguagens de programação igualmente, escolha **várias**. Se uma página é principalmente conceitual e seu conteúdo é geralmente aplicável a várias linguagens de programação, escolha **várias**. Se uma página não se destina a desenvolvedores e a capacidade de aplicação de idioma programação não é relevante, escolha **disp**. Use a **api rest** para identificar os tópicos de referência de API REST.

 - **CPP**
 - **dotnet**
 - **Java**
 - **JavaScript**
 - **vários**: A página se aplica a várias linguagens de programação igualmente.
 - **disp**: A página não é visando desenvolvedores e não é específica para qualquer linguagens de programação.
 - **nodejs**
 - **objetivo-c**
 - **PHP**
 - **Python**
 - **api REST**
 - **Ruby**


![](./media/article-metadata/checkmark-small.png)**MS.Topic**: obrigatório. Especificações do tópico digite. A maioria das novas páginas criadas por colaboradores será artigo ou referência.

 - **artigo**: um tópico conceitual, tutorial, guia recurso ou outro artigo não-referência

 - **página de campanha**: Azure.com somente.  Uma página que é criada especificamente como uma página de aterrissagem para campanhas externas e não está incluído como parte do site principal IA.  Não deve ser usado para artigos de documentação ou regular doc páginas iniciais.  Exemplos: azure.microsoft.com/develop/net/aspnet/; Azure.microsoft.com/develop/Mobile/IOS/

 - **Centro de desenvolvimento-página inicial**: Azure.com somente.  Um desenvolvimento centralizar a página inicial, por exemplo, / desenvolver/líquido /

 - **Get-iniciado-artigo**: atribuir para artigos que estão presentes na seção Introdução ou visão geral do painel de navegação esquerdo para um serviço.

 - **banner do artigo**: um tutorial "banner" que foi projetado para fornecer uma introdução a um serviço ou recurso que obtém os visitantes a usar o serviço rapidamente e unidades teste gratuito inscrições e ativações MSDN. Atribuir esse valor apenas para artigos que estão presentes na parte superior da página inicial da documentação seu serviço.

 - **home page**: página inicial de documentação de nível superior. Só temos dois: azure.microsoft.com/documentation/ e msdn.microsoft.com/library/azure/

 - **página de índice**: segundo nível inicial páginas para idiomas, serviços ou recursos de programação. Essas são distribuídas entre Azure.com e a biblioteca e são usadas como pontos de entrada para obter informações mais específicas, com escopo. Exemplos: http://azure.microsoft.com/develop/mobile/resources-wp8/, http://msdn.microsoft.com/library/azure/jj673460.aspx, http://msdn.microsoft.com/library/azure/hh689864.aspx

 - **página de infographic**: Azure.com apenas. Uma página que apresenta um infographic pesquisável ou cartaz, por exemplo http://azure.microsoft.com/documentation/infographics/windows-azure/

 - **referência**: página de referência de API um (incluindo a API REST) ou página de referência de cmdlet do PowerShell

 - **home page Service**: Azure.com somente.  Uma doc serviço home page, por exemplo, /documentation/services/virtual-machines /

 - **site-seção-home page**: Azure.com somente. Uma "home page" para um determinado tipo de conteúdo em azure.com exemplos: http://azure.microsoft.com/documentation/infographics/, http://azure.microsoft.com/documentation/scripts/, http://azure.microsoft.com/documentation/videos/home/, http://azure.microsoft.com/downloads/

 - **página de vídeo**: Azure.com somente.  Uma página que apresenta um vídeo, por exemplo http://azure.microsoft.com/documentation/videos/azure-webjobs-hosting-testing-net/

![](./media/article-metadata/checkmark-small.png)**MS.tgt_pltfrm**: obrigatório. Especifica a plataforma de destino, por exemplo Windows, Linux, Windows Phone, iOS, Android ou plataformas de cache especial. Um valor por página. Este valor será **ND** para a maioria dos tópicos exceto mobile e máquinas virtuais.

 - **função de cache**
 - **múltiplo de cache**
 - **relacionada de cache**
 - **serviço de cache**
 - **cache compartilhado**
 - **interface de comando linha**
 - **ibiza**: conteúdo que usa o portal de Ibiza. Use este comando somente em casos onde o recurso está sendo discutido está disponível através do portal de Ibiza e o portal atual.
 - **celular android**: Azure.com somente agora
 - **Mobile-html**: Azure.com somente agora
 - **ios Mobile**: Azure.com somente agora
 - **dispositivos móveis-kindle**: Azure.com apenas agora
 - **múltiplo de celular**
 - **Mobile-nokia-x**: Azure.com somente agora
 - **Mobile phonegap**: Azure.com somente agora
 - **Mobile-sencha**: Azure.com apenas agora
 - **windows Mobile**: Azure.com somente agora; Universal do Windows
 - **celular windows**
 - **repositório de windows Mobile**
 - **Mobile-xamarin**: Azure.com apenas agora; Xamarin todas as plataformas
 - **Mobile-xamarin-android**: Azure.com somente agora
 - **Mobile-xamarin-ios**: Azure.com somente agora
 - **vários**: A página se aplica a várias plataformas igualmente
 - **disp**: um especificador de plataforma não é aplicável para esta página
 - **PowerShell**
 - **máquina virtual linux**
 - **múltiplo de máquina virtual**
 - **máquina virtual-windows**
 - **máquina virtual-windows-sharepoint**
 - **máquina virtual-windows-sql-server**
 - **VS--Introdução**: identifica o grupo de página Introdução VS. Marca adicionada 1/12/14.
 - **VS-what-aconteceu**: identifica a página VS obtendo iniciado o que aconteceu. Marca adicionada 1/12/14.

![](./media/article-metadata/checkmark-small.png)**MS.Workload**: obrigatório. Especifica a carga de trabalho Azure que a página se aplica a. Um valor somente por artigo.

**Atualizar 8/6/15** O valor de ms.workload está sendo mapeado por um xls, não o valor no arquivo .md. O valor de ms.workload ainda é necessário para validação até que o recurso pode ser atualizado. Agora está sendo programado que funcionam.  Use **"ND"** como o valor por agora.

![](./media/article-metadata/checkmark-small.png)**MS.Date**: obrigatório. Especifica a data que o artigo última revisão de relevância, precisão, capturas de tela correta e links úteis. Insira a data no formato dd/mm/aaaa. Essa data também aparece no artigo do publicada como a data da última atualização.

![](./media/article-metadata/checkmark-small.png)**MS.Author**: obrigatório. Especifica o author(s) associado com o tópico. Relatórios internos (como a atualização do), esse valor é usado para associar o direito author(s) o artigo. Para especificar vários valores, você deve separá-las com ponto e vírgula. Aliases da Microsoft ou endereços de email completo são aceitáveis. O comprimento pode ser não tenha mais de 200 caracteres.


###<a name="contributors-guide-links"></a>Links de guia dos colaboradores

- [Artigo de visão geral](./../README.md)
- [Índice de artigos de orientação](./contributor-guide-index.md)


<!--Anchors-->
[Sintaxe]: #syntax
[Uso]: #usage
[Atributos e valores para a seção de propriedades]: #attributes-and-values-for-the-properties-section
[Atributos e valores para a seção de marcas]: #attributes-and-values-for-the-tags-section
