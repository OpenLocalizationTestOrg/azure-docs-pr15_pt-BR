<properties
   pageTitle="Perguntas frequentes sobre o catálogo de dados do Azure | Microsoft Azure"
   description="Perguntas frequentes sobre o catálogo de dados do Azure, incluindo recursos para descoberta de fonte de dados, as anotações e gerenciamento."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/04/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-frequently-asked-questions"></a>Perguntas frequentes sobre o catálogo de dados do Azure

Este artigo fornece respostas para perguntas frequentes relacionadas ao serviço de **Catálogo de dados do Azure** da Microsoft.

## <a name="q-what-is-azure-data-catalog"></a>P: o que é o catálogo de dados do Azure?

R catálogo de dados Microsoft Azure é um serviço totalmente gerenciado hospedado na nuvem Microsoft Azure que serve como um sistema de registro e sistema de descoberta para fontes de dados corporativos. Catálogo de dados do Azure fornece recursos que permitem que qualquer usuário – de analistas para cientistas de dados para os desenvolvedores – registrar, descobrir, compreendam e consumam fontes de dados.

## <a name="q-what-customer-challenges-does-azure-data-catalog-solve"></a>P: qual cliente desafios faz o catálogo de dados do Azure resolver?

Catálogo de dados do Azure endereços o desafio de descoberta de fonte de dados e "dados escuros" permitindo que os usuários descobrir e entender origens de dados corporativos.

## <a name="q-who-are-the-target-audiences-for-azure-data-catalog"></a>P: quem são os públicos-alvo do catálogo de dados do Azure?

Catálogo de dados do Azure fornece recursos para técnicos e de usuários, incluindo:

- Desenvolvedores de dados, BI e profissionais de análise: quem é responsável por produzir conteúdo de dados e análise para consumir por outras pessoas
-   Administradores de dados: Aqueles que têm o conhecimento sobre os dados, o que significa e como ele se destina a ser usado e para qual finalidade
- Os consumidores de dados: Aqueles que precisam ser capazes de descobrir, compreender e se conectar aos dados necessários para fazer seu trabalho usando a ferramenta de sua escolha
- Central IT: aqueles que precisa fazer centenas de fontes de dados detectáveis para os usuários de negócios e que precisam manter supervisão sobre como dados está sendo usados e por quem

## <a name="q-what-is-the-azure-data-catalog-region-availability"></a>P: qual é a disponibilidade de região de catálogo de dados do Azure?

Serviços de catálogo de dados Azure estão disponíveis atualmente nos seguintes data centers:

- Oeste EUA
- Leste EUA
- Europa Ocidental
- Norte da Europa
- Austrália Oriental
- Sudeste Asiático

## <a name="q-what-are-the-limits-on-the-number-of-data-assets-in-azure-data-catalog"></a>P: o que são os limites no número de ativos de dados no catálogo de dados do Azure?

O catálogo de dados gratuito Edition do Azure está limitado aos ativos de dados registrados 5.000.

O catálogo de dados Standard Edition do Azure suporta até 100.000 ativos de dados registrados.

## <a name="q-what-are-the-supported-data-source-and-asset-types"></a>P: o que são os tipos de origem e de ativos de dados com suporte?

Consulte [DSR de catálogo de dados](data-catalog-dsr.md) para a lista de fontes de dados com suporte no momento.

## <a name="q-how-do-i-request-support-for-another-data-source"></a>P: como posso solicitar suporte para outra fonte de dados?

Solicitações de recurso e outros comentários podem ser enviados no [Fórum do catálogo de dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="q-how-do-i-get-started-with-azure-data-catalog"></a>P: como faço para começar a com o catálogo de dados do Azure?

O melhor lugar para começar é seguindo as instruções na [Introdução ao catálogo de dados](data-catalog-get-started.md). Este artigo é uma visão geral de ponta a ponta dos recursos no serviço.

## <a name="q-how-do-i-register-my-data"></a>P: como se registrar meus dados?

Para registrar seus dados no catálogo de dados do Azure, inicie a ferramenta de registro do catálogo de dados do Azure da área do portal do catálogo de dados do Azure "Publicar". No aplicativo de publicação do catálogo de dados do Azure, faça logon usando as mesmas credenciais que você usa para acessar o portal de catálogo de dados do Azure e selecione a fonte de dados e os ativos específicos que você deseja registrar.

## <a name="q-what-properties-are-extracted-for-data-assets-that-are-registered"></a>P: quais propriedades são extraídas para ativos de dados que são registrados?

As propriedades específicas serão diferentes de fonte de dados à fonte de dados, mas em geral o serviço de publicação do catálogo de dados do Azure irá extrair as seguintes informações:

- Nome do ativo
- Tipo de ativo
- Descrição do ativo
- Nomes de atributo/coluna
- Tipos de dados de atributo/coluna
- Descrição do atributo/coluna

> [AZURE.IMPORTANT] Registrando ativos de dados com o catálogo de dados do Azure não mover ou copiar os dados para a nuvem. Registrar ativos a partir de uma fonte de dados irá copiar metadados dos ativos no Azure, mas os dados permanecem no local de origem de dados existente. A única exceção a essa regra é se um usuário optar por carregar visualizar registros ou um perfil de dados ao registrar ativos. Quando incluindo uma visualização, até 20 registros serão copiados do cada ativo e armazenados como um instantâneo no catálogo de dados do Azure. Ao incluir um perfil de dados, agregar informações (como o tamanho de tabelas, os valores nulos porcentagem por coluna e os valores mínimos, máximos e médio para colunas) serão calculadas e incluídas nos metadados armazenados no catálogo.

<br/>

> [AZURE.NOTE] Para fontes de dados como o SQL Server Analysis Services que tenham uma propriedade de **Descrição** de primeira classe, o aplicativo de publicação do catálogo de dados do Azure irá extrair valor da propriedade. Para o SQL Server bancos de dados relacionais, que não têm uma propriedade de **Descrição** de primeira classe, o aplicativo de publicação do catálogo de dados do Azure irá extrair o valor da ms_description propriedade para objetos e colunas estendida. Para obter mais informações, consulte o TechNet [Usando propriedades estendidas em objetos de banco de dados](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).

## <a name="q-how-long-should-it-take-for-newly-registered-assets-to-appear-in-azure-data-catalog"></a>P: quanto tempo deve tomar para ativos recém registrados apareça no catálogo de dados do Azure?

Depois que você registrar ativos com o catálogo de dados do Azure pode haver um período de 5 a 10 segundos antes que eles apareçam no portal do catálogo de dados do Azure.

## <a name="q-how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>P: como anotar e enriquecer os metadados para Meus ativos de dados registrados?

A maneira mais simples para fornecer metadados para ativos registrados é selecione ativo no portal do catálogo de dados do Azure e insira os valores de metadados no painel de propriedades ou painel de esquema para o objeto selecionado.

Você também pode fornecer alguns metadados, como especialistas em marcas e, durante o processo de registro. Os valores fornecidos no serviço de publicação do catálogo de dados do Azure serão aplicada a todos os ativos que está sendo registrados no momento. Para exibir os objetos recentemente registrado no portal para anotação adicional, selecione o botão de **Portal de modo de exibição** na tela final do aplicativo de publicação do catálogo de dados do Azure.

## <a name="q-how-do-i-delete-my-registered-data-objects"></a>P: como posso excluir objetos my dados registrados?

Você pode excluir um objeto de catálogo de dados do Azure selecionando o objeto no portal e, em seguida, clicando no botão **Excluir** . Isso removerá os metadados para o objeto de catálogo de dados do Azure, mas não afeta a fonte de dados subjacente real.

## <a name="q-what-is-an-expert"></a>P: o que é um especialista?

Especialista é uma pessoa que tenha uma perspectiva informada sobre um objeto de dados. Um objeto pode ter vários especialistas. Um especialista não precisa ser o "proprietário" para um objeto; o especialista é simplesmente alguém que sabe como os dados podem e deve ser usado.

## <a name="q-how-do-i-share-information-with-the-azure-data-catalog-team-if-i-encounter-problems"></a>P: como posso compartilhar informações com a equipe do catálogo de dados do Azure se encontrar problemas?

Use o fórum do catálogo de dados do Azure para relatar problemas, compartilhar informações e fazer perguntas. O Fórum pode ser encontrado em http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409

##<a name="q-does-azure-data-catalog-work-with-this-other-data-source-im-interested-in"></a>P: Azure catálogo de dados funciona com essa fonte de dados que estiver interessado em?
Estamos trabalhando ativamente sobre como adicionar mais fontes de dados ao catálogo de dados do Azure. Se houver uma fonte de dados que você gostaria de ver suportados, sugira que ele (ou o suporte de voz, se ele já foi sugeriu) no [Fórum do catálogo de dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="q-how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>P: como o catálogo de dados do Azure está relacionado ao catálogo de dados no Power BI para Office 365?

Você pode pensar catálogo de dados do Azure como uma evolução do catálogo de dados. Catálogo de dados do Azure fornece recursos semelhantes para publicação de fonte de dados e descoberta, mas é com foco em cenários mais amplos e não dependentes no Office 365. Logo após o catálogo de dados do Azure fique disponível os dois catálogos serão mesclar em um único serviço.

## <a name="q-what-permissions-does-a-user-need-to-register-assets-with-azure-data-catalog"></a>P: quais permissões de um usuário precisa registrar ativos com o catálogo de dados do Azure?

O usuário executando a ferramenta de registro do catálogo de dados do Azure precisa permissões na fonte de dados que permitirá que ele ler os metadados da fonte. Se o usuário também seleciona para incluir uma visualização, o usuário também deve ter permissões que permitam a leitura dos dados nos objetos que está sendo registrado.

## <a name="q-will-azure-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>P: catálogo de dados do Azure estarão disponível para implantação local também?

Catálogo de dados do Azure é um serviço de nuvem que pode trabalhar com fontes de dados de nuvem e no local, oferece uma solução de descoberta de fonte de dados híbrida. Atualmente, não há nenhum plano para uma versão do serviço de catálogo de dados do Azure que serão executadas no local.

##<a name="q-can-we-extract-more--richer-metadata-from-the-data-sources-we-register"></a>P: nós é possível extrair metadados mais / mais sofisticados de fontes de dados, que podemos registrar?

Estamos trabalhando ativamente para expandir os recursos do catálogo de dados do Azure. Se houver metadados adicionais que você gostaria de ver extraído da fonte de dados durante o registro, por favor sugerir (ou voto para que ele se ele já foi sugeriu) no [Fórum do catálogo de dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). No futuro, podemos permitirá terceiros adicionar novos tipos de fonte de dados por meio de uma API de extensibilidade.

## <a name="q-how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>P: como restringir a visibilidade de ativos de dados registrados, para que apenas determinadas pessoas possam detectá-las?

R: selecione ativos de dados no catálogo de dados do Azure e clique no botão "Tomar posse". Proprietários de ativos de dados no catálogo de dados do Azure podem alterar as configurações de visibilidade, para permitir que todos os usuários de catálogo para descobrir os ativos de propriedade ou para restringir a visibilidade para usuários específicos.

## <a name="q-how-do-i-update-the-registration-for-a-data-asset-to-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>P: como posso atualizar o registro de um ativo de dados para que muda na fonte de dados sejam refletidas no catálogo?

R: para atualizar os metadados de ativos de dados que já estão registrados no catálogo, basta registre novamente a fonte de dados que contém os ativos. As alterações na fonte de dados, como colunas está sendo adicionadas ou removidas de tabelas ou exibições, serão atualizadas no catálogo, mas quaisquer anotações fornecidas por usuários serão mantidas.

## <a name="q-my-question-isnt-answered-here--what-should-i-do"></a>P: minha dúvida não seja resolvida aqui – o que devo fazer?

Vá diretamente para o [Fórum do catálogo de dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Perguntas lá encontrarão seu caminho aqui.
