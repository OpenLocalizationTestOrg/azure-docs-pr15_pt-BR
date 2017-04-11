<properties
   pageTitle="Como gerenciar ativos de dados | Microsoft Azure"
   description="Artigo de instruções realçando como controlar a visibilidade e a propriedade de ativos de dados registrado no catálogo de dados do Azure."
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


# <a name="how-to-manage-data-assets"></a>Como gerenciar ativos de dados

## <a name="introduction"></a>Introdução

**Catálogo de dados do Azure** oferece recursos de descoberta de fonte de dados, permitindo que os usuários descobrir facilmente e compreender as fontes de dados que precisam para executar análises e tomar decisões. Esses recursos de descoberta tornam o maior impacto quando todos os usuários podem encontrar e entender a maior variedade de fontes de dados disponíveis. Com isso em mente, o comportamento padrão do catálogo de dados para todas as fontes de dados registrados ser descoberto pelos e visível para – – todos os usuários de catálogo.

Catálogo de dados não dar aos usuários acesso aos dados em si. Acesso a dados é controlado pelo proprietário da fonte de dados. Catálogo de dados permite aos usuários para descobrir fontes de dados e exibir os metadados relacionados às fontes registradas no catálogo.

Pode haver situações, no entanto, onde fontes de dados somente devem estar visíveis para usuários específicos ou membros de grupos específicos. Para esses cenários, catálogo de dados permite que os usuários obter a propriedade de ativos de dados registrados no catálogo do e para o controle, em seguida, a visibilidade dos ativos possuem.

> [AZURE.NOTE] A funcionalidade descrita neste artigo estão disponíveis apenas no Standard Edition do Azure catálogo de dados. A edição livre não fornecem recursos para a propriedade e restringindo visibilidade de ativos de dados.

## <a name="managing-ownership-of-data-assets"></a>Gerenciando a propriedade de ativos de dados
Por padrão, os ativos de dados registrados no catálogo de dados são proprietário; qualquer usuário com permissão para acessar o catálogo pode descobrir e anotar esses ativos. Usuários podem levar a propriedade de ativos de dados sem proprietário e podem limitar a visibilidade os ativos que eles possuem.

Quando um ativo de dados no catálogo de dados pertence, somente os usuários autorizados pelos proprietários podem descobrir o ativo e exibir seus metadados e somente os proprietários podem excluir o ativo do catálogo.

> [AZURE.NOTE] A propriedade no catálogo de dados afeta apenas os metadados armazenados no catálogo. Ele não concede todas as permissões na fonte de dados subjacente.

### <a name="taking-ownership"></a>Tomar a propriedade
Os usuários podem levar a propriedade de ativos de dados selecionando a opção de "Tomar posse" no portal do catálogo de dados. Sem permissões especiais são necessárias para obter a propriedade de um ativo de proprietário de dados; qualquer usuário pode levar a propriedade de um ativo de proprietário de dados.

### <a name="adding-owners-and-co-owners"></a>Adicionando proprietários e proprietários de colegas
Se um ativo de dados já pertence, os usuários simplesmente não é possível obter a propriedade – eles devem ser adicionados como proprietários co por um proprietário existente. Qualquer proprietário pode adicionar outros usuários ou grupos de segurança como proprietários de colegas.

> [AZURE.NOTE] É uma prática recomendada ter pelo menos dois indivíduos como proprietários para qualquer propriedade dados ativos.

### <a name="removing-owners"></a>Removendo proprietários
Assim como qualquer proprietário ativo pode adicionar colegas proprietários, qualquer proprietário ativo pode remover qualquer coproprietário.

Se um proprietário de ativo remove si como um proprietário, ele não pode mais gerenciar o ativo. Se um proprietário de ativo remove si como um proprietário e não há nenhuma outros colegas proprietários, o ativo será revertida para um estado não proprietário.

## <a name="visibility"></a>Visibilidade
Os proprietários de ativos de dados podem controlar a visibilidade dos ativos de dados que eles possuem. Para restringir a visibilidade do padrão – onde todos os usuários do catálogo de dados podem descobrir e exibir os dados ativos – o proprietário do ativo pode alternar a configuração de visibilidade de "Todos" para proprietários & estes "usuários" nas propriedades do ativo. Proprietários, podem adicionar usuários e grupos de segurança específicos.

> [AZURE.NOTE] Sempre que possível, permissões de propriedade e visibilidade de ativo devem ser atribuídas para grupos de segurança e não para usuários individuais.

## <a name="catalog-administrators"></a>Administradores de catálogo
Administradores de catálogo de dados são implicitamente co proprietários de todos os ativos do catálogo. Proprietários de ativos não podem remover a visibilidade de administradores de catálogo, e os administradores podem gerenciar a propriedade e visibilidade para todos os ativos de dados do catálogo.

## <a name="summary"></a>Resumo
Modelo de crowdsourcing do catálogo de dados para descoberta de ativos de dados e metadados permite que todos os usuários de catálogo contribuir e descobrir. Standard Edition do catálogo de dados oferece recursos de gerenciamento e a propriedade limitar a visibilidade e o uso de ativos de dados específicos.
