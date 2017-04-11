
<properties
    pageTitle="Armazenamento do Azure consistente: diferenças e considerações | Microsoft Azure"
    description="Compreenda as diferenças de armazenamento do Azure e outras considerações de implantação de armazenamento do Azure consistentes."
    services="azure-stack"
    documentationCenter=""
    authors="MChadalapaka"
    manager="siroy"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="mchad"/>

# <a name="azure-consistent-storage-differences-and-considerations"></a>Armazenamento do Azure consistente: diferenças e considerações

Armazenamento do Azure consistente é o conjunto de serviços de nuvem do armazenamento na pilha do Microsoft Azure. Armazenamento do Azure consistente fornece blob, tabela, fila e funcionalidade de gerenciamento de conta com uma semântica Azure consistente. Este artigo resume as diferenças de armazenamento do Azure consistente conhecidas com o armazenamento do Azure. Ele também resume outras considerações a serem lembrados quando você implanta a versão preview atualmente publicamente disponível do Microsoft Azure pilha.

<span id="Concepts" class="anchor"><span id="_Toc386544169" class="anchor"><span id="_Toc389466742" class="anchor"><span id="_Ref428966996" class="anchor"><span id="_Toc433223853" class="anchor"></span></span></span></span></span>
## <a name="known-differences"></a>Diferenças conhecidas

Esta versão de visualização técnica do armazenamento do Azure consistente não tem paridade de recursos de 100%, com o armazenamento do Azure para as versões de API que têm suporte. Conhecidos as diferenças de recursos incluem o seguinte:

-   Determinados tipos de conta de armazenamento ainda não estão disponíveis, por exemplo, padrão\_RAGRS e padrão\_GRS.

-   Premium\_contas de armazenamento LRS podem ser provisionadas. No entanto, existem atualmente não há limites de desempenho ou garantias.

-   Funcionalidade de arquivos Azure ainda não está disponível.

-   A API obter intervalos de página não oferece suporte a recuperação de páginas que diferem entre instantâneos dos blobs de página.

-   A API de intervalos de página obter retorna páginas que têm 4 KB de detalhamento.

-   Chave de partição e chave de linha na implementação de tabela de armazenamento do Azure consistentes são cada limitado a 400 caracteres (ou seja, 800 bytes) de tamanho.

-   Nomes de blob na implementação do serviço de Blob storage Azure consistentes são limitados ao 880 caracteres (ou seja, 1760 bytes) de tamanho.

-   A implementação de armazenamento do Azure consistentes de dados de uso de armazenamento de locatário fornece medidores de uso de armazenamento que são idênticos no Azure, com a mesma semântica e unidades de relatório. Atualmente, no entanto, o medidor de uso de transações de armazenamento não inclui IaaS transações e medidor de uso de transferência de dados não diferencia o uso de largura de banda por tráfego de rede interna ou externa para uma região do Azure pilha.

-   Algumas diferenças constar o escopo da funcionalidade de gerenciamento de armazenamento. Por exemplo, você não pode alterar o tipo de conta ou tiver domínios personalizados. Além disso, somente consistência em nível de API é oferecida para o Premium\_LRS tipo de conta de armazenamento.

## <a name="deployment-considerations"></a>Considerações de implantação

-   **Somente teste.** Não implante de armazenamento do Azure consistente em ambientes de produção que usam a versão de visualização técnica do Microsoft Azure pilha atual. Esta versão destina-se somente para fins de avaliação em um ambiente de laboratório de teste.

-   **Desempenho**. A versão de visualização técnica do Microsoft Azure pilha de armazenamento do Azure consistente não é totalmente otimizada para o desempenho.

-   **Escalabilidade.** A versão de visualização técnica do Microsoft Azure pilha de armazenamento do Azure consistente não é otimizada para escalabilidade.

## <a name="version-support-considerations"></a>Considerações de suporte de versão

As seguintes versões são compatíveis com esta versão de visualização do armazenamento do Azure consistente:

> Biblioteca de cliente de armazenamento do Azure: [SDK do .NET do Microsoft Azure armazenamento 6. x](http://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)
>
> Serviços de dados do Azure armazenamento: [versão da API REST de 2015-04-05](https://msdn.microsoft.com/library/azure/mt705637.aspx)
>
> Serviços de gerenciamento de armazenamento Azure: [2015-05-01-visualização](https://msdn.microsoft.com/library/azure/mt163683.aspx)
> [2015-06-15](https://msdn.microsoft.com/library/azure/mt163683.aspx)
## <a name="next-steps"></a>Próximas etapas

-   [Introdução ao armazenamento do Azure consistentes](azure-stack-storage-overview.md)
