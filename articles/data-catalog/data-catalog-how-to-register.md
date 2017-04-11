<properties
   pageTitle="Como registrar a fontes de dados | Microsoft Azure"
   description="Artigo de instruções realce como registrar a fontes de dados com o catálogo de dados do Azure, incluindo os campos de metadados extraídos durante o registro."
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


# <a name="how-to-register-data-sources"></a>Como registrar a fontes de dados

## <a name="introduction"></a>Introdução
**Catálogo de dados do Microsoft Azure** é um serviço de nuvem totalmente gerenciado que serve como um sistema de registro e sistema de descoberta para fontes de dados corporativos. Em outras palavras, **Catálogo de dados do Azure** trata ajudar as pessoas a descobrir, entender e usar fontes de dados e ajudar as empresas para obter mais valor de seus dados existentes. E é a primeira etapa para fazer uma fonte de dados detectáveis pelo **Catálogo de dados do Azure** registrar a fonte de dados.
## <a name="registering-data-sources"></a>Registrando fontes de dados
Registro é o processo de extrair metadados da fonte de dados e copiando dados para o serviço de **Catálogo de dados do Azure** . Os dados permanecem onde ele atualmente reside e permaneça sob o controle dos administradores e políticas do sistema atual.

Para registrar uma fonte de dados, basta inicie a ferramenta de registro de fonte de dados do **Catálogo de dados do Azure** a partir do portal de **Catálogo de dados do Azure** . Log com o seu trabalho ou conta do Estudante (as mesmas credenciais Azure Active Directory você usa para fazer logon no portal do) e em seguida, selecione a fonte de dados que você deseja registrar.
Consulte o tutorial de [Introdução ao catálogo de dados do Azure](data-catalog-get-started.md) para obter mais detalhes passo a passo.

Depois que a fonte de dados tiver sido registrada, o catálogo controla sua localização e índices seus metadados, para que os usuários podem pesquisar, procurar, descobrir a origem de dados e use seu local para se conectar ao usando o aplicativo ou a ferramenta de sua escolha.

## <a name="sources-supported"></a>Fontes com suporte
Consulte [DSR de catálogo de dados](data-catalog-dsr.md) para a lista de fontes de dados com suporte no momento.
<br/>


## <a name="structural-metadata"></a>Metadados estruturais
Quando você estiver registrando uma fonte de dados, a ferramenta de registro irá extrair informações sobre a estrutura dos objetos selecionados – isso é referido como estruturais metadados.

Para todos os objetos, este metadados estruturais incluirá o local do objeto, para que os usuários que descubra os dados podem usar essas informações para se conectar ao objeto nas ferramentas do cliente de sua escolha. Outros metadados estruturais incluem o tipo e o nome do objeto e, em seguida, digite o nome de atributo/coluna e dados.

## <a name="descriptive-metadata"></a>Metadados descritivos
Além de núcleo estruturais metadados extraídos da fonte de dados, a ferramenta de registro de fonte de dados também extrai metadados descritivos também. Para SQL Server Analysis Services e SQL Server Reporting Services, isso é obtido as propriedades de descrição expostas por esses serviços. Para SQL Server, valores fornecidos usando o ms_description propriedade estendida serão extraídos. Banco de dados Oracle, a ferramenta de registro de fonte de dados irá extrair a coluna de comentários no modo de exibição ALL_TAB_COMMENTS.

Além de metadados descritivo extraídos da fonte de dados, os usuários também podem inserir metadados descritivos usando a ferramenta de registro de fonte de dados. Os usuários podem adicionar marcas e podem identificar especialistas para os objetos que está sendo registrados. Todos esses metadados descritivo é copiado para o serviço de **Catálogo de dados do Azure** juntamente com os metadados estruturais.

## <a name="including-previews"></a>Incluindo visualizações

Por padrão, apenas os metadados é extraído de fontes de dados e copiado para o serviço de **Catálogo de dados do Azure** , mas Noções básicas sobre que uma fonte de dados é geralmente facilitada por vendo uma amostra dos dados que ele contém.

A ferramenta de registro de fonte de dados do **Catálogo de dados do Azure** permite aos usuários incluir uma visualização de instantâneo dos dados em cada tabela e o modo de exibição registrado. Se o usuário opta-in para incluir visualizações durante o registro, a ferramenta de registro incluirá até 20 registros de cada tabela e o modo de exibição. Esse instantâneo depois é copiado para o catálogo junto com os metadados estruturais e descritivo.


> [AZURE.NOTE]  Ampla tabelas com um grande número de colunas podem ter menos de 20 registros incluídos na sua visualização.


## <a name="including-data-profiles"></a>Incluindo perfis de dados

Assim como visualizações incluindo podem fornecer contexto valioso para usuários procurando fontes de dados no **Catálogo de dados do Azure**, incluindo um perfil de dados pode também facilitam entender fontes de dados detectados.

A ferramenta de registro de fonte de dados do **Catálogo de dados do Azure** permite aos usuários incluir um perfil de dados para cada tabela e o modo de exibição registrado. Se o usuário optar por incluir um perfil de dados durante o registro, a ferramenta de registro incluirá estatísticas agregadas sobre os dados em cada tabela e o modo de exibição, incluindo:

* O número de linhas e o tamanho dos dados no objeto
* A data para a atualização mais recente dos dados e o esquema de objeto
* O número de registros nulo e valores distintos para colunas
* Os valores mínimo, máximo, média e o desvio padrão para colunas

Essas estatísticas são copiadas para o catálogo junto com os metadados estruturais e descritivo.

> [AZURE.NOTE]  Colunas de texto e data não incluirão as estatísticas de média ou desvio padrão em seu perfil de dados.

## <a name="updating-registrations"></a>Atualizando registros

Registrar uma fonte de dados tornará descoberto no **Catálogo de dados do Azure** usando os metadados e visualização opcionais extraídas durante o registro. Se a fonte de dados precisa ser atualizado no catálogo (por exemplo, se o esquema de um objeto foi alterado, ou tabelas originalmente excluídas devem ser incluídas, ou um usuário desejar atualizar os dados incluídos nas visualizações) a ferramenta de registro de fonte de dados pode ser executada novamente.

Registrar novamente uma fonte de dados já registrado executa uma operação de mesclagem "upsert": objetos existentes serão atualizados, enquanto novos objetos serão criados. Todos os metadados fornecidos por usuários por meio do portal de **Catálogo de dados do Azure** serão mantido.

## <a name="summary"></a>Resumo
Registrar uma fonte de dados com o **Catálogo de dados do Azure** facilita a descobrir e entender, copiando metadados estruturais e descritivo da fonte de dados para o serviço de catálogo de fonte de dados. Depois que uma fonte de dados tiver sido registrada, ele pode então ser anotado, gerenciada e descobertos usando o portal de **Catálogo de dados do Azure** .

## <a name="see-also"></a>Consulte também
- Tutorial de [Introdução ao catálogo de dados do Azure](data-catalog-get-started.md) para obter detalhes passo a passo sobre como registrar a fontes de dados.
