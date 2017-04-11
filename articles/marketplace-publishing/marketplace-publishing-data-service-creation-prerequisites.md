<properties
   pageTitle="Pré-requisitos técnicos para a criação de um serviço de dados para o Marketplace | Microsoft Azure"
   description="Compreender os requisitos para a criação de um serviço de dados para implantar e vender no Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="hascipio; avikova" />

# <a name="technical-pre-requisites-for-creating-a-data-service-offer-for-the-azure-marketplace"></a>Oferecem técnicos pré-requisitos para a criação de um serviço de dados do Azure Marketplace

>[AZURE.IMPORTANT] **Neste momento, não são mais integração qualquer novas fornecedores de serviço de dados. Novo dataservices não irá obter aprovado para listagem.** Se você tiver um aplicativo de negócios SaaS que você gostaria de publicar no AppSource você pode encontrar mais informações [aqui](https://appsource.microsoft.com/partners). Se você tiver um aplicativos IaaS ou serviço de desenvolvedor que você gostaria de publicar no Azure Marketplace, você pode encontrar mais informações [aqui](https://azure.microsoft.com/marketplace/programs/certified/).

Leia o processo completamente antes de começar e entenda onde e por que cada etapa é executada. Tanto quanto possível, você deve preparar informações da sua empresa e outros dados, baixe ferramentas necessárias e/ou criar componentes técnicos antes de iniciar o processo de criação de oferta.

Você deve ter os seguintes itens prontos antes de iniciar o processo:

## <a name="make-a-decision-on-what-technology-will-be-used-to-publish-your-data-service-offer"></a>Tomar uma decisão sobre qual tecnologia será usada para publicar sua oferta de serviço de dados

Um fornecedor pode decidir entre várias tecnologias quando o serviço de dados de publicação no Azure Marketplace. As principais tecnologias suportados descritas a seguir. Independentemente que tecnologia é usada para publicar o serviço de dados, o usuário final consome dados por meio do **feed OData** expostos pelo serviço do Azure Marketplace. Informações completas sobre serviço OData que você pode encontrar no [http://www.odata.org/](http://www.odata.org/)

## <a name="sql-azure-database"></a>SQL Azure banco de dados

Ter dataset pronto no SQL Azure é responsabilidade do Publisher. Você precisará assinar Azure, provisionar tamanho apropriado do banco de dados e carregar seus dados para o banco de dados do SQL Azure. O Publisher também é responsável para manter seus dados sempre atualizados. Mais informações sobre como assinar Azure serviços de que você pode encontrar no [https://azure.microsoft.com/services/sql-database/](https://azure.microsoft.com/services/sql-database/)


Ao mover os dados para o SQL Azure, do Azure Marketplace pode expor tabelas e exibições. O Publisher pode especificar quais tabelas/modos de exibição e colunas são expostas para o usuário final. Ainda mais o provedor de conteúdo também pode especificar quais colunas podem ser consultadas pelo usuário final e quais são retornados somente na carga. Isso oferece um alto nível de flexibilidade sobre quais dados no banco de dados devem ser expostos. Colunas que podem ser consultadas precisam ser feito por um ou mais índices de banco de dados.

## <a name="rest-based-web-service"></a>Serviço web REST com base

Suporte para o protocolo: **HTTPS somente**

Serviços REST com base em existente podem ser expostos através do Azure Marketplace. Porque o dataset sempre é exposto para o usuário final como um feed OData, o serviço do Azure Marketplace precisa ser capaz de mapear o serviço a um OData com base em serviço. Para fazer isso o restante com base em pontos de extremidade precisa expor todos os parâmetros como parâmetros HTTP.

A carga precisa estar em um formulário que pode ser mapeado para uma resposta ATOM. Portanto, a resposta do serviços precisa estar no formato XML e só poderá conter um elemento de repetição que contém os valores de carga (como o conjunto de registros). O serviço do Azure Marketplace será mapeada o nó repetitivo para o nó de entrada no ATOM e os valores de carga em nós de propriedade dentro do nó de entrada.

Informações de autorização (como API chave, autenticação token, etc.) devem ser fornecido como um parâmetro de HTTP ou no cabeçalho HTTP (par de valor de chave) – também há suporte para autenticação básica. Uma chave válida precisa ser fornecido e todas as solicitações por meio do Azure Marketplace estão sendo feitas por meio de chave. Usuário monitoramento e cobrança acontece na camada do Azure Marketplace.

Erros retornados pelo serviço precisam ser mapeado nos códigos de status HTTP. Caso o serviço retorna um XML que contém o erro que esses vai ser mapeado pelo serviço do Azure Marketplace para códigos de status HTTP.

## <a name="soap-based-web-services"></a>Serviços web SOAP com base

Protocolo: **HTTPS somente**

Os requisitos são iguais aos restante com base em seção serviço. A única diferença é que parâmetros também podem ser fornecidos no corpo de uma XML que está sendo lançado ao serviço do editor com todas as solicitações feitas por meio do Azure Marketplace. Isso significa que o usuário fornece no front-end de parâmetros HTTP estão sendo traduzidos em elementos XML de um documento XML que está sendo lançado com a solicitação de serviço da web do provedor de conteúdo.

## <a name="odata-based-web-services"></a>Serviços web do OData com base

Protocolo: **HTTPS somente**

Dados podem ser expostos como um serviço OData ao Azure Marketplace. O sistema vai passar o serviço por meio e substituirá a raiz do serviço com a raiz de serviço do Azure Marketplace – para garantir que todas as chamadas subsequentes percorrer Azure Marketplace.

Serviços OData não só precisarão ir em relação a um banco de dados back-end. OData oferece suporte para qualquer tipo de armazenamento ou a lógica comercial para orientar o serviço.


## <a name="next-steps"></a>Próximas etapas
Agora que você revisar os pré-requisitos e concluir as tarefas necessárias, você pode avançar com a criação de sua oferta de serviço de dados conforme detalhado no [Guia de publicação de serviço de dados](marketplace-publishing-data-service-creation.md).

Ou, se você quiser revisar o processo geral e os respectivos artigos para cada uma das fases publicação, consulte o artigo [Introdução: como publicar uma oferta do Azure Marketplace](marketplace-publishing-getting-started.md).

[link-acct]:marketplace-publishing-accounts-creation-registration.md
