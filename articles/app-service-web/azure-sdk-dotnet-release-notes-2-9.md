<properties 
   pageTitle="Notas de versão do SDK do Azure para .NET 2,9" 
   description="Notas de versão do SDK do Azure para .NET 2,9" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/17/2016"
   ms.author="juliako"/>

# <a name="azure-sdk-for-net-29-release-notes"></a>Notas de versão do SDK do Azure para .NET 2,9

##<a name="overview"></a>Visão geral

Este documento contém as notas de versão para o SDK do Azure para .NET 2,9 lançamento. 

Para obter informações detalhadas sobre atualizações nesta versão, consulte o [lançamento do Azure SDK 2,9 postar](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Visualizar Azure SDK 2,9 para atualização do Visual Studio 2015 2 e Visual Studio "15"
 
Esta atualização inclui as seguintes correções:

- Problema relacionado ao POSICIONA API de geração de cliente em que a cadeia de caracteres "Tipo desconhecido" deverá aparecer como o nome da pasta de geração de código e/ou o nome do namespace soltas no código gerado.
- Problema relacionado ao WebJobs agendada no qual as informações de autenticação foi falhando a serem transmitidos Agendador do processo de provisionamento.

Esta atualização inclui o novo recurso a seguir:

- Suporte para os serviços de aplicativo secundário na guia "Serviços" da caixa de diálogo de provisionamento da serviço de aplicativo. 

##<a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Dados Azure Lucerne ferramentas para atualização de 2015 Visual Studio 2
 
Essas atualizações inclui o seguinte:

- **Ferramentas de Lucerne de dados do azure** para Visual Studio agora é mesclado no SDK do Azure para a versão de .NET. A ferramenta é instalada automaticamente quando você instala o SDK do Azure. 

    A ferramenta é atualizada com frequência, [aqui](http://aka.ms/datalaketool) para obter as atualizações.

- **Server Explorer** agora permite exibir todos e criar algumas entidades de metadados U-SQL. Para obter mais informações, consulte [Este](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blog.


##<a name="hdinsight-tools"></a>Ferramentas de HDInsight 

**Ferramentas de HDInsight** para Visual Studio agora dá suporte à versão 3.3, incluindo mostrando Tez gráficos HDInsight e correções de outro idioma.


##<a name="azure-resource-manager"></a>Gerenciador de recursos do Azure 

Esta versão oferece suporte de [KeyVault](../resource-manager-keyvault-parameter.md) para modelos ARM.

##<a name="see-also"></a>Consulte também

[Postagem de lançamento do Azure SDK 2,9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)
