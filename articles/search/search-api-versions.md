<properties
   pageTitle="As versões de API de pesquisa do Azure | Microsoft Azure | API de pesquisa"
   description="Política de versão para APIs de REST de pesquisa do Azure e a biblioteca de cliente no SDK do .NET."
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="08/16/2016"
   ms.author="brjohnst"/>

# <a name="api-versions-in-azure-search"></a>Versões de API na pesquisa do Azure

Pesquisa Azure implementa atualizações de recursos regularmente. Às vezes, mas não sempre, essas atualizações exigem conosco publicar uma nova versão do nosso API para preservar compatibilidade com versões anteriores. Uma nova versão de publicação permite controlar quando e como você integrar atualizações de serviço de pesquisa no seu código.

Como uma regra, tentamos publicar novas versões somente quando necessário, desde que ela pode envolver um esforço para atualizar seu código para usar uma nova versão de API. Estamos apenas publica uma nova versão se for necessário alterar alguns aspectos da API de uma forma que quebras de compatibilidade com versões anteriores. Isso pode acontecer por causa de correções para recursos existentes ou devido a novos recursos que alterar área de superfície de API existente.

Seguimos a mesma regra SDK atualizações. O SDK de pesquisa do Azure segue as regras de [controle de versão semântico](http://semver.org/) , que significa que sua versão tem três partes: principais, secundária e número (por exemplo, 1.1.0) de compilação. Nós será lançada uma nova versão principal do SDK somente no caso de alterações que interromper a compatibilidade com versões anteriores. Atualizações de recurso incondicionais, podemos será incrementado a versão secundária e para correções de bugs nós só aumentará a versão de compilação.

##<a name="snapshot-of-current-versions"></a>Instantâneo de versões atuais 

Abaixo está um instantâneo de versões atuais de todas as interfaces de programação para pesquisar do Azure. Mapas e outros detalhes podem ser encontrados nas próximas seções deste documento.

Interfaces|Versão principal mais recente|Status
----------|-------------------------|------
[.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)|1.1|Normalmente disponíveis, lançadas fevereiro de 2016
[.NET SDK Preview](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx)|visualização de 2.0|Visualização de lançadas agosto de 2016
[API REST de serviço](https://msdn.microsoft.com/library/azure/dn798935.aspx)|2015-02-28|Disponibilidade geral
[Visualização de API do serviço restante](search-api-2015-02-28-preview.md)|2015-02-visualização de 28|Visualizar
[Gerenciamento de API REST](https://msdn.microsoft.com/library/azure/dn832684.aspx)|2015-08-19|Disponibilidade geral

Para as APIs REST, incluindo o `api-version` em cada chamada é necessária. Isso facilita a uma versão específica, como uma visualização API de destino. O exemplo a seguir ilustra como o `api-version` parâmetro for especificado:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2015-02-28

> [AZURE.NOTE] Embora cada solicitação tem um `api-version`, recomendamos que você use a mesma versão para todas as solicitações de API. Isso é especialmente verdadeiro quando novas versões de API introduzem atributos ou operações que não são reconhecidas por versões anteriores. Mistura de versões de API pode ter consequências não intencionais e deve ser evitado.
> 
A API REST de serviço e a API REST de gerenciamento são versão independentemente uns dos outros. Qualquer similaridade nos números de versão é incidentais colegas.

Disponibilidade geral (ou GA) APIs podem ser usados em produção e estão sujeitos a contratos de nível de serviço Azure. Versões de visualização têm experimentais recursos que não são migrados sempre para uma versão de lançamento. **É altamente recomendável contra usando a visualização APIs em aplicativos de produção.**

##<a name="sdk-version-roadmap"></a>Mapa de versão do SDK

Cada versão do SDK do .NET alvo uma versão específica da API REST do serviço. Recursos são implementados na API REST primeiro e então implementados no SDK.

O SDK do .NET agora está disponível em geral e trabalho já está em andamento na próxima versão. A tabela a seguir em frente procura para futuras versões do SDK para que você tenha uma ideia de como o que vem em seguida.

Versão do .NET SDK|Versão da API REST|Recursos|ETA
----------------|----------------|--------|---
1.1|2015-02-28|Sintaxe de consulta Lucene|Fevereiro de 2016
visualização de 2.0|2015-02-visualização de 28|Analisadores personalizados, Azure Blob e indexadores de tabela, mapeamentos de campo, ETags|Agosto de 2016
2. x|Nova versão de API de GA|Mesmo que a visualização de 2.0|Antecipado Q4 2016

##<a name="about-preview-and-generally-available-versions"></a>Sobre versões de visualização e disponibilidade geral

Pesquisa Azure sempre previamente libera recursos experimentais por meio da API REST primeiro, em seguida, por meio de versões de pré-lançamento do SDK do .NET.

Não há garantia de Preview recursos a serem migradas para uma versão de lançamento. Enquanto os recursos em uma versão de GA são considerados estável e provavelmente não alterar excepto aprimoramentos e correções de compatibilidade pequenas, preview recursos estão disponíveis para teste e experimento, com o objetivo de coletar comentários em implementação e design do recurso. 

No entanto, como os recursos de visualização estão sujeitos a mudança, recomendamos contra escrever código de produção que leva uma dependência em versões de visualização. Se você estiver usando uma versão antiga de visualização, recomendamos a migração para a versão (GA) estará disponível. 

Para o SDK do .NET: orientação para migração de código pode ser encontrada em [atualizar o SDK do .NET](search-dotnet-sdk-migration.md).

Disponibilidade geral significa que a pesquisa do Azure está agora sob o contrato de nível de serviço (SLA). O SLA pode ser encontrado no [Azure contratos de nível de serviço de pesquisa](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

