<properties
   pageTitle="Instalar o Visual Studio e SSDT para o SQL Data Warehouse | Microsoft Azure"
   description="Instalar o Visual Studio e ferramentas de desenvolvimento do SQL Server (SSDT) para depósito de dados do SQL Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess"/>

# <a name="install-visual-studio-2015-and-ssdt-for-sql-data-warehouse"></a>Instalar o Visual Studio 2015 e SSDT para depósito de dados do SQL

Para desenvolver aplicativos para o SQL Data Warehouse, é recomendável usar o Visual Studio 2015 com a versão mais recente do SQL Server dados ferramentas (SSDT).  Também é compatível com o Visual Studio 2013 Update 5 com SSDT para fins de compatibilidade.  

Usando o Visual Studio com SSDT permitirá que você use o SQL Server Object Explorer visualmente explorar tabelas, exibições, procedimentos armazenados e muito mais objetos no SQL Data Warehouse, bem como executar consultas.

> [AZURE.NOTE] SQL Data Warehouse ainda não dá suporte para os projetos de banco de dados do Visual Studio.  Este recurso será adicionado em uma versão futura.

## <a name="step-1-install-visual-studio-2015"></a>Etapa 1: Instalar o Visual Studio de 2015

Siga estes links para baixar e instalar o Visual Studio 2015. Se você já tiver Visual Studio 2013 ou 2015 instalado, você pode pular para a etapa 2, instale o SSDT.

1. [Baixe o Visual Studio 2015][].
2. Siga o guia de [Instalação do Visual Studio][] no MSDN e escolha as configurações padrão.

## <a name="step-2-install-ssdt"></a>Etapa 2: Instalar SSDT

Para instalar o SSDT para Visual Studio simplesmente procure uma atualização SSDT de dentro do Visual Studio seguindo estas etapas.

1. No Visual Studio, clique em **Ferramentas** / **extensões e atualizações …**  /  **Atualizações**
2. Selecione **Atualizações de produtos** e, em seguida, procure **Ferramentas de atualização do Microsoft SQL Server para o banco de dados**

Se uma atualização não for encontrada, você deve ter a versão mais recente instalada.  Para confirmar SSDT estiver instalado, clique em **Ajuda** / **Sobre o Microsoft Visual Studio** e procure ferramentas de dados do SQL Server na lista.  Versão mais recente do SSDT é 14.0.60525.0.  Se a opção para instalar não estiver disponível no Visual Studio, Alternativamente você pode visitar a página de [Download de SSDT][] para baixar e instalar SSDT manualmente.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem a versão mais recente do SSDT, você está pronto para [se conectar][] a seu Data Warehouse SQL.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[conectar-se]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Baixe o Visual Studio de 2015]: https://www.visualstudio.com/downloads/
[Instalar o Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[Download SSDT]: https://msdn.microsoft.com/library/mt204009.aspx
